---
title: Načíst data o maloobchodním Contoso do Azure SQL Data Warehouse | Microsoft Docs
description: Načtení dvou tabulek z Contoso prodejní data do Azure SQL Data Warehouse pomocí PolyBase a T-SQL příkazů.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 0b066699475b753bb1104a78a4a1c60564535700
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32190891"
---
# <a name="load-contoso-retail-data-to-azure-sql-data-warehouse"></a>Načíst data o maloobchodním Contoso do Azure SQL Data Warehouse

Načtení dvou tabulek z Contoso prodejní data do Azure SQL Data Warehouse pomocí PolyBase a T-SQL příkazů. Pokud chcete načíst úplnou datovou sadu, spusťte v příkladu [načíst úplné Contoso prodejní datového skladu](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md) z úložiště ukázky Microsoft SQL Server.

V tomto kurzu provedete následující:

1. Konfigurace PolyBase načíst z Azure blob storage
2. Načíst veřejná data do databáze
3. Po dokončení zatížení, provedení optimalizace.

## <a name="before-you-begin"></a>Než začnete
Spustit v tomto kurzu, budete potřebovat účet Azure, která již obsahuje databázi SQL Data Warehouse. Pokud to nemáte, přečtěte si téma [vytvořit SQL Data Warehouse][Create a SQL Data Warehouse].

## <a name="1-configure-the-data-source"></a>1. Nakonfigurujte zdroj dat
PolyBase používá externí objekty T-SQL zadat umístění a atributy externí data. Definice externího objektu jsou uloženy v SQL Data Warehouse. Samotná data ukládána externě.

### <a name="11-create-a-credential"></a>1.1. Vytvoření přihlašovacích údajů
**Tento krok přeskočit** při zavádění veřejná data společnosti Contoso. Nepotřebujete zabezpečený přístup k veřejná data, protože je již přístupné všem uživatelům.

**Není tento krok přeskočit** Pokud používáte v tomto kurzu jako šablona pro načítání svoje vlastní data. Přístup k datům prostřednictvím pověření pomocí následujícího skriptu vytvoření přihlašovacích údajů platných pro databázi a použít jej při definování umístění zdroje dat.

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
Použít [vytvořit externí zdroj dat] [ CREATE EXTERNAL DATA SOURCE] příkazu umístění dat a typu dat úložiště. 

```sql
CREATE EXTERNAL DATA SOURCE AzureStorage_west_public
WITH 
(  
    TYPE = Hadoop 
,   LOCATION = 'wasbs://contosoretaildw-tables@contosoretaildw.blob.core.windows.net/'
); 
```

> [!IMPORTANT]
> Pokud zvolíte možnost zveřejnit objektu blob služby azure storage kontejnery, mějte na paměti, že jako vlastník dat vám bude účtována pro data s nimi spojeným nákladům při data ponechá datového centra. 
> 
> 

## <a name="2-configure-data-format"></a>2. Konfigurovat formát dat
Jsou data uložena v textových souborů v Azure blob storage a každé pole je oddělená s oddělovačem. To [vytvořit EXTERNAL FILE FORMAT] [ CREATE EXTERNAL FILE FORMAT] příkaz k určení formátu dat v textových souborů. Data společnosti Contoso se nekomprimované a oddělený kanálu.

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

## <a name="3-create-the-external-tables"></a>3. Vytvoření externí tabulky
Formát dat zdroje a soubor jste zadali, jste připraveni k vytvoření externí tabulky. 

### <a name="31-create-a-schema-for-the-data"></a>3.1. Vytvořte schéma pro data.
Pokud chcete vytvořit místo, kam můžete ukládat data společnosti Contoso ve vaší databázi, vytvořte schéma.

```sql
CREATE SCHEMA [asb]
GO
```

### <a name="32-create-the-external-tables"></a>3.2. Vytvořte externí tabulky.
Spusťte tento skript pro vytvoření DimProduct a FactOnlineSales externí tabulky. Všechny, které jsme to děláte, zde je definování názvy sloupců a datové typy a vazba je do umístění a formát souborů úložiště objektů blob v Azure. Definice je uložená v SQL Data Warehouse a dat je stále v Azure Storage Blob.

**Umístění** parametr je složka, v kořenové složce v Azure Storage Blob. Každá tabulka je v jiné složce.

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
Není k dispozici různé způsoby přístup k externím datům.  Můžete zadávat dotazy na data přímo z externí tabulky, načíst data do nové tabulky databáze nebo přidat externí data do existující tabulky databáze.  

### <a name="41-create-a-new-schema"></a>4.1. Vytvořit nové schéma
Funkce CTAS vytvoří novou tabulku, která obsahuje data.  Nejprve vytvořte schéma pro data společnosti contoso.

