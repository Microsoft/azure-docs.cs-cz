---
title: Systémové tabulky – Citus (Velká měřítko) – Azure Database for PostgreSQL
description: Metadata pro provádění distribuovaného dotazu
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 74403365fe48584fa5d1db0e349c9dfc3772d874
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97652843"
---
# <a name="system-tables-and-views"></a>Systémové tabulky a zobrazení

Citus () vytváří a udržuje speciální tabulky, které obsahují informace o distribuovaných datech ve skupině serverů. Uzel koordinátora tyto tabulky projedná při plánování spouštění dotazů v pracovních uzlech.

## <a name="coordinator-metadata"></a>Metadata koordinátora

Citus () rozděluje každou distribuovanou tabulku na více logických horizontálních oddílůů na základě distribučního sloupce. Koordinátor pak udržuje tabulky metadat pro sledování statistik a informací o stavu a umístění těchto horizontálních oddílů.

V této části popíšeme všechny tyto tabulky metadat a jejich schéma.
Tyto tabulky můžete zobrazit a dotazovat pomocí jazyka SQL po přihlášení do uzlu koordinátor.

> [!NOTE]
>
> Skupiny serverů Citus (), na kterých běží starší verze modulu Citus, nemusí nabízet všechny tabulky uvedené níže.

### <a name="partition-table"></a>Tabulka oddílů

\_ \_ Tabulka oddílů pg DIST ukládá metadata o tom, které tabulky v databázi jsou distribuované. Pro každou distribuovanou tabulku také ukládá informace o metodě distribuce a podrobné informace o distribučním sloupci.

| Název         | Typ     | Description                                                                                                                                                                                                                                           |
|--------------|----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| logicalrelid | regclass | Distribuovaná tabulka, do které tento řádek odpovídá Tato hodnota odkazuje na sloupec relfilenode v tabulce katalogu katalogu pg_class System.                                                                                                                   |
| partmethod   | char     | Metoda použitá pro dělení a distribuci. Hodnoty tohoto sloupce odpovídající různým metodám distribuce jsou připojeny: "a", hash: ' h ', referenční tabulka: ' n '                                                                          |
| partkey      | text     | Podrobné informace o distribučním sloupci, včetně čísla sloupce, typu a dalších relevantních informací.                                                                                                                                      |
| colocationid | integer  | Skupina kolokace, do níž patří tato tabulka Tabulky ve stejné skupině umožňují společně umístěná spojení a distribuované souhrny mezi dalšími optimalizacemi. Tato hodnota odkazuje na sloupec colocationid v tabulce pg_dist_colocation.                      |
| repmodel     | char     | Metoda použitá pro replikaci dat Hodnoty tohoto sloupce odpovídající různým metodám replikace jsou: replikace založená na příkazech Citus: c, replikace PostgreSQL streaming:, dvoufázové potvrzení (pro referenční tabulky): t |

```
SELECT * from pg_dist_partition;
 logicalrelid  | partmethod |                                                        partkey                                                         | colocationid | repmodel 
---------------+------------+------------------------------------------------------------------------------------------------------------------------+--------------+----------
 github_events | h          | {VAR :varno 1 :varattno 4 :vartype 20 :vartypmod -1 :varcollid 0 :varlevelsup 0 :varnoold 1 :varoattno 4 :location -1} |            2 | c
 (1 row)
```

### <a name="shard-table"></a>Tabulka horizontálních oddílů

Tabulka pg \_ DIST \_ horizontálních oddílů ukládá metadata o jednotlivých horizontálních oddílů tabulky. Pg_dist_shard obsahuje informace o tom, kterou distribuovanou tabulku horizontálních oddílů patří, a statistiku distribučního sloupce pro horizontálních oddílů.
Pro připojené distribuované tabulky odpovídají tyto statistiky Minimálním a maximálním hodnotám distribučního sloupce. U distribuovaných tabulek hash se jedná o rozsahy tokenů hash přiřazené k tomuto horizontálních oddílů. Tyto statistiky se používají k vyřazení nesouvisejících horizontálních oddílů při výběru dotazů.

