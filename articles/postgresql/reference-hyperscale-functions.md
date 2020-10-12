---
title: Funkce SQL – Citus (Scale) – Azure Database for PostgreSQL
description: Funkce v rozhraní Citus API pro škálování na více systému ()
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 16c3a45e0d88a0546772b3fdc855c90f2e450d14
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91250327"
---
# <a name="functions-in-the-hyperscale-citus-sql-api"></a>Funkce v rozhraní Citus API pro škálování na více systému ()

Tato část obsahuje referenční informace o uživatelsky definovaných funkcích poskytovaných v rámci škálování (Citus). Tyto funkce vám pomůžou poskytnout další distribuované funkce škálování (Citus) jiné než standardní příkazy SQL.

> [!NOTE]
>
> Skupiny serverů Citus (), na kterých běží starší verze modulu Citus, nemusí nabízet všechny funkce uvedené níže.

## <a name="table-and-shard-ddl"></a>Tabulka a horizontálních oddílů DDL

### <a name="create_distributed_table"></a>vytvořit \_ distribuovanou \_ tabulku

Funkce vytvořit \_ distribuovanou \_ tabulku () se používá k definování distribuované tabulky a vytvoření jejího horizontálních oddílůu, pokud se jedná o tabulku s mezihodnotou hash. Tato funkce přebírá název tabulky, distribuční sloupec a volitelnou distribuční metodu a vkládá příslušná metadata k označení tabulky jako distribuované. Tato funkce je ve výchozím nastavení nastavená na hodnotu ' hash ', pokud není zadána žádná metoda distribuce. Pokud je tabulka distribuována pomocí algoritmu hash, funkce také vytvoří pracovní proces horizontálních oddílů na základě hodnot konfigurace horizontálních oddílů Count a horizontálních oddílů Replication faktor. Pokud tabulka obsahuje všechny řádky, jsou automaticky distribuovány do pracovních uzlů.

Tato funkce nahradí využití hlavní \_ tabulky Create \_ Distributed \_ Table () následovanou hlavním \_ vytvořením \_ pracovního procesu \_ horizontálních oddílů ().

#### <a name="arguments"></a>Argumenty

** \_ název tabulky:** název tabulky, která musí být distribuována.

**distribuční \_ sloupec:** sloupec, do kterého má být tabulka distribuována.

** \_ typ distribuce:** (nepovinný) metoda, podle které se má tabulka distribuovat Přípustné hodnoty jsou Append nebo hash s výchozí hodnotou hash.

**spolunajít \_ s:** (volitelné) zahrnout aktuální tabulku do skupiny kolocation v jiné tabulce. Ve výchozím nastavení jsou tabulky společně umístěny, pokud jsou distribuovány pomocí sloupců stejného typu, mají stejný horizontálních oddílů počet a mají stejný faktor replikace. Možné hodnoty pro `colocate_with` jsou `default` , pokud `none` chcete spustit novou skupinu kolokace nebo název jiné tabulky, která se má v této tabulce vyhledat.  (Viz společné [umístění tabulky](concepts-hyperscale-colocation.md).)

