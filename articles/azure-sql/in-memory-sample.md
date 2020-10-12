---
title: Ukázka In-Memory
description: Vyzkoušejte Azure SQL Database In-Memory technologie pomocí OLTP a columnstore Sample.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: 2829b1c71aebcc97452fc658e6509e4fae42da8c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91616801"
---
# <a name="in-memory-sample"></a>Ukázka In-Memory
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Technologie In-Memory v Azure SQL Database umožňují zvýšit výkon aplikace a potenciálně snížit náklady na vaši databázi. Pomocí In-Memory technologií v Azure SQL Database můžete dosáhnout zlepšení výkonu s různými úlohami.

V tomto článku se zobrazí dvě ukázky, které ilustrují použití In-Memory OLTP a také indexy columnstore v Azure SQL Database.

Další informace naleznete v tématech:

- [Přehled OLTP v paměti a scénáře použití](/sql/relational-databases/in-memory-oltp/overview-and-usage-scenarios) (zahrnuje odkazy na případové studie zákazníků a informace o tom, jak začít)
- [Dokumentace pro In-Memory OLTP](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)
- [Průvodce indexy columnstore](/sql/relational-databases/indexes/columnstore-indexes-overview)
- Hybridní transakční/analytické zpracování (HTAP), označované také jako [provozní analýza v reálném čase](/sql/relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics)

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="1-install-the-in-memory-oltp-sample"></a>1. instalace ukázky In-Memory OLTP