| Název          | Typ     | Description                                                                                                                                                                                  |
|---------------|----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| logicalrelid  | regclass | Distribuovaná tabulka, do které tento řádek odpovídá Tato hodnota odkazuje na sloupec relfilenode v tabulce katalogu katalogu pg_class System.                                                          |
| shardid       | bigint   | K tomuto horizontálních oddílů je přiřazen globálně jedinečný identifikátor.                                                                                                                                           |
| shardstorage  | char     | Typ úložiště, který se používá pro tento horizontálních oddílů. V následující tabulce jsou popsány různé typy úložišť.                                                                                               |
| shardminvalue | text     | Pro příkaz připojit distribuované tabulky, minimální hodnotu distribučního sloupce v tomto horizontálních oddílů (včetně). V případě distribuovaných tabulek hash je minimální hodnota tokenu hash přiřazená tomuto horizontálních oddílů (včetně). |
| shardmaxvalue | text     | V případě připojených distribuovaných tabulek maximální hodnota distribučního sloupce v tomto horizontálních oddílů (včetně). V případě distribuovaných tabulek hash je maximální hodnota tokenu hash přiřazená tomuto horizontálních oddílů (včetně). |

```
SELECT * from pg_dist_shard;
 logicalrelid  | shardid | shardstorage | shardminvalue | shardmaxvalue 
---------------+---------+--------------+---------------+---------------
 github_events |  102026 | t            | 268435456     | 402653183
 github_events |  102027 | t            | 402653184     | 536870911
 github_events |  102028 | t            | 536870912     | 671088639
 github_events |  102029 | t            | 671088640     | 805306367
 (4 rows)
```

#### <a name="shard-storage-types"></a>Typy úložiště horizontálních oddílů

Sloupec shardstorage v pg \_ DIST \_ horizontálních oddílů označuje typ úložiště, který se používá pro horizontálních oddílů. Stručný přehled různých typů úložiště horizontálních oddílů a jejich reprezentace najdete níže.

| Typ úložiště | Hodnota Shardstorage | Description                                                                        |
|--------------|--------------------|------------------------------------------------------------------------------------|
| STOLNÍ        | 's                | Označuje, že horizontálních oddílů ukládá data patřící do běžné distribuované tabulky.         |
| SLOUPC     | r                | Označuje, že horizontálních oddílů ukládá sloupcová data. (Používá se v distribuovaných cstore_fdw tabulkách) |
| FOREIGN      | FJ                | Indikuje, že horizontálních oddílů ukládá cizí data. (Používá se v distribuovaných file_fdw tabulkách)    |

### <a name="shard-placement-table"></a>Tabulka umístění horizontálních oddílů

\_ \_ Tabulka umístění pro umístění balíčku pg sleduje umístění replik horizontálních oddílů na pracovních uzlech. Každá replika horizontálních oddílů přiřazená konkrétnímu uzlu se nazývá umístění horizontálních oddílů. V této tabulce jsou uloženy informace o stavu a umístění každého umístění horizontálních oddílů.

| Název        | Typ   | Description                                                                                                                               |
|-------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------|
| shardid     | bigint | Horizontálních oddílů identifikátor přidružený k tomuto umístění Tato hodnota odkazuje na sloupec shardid v tabulce katalogu pg_dist_shard.             |
| shardstate  | int    | Popisuje stav tohoto umístění. V následující části jsou popsány různé horizontálních oddílů stavy.                                         |
| shardlength | bigint | U připojených distribuovaných tabulek je velikost umístění horizontálních oddílů v pracovním uzlu v bajtech. V případě distribuovaných tabulek hash je nula.            |
| placementid | bigint | Jedinečný automaticky generovaný identifikátor pro každé jednotlivé umístění.                                                                           |
| GroupID     | int    | Označuje skupinu jednoho primárního serveru a nula nebo více sekundárních serverů při použití modelu replikace streamování. |

