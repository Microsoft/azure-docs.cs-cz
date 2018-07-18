---
title: Azure SQL Database začleňování paměťových technologií | Dokumentace Microsoftu
description: Azure SQL Database začleňování paměťových technologií výrazně zlepšit výkon transakční a analytické úlohy.
services: sql-database
author: jodebrui
manager: craigg
ms.service: sql-database
ms.custom: develop databases
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: jodebrui
ms.openlocfilehash: f4e056e9f8461085df6ae447672e2c9ec91aa3ca
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39092399"
---
# <a name="optimize-performance-by-using-in-memory-technologies-in-sql-database"></a>Optimalizace výkonu pomocí v začleňování paměťových technologií ve službě SQL Database

S použitím technologií v paměti ve službě Azure SQL Database, můžete dosáhnout zlepšení výkonu pomocí různých úloh: transakční (online zpracování transakcí (OLTP)), analýza (online analytického zpracování (OLAP)) a smíšené (hybridní transakce / analýzy zpracování (HTAP)). Z důvodu efektivnější dotazu a zpracování transakcí v začleňování paměťových technologií také pomoci snížit náklady. Obvykle není nutné upgradovat cenovou úroveň databáze, kterou chcete dosáhnout zvýšení výkonu. V některých případech dokonce je možné snížit cenovou úroveň a současně stále se zobrazuje vylepšení výkonu díky paměťovým technologiím.

Tady jsou dva příklady jak pomohl OLTP v paměti k výraznému zlepšení výkonu:

- S použitím OLTP v paměti [kvora podniková řešení se podařilo dvakrát svoje úlohy při současném zvyšování Dtu o 70 %](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database).
    - DTU znamená *jednotky transakcí databáze*, a obsahuje měření využití prostředků.
