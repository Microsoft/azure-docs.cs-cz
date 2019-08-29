---
title: Načtení maloobchodních dat Contoso do Azure SQL Data Warehouse | Microsoft Docs
description: Použijte příkazy jazyka T-SQL k načtení dvou tabulek z maloobchodních dat společnosti Contoso do Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load-data
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: b96b65b7dd38900fccb8d5d3a9133f37ee93949f
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "67595524"
---
# <a name="load-contoso-retail-data-to-azure-sql-data-warehouse"></a>Načtení maloobchodních dat Contoso do Azure SQL Data Warehouse

V tomto kurzu se naučíte používat příkazy jazyka T-SQL k načtení dvou tabulek z maloobchodních dat společnosti Contoso do Azure SQL Data Warehouse. 

V tomto kurzu provedete tyto kroky:

1. Konfigurace základu pro načtení ze služby Azure Blob Storage
2. Načtení veřejných dat do databáze
3. Proveďte optimalizace po dokončení zátěže.

## <a name="before-you-begin"></a>Před zahájením
Ke spuštění tohoto kurzu potřebujete účet Azure, který už SQL Data Warehouse. Pokud nemáte zřízen datový sklad, přečtěte si téma [vytvoření SQL Data Warehouse a nastavení pravidla brány firewall na úrovni serveru][Create a SQL Data Warehouse].

## <a name="1-configure-the-data-source"></a>1. Konfigurace zdroje dat
Základna používá externí objekty T-SQL k definování umístění a atributů externích dat. Definice externích objektů jsou uloženy v SQL Data Warehouse. Data jsou ukládána externě.

### <a name="11-create-a-credential"></a>1.1. Vytvoření přihlašovacích údajů
**Tento krok přeskočte** , pokud načítáte veřejná data společnosti Contoso. Nepotřebujete zabezpečený přístup k veřejným datům, protože už je přístupná pro kohokoli.

**Tento krok** nepřeskakujte, pokud tento kurz používáte jako šablonu pro načítání vlastních dat. Pro přístup k datům prostřednictvím přihlašovacích údajů použijte následující skript k vytvoření přihlašovacích údajů v rámci databáze a pak ji použijte při definování umístění zdroje dat.

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

### <a name="12-create-the-external-data-source"></a>1.2. Vytvoření externího zdroje dat
Pomocí tohoto příkazu [vytvořit externí zdroj dat][CREATE EXTERNAL DATA SOURCE] můžete uložit umístění dat a typ dat. 

```sql
CREATE EXTERNAL DATA SOURCE AzureStorage_west_public
WITH 
(  
    TYPE = Hadoop 
,   LOCATION = 'wasbs://contosoretaildw-tables@contosoretaildw.blob.core.windows.net/'
); 
```

> [!IMPORTANT]
> Pokud se rozhodnete, aby byly kontejnery úložiště objektů BLOB v Azure veřejné, pamatujte, že jako vlastník dat se vám budou účtovat poplatky za přenos dat, když data opustí datové centrum. 
> 
> 

## <a name="2-configure-data-format"></a>2. Konfigurace formátu dat
Data jsou uložená v textových souborech v úložišti objektů BLOB v Azure a každé pole je oddělené oddělovačem. V SSMS spusťte následující příkaz [Create External File Format][CREATE EXTERNAL FILE FORMAT] a určete formát dat v textových souborech. Data společnosti Contoso jsou nekomprimovaná a oddělená kanálem.

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
Teď, když jste zadali zdroj dat a formát souboru, jste připraveni vytvořit externí tabulky. 

### <a name="31-create-a-schema-for-the-data"></a>3.1. Vytvořte schéma pro data.
Chcete-li vytvořit místo pro uložení dat společnosti Contoso v databázi, vytvořte schéma.

```sql
CREATE SCHEMA [asb]
GO
```

### <a name="32-create-the-external-tables"></a>3.2. Vytvořte externí tabulky.
Spusťte následující skript pro vytvoření externích tabulek DimProduct a FactOnlineSales. Tady je definování názvů sloupců a datových typů a jejich navázání na umístění a formát souborů úložiště objektů BLOB v Azure. Definice je uložena v SQL Data Warehouse a data jsou stále v Azure Storage Blob.

Parametr **Location** je složka v kořenové složce Azure Storage BLOB. Každá tabulka je v jiné složce.

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
Existují různé způsoby, jak získat přístup k externím datům.  Můžete zadávat dotazy na data přímo z externích tabulek, načítat data do nových tabulek v datovém skladu nebo přidávat externí data do existujících tabulek datového skladu.  