```
SELECT * from pg_dist_placement;
  shardid | shardstate | shardlength | placementid | groupid
 ---------+------------+-------------+-------------+---------
   102008 |          1 |           0 |           1 |       1
   102008 |          1 |           0 |           2 |       2
   102009 |          1 |           0 |           3 |       2
   102009 |          1 |           0 |           4 |       3
   102010 |          1 |           0 |           5 |       3
   102010 |          1 |           0 |           6 |       4
   102011 |          1 |           0 |           7 |       4
```

#### <a name="shard-placement-states"></a>Stavy umístění horizontálních oddílů

Citus () spravuje horizontálních oddílů stav na základě jednotlivých umístění. Pokud umístění přepne systém do nekonzistentního stavu, Citus ho automaticky označí jako nedostupný. Stav umístění je zaznamenán v tabulce pg_dist_shard_placement v rámci sloupce shardstate. Tady je stručný přehled různých stavů umístění horizontálních oddílů:

| Název stavu | Hodnota Shardstate | Description                                                                                                                                                                                                                                                                                                                                                                                                                         |
|------------|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| FINALIZOVÁN  | 1                | Stav nový horizontálních oddílů se vytvoří v. Horizontálních oddílů místa v tomto stavu se považují za aktuální a používají se při plánování a provádění dotazů.                                                                                                                                                                                                                                                                                 |
| TERMÍN   | 3                | Horizontálních oddílů místa v tomto stavu se považují za neaktivní, protože se nesynchronizují s ostatními replikami stejného horizontálních oddílů. K tomuto stavu může dojít v případě, že pro toto umístění dojde k chybě připojení, úpravy (vložení, aktualizace, odstranění) nebo operace DDL. Plánovač dotazů bude během plánování a provádění ignorovat počet míst v tomto stavu. Uživatelé mohou synchronizovat data v těchto horizontálních oddílů s konečnou replikou jako aktivitu na pozadí. |
| TO_DELETE  | 4                | Pokud se Citus pokusí odstranit horizontálních oddílů umístění v reakci na volání master_apply_delete_command a dojde k chybě, umístění se přesune do tohoto stavu. Uživatelé pak můžou tyto horizontálních oddílů odstranit jako následnou aktivitu na pozadí.                                                                                                                                                                                                              |

### <a name="worker-node-table"></a>Tabulka uzlu pracovního procesu

\_ \_ Tabulka uzlu pg DIST obsahuje informace o uzlech pracovních procesů v clusteru.

| Název             | Typ    | Description                                                                                                                                                                                |
|------------------|---------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| NodeId           | int     | Automaticky generovaný identifikátor pro jednotlivý uzel                                                                                                                                          |
| GroupID          | int     | Identifikátor používaný k označení skupiny jednoho primárního serveru a nulového nebo více sekundárních serverů při použití modelu replikace streamování. Ve výchozím nastavení je stejný jako NodeId.         |
| Nodename         | text    | Název hostitele nebo IP adresa uzlu PostgreSQL Worker                                                                                                                                     |
| nodeport         | int     | Číslo portu, na kterém naslouchá pracovní uzel PostgreSQL.                                                                                                                              |
| noderack         | text    | Volitelné Informace o umístění racku pro pracovní uzel.                                                                                                                                 |
| hasmetadata      | boolean | Vyhrazeno pro interní použití.                                                                                                                                                                 |
| IsActive         | boolean | Určuje, zda je uzel aktivní pro přijímání horizontálních oddílůch míst.                                                                                                                                     |
| noderole         | text    | Zda je uzel primární nebo sekundární                                                                                                                                                 |
| nodecluster      | text    | Název clusteru, který obsahuje tento uzel                                                                                                                                               |
| shouldhaveshards | boolean | Pokud je hodnota false, horizontálních oddílů se při opětovném vyrovnávání přesune mimo uzel (vyprázdněno), ani nebude horizontálních oddílů z nových distribuovaných tabulek na uzel, pokud se už nenacházejí v horizontálních oddílů. |

