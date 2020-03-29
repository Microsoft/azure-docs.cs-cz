---
title: Společné umístění tabulky – hyperškálování (Citus) – databáze Azure pro PostgreSQL
description: Jak společně ukládat související informace pro rychlejší dotazy
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 7e4073ec45f4c21f33d20924a9948e72f961c7f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74967333"
---
# <a name="table-colocation-in-azure-database-for-postgresql--hyperscale-citus"></a>Společné umístění tabulky v databázi Azure pro PostgreSQL – hyperškálování (Citus)

Kolokace znamená společné ukládání souvisejících informací na stejných uzlech. Dotazy mohou být rychlé, pokud jsou všechna potřebná data k dispozici bez síťového provozu. Colocating související data na různých uzlech umožňuje dotazy běžet efektivně paralelně na každém uzlu.

## <a name="data-colocation-for-hash-distributed-tables"></a>Společné umístění dat pro tabulky distribuované hash

V Azure Database for PostgreSQL – Hyperscale (Citus) se řádek uloží do horizontálního oddílu, pokud hodnota hash ve sloupci distribuce spadá do rozsahu hash horizontálního oddílu. Úlomky se stejným rozsahem hash jsou vždy umístěny na stejném uzlu. Řádky se stejnými hodnotami distribučních sloupců jsou vždy na stejném uzlu mezi tabulkami.

![Střepy](media/concepts-hyperscale-colocation/colocation-shards.png)

## <a name="a-practical-example-of-colocation"></a>Praktický příklad kolokace

Zvažte následující tabulky, které mohou být součástí víceklientské webové analýzy SaaS:

```sql
CREATE TABLE event (
  tenant_id int,
  event_id bigint,
  page_id int,
  payload jsonb,
  primary key (tenant_id, event_id)
);

CREATE TABLE page (
  tenant_id int,
  page_id int,
  path text,
  primary key (tenant_id, page_id)
);
```

Nyní chceme odpovědět na dotazy, které mohou být vydány řídicím panelem pro zákazníka. Příklad dotazu je "Vrátit počet návštěv v posledním týdnu pro všechny stránky začínající '/blog' v tenant a šest."

Pokud naše data byla v možnosti nasazení jednoho serveru, mohli bychom snadno vyjádřit náš dotaz pomocí bohaté sady relačních operací nabízených SQL:

```sql
SELECT page_id, count(event_id)
FROM
  page
LEFT JOIN  (
  SELECT * FROM event
  WHERE (payload->>'time')::timestamptz >= now() - interval '1 week'
) recent
USING (tenant_id, page_id)
WHERE tenant_id = 6 AND path LIKE '/blog%'
GROUP BY page_id;
```

Dokud pracovní [sada](https://en.wikipedia.org/wiki/Working_set) pro tento dotaz vejde do paměti, tabulka jednoho serveru je vhodné řešení. Podívejme se na možnosti škálování datového modelu pomocí možnosti nasazení Hyperscale (Citus).

### <a name="distribute-tables-by-id"></a>Distribuce tabulek podle ID

Dotazy na jednom serveru se začínají zpomalovat s tím, jak roste počet klientů a data uložená pro každého klienta. Pracovní sada zastaví montáž v paměti a procesor se stane kritickým bodem.

V tomto případě můžeme horizontálního oddílu data přes mnoho uzlů pomocí Hyperscale (Citus). První a nejdůležitější volbou, kterou musíme udělat, když se rozhodneme pro rozdělení, je distribuční sloupec. Začněme s naivní volbou použití `event_id` pro tabulku `page_id` událostí `page` a pro tabulku:

```sql
-- naively use event_id and page_id as distribution columns

SELECT create_distributed_table('event', 'event_id');
SELECT create_distributed_table('page', 'page_id');
```

Když jsou data rozptýlena mezi různými pracovníky, nemůžeme provést spojení, jako bychom na jednom uzlu PostgreSQL. Místo toho musíme vydat dva dotazy:

```sql
-- (Q1) get the relevant page_ids
SELECT page_id FROM page WHERE path LIKE '/blog%' AND tenant_id = 6;

-- (Q2) get the counts
SELECT page_id, count(*) AS count
FROM event
WHERE page_id IN (/*…page IDs from first query…*/)
  AND tenant_id = 6
  AND (payload->>'time')::date >= now() - interval '1 week'
GROUP BY page_id ORDER BY count DESC LIMIT 10;
```

Poté musí být výsledky z obou kroků kombinovány aplikací.

Spuštění dotazů musí konzultovat data v úlomcích roztroušených po uzlech.

![Neefektivní dotazy](media/concepts-hyperscale-colocation/colocation-inefficient-queries.png)

V tomto případě distribuce dat vytváří značné nevýhody:

-   Režie z dotazování každý oddíl a spuštění více dotazů.
-   Režie Q1 vrací mnoho řádků klientovi.
-   Q2 se zvětší.
-   Potřeba psát dotazy ve více krocích vyžaduje změny v aplikaci.

Data jsou rozptýlena, takže dotazy mohou být paralelizovány. Je to výhodné pouze v případě, že množství práce, které dotaz dělá, je podstatně větší než režie dotazování mnoho oddílů.

### <a name="distribute-tables-by-tenant"></a>Distribuce tabulek podle tenanta

V Hyperscale (Citus) jsou řádky se stejnou hodnotou distribučního sloupce zaručeně na stejném uzlu. Počínaje znovu, můžeme vytvořit `tenant_id` naše tabulky s jako distribuční sloupec.

```sql
-- co-locate tables by using a common distribution column
SELECT create_distributed_table('event', 'tenant_id');
SELECT create_distributed_table('page', 'tenant_id', colocate_with => 'event');
```

Nyní Hyperscale (Citus) může odpovědět na původní dotaz na jeden server bez úprav (Q1):

```sql
SELECT page_id, count(event_id)
FROM
  page
LEFT JOIN  (
  SELECT * FROM event
  WHERE (payload->>'time')::timestamptz >= now() - interval '1 week'
) recent
USING (tenant_id, page_id)
WHERE tenant_id = 6 AND path LIKE '/blog%'
GROUP BY page_id;
```

Z důvodu filtr a spojit na tenant_id, Hyperscale (Citus) ví, že celý dotaz lze odpovědět pomocí sady společně přidělené horizontálních oddílů, které obsahují data pro konkrétního klienta. Jeden uzel PostgreSQL může odpovědět na dotaz v jednom kroku.

![Lepší dotaz](media/concepts-hyperscale-colocation/colocation-better-query.png)

V některých případech musí být dotazy a schémata tabulek změněny tak, aby zahrnovaly ID klienta v jedinečných omezeních a podmínkách spojení. Tato změna je obvykle jednoduché.

## <a name="next-steps"></a>Další kroky

- Podívejte se, jak se data klienta spolualokují v [kurzu s více tenanty](tutorial-design-database-hyperscale-multi-tenant.md).
