---
title: Kurz – načtení dat z Azure Data Lake Storage
description: Pomocí příkazu COPY načtěte data z Azure Data Lake Storage vyhrazených fondů SQL.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 16f95a86169be04eba202b311fc4437b204ec8b3
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566524"
---
# <a name="load-data-from-azure-data-lake-storage-into-dedicated-sql-pools-in-azure-synapse-analytics"></a>Načtení dat z Azure Data Lake Storage do vyhrazených fondů SQL ve službě Azure synapse Analytics

Tato příručka popisuje, jak pomocí [příkazu copy](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) načíst data z Azure Data Lake Storage. Rychlé příklady použití příkazu Kopírovat napříč všemi metodami ověřování najdete v následující dokumentaci: [bezpečné načtení dat pomocí vyhrazených fondů SQL](./quickstart-bulk-load-copy-tsql-examples.md).

> [!NOTE]  
> Pokud chcete poskytnout zpětnou vazbu nebo ohlásit problémy v příkazu COPY, odešlete e-mail na následující distribuční seznam: sqldwcopypreview@service.microsoft.com .
>
> [!div class="checklist"]
>
> * Vytvořte cílovou tabulku pro načtení dat z Azure Data Lake Storage.
> * Vytvořte příkaz COPY pro načtení dat do datového skladu.

Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="before-you-begin"></a>Než začnete

Než začnete s tímto kurzem, stáhněte a nainstalujte nejnovější verzi aplikace [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (SSMS).

Pro spuštění tohoto kurzu budete potřebovat:

* Vyhrazený fond SQL. Viz [vytvoření vyhrazeného fondu SQL a dat dotazů](create-data-warehouse-portal.md).
* Účet Data Lake Storage. Viz Začínáme [s Azure Data Lake Storage](../../data-lake-store/data-lake-store-get-started-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Pro tento účet úložiště budete muset nakonfigurovat nebo zadat jedno z následujících přihlašovacích údajů, které se mají načíst: klíč účtu úložiště, klíč sdíleného přístupového podpisu (SAS), uživatel aplikace Azure Directory nebo uživatel AAD, který má příslušnou roli Azure pro účet úložiště.

## <a name="create-the-target-table"></a>Vytvoření cílové tabulky

Připojte se k vyhrazenému fondu SQL a vytvořte cílovou tabulku, do které budete načítat. V tomto příkladu vytváříme tabulku dimenzí produktu.

```sql
-- A: Create the target table
-- DimProduct
CREATE TABLE [dbo].[DimProduct]
(
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL
)
WITH
(
    DISTRIBUTION = HASH([ProductKey]),
    CLUSTERED COLUMNSTORE INDEX
    --HEAP
);
```


## <a name="create-the-copy-statement"></a>Vytvoření příkazu COPY

Připojte se ke svému vyhrazenému fondu SQL a spusťte příkaz COPY. Úplný seznam příkladů najdete v následující dokumentaci: [bezpečné načtení dat pomocí vyhrazených fondů SQL](./quickstart-bulk-load-copy-tsql-examples.md).

```sql
-- B: Create and execute the COPY statement

COPY INTO [dbo].[DimProduct] 
--The column list allows you map, omit, or reorder input file columns to target table columns. 
--You can also specify the default value when there is a NULL value in the file.
--When the column list is not specified, columns will be mapped based on source and target ordinality
(
    ProductKey default -1 1,
    ProductLabel default 'myStringDefaultWhenNull' 2,
    ProductName default 'myStringDefaultWhenNull' 3
)
--The storage account location where you data is staged
FROM 'https://storageaccount.blob.core.windows.net/container/directory/'
WITH 
(
   --CREDENTIAL: Specifies the authentication method and credential access your storage account
   CREDENTIAL = (IDENTITY = '', SECRET = ''),
   --FILE_TYPE: Specifies the file type in your storage account location
   FILE_TYPE = 'CSV',
   --FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text (CSV) file
   FIELDTERMINATOR = '|',
   --ROWTERMINATOR: Marks the end of a record in the file
   ROWTERMINATOR = '0x0A',
   --FIELDQUOTE: Specifies the delimiter for data of type string in a delimited text (CSV) file
   FIELDQUOTE = '',
   ENCODING = 'UTF8',
   DATEFORMAT = 'ymd',
   --MAXERRORS: Maximum number of reject rows allowed in the load before the COPY operation is canceled
   MAXERRORS = 10,
   --ERRORFILE: Specifies the directory where the rejected rows and the corresponding error reason should be written
   ERRORFILE = '/errorsfolder',
) OPTION (LABEL = 'COPY: ADLS tutorial');
```

## <a name="optimize-columnstore-compression"></a>Optimalizace komprese columnstore

Ve výchozím nastavení jsou tabulky definovány jako clusterovaný index columnstore. Po dokončení načtení se některé řádky dat nemusí do columnstore komprimovat.  K tomu může dojít z nejrůznějších důvodů. Další informace najdete v tématu [Správa indexů columnstore](sql-data-warehouse-tables-index.md).

Pro optimalizaci výkonu dotazů a komprese columnstore po načtení znovu sestavte tabulku, aby index columnstore vynutil komprimaci všech řádků.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>Optimalizace statistik

Je nejlepší vytvořit statistiku s jedním sloupcem hned po načtení. K dispozici je několik možností pro statistiku. Pokud například vytvoříte statistiku s jedním sloupcem v každém sloupci, může opětovné sestavení všech statistik trvat dlouhou dobu. Pokud víte, že některé sloupce nejsou v predikátech dotazů, můžete na tyto sloupce přeskočit vytváření statistik.

Pokud se rozhodnete vytvořit statistiku s jedním sloupcem pro každý sloupec každé tabulky, můžete použít ukázku kódu uložené procedury `prc_sqldw_create_stats` v článku [Statistika](sql-data-warehouse-tables-statistics.md) .

Následující příklad je dobrým výchozím bodem pro vytváření statistik. Vytvoří statistiku s jedním sloupcem pro každý sloupec v tabulce dimenzí a pro každý sloupec spojování v tabulkách faktů. Můžete kdykoli přidat statistiku jednoho nebo více sloupců do dalších sloupců tabulky faktů.

## <a name="achievement-unlocked"></a>Úspěch je odemčený!

Úspěšně jste načetli data do svého datového skladu. Skvělá práce!

## <a name="next-steps"></a>Další kroky
Načítání dat je prvním krokem k vývoji řešení datového skladu pomocí Azure synapse Analytics. Podívejte se na naše vývojové prostředky.

> [!div class="nextstepaction"]
> [Naučte se vyvíjet tabulky pro datové sklady](sql-data-warehouse-tables-overview.md)

Další příklady a odkazy pro načítání naleznete v následující dokumentaci:
- [Dokumentace k příkazu COPY reference](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax)
- [ZKOPÍROVAT příklady pro každou metodu ověřování](./quickstart-bulk-load-copy-tsql-examples.md)
- [Rychlé spuštění kopírování pro jednu tabulku](./quickstart-bulk-load-copy-tsql.md)