```
SELECT * from pg_dist_node;
 nodeid | groupid | nodename  | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | shouldhaveshards
--------+---------+-----------+----------+----------+-------------+----------+----------+-------------+------------------
      1 |       1 | localhost |    12345 | default  | f           | t        | primary  | default     | t
      2 |       2 | localhost |    12346 | default  | f           | t        | primary  | default     | t
      3 |       3 | localhost |    12347 | default  | f           | t        | primary  | default     | t
(3 rows)
```

### <a name="distributed-object-table"></a>Tabulka distribuovaných objektů

\_ \_ Tabulka objektů citus.pg DIST obsahuje seznam objektů, jako jsou typy a funkce, které byly vytvořeny v uzlu koordinátora a šířeny do pracovních uzlů. Když správce přidá do clusteru nové pracovní uzly, Citus (škálování) automaticky vytvoří kopie distribuovaných objektů na nových uzlech (ve správném pořadí, aby splňovaly závislosti objektů).

| Název                        | Typ    | Description                                          |
|-----------------------------|---------|------------------------------------------------------|
| ClassID                     | identifikátor     | Třída distribuovaného objektu                      |
| objid                       | identifikátor     | ID objektu distribuovaného objektu                  |
| objsubid                    | integer | Dílčí ID objektu distribuovaného objektu, například attnum |
| typ                        | text    | Část stabilní adresy používané během upgradů pg   |
| object_names                | text []  | Část stabilní adresy používané během upgradů pg   |
| object_args                 | text []  | Část stabilní adresy používané během upgradů pg   |
| distribution_argument_index | integer | Platí pouze pro distribuované funkce nebo procedury.      |
| colocationid                | integer | Platí pouze pro distribuované funkce nebo procedury.      |

\"Stabilní adresy \" jednoznačně identifikují objekty nezávisle na určitém serveru. Citus (škálování na úrovni) sleduje objekty během upgradu PostgreSQL pomocí stabilních adres vytvořených pomocí funkce [pg \_ identifikují \_ objekt \_ jako \_ adresu ()](https://www.postgresql.org/docs/current/functions-info.html#FUNCTIONS-INFO-OBJECT-TABLE) .

Zde \' je příklad, jak `create_distributed_function()` Přidat položky do `citus.pg_dist_object` tabulky:

```psql
CREATE TYPE stoplight AS enum ('green', 'yellow', 'red');

CREATE OR REPLACE FUNCTION intersection()
RETURNS stoplight AS $$
DECLARE
        color stoplight;
BEGIN
        SELECT *
          FROM unnest(enum_range(NULL::stoplight)) INTO color
         ORDER BY random() LIMIT 1;
        RETURN color;
END;
$$ LANGUAGE plpgsql VOLATILE;

SELECT create_distributed_function('intersection()');

-- will have two rows, one for the TYPE and one for the FUNCTION
TABLE citus.pg_dist_object;
```

```
-[ RECORD 1 ]---------------+------
classid                     | 1247
objid                       | 16780
objsubid                    | 0
type                        |
object_names                |
object_args                 |
distribution_argument_index |
colocationid                |
-[ RECORD 2 ]---------------+------
classid                     | 1255
objid                       | 16788
objsubid                    | 0
type                        |
object_names                |
object_args                 |
distribution_argument_index |
colocationid                |
```

### <a name="colocation-group-table"></a>Tabulka skupiny kolocation

Tabulka v \_ rámci \_ kolokace na str DIST obsahuje informace o tom, které tabulky \' horizontálních oddílů by se měly umístit dohromady nebo společně [umístěné](concepts-hyperscale-colocation.md).
Pokud jsou dvě tabulky ve stejné skupině kolocation (Citus), zajistíte, aby horizontálních oddílů se stejnými hodnotami oddílů byly umístěny na stejných pracovních uzlech.
Společné umístění umožňuje optimalizace spojení, určité distribuované souhrny a podporu cizích klíčů. Horizontálních oddílů společné umístění je odvozeno v případě, že se počty horizontálních oddílů, faktory replikace a sloupce oddílů shodují mezi dvěma tabulkami. je však možné zadat vlastní skupinu kolocation při vytváření distribuované tabulky, pokud je to žádoucí.