Ukázkovou databázi AdventureWorksLT můžete vytvořit několika kliknutími v [Azure Portal](https://portal.azure.com/). Kroky v této části vysvětlují, jak můžete rozšířit databázi AdventureWorksLT pomocí In-Memory objektů OLTP a předvést výkonnostní výhody.

Další zjednodušený, ale vizuální opravení výkonu pro In-Memory OLTP, najdete v tématech:

- Verze: [In-Memory-OLTP-demo-v 1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- Zdrojový kód: [In-Memory-OLTP-demo-Source-Code](https://github.com/microsoft/sql-server-samples/tree/master/samples/features/in-memory-database)

### <a name="installation-steps"></a>Kroky instalace

1. V [Azure Portal](https://portal.azure.com/)vytvořte databázi Premium nebo pro důležité obchodní informace na serveru. Nastavte **zdroj** na ukázkovou databázi AdventureWorksLT. Podrobné pokyny najdete v tématu [Vytvoření první databáze v Azure SQL Database](database/single-database-create-quickstart.md).

2. Připojte se k databázi pomocí SQL Server Management Studio [(SSMS.exe)](https://msdn.microsoft.com/library/mt238290.aspx).

3. Zkopírujte [skript Transact-SQL OLTP v paměti](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/sql_in-memory_oltp_sample.sql) do schránky. Skript T-SQL vytvoří potřebné objekty In-Memory v ukázkové databázi AdventureWorksLT, kterou jste vytvořili v kroku 1.

4. Vložte skript T-SQL do SSMS a potom spusťte skript. `MEMORY_OPTIMIZED = ON`Klauzule CREATE TABLE příkazy jsou klíčové. Například:

```sql
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```

### <a name="error-40536"></a>Chyba 40536

Pokud při spuštění skriptu T-SQL dojde k chybě 40536, spusťte následující skript T-SQL, který ověří, jestli databáze podporuje paměť v paměti:

```sql
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```

Výsledkem **0** znamená, že In-Memory nejsou podporovány a **1** znamená, že je podporována. Pokud chcete problém diagnostikovat, ujistěte se, že je databáze na úrovni služby Premium.

### <a name="about-the-created-memory-optimized-items"></a>O vytvořených paměťově optimalizovaných položkách

**Tabulky**: ukázka obsahuje následující paměťově optimalizované tabulky:

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo. DemoSalesOrderHeaderSeed
- Demo. DemoSalesOrderDetailSeed

Paměťově optimalizované tabulky můžete kontrolovat pomocí **Průzkumník objektů** v SSMS. V případě, že kliknete pravým tlačítkem myši na **tabulka**  >  **Filter**  >  ,**nastavení filtru**filtru  >  **je optimalizován** Hodnota se rovná 1.

Nebo můžete zadat dotaz na zobrazení katalogu, například:

```sql
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```

**Nativně kompilovaná uložená procedura**: SalesLT.usp_InsertSalesOrder_inmem můžete zkontrolovat pomocí dotazu zobrazení katalogu:

```sql
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```

&nbsp;

### <a name="run-the-sample-oltp-workload"></a>Spuštění ukázkové úlohy OLTP

Jediným rozdílem mezi těmito dvěma *uloženými postupy* je, že první postup používá paměťově optimalizované verze tabulek, zatímco druhý postup používá běžné tabulky na disku:

- Tabulky SalesLT **.** usp_InsertSalesOrder **_inmem**
- Tabulky SalesLT **.** usp_InsertSalesOrder **_ondisk**

V této části zjistíte, jak pomocí užitečného nástroje **ostress.exe** spustit dva uložené procedury na úrovních stresující. Můžete porovnat, jak dlouho trvá dokončení dvou zátěžových testů.

Když spustíte ostress.exe, doporučujeme předat hodnoty parametrů navržené pro obě následující položky:

- Spusťte velký počet souběžných připojení pomocí-N100.
- Vykonání všech cyklů připojení se stovky časů pomocí-R500.

Můžete ale chtít začít s mnohem menšími hodnotami jako-N10 a-R50, abyste zajistili, že všechno funguje.

### <a name="script-for-ostressexe"></a>Skript pro ostress.exe

V této části se zobrazuje skript T-SQL, který je vložený do našeho ostress.exe příkazového řádku. Skript používá položky, které byly vytvořeny pomocí skriptu T-SQL, který jste nainstalovali dříve.

Následující skript vloží ukázkovou prodejní objednávku s pěti položkami řádků do následujících paměťově optimalizovaných *tabulek*:

- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem

```sql
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

Chcete-li nastavit *_ondisk* verzi předchozího skriptu T-SQL pro ostress.exe, nahraďte oba výskyty *_inmem* dílčího řetězce *_ondisk*. Tyto náhrady mají vliv na názvy tabulek a uložených procedur.

#### <a name="install-rml-utilities-and-ostress"></a>Instalace RML nástrojů a `ostress`

V ideálním případě byste měli naplánovat spuštění ostress.exe na virtuálním počítači Azure (VM). Vytvořili jste [virtuální počítač Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) ve stejné geografické oblasti Azure, kde se nachází vaše databáze AdventureWorksLT. Ale můžete místo toho spustit ostress.exe na svém přenosném počítači.

Na virtuálním počítači nebo na jakémkoli zvoleném hostiteli nainstalujte nástroje RML (replay Markup Language). Mezi tyto nástroje patří ostress.exe.

Další informace naleznete v tématech:

- ostress.exe diskuzi v [ukázkové databázi pro In-Memory OLTP](https://msdn.microsoft.com/library/mt465764.aspx).
- [Ukázková databáze pro In-Memory OLTP](https://msdn.microsoft.com/library/mt465764.aspx)
- [Blog pro instalaci ostress.exe](https://techcommunity.microsoft.com/t5/sql-server-support/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql/ba-p/317910).

<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(https://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(https://msdn.microsoft.com/library/mt465764.aspx)
-->

#### <a name="run-the-_inmem-stress-workload-first"></a>Nejdřív spusťte úlohu *_inmem* zátěže.

Pomocí okna příkazového řádku *RML cmd* můžete spustit náš ostress.exe příkazový řádek. Parametry příkazového řádku přímo `ostress` na:

- Spouštějte připojení 100 souběžně (-N100).
- U každého připojení spusťte skript T-SQL 50 časy (-R50).

```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```

Spuštění předchozího příkazového řádku ostress.exe:

1. Obnovte obsah databázových dat spuštěním následujícího příkazu v SSMS, abyste odstranili všechna data, která byla vložena v předchozích spuštěních:

    ``` tsql
    EXECUTE Demo.usp_DemoReset;
    ```

2. Zkopírujte text předchozího ostress.exe příkazového řádku do schránky.

3. Nahraďte `<placeholders>` parametrem-S-U-P-d správnými skutečnými hodnotami.

4. Spusťte upravený příkazový řádek v okně RML cmd.

#### <a name="result-is-a-duration"></a>Výsledkem je doba trvání

Po `ostress.exe` dokončení zapíše dobu běhu jako poslední řádek výstupu v okně RML cmd. Například kratší testovací běh uplynul přibližně 1,5 minut:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`

#### <a name="reset-edit-for-_ondisk-then-rerun"></a>Resetovat, upravit pro *_ondisk*a pak znovu spustit

Po dokončení spuštění *_inmem* proveďte následující kroky pro *_ondisk* spuštění:

1. Obnovte databázi spuštěním následujícího příkazu v SSMS a odstraňte všechna data, která byla vložena v předchozím běhu:

   ```sql
   EXECUTE Demo.usp_DemoReset;
   ```

2. Úpravou ostress.exe příkazového řádku nahraďte *_inmem* všechny _inmem *_ondisk*.

3. Spusťte znovu ostress.exe pro druhý čas a zachyťte výsledek trvání.

4. Znovu obnovte databázi (pro zodpovědnou odstranění toho, co může být velké množství testovacích dat).

#### <a name="expected-comparison-results"></a>Očekávané výsledky porovnání

Naše In-Memory testy ukázaly, že výkon v rámci této zjednodušený úlohy byl během **devíti časů** vylepšený a `ostress` běží na virtuálním počítači Azure ve stejné oblasti Azure jako databáze.

<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;

## <a name="2-install-the-in-memory-analytics-sample"></a>2. Nainstalujte ukázku In-Memory Analytics.

V této části porovnáte výsledky IO a STATISTICS, když používáte index columnstore oproti tradičnímu indexu b-Tree.

Pro analýzy v reálném čase pro OLTP úlohy je často vhodné použít neclusterovaný index columnstore. Podrobnosti najdete v tématu [popsané indexy columnstore](https://msdn.microsoft.com/library/gg492088.aspx).

### <a name="prepare-the-columnstore-analytics-test"></a>Příprava analytického testu columnstore

1. Použijte Azure Portal k vytvoření nové databáze AdventureWorksLT z ukázky.
   - Použijte tento přesný název.
   - Vyberte libovolnou úroveň služby Premium.

2. Zkopírujte [sql_in-memory_analytics_sample](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/sql_in-memory_analytics_sample.sql) do schránky.
   - Skript T-SQL vytvoří potřebné objekty In-Memory v ukázkové databázi AdventureWorksLT, kterou jste vytvořili v kroku 1.
   - Skript vytvoří tabulku dimenzí a dvě tabulky faktů. Do tabulek faktů se naplní 3 500 000 řádků.
   - Dokončení skriptu může trvat 15 minut.

3. Vložte skript T-SQL do SSMS a potom spusťte skript. Klíčové slovo **COLUMNSTORE** v příkazu **Create index** je klíčové, jako v:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Nastavte AdventureWorksLT na úroveň kompatibility 130:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`

    Úroveň 130 přímo nesouvisí s funkcemi In-Memory. Úroveň 130 ale obecně poskytuje rychlejší výkon dotazů než 120.

#### <a name="key-tables-and-columnstore-indexes"></a>Klíčové tabulky a indexy columnstore

- dbo. FactResellerSalesXL_CCI je tabulka s clusterovaným indexem columnstore, která má rozšířenou kompresi na úrovni *dat* .

- dbo. FactResellerSalesXL_PageCompressed je tabulka, která má ekvivalentní pravidelný clusterovaný index, který je komprimován pouze na úrovni *stránky* .

#### <a name="key-queries-to-compare-the-columnstore-index"></a>Klíčové dotazy pro porovnání indexu columnstore

Existuje [několik typů dotazů T-SQL, které můžete spustit,](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/clustered_columnstore_sample_queries.sql) abyste viděli zvýšení výkonu. V kroku 2 ve skriptu T-SQL věnujte pozornost tomuto páru dotazů. Liší se pouze na jednom řádku:

- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`

Clusterovaný index columnstore se nachází v tabulce FactResellerSalesXL \_ Ski.

Následující výpis skriptu T-SQL vytiskne statistiku pro vstupně-výstupní operace a čas pro dotaz každé tabulky.

```sql
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

V databázi s cenovou úrovní P2 můžete očekávat přibližně devět časů zvýšení výkonu tohoto dotazu pomocí clusterovaného indexu columnstore v porovnání s tradičním indexem. Pomocí P15 můžete očekávat zvýšení výkonu pomocí indexu columnstore přibližně 57 času.

## <a name="next-steps"></a>Další kroky

- [Rychlý Start 1: In-Memory OLTP technologie pro rychlejší výkon T-SQL](https://msdn.microsoft.com/library/mt694156.aspx)

- [Použití In-Memory OLTP v existující aplikaci Azure SQL](in-memory-oltp-configure.md)

- [Monitorování In-Memory OLTP úložiště](in-memory-oltp-monitor-space.md) pro In-Memory OLTP

## <a name="additional-resources"></a>Další zdroje

### <a name="deeper-information"></a>Hlubší informace

- [Přečtěte si, jak kvorum podvoje úlohy klíčové databáze při snižování DTU o 70% s In-Memory OLTP v Azure SQL Database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [OLTP v paměti v příspěvku Azure SQL Database na blogu](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

- [Další informace o In-Memory OLTP](https://msdn.microsoft.com/library/dn133186.aspx)

- [Další informace o indexech columnstore](https://msdn.microsoft.com/library/gg492088.aspx)

- [Další informace o provozní analýze v reálném čase](https://msdn.microsoft.com/library/dn817827.aspx)

- Podívejte [se na běžné vzory úloh a důležité informace k migraci](https://msdn.microsoft.com/library/dn673538.aspx) (které popisují vzory úloh, kde In-Memory OLTP běžně poskytuje výrazné zvýšení výkonu).

#### <a name="application-design"></a>Návrh aplikací

- [OLTP v paměti (optimalizace v paměti)](https://msdn.microsoft.com/library/dn133186.aspx)

- [Použití In-Memory OLTP v existující aplikaci Azure SQL](in-memory-oltp-configure.md)

#### <a name="tools"></a>Nástroje

- [Azure Portal](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

- [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
