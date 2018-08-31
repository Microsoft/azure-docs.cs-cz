---
title: Načtení dat maloobchodního prodeje Contoso do služby Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Načtení dvě tabulky z dat maloobchodního prodeje Contoso do Azure SQL Data Warehouse pomocí PolyBase a T-SQL příkazy.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: cf484d5d7a48117bac659dcfad356238f1e9f8a2
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43307433"
---
# <a name="load-contoso-retail-data-to-azure-sql-data-warehouse"></a>Načtení dat maloobchodního prodeje Contoso do služby Azure SQL Data Warehouse

Načtení dvě tabulky z dat maloobchodního prodeje Contoso do Azure SQL Data Warehouse pomocí PolyBase a T-SQL příkazy. Načíst kompletní data, spusťte příklad [načíst úplný datový sklad maloobchodního prodeje Contoso](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md) z úložiště ukázek Microsoft SQL Server.

V tomto kurzu provedete následující:

1. Konfigurace funkce PolyBase k načtení z úložiště objektů blob v Azure
2. Načíst veřejná data do databáze
3. Po dokončení zatížení, proveďte optimalizace.

## <a name="before-you-begin"></a>Než začnete
Pokud chcete spustit v tomto kurzu, budete potřebovat účet Azure, který již obsahuje databázi SQL Data Warehouse. Pokud to nemáte, přečtěte si téma [vytvořit SQL Data Warehouse][Create a SQL Data Warehouse].

## <a name="1-configure-the-data-source"></a>1. Konfigurace zdroje dat
PolyBase používá externí objekty jazyka T-SQL k určení umístění a atributy externí data. Externí objekt definice jsou uložené ve službě SQL Data Warehouse. Vlastní data uložená externě.

### <a name="11-create-a-credential"></a>1.1. Vytvoření přihlašovacích údajů
**Tento krok přeskočit** Pokud načítáte veřejná data společnosti Contoso. Zabezpečený přístup k datům veřejné nepotřebujete, protože už je přístupné všem uživatelům.

**Nechcete Přeskočit tento krok** Pokud používáte v tomto kurzu jako šablonu pro načítání svoje vlastní data. Pro přístup k datům prostřednictvím přihlašovacích údajů, použijte tento skript k vytvoření přihlašovacích údajů s rozsahem databáze a pak použít při definování umístění zdroje dat.

```sql
-- A: Create a master key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.


CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;


-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure blob storage.
-- LOCATION: Provide Azure storage account name and blob container name.
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'wasbs://<blob_container_name>@<azure_storage_account_name>.blob.core.windows.net',
    CREDENTIAL = AzureStorageCredential
);
```

Přejděte ke kroku 2.

### <a name="12-create-the-external-data-source"></a>1.2. Vytvoření externího zdroje dat.
Použijte tento [CREATE EXTERNAL DATA SOURCE] [ CREATE EXTERNAL DATA SOURCE] příkaz k uložení umístění dat a typu dat. 

```sql
CREATE EXTERNAL DATA SOURCE AzureStorage_west_public
WITH 
(  
    TYPE = Hadoop 
,   LOCATION = 'wasbs://contosoretaildw-tables@contosoretaildw.blob.core.windows.net/'
); 
```

> [!IMPORTANT]
> Pokud se rozhodnete zveřejněte objektu blob služby azure storage kontejnery, mějte na paměti, že jako vlastník dat vám bude účtovat data poplatky za odchozí přenos dat zůstanou datového centra. 
> 
> 

## <a name="2-configure-data-format"></a>2. Konfigurovat formát dat
Data uložená v textových souborů ve službě Azure blob storage a každé pole je oddělené oddělovačem. To [CREATE EXTERNAL FILE FORMAT] [ CREATE EXTERNAL FILE FORMAT] příkaz k určení formátu dat v textových souborech. Nekomprimovaná data společnosti Contoso a oddělené kanálu.

```sql
CREATE EXTERNAL FILE FORMAT TextFileFormat 
WITH 
(   FORMAT_TYPE = DELIMITEDTEXT
,    FORMAT_OPTIONS    (   FIELD_TERMINATOR = '|'
                    ,    STRING_DELIMITER = ''
                    ,    DATE_FORMAT         = 'yyyy-MM-dd HH:mm:ss.fff'
                    ,    USE_TYPE_DEFAULT = FALSE 
                    )
);
``` 

## <a name="3-create-the-external-tables"></a>3. Vytvoření externích tabulek
Teď, když zadáte zdroj a soubor formátu data, jste připraveni k vytvoření externí tabulky. 