| Název                   | Typ | Description                                                                   |
|------------------------|------|-------------------------------------------------------------------------------|
| colocationid           | int  | Jedinečný identifikátor pro skupinu kolocation, na kterou tento řádek odpovídá          |
| shardcount             | int  | Počet horizontálních oddílů pro všechny tabulky v této skupině kolocation                          |
| replicationfactor      | int  | Faktor replikace pro všechny tabulky v této skupině kolocation.                  |
| distributioncolumntype | identifikátor  | Typ distribučního sloupce pro všechny tabulky v této skupině kolocation. |

```
SELECT * from pg_dist_colocation;
  colocationid | shardcount | replicationfactor | distributioncolumntype 
 --------------+------------+-------------------+------------------------
             2 |         32 |                 2 |                     20
  (1 row)
```

### <a name="rebalancer-strategy-table"></a>Tabulka strategie pro vyrovnávání zatížení

Tato tabulka definuje strategie, které [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) můžou použít k určení místa přesunutí horizontálních oddílů.

| Název                           | Typ    | Description                                                                                                                                       |
|--------------------------------|---------|---------------------------------------------------------------------------------------------------------------------------------------------------|
| default_strategy               | boolean | Určuje, zda má rebalance_table_shards zvolit tuto strategii ve výchozím nastavení. K aktualizaci tohoto sloupce použijte citus_set_default_rebalance_strategy             |
| shard_cost_function            | regproc | Identifikátor pro nákladovou funkci, která musí přebírat shardid jako bigint a vracet svůj pojem nákladů, jako typ Real                                |
| node_capacity_function         | regproc | Identifikátor funkce kapacity, která musí přebírat NodeId jako int a vracet svůj pojem kapacity uzlu jako typ Real                          |
| shard_allowed_on_node_function | regproc | Identifikátor pro funkci, která předaná shardid bigint a nodeidarg int, vrátí logickou hodnotu pro to, jestli Citus může ukládat horizontálních oddílů na uzel. |
| default_threshold              | float4  | Prahová hodnota pro pokládání uzlu je příliš plná nebo příliš prázdná, která určuje, kdy se má rebalance_table_shards pokusit přesunout horizontálních oddílů                    |
| minimum_threshold              | float4  | Ochrana před tím, než je hodnota prahové hodnoty rebalance_table_shards () nastavena příliš nízká                                                  |

Instalace Citus () je dodávána s těmito strategiemi v tabulce:

```postgresql
SELECT * FROM pg_dist_rebalance_strategy;
```

```
-[ RECORD 1 ]-------------------+-----------------------------------
Name                            | by_shard_count
default_strategy                | true
shard_cost_function             | citus_shard_cost_1
node_capacity_function          | citus_node_capacity_1
shard_allowed_on_node_function  | citus_shard_allowed_on_node_true
default_threshold               | 0
minimum_threshold               | 0
-[ RECORD 2 ]-------------------+-----------------------------------
Name                            | by_disk_size
default_strategy                | false
shard_cost_function             | citus_shard_cost_by_disk_size
node_capacity_function          | citus_node_capacity_1
shard_allowed_on_node_function  | citus_shard_allowed_on_node_true
default_threshold               | 0.1
minimum_threshold               | 0.01
```

