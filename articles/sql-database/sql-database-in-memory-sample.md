---
title: Azure SQL Database v paměti ukázka | Dokumentace Microsoftu
description: Vyzkoušejte Azure SQL Database v začleňování paměťových technologií s online zpracováním transakcí a columnstore ukázku.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: 9a394c0dff74ec5f926356a3d700c5bbba4c0e4f
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55478283"
---
# <a name="in-memory-sample"></a>Ukázka v paměti

V začleňování paměťových technologií ve službě Azure SQL Database vám umožní zlepšit výkon vaší aplikace a potenciálně snížili množství náklady na vaši databázi. S použitím technologií v paměti ve službě Azure SQL Database, můžete dosáhnout zlepšení výkonu pomocí různých úloh.

V tomto článku uvidíte dvě ukázky, které ilustrují použití OLTP v paměti, stejně jako indexy columnstore Azure SQL Database.

Další informace naleznete v tématu:
- [Přehled OLTP v paměti a scénáře použití](https://msdn.microsoft.com/library/mt774593.aspx) (včetně odkazů na Zákaznické případové studie a informace, abyste mohli začít)
- [Dokumentace pro OLTP v paměti](https://msdn.microsoft.com/library/dn133186.aspx)
- [Průvodce indexy Columnstore](https://msdn.microsoft.com/library/gg492088.aspx)
- Hybridní transakční a analytické zpracování (HTAP), označované také jako [provozní analýzy v reálném čase](https://msdn.microsoft.com/library/dn817827.aspx)

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="1-install-the-in-memory-oltp-sample"></a>1. Nainstalovat vzorovou OLTP v paměti

Vytvoříte ukázkové databáze AdventureWorksLT s otázkou několika kliknutí [webu Azure portal](https://portal.azure.com/). Potom kroky v této části popisují, jak můžete rozšířit vaše databáze AdventureWorksLT s objekty OLTP v paměti a ukazují přinese zlepšení výkonu.

Více zjednodušenou, ale atraktivnější výkonu ukázku pro OLTP v paměti naleznete v tématu:

- Verze: [v – paměť – oltp-demo-v1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- Zdrojový kód: [in-memory-oltp-demo-source-code](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/in-memory/ticket-reservations)

#### <a name="installation-steps"></a>Postup instalace

1. V [webu Azure portal](https://portal.azure.com/), vytvoření databáze úrovně Premium nebo pro důležité obchodní informace na serveru. Nastavte **zdroj** ukázkové databáze AdventureWorksLT. Podrobné pokyny najdete v tématu [vytvořit svoji první databázi Azure SQL](sql-database-get-started-portal.md).

2. Připojení k databázi pomocí aplikace SQL Server Management Studio [(SSMS.exe)](https://msdn.microsoft.com/library/mt238290.aspx).

3. Kopírovat [OLTP v paměti příkazů jazyka Transact-SQL skriptů](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_oltp_sample.sql) do schránky. Skript T-SQL vytvoří objekty nezbytné v paměti v ukázkové databáze AdventureWorksLT, který jste vytvořili v kroku 1.

4. Vložte skript T-SQL do aplikace SSMS a pak spusťte tento skript. `MEMORY_OPTIMIZED = ON` Příkazy CREATE TABLE klauzule jsou velmi důležitá. Příklad:


```sql
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### <a name="error-40536"></a>Chyba 40536


Pokud se zobrazí chyba 40536 při spuštění skriptu T-SQL, spusťte následující skript T-SQL k ověření, jestli podporuje databázi v paměti:


```sql
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


```sql
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```


**Nativně kompilované uložené procedury**: SalesLT.usp_InsertSalesOrder_inmem si můžete prohlédnout pomocí zobrazení katalogu dotazu:


```sql
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


Chcete-li *_ondisk* verze předchozího skriptu T-SQL pro ostress.exe by nahraďte oba výskyty *_inmem* podřetězec s *_ondisk*. Tyto náhrady odkazuje vliv na názvy tabulek a uložených procedur.


### <a name="install-rml-utilities-and-ostress"></a>Nainstalujte nástroje RML a `ostress`


V ideálním případě by plánujete spouštět ostress.exe na virtuálním počítači Azure (VM). Měli byste vytvořit [virtuálního počítače Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) ve stejné zeměpisné oblasti Azure ve které se nachází databáze AdventureWorksLT. Ale můžete na svém přenosném počítači spustit ostress.exe místo.


Na virtuálním počítači nebo na cokoli, co můžete hostovat zvolte, nainstalujte nástroje opětovného přehrání Markup Language (RML). Nástroje zahrnují ostress.exe.

Další informace naleznete v tématu:
- Ostress.exe diskuze v [ukázkovou databázi pro OLTP v paměti](https://msdn.microsoft.com/library/mt465764.aspx).
- [Ukázková databáze pro OLTP v paměti](https://msdn.microsoft.com/library/mt465764.aspx).
- [Blogu pro instalaci ostress.exe](https://blogs.msdn.com/b/psssql/archive/2013/10/29/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx).



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(http://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(http://msdn.microsoft.com/library/mt465764.aspx)
-->



### <a name="run-the-inmem-stress-workload-first"></a>Spustit *_inmem* nejprve zátěže úloh


Můžete použít *RML Cmd výzvy* okno ke spuštění našich ostress.exe příkazového řádku. Parametry příkazového řádku přímo `ostress` na:

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


Když `ostress.exe` dokončí, zapíše doba trvání běhu jako jeho poslední řádek výstupu v okně příkazového řádku RML. Například kratší testovacího běhu trval po asi 1,5 minuty:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### <a name="reset-edit-for-ondisk-then-rerun"></a>Resetovat, upravit pro *_ondisk*, pak znovu spusťte


Až budete mít výsledkem *_inmem* spouštění, proveďte následující kroky pro *_ondisk* spustit:


1. Obnovení databáze spuštěním následujícího příkazu v aplikaci SSMS odstranit všechna data, která byla vložena předchozího spuštění:
```sql
EXECUTE Demo.usp_DemoReset;
```

2. Upravit ostress.exe příkazového řádku k nahrazení všech *_inmem* s *_ondisk*.

3. Znovu spustit ostress.exe podruhé a zaznamenat výsledek doby trvání.

4. Opět obnovte databázi (pro odstranění zodpovědně velkého objemu testovacích dat, může být).


#### <a name="expected-comparison-results"></a>Srovnání očekávané výsledky

Naše testy v paměti ukázalo tento výkon vylepšit **devětkrát** pro danou úlohu zjednodušenou s `ostress` běžící na Virtuálním počítači Azure ve stejné oblasti Azure jako databáze.

<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;

## <a name="2-install-the-in-memory-analytics-sample"></a>2. Nainstalujte ukázka analýzy v paměti


V této části porovnejte výsledky vstupně-výstupní operace a statistiky při použití s indexem columnstore a indexu tradiční b stromu.


Pro analýzu v reálném čase na úloh s online zpracováním často je nejvhodnější použít neclusterovaný index columnstore. Podrobnosti najdete v tématu [popsané indexy Columnstore](https://msdn.microsoft.com/library/gg492088.aspx).



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

V databázi s cenovou úroveň P2 můžete očekávat přibližně devětkrát zvýšení výkonu pro tento dotaz pomocí clusterovaného indexu columnstore v porovnání s tradičním indexu. S P15 můžou očekávat přibližně 57 časy výkonový zisk plynoucí pomocí indexu columnstore.



## <a name="next-steps"></a>Další postup

- [Rychlý start 1: Technologie OLTP v paměti pro dosažení vyššího výkonu T-SQL](https://msdn.microsoft.com/library/mt694156.aspx)

- [OLTP v paměti pro použití v existující aplikaci Azure SQL](sql-database-in-memory-oltp-migration.md)

- [Úložiště OLTP v paměti monitorování](sql-database-in-memory-oltp-monitoring.md) pro OLTP v paměti


## <a name="additional-resources"></a>Další materiály

#### <a name="deeper-information"></a>Podrobnější informace.

- [Zjistěte, jak kvorum zdvojnásobuje klíčové databázové úlohy při současném snižování DTU o 70 % s OLTP v paměti ve službě SQL Database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [OLTP v paměti v příspěvku na blogu Azure SQL Database](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

- [Další informace o OLTP v paměti](https://msdn.microsoft.com/library/dn133186.aspx)

- [Seznamte se s indexy columnstore](https://msdn.microsoft.com/library/gg492088.aspx)

- [Další informace o provozní analýzy v reálném čase](https://msdn.microsoft.com/library/dn817827.aspx)

- Zobrazit [běžné vzory úlohy a důležité informace o migraci](https://msdn.microsoft.com/library/dn673538.aspx) (které popisuje vzory zatížení kde OLTP v paměti obvykle poskytují významného zvýšení výkonu)

#### <a name="application-design"></a>Návrh aplikace

- [Paměti OLTP (Optimalizace v paměti)](https://msdn.microsoft.com/library/dn133186.aspx)

- [OLTP v paměti pro použití v existující aplikaci Azure SQL](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>Nástroje

- [Azure Portal](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

- [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
