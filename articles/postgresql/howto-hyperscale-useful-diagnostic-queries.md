---
title: Užitečné diagnostické dotazy – Citus (-Scale) – Azure Database for PostgreSQL
description: Dotazy na informace o distribuovaných datech a dalších
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 1/5/2021
ms.openlocfilehash: 4858f650aca1b704ac79482e0158fd83fc0264b8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98165237"
---
# <a name="useful-diagnostic-queries"></a>Užitečné diagnostické dotazy

## <a name="finding-which-node-contains-data-for-a-specific-tenant"></a>Hledání, který uzel obsahuje data pro konkrétního tenanta

V případě víceklientské použití můžeme určit, který pracovní uzel obsahuje řádky pro konkrétního tenanta.  Citus) seskupí řádky distribuovaných tabulek do horizontálních oddílů a každou horizontálních oddílů umístí do pracovního uzlu ve skupině serverů. 

Předpokládejme, že naši tenant aplikace jsou obchody, a chceme zjistit, který pracovní uzel obsahuje data pro úložiště ID = 4.  Jinými slovy chceme najít umístění pro horizontálních oddílů obsahující řádky, jejichž distribuční sloupec má hodnotu 4:

``` postgresql
SELECT shardid, shardstate, shardlength, nodename, nodeport, placementid
  FROM pg_dist_placement AS placement,
       pg_dist_node AS node
 WHERE placement.groupid = node.groupid
   AND node.noderole = 'primary'
   AND shardid = (
     SELECT get_shard_id_for_distribution_column('stores', 4)
   );
```

Výstup obsahuje hostitele a port pracovní databáze.

```
┌─────────┬────────────┬─────────────┬───────────┬──────────┬─────────────┐
│ shardid │ shardstate │ shardlength │ nodename  │ nodeport │ placementid │
├─────────┼────────────┼─────────────┼───────────┼──────────┼─────────────┤
│  102009 │          1 │           0 │ 10.0.0.16 │     5432 │           2 │
└─────────┴────────────┴─────────────┴───────────┴──────────┴─────────────┘
```

## <a name="finding-the-distribution-column-for-a-table"></a>Vyhledání distribučního sloupce pro tabulku

Každá distribuovaná tabulka v Citus () má "distribuční sloupec". (Další informace najdete v tématu [modelování distribuovaných dat](concepts-hyperscale-choose-distribution-column.md).) Může být důležité znát, který sloupec je. Například při spojování nebo filtrování tabulek se můžou zobrazit chybové zprávy s nápovědami, jako je přidání filtru do distribučního sloupce.

`pg_dist_*`Tabulky v uzlu koordinátora obsahují různé metadata distribuované databáze. Konkrétně `pg_dist_partition` uchovává informace o distribučním sloupci pro každou tabulku. Pomocí vhodné funkce Utility můžete vyhledat název distribučního sloupce z podrobností nízké úrovně v metadatech. Tady je příklad a jeho výstup:

``` postgresql
-- create example table

CREATE TABLE products (
  store_id bigint,
  product_id bigint,
  name text,
  price money,

  CONSTRAINT products_pkey PRIMARY KEY (store_id, product_id)
);

-- pick store_id as distribution column

SELECT create_distributed_table('products', 'store_id');

-- get distribution column name for products table

SELECT column_to_column_name(logicalrelid, partkey) AS dist_col_name
  FROM pg_dist_partition
 WHERE logicalrelid='products'::regclass;
```

Příklad výstupu:

```
┌───────────────┐
│ dist_col_name │
├───────────────┤
│ store_id      │
└───────────────┘
```

## <a name="detecting-locks"></a>Zjišťování zámků

Tento dotaz se spustí ve všech pracovních uzlech a identifikuje zámky, jak dlouho byly otevřené, a problematické dotazy:

``` postgresql
SELECT run_command_on_workers($cmd$
  SELECT array_agg(
    blocked_statement || ' $ ' || cur_stmt_blocking_proc
    || ' $ ' || cnt::text || ' $ ' || age
  )
  FROM (
    SELECT blocked_activity.query    AS blocked_statement,
           blocking_activity.query   AS cur_stmt_blocking_proc,
           count(*)                  AS cnt,
           age(now(), min(blocked_activity.query_start)) AS "age"
    FROM pg_catalog.pg_locks         blocked_locks
    JOIN pg_catalog.pg_stat_activity blocked_activity
      ON blocked_activity.pid = blocked_locks.pid
    JOIN pg_catalog.pg_locks         blocking_locks
      ON blocking_locks.locktype = blocked_locks.locktype
     AND blocking_locks.DATABASE IS NOT DISTINCT FROM blocked_locks.DATABASE
     AND blocking_locks.relation IS NOT DISTINCT FROM blocked_locks.relation
     AND blocking_locks.page IS NOT DISTINCT FROM blocked_locks.page
     AND blocking_locks.tuple IS NOT DISTINCT FROM blocked_locks.tuple
     AND blocking_locks.virtualxid IS NOT DISTINCT FROM blocked_locks.virtualxid
     AND blocking_locks.transactionid IS NOT DISTINCT FROM blocked_locks.transactionid
     AND blocking_locks.classid IS NOT DISTINCT FROM blocked_locks.classid
     AND blocking_locks.objid IS NOT DISTINCT FROM blocked_locks.objid
     AND blocking_locks.objsubid IS NOT DISTINCT FROM blocked_locks.objsubid
     AND blocking_locks.pid != blocked_locks.pid
    JOIN pg_catalog.pg_stat_activity blocking_activity ON blocking_activity.pid = blocking_locks.pid
    WHERE NOT blocked_locks.GRANTED
     AND blocking_locks.GRANTED
    GROUP BY blocked_activity.query,
             blocking_activity.query
    ORDER BY 4
  ) a
$cmd$);
```

Příklad výstupu:

```
┌───────────────────────────────────────────────────────────────────────────────────┐
│                               run_command_on_workers                              │
├───────────────────────────────────────────────────────────────────────────────────┤
│ (10.0.0.16,5432,t,"")                                                             │
│ (10.0.0.20,5432,t,"{""update ads_102277 set name = 'new name' where id = 1; $ sel…│
│…ect * from ads_102277 where id = 1 for update; $ 1 $ 00:00:03.729519""}")         │
└───────────────────────────────────────────────────────────────────────────────────┘
```

## <a name="querying-the-size-of-your-shards"></a>Dotazování na velikost vašeho horizontálních oddílůu

Tento dotaz vám poskytne velikost každého horizontálních oddílů distribuované tabulky s názvem `my_distributed_table` :

``` postgresql
SELECT *
FROM run_command_on_shards('my_distributed_table', $cmd$
  SELECT json_build_object(
    'shard_name', '%1$s',
    'size',       pg_size_pretty(pg_table_size('%1$s'))
  );
$cmd$);
```

Příklad výstupu:

```
┌─────────┬─────────┬───────────────────────────────────────────────────────────────────────┐
│ shardid │ success │                                result                                 │
├─────────┼─────────┼───────────────────────────────────────────────────────────────────────┤
│  102008 │ t       │ {"shard_name" : "my_distributed_table_102008", "size" : "2416 kB"}    │
│  102009 │ t       │ {"shard_name" : "my_distributed_table_102009", "size" : "3960 kB"}    │
│  102010 │ t       │ {"shard_name" : "my_distributed_table_102010", "size" : "1624 kB"}    │
│  102011 │ t       │ {"shard_name" : "my_distributed_table_102011", "size" : "4792 kB"}    │
└─────────┴─────────┴───────────────────────────────────────────────────────────────────────┘
```

## <a name="querying-the-size-of-all-distributed-tables"></a>Dotazování na velikost všech distribuovaných tabulek

Tento dotaz načte seznam velikostí pro každou distribuovanou tabulku a velikost jejich indexů.

``` postgresql
SELECT
  tablename,
  pg_size_pretty(
    citus_total_relation_size(tablename::text)
  ) AS total_size
FROM pg_tables pt
JOIN pg_dist_partition pp
  ON pt.tablename = pp.logicalrelid::text
WHERE schemaname = 'public';
```

Příklad výstupu:

```
┌───────────────┬────────────┐
│   tablename   │ total_size │
├───────────────┼────────────┤
│ github_users  │ 39 MB      │
│ github_events │ 98 MB      │
└───────────────┴────────────┘
```

Všimněte si, že pro dotazování velikosti distribuované tabulky jsou k dispozici další funkce Citus (s možností škálování), viz [Určení velikosti tabulky](howto-hyperscale-table-size.md).

## <a name="identifying-unused-indices"></a>Identifikace nepoužitých indexů

