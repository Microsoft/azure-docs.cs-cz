---
title: Ukázka v paměti
description: Vyzkoušejte technologie Azure SQL Database In-Memory s ukázkou OLTP a columnstore.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: b707d67c88eb550d397134b2294c1c5b0e1f7f7d
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80528218"
---
# <a name="in-memory-sample"></a>Ukázka v paměti

Technologie v paměti v Azure SQL Database umožňují zlepšit výkon vaší aplikace a potenciálně snížit náklady na databázi. Pomocí technologií v paměti v Azure SQL Database, můžete dosáhnout zlepšení výkonu s různými úlohami.

V tomto článku uvidíte dvě ukázky, které ilustrují použití OLTP v paměti, stejně jako columnstore indexy v Azure SQL Database.

Další informace naleznete v tématu:
- [Scénáře přehledu oltp v paměti a použití](https://msdn.microsoft.com/library/mt774593.aspx) (obsahuje odkazy na případové studie zákazníka a informace, které můžete začít)
- [Dokumentace pro OLTP v paměti](https://msdn.microsoft.com/library/dn133186.aspx)
- [Průvodce indexy columnstore](https://msdn.microsoft.com/library/gg492088.aspx)
- Hybridní transakční/analytické zpracování (HTAP), známé také jako [provozní analýza v reálném čase](https://msdn.microsoft.com/library/dn817827.aspx)

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="1-install-the-in-memory-oltp-sample"></a>1. Nainstalujte ukázku OLTP v paměti

Ukázkovou databázi AdventureWorksLT můžete vytvořit několika kliknutími na [webu Azure Portal](https://portal.azure.com/). Kroky v této části pak vysvětlují, jak můžete obohatit databázi AdventureWorksLT o objekty OLTP v paměti a prokázat výhody výkonu.

Pro více zjednodušující, ale více vizuálně přitažlivý výkon demo pro In-Memory OLTP, viz:

- Vydání: [in-memory-oltp-demo-v1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- Zdrojový kód: [in-memory-oltp-demo-source-code](https://github.com/microsoft/sql-server-samples/tree/master/samples/features/in-memory-database)

#### <a name="installation-steps"></a>Kroky instalace

1. Na [webu Azure Portal](https://portal.azure.com/)vytvořte na serveru databázi Premium nebo Business Critical. Nastavte **zdroj** na ukázkovou databázi AdventureWorksLT. Podrobné pokyny najdete v [tématu Vytvoření první databáze Azure SQL](sql-database-single-database-get-started.md).

2. Připojte se k databázi pomocí aplikace SQL Server Management Studio [(SSMS.exe).](https://msdn.microsoft.com/library/mt238290.aspx)

3. Zkopírujte [skript OLTP Transact-SQL v paměti](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/sql_in-memory_oltp_sample.sql) do schránky. Skript T-SQL vytvoří potřebné objekty v paměti v ukázkové databázi AdventureWorksLT, které jste vytvořili v kroku 1.

4. Vložte skript T-SQL do SSMS a pak skript spusťte. Klauzule `MEMORY_OPTIMIZED = ON` CREATE TABLE příkazy jsou rozhodující. Například:


```sql
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### <a name="error-40536"></a>Chyba 40536


Pokud se při spuštění skriptu T-SQL zobrazí chyba 40536, ověřte, zda databáze podporuje in-memory:


```sql
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


Výsledek **0** znamená, že in-memory není podporována a **1** znamená, že je podporována. Chcete-li diagnostikovat problém, ujistěte se, že databáze je na úrovni služby Premium.


#### <a name="about-the-created-memory-optimized-items"></a>O vytvořených položkách optimalizovaných pro paměť

**Tabulky**: Ukázka obsahuje následující tabulky optimalizované pro paměť:

- ProdejLT.Product_inmem
- ProdejLT.SalesOrderHeader_inmem
- ProdejLT.SalesOrderDetail_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


Tabulky optimalizované pro paměť můžete zkontrolovat prostřednictvím **Průzkumníka objektů** v SSMS. Klepněte pravým **tlačítkem** > myši na**nastavení** > **filtru filtru** > tabulky**je optimalizováno pro paměť**. Hodnota se rovná 1.


Nebo můžete zadat dotaz na zobrazení katalogu, například:


```sql
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```


**Nativně zkompilované uložené procedury**: Můžete zkontrolovat SalesLT.usp_InsertSalesOrder_inmem prostřednictvím dotazu zobrazení katalogu:


```sql
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```


&nbsp;

### <a name="run-the-sample-oltp-workload"></a>Spuštění ukázkového pracovního vytížení OLTP

Jediný rozdíl mezi následující dvě *uložené procedury* je, že první postup používá verze optimalizované pro paměť tabulek, zatímco druhý postup používá běžné tabulky na disku:

- SalesLT **.** usp_InsertSalesOrder **_inmem**
- SalesLT **.** usp_InsertSalesOrder **_ondisk**


V této části uvidíte, jak používat šikovný **nástroj ostress.exe** k provedení dvou uložených procedur na stresující úrovni. Můžete porovnat, jak dlouho trvá, než dva stresběží až do konce.


Při spuštění ostress.exe doporučujeme předat hodnoty parametrů určené pro obě následující:

- Spusťte velký počet souběžných připojení pomocí -n100.
- Mít každé připojení smyčky stokrát, pomocí -r500.


Můžete však začít s mnohem menšími hodnotami, jako jsou -n10 a -r50, abyste zajistili, že vše funguje.


### <a name="script-for-ostressexe"></a>Skript pro ostress.exe


V této části se zobrazí skript T-SQL, který je vložen do našeho příkazového řádku ostress.exe. Skript používá položky, které byly vytvořeny skriptem T-SQL, který jste nainstalovali dříve.


Následující skript vloží ukázkovou prodejní objednávku s pěti řádkovými položkami do následujících *tabulek*optimalizovaných pro paměť :

- ProdejLT.SalesOrderHeader_inmem
- ProdejLT.SalesOrderDetail_inmem


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


Chcete-li vytvořit *_ondisk* verzi předchozího skriptu T-SQL pro ostress.exe, nahradíte oba výskyty *podřetězce _inmem* *_ondisk*. Tyto náhrady ovlivňují názvy tabulek a uložené procedury.


### <a name="install-rml-utilities-and-ostress"></a>Instalace nástrojů RML a`ostress`


V ideálním případě byste plánovali spustit ostress.exe na virtuálním počítači Azure (VM). Virtuální počítač Azure byste [vytvořili](https://azure.microsoft.com/documentation/services/virtual-machines/) ve stejné geografické oblasti Azure, kde se nachází vaše databáze AdventureWorksLT. Ale můžete spustit ostress.exe na vašem notebooku místo.


Na virtuálním počítači nebo na libovolném hostiteli, který si vyberete, nainstalujte nástroje Replay Markup Language (RML). Nástroje zahrnují ostress.exe.

Další informace naleznete v tématu:
- Diskuse ostress.exe v [ukázkové databázi pro oltp v paměti](https://msdn.microsoft.com/library/mt465764.aspx).
- [Ukázková databáze pro protokol OLTP v paměti](https://msdn.microsoft.com/library/mt465764.aspx).
- [Blog pro instalaci ostress.exe](https://blogs.msdn.com/b/psssql/archive/20../../cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx).



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(https://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(https://msdn.microsoft.com/library/mt465764.aspx)
-->



### <a name="run-the-_inmem-stress-workload-first"></a>Nejprve spusťte *_inmem* zátěžovou zátěž


K spuštění příkazového řádku ostress.exe můžete použít okno *RML CmD Prompt.* Parametry příkazového řádku `ostress` přímo na:

- Spusťte 100 připojení současně (-n100).
- Každé připojení spustit Skript T-SQL 50 krát (-r50).


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


Spuštění předchozího příkazového řádku ostress.exe:


1. Obnovte obsah dat databáze spuštěním následujícího příkazu v ssms, chcete-li odstranit všechna data, která byla vložena všechny předchozí spuštění:

    ``` tsql
    EXECUTE Demo.usp_DemoReset;
    ```

2. Zkopírujte text předchozího příkazového řádku ostress.exe do schránky.

3. Nahraďte `<placeholders>` parametry -S -U -P -d správnými reálnými hodnotami.

4. Spusťte upravený příkazový řádek v okně RML Cmd.


#### <a name="result-is-a-duration"></a>Výsledkem je doba trvání


Po `ostress.exe` dokončení zapíše dobu trvání spuštění jako jeho poslední řádek výstupu v okně RML Cmd. Například kratší zkušební provoz trval přibližně 1,5 minuty:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### <a name="reset-edit-for-_ondisk-then-rerun"></a>Resetovat, upravovat *_ondisk*a znovu je spustit


Po spuštění *_inmem* proveďte následující kroky pro *spuštění _ondisk:*


1. Resetujte databázi spuštěním následujícího příkazu v ssms odstranit všechna data, která byla vložena předchozí spuštění:
   ```sql
   EXECUTE Demo.usp_DemoReset;
   ```

2. Upravte příkazový řádek ostress.exe tak, aby nahradil všechny *_inmem* *_ondisk*.

3. Znovu spusťte ostress.exe podruhé a zachyťte výsledek trvání.

4. Znovu obnovit databázi (pro zodpovědně odstranění co může být velké množství testovacích dat).


#### <a name="expected-comparison-results"></a>Očekávané výsledky porovnání

Naše testy v paměti ukázaly, že výkon se zvýšil **devětkrát** pro toto zjednodušené zatížení, s `ostress` spuštěným na virtuálním počítači Azure ve stejné oblasti Azure jako databáze.

<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;

## <a name="2-install-the-in-memory-analytics-sample"></a>2. Nainstalujte ukázku analýzy v paměti


V této části porovnáte výsledky vi a statistiky při použití indexu columnstore versus tradiční index b-strom.


Pro analýzy v reálném čase na zatížení OLTP je často nejlepší použít index columnstore bez clusterů. Podrobnosti naleznete v [tématu Columnstore Indexes Described](https://msdn.microsoft.com/library/gg492088.aspx).



### <a name="prepare-the-columnstore-analytics-test"></a>Příprava testu analýzy columnstore


1. Na webu Azure Portal můžete vytvořit novou databázi AdventureWorksLT z ukázky.
   - Použij přesně ten název.
   - Vyberte si libovolnou úroveň premium.

2. Zkopírujte [memory_analytics_sample sql_in](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/sql_in-memory_analytics_sample.sql) do schránky.
   - Skript T-SQL vytvoří potřebné objekty v paměti v ukázkové databázi AdventureWorksLT, které jste vytvořili v kroku 1.
   - Skript vytvoří tabulku Dimenze a dvě tabulky faktů. Tabulky faktů jsou vyplněny 3,5 milionu řádků.
   - Dokončení skriptu může trvat 15 minut.

3. Vložte skript T-SQL do SSMS a pak skript spusťte. **ColumnSTORE** klíčové slovo v **příkazu VYTVOŘIT INDEX** je rozhodující, jako v:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Nastavte AdventureWorksLT na úroveň kompatibility 130:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`

    Úroveň 130 přímo nesouvisí s funkcemi v paměti. Ale úroveň 130 obecně poskytuje rychlejší výkon dotazu než 120.


#### <a name="key-tables-and-columnstore-indexes"></a>Klíčové tabulky a indexy columnstore


- Dbo. FactResellerSalesXL_CCI je tabulka, která má clusterovaný index columnstore, který má pokročilou kompresi na úrovni *dat.*

- Dbo. FactResellerSalesXL_PageCompressed je tabulka, která má ekvivalentní pravidelný seskupený index, který je komprimován pouze na úrovni *stránky.*


#### <a name="key-queries-to-compare-the-columnstore-index"></a>Klíčové dotazy k porovnání indexu columnstore


Existuje [několik typů dotazů T-SQL, které můžete spustit,](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/clustered_columnstore_sample_queries.sql) abyste viděli zlepšení výkonu. V kroku 2 skriptu T-SQL věnujte pozornost této dvojici dotazů. Liší se pouze na jednom řádku:


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


Index clusterovaného columnstore je v tabulce\_CCI FactResellerSalesXL.

Následující výňatek skriptu T-SQL vytiskne statistiky pro VO a TIME pro dotaz každé tabulky.


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

V databázi s cenovou vrstvou P2 můžete očekávat přibližně devítinásobek zvýšení výkonu pro tento dotaz pomocí clusterovaného indexu columnstore ve srovnání s tradičním indexem. S P15, můžete očekávat, že přibližně 57 krát zvýšení výkonu pomocí columnstore indexu.



## <a name="next-steps"></a>Další kroky

- [Úvodní příručka 1: Technologie OLTP v paměti pro rychlejší výkon T-SQL](https://msdn.microsoft.com/library/mt694156.aspx)

- [Použití OLTP v paměti v existující aplikaci Azure SQL](sql-database-in-memory-oltp-migration.md)

- [Úložiště OLTP monitoru v paměti](sql-database-in-memory-oltp-monitoring.md) pro oltp v paměti


## <a name="additional-resources"></a>Další zdroje

#### <a name="deeper-information"></a>Hlubší informace

- [Zjistěte, jak kvorum zdvojnásobuje zatížení databáze klíčů při současném snížení DTU o 70 % pomocí oltp v paměti v databázi SQL Database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [Oltp v paměti v příspěvku blogu databáze Azure SQL](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

- [Další informace o oltp v paměti](https://msdn.microsoft.com/library/dn133186.aspx)

- [Další informace o indexech columnstore](https://msdn.microsoft.com/library/gg492088.aspx)

- [Další informace o provozních analýzách v reálném čase](https://msdn.microsoft.com/library/dn817827.aspx)

- Viz [Běžné vzory pracovního vytížení a aspekty migrace](https://msdn.microsoft.com/library/dn673538.aspx) (který popisuje vzory pracovního vytížení, kde oltp v paměti běžně poskytuje významné zvýšení výkonu)

#### <a name="application-design"></a>Návrh aplikací

- [Oltp v paměti (optimalizace v paměti)](https://msdn.microsoft.com/library/dn133186.aspx)

- [Použití OLTP v paměti v existující aplikaci Azure SQL](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>nástroje

- [portál Azure](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

- [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
