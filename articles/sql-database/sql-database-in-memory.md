---
title: Azure SQL Database v paměti technologie | Microsoft Docs
description: Azure SQL Database v paměti technologie výrazně zlepšit výkon transakcí a analýzy úlohy.
services: sql-database
author: jodebrui
manager: craigg
ms.service: sql-database
ms.custom: develop databases
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: jodebrui
ms.openlocfilehash: 89e5497c10f3e24dd84333f095a9bd7c0017ea88
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647863"
---
# <a name="optimize-performance-by-using-in-memory-technologies-in-sql-database"></a>Optimalizace výkonu pomocí technologie v paměti v databázi SQL

Použití technologií v paměti ve službě Azure SQL Database, můžete dosáhnout zlepšení výkonu pomocí různých úloh: transakcí (online zpracování transakcí (OLTP)), analytics (online analytického zpracování (OLAP)) a ve smíšeném (hybridní transakce/analytického zpracování (HTAP)). Z důvodu efektivnější dotazu a zpracování transakcí v paměti technologie také pomoci snížit náklady. Obvykle nemusíte upgradovat cenová úroveň databáze k dosáhnout zvýšení výkonu. V některých případech i je možné snížit cenovou úroveň, při vylepšení výkonu s technologiemi v paměti se přesto zobrazuje.

Zde jsou dva příklady, jak článek OLTP v paměti pomůže výrazně zlepšit výkon:

- Pomocí OLTP v paměti, [kvora obchodními řešeními bylo možné dvakrát jejich zatížení při současném zvyšování Dtu 70 %](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database).
    - DTU znamená *jednotky transakcí databáze*, a obsahuje měření spotřeby prostředků.