- Toto video ukazuje přináší značné vylepšení v spotřeba prostředků se ukázky pracovního vytížení: [OLTP v paměti v Azure SQL Database videa](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB).
    - Další informace naleznete v příspěvku blogu: [OLTP v paměti v Azure SQL Database Blogový příspěvek](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

V začleňování paměťových technologií jsou k dispozici ve všech databázích na úrovni Premium, včetně databází v elastických fondech Premium.

Následující video vysvětluje potenciální zvýšení výkonu se v začleňování paměťových technologií ve službě Azure SQL Database. Mějte na paměti, zvýšení výkonu, který se zobrazí vždy závislá na mnoha faktorech, včetně povaze úloh a data, vzory přístupu databáze a tak dále.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>
>

Azure SQL Database má následující v začleňování paměťových technologií:

- *OLTP v paměti* transakce se zvyšuje a snižuje latenci a zpracování transakcí. Scénáře využívající OLTP v paměti jsou: zpracování například obchodní a hry, příjem dat ze zařízení IoT, ukládání do mezipaměti, načtení dat a dočasné tabulky a scénáře proměnné tabulky nebo událostí vysokou propustnost transakcí.
- *Clusterované indexy columnstore* snížit nároky na úložiště (až 10 x) a zlepšit výkon pro dotazy analýz a generování sestav. Můžete ji pomocí tabulky faktů v datových tržištích přizpůsobit další data v databázi a zlepšit výkon. Také vám pomůže ho s využitím historických dat v provozní databázi archivovat a mít možnost provádět dotazy až 10krát další data.
- *Neclusterovaných indexů columnstore* pro HTAP umožňují v reálném čase přehledy o svoji obchodní pozici prostřednictvím dotazování provozní databáze přímo, bez nutnosti provádět náročné extrakce, transformace a načítání (ETL) proces a počkejte datový sklad, který se má naplnit. Neclusterovaných indexů columnstore povolit velmi rychlé spouštění analytických dotazů v databázi s online zpracováním transakcí a snižuje nutnost vliv na provozní úlohy.
- Také můžete mít kombinaci paměťově optimalizované tabulky s indexem columnstore. Tato kombinace umožňuje provádět zpracování velmi rychlé zpracování transakcí a *souběžně* velmi rychle spouštění analytických dotazů na stejná data.

Indexy columnstore a OLTP v paměti byly součástí produktu SQL Server 2012 a 2014, v uvedeném pořadí. Azure SQL Database a SQL Server sdílet stejnou implementaci technologií v paměti. Od této chvíle, nové možnosti pro tyto technologie jsou všeobecně dostupné ve službě Azure SQL Database nejprve, před jejich uvedením v systému SQL Server.

Tento článek popisuje aspekty OLTP v paměti a columnstore indexy, které jsou specifické pro Azure SQL Database a také obsahuje ukázky:
- Zobrazí se vám dopad těchto technologií na omezení velikosti úložiště a data.
- Uvidíte jak spravovat přesunu databází, které používají tyto technologie mezi různé cenové úrovně.
- Uvidíte dvě ukázky, které ilustrují použití OLTP v paměti, stejně jako indexy columnstore Azure SQL Database.

Prohlédněte si následující zdroje informací pro další informace.

Podrobné informace o technologiích:

- [Přehled OLTP v paměti a scénáře použití](https://msdn.microsoft.com/library/mt774593.aspx) (včetně odkazů na Zákaznické případové studie a informace, abyste mohli začít)
- [Dokumentace pro OLTP v paměti](http://msdn.microsoft.com/library/dn133186.aspx)
- [Průvodce indexy Columnstore](https://msdn.microsoft.com/library/gg492088.aspx)
- Hybridní transakční a analytické zpracování (HTAP), označované také jako [provozní analýzy v reálném čase](https://msdn.microsoft.com/library/dn817827.aspx)

Rychlý úvod do na OLTP v paměti: [1 rychlý Start: technologie OLTP v paměti pro rychlejší výkon T-SQL](http://msdn.microsoft.com/library/mt694156.aspx) (jiného článku vám pomůžou začít)

Podrobná videa o technologiích:

- [OLTP v paměti ve službě Azure SQL Database](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (která obsahuje ukázky přinese zlepšení výkonu a kroky pro reprodukci tyto výsledky sami)
- [Videa OLTP v paměti: Co je a v případě/jak ho použít](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/10/03/in-memory-oltp-video-what-it-is-and-whenhow-to-use-it/)
- [Columnstore Index: Analýzu v paměti videa z Ignite 2016](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/10/04/columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016/)

## <a name="storage-and-data-size"></a>Velikost úložiště a data

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>Limit velikosti a skladování dat pro OLTP v paměti

OLTP v paměti obsahuje paměťově optimalizované tabulky, které se používají k ukládání dat uživatelů. Tyto tabulky jsou nutné přizpůsobit v paměti. Vzhledem k tomu, že budete spravovat paměti přímo ve službě SQL Database, máme koncept kvótu pro data uživatelů. Tento nápad se označuje jako *úložiště OLTP v paměti*.

Každý podporovaný samostatnou databázi cenová úroveň a každý elastický fond cenová úroveň zahrnuje množství úložiště OLTP v paměti. V tématu [omezení prostředků založený na DTU – izolované databáze](sql-database-dtu-resource-limits-single-databases.md), [omezení prostředků založený na DTU - elastické fondy](sql-database-dtu-resource-limits-elastic-pools.md),[omezení prostředků na základě virtuálních jader – izolované databáze](sql-database-vcore-resource-limits-single-databases.md) a [omezení prostředků založený na virtuálních jádrech - elastických fondů](sql-database-vcore-resource-limits-elastic-pools.md).

Následující položky se počítají vaše kapacita úložiště OLTP v paměti:

- Aktivní uživatel řádky dat v paměťově optimalizovaných tabulkách a proměnné tabulky. Všimněte si, že stará verze řádků se nepočítají směrem k zakončení.
- Indexy na paměťově optimalizovaných tabulkách.
- Provozní režie operace ALTER TABLE.

Pokud dosáhnete zakončení, obdržíte chybu limit kvóty a nejste již moci vložit nebo aktualizovat data. Pro zmírnění této chyby, odstraňte data nebo zvýšení cenové úrovně služby databáze nebo fondu.

Podrobnosti o sledování využití úložiště OLTP v paměti a konfiguraci výstrah, až se dostanete téměř zakončení najdete v tématu [monitorování v paměti úložiště](sql-database-in-memory-oltp-monitoring.md).

#### <a name="about-elastic-pools"></a>Informace o elastických fondů

S elastickými fondy úložiště OLTP v paměti se sdílí napříč všemi databázemi ve fondu. Využití v jedné databázi proto může potenciálně ovlivnit ostatní databáze. Jsou dva způsoby zmírnění rizik pro toto:

- Konfigurace `Max-eDTU` nebo `MaxvCore` pro databáze, které je nižší než počet eDTU nebo – vCore pro fond jako celek. Toto maximum caps využití úložiště OLTP v paměti, všechny databáze ve fondu, aby velikost, která odpovídá počtu eDTU.
- Konfigurace `Min-eDTU` nebo `MinvCore` , který je větší než 0. Tohle minimum zaručuje, že každá databáze ve fondu má velikost dostupného úložiště OLTP v paměti, která odpovídá nakonfigurované `Min-eDTU` nebo `vCore`.

### <a name="data-size-and-storage-for-columnstore-indexes"></a>Velikost dat a úložiště v případě indexů columnstore

Indexy Columnstore není nutné přizpůsobit v paměti. Proto pouze limit velikosti indexy, které je maximální celkovou velikost databáze, která je popsána v [nákupní model založený na DTU](sql-database-service-tiers-dtu.md) a [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md) článků.

Při použití Clusterované indexy columnstore úložiště se sloupcovou strukturou komprese se používá pro základní tabulky úložiště. Tato komprese může výrazně snížit nároky na úložiště dat uživatele, což znamená, že může obsahovat další data v databázi. A komprese můžete dál zvýšit požadavkem [úložiště se sloupcovou strukturou archivace komprese](https://msdn.microsoft.com/library/cc280449.aspx#Using Columnstore and Columnstore Archive Compression). Komprese, které můžete dosáhnout závisí na charakteru dat, ale není, 10krát komprese.

Například pokud máte databáze s maximální velikostí 1 terabajtů (TB) a dosáhnout 10krát komprese pomocí indexů columnstore, lze zobrazit celkem 10 TB dat uživatele v databázi.

Při použití neclusterovaných indexů columnstore v základní tabulce zůstanou uložena v tradiční rowstore formátu. Proto nejsou tak velká jako v clusterovaných indexech columnstore úspory úložiště. Pokud nahrazujete počet tradiční neclusterovaných indexů s indexem columnstore jeden, uvidíte stále celkové úspory v nároky na úložiště pro tabulku.

## <a name="moving-databases-that-use-in-memory-technologies-between-pricing-tiers"></a>Přesouvání databází, které používají v začleňování paměťových technologií mezi cenové úrovně

Nejsou nikdy jakékoli nekompatibility nebo jiné problémy při upgradu na vyšší cenovou úroveň, jako například úroveň ze Standard na Premium. Dostupné funkce a prostředky pouze zvýšit.

Ale Downgrade cenovou úroveň může mít negativní vliv na vaši databázi. Pokud spustíte downgrade z úrovně Premium na Standard nebo Basic, když databáze obsahuje objekty OLTP v paměti je zejména zjevný dopad. Paměťově optimalizované tabulky nejsou k dispozici po downgrade (i v případě, že zůstávají viditelné). Platí stejné aspekty při současném snižování cenové úrovně služby elastického fondu, nebo přesunutí databáze s začleňování paměťových technologií do Standard nebo Basic elastického fondu.

### <a name="in-memory-oltp"></a>OLTP v paměti

*Downgrade Basic nebo Standard*: OLTP v paměti se nepodporuje v databáze v úrovni Standard nebo Basic. Kromě toho není možné přesunout databázi, která obsahuje všechny objekty OLTP v paměti na úroveň Standard nebo Basic.

Existuje programový způsob, jak pochopit, jestli podporuje danou databázi OLTP v paměti. Můžete spustit následující dotaz jazyka Transact-SQL:

```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```

Pokud dotaz vrací **1**, OLTP v paměti je podporováno v této databázi.

Než spustíte downgrade databáze Standard nebo Basic, odeberte všechny paměťově optimalizovaných tabulkách a typů tabulek, jakož i všechny nativně Kompilované moduly T-SQL. Následující dotazy Identifikujte všechny objekty, které je třeba odebrat dříve, než databáze může být downgradovat na Standard nebo Basic:

```
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

*Downgrade na nižší úroveň Premium*: Data v paměťově optimalizovaných tabulkách musí vejít do úložiště OLTP v paměti, který je přidružený k cenová úroveň databáze nebo je k dispozici v elastickém fondu. Pokud se pokusíte snížit cenovou úroveň nebo přesunutí databáze do fondu, který nemá dostatek dostupného úložiště OLTP v paměti se operace nezdaří.

### <a name="columnstore-indexes"></a>Indexy Columnstore

*Downgrade na Basic nebo Standard*: indexy Columnstore jsou podporovány pouze na cenové úrovně Premium a na úrovni Standard S3 a výše a ne na úrovni Basic. Pokud spustíte downgrade databáze na úrovni nebo nepodporovaná úroveň, stane nedostupným indexu columnstore. Systém udržuje indexu columnstore, ale nikdy využívá index. Pokud upgradujete později zpět na podporovanou úroveň a úroveň, indexu columnstore je okamžitě připravené k využít znovu.

Pokud máte **Clusterované** columnstore index celé tabulky přestane být k dispozici po downgrade. Proto doporučujeme vyřaďte všechny *Clusterované* indexy columnstore předtím, než spustíte downgrade databáze na úrovni nebo nepodporovaná úroveň.

*Downgrade nižší úrovně podporované nebo úroveň*: Tento přechod na starší verzi proběhne úspěšně, pokud celé databáze vejde maximální velikost databáze pro cíl cenovou úroveň, nebo v rámci dostupného úložiště v elastickém fondu. Neexistuje žádná konkrétní žádný vliv na indexy columnstore.


<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="1-install-the-in-memory-oltp-sample"></a>1. Nainstalovat vzorovou OLTP v paměti

Vytvoříte ukázkové databáze AdventureWorksLT s otázkou několika kliknutí [webu Azure portal](https://portal.azure.com/). Potom kroky v této části popisují, jak můžete rozšířit vaše databáze AdventureWorksLT s objekty OLTP v paměti a ukazují přinese zlepšení výkonu.

Více zjednodušenou, ale atraktivnější výkonu ukázku pro OLTP v paměti naleznete v tématu:

- Verze: [v – paměť – oltp-demo-v1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- Zdrojový kód: [in-memory-oltp-demo-source-code](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/in-memory/ticket-reservations)

#### <a name="installation-steps"></a>Postup instalace

1. V [webu Azure portal](https://portal.azure.com/), vytvoření databáze úrovně Premium nebo pro důležité obchodní informace na serveru. Nastavte **zdroj** ukázkové databáze AdventureWorksLT. Podrobné pokyny najdete v tématu [vytvořit svoji první databázi Azure SQL](sql-database-get-started-portal.md).

2. Připojení k databázi pomocí aplikace SQL Server Management Studio [(SSMS.exe)](http://msdn.microsoft.com/library/mt238290.aspx).

3. Kopírovat [OLTP v paměti příkazů jazyka Transact-SQL skriptů](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_oltp_sample.sql) do schránky. Skript T-SQL vytvoří objekty nezbytné v paměti v ukázkové databáze AdventureWorksLT, který jste vytvořili v kroku 1.

4. Vložte skript T-SQL do aplikace SSMS a pak spusťte tento skript. `MEMORY_OPTIMIZED = ON` Příkazy CREATE TABLE klauzule jsou velmi důležitá. Příklad:


```
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### <a name="error-40536"></a>Chyba 40536


Pokud se zobrazí chyba 40536 při spuštění skriptu T-SQL, spusťte následující skript T-SQL k ověření, jestli podporuje databázi v paměti:


```
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


Výsledkem **0** znamená, že tento In-Memory není podporována, a **1** znamená, že se podporuje. A Diagnostikujte problém, ujistěte se, že databáze je na úrovně Premium.


#### <a name="about-the-created-memory-optimized-items"></a>O položek vytvořený optimalizovaných pro paměť

**Tabulky**: Ukázka obsahuje paměťově optimalizované tabulky:

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


Paměťově optimalizované tabulky prostřednictvím si můžete prohlédnout **Průzkumník objektů systému** v aplikaci SSMS. Klikněte pravým tlačítkem na **tabulky** > **filtr** > **nastavení filtru** > **je paměťově optimalizovaná**. Hodnota se rovná 1.


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

### <a name="run-the-sample-oltp-workload"></a>Spuštění úlohy OLTP vzorku

Jediným rozdílem mezi těmito dvěma následující *uložených procedur komponentami TableAdapter* je, že první postup používá verze paměťově optimalizovaných tabulek, zatímco druhý postup používá regulární tabulky na disku:

- SalesLT **.** usp_InsertSalesOrder **_inmem**
- SalesLT **.** usp_InsertSalesOrder **_ondisk**


V této části můžete zjistit, jak použít po ruce **ostress.exe** nástroj k provedení dvě uložené procedury na velmi stresující úrovních. Můžete porovnat, jak dlouho trvá, spustí dvě zátěže na dokončení.


Když spustíte ostress.exe, doporučujeme předání hodnot parametrů pro obě z následujících akcí:

- Spuštění velkého počtu souběžných připojení, pomocí volby - n100.
- Podle mít jednotlivé smyčky připojení stovky časů, pomocí parametru - r500.


Můžete však chtít začněte s mnohem menší hodnoty jako - n10 a - 50 a ujistěte se, že všechno funguje.


### <a name="script-for-ostressexe"></a>Skript pro ostress.exe


V této části zobrazí skript T-SQL, které jsou součástí naší ostress.exe příkazového řádku. Tento skript využívá položky, které byly vytvořeny pomocí skriptu T-SQL, který jste dříve nainstalovali.


Následující skript vloží ukázková prodejní objednávka s pěti řádcích do následující paměťově optimalizované *tabulky*:

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


Chcete-li *_ondisk* verze předchozího skriptu T-SQL pro ostress.exe by nahraďte oba výskyty *_inmem* podřetězec s *_ondisk*. Tyto náhrady odkazuje vliv na názvy tabulek a uložených procedur.


### <a name="install-rml-utilities-and-ostress"></a>Instalace nástroje RML a ostress


V ideálním případě by plánujete spouštět ostress.exe na virtuálním počítači Azure (VM). Měli byste vytvořit [virtuálního počítače Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) ve stejné zeměpisné oblasti Azure ve které se nachází databáze AdventureWorksLT. Ale můžete na svém přenosném počítači spustit ostress.exe místo.


Na virtuálním počítači nebo na cokoli, co můžete hostovat zvolte, nainstalujte nástroje opětovného přehrání Markup Language (RML). Nástroje zahrnují ostress.exe.

Další informace naleznete v tématu:
- Ostress.exe diskuze v [ukázkovou databázi pro OLTP v paměti](http://msdn.microsoft.com/library/mt465764.aspx).
- [Ukázková databáze pro OLTP v paměti](http://msdn.microsoft.com/library/mt465764.aspx).
- [Blogu pro instalaci ostress.exe](http://blogs.msdn.com/b/psssql/archive/2013/10/29/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx).



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(http://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(http://msdn.microsoft.com/library/mt465764.aspx)
-->



### <a name="run-the-inmem-stress-workload-first"></a>Spustit *_inmem* nejprve zátěže úloh


Můžete použít *RML Cmd výzvy* okno ke spuštění našich ostress.exe příkazového řádku. Parametry příkazového řádku přímé ostress na:

- Souběžně 100 připojení (-n100).
- Každé připojení 50 časy spuštění skriptu T-SQL (-50).


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


Spuštění předchozího ostress.exe příkazového řádku:


1. Vynulování obsahu databáze data spuštěním následujícího příkazu v aplikaci SSMS, chcete-li odstranit všechna data, která byla vložena všechny předchozí běhy:

    ``` tsql
    EXECUTE Demo.usp_DemoReset;
    ```

2. Kopírovat text z příkazového řádku předchozí ostress.exe do schránky.

3. Nahradit `<placeholders>` pro parametry -S - U -P -d s správné skutečné hodnoty.

4. V okně RML Cmd spusťte upravený příkazového řádku.


#### <a name="result-is-a-duration"></a>Výsledkem je doba trvání


Po dokončení ostress.exe zapíše doba trvání běhu jako jeho poslední řádek výstupu v okně příkazového řádku RML. Například kratší testovacího běhu trval po asi 1,5 minuty:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### <a name="reset-edit-for-ondisk-then-rerun"></a>Resetovat, upravit pro *_ondisk*, pak znovu spusťte


Až budete mít výsledkem *_inmem* spouštění, proveďte následující kroky pro *_ondisk* spustit:


1. Obnovení databáze spuštěním následujícího příkazu v aplikaci SSMS odstranit všechna data, která byla vložena předchozího spuštění:
```
EXECUTE Demo.usp_DemoReset;
```

2. Upravit ostress.exe příkazového řádku k nahrazení všech *_inmem* s *_ondisk*.

3. Znovu spustit ostress.exe podruhé a zaznamenat výsledek doby trvání.

4. Opět obnovte databázi (pro odstranění zodpovědně velkého objemu testovacích dat, může být).


#### <a name="expected-comparison-results"></a>Srovnání očekávané výsledky

Naše testy v paměti ukázalo tento výkon vylepšit **devětkrát** pro danou zjednodušenou úlohu s ostress běžící na Virtuálním počítači Azure ve stejné oblasti Azure jako databáze.

<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;

## <a name="2-install-the-in-memory-analytics-sample"></a>2. Nainstalujte ukázka analýzy v paměti


V této části porovnejte výsledky vstupně-výstupní operace a statistiky při použití s indexem columnstore a indexu tradiční b stromu.


Pro analýzu v reálném čase na úloh s online zpracováním často je nejvhodnější použít neclusterovaný index columnstore. Podrobnosti najdete v tématu [popsané indexy Columnstore](http://msdn.microsoft.com/library/gg492088.aspx).



### <a name="prepare-the-columnstore-analytics-test"></a>Příprava testovacího analytics columnstore


1. Pomocí webu Azure portal k vytvoření nové databáze AdventureWorksLT z ukázky.
 - Pomocí tohoto přesným názvem.
 - Vyberte všechny úrovně služeb Premium.

2. Kopírovat [sql_in memory_analytics_sample](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_analytics_sample.sql) do schránky.
 - Skript T-SQL vytvoří objekty nezbytné v paměti v ukázkové databáze AdventureWorksLT, který jste vytvořili v kroku 1.
 - Tento skript vytvoří tabulky dimenzí a dvou tabulek faktů. Tabulky faktů jsou vyplněna 3,5 milionu řádků.
 - Skript může trvat 15 minut.

3. Vložte skript T-SQL do aplikace SSMS a pak spusťte tento skript. **COLUMNSTORE** – klíčové slovo v **CREATE INDEX** je velmi důležité, stejně jako v příkazu:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Nastavit úroveň kompatibility 130 AdventureWorksLT:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`

    Úroveň 130 přímo nesouvisí s funkcemi v paměti. Ale úroveň 130 obecně poskytuje vyšší výkon dotazu než 120.


#### <a name="key-tables-and-columnstore-indexes"></a>Klíče tabulky a indexy columnstore


- vlastník databáze. FactResellerSalesXL_CCI je tabulka, která má clusterovaný index columnstore, která obsahuje rozšířené komprese v *data* úroveň.

- vlastník databáze. FactResellerSalesXL_PageCompressed je tabulka, která má ekvivalentní regulární clusterovaný index, který je komprimován pouze na *stránky* úroveň.


#### <a name="key-queries-to-compare-the-columnstore-index"></a>Dotazy klíčů k porovnání columnstore index


Existují [několik typů dotazu T-SQL, které můžete spustit](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/clustered_columnstore_sample_queries.sql) zobrazíte vylepšení výkonu. V kroku 2 ve skriptu T-SQL věnujte pozornost tento pár dotazů. Liší se pouze na jednom řádku:


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


Clusterovaný index columnstore probíhá FactResellerSalesXL\_CCI tabulky.

Následující úryvek skriptu T-SQL vypíše statistiky pro vstup/výstup a čas pro dotaz každou tabulku.


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

V databázi s cenovou úroveň P2 můžete očekávat přibližně devětkrát zvýšení výkonu pro tento dotaz pomocí clusterovaného indexu columnstore v porovnání s tradičním indexu. S P15 můžou očekávat přibližně 57 časy výkonový zisk plynoucí pomocí indexu columnstore.



## <a name="next-steps"></a>Další postup

- [Rychlý Start 1: Technologie OLTP v paměti pro dosažení vyššího výkonu T-SQL](http://msdn.microsoft.com/library/mt694156.aspx)

- [OLTP v paměti pro použití v existující aplikaci Azure SQL](sql-database-in-memory-oltp-migration.md)

- [Úložiště OLTP v paměti monitorování](sql-database-in-memory-oltp-monitoring.md) pro OLTP v paměti


## <a name="additional-resources"></a>Další zdroje informací:

#### <a name="deeper-information"></a>Podrobnější informace.

- [Zjistěte, jak kvorum zdvojnásobuje klíčové databázové úlohy při současném snižování DTU o 70 % s OLTP v paměti ve službě SQL Database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [OLTP v paměti v příspěvku na blogu Azure SQL Database](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

- [Další informace o OLTP v paměti](http://msdn.microsoft.com/library/dn133186.aspx)

- [Seznamte se s indexy columnstore](https://msdn.microsoft.com/library/gg492088.aspx)

- [Další informace o provozní analýzy v reálném čase](http://msdn.microsoft.com/library/dn817827.aspx)

- Zobrazit [běžné vzory úlohy a důležité informace o migraci](http://msdn.microsoft.com/library/dn673538.aspx) (které popisuje vzory zatížení kde OLTP v paměti obvykle poskytují významného zvýšení výkonu)

#### <a name="application-design"></a>Návrh aplikace

- [Paměti OLTP (Optimalizace v paměti)](http://msdn.microsoft.com/library/dn133186.aspx)

- [OLTP v paměti pro použití v existující aplikaci Azure SQL](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>Nástroje

- [Azure Portal](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

- [SQL Server Data Tools (SSDT)](http://msdn.microsoft.com/library/mt204009.aspx)