Následující dotaz identifikuje nepoužívané indexy na uzlech pracovního procesu pro danou distribuovanou tabulku ( `my_distributed_table` ).

``` postgresql
SELECT *
FROM run_command_on_shards('my_distributed_table', $cmd$
  SELECT array_agg(a) as infos
  FROM (
    SELECT (
      schemaname || '.' || relname || '##' || indexrelname || '##'
                 || pg_size_pretty(pg_relation_size(i.indexrelid))::text
                 || '##' || idx_scan::text
    ) AS a
    FROM  pg_stat_user_indexes ui
    JOIN  pg_index i
    ON    ui.indexrelid = i.indexrelid
    WHERE NOT indisunique
    AND   idx_scan < 50
    AND   pg_relation_size(relid) > 5 * 8192
    AND   (schemaname || '.' || relname)::regclass = '%s'::regclass
    ORDER BY
      pg_relation_size(i.indexrelid) / NULLIF(idx_scan, 0) DESC nulls first,
      pg_relation_size(i.indexrelid) DESC
  ) sub
$cmd$);
```

Příklad výstupu:

```
┌─────────┬─────────┬───────────────────────────────────────────────────────────────────────┐
│ shardid │ success │                            result                                     │
├─────────┼─────────┼───────────────────────────────────────────────────────────────────────┤
│  102008 │ t       │                                                                       │
│  102009 │ t       │ {"public.my_distributed_table_102009##some_index_102009##28 MB##0"}   │
│  102010 │ t       │                                                                       │
│  102011 │ t       │                                                                       │
└─────────┴─────────┴───────────────────────────────────────────────────────────────────────┘
```

## <a name="monitoring-client-connection-count"></a>Monitorování počtu připojení klientů

Následující dotaz počítá počet otevřených připojení v koordinátoru a seskupuje je podle typu.

``` sql
SELECT state, count(*)
FROM pg_stat_activity
GROUP BY state;
```

Příklad výstupu:

```
┌────────┬───────┐
│ state  │ count │
├────────┼───────┤
│ active │     3 │
│ idle   │     3 │
│ ∅      │     6 │
└────────┴───────┘
```

## <a name="index-hit-rate"></a>Četnost přístupů k indexu

Tento dotaz vám poskytne míru úspěšnosti indexu napříč všemi uzly. Míra přístupů k indexu je užitečná při určování, jak často se indexy používají při dotazování:

``` postgresql
SELECT nodename, result as index_hit_rate
FROM run_command_on_workers($cmd$
  SELECT CASE sum(idx_blks_hit)
    WHEN 0 THEN 'NaN'::numeric
    ELSE to_char((sum(idx_blks_hit) - sum(idx_blks_read)) / sum(idx_blks_hit + idx_blks_read), '99.99')::numeric
    END AS ratio
  FROM pg_statio_user_indexes
$cmd$);
```

Příklad výstupu:

```
┌───────────┬────────────────┐
│ nodename  │ index_hit_rate │
├───────────┼────────────────┤
│ 10.0.0.16 │ 0.88           │
│ 10.0.0.20 │ 0.89           │
└───────────┴────────────────┘
```

## <a name="cache-hit-rate"></a>Rychlost přístupů do mezipaměti

Většina aplikací obvykle přistupuje k malým zlomku svých celkových dat najednou. PostgreSQL udržuje často používaná data v paměti, aby se předešlo pomalému čtení z disku. V zobrazení [pg_statio_user_tables](https://www.postgresql.org/docs/current/monitoring-stats.html#MONITORING-PG-STATIO-ALL-TABLES-VIEW) můžete zobrazit statistiky.

Důležité měření spočívá v tom, jaké procento dat pochází z mezipaměti paměti a disku v rámci úlohy:

``` postgresql
SELECT
  sum(heap_blks_read) AS heap_read,
  sum(heap_blks_hit)  AS heap_hit,
  sum(heap_blks_hit) / (sum(heap_blks_hit) + sum(heap_blks_read)) AS ratio
FROM
  pg_statio_user_tables;
```

Příklad výstupu:

```
 heap_read | heap_hit |         ratio
-----------+----------+------------------------
         1 |      132 | 0.99248120300751879699
```

Pokud zjistíte, že se jedná o poměr významně nižší než 99%, pak pravděpodobně budete chtít zvážit zvýšení dostupné mezipaměti pro vaši databázi.

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [systémových tabulkách](reference-hyperscale-metadata.md) , které jsou užitečné pro diagnostiku
