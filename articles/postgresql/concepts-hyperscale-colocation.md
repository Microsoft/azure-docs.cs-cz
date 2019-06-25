---
title: Koncepty serveru ve službě Azure Database for PostgreSQL
description: Tento článek obsahuje důležité informace a pokyny pro konfiguraci a správu databáze Azure pro servery PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: d03cfd49887adf1f6a4650e374d3e13eeca735a4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65077467"
---
# <a name="table-colocation-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Společné umístění tabulky ve službě Azure Database for PostgreSQL – velkokapacitní (Citus) (preview)

Společné umístění znamená, že ukládání související informace dohromady na stejné uzly. Rychlé, když je k dispozici bez síťový provoz všech potřebných dat dotazy přejít. Společné umístění dat na různých uzlech umožňuje vytvářet dotazy umožňuje efektivně spouštět paralelně na jednotlivých uzlech.

## <a name="data-colocation-for-hash-distributed-tables"></a>Společné umístění dat pro hodnoty hash distribuované tabulky

Ve Hyperškálovatelného řádek ukládá v horizontálním oddílu, pokud hodnota hash hodnoty ve sloupci distribuční spadá do rozsahu hodnot hash horizontálních oddílů. Horizontálních oddílech stejný rozsah hodnot hash jsou vždy umístěny na stejném uzlu. Neustále řádků s hodnotami sloupce rovnoměrná distribuce mezi tabulkami ve stejném uzlu.

![Horizontálních oddílů](media/concepts-hyperscale-colocation/colocation-shards.png)

## <a name="a-practical-example-of-colocation"></a>Praktické příklad společného umístění

Vezměte v úvahu následující tabulky, které mohou být součástí analýzy webových víceklientské SaaS:

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

Nyní chceme zodpovědět dotazy, které můžou být vystavené řídicí panel určených pro zákazníky, například: "Vrátí počet návštěv v minulém týdnu pro všechny stránky, počínaje" / blogu "v tenantovi šest."

Pokud naše data byla v možnosti nasazení s jedním serverem, jsme mohli snadno express dotaz pomocí bohaté sadě relační operací, které SQL:

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

Za předpokladu, [pracovního sadě](https://en.wikipedia.org/wiki/Working_set) pro tento dotaz se vejde do paměti, tabulka jedním serverem je vhodné řešení. Zvažte však příležitostí škálování datového modelu s Hyperškálováním možností nasazení.

### <a name="distributing-tables-by-id"></a>Distribuce tabulky podle ID

Dotazy na jeden server spusťte zpomalení roste počet klientů a data uložená pro každého tenanta. Pracovní sada zastaví do paměti a procesoru stane kritickým bodem.

V tomto případě můžeme horizontálních oddílů data napříč mnoha uzly pomocí mírou škálování. První a nejdůležitější možnost, kterou potřebujeme se ujistit, pokud horizontální dělení představuje sloupci distribuce. Začněme naivní výběr mezi používáním `event_id` pro tabulku událostí a `page_id` pro `page` tabulky:

```sql
-- naively use event_id and page_id as distribution columns

SELECT create_distributed_table('event', 'event_id');
SELECT create_distributed_table('page', 'page_id');
```

Když data rozptýlena mezi různé pracovní procesy, jsme nejde provést spojení, protože jsme by v jednom uzlu PostgreSQL. Místo toho budeme muset zadávat dotazy na dvě:

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

Později potřebujete výsledky ze dvou kroků a nelze jej zkombinovat aplikací.

Spuštění dotazů musí zkontrolujte data v horizontálních oddílech, které jsou rozmístěny napříč uzly.

![neefektivní dotazů](media/concepts-hyperscale-colocation/colocation-inefficient-queries.png)

V tomto případě distribuci dat vytvoří podstatné nevýhody:

-   Režie z dotazování každý horizontální oddíl, spouštění více dotazů
-   Režie Q1 vrácením klientovi mnoho řádků
-   2\. čtvrtletí stát velké
-   Nemusíte psát dotazy v několika krocích vyžaduje změny v aplikaci

Protože data rozptýlena, může být paralelizována dotazy. Je však pouze výhodné Pokud množství práce, která provádí dotaz je podstatně větší nároky na dotazování více horizontálních oddílů.

### <a name="distributing-tables-by-tenant"></a>Distribuce tabulky pomocí tenanta

V Hyperškálovacím jsou řádky se stejnou hodnotou sloupec distribuce musí být na stejném uzlu. Začít znova, můžeme vytvořit naší tabulky s `tenant_id` jako sloupec distribuce.

```sql
-- co-locate tables by using a common distribution column
SELECT create_distributed_table('event', 'tenant_id');
SELECT create_distributed_table('page', 'tenant_id', colocate_with => 'event');
```

Velkokapacitní nyní můžete odpovědět na původní dotaz jeden server bez úprav (1):

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

Z důvodu filtr a spojení tenant_id Hyperškálovatelného ví, lze zodpovědět celý dotaz pomocí sady společně umísťovat horizontální oddíly, které obsahují data tohoto konkrétního tenanta. Jeden uzel PostgreSQL může zodpovědět v jediném kroku.

![lepší dotazu](media/concepts-hyperscale-colocation/colocation-better-query.png)

V některých případech musí obsahovat ID tenanta v unikátních omezení a připojit se k podmínky změnit dotazy a schémata tabulek. Je to ale obvykle jednoduché změny.

## <a name="next-steps"></a>Další postup

- Zobrazit, jak se data tenanta umístěny společně v [kurzu práce s více tenanty](tutorial-design-database-hyperscale-multi-tenant.md)
