---
title: Načtení maloobchodních dat Contoso do synapse SQL
description: K načtení dvou tabulek z maloobchodních dat společnosti Contoso do synapse SQL použijte příkazy jazyka T-SQL.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 904ce55f376e42156b014056b1226512b2784742
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2020
ms.locfileid: "89461693"
---
# <a name="load-contoso-retail-data-to-synapse-sql"></a>Načtení maloobchodních dat Contoso do synapse SQL 

V tomto kurzu se naučíte používat příkazy jazyka T-SQL k načtení dvou tabulek z maloobchodních dat společnosti Contoso do synapse SQL.

V tomto kurzu provedete tyto kroky:

1. Konfigurace základu pro načtení ze služby Azure Blob Storage
2. Načtení veřejných dat do databáze
3. Proveďte optimalizace po dokončení zátěže.

## <a name="before-you-begin"></a>Než začnete

Ke spuštění tohoto kurzu potřebujete účet Azure, který už má synapse SQL. Pokud nemáte zřízen datový sklad, přečtěte si téma [vytvoření datového skladu a nastavení pravidla brány firewall na úrovni serveru](create-data-warehouse-portal.md).

## <a name="configure-the-data-source"></a>Konfigurace zdroje dat

Základna používá externí objekty T-SQL k definování umístění a atributů externích dat. Definice externích objektů jsou uloženy v synapse SQL. Data jsou ukládána externě.

## <a name="create-a-credential"></a>Vytvoření přihlašovacích údajů

**Tento krok přeskočte** , pokud načítáte veřejná data společnosti Contoso. Nepotřebujete zabezpečený přístup k veřejným datům, protože už je přístupná pro kohokoli.

**Tento krok nepřeskakujte** , pokud tento kurz používáte jako šablonu pro načítání vlastních dat. Pro přístup k datům prostřednictvím přihlašovacích údajů použijte následující skript k vytvoření přihlašovacích údajů v oboru databáze. Pak ji použijte při definování umístění zdroje dat.

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

## <a name="create-the-external-data-source"></a>Vytvoření externího zdroje dat

Pomocí tohoto příkazu [Create External data source](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) uložte umístění dat a datový typ.

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

## <a name="configure-the-data-format"></a>Konfigurace formátu dat

Data jsou uložená v textových souborech v úložišti objektů BLOB v Azure a každé pole je oddělené oddělovačem. V SSMS spusťte následující příkaz CREATE EXTERNAL FILE FORMAT a určete formát dat v textových souborech. Data společnosti Contoso jsou nekomprimovaná a oddělená kanálem.

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

## <a name="create-the-schema-for-the-external-tables"></a>Vytvoření schématu pro externí tabulky

Teď, když jste zadali zdroj dat a formát souboru, jste připraveni vytvořit schéma pro externí tabulky.

Chcete-li vytvořit místo pro uložení dat společnosti Contoso v databázi, vytvořte schéma.

```sql
CREATE SCHEMA [asb]
GO
```

## <a name="create-the-external-tables"></a>Vytvoření externích tabulek

Spusťte následující skript pro vytvoření externích tabulek DimProduct a FactOnlineSales. Tady je definování názvů sloupců a datových typů a jejich navázání na umístění a formát souborů úložiště objektů BLOB v Azure. Definice je uložena v datovém skladu a data jsou stále v Azure Storage Blob.

Parametr  **Location** je složka v kořenové složce Azure Storage BLOB. Každá tabulka je v jiné složce.

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

## <a name="load-the-data"></a>Načtení dat

Existují různé způsoby, jak získat přístup k externím datům.  Můžete zadávat dotazy na data přímo z externích tabulek, načítat data do nových tabulek v datovém skladu nebo přidávat externí data do existujících tabulek datového skladu.  

### <a name="create-a-new-schema"></a>Vytvořit nové schéma

CTAS vytvoří novou tabulku, která obsahuje data.  Nejprve vytvořte schéma pro data společnosti Contoso.