### <a name="31-create-a-schema-for-the-data"></a>3.1. Vytvořte schéma pro data.
Pokud chcete vytvořit místo, kde můžete ukládat data společnosti Contoso ve vaší databázi, vytvoření schématu.

```sql
CREATE SCHEMA [asb]
GO
```

### <a name="32-create-the-external-tables"></a>3.2. Vytvořte externí tabulky.
Spusťte tento skript k vytvoření externí tabulky DimProduct a FactOnlineSales. Všechno, co se nám daří zde je definování názvy sloupců a datové typy a jejich vazbu na umístění a formát souborů úložiště objektů blob v Azure. Definice je uložená ve službě SQL Data Warehouse a data jsou stále v Azure Storage Blob.

**Umístění** parametr je složku, v kořenové složce v Azure Storage Blob. Každá tabulka je v jiné složce.

```sql

--DimProduct
CREATE EXTERNAL TABLE [asb].DimProduct (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL,
    [ProductDescription] [nvarchar](400) NULL,
    [ProductSubcategoryKey] [int] NULL,
    [Manufacturer] [nvarchar](50) NULL,
    [BrandName] [nvarchar](50) NULL,
    [ClassID] [nvarchar](10) NULL,
    [ClassName] [nvarchar](20) NULL,
    [StyleID] [nvarchar](10) NULL,
    [StyleName] [nvarchar](20) NULL,
    [ColorID] [nvarchar](10) NULL,
    [ColorName] [nvarchar](20) NOT NULL,
    [Size] [nvarchar](50) NULL,
    [SizeRange] [nvarchar](50) NULL,
    [SizeUnitMeasureID] [nvarchar](20) NULL,
    [Weight] [float] NULL,
    [WeightUnitMeasureID] [nvarchar](20) NULL,
    [UnitOfMeasureID] [nvarchar](10) NULL,
    [UnitOfMeasureName] [nvarchar](40) NULL,
    [StockTypeID] [nvarchar](10) NULL,
    [StockTypeName] [nvarchar](40) NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [AvailableForSaleDate] [datetime] NULL,
    [StopSaleDate] [datetime] NULL,
    [Status] [nvarchar](7) NULL,
    [ImageURL] [nvarchar](150) NULL,
    [ProductURL] [nvarchar](150) NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/DimProduct/' 
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

--FactOnlineSales
CREATE EXTERNAL TABLE [asb].FactOnlineSales 
(
    [OnlineSalesKey] [int]  NOT NULL,
    [DateKey] [datetime] NOT NULL,
    [StoreKey] [int] NOT NULL,
    [ProductKey] [int] NOT NULL,
    [PromotionKey] [int] NOT NULL,
    [CurrencyKey] [int] NOT NULL,
    [CustomerKey] [int] NOT NULL,
    [SalesOrderNumber] [nvarchar](20) NOT NULL,
    [SalesOrderLineNumber] [int] NULL,
    [SalesQuantity] [int] NOT NULL,
    [SalesAmount] [money] NOT NULL,
    [ReturnQuantity] [int] NOT NULL,
    [ReturnAmount] [money] NULL,
    [DiscountQuantity] [int] NULL,
    [DiscountAmount] [money] NULL,
    [TotalCost] [money] NOT NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/FactOnlineSales/' 
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;
```

## <a name="4-load-the-data"></a>4. Načtení dat
Není k dispozici různé způsoby, jak získat přístup k externím datům.  Dotazování na data přímo z externí tabulky, načtěte data do nové databáze tabulek nebo přidat externí data na stávající databáze tabulky.  

### <a name="41-create-a-new-schema"></a>4.1. Vytvoření nové schéma
Příkaz CTAS vytvoří novou tabulku, která obsahuje data.  Nejprve vytvořte schéma pro data společnosti contoso.

```sql
CREATE SCHEMA [cso]
GO
```

### <a name="42-load-the-data-into-new-tables"></a>4.2. Načtení dat do nové tabulky
Chcete-li načíst data z Azure blob storage a uložte ho do tabulky v databázi, použijte [CREATE TABLE AS SELECT (Transact-SQL)] [ CREATE TABLE AS SELECT (Transact-SQL)] příkazu. Načítání s CTAS využívá silného typu externí tabulky, kterou jste právě vytvořili. Chcete-li do nové tabulky načíst data, použijte jednu [CTAS] [ CTAS] příkaz jednu tabulku. 
 