### <a name="41-create-a-new-schema"></a>4.1. Vytvořit nové schéma
CTAS vytvoří novou tabulku, která obsahuje data.  Nejprve vytvořte schéma pro data společnosti Contoso.

```sql
CREATE SCHEMA [cso]
GO
```

### <a name="42-load-the-data-into-new-tables"></a>4.2. Načtení dat do nových tabulek
K načtení dat z úložiště objektů BLOB v Azure do tabulky datového skladu použijte příkaz [CREATE TABLE AS Select (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)] . Načítání pomocí CTAS využívá externí tabulky silného typu, které jste vytvořili. Chcete-li načíst data do nových tabulek, použijte jeden příkaz [CTAS][CTAS] na tabulku. 
 
CTAS vytvoří novou tabulku a naplní ji výsledky příkazu SELECT. CTAS definuje novou tabulku, která bude mít stejné sloupce a datové typy jako výsledky příkazu SELECT. Pokud vyberete všechny sloupce z externí tabulky, bude nová tabulka replikou sloupců a datových typů v externí tabulce.

V tomto příkladu vytvoříme dimenzi i tabulku faktů jako distribuované tabulky hash. 

```sql
SELECT GETDATE();
GO

CREATE TABLE [cso].[DimProduct]            WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[DimProduct]             OPTION (LABEL = 'CTAS : Load [cso].[DimProduct]             ');
CREATE TABLE [cso].[FactOnlineSales]       WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[FactOnlineSales]        OPTION (LABEL = 'CTAS : Load [cso].[FactOnlineSales]        ');
```

### <a name="43-track-the-load-progress"></a>4,3 sledovat průběh načítání
Průběh zátěže můžete sledovat pomocí zobrazení dynamické správy (zobrazení dynamické správy). 

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
Ve výchozím nastavení SQL Data Warehouse ukládá tabulku jako clusterovaný index columnstore. Po dokončení načtení se některé řádky dat nemusí do columnstore komprimovat.  Existují různé důvody, proč k tomu může dojít. Další informace najdete v tématu [Správa indexů columnstore][manage columnstore indexes].

Pro optimalizaci výkonu dotazů a komprese columnstore po načtení znovu sestavte tabulku, aby index columnstore vynutil komprimaci všech řádků. 

```sql
SELECT GETDATE();
GO

ALTER INDEX ALL ON [cso].[DimProduct]               REBUILD;
ALTER INDEX ALL ON [cso].[FactOnlineSales]          REBUILD;
```

Další informace o údržbě indexů columnstore najdete v článku [Správa indexů][manage columnstore indexes] columnstore.

## <a name="6-optimize-statistics"></a>6. Optimalizace statistik
Je nejlepší vytvořit statistiku s jedním sloupcem hned po načtení. Pokud víte, že některé sloupce nebudou v predikátech dotazů, můžete na tyto sloupce přeskočit vytváření statistik. Pokud vytvoříte statistiku s jedním sloupcem v každém sloupci, může se znovu sestavit všechny statistiky. 

Pokud se rozhodnete vytvořit statistiku s jedním sloupcem pro každý sloupec každé tabulky, můžete použít ukázku `prc_sqldw_create_stats` kódu uložené procedury v článku [Statistika][statistics] .

Následující příklad je dobrým výchozím bodem pro vytváření statistik. Vytvoří statistiku s jedním sloupcem pro každý sloupec v tabulce dimenzí a pro každý sloupec spojování v tabulkách faktů. Můžete kdykoli přidat statistiku jednoho nebo více sloupců do dalších sloupců tabulky faktů.

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

## <a name="achievement-unlocked"></a>Úspěch je odemčený!
Do Azure SQL Data Warehouse jste úspěšně načetli veřejná data. Skvělá práce!

Teď můžete začít dotazovat se na tabulky a prozkoumat data. Chcete-li zjistit celkový prodej na jednu značku, spusťte následující dotaz:

```sql
SELECT  SUM(f.[SalesAmount]) AS [sales_by_brand_amount]
,       p.[BrandName]
FROM    [cso].[FactOnlineSales] AS f
JOIN    [cso].[DimProduct]      AS p ON f.[ProductKey] = p.[ProductKey]
GROUP BY p.[BrandName]
```

## <a name="next-steps"></a>Další postup
Pokud chcete načíst celou datovou sadu, spusťte příklad [načtení úplného maloobchodního datového skladu společnosti Contoso](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md) z úložiště ukázek Microsoft SQL Server.

Další tipy pro vývoj najdete v části [Přehled vývoje SQL Data Warehouse][SQL Data Warehouse development overview].

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
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
[Load the full Contoso Retail Data Warehouse]: https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md
