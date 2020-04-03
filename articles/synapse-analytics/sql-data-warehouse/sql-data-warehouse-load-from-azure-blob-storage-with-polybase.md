---
title: Načtení maloobchodních dat společnosti Contoso do datového skladu Synapse SQL
description: Příkazy PolyBase a T-SQL slouží k načtení dvou tabulek z maloobchodních dat společnosti Contoso do synapse SQL.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 7460a59dd2a7a5906a483195929136391657fa50
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584010"
---
# <a name="load-contoso-retail-data-to-a-synapse-sql-data-warehouse"></a>Načtení maloobchodních dat společnosti Contoso do datového skladu Synapse SQL

V tomto kurzu se naučíte používat příkazy PolyBase a T-SQL k načtení dvou tabulek z maloobchodních dat Contoso do datového skladu Synapse SQL.

V tomto tutoriálu budete:

1. Konfigurace polybase pro načtení z úložiště objektů blob Azure
2. Načítání veřejných dat do databáze
3. Po dokončení načítání proveďte optimalizace.

## <a name="before-you-begin"></a>Než začnete

Ke spuštění tohoto kurzu potřebujete účet Azure, který už má datový sklad Synapse SQL. Pokud nemáte zřízený datový sklad, přečtěte si článek [Vytvoření datového skladu a nastavení pravidla brány firewall na úrovni serveru](create-data-warehouse-portal.md).

## <a name="configure-the-data-source"></a>Konfigurace zdroje dat

PolyBase používá externí objekty T-SQL k definování umístění a atributů externích dat. Definice externích objektů jsou uloženy v datovém skladu Synapse SQL. Data jsou uložena externě.

## <a name="create-a-credential"></a>Vytvoření pověření

**Tento krok přeskočte,** pokud načítáte veřejná data contoso. Nepotřebujete zabezpečený přístup k veřejným datům, protože jsou již přístupná komukoli.

**Tento krok nepřeskakujte,** pokud tento kurz používáte jako šablonu pro načítání vlastních dat. Chcete-li získat přístup k datům prostřednictvím pověření, vytvořte pověření s rozsahem databáze pomocí následujícího skriptu. Pak ji použijte při definování umístění zdroje dat.

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

Pomocí tohoto příkazu [VYTVOŘIT EXTERNÍ ZDROJ DAT](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql?view=sql-server-ver15) můžete uložit umístění dat a datový typ. 

```sql
CREATE EXTERNAL DATA SOURCE AzureStorage_west_public
WITH 
(  
    TYPE = Hadoop 
,   LOCATION = 'wasbs://contosoretaildw-tables@contosoretaildw.blob.core.windows.net/'
); 
```

> [!IMPORTANT]
> Pokud se rozhodnete, že vaše kontejnery úložiště objektů blob azure budou veřejné, nezapomeňte, že jako vlastník dat se vám budou účtovat poplatky za odchozí data, když data opustí datové centrum. 
> 

## <a name="configure-the-data-format"></a>Konfigurace formátu dat

Data jsou uložená v textových souborech v úložišti objektů blob Azure a každé pole je odděleno oddělovačem. V SSMS spusťte následující příkaz CREATE EXTERNAL FILE FORMAT a určete formát dat v textových souborech. Data Contoso jsou nekomprimovaná a potrubí odděleno.

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

## <a name="create-the-external-tables"></a>Vytvoření externích tabulek
Teď, když jste zadali zdroj dat a formát souboru, jste připraveni vytvořit externí tabulky. 

## <a name="create-a-schema-for-the-data"></a>Vytvoření schématu pro data
Chcete-li vytvořit místo pro uložení dat contoso v databázi, vytvořte schéma.

```sql
CREATE SCHEMA [asb]
GO
```

## <a name="create-the-external-tables"></a>Vytvoření externích tabulek

Spusťte následující skript a vytvořte externí tabulky DimProduct a FactOnlineSales. Jediné, co tady děláte, je definování názvů sloupců a datových typů a jejich vazba na umístění a formát souborů úložiště objektů blob Azure. Definice se uložených v datovém skladu a data je stále v objektu blob úložiště Azure.

Parametr **LOCATION** je složka pod kořenovou složkou v objektu blob úložiště Azure. Každá tabulka je v jiné složce.

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
Existují různé způsoby přístupu k externím datům.  Data můžete dotazovat přímo z externích tabulek, načíst je do nových tabulek v datovém skladu nebo přidat externí data do existujících tabulek datového skladu.  

###  <a name="create-a-new-schema"></a>Vytvoření nového schématu