- Toto video ukazuje výrazné zlepšení spotřeby prostředků s ukázky pracovního vytížení: [OLTP v paměti v Azure SQL Database Video](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB).
    - Další informace naleznete v příspěvku blogu: [OLTP v paměti v příspěvku blogu databáze SQL Azure](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

Technologie v paměti jsou k dispozici v všechny databáze v úrovni Premium, včetně databází v elastické fondy Premium.

Následující video vysvětluje potenciální zvýšení výkonu s technologiemi v paměti ve službě Azure SQL Database. Pamatujte, že zvýšení výkonu, který se zobrazí vždy závisí na mnoha faktorech, včetně povahu pracovního vytížení a data, vzor přístupu databáze a tak dále.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>
>

Azure SQL Database má následující technologie v paměti:

- *OLTP v paměti* zvyšuje transakce a snižuje latence pro zpracování transakcí. Scénáře využívající OLTP v paměti jsou: transakce vysokou propustností zpracování například obchodní a hraní, přijímání dat ze zařízení IoT, ukládání do mezipaměti, načtení dat a dočasné tabulky a scénáře proměnné tabulky nebo události.
- *Clusterované indexy columnstore* redukuje vašeho úložiště (až 10 x) a zlepšení výkonu pro dotazy analýz a generování sestav. Můžete ji pomocí tabulky faktů v datová Tržiště nevejde se do databáze více dat a zlepšíte výkon. Navíc můžete ho s historických dat v provozní databázi nástroje k archivaci a moct dotaz až 10 x další data.
- *Neclusterovaných indexů columnstore* pro HTAP umožňují v reálném čase proniknout do vaší firmy prostřednictvím dotazování provozní databáze přímo, aniž by bylo nutné spustit nákladné extrakce, transformace a načítání (ETL) proces a počkejte datový sklad vyplnit. Neclusterovaných indexů columnstore povolí velmi rychlé spuštění analytické dotazy na databáze OLTP navíc snižuje dopad na provozní úlohy.
- Můžete taky nechat kombinace paměťově optimalizované tabulky s indexem columnstore. Tato kombinace umožňuje provádět zpracování velmi rychlé transakcí a *souběžně* velmi rychle spustit analytické dotazy na stejná data.

Indexy columnstore a OLTP v paměti se součástí produktu SQL Server od 2012 a 2014, v uvedeném pořadí. Azure SQL Database a SQL Server sdílet stejné implementaci technologií v paměti. Do budoucna, nové funkce pro tyto technologie jsou vydávány v Azure SQL Database nejprve před jejich vydání v systému SQL Server.

Tento článek popisuje aspekty OLTP v paměti a columnstore indexy, které jsou specifické pro Azure SQL Database a také obsahuje ukázky:
- Dopad tyto technologie uvidíte na omezení velikosti úložiště a data.
- Uvidíte, jak spravovat přesouvání databází, které používají tyto technologie mezi různé cenové úrovně.
- Zobrazí se dvou vzorcích, které ilustrují použití OLTP v paměti, jakož i indexy columnstore ve službě Azure SQL Database.

Najdete v následujících materiálech Další informace.

Podrobné informace o technologiích:

- [Přehled OLTP v paměti a scénáře použití](https://msdn.microsoft.com/library/mt774593.aspx) (obsahuje odkazy na informace o začít a Zákaznické případové studie)
- [Dokumentace pro OLTP v paměti](http://msdn.microsoft.com/library/dn133186.aspx)
- [Průvodce indexy Columnstore](https://msdn.microsoft.com/library/gg492088.aspx)
- Hybridní transakcí nebo analytického zpracování (HTAP), také známé jako [provozní analýzu v reálném čase](https://msdn.microsoft.com/library/dn817827.aspx)

Rychlý úvod do na OLTP v paměti: [rychlý Start 1: technologie OLTP v paměti pro rychlejší T-SQL výkonu](http://msdn.microsoft.com/library/mt694156.aspx) (jiný článek vám pomohou začít)

Podrobný videa o technologiích:

- [OLTP v paměti ve službě Azure SQL Database](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (která obsahuje ukázku výkonnostních výhod a postup reprodukování těchto výsledků sami)
- [Videa OLTP v paměti: Co je a v případě nebo postupy pro použití](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/10/03/in-memory-oltp-video-what-it-is-and-whenhow-to-use-it/)
- [Columnstore Index: Analýzy v paměti videa z webu Ignite 2016](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/10/04/columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016/)

## <a name="storage-and-data-size"></a>Velikost úložiště a dat.

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>Limitu velikosti a úložiště dat pro OLTP v paměti

OLTP v paměti obsahuje paměťově optimalizované tabulky, které se používají k ukládání dat uživatele. Tyto tabulky musí nevejdou se do paměti. Vzhledem k tomu, že budete spravovat paměti přímo ve službě SQL Database, máme koncept kvótu pro data uživatelů. Toto je vhodné se označuje jako *OLTP v paměti úložiště*.

Každý podporovaný samostatná databáze cenová úroveň a každý elastického fondu cenová úroveň zahrnuje množství OLTP v paměti úložiště. V tématu [limitů prostředků na základě DTU](sql-database-dtu-resource-limits.md) a [limitů prostředků na základě vCore](sql-database-vcore-resource-limits.md).

Následující položky započítávat vašeho úložiště cap OLTP v paměti:

- Řádky dat aktivního uživatele v paměťově optimalizované tabulky a proměnné tabulek. Všimněte si, že nemáte směrem k krytky počet staré verze řádku.
- Indexy v paměťově optimalizovaných tabulkách.
- Provozní režie operací ALTER TABLE.

Jestli jste nedosáhli krytky, obdržíte chybu na více systémů kvóty a jste již nebude možné vložit nebo aktualizovat data. Pro zmírnění této chybě, odstranit data nebo zvyšte cenová úroveň databáze nebo fond.

Podrobnosti o sledování využití úložiště OLTP v paměti a konfiguraci výstrah při téměř zásahu krytky najdete v tématu [monitorování v paměti úložiště](sql-database-in-memory-oltp-monitoring.md).

#### <a name="about-elastic-pools"></a>O elastické fondy

S elastické fondy úložiště OLTP v paměti je sdílet všechny databáze ve fondu. Proto využití v jedné databáze může potenciálně ovlivnit jiné databáze. Jsou dvě jejich zmírnění pro toto:

- Konfigurace `Max-eDTU` nebo `MaxvCore` pro databáze, které je nižší než počet eDTU nebo vCore pro fond jako celek. Tento maximální caps využití úložiště OLTP v paměti, všechny databáze ve fondu, velikost, která odpovídá počet eDTU.
- Konfigurace `Min-eDTU` nebo `MinvCore` větší než 0. Toto minimum zaručuje, že každá databáze ve fondu má velikost úložiště k dispozici OLTP v paměti, která odpovídá konfigurovaného `Min-eDTU` nebo `vCore`.

### <a name="data-size-and-storage-for-columnstore-indexes"></a>Velikost dat a úložiště pro indexy columnstore

Indexy Columnstore nejsou povinné, aby se vešla do paměti. Proto krytky pouze na velikost indexy je maximální celkovou velikost databáze, která je popsána v [na základě DTU nákupní model](sql-database-service-tiers-dtu.md) a [nákupní model (preview) na základě vCore](sql-database-service-tiers-vcore.md) články.

Při použití Clusterované indexy columnstore sloupcovém komprese se používá pro základní tabulka úložiště. Tato komprese může výrazně snížit nároky na úložiště dat uživatele, což znamená, že můžete začlenit další data v databázi. A komprese může být zvýšena další s [sloupcovém archivace komprese](https://msdn.microsoft.com/library/cc280449.aspx#Using Columnstore and Columnstore Archive Compression). Komprese, můžete dosáhnout závisí na povaze data, ale 10krát komprese není.

Například pokud máte databázi s maximální velikostí 1 terabajt (TB) a dosáhnout 10krát komprese pomocí indexy columnstore, můžete začlenit celkem 10 TB dat uživatele v databázi.

Při použití neclusterovaných indexů columnstore základní tabulka je pořád uložená ve formátu tradiční rowstore. Proto nejsou úspory úložiště stejnou velikost jako s Clusterované indexy columnstore. Pokud chcete nahradit počet tradiční neclusterované indexy s indexem columnstore jeden, uvidíte stále celkové úspory v nároky na úložiště pro tabulku.

## <a name="moving-databases-that-use-in-memory-technologies-between-pricing-tiers"></a>Přesunutí databází, které používají technologie v paměti mezi cenové úrovně

Nejsou nikdy žádné nekompatibility nebo jiné problémy při upgradu na vyšší cenová úroveň, například z standardní, Premium. K dispozici funkce a prostředky pouze zvýšit.

Ale přechod na starší verzi cenové úrovně může mít negativní vliv na vaši databázi. Dopad je obzvláště zřejmá při downgradovat z úrovně Premium standardní nebo základní Pokud databáze obsahuje objekty OLTP v paměti. Paměťově optimalizované tabulky nejsou k dispozici po downgrade (i v případě, že zůstanou viditelné). Stejné aspekty platí při snížení cenová úroveň fondu elastické databáze, nebo přesunutí databáze s technologiemi v paměti do Standard a Basic elastického fondu.

### <a name="in-memory-oltp"></a>OLTP v paměti

*Přechod na starší verzi na Basic nebo Standard*: OLTP v paměti není podporována v databázích v úroveň Standard nebo Basic. Kromě toho není možné přesunout databáze, která obsahuje všechny objekty OLTP v paměti na úroveň Standard nebo Basic.

Není programový způsob, jak pochopit, jestli na danou databázi podporuje OLTP v paměti. Můžete spustit následující dotaz jazyka Transact-SQL:

```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```

Pokud dotaz vrátí **1**, OLTP v paměti je podporováno v této databázi.

Předtím, než jste starší verzi databáze, která se standardní a základní, odeberte všechny paměťově optimalizované tabulky a typů tabulek, jakož i všechny nativně Kompilované moduly T-SQL. Následující dotazy Identifikujte všechny objekty, které je potřeba předtím, než databázi můžete snížit na standardní a základní odebrat:

```
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

*Přechod na starší verzi nižší úrovně Premium*: Data v paměťově optimalizovaných tabulkách musí být přizpůsobena úložiště OLTP v paměti, které souvisí s cenová úroveň databáze nebo není k dispozici elastický fond. Pokud se pokusíte snížit cenovou úroveň, nebo přesunout databáze ve fondu, který nemá dostatek dostupné úložiště OLTP v paměti se operace nezdaří.

### <a name="columnstore-indexes"></a>Indexy Columnstore

*Přechod na starší verzi Basic nebo Standard*: indexy Columnstore jsou podporovány pouze na cenová úroveň Premium a na úrovni Standard S3 a výše a není v základní vrstvě. Když jste starší verzi databáze na úrovni nebo nepodporovaný vrstvy, stane se indexu columnstore není k dispozici. Systém udržuje indexu columnstore, ale nikdy využívá index. Pokud později upgradujete zpět na úrovni nebo podporovanou úroveň, je okamžitě připraven znovu využít indexu columnstore.

Pokud máte **clusterové** columnstore index, celé tabulky nedostupný po downgrade. Proto doporučujeme vyřaďte všechny *clusterové* indexy columnstore před downgradovat databáze na úrovni nebo nepodporovaný vrstvy.

*Přechod na starší verzi na nižší úroveň podporované nebo úroveň*: Tento přechod na starší verzi úspěšná, pokud odpovídá celé databáze v rámci maximální velikost pro cíl cenová úroveň, nebo dostupné úložiště v elastickém fondu. Neexistuje žádný konkrétní vliv z indexů columnstore.


<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="1-install-the-in-memory-oltp-sample"></a>1. Instalace ukázkové OLTP v paměti

Ukázkové databáze AdventureWorksLT můžete vytvořit pomocí několika kliknutí v [portál Azure](https://portal.azure.com/). Pak kroků v této části popisují, jak můžete rozšířit vaše databáze AdventureWorksLT s objekty OLTP v paměti a předvedení výkonnostních výhod.

Více zneužívající vlastností prohlížeče, ale vizuálně výkonu ukázku pro OLTP v paměti najdete v části:

- Verze: [v – paměť oltp-demo-verze 1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- Zdrojový kód: [in-memory-oltp-demo-source-code](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/in-memory/ticket-reservations)

#### <a name="installation-steps"></a>Postup instalace

1. V [portál Azure](https://portal.azure.com/), vytvořit Premium nebo kritické obchodní (preview) databáze na serveru. Nastavte **zdroj** ukázkové databáze AdventureWorksLT. Podrobné pokyny najdete v tématu [vytvořit svoji první databázi Azure SQL](sql-database-get-started-portal.md).

2. Připojení k databázi s SQL Server Management Studio [(SSMS.exe)](http://msdn.microsoft.com/library/mt238290.aspx).

3. Kopírování [OLTP v paměti jazyka Transact-SQL skriptu](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_oltp_sample.sql) do schránky. Skriptu T-SQL vytváří objekty nezbytné v paměti ukázkové databáze AdventureWorksLT, kterou jste vytvořili v kroku 1.

4. Vložit do aplikace SSMS skriptu T-SQL a poté spusťte tento skript. `MEMORY_OPTIMIZED = ON` Příkazy CREATE TABLE klauzule jsou zásadní. Příklad:


```
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### <a name="error-40536"></a>Chyba 40536


Pokud se zobrazí chyba 40536 při spuštění skriptu T-SQL, spusťte následující skript T-SQL, chcete-li ověřit, jestli podporuje databázi v paměti:


```
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


Důsledkem **0** znamená, že v paměti není podporován, a **1** znamená, že je podporovaná. A diagnostikovat problém, zkontrolujte, zda databáze na úroveň služeb Premium.


#### <a name="about-the-created-memory-optimized-items"></a>O vytvořených položek paměťově optimalizované

**Tabulky**: Ukázka obsahuje paměťově optimalizované tabulky:

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


Paměťově optimalizované tabulky prostřednictvím si můžete prohlédnout **Průzkumník objektů** v aplikaci SSMS. Klikněte pravým tlačítkem na **tabulky** > **filtru** > **nastavení filtru** > **je paměťově optimalizovaná**. Hodnota se rovná 1.


Nebo můžete dát dotaz na zobrazení katalogu, jako například:


```
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```


**Nativně kompilované uložené procedury**: SalesLT.usp_InsertSalesOrder_inmem si můžete prohlédnout pomocí zobrazení katalogu dotazu:


```
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```


&nbsp;

### <a name="run-the-sample-oltp-workload"></a>Spuštění ukázky pracovního vytížení OLTP

Jediným rozdílem mezi následující dva *uložené procedury* je, že první postup používá verzích paměťově optimalizované tabulky, zatímco druhý postup používá regulární tabulky na disku:

- SalesLT **.** usp_InsertSalesOrder **_inmem**
- SalesLT **.** usp_InsertSalesOrder **_ondisk**


V této části najdete postup používání užitečný v **ostress.exe** nástroj provést dvě uložené procedury na stressful úrovních. Jak dlouho trvá pro spustí dvě přízvuk dokončíte, můžete porovnat.


Když spustíte ostress.exe, doporučujeme předat hodnoty parametrů, které jsou určené pro obě z následujících akcí:

- Spustit velký počet souběžných připojení pomocí - n100.
- Pomocí mít každý připojení smyčku stovky dobu, pomocí - r500.


Ale můžete chtít začít s mnohem menšími hodnoty jako - n10 a - r 50 zajistit, že vše funguje.


### <a name="script-for-ostressexe"></a>Skript pro ostress.exe


V této části zobrazí skriptu T-SQL, vložené v našem ostress.exe příkazového řádku. Tento skript využívá položky, které byly vytvořeny pomocí skriptu T-SQL, který jste dříve nainstalovali.


Následující skript vloží ukázka prodejní objednávky s pěti položek řádku do těchto paměťově optimalizované *tabulky*:

- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem


```
DECLARE
    @i int = 0,
    @od SalesLT.SalesOrderDetailType_inmem,
    @SalesOrderID int,
    @DueDate datetime2 = sysdatetime(),
    @CustomerID int = rand() * 8000,
    @BillToAddressID int = rand() * 10000,
    @ShipToAddressID int = rand() * 10000;

INSERT INTO @od
    SELECT OrderQty, ProductID
    FROM Demo.DemoSalesOrderDetailSeed
    WHERE OrderID= cast((rand()*60) as int);

WHILE (@i < 20)
begin;
    EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
        @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
    SET @i = @i + 1;
end
```


Chcete-li *_ondisk* verzi předchozí skriptu T-SQL pro ostress.exe, měli byste nahradit oba výskyty *_inmem* substring s *_ondisk*. Tyto náhrady ovlivnit názvy tabulek a uložených procedur.


### <a name="install-rml-utilities-and-ostress"></a>Instalace nástrojů RML a ostress


V ideálním případě by plánujete spouštět ostress.exe na virtuální počítač Azure (VM). Měli byste vytvořit [virtuálního počítače Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) ve stejné Azure geografické oblasti, kde se nachází databáze AdventureWorksLT. Ale ostress.exe na svém přenosném počítači může spouštět místo.


Ve virtuálním počítači nebo na ať hostitele, můžete zvolit, nainstalujte nástroje opětovného přehrání Markup Language (RML). Nástroje zahrnují ostress.exe.

Další informace naleznete v tématu:
- Ostress.exe diskuse ve [ukázkové databáze pro OLTP v paměti](http://msdn.microsoft.com/library/mt465764.aspx).
- [Ukázkové databáze pro OLTP v paměti](http://msdn.microsoft.com/library/mt465764.aspx).
- [Blogu pro instalaci ostress.exe](http://blogs.msdn.com/b/psssql/archive/2013/10/29/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx).



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(http://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(http://msdn.microsoft.com/library/mt465764.aspx)
-->



### <a name="run-the-inmem-stress-workload-first"></a>Spustit *_inmem* nejprve vystavila zátěži pracovního vytížení


Můžete použít *RML Cmd výzva* okno spustíte naše ostress.exe příkazový řádek. Parametry příkazového řádku přímé ostress na:

- Připojení 100 běželo (-n100).
- Každé připojení 50 času spuštění skriptu T-SQL (-r 50).


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


Spuštění předchozího ostress.exe příkazového řádku:


1. Obnovení dat obsah databáze spuštěním následujícího příkazu v aplikaci SSMS odstranit všechna data, která byla vložená všech předchozích spuštění:

    ``` tsql
    EXECUTE Demo.usp_DemoReset;
    ```

2. Text předchozí příkazového řádku ostress.exe kopírovat do schránky.

3. Nahraďte `<placeholders>` pro parametry -S - U -P -d s správné skutečné hodnoty.

4. V okně RML Cmd spusťte do upravená příkazového řádku.


#### <a name="result-is-a-duration"></a>Výsledkem je, doba trvání


Po dokončení ostress.exe zapíše spuštění doba trvání jako jeho poslední řádek výstupu v okně RML Cmd. Například kratší testovací běh už bylo asi 1,5 minuty:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### <a name="reset-edit-for-ondisk-then-rerun"></a>Resetovat, upravit pro *_ondisk*, pak znovu spusťte


Až budete mít výsledek z *_inmem* spustit, proveďte následující kroky pro *_ondisk* spustit:


1. Obnovit databázi v SSMS pro odstranění všech dat, který byl vložen podle předchozího spuštění spustíte následující příkaz:
```
EXECUTE Demo.usp_DemoReset;
```

2. Upravit ostress.exe příkazového řádku k nahrazení všech *_inmem* s *_ondisk*.

3. Spusťte ostress.exe podruhé a zaznamenat výsledek doba trvání.

4. Znovu obnovte databázi (pro odstranění jeho zodpovědné, může být velký objem dat test).


#### <a name="expected-comparison-results"></a>Očekávaný porovnání výsledků

Naše testy v paměti ukázaly, že výkonu vylepšené podle **devětkrát** pro tento zneužívající vlastností prohlížeče zatížení s ostress systémem virtuálního počítače Azure ve stejné oblasti Azure jako databáze.

<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;

## <a name="2-install-the-in-memory-analytics-sample"></a>2. Instalace ukázkové analýzy v paměti


V této části při porovnání výsledků vstupně-výstupní operace a statistiky, když používáte index columnstore a index tradiční b stromu.


Pro analýzu v reálném čase na úloh s online zpracováním je často nejvhodnější použít neclusterovaný index columnstore. Podrobnosti najdete v tématu [popsané indexy Columnstore](http://msdn.microsoft.com/library/gg492088.aspx).



### <a name="prepare-the-columnstore-analytics-test"></a>Příprava testovacího analytics columnstore


1. Použití portálu Azure vytvořit novou databázi AdventureWorksLT od vzorku.
 - Použijte tento přesný název.
 - Vyberte všechny úroveň služeb Premium.

2. Kopírování [sql_in memory_analytics_sample](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_analytics_sample.sql) do schránky.
 - Skriptu T-SQL vytváří objekty nezbytné v paměti ukázkové databáze AdventureWorksLT, kterou jste vytvořili v kroku 1.
 - Tento skript vytvoří tabulce dimenzí a dvě tabulky faktů. Tabulky faktů se naplní 3.5 milionu řádků.
 - Skript může trvat 15 minut.

3. Vložit do aplikace SSMS skriptu T-SQL a poté spusťte tento skript. **COLUMNSTORE** – klíčové slovo v **CREATE INDEX** údajů je velmi důležitý, stejně jako na:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Nastavit úroveň kompatibility 130 AdventureWorksLT:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`

    Úroveň 130 přímo nesouvisí s funkcí v paměti. Ale úroveň 130 obecně poskytuje vyšší výkon dotazu než 120.


#### <a name="key-tables-and-columnstore-indexes"></a>Klíče tabulky a indexy columnstore


- dbo. FactResellerSalesXL_CCI je tabulka, která má clusterovaný index columnstore, která obsahuje rozšířené komprese na *data* úroveň.

- dbo. FactResellerSalesXL_PageCompressed je tabulku, která má ekvivalentní regulární clusterovaný index, který se komprimují jenom na *stránky* úroveň.


#### <a name="key-queries-to-compare-the-columnstore-index"></a>Klíče dotazů k porovnání columnstore index


Existují [několik typů dotazu T-SQL, které můžete spustit](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/clustered_columnstore_sample_queries.sql) zobrazíte vylepšení výkonu. V kroku 2 ve skriptu T-SQL věnujte pozornost tento pár dotazů. Liší se pouze na jednom řádku:


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


Clusterovaný index columnstore probíhá FactResellerSalesXL\_KÚS tabulky.

Následující výpis skriptu T-SQL vytiskne statistiky pro vstupně-výstupní operace a čas pro dotaz každé tabulky.


```
/*********************************************************************
Step 2 -- Overview
-- Page Compressed BTree table v/s Columnstore table performance differences
-- Enable actual Query Plan in order to see Plan differences when Executing
*/
-- Ensure Database is in 130 compatibility mode
ALTER DATABASE AdventureworksLT SET compatibility_level = 130
GO

-- Execute a typical query that joins the Fact Table with dimension tables
-- Note this query will run on the Page Compressed table, Note down the time
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO

SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_PageCompressed a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO


-- This is the same Prior query on a table with a clustered columnstore index CCI
-- The comparison numbers are even more dramatic the larger the table is (this is an 11 million row table only)
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO
SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_CCI a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO

SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO
```

V databázi s P2 cenovou úroveň bude pravděpodobně přibližně devětkrát zvýšení výkonu pro tento dotaz pomocí clusterovaný index columnstore v porovnání s tradiční index. S P15 bude pravděpodobně přibližně 57 časy zvýšení výkonu pomocí columnstore index.



## <a name="next-steps"></a>Další postup

- [Rychlý Start 1: Technologie OLTP v paměti pro dosažení vyššího výkonu T-SQL](http://msdn.microsoft.com/library/mt694156.aspx)

- [Použití OLTP v paměti v aplikaci existující Azure SQL](sql-database-in-memory-oltp-migration.md)

- [Monitorování OLTP v paměti úložiště](sql-database-in-memory-oltp-monitoring.md) pro OLTP v paměti


## <a name="additional-resources"></a>Další zdroje informací:

#### <a name="deeper-information"></a>Podrobnější informace.

- [Zjistěte, jak kvora zdvojnásobí klíče databáze zatížení při snížení DTU 70 % s OLTP v paměti v databázi SQL](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [OLTP v paměti v příspěvku na blogu databáze Azure SQL](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

- [Další informace o OLTP v paměti](http://msdn.microsoft.com/library/dn133186.aspx)

- [Další informace o indexy columnstore](https://msdn.microsoft.com/library/gg492088.aspx)

- [Další informace o provozní analýzu v reálném čase](http://msdn.microsoft.com/library/dn817827.aspx)

- V tématu [vzory běžné úlohy a posouzení migrace](http://msdn.microsoft.com/library/dn673538.aspx) (které popisuje vzory úlohy, kde OLTP v paměti běžně nabízí výrazné zvýšení výkonu)

#### <a name="application-design"></a>Návrh aplikace

- [Paměť OLTP (v paměti optimalizace)](http://msdn.microsoft.com/library/dn133186.aspx)

- [Použití OLTP v paměti v aplikaci existující Azure SQL](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>Nástroje

- [Azure Portal](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

- [SQL Server Data Tools (SSDT)](http://msdn.microsoft.com/library/mt204009.aspx)
