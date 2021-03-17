---
title: 'Kurz: horizontálních oddílů data na pracovních uzlech – škálovatelné (Citus) – Azure Database for PostgreSQL'
description: V tomto kurzu se dozvíte, jak vytvořit distribuované tabulky a vizualizovat jejich distribuci dat pomocí Azure Database for PostgreSQL Citus (škálovatelné).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 12/16/2020
ms.openlocfilehash: 7d93002af866aa653972182a13ea37d37e912ce8
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97630305"
---
# <a name="tutorial-shard-data-on-worker-nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>Kurz: horizontálních oddílů data na pracovních uzlech v Azure Database for PostgreSQL – škálovatelné (Citus)

V tomto kurzu se naučíte, jak používat Azure Database for PostgreSQL-Citus (škálování na více procesorů):

> [!div class="checklist"]
> * Vytvoření horizontálních oddílů distribuovaných algoritmem hash
> * Zobrazení umístění tabulky horizontálních oddílů
> * Identifikace zkosených distribucí
> * Vytváření omezení pro distribuované tabulky
> * Spouštění dotazů na distribuovaných datech

## <a name="prerequisites"></a>Požadavky

Tento kurz vyžaduje spuštěnou skupinu serverů Citus () se dvěma pracovními uzly. Pokud nemáte spuštěnou skupinu serverů, postupujte podle kurzu [Vytvoření skupiny serverů](tutorial-hyperscale-server-group.md) a pak se vraťte k tomuto.

## <a name="hash-distributed-data"></a>Data distribuovaná algoritmem hash

Distribuce řádků tabulky mezi více PostgreSQL servery je klíčovou technikou pro škálovatelné dotazy v Citus (Scale). Společně může více uzlů obsahovat více dat než tradiční databáze a v mnoha případech může k provádění dotazů využít paralelní procesory procesů paralelně.

V části požadavky jsme vytvořili skupinu serverů Citus (s použitím dvou pracovních uzlů).

![koordinátor a dva pracovní procesy](tutorial-hyperscale-shard/nodes.png)

Tabulky metadat v uzlu koordinátora sledují pracovní procesy a distribuovaná data. Aktivní pracovní procesy můžeme kontrolovat v [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table) tabulce.

```sql
select nodeid from pg_dist_node where isactive;
```
```
 nodeid | nodename
--------+-----------
      1 | 10.0.0.21
      2 | 10.0.0.23
```

> [!NOTE]
> Nodenames na úrovni Citus () jsou interní IP adresy ve virtuální síti a skutečné adresy, které vidíte, se mohou lišit.

### <a name="rows-shards-and-placements"></a>Řádky, horizontálních oddílů a místa

Aby bylo možné používat prostředky procesoru a úložiště pracovních uzlů, musíme distribuovat data tabulky v celé skupině serverů.  Distribuce tabulky přiřadí jednotlivé řádky do logické skupiny s názvem *horizontálních oddílů.* Pojďme vytvořit tabulku a distribuovat ji:

```sql
-- create a table on the coordinator
create table users ( email text primary key, bday date not null );

-- distribute it into shards on workers
select create_distributed_table('users', 'email');
```

Citus () přiřadí každý řádek horizontálních oddílů na základě hodnoty *distribučního sloupce*, který je v našem případě určen jako `email` . Každý řádek bude v přesně jednom horizontálních oddílů a každý horizontálních oddílů může obsahovat více řádků.

![Tabulka uživatelů s řádky ukazujícími na horizontálních oddílů](tutorial-hyperscale-shard/table.png)