```sql
CREATE SCHEMA [cso]
GO
```

### <a name="42-load-the-data-into-new-tables"></a>4.2. Načíst data do nové tabulky
Pokud chcete načíst data z Azure blob storage a uložit ho v tabulce uvnitř vaší databáze, použijte [CREATE TABLE AS SELECT (Transact-SQL)] [ CREATE TABLE AS SELECT (Transact-SQL)] příkaz. Načítání se funkce CTAS využívá silného typu externí tabulky, které jste právě vytvořili. Chcete-li načíst data do nové tabulky, použijte jednu [funkce CTAS] [ CTAS] příkaz na jednu tabulku. 
 
Funkce CTAS vytvoří novou tabulku a naplní s výsledky příkazu select. Funkce CTAS definuje novou tabulku tak, aby měl stejné sloupce a typy dat jako výsledky příkazu select. Pokud vyberete všechny sloupce z externí tabulky, bude nová tabulka repliku sloupce a typy dat v externí tabulky.

V tomto příkladu vytvoříme dimenze a tabulky faktů jako hash distribuované tabulky. 

```sql
SELECT GETDATE();
GO

CREATE TABLE [cso].[DimProduct]            WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[DimProduct]             OPTION (LABEL = 'CTAS : Load [cso].[DimProduct]             ');
CREATE TABLE [cso].[FactOnlineSales]       WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[FactOnlineSales]        OPTION (LABEL = 'CTAS : Load [cso].[FactOnlineSales]        ');
```

### <a name="43-track-the-load-progress"></a>4.3 sledovat průběh zatížení
Můžete sledovat průběh zatížení pomocí zobrazení dynamické správy (zobrazení dynamické správy). 

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

## <a name="5-optimize-columnstore-compression"></a>5. Optimalizace columnstore komprese
Ve výchozím nastavení SQL Data Warehouse ukládá jako clusterovaný index columnstore v tabulce. Po dokončení zatížení některé řádky dat nemusí být komprimovány do columnstore.  Je z různých důvodů, proč k tomu může dojít. Další informace najdete v tématu [spravovat indexy columnstore][manage columnstore indexes].

Chcete-li optimalizovat výkon dotazů a komprese columnstore po zatížení, znovu sestavte vynutit index columnstore komprimovat všechny řádky v tabulce. 

```sql
SELECT GETDATE();
GO

ALTER INDEX ALL ON [cso].[DimProduct]               REBUILD;
ALTER INDEX ALL ON [cso].[FactOnlineSales]          REBUILD;
```

Další informace o údržbě indexů columnstore, najdete v článku [spravovat indexy columnstore] [ manage columnstore indexes] článku.

## <a name="6-optimize-statistics"></a>6. Optimalizace statistiky
Je vhodné vytvořit jednosloupcovou statistiku okamžitě po zatížení. Existuje několik možností pro statistiky. Například pokud vytvoříte jednosloupcovou statistiku pro každý sloupec může trvat dlouhou dobu znovu vytvořit všechny statistiky. Pokud víte, že některé sloupce nejsou má být v predikátech dotazu, můžete přeskočit vytvoření statistiky pro tyto sloupce.

Pokud se rozhodnete vytvořit jednosloupcovou statistiku pro každý sloupec každé tabulky, můžete použít ukázka kódu uložené procedury `prc_sqldw_create_stats` v [statistiky] [ statistics] článku.

V následujícím příkladu je to dobrý výchozí bod pro vytvoření statistiky. Vytvoří jednosloupcovou statistiku pro každý sloupec v tabulce dimenze a pro každý sloupec spojující v tabulkách faktů. Vždy přidáním statistiky jeden nebo více sloupců do ostatních sloupců tabulky faktů později.

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

## <a name="achievement-unlocked"></a>Dosažení odemčený!
Veřejná data mají úspěšně načíst do Azure SQL Data Warehouse. Skvělá práce!

Nyní můžete spustit dotazu na tabulky pomocí dotazů takto:

```sql
SELECT  SUM(f.[SalesAmount]) AS [sales_by_brand_amount]
,       p.[BrandName]
FROM    [cso].[FactOnlineSales] AS f
JOIN    [cso].[DimProduct]      AS p ON f.[ProductKey] = p.[ProductKey]
GROUP BY p.[BrandName]
```

## <a name="next-steps"></a>Další postup
Pokud chcete načíst data úplné Contoso prodejní datového skladu, použijte skript v další tipy pro vývoj, najdete v části [přehled vývoje SQL Data Warehouse][SQL Data Warehouse development overview].

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