Funkce CTAS vytvoří novou tabulku a naplní ji výsledky příkazu select. Funkce CTAS definuje novou tabulku pro mají stejné sloupce a datové typy jako výsledky příkazu select. Je-li vybrat všechny sloupce z externí tabulky, bude nová tabulka repliky sloupců a datové typy v externí tabulky.

V tomto příkladu vytvoříme dimenze a tabulka faktů jako distribuované tabulky hash. 

```sql
SELECT GETDATE();
GO

CREATE TABLE [cso].[DimProduct]            WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[DimProduct]             OPTION (LABEL = 'CTAS : Load [cso].[DimProduct]             ');
CREATE TABLE [cso].[FactOnlineSales]       WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[FactOnlineSales]        OPTION (LABEL = 'CTAS : Load [cso].[FactOnlineSales]        ');
```

### <a name="43-track-the-load-progress"></a>4.3 sledovat průběh načítání ukazatel
Můžete sledovat průběh vaší úlohy pomocí zobrazení dynamické správy (DMV). 

```sql
-- To see all requests
SELECT * FROM sys.dm_pdw_exec_requests;

-- To see a particular request identified by its label
SELECT * FROM sys.dm_pdw_exec_requests as r
WHERE r.[label] = 'CTAS : Load [cso].[DimProduct]             '
      OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
;

-- To track bytes and files
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files, 
    sum(s.bytes_processed)/1024/1024/1024 as gb_processed
FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
        on r.request_id = s.request_id
WHERE 
    r.[label] = 'CTAS : Load [cso].[DimProduct]             '
    OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="5-optimize-columnstore-compression"></a>5. Optimalizace komprese columnstore
Ve výchozím nastavení SQL Data Warehouse ukládá jako clusterovaný index columnstore v tabulce. Po dokončení zátěžového některé řádky dat nemusí být komprimovaná do indexu columnstore.  Není k dispozici z různých důvodů, proč k tomu může dojít. Další informace najdete v tématu [spravovat indexy columnstore][manage columnstore indexes].

Pokud chcete optimalizovat výkon dotazů a provádí kompresi columnstore po zatížení, tabulku sestavte v přinutit index columnstore tak, aby se komprimoval všechny řádky. 

```sql
SELECT GETDATE();
GO