CTAS vytvoří novou tabulku, která obsahuje data.  Nejprve vytvořte schéma pro data contoso.

```sql
CREATE SCHEMA [cso]
GO
```

### <a name="load-the-data-into-new-tables"></a>Načtení dat do nových tabulek

Chcete-li načíst data z úložiště objektů blob Azure do tabulky datového skladu, použijte příkaz [CREATE TABLE AS SELECT (Transact-SQL).](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=aps-pdw-2016-au7) Načítání pomocí [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md) využívá silně typované externí tabulky, které jste vytvořili. Chcete-li načíst data do nových tabulek, použijte jeden příkaz CTAS pro tabulku. 
 
CTAS vytvoří novou tabulku a naplní ji výsledky příkazu select. CTAS definuje novou tabulku mít stejné sloupce a datové typy jako výsledky příkazu select. Pokud vyberete všechny sloupce z externí tabulky, bude nová tabulka replikou sloupců a datových typů v externí tabulce.

V tomto příkladu vytvoříme dimenzi i tabulku faktů jako distribuované tabulky hash. 

```sql
SELECT GETDATE();
GO

CREATE TABLE [cso].[DimProduct]            WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[DimProduct]             OPTION (LABEL = 'CTAS : Load [cso].[DimProduct]             ');
CREATE TABLE [cso].[FactOnlineSales]       WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[FactOnlineSales]        OPTION (LABEL = 'CTAS : Load [cso].[FactOnlineSales]        ');
```

### <a name="track-the-load-progress"></a>Sledování průběhu načítání

Průběh načítání můžete sledovat pomocí dynamických zobrazení správy (DMVs). 

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

Ve výchozím nastavení ukládá datový sklad Synapse SQL tabulku jako clusterovaný index columnstore. Po dokončení zatížení některé řádky dat nemusí být komprimovány do columnstore.  Existují různé důvody, proč k tomu může dojít. Další informace naleznete v [tématu správa indexů columnstore](sql-data-warehouse-tables-index.md).

Chcete-li optimalizovat výkon dotazu a kompresi columnstore po zatížení, znovu sestavit tabulku vynutit columnstore index komprimovat všechny řádky. 

```sql
SELECT GETDATE();
GO

ALTER INDEX ALL ON [cso].[DimProduct]               REBUILD;
ALTER INDEX ALL ON [cso].[FactOnlineSales]          REBUILD;
```

Další informace o udržování indexů columnstore naleznete v článku [správa indexů columnstore.](sql-data-warehouse-tables-index.md)

## <a name="optimize-statistics"></a>Optimalizace statistik

Je nejlepší vytvořit statistiky s jedním sloupcem ihned po zatížení. Pokud víte, že některé sloupce nebudou v predikátech dotazů, můžete přeskočit vytváření statistik v těchto sloupcích. Pokud vytvoříte statistiky s jedním sloupcem pro každý sloupec, může trvat dlouhou dobu znovu sestavit všechny statistiky. 

Pokud se rozhodnete vytvořit statistiku jednoho sloupce na každém sloupci každé `prc_sqldw_create_stats` tabulky, můžete použít ukázku kódu uložené procedury v článku [statistiky.](sql-data-warehouse-tables-statistics.md)

Následující příklad je dobrým výchozím bodem pro vytváření statistik. Vytvoří jednosloupcové statistiky pro každý sloupec v tabulce dimenzí a na každém spojovacím sloupci v tabulkách faktů. Statistiky s jedním nebo více sloupci můžete vždy přidat do jiných sloupců tabulky faktů později.

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

## <a name="achievement-unlocked"></a>Úspěch odemčený!
Úspěšně jste načetli veřejná data do datového skladu. Skvělá práce!

Nyní můžete začít dotazovat tabulek, abyste prozkoumali data. Spusťte následující dotaz, abyste zjistili celkový prodej podle značky:

```sql
SELECT  SUM(f.[SalesAmount]) AS [sales_by_brand_amount]
,       p.[BrandName]
FROM    [cso].[FactOnlineSales] AS f
JOIN    [cso].[DimProduct]      AS p ON f.[ProductKey] = p.[ProductKey]
GROUP BY p.[BrandName]
```

## <a name="next-steps"></a>Další kroky
Chcete-li načíst úplnou sadu dat, spusťte příklad [načtení úplného maloobchodního datového skladu Contoso](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md) z úložiště ukázek serveru Microsoft SQL Server.
Další tipy pro vývoj najdete v [tématu Rozhodnutí o návrhu a techniky kódování pro datové sklady](sql-data-warehouse-overview-develop.md).