Výchozí strategie, `by_shard_count` přiřadí každou horizontálních oddílů stejné náklady. Jeho účinkem je vyrovnat počet horizontálních oddílů napříč uzly. Druhá předdefinovaná strategie `by_disk_size` přiřadí každému horizontálních oddílůi náklady odpovídající velikosti disku v bajtech a horizontálních oddílů, které se společně s ním nacházejí. Velikost disku se počítá pomocí `pg_total_relation_size` , takže zahrnuje indexy. Tato strategie se pokusí dosáhnout na každém uzlu stejné místo na disku. Všimněte si prahové hodnoty 0,1 – zabrání zbytečnému přesunu horizontálních oddílů způsobenému nedůležitým rozdílem v místě na disku.

#### <a name="creating-custom-rebalancer-strategies"></a>Vytváření vlastních strategií pro vyrovnávání zatížení

Tady jsou příklady funkcí, které se dají použít v rámci nových strategií horizontálních oddílů pro vyrovnávání zatížení, a které jsou zaregistrované v [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md?#rebalancer-strategy-table) pomocí funkce [citus_add_rebalance_strategy](reference-hyperscale-functions.md#citus_add_rebalance_strategy) .

-   Nastavení výjimky kapacity uzlu podle vzoru názvu hostitele:

    ```postgresql
    CREATE FUNCTION v2_node_double_capacity(nodeidarg int)
        RETURNS boolean AS $$
        SELECT
            (CASE WHEN nodename LIKE '%.v2.worker.citusdata.com' THEN 2 ELSE 1 END)
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;
    ```

-   Nové vyrovnávání podle počtu dotazů, které jdou na horizontálních oddílů, podle měření [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table):

    ```postgresql
    -- example of shard_cost_function

    CREATE FUNCTION cost_of_shard_by_number_of_queries(shardid bigint)
        RETURNS real AS $$
        SELECT coalesce(sum(calls)::real, 0.001) as shard_total_queries
        FROM citus_stat_statements
        WHERE partition_key is not null
            AND get_shard_id_for_distribution_column('tab', partition_key) = shardid;
    $$ LANGUAGE sql;
    ```

-   Izolace konkrétního horizontálních oddílů (10000) na uzlu (adresa \' 10.0.0.1 \' ):

    ```postgresql
    -- example of shard_allowed_on_node_function

    CREATE FUNCTION isolate_shard_10000_on_10_0_0_1(shardid bigint, nodeidarg int)
        RETURNS boolean AS $$
        SELECT
            (CASE WHEN nodename = '10.0.0.1' THEN shardid = 10000 ELSE shardid != 10000 END)
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;

    -- The next two definitions are recommended in combination with the above function.
    -- This way the average utilization of nodes is not impacted by the isolated shard.
    CREATE FUNCTION no_capacity_for_10_0_0_1(nodeidarg int)
        RETURNS real AS $$
        SELECT
            (CASE WHEN nodename = '10.0.0.1' THEN 0 ELSE 1 END)::real
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;
    CREATE FUNCTION no_cost_for_10000(shardid bigint)
        RETURNS real AS $$
        SELECT
            (CASE WHEN shardid = 10000 THEN 0 ELSE 1 END)::real
        $$ LANGUAGE sql;
    ```

### <a name="query-statistics-table"></a>Tabulka statistických údajů dotazu

Citus () poskytuje `citus_stat_statements` informace o tom, jak se dotazy provádějí, a pro koho. Je \' analogická k (a lze k němu připojit) zobrazení [ \_ \_ příkazů pg stat](https://www.postgresql.org/docs/current/static/pgstatstatements.html) v PostgreSQL, které sleduje statistiku rychlosti dotazů.

Toto zobrazení může trasovat dotazy na původce klientů v aplikaci s více klienty, což pomáhá při rozhodování o izolaci tenanta.

| Název          | Typ   | Description                                                                      |
|---------------|--------|----------------------------------------------------------------------------------|
| QueryId       | bigint | identifikátor (vhodný pro spojení pg_stat_statements)                                   |
| UserID        | identifikátor    | uživatel, který spustil dotaz                                                           |
| dbid          | identifikátor    | instance databáze koordinátora                                                 |
| query         | text   | řetězec anonymního dotazu                                                          |
| modulu      | text   | Používá se prováděcí modul Citus: adaptivní, v reálném čase, úlohy – sledování úkolů, směrovač nebo vložení – výběr. |
| partition_key | text   | hodnota distribučního sloupce v dotazech spuštěných směrovači, jinak NULL               |
| volání         | bigint | počet, kolikrát se dotaz spustil                                                |

```sql
-- create and populate distributed table
create table foo ( id int );
select create_distributed_table('foo', 'id');
insert into foo select generate_series(1,100);

-- enable stats
-- pg_stat_statements must be in shared_preload libraries
create extension pg_stat_statements;

select count(*) from foo;
select * from foo where id = 42;

select * from citus_stat_statements;
```

Výsledky:

```
-[ RECORD 1 ]-+----------------------------------------------
queryid       | -909556869173432820
userid        | 10
dbid          | 13340
query         | insert into foo select generate_series($1,$2)
executor      | insert-select
partition_key |
calls         | 1
-[ RECORD 2 ]-+----------------------------------------------
queryid       | 3919808845681956665
userid        | 10
dbid          | 13340
query         | select count(*) from foo;
executor      | adaptive
partition_key |
calls         | 1
-[ RECORD 3 ]-+----------------------------------------------
queryid       | 5351346905785208738
userid        | 10
dbid          | 13340
query         | select * from foo where id = $1
executor      | adaptive
partition_key | 42
calls         | 1
```

Upozornění

-   Data statistiky se nereplikují a výhra \' t se nekoná při selhání databáze nebo převzetí služeb při selhání.
-   Sleduje omezený počet dotazů, které nastavuje `pg_stat_statements.max` GUC (výchozí 5000).
-   Chcete-li zkrátit tabulku, použijte `citus_stat_statements_reset()` funkci

### <a name="distributed-query-activity"></a>Aktivita distribuovaného dotazu

Citus () poskytuje speciální zobrazení pro sledování dotazů a zámků v celém clusteru, včetně dotazů specifických pro horizontálních oddílů interně používaných k sestavení výsledků pro distribuované dotazy.

-   **citus \_ DIST \_ stat \_ Activity**: zobrazuje distribuované dotazy, které jsou spuštěny na všech uzlech. Nadmnožina `pg_stat_activity` , použitelná všude, kde je tato.
-   **\_ \_ \_ aktivita citus Worker stat**: zobrazuje dotazy na pracovní procesy včetně fragmentů dotazů na jednotlivé horizontálních oddílů.
-   **citus \_ Lock \_ čeká**: blokované dotazy v rámci clusteru.

První dvě zobrazení zahrnují všechny sloupce [ \_ \_ aktivity pg stat](https://www.postgresql.org/docs/current/static/monitoring-stats.html#PG-STAT-ACTIVITY-VIEW) plus hostitelský Hostitel/port pracovního procesu, který inicioval dotaz a hostitele/port koordinátora clusteru.

Zvažte například možnost počítat řádky v distribuované tabulce:

```postgresql
-- run from worker on localhost:9701

SELECT count(*) FROM users_table;
```

Zobrazuje se dotaz v `citus_dist_stat_activity` :

```postgresql
SELECT * FROM citus_dist_stat_activity;

-[ RECORD 1 ]----------+----------------------------------
query_hostname         | localhost
query_hostport         | 9701
master_query_host_name | localhost
master_query_host_port | 9701
transaction_number     | 1
transaction_stamp      | 2018-10-05 13:27:20.691907+03
datid                  | 12630
datname                | postgres
pid                    | 23723
usesysid               | 10
usename                | citus
application\_name      | psql
client\_addr           | 
client\_hostname       | 
client\_port           | -1
backend\_start         | 2018-10-05 13:27:14.419905+03
xact\_start            | 2018-10-05 13:27:16.362887+03
query\_start           | 2018-10-05 13:27:20.682452+03
state\_change          | 2018-10-05 13:27:20.896546+03
wait\_event_type       | Client
wait\_event            | ClientRead
state                  | idle in transaction
backend\_xid           | 
backend\_xmin          | 
query                  | SELECT count(*) FROM users_table;
backend\_type          | client backend
```

Tento dotaz vyžaduje informace ze všech horizontálních oddílů. Některé z informací jsou v horizontálních oddílů `users_table_102038` , které se ukládají v `localhost:9700` . Dotaz, který přistupuje k horizontálních oddílů, se zobrazí, když prohlížíte `citus_worker_stat_activity` zobrazení:

```postgresql
SELECT * FROM citus_worker_stat_activity;

-[ RECORD 1 ]----------+-----------------------------------------------------------------------------------------
query_hostname         | localhost
query_hostport         | 9700
master_query_host_name | localhost
master_query_host_port | 9701
transaction_number     | 1
transaction_stamp      | 2018-10-05 13:27:20.691907+03
datid                  | 12630
datname                | postgres
pid                    | 23781
usesysid               | 10
usename                | citus
application\_name      | citus
client\_addr           | ::1
client\_hostname       | 
client\_port           | 51773
backend\_start         | 2018-10-05 13:27:20.75839+03
xact\_start            | 2018-10-05 13:27:20.84112+03
query\_start           | 2018-10-05 13:27:20.867446+03
state\_change          | 2018-10-05 13:27:20.869889+03
wait\_event_type       | Client
wait\_event            | ClientRead
state                  | idle in transaction
backend\_xid           | 
backend\_xmin          | 
query                  | COPY (SELECT count(*) AS count FROM users_table_102038 users_table WHERE true) TO STDOUT
backend\_type          | client backend
```

V `query` poli se zobrazí data, která se zkopírují ze horizontálních oddílů, která se mají spočítat.

> [!NOTE]
> Pokud dotaz směrovače (například jeden tenant v aplikaci s více klienty, SELECT
> * V tabulce, kde tenant_id = X) je spuštěn bez bloku transakce, \_ \_ bude název hostitele hlavního dotazu \_ a \_ \_ sloupce portů hostitele hlavního dotazu \_ null v \_ aktivitě citus Worker \_ stat \_ .

Pokud chcete zjistit `citus_lock_waits` , jak funguje, můžeme vytvořit situaci uzamčení ručně. Nejdřív \' nastavili jsme testovací tabulku od koordinátora:

```postgresql
CREATE TABLE numbers AS
  SELECT i, 0 AS j FROM generate_series(1,10) AS i;
SELECT create_distributed_table('numbers', 'i');
```

Pak můžete pomocí dvou relací koordinátora spustit tuto sekvenci příkazů:

```postgresql
-- session 1                           -- session 2
-------------------------------------  -------------------------------------
BEGIN;
UPDATE numbers SET j = 2 WHERE i = 1;
                                       BEGIN;
                                       UPDATE numbers SET j = 3 WHERE i = 1;
                                       -- (this blocks)
```

V `citus_lock_waits` zobrazení se zobrazuje situace.

```postgresql
SELECT * FROM citus_lock_waits;

-[ RECORD 1 ]-------------------------+----------------------------------------
waiting_pid                           | 88624
blocking_pid                          | 88615
blocked_statement                     | UPDATE numbers SET j = 3 WHERE i = 1;
current_statement_in_blocking_process | UPDATE numbers SET j = 2 WHERE i = 1;
waiting_node_id                       | 0
blocking_node_id                      | 0
waiting_node_name                     | coordinator_host
blocking_node_name                    | coordinator_host
waiting_node_port                     | 5432
blocking_node_port                    | 5432
```

V tomto příkladu se jedná o dotazy pocházející z koordinátora, ale zobrazení také může vypsat zámky mezi dotazy pocházejícími z pracovních procesů (spuštěnými s Citus () MX pro instanci).

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak některé [funkce Citus ()](reference-hyperscale-functions.md) mění systémové tabulky.
* Kontrola konceptů [uzlů a tabulek](concepts-hyperscale-nodes.md)