```sql
CREATE SCHEMA [cso]
GO
```

### <a name="load-the-data-into-new-tables"></a>Načtení dat do nových tabulek

K načtení dat z úložiště objektů BLOB v Azure do tabulky datového skladu použijte příkaz [CREATE TABLE AS Select (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) . Načítání pomocí [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) využívá externí tabulky silného typu, které jste vytvořili. Chcete-li načíst data do nových tabulek, použijte jeden příkaz CTAS na tabulku.

CTAS vytvoří novou tabulku a naplní ji výsledky příkazu SELECT. CTAS definuje novou tabulku, která bude mít stejné sloupce a datové typy jako výsledky příkazu SELECT. Pokud vyberete všechny sloupce z externí tabulky, bude nová tabulka replikou sloupců a datových typů v externí tabulce.

V tomto příkladu vytvoříme dimenzi i tabulku faktů jako distribuované tabulky hash.

```sql
SELECT GETDATE();
GO

CREATE TABLE [cso].[DimProduct]            WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[DimProduct]             OPTION (LABEL = 'CTAS : Load [cso].[DimProduct]             ');
CREATE TABLE [cso].[FactOnlineSales]       WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[FactOnlineSales]        OPTION (LABEL = 'CTAS : Load [cso].[FactOnlineSales]        ');
```

### <a name="track-the-load-progress"></a>Sledovat průběh načítání

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

## <a name="optimize-columnstore-compression"></a>Optimalizace komprese columnstore

Ve výchozím nastavení synapse SQL ukládá tabulku jako clusterovaný index columnstore. Po dokončení načtení se některé řádky dat nemusí do columnstore komprimovat.  Existují různé důvody, proč k tomu může dojít. Další informace najdete v tématu [Správa indexů columnstore](sql-data-warehouse-tables-index.md).

Pro optimalizaci výkonu dotazů a komprese columnstore po načtení znovu sestavte tabulku, aby index columnstore vynutil komprimaci všech řádků.

```sql
SELECT GETDATE();
GO

ALTER INDEX ALL ON [cso].[DimProduct]               REBUILD;
ALTER INDEX ALL ON [cso].[FactOnlineSales]          REBUILD;
```

Další informace o údržbě indexů columnstore najdete v článku [Správa indexů](sql-data-warehouse-tables-index.md) columnstore.

## <a name="optimize-statistics"></a>Optimalizace statistik

Je nejlepší vytvořit statistiku s jedním sloupcem hned po načtení. Pokud víte, že některé sloupce nebudou v predikátech dotazů, můžete na tyto sloupce přeskočit vytváření statistik. Pokud vytvoříte statistiku s jedním sloupcem v každém sloupci, může se znovu sestavit všechny statistiky.

Pokud se rozhodnete vytvořit statistiku s jedním sloupcem pro každý sloupec každé tabulky, můžete použít ukázku kódu uložené procedury `prc_sqldw_create_stats` v článku [Statistika](sql-data-warehouse-tables-statistics.md) .

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

Do datového skladu jste úspěšně načetli veřejná data. Skvělá práce!

Teď můžete začít dotazovat se na tabulky a prozkoumat data. Chcete-li zjistit celkový prodej na jednu značku, spusťte následující dotaz:

```sql
SELECT  SUM(f.[SalesAmount]) AS [sales_by_brand_amount]
,       p.[BrandName]
FROM    [cso].[FactOnlineSales] AS f
JOIN    [cso].[DimProduct]      AS p ON f.[ProductKey] = p.[ProductKey]
GROUP BY p.[BrandName]
```

## <a name="next-steps"></a>Další kroky

Pokud chcete načíst celou datovou sadu, spusťte příklad [načtení úplného maloobchodního datového skladu společnosti Contoso](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md) z úložiště ukázek Microsoft SQL Server.
Další tipy pro vývoj najdete v tématu věnovaném [rozhodování o návrhu a technikům kódování pro datové sklady](sql-data-warehouse-overview-develop.md).
