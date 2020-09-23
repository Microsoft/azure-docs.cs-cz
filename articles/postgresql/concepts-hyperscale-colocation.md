---
title: Společné umístění tabulky – Citus (Velká) – Azure Database for PostgreSQL
description: Jak ukládat související informace dohromady pro rychlejší dotazy
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 842563319e09a001fd6e85403d8aee6fb14690ee
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90884421"
---
# <a name="table-colocation-in-azure-database-for-postgresql--hyperscale-citus"></a>Společné umístění tabulky v Azure Database for PostgreSQL – měřítko (Citus)

Spolupoloha znamená ukládání souvisejících informací společně na stejných uzlech. Dotazy můžou být rychlé, pokud jsou všechna potřebná data dostupná bez jakýchkoli síťových přenosů. Spolulokalizace souvisejících dat na různých uzlech umožňuje, aby dotazy byly efektivně spouštěny paralelně na jednotlivých uzlech.

## <a name="data-colocation-for-hash-distributed-tables"></a>Společné umístění dat pro tabulky distribuované algoritmem hash

V Azure Database for PostgreSQL – Citus () se řádek ukládá do horizontálních oddílů, pokud hodnota hash hodnoty v distribučním sloupci spadá do rozsahu hodnot hash horizontálních oddílů. Horizontálních oddílů se stejným rozsahem hodnot hash jsou vždy umístěny do stejného uzlu. Řádky s hodnotami rovnoměrného distribučního sloupce jsou vždy na stejném uzlu napříč tabulkami.

:::image type="content" source="media/concepts-hyperscale-colocation/colocation-shards.png" alt-text="Horizontálních oddílů":::

## <a name="a-practical-example-of-colocation"></a>Praktický příklad kolokace

Vezměte v úvahu následující tabulky, které mohou být součástí SaaS webového analytického nástroje pro více tenantů:

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

Nyní chceme odpovídat na dotazy, které mohou být vydány řídicím panelem, který je zákazníkem přístupný. Příkladem dotazu je "vrácení počtu návštěv za poslední týden pro všechny stránky začínající na"/blog "v tenantovi 6."

Pokud byla naše data v možnosti nasazení na jednom serveru, můžeme snadno vyjádřit náš dotaz pomocí bohatě dostupné relačních operací, které nabízí SQL:

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

Pokud se do paměti vejde [pracovní sada](https://en.wikipedia.org/wiki/Working_set) tohoto dotazu, bude vhodné řešení tabulka jednoho serveru. Pojďme vzít v úvahu příležitosti škálování datového modelu s možností nasazení Citus ().

### <a name="distribute-tables-by-id"></a>Distribuovat tabulky podle ID

Dotazy na jeden server začínají snižovat počet klientů a data uložená pro každého tenanta roste. Pracovní sada se zastaví přizpůsobení paměti a CPU se stane kritickým bodem.

V tomto případě můžeme horizontálních oddílů data v mnoha uzlech pomocí škálování (Citus). První a nejdůležitější volba, kterou musíme udělat, když se rozhodneme, že horizontálních oddílů je distribuční sloupec. Pojďme začít s Naive možností použití `event_id` pro tabulku událostí a `page_id` pro `page` tabulku:

```sql
-- naively use event_id and page_id as distribution columns

SELECT create_distributed_table('event', 'event_id');
SELECT create_distributed_table('page', 'page_id');
```

Pokud jsou data rozdělená mezi různé pracovní procesy, nemůžeme na jednom uzlu PostgreSQL provést spojení, jako bychom rádi. Místo toho musíme vydávat dva dotazy:

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

Následně je třeba, aby se výsledky ze dvou kroků spojily s aplikací.

Spuštění dotazů musí prostudovat data v horizontálních oddílů rozptýlených napříč uzly.

:::image type="content" source="media/concepts-hyperscale-colocation/colocation-inefficient-queries.png" alt-text="Neefektivní dotazy":::

V takovém případě distribuce dat vytváří zásadní nevýhody:

-   Režie při dotazování jednotlivých horizontálních oddílů a spuštění více dotazů.
-   Režie z Q1 vracející mnoho řádků klientovi.
-   Dotaz č. 2 se bude velký.
-   Potřeba psát dotazy v několika krocích vyžaduje změny v aplikaci.

Data jsou rozptýlená, takže dotazy je možné paralelně rozdávat. To je výhodné jenom v případě, že množství práce, které dotaz dělá, je podstatně větší než režie pro dotazování mnoha horizontálních oddílů.

### <a name="distribute-tables-by-tenant"></a>Distribuovat tabulky podle tenanta

V Citus je zaručeno, že řádky se stejnou hodnotou distribučního sloupce budou ve stejném uzlu. Počínaje tím můžeme vytvořit tabulky s použitím `tenant_id` distribučního sloupce.

```sql
-- co-locate tables by using a common distribution column
SELECT create_distributed_table('event', 'tenant_id');
SELECT create_distributed_table('page', 'tenant_id', colocate_with => 'event');
```

Nyní může Citus odpovědět na původní dotaz s jedním serverem beze změny (Q1):

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

Vzhledem k tomu, že filtr a spojení tenant_id, Citus (škálování) ví, že je možné odpovědět na celý dotaz pomocí sady společně umístěných horizontálních oddílů, které obsahují data tohoto konkrétního tenanta. Jeden uzel PostgreSQL může odpovědět na dotaz v jednom kroku.

:::image type="content" source="media/concepts-hyperscale-colocation/colocation-better-query.png" alt-text="Lepší dotaz":::

V některých případech se dotazy a schémata tabulek musí změnit tak, aby zahrnovaly ID tenanta v jedinečných omezeních a podmínkách připojení. Tato změna je obvykle jednoduchá.

## <a name="next-steps"></a>Další kroky

- Podívejte se, jak se v [kurzu víceklientské architektury](tutorial-design-database-hyperscale-multi-tenant.md)nacházejí data tenanta.