Pamatujte, že výchozí hodnota `colocate_with` má implicitní společné umístění. Společné [umístění](concepts-hyperscale-colocation.md) může být skvělé, když se tabulky vztahují nebo budou spojeny.  Pokud ale nesouvisí s dvěma tabulkami, ale k použití stejného datového typu u jejich distribučních sloupců se používá stejný datový typ, nechtěně prohledání může snížit výkon během [horizontálních oddílůho vyrovnávání zatížení](howto-hyperscale-scaling.md#rebalance-shards).  Tabulka horizontálních oddílů bude v kaskádě přesunuta zbytečně \" .\"

Pokud nová distribuovaná tabulka nesouvisí s jinými tabulkami, je nejlepší určit `colocate_with => 'none'` .

#### <a name="return-value"></a>Návratová hodnota

Není k dispozici

#### <a name="example"></a>Příklad

V tomto příkladu se databáze informují o tom, že \_ by měla být tabulka událostí GitHubu distribuovaná pomocí algoritmu hash ve \_ sloupci ID úložiště.

```postgresql
SELECT create_distributed_table('github_events', 'repo_id');

-- alternatively, to be more explicit:
SELECT create_distributed_table('github_events', 'repo_id',
                                colocate_with => 'github_repo');
```

### <a name="create_reference_table"></a>vytvořit \_ referenční \_ tabulku

Funkce vytvořit \_ referenční \_ tabulku () se používá k definování malého odkazu nebo tabulky dimenzí. Tato funkce přijímá název tabulky a vytvoří distribuovanou tabulku s pouze jedním horizontálních oddílů, která se replikuje do všech pracovních uzlů.

#### <a name="arguments"></a>Argumenty

** \_ název tabulky:** název malé dimenze nebo referenční tabulky, která musí být distribuována.

#### <a name="return-value"></a>Návratová hodnota

Není k dispozici

#### <a name="example"></a>Příklad

Tento příklad informuje databázi o tom, že by měla být definovaná tabulka pro zemi jako referenční tabulka.

```postgresql
SELECT create_reference_table('nation');
```

### <a name="upgrade_to_reference_table"></a>upgradovat \_ na \_ referenční \_ tabulku

Funkce upgradu \_ na \_ referenční \_ tabulku () převezme existující distribuovanou tabulku, která má horizontálních oddílů počet 1, a upgraduje ji tak, aby byla rozpoznanou referenční tabulkou. Po volání této funkce bude tabulka obsahovat, jako kdyby byla vytvořena pomocí [create_reference_table](#create_reference_table).

#### <a name="arguments"></a>Argumenty

** \_ název tabulky:** název distribuované tabulky (s horizontálních oddílů Count = 1), která bude distribuována jako referenční tabulka.

#### <a name="return-value"></a>Návratová hodnota

Není k dispozici

#### <a name="example"></a>Příklad

Tento příklad informuje databázi o tom, že by měla být definovaná tabulka pro zemi jako referenční tabulka.

```postgresql
SELECT upgrade_to_reference_table('nation');
```

### <a name="mark_tables_colocated"></a>označit \_ tabulky společně \_ umístěné

\_Funkce tabulky značek společně \_ umístěné () převezme distribuovanou tabulku (zdroj) a seznam dalších (cílů) a umístí cíle do stejné skupiny pro společné umístění jako zdroj. Pokud zdroj ještě není ve skupině, tato funkce vytvoří jeden a přiřadí mu zdroj a cíl.

Tabulky se stejným umístěním by se měly provádět v době distribuce v tabulce `colocate_with` , a to prostřednictvím parametru [create_distributed_table](#create_distributed_table), ale v `mark_tables_colocated` případě potřeby se můžou postarat později.

#### <a name="arguments"></a>Argumenty

** \_ název zdrojové tabulky \_ :** název distribuované tabulky, jejíž skupina pro kolokace bude přiřazována k odpovídajícím cílům.

** \_ názvy cílových tabulek \_ :** pole názvů distribuovaných cílových tabulek nesmí být prázdné. Tyto distribuované tabulky musí odpovídat zdrojové tabulce v:

> -   Metoda distribuce
> -   typ distribučního sloupce
> -   typ replikace
> -   počet horizontálních oddílů

Pokud žádná z výše uvedených použití není, Citus (škálování) vyvolá chybu. Například pokus o vyhledání tabulek `apples` a `oranges` jejichž typy distribučních sloupců se liší v těchto případech:

```
ERROR:  XX000: cannot colocate tables apples and oranges
DETAIL:  Distribution column types don't match for apples and oranges.
```

#### <a name="return-value"></a>Návratová hodnota

Není k dispozici

#### <a name="example"></a>Příklad

Tento příklad vloží `products` a `line_items` do stejné skupiny pro společné umístění jako `stores` . V příkladu se předpokládá, že jsou všechny tyto tabulky distribuované na sloupci se shodným typem, s nejpravděpodobnějším \" ID úložiště.\"

```postgresql
SELECT mark_tables_colocated('stores', ARRAY['products', 'line_items']);
```

### <a name="create_distributed_function"></a>vytvořit \_ distribuovanou \_ funkci

Šíří funkci z uzlu koordinátora na pracovníky a označí ji pro distribuované provádění. Když je v koordinátoru volána distribuovaná funkce, Citus (škálování) používá hodnotu \" argumentu distribuce \" k výběru pracovního uzlu pro spuštění funkce. Provádění funkce na pracovních procesech zvyšuje paralelismus a může přenést kód blíž k datům v horizontálních oddílů pro zajištění nižší latence.

Cesta pro vyhledávání Postgres není šířena od koordinátora k pracovníkům během provádění distribuované funkce, takže kód distribuované funkce by měl plně kvalifikovat názvy databázových objektů. Oznámení vygenerovaná funkcemi se uživatelům nezobrazují také.

#### <a name="arguments"></a>Argumenty

** \_ název funkce:** název funkce, která se má distribuovat. Název musí obsahovat typy parametrů funkce v závorkách, protože více funkcí může mít stejný název v PostgreSQL. Instance se například `'foo(int)'` liší od `'foo(int, text)'` .

** \_ \_ název distribučního** argumentu: (nepovinný) název argumentu, podle kterého se má distribuovat. Pro usnadnění (nebo pokud argumenty funkce nemají názvy) je povolený zástupný symbol pozice, například `'$1'` . Pokud tento parametr není zadán, funkce s názvem by `function_name` byla vytvořena pouze na pracovních procesech. Pokud jsou pracovní uzly přidány do budoucna, funkce bude automaticky vytvořena také.

**spolunajít \_ s:** (volitelné) když distribuovaná funkce čte nebo zapisuje do distribuované tabulky (nebo obecněji skupiny pro společné umístění), nezapomeňte tuto tabulku pojmenovat pomocí `colocate_with` parametru. Pak se každé vyvolání funkce spustí na pracovním uzlu, který obsahuje relevantní horizontálních oddílů.

#### <a name="return-value"></a>Návratová hodnota

Není k dispozici

#### <a name="example"></a>Příklad

```postgresql
-- an example function which updates a hypothetical
-- event_responses table which itself is distributed by event_id
CREATE OR REPLACE FUNCTION
  register_for_event(p_event_id int, p_user_id int)
RETURNS void LANGUAGE plpgsql AS $fn$
BEGIN
  INSERT INTO event_responses VALUES ($1, $2, 'yes')
  ON CONFLICT (event_id, user_id)
  DO UPDATE SET response = EXCLUDED.response;
END;
$fn$;

-- distribute the function to workers, using the p_event_id argument
-- to determine which shard each invocation affects, and explicitly
-- colocating with event_responses which the function updates
SELECT create_distributed_function(
  'register_for_event(int, int)', 'p_event_id',
  colocate_with := 'event_responses'
);
```

## <a name="metadata--configuration-information"></a>Metadata/konfigurační informace

### <a name="master_get_table_metadata"></a>Hlavní \_ načtení \_ \_ metadat tabulky

Funkce Master \_ Get \_ Table \_ metadata () se dá použít k vrácení metadat souvisejících s distribucí pro distribuovanou tabulku. Tato metadata zahrnují ID vztahu, typ úložiště, způsob distribuce, distribuční sloupec, počet replikací, maximální velikost horizontálních oddílů a zásady umístění horizontálních oddílů pro tabulku. Za sebou tato funkce dotazuje tabulky metadat Citus (), aby získala požadované informace a zřetězí ji do řazené kolekce členů předtím, než ji vrátí uživateli.

#### <a name="arguments"></a>Argumenty

** \_ název tabulky:** název distribuované tabulky, pro kterou chcete načíst metadata.

#### <a name="return-value"></a>Návratová hodnota

Řazená kolekce členů obsahující následující informace:

**Logical \_ relid:** OID distribuované tabulky. Odkazuje na sloupec relfilenode v \_ tabulce katalogu systému třídy PG.

**součást \_ \_ Typ úložiště:** typ úložiště, který se používá pro tabulku. Může být t ' (standardní tabulka), ' f ' (cizí tabulka) nebo ' c ' (sloupcová tabulka).

** \_ Metoda Part:** metoda distribuce použitá pro tabulku. Může být "a" (append) nebo "h" (hash).

**Part \_ Key:** sloupec distribuce pro tabulku.

** \_ počet replik částí \_ :** aktuální počet horizontálních oddílů replikace.

** \_ maximální \_ Velikost součásti:** aktuální maximální velikost horizontálních oddílů v bajtech.

** \_ Zásady umístění částí \_ :** zásady umístění horizontálních oddílů používané pro umístění horizontálních oddílů tabulky. Může být 1 (místní uzel-First) nebo 2 (kruhové dotazování).

#### <a name="example"></a>Příklad

Následující příklad načte a zobrazí metadata tabulky pro \_ tabulku událostí GitHubu.

```postgresql
SELECT * from master_get_table_metadata('github_events');
 logical_relid | part_storage_type | part_method | part_key | part_replica_count | part_max_size | part_placement_policy 
---------------+-------------------+-------------+----------+--------------------+---------------+-----------------------
         24180 | t                 | h           | repo_id  |                  2 |    1073741824 |                     2
(1 row)
```

### <a name="get_shard_id_for_distribution_column"></a>získat \_ \_ ID horizontálních oddílů \_ pro \_ distribuční \_ sloupec

Citus () přiřadí každý řádek distribuované tabulky k horizontálních oddílů na základě hodnoty sloupce distribuce řádku a metody distribuce tabulky. Ve většině případů je přesné mapování podrobnosti nízké úrovně, které správce databáze může ignorovat. Může to ale být užitečné pro určení horizontálních oddílů řádku, a to buď pro ruční úlohy údržby databáze, nebo jenom pro uspokojení objevili. `get_shard_id_for_distribution_column`Funkce poskytuje tyto informace pro tabulky s hodnotami hash a rozsah a také referenční tabulky. Nefunguje pro připojení distribuce.

#### <a name="arguments"></a>Argumenty

** \_ název tabulky:** distribuovaná tabulka.

** \_ hodnota distribuce:** hodnota distribučního sloupce.

#### <a name="return-value"></a>Návratová hodnota

Horizontálních oddílů ID Citus () přidružuje k hodnotě distribučního sloupce pro danou tabulku.

#### <a name="example"></a>Příklad

```postgresql
SELECT get_shard_id_for_distribution_column('my_table', 4);

 get_shard_id_for_distribution_column
--------------------------------------
                               540007
(1 row)
```

### <a name="column_to_column_name"></a>\_do sloupce \_ \_ název sloupce

Přeloží sloupec na `partkey` `pg_dist_partition` název textového sloupce. Překlad je vhodný pro určení distribučního sloupce distribuované tabulky.

Podrobnější diskuzi najdete v tématu [Volba distribučního sloupce](concepts-hyperscale-choose-distribution-column.md).

#### <a name="arguments"></a>Argumenty

** \_ název tabulky:** distribuovaná tabulka.

** \_ text var sloupce \_ :** hodnota `partkey` v `pg_dist_partition` tabulce.

#### <a name="return-value"></a>Návratová hodnota

Název `table_name` distribučního sloupce.

#### <a name="example"></a>Příklad

```postgresql
-- get distribution column name for products table

SELECT column_to_column_name(logicalrelid, partkey) AS dist_col_name
  FROM pg_dist_partition
 WHERE logicalrelid='products'::regclass;
```

Výstup:

```
┌───────────────┐
│ dist_col_name │
├───────────────┤
│ company_id    │
└───────────────┘
```

### <a name="citus_relation_size"></a>\_Velikost vztahu \_ citus

Získá místo na disku využívané všemi horizontálních oddílůy zadané distribuované tabulky.
Místo na disku zahrnuje velikost \" hlavní větve, \" ale nezahrnuje mapu viditelnosti a mapu volného místa pro horizontálních oddílů.

#### <a name="arguments"></a>Argumenty

**logicalrelid:** název distribuované tabulky.

#### <a name="return-value"></a>Návratová hodnota

Velikost v bajtech jako bigint.

#### <a name="example"></a>Příklad

```postgresql
SELECT pg_size_pretty(citus_relation_size('github_events'));
```

```
pg_size_pretty
--------------
23 MB
```

### <a name="citus_table_size"></a>\_Velikost tabulky \_ citus

Získejte místo na disku využité všemi horizontálních oddílůy zadané distribuované tabulky, kromě indexů (ale včetně informačních zpráv, mapy volného místa a mapy viditelnosti).

#### <a name="arguments"></a>Argumenty

**logicalrelid:** název distribuované tabulky.

#### <a name="return-value"></a>Návratová hodnota

Velikost v bajtech jako bigint.

#### <a name="example"></a>Příklad

```postgresql
SELECT pg_size_pretty(citus_table_size('github_events'));
```

```
pg_size_pretty
--------------
37 MB
```

### <a name="citus_total_relation_size"></a>\_Celková \_ Velikost vztahu \_ citus

Získejte celkové místo na disku využité všemi horizontálních oddílů zadanými distribuovanými tabulkami, včetně všech indexů a dat informačních zpráv.

#### <a name="arguments"></a>Argumenty

**logicalrelid:** název distribuované tabulky.

#### <a name="return-value"></a>Návratová hodnota

Velikost v bajtech jako bigint.

#### <a name="example"></a>Příklad

```postgresql
SELECT pg_size_pretty(citus_total_relation_size('github_events'));
```

```
pg_size_pretty
--------------
73 MB
```

### <a name="citus_stat_statements_reset"></a>\_ \_ resetování příkazů stat příkazu citus \_

Odebere všechny řádky z [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table).
Tato funkce funguje nezávisle na `pg_stat_statements_reset()` . Chcete-li obnovit všechny statistiky, zavolejte obě funkce.

#### <a name="arguments"></a>Argumenty

Není k dispozici

#### <a name="return-value"></a>Návratová hodnota

Žádné

## <a name="server-group-management-and-repair"></a>Správa a oprava skupin serverů

### <a name="master_copy_shard_placement"></a>\_umístění hlavní kopie \_ horizontálních oddílů \_

Pokud se umístění horizontálních oddílů nepovede aktualizovat během příkazu Upravit nebo v operaci DDL, bude označeno jako neaktivní. \_ \_ Funkce umístění hlavní kopie horizontálních oddílů se \_ pak může volat za účelem opravy neaktivního umístění horizontálních oddílů pomocí dat z umístění v pořádku.

K opravě horizontálních oddílů funkce nejprve přenechá nestavové horizontálních oddílů umístění a znovu ho vytvoří pomocí schématu koordinátora. Po vytvoření umístění horizontálních oddílů funkce zkopíruje data z umístění v pořádku a aktualizuje metadata tak, aby označila nové umístění horizontálních oddílů jako v pořádku. Tato funkce zajišťuje, že horizontálních oddílů bude během opravy chráněn před všemi souběžnými úpravami.

#### <a name="arguments"></a>Argumenty

** \_ ID horizontálních oddílů:** ID horizontálních oddílů, které se má opravit

** \_ \_ název zdrojového uzlu:** název DNS uzlu, na kterém je přítomno dobré umístění horizontálních oddílů ( \" zdrojový \" uzel).

** \_ port zdrojového uzlu \_ :** port na zdrojovém uzlu pracovního procesu, na kterém server databáze naslouchá.

** \_ \_ název cílového uzlu:** název DNS uzlu, na kterém je přítomné neplatné umístění horizontálních oddílů ( \" cílový \" uzel).

** \_ port cílového uzlu \_ :** port na cílovém uzlu pracovního procesu, na kterém server databáze naslouchá.

#### <a name="return-value"></a>Návratová hodnota

Není k dispozici

#### <a name="example"></a>Příklad

Níže uvedený příklad opraví neaktivní umístění horizontálních oddílů horizontálních oddílů 12345, které je k dispozici na databázovém serveru, na kterém je spuštěný na ' špatném \_ hostiteli ' na portu 5432. K jejich opravě budou používat data z zdravého umístění horizontálních oddílů, které je k dispozici na serveru, na kterém běží na ' dobrém \_ hostiteli ' na portu.
5432.

```postgresql
SELECT master_copy_shard_placement(12345, 'good_host', 5432, 'bad_host', 5432);
```

### <a name="master_move_shard_placement"></a>\_umístění hlavního přesunutí \_ horizontálních oddílů \_

Tato funkce přesune daný horizontálních oddílů (a horizontálních oddílů společně umístěný) z jednoho uzlu do druhého. Obvykle se používá nepřímo během horizontálních oddílůho vyrovnávání zatížení místo přímého volání správcem databáze.

Existují dva způsoby, jak přesunout data: blokování nebo neblokování. Přístup k blokování znamená, že během přesunu všech úprav horizontálních oddílů jsou pozastaveny.
Druhý způsob, kterým se vyhnete blokování horizontálních oddílů zápisů, spoléhá na logickou replikaci Postgres 10.

Po úspěšné operaci přesunu se horizontálních oddílů ve zdrojovém uzlu odstraní. Pokud se přesun v kterémkoli okamžiku nepovede, tato funkce vyvolá chybu a zdrojový a cílový uzel ponechá beze změny.

#### <a name="arguments"></a>Argumenty

** \_ ID horizontálních oddílů:** ID horizontálních oddílů, které se má přesunout.

** \_ \_ název zdrojového uzlu:** název DNS uzlu, na kterém je přítomno dobré umístění horizontálních oddílů ( \" zdrojový \" uzel).

** \_ port zdrojového uzlu \_ :** port na zdrojovém uzlu pracovního procesu, na kterém server databáze naslouchá.

** \_ \_ název cílového uzlu:** název DNS uzlu, na kterém je přítomné neplatné umístění horizontálních oddílů ( \" cílový \" uzel).

** \_ port cílového uzlu \_ :** port na cílovém uzlu pracovního procesu, na kterém server databáze naslouchá.

** \_ režim přenosu horizontálních oddílů \_ :** (volitelné) zadejte metodu replikace, jestli se má použít logická replikace POSTGRESQL nebo příkaz pro kopírování mezi pracovními procesy. Možné hodnoty jsou:

> -   `auto`: Vyžaduje identitu repliky, pokud je možné logickou replikaci. v opačném případě použijte starší chování (např. horizontálních oddílů Repair, PostgreSQL 9,6). Toto je výchozí hodnota.
> -   `force_logical`: Použijte logickou replikaci, i když tabulka nemá identitu repliky. Jakékoli souběžné příkazy Update/Delete v tabulce selžou při replikaci.
> -   `block_writes`: Použijte kopírování (blokující zápisy) pro tabulky, které nemají primární klíč nebo identitu repliky.

#### <a name="return-value"></a>Návratová hodnota

Není k dispozici

#### <a name="example"></a>Příklad

```postgresql
SELECT master_move_shard_placement(12345, 'from_host', 5432, 'to_host', 5432);
```

### <a name="rebalance_table_shards"></a>vyvážit \_ \_ horizontálních oddílů tabulky

\_ \_ Funkce tabulky horizontálních oddílů () pro vyrovnávání zatížení přesune horizontálních oddílů ze zadané tabulky, aby byly rovnoměrně rozloženy mezi pracovními procesy. Funkce nejprve vypočítá seznam přesunů, které je třeba provést, aby bylo zajištěno, že je skupina serverů v rámci dané prahové hodnoty vyvážená. Pak přesune horizontálních oddílů umístění jeden po jednom ze zdrojového uzlu do cílového uzlu a aktualizuje odpovídající horizontálních oddílů metadata tak, aby odrážela přesun.

Každému horizontálních oddílů se při určování, jestli horizontálních oddílů \" rovnoměrně distribuuje, přiřadí náklady. \" Ve výchozím nastavení má každý horizontálních oddílů stejné náklady (hodnota 1), takže distribuce za účelem vyrovnání nákladů napříč zaměstnanci je stejná jako vyrovnání počtu horizontálních oddílů na každém z nich. Strategie konstantních nákladů se volá \" podle \_ \_ počtu horizontálních oddílů \" a je výchozí strategií pro nové vyrovnávání zatížení.

Výchozí strategii je vhodná pro tyto případy:

*  Horizontálních oddílů mají přibližně stejnou velikost.
*  Horizontálních oddílů získá zhruba stejný objem provozu.
*  Pracovní uzly mají stejnou velikost/typ.
*  Horizontálních oddílů nebylo připnuté na konkrétní pracovní procesy.

Pokud některý z těchto předpokladů nedrží, výchozí vyrovnávání zatížení může mít za následek špatný plán. V takovém případě můžete strategii přizpůsobit pomocí `rebalance_strategy` parametru.

Aby bylo [get_rebalance_table_shards_plan](#get_rebalance_table_shards_plan) \_ \_ možné zobrazit a ověřit akce, které mají být provedeny, je vhodné volat get_rebalance_table_shards_plan před spuštěním horizontálních oddílůí tabulky s vyrovnáváním zatížení.

#### <a name="arguments"></a>Argumenty

** \_ název tabulky:** (nepovinný) název tabulky, jejíž horizontálních oddílů musí být znovu vyrovnaný. Pokud je hodnota NULL, pak znovu vyvážit všechny existující skupiny kolokace.

**prahová hodnota:** (volitelné) číslo float mezi 0,0 a 1,0, které určuje maximální poměr rozdílu využití uzlu z průměrného využití. Například zadání 0,1 způsobí, že se horizontálních oddílů pro vyrovnávání zatížení všech uzlů bude uchovávat stejný počet horizontálních oddílů ± 10%.
Horizontálních oddílů Nástroj pro vyrovnávání zatížení se konkrétně pokusí konvergovat využití všech pracovních uzlů do průměrného využití (1 – prahová hodnota) \* \_ \. . (1
+ prahová hodnota) \* průměr \_ rozsahu využití.

**Max \_ horizontálních oddílů \_ přesunů:** (volitelné) maximální počet horizontálních oddílů, které se mají přesunout.

**seznam vyloučených \_ horizontálních oddílů \_ :** (volitelné) identifikátory horizontálních oddílů, které by se během operace obnovení rovnováhy neměly přesunout.

** \_ režim přenosu horizontálních oddílů \_ :** (volitelné) zadejte metodu replikace, jestli se má použít logická replikace POSTGRESQL nebo příkaz pro kopírování mezi pracovními procesy. Možné hodnoty jsou:

> -   `auto`: Vyžaduje identitu repliky, pokud je možné logickou replikaci. v opačném případě použijte starší chování (např. horizontálních oddílů Repair, PostgreSQL 9,6). Toto je výchozí hodnota.
> -   `force_logical`: Použijte logickou replikaci, i když tabulka nemá identitu repliky. Jakékoli souběžné příkazy Update/Delete v tabulce selžou při replikaci.
> -   `block_writes`: Použijte kopírování (blokující zápisy) pro tabulky, které nemají primární klíč nebo identitu repliky.

**pouze vyprázdnit \_ :** (volitelné) Pokud má hodnotu true, přesuňte horizontálních oddílů mimo pracovní uzly, které mají `shouldhaveshards` v [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table)nastavené na hodnotu false; přesuňte žádné jiné horizontálních oddílů.

** \_ strategie vyrovnávání zatížení:** (nepovinný) název strategie v [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table).
Pokud je tento argument vynechán, funkce zvolí výchozí strategii, jak je uvedeno v tabulce.

#### <a name="return-value"></a>Návratová hodnota

Není k dispozici

#### <a name="example"></a>Příklad

Níže uvedený příklad se pokusí znovu vyrovnávat horizontálních oddílů tabulky událostí GitHubu \_ v rámci výchozí prahové hodnoty.

```postgresql
SELECT rebalance_table_shards('github_events');
```

Tento příklad použití se pokusí o vyrovnávání zatížení \_ tabulky událostí GitHubu bez přesunutí horizontálních oddílů s ID 1 a 2.

```postgresql
SELECT rebalance_table_shards('github_events', excluded_shard_list:='{1,2}');
```

### <a name="get_rebalance_table_shards_plan"></a>získat \_ \_ \_ plán horizontálních oddílů tabulky pro vyrovnávání zatížení \_

Výstup plánovaných pohybů [rebalance_table_shards](#rebalance_table_shards) horizontálních oddílů bez jejich provedení.
I když je pravděpodobné, že \_ plán horizontálních oddílů tabulky pro vyrovnávání zatížení \_ \_ může mít \_ za výsledek trochu jiný plán než \_ \_ volání tabulky horizontálních oddílů se stejnými argumenty. Nejsou spouštěny současně, takže fakta týkající se skupiny serverů \- – například místo na disku \- – může být mezi voláními odlišná.

#### <a name="arguments"></a>Argumenty

Stejné argumenty jako horizontálních oddílů tabulky s vyrovnáváním zatížení \_ \_ : vztah, prahová hodnota, Max \_ horizontálních oddílů – \_ přesunout, vyloučený \_ \_ seznam horizontálních oddílů a jenom vyprázdnit \_ . Význam těchto argumentů naleznete v dokumentaci této funkce.

#### <a name="return-value"></a>Návratová hodnota

Řazené kolekce členů obsahující tyto sloupce:

-   ** \_ název tabulky**: tabulka, jejíž horizontálních oddílů by přesunul
-   **shardid**: horizontálních oddílů
-   ** \_ Velikost horizontálních oddílů**: velikost v bajtech
-   název **zdroje**: název hostitele zdrojového uzlu
-   **sourceport**: port zdrojového uzlu
-   **cílový_název**: název hostitele cílového uzlu
-   **TARGETPORT**: port cílového uzlu

### <a name="get_rebalance_progress"></a>získat \_ průběh převážení \_

Po zahájení horizontálních oddílů rovnováha `get_rebalance_progress()` funkce vypíše průběh každého horizontálních oddílů. Monitoruje plánované a spouštěné přesuny `rebalance_table_shards()` .

#### <a name="arguments"></a>Argumenty

Není k dispozici

#### <a name="return-value"></a>Návratová hodnota

Řazené kolekce členů obsahující tyto sloupce:

-   **SessionID**: Postgres PID monitoru pro vyrovnávání zatížení
-   ** \_ název tabulky**: tabulka, jejíž horizontálních oddílů se přesouvá.
-   **shardid**: horizontálních oddílů
-   ** \_ Velikost horizontálních oddílů**: velikost v bajtech
-   název **zdroje**: název hostitele zdrojového uzlu
-   **sourceport**: port zdrojového uzlu
-   **cílový_název**: název hostitele cílového uzlu
-   **TARGETPORT**: port cílového uzlu
-   **průběh**: 0 = čeká se na přesunutí; 1 = přesun; 2 = dokončeno

#### <a name="example"></a>Příklad

```sql
SELECT * FROM get_rebalance_progress();
```

```
┌───────────┬────────────┬─────────┬────────────┬───────────────┬────────────┬───────────────┬────────────┬──────────┐
│ sessionid │ table_name │ shardid │ shard_size │  sourcename   │ sourceport │  targetname   │ targetport │ progress │
├───────────┼────────────┼─────────┼────────────┼───────────────┼────────────┼───────────────┼────────────┼──────────┤
│      7083 │ foo        │  102008 │    1204224 │ n1.foobar.com │       5432 │ n4.foobar.com │       5432 │        0 │
│      7083 │ foo        │  102009 │    1802240 │ n1.foobar.com │       5432 │ n4.foobar.com │       5432 │        0 │
│      7083 │ foo        │  102018 │     614400 │ n2.foobar.com │       5432 │ n4.foobar.com │       5432 │        1 │
│      7083 │ foo        │  102019 │       8192 │ n3.foobar.com │       5432 │ n4.foobar.com │       5432 │        2 │
└───────────┴────────────┴─────────┴────────────┴───────────────┴────────────┴───────────────┴────────────┴──────────┘
```

### <a name="citus_add_rebalance_strategy"></a>citus \_ Přidat \_ strategii převážení \_

Připojí řádek do [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md?#rebalancer-strategy-table) .

#### <a name="arguments"></a>Argumenty

Další informace o těchto argumentech naleznete v tématu odpovídající hodnoty sloupce v `pg_dist_rebalance_strategy` .

**Name:** identifikátor nové strategie

**horizontálních oddílů \_ cost \_ Function:** identifikuje funkci použitou k určení \" nákladů na \" jednotlivé horizontálních oddílůy.

** \_ funkce kapacity uzlu \_ :** identifikuje funkci pro měření kapacity uzlu.

**horizontálních oddílů \_ povolená \_ u \_ \_ funkce Node:** Určuje funkci, která určuje, který horizontálních oddílů se dá umístit na které uzly.

**výchozí \_ prahová hodnota:** prahová hodnota s plovoucí desetinnou čárkou, která vyhodnotí, jak přesně se mají vyrovnávat kumulativní horizontálních oddílů náklady mezi uzly

**minimální \_ prahová hodnota:** (nepovinný) sloupec pro zabezpečení, který obsahuje minimální hodnotu povolenou pro mezní hodnotu argumentu pro vyrovnávání zatížení \_ tabulky \_ horizontálních oddílů (). Jeho výchozí hodnota je 0.

#### <a name="return-value"></a>Návratová hodnota

Není k dispozici

### <a name="citus_set_default_rebalance_strategy"></a>citus \_ nastavit \_ výchozí \_ strategii pro vyrovnávání zatížení \_

Aktualizujte [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table) tabulku a změňte strategii s názvem podle jejího argumentu na výchozí hodnotu vybranou při vyrovnávání horizontálních oddílů.

#### <a name="arguments"></a>Argumenty

**Name (název):** název strategie v rámci \_ \_ strategie rerovnováhy pro distribuci pg \_

#### <a name="return-value"></a>Návratová hodnota

Není k dispozici

#### <a name="example"></a>Příklad

```postgresql
SELECT citus_set_default_rebalance_strategy('by_disk_size');
```

### <a name="citus_remote_connection_stats"></a>\_Statistika vzdáleného \_ připojení \_ citus

\_ \_ Funkce statistiky vzdáleného připojení citus \_ () zobrazuje počet aktivních připojení ke každému vzdálenému uzlu.

#### <a name="arguments"></a>Argumenty

Není k dispozici

#### <a name="example"></a>Příklad

```postgresql
SELECT * from citus_remote_connection_stats();
```

```
    hostname    | port | database_name | connection_count_to_node
----------------+------+---------------+--------------------------
 citus_worker_1 | 5432 | postgres      |                        3
(1 row)
```

### <a name="master_drain_node"></a>\_uzel hlavní vyprázdnění \_

Funkce hlavního \_ vyprázdnění \_ uzlu () přesouvá horizontálních oddílů z určeného uzlu a do jiných uzlů, které `shouldhaveshards` v [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table)mají nastavené na hodnotu true. Zavolejte funkci před odebráním uzlu ze skupiny serverů a vypnutím fyzického serveru uzlu.

#### <a name="arguments"></a>Argumenty

**Node:** Název hostitele uzlu, který se má vyprázdnit

**nodeport:** Číslo portu, který se má vyprázdnit

** \_ režim přenosu horizontálních oddílů \_ :** (volitelné) zadejte metodu replikace, jestli se má použít logická replikace POSTGRESQL nebo příkaz pro kopírování mezi pracovními procesy. Možné hodnoty jsou:

> -   `auto`: Vyžaduje identitu repliky, pokud je možné logickou replikaci. v opačném případě použijte starší chování (např. horizontálních oddílů Repair, PostgreSQL 9,6). Toto je výchozí hodnota.
> -   `force_logical`: Použijte logickou replikaci, i když tabulka nemá identitu repliky. Jakékoli souběžné příkazy Update/Delete v tabulce selžou při replikaci.
> -   `block_writes`: Použijte kopírování (blokující zápisy) pro tabulky, které nemají primární klíč nebo identitu repliky.

** \_ strategie vyrovnávání zatížení:** (nepovinný) název strategie v [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table).
Pokud je tento argument vynechán, funkce zvolí výchozí strategii, jak je uvedeno v tabulce.

#### <a name="return-value"></a>Návratová hodnota

Není k dispozici

#### <a name="example"></a>Příklad

Tady jsou typické kroky pro odebrání jednoho uzlu (například "10.0.0.1" na standardním portu PostgreSQL):

1.  Vyprázdněte uzel.

    ```postgresql
    SELECT * from master_drain_node('10.0.0.1', 5432);
    ```

2.  Počkejte, až se příkaz dokončí.

3.  Odebrat uzel

Při vyprazdňování více uzlů doporučujeme místo toho použít [rebalance_table_shards](#rebalance_table_shards) . Tím umožníte, aby Citus plán dopředu a přesunuli horizontálních oddílůy minimální počet pokusů.

1.  Spusťte pro každý uzel, který chcete odebrat:

    ```postgresql
    SELECT * FROM master_set_node_property(node_hostname, node_port, 'shouldhaveshards', false);
    ```

2.  Vyprázdněte je všechny najednou pomocí [rebalance_table_shards](#rebalance_table_shards)

    ```postgresql
    SELECT * FROM rebalance_table_shards(drain_only := true);
    ```

3.  Počkejte, dokud se nedokončí vyprazdňování rovnováhy.

4.  Odebrat uzly

### <a name="replicate_table_shards"></a>replikovat \_ \_ horizontálních oddílů tabulek

Funkce replikovat \_ tabulku \_ horizontálních oddílů () replikuje replikované horizontálních oddílů z dané tabulky. Funkce nejprve vypočítá seznam replikovaných horizontálních oddílů a umístění, ze kterých je možné je načíst pro replikaci. Funkce pak zkopíruje tyto horizontálních oddílů a aktualizuje odpovídající metadata horizontálních oddílů, aby odrážela kopii.

#### <a name="arguments"></a>Argumenty

** \_ název tabulky:** název tabulky, jejíž horizontálních oddílů je třeba replikovat.

** \_ faktor replikace horizontálních oddílů \_ :** (nepovinný) požadovaný faktor replikace, který se má pro každý horizontálních oddílů dosáhnout.

**Max \_ horizontálních oddílů \_ kopií:** (volitelné) maximální počet horizontálních oddílů, které se mají zkopírovat, aby se dosáhlo požadovaného faktoru replikace.

**seznam vyloučených \_ horizontálních oddílů \_ :** (volitelné) identifikátory horizontálních oddílů, které by se během operace replikace neměly kopírovat.

#### <a name="return-value"></a>Návratová hodnota

Není k dispozici

#### <a name="examples"></a>Příklady

Níže uvedený příklad se pokusí replikovat horizontálních oddílů tabulky událostí GitHubu \_ do \_ faktoru replikace horizontálních oddílů \_ .

```postgresql
SELECT replicate_table_shards('github_events');
```

Tento příklad se pokusí přenést horizontálních oddílů \_ tabulky událostí GitHubu do požadovaného faktoru replikace s maximálně 10 horizontálních oddílů kopiemi. Nástroj pro vyrovnávání zatížení bude kopírovat maximálně 10 horizontálních oddílů ve svém pokusu o dosažení požadovaného faktoru replikace.

```postgresql
SELECT replicate_table_shards('github_events', max_shard_copies:=10);
```

### <a name="isolate_tenant_to_new_shard"></a>izolovat \_ tenanta \_ pro \_ nové \_ horizontálních oddílů

Tato funkce vytvoří nový horizontálních oddílů, který bude uchovávat řádky s konkrétní jednou hodnotou v distribučním sloupci. Je to obzvláště užitečné pro případ použití Citus (multi-tenant), kde se velký tenant dá umístit samostatně na vlastní horizontálních oddílů a nakonec na vlastní fyzický uzel.

#### <a name="arguments"></a>Argumenty

** \_ název tabulky:** název tabulky, do které se má načíst nový horizontálních oddílů.

** \_ ID tenanta:** hodnota distribučního sloupce, který se přiřadí novému horizontálních oddílů.

**kaskádová \_ možnost:** (volitelné), pokud je nastavena na \" CASCADE \" také izoluje horizontálních oddílů ze všech tabulek ve [skupině kolocation](concepts-hyperscale-colocation.md)aktuální tabulky.

#### <a name="return-value"></a>Návratová hodnota

** \_ ID horizontálních oddílů:** funkce vrátí jedinečné ID přiřazené nově vytvořeným horizontálních oddílů.

#### <a name="examples"></a>Příklady

Vytvořte nový horizontálních oddílů, který bude obsahovat položky řádku pro tenanta 135:

```postgresql
SELECT isolate_tenant_to_new_shard('lineitem', 135);
```

```
┌─────────────────────────────┐
│ isolate_tenant_to_new_shard │
├─────────────────────────────┤
│                      102240 │
└─────────────────────────────┘
```

## <a name="next-steps"></a>Další kroky

* Mnohé z funkcí v tomto článku upravují tabulky systémových [metadat](reference-hyperscale-metadata.md) .
* [Parametry serveru](reference-hyperscale-parameters.md) přizpůsobují chování některých funkcí.