ALTER INDEX ALL ON [cso].[DimProduct]               REBUILD;
ALTER INDEX ALL ON [cso].[FactOnlineSales]          REBUILD;
```

Další informace o zachování indexy columnstore, najdete v článku [spravovat indexy columnstore] [ manage columnstore indexes] článku.

## <a name="6-optimize-statistics"></a>6. Optimalizace statistiky
Doporučujeme vytvořit jednosloupcovou statistiku okamžitě po zatížení. Existuje několik možností pro statistiku. Například pokud vytvořit jednosloupcovou statistiku pro každý sloupec může trvat dlouhou dobu znovu sestavit všechny statistiky. Pokud víte, že některé sloupce se bude v predikátech dotazu, můžete přeskočit vytvoření statistiky pro tyto sloupce.

Pokud se rozhodnete vytvořit jednosloupcovou statistiku pro každý sloupec každé tabulky, můžete použít vzorek kódu uloženou proceduru `prc_sqldw_create_stats` v [statistiky] [ statistics] článku.

V následujícím příkladu je dobrým výchozím bodem pro vytváření statistik. Vytvoří jednosloupcovou statistiku pro každý sloupec v tabulce dimenzí a pro každý sloupec spojovacího v tabulkách faktů. Můžete vždy přidat jeden nebo více sloupci statistiky ostatním sloupcům tabulky faktů později.

```sql
CREATE STATISTICS [stat_cso_DimProduct_AvailableForSaleDate] ON [cso].[DimProduct]([AvailableForSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_BrandName] ON [cso].[DimProduct]([BrandName]);
CREATE STATISTICS [stat_cso_DimProduct_ClassID] ON [cso].[DimProduct]([ClassID]);
CREATE STATISTICS [stat_cso_DimProduct_ClassName] ON [cso].[DimProduct]([ClassName]);
CREATE STATISTICS [stat_cso_DimProduct_ColorID] ON [cso].[DimProduct]([ColorID]);
CREATE STATISTICS [stat_cso_DimProduct_ColorName] ON [cso].[DimProduct]([ColorName]);
CREATE STATISTICS [stat_cso_DimProduct_ETLLoadID] ON [cso].[DimProduct]([ETLLoadID]);
CREATE STATISTICS [stat_cso_DimProduct_ImageURL] ON [cso].[DimProduct]([ImageURL]);
CREATE STATISTICS [stat_cso_DimProduct_LoadDate] ON [cso].[DimProduct]([LoadDate]);
CREATE STATISTICS [stat_cso_DimProduct_Manufacturer] ON [cso].[DimProduct]([Manufacturer]);
CREATE STATISTICS [stat_cso_DimProduct_ProductDescription] ON [cso].[DimProduct]([ProductDescription]);
CREATE STATISTICS [stat_cso_DimProduct_ProductKey] ON [cso].[DimProduct]([ProductKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductLabel] ON [cso].[DimProduct]([ProductLabel]);
CREATE STATISTICS [stat_cso_DimProduct_ProductName] ON [cso].[DimProduct]([ProductName]);
CREATE STATISTICS [stat_cso_DimProduct_ProductSubcategoryKey] ON [cso].[DimProduct]([ProductSubcategoryKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductURL] ON [cso].[DimProduct]([ProductURL]);
CREATE STATISTICS [stat_cso_DimProduct_Size] ON [cso].[DimProduct]([Size]);
CREATE STATISTICS [stat_cso_DimProduct_SizeRange] ON [cso].[DimProduct]([SizeRange]);
CREATE STATISTICS [stat_cso_DimProduct_SizeUnitMeasureID] ON [cso].[DimProduct]([SizeUnitMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_Status] ON [cso].[DimProduct]([Status]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeID] ON [cso].[DimProduct]([StockTypeID]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeName] ON [cso].[DimProduct]([StockTypeName]);
CREATE STATISTICS [stat_cso_DimProduct_StopSaleDate] ON [cso].[DimProduct]([StopSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_StyleID] ON [cso].[DimProduct]([StyleID]);
CREATE STATISTICS [stat_cso_DimProduct_StyleName] ON [cso].[DimProduct]([StyleName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitCost] ON [cso].[DimProduct]([UnitCost]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureID] ON [cso].[DimProduct]([UnitOfMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureName] ON [cso].[DimProduct]([UnitOfMeasureName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitPrice] ON [cso].[DimProduct]([UnitPrice]);
CREATE STATISTICS [stat_cso_DimProduct_UpdateDate] ON [cso].[DimProduct]([UpdateDate]);
CREATE STATISTICS [stat_cso_DimProduct_Weight] ON [cso].[DimProduct]([Weight]);
CREATE STATISTICS [stat_cso_DimProduct_WeightUnitMeasureID] ON [cso].[DimProduct]([WeightUnitMeasureID]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CurrencyKey] ON [cso].[FactOnlineSales]([CurrencyKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CustomerKey] ON [cso].[FactOnlineSales]([CustomerKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_DateKey] ON [cso].[FactOnlineSales]([DateKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_OnlineSalesKey] ON [cso].[FactOnlineSales]([OnlineSalesKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_ProductKey] ON [cso].[FactOnlineSales]([ProductKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_PromotionKey] ON [cso].[FactOnlineSales]([PromotionKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_StoreKey] ON [cso].[FactOnlineSales]([StoreKey]);
```

## <a name="achievement-unlocked"></a>Odemknout úspěch!
Veřejná data úspěšně načetly do Azure SQL Data Warehouse. Skvělá práce!

Nyní můžete spustit dotazy na tabulky pomocí dotazů vypadat asi takto:

```sql
SELECT  SUM(f.[SalesAmount]) AS [sales_by_brand_amount]
,       p.[BrandName]
FROM    [cso].[FactOnlineSales] AS f
JOIN    [cso].[DimProduct]      AS p ON f.[ProductKey] = p.[ProductKey]
GROUP BY p.[BrandName]
```

## <a name="next-steps"></a>Další postup
Chcete-li načtěte kompletní data společnosti Contoso maloobchodní datového skladu, použijte skript v další tipy pro vývoj, naleznete v tématu [přehled vývoje SQL Data Warehouse][SQL Data Warehouse development overview].

<!--Image references-->

<!--Article references-->
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Load data into SQL Data Warehouse]: sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[manage columnstore indexes]: sql-data-warehouse-tables-index.md
[Statistics]: sql-data-warehouse-tables-statistics.md
[CTAS]: sql-data-warehouse-develop-ctas.md
[label]: sql-data-warehouse-develop-label.md

<!--MSDN references-->
[CREATE EXTERNAL DATA SOURCE]: https://msdn.microsoft.com/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[REBUILD]: https://msdn.microsoft.com/library/ms188388.aspx

<!--Other Web references-->
[Microsoft Download Center]: http://www.microsoft.com/download/details.aspx?id=36433
[Load the full Contoso Retail Data Warehouse]: https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md