Ve výchozím nastavení `create_distributed_table()` je 32 horizontálních oddílů, jak se dá zobrazit podle počtu v tabulce metadat [pg_dist_shard](reference-hyperscale-metadata.md#shard-table):

```sql
select logicalrelid, count(shardid)
  from pg_dist_shard
 group by logicalrelid;
```
```
 logicalrelid | count
--------------+-------
 users        |    32
```

Citus () používá `pg_dist_shard` tabulku k přiřazení řádků horizontálních oddílů, a to na základě hodnoty hash hodnoty ve sloupci distribuce. Podrobnosti o hodnotě hash jsou pro tento kurz neimportované. V čem se můžeme dotazovat na to, které hodnoty se mapují na to, která ID horizontálních oddílů:

```sql
-- Where would a row containing hi@test.com be stored?
-- (The value doesn't have to actually be present in users, the mapping
-- is a mathematical operation consulting pg_dist_shard.)
select get_shard_id_for_distribution_column('users', 'hi@test.com');
```
```
 get_shard_id_for_distribution_column
--------------------------------------
                               102008
```

Mapování řádků na horizontálních oddílů je čistě logické. Horizontálních oddílů se musí přiřadit ke konkrétním pracovním uzlům pro úložiště, v jakém rozsahu (Citus) volá *umístění horizontálních oddílů*.

![horizontálních oddílů přiřazené k pracovníkům](tutorial-hyperscale-shard/shard-placement.png)

Můžeme se podívat na horizontálních oddílů místa v [pg_dist_placement](reference-hyperscale-metadata.md#shard-placement-table).
Po připojení k ostatním tabulkám metadat, které jsme si poznamenali, vidíte, kde každý horizontálních oddílů žije.

```sql
-- limit the output to the first five placements

select
    shard.logicalrelid as table,
    placement.shardid as shard,
    node.nodename as host
from
    pg_dist_placement placement,
    pg_dist_node node,
    pg_dist_shard shard
where placement.groupid = node.groupid
  and shard.shardid = placement.shardid
order by shard
limit 5;
```
```
 table | shard  |    host
-------+--------+------------
 users | 102008 | 10.0.0.21
 users | 102009 | 10.0.0.23
 users | 102010 | 10.0.0.21
 users | 102011 | 10.0.0.23
 users | 102012 | 10.0.0.21
```

### <a name="data-skew"></a>Zkosit data

Skupina serverů se spouští efektivně, když umístíte data rovnoměrně na pracovní uzly a když na stejné pracovní procesy umístíte související data. V této části se zaměříme na první část s jednotným umístěním.

Aby bylo možné Ukázat, vytvoříme pro tabulku ukázková data `users` :

```sql
-- load sample data
insert into users
select
    md5(random()::text) || '@test.com',
    date_trunc('day', now() - random()*'100 years'::interval)
from generate_series(1, 1000);
```

Pokud chcete zobrazit velikosti horizontálních oddílů, můžeme na horizontálních oddílů spouštět [funkce pro velikost tabulky](https://www.postgresql.org/docs/current/functions-admin.html#FUNCTIONS-ADMIN-DBSIZE) .

```sql
-- sizes of the first five shards
select *
from
    run_command_on_shards('users', $cmd$
      select pg_size_pretty(pg_table_size('%1$s'));
    $cmd$)
order by shardid
limit 5;
```
```
 shardid | success | result
---------+---------+--------
  102008 | t       | 16 kB
  102009 | t       | 16 kB
  102010 | t       | 16 kB
  102011 | t       | 16 kB
  102012 | t       | 16 kB
```

Vidíte, že horizontálních oddílů má stejnou velikost. Již jsme zjistili, že se zastavění rovnoměrně rozdělují mezi pracovní procesy, takže můžeme odvodit, že pracovní uzly obsahují zhruba stejný počet řádků.

Řádky v našem `users` příkladu jsou distribuovány rovnoměrně, protože vlastnosti distribučního sloupce, `email` .

1. Počet e-mailových adres je větší nebo roven počtu horizontálních oddílů.
2. Počet řádků na e-mailovou adresu byl podobný (v našem případě přesně jeden řádek na adresu, protože jsme deklarovali klíč e-mailu).

Libovolná volba sloupce tabulky a distribuce, kde selže kterákoli z vlastností, skončí s nestejnoměrnou velikostí dat na pracovních procesech, to znamená, že se *data zkosí*.

### <a name="add-constraints-to-distributed-data"></a>Přidání omezení do distribuovaných dat

Použití Citus () vám umožní nadále využívat zabezpečení relační databáze, včetně [omezení databáze](https://www.postgresql.org/docs/current/ddl-constraints.html).
Existuje však omezení. Vzhledem k povaze distribuovaných systémů nezpůsobuje škálování na více referencích omezení jedinečnosti ani referenční integritu mezi pracovními uzly.

Podívejme `users` se na náš příklad tabulky se související tabulkou.

```sql
-- books that users own
create table books (
    owner_email text references users (email),
    isbn text not null,
    title text not null
);

-- distribute it
select create_distributed_table('books', 'owner_email');
```

Pro efektivitu distribuujeme `books` stejným způsobem jako v `users` e-mailové adrese vlastníka: Distribuce podle podobných hodnot sloupců se nazývá společné [umístění](concepts-hyperscale-colocation.md).

Nedošlo k potížím při distribuci knih s cizím klíčem uživatelům, protože klíč byl v distribučním sloupci. Při vytváření klíče by ale došlo k potížím `isbn` :

```sql
-- will not work
alter table books add constraint books_isbn unique (isbn);
```
```
ERROR:  cannot create constraint on "books"
DETAIL: Distributed relations cannot have UNIQUE, EXCLUDE, or
        PRIMARY KEY constraints that do not include the partition column
        (with an equality operator if EXCLUDE).
```

V distribuované tabulce to nejlepší můžeme udělat, aby sloupce byly jedinečné modulo v distribučním sloupci:

```sql
-- a weaker constraint is allowed
alter table books add constraint books_isbn unique (owner_email, isbn);
```

Výše uvedené omezení zpřístupňuje v rámci jedinečného ISBN pro uživatele. Další možností je vytvořit v knihách [referenční tabulku](howto-hyperscale-modify-distributed-tables.md#reference-tables) a nikoli distribuovanou tabulku a vytvořit samostatnou distribuovanou tabulku, která přidružuje knihy k uživatelům.

## <a name="query-distributed-tables"></a>Dotaz na distribuované tabulky

V předchozích částech jsme viděli, jak se distribuované řádky tabulky nacházejí v horizontálních oddílů na pracovních uzlech. Ve většině případů nemusíte znát, jak nebo kde se data ukládají do skupiny serverů. Citus) obsahuje vykonavatel distribuovaných dotazů, který automaticky rozděluje běžné dotazy SQL. Spouští je paralelně na pracovních uzlech blízko k datům.

Například můžeme spustit dotaz, který najde průměrnou věkovou skupinu uživatelů, a to tak, aby se distribuovaná tabulka povedla `users` jako na běžnou tabulku koordinátora.

```sql
select avg(current_date - bday) as avg_days_old from users;
```
```
    avg_days_old
--------------------
 17926.348000000000
```

![dotaz na horizontálních oddílů prostřednictvím koordinátora](tutorial-hyperscale-shard/query-fragments.png)

Na pozadí vykonavatel Citus () vytvoří samostatný dotaz pro každý horizontálních oddílů, spustí ho na pracovních procesech a zkombinuje výsledek. Můžete ji zobrazit, pokud použijete příkaz PostgreSQL vysvětlit:

```sql
explain select avg(current_date - bday) from users;
```
```
                                  QUERY PLAN
----------------------------------------------------------------------------------
 Aggregate  (cost=500.00..500.02 rows=1 width=32)
   ->  Custom Scan (Citus Adaptive)  (cost=0.00..0.00 rows=100000 width=16)
     Task Count: 32
     Tasks Shown: One of 32
     ->  Task
       Node: host=10.0.0.21 port=5432 dbname=citus
       ->  Aggregate  (cost=41.75..41.76 rows=1 width=16)
         ->  Seq Scan on users_102040 users  (cost=0.00..22.70 rows=1270 width=4)
```

Výstup ukazuje příklad plánu spuštění pro *fragment dotazu* běžícího na horizontálních oddílů 102040 (tabulka `users_102040` na 10.0.0.21 pracovního procesu). Ostatní fragmenty nejsou zobrazeny, protože jsou podobné. Vidíte, že pracovní uzel prohledává tabulky horizontálních oddílů a aplikuje agregaci. Uzel koordinátora kombinuje agregace pro konečný výsledek.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jsme vytvořili distribuovanou tabulku a dozvěděli jsme se o jejím horizontálních oddílů a místech. Zjistili jsme, že používáme omezení jedinečnosti a cizích klíčů a nakonec jste viděli, jak distribuované dotazy fungují na vysoké úrovni.

* Další informace o [typech tabulek](concepts-hyperscale-nodes.md) Citus ()
* Získání dalších tipů pro [Výběr distribučního sloupce](concepts-hyperscale-choose-distribution-column.md)
* Seznamte se s výhodami [meziumístění tabulky](concepts-hyperscale-colocation.md) .
