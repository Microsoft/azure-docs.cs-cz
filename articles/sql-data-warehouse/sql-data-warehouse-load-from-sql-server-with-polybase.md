---
title: "Načtení dat z SQL Serveru do Azure SQL Data Warehouse (PolyBase) | Dokumentace Microsoftu"
description: "Využívá bcp k exportu dat z SQL Serveru do plochých souborů, AZCopy k importu dat do Azure Blob Storage a PolyBase ke zpracování příjmu dat do Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 860c86e0-90f7-492c-9a84-1bdd3d1735cd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: barbkess
ms.openlocfilehash: f8c251fb9b8275eeea2940f8856669d2ef9f3549
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2017
---
# <a name="load-data-with-polybase-in-sql-data-warehouse"></a>Načtení dat pomocí funkce PolyBase v SQL Data Warehouse
> [!div class="op_single_selector"]
> * [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
> * [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
> * [bcp](sql-data-warehouse-load-from-sql-server-with-bcp.md)
> 
> 

Tento kurz ukazuje, jak načíst data do SQL Data Warehouse s použitím AzCopy a PolyBase. Po dokončení tohoto kurzu budete vědět, jak:

* Pomocí AzCopy kopírovat data do Azure Blob Storage
* Vytvářením databázových objektů definovat data
* Spuštěním dotazu T-SQL načítat data

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Loading-data-with-PolyBase-in-Azure-SQL-Data-Warehouse/player]
> 
> 

## <a name="prerequisites"></a>Požadavky
Pro jednotlivé kroky v tomto kurzu budete potřebovat

* Databázi SQL Data Warehouse
* Účet úložiště Azure typu Standard Locally Redundant Storage (Standard-LRS), Standard Geo-Redundant Storage (Standard-GRS) nebo Standard Read-Access Geo-Redundant Storage (Standard-RAGRS)
* Nástroj příkazového řádku AzCopy. Stáhněte a nainstalujte si [nejnovější verzi AzCopy][latest version of AzCopy], která se instaluje s nástroji Microsoft Azure Storage Tools.
  
    ![Nástroje Azure Storage Tools](./media/sql-data-warehouse-get-started-load-with-polybase/install-azcopy.png)

## <a name="step-1-add-sample-data-to-azure-blob-storage"></a>Krok 1: Přidání ukázkových dat do Azure Blob Storage
Aby bylo možné data načíst, musíme vložit nějaká ukázková data do Azure Blob Storage. V tomto kroku naplníme objekt blob úložiště Azure ukázkovými daty. Později pomocí funkce PolyBase načteme tato ukázková data do vaší databáze SQL Data Warehouse.

### <a name="a-prepare-a-sample-text-file"></a>A. Příprava ukázkového textového souboru
Ukázkový textový soubor připravíte takto:

1. Otevřete Poznámkový blok a zkopírujte následující řádky dat do nového souboru. Uložte zkopírované řádky do místního dočasného (temp) adresáře do souboru % temp%\DimDate2.txt.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

### <a name="b-find-your-blob-service-endpoint"></a>B. Vyhledání vašeho koncového bodu Služby objektů blob
Vyhledání vašeho koncového bodu Služby objektů blob:

1. Na portálu Azure vyberte **Procházet** > **Účty úložiště**.
2. Klikněte na účet úložiště, který chcete použít.
3. V okně Účet úložiště klikněte na Objekty blob.
   
    ![Klikněte na Objekty blob.](./media/sql-data-warehouse-get-started-load-with-polybase/click-blobs.png)
4. Uložte si adresu URL koncového bodu Služby objektů blob pro pozdější použití.
   
    ![Koncový bod Služby objektů blob](./media/sql-data-warehouse-get-started-load-with-polybase/blob-service.png)

### <a name="c-find-your-azure-storage-key"></a>C. Vyhledání klíče účtu úložiště Azure
Vyhledání klíče účtu úložiště Azure:

1. Na portálu Azure vyberte **Procházet** > **Účty úložiště**.
2. Klikněte na účet úložiště, který chcete použít.
3. Vyberte **Všechna nastavení** > **Přístupové klíče**.
4. Kliknutím na políčko pro kopírování si zkopírujte jeden ze svých přístupových klíčů do schránky.
   
    ![Zkopírování klíče úložiště Azure](./media/sql-data-warehouse-get-started-load-with-polybase/access-key.png)

### <a name="d-copy-the-sample-file-to-azure-blob-storage"></a>D. Zkopírování ukázkového souboru do Azure Blob Storage
Zkopírování vašich dat do Azure Blob Storage:

1. Otevřete příkazový řádek a změňte adresář na instalační adresář AzCopy. Tento příkaz změní adresář na výchozí instalační adresář na 64bitovém klientovi Windows.
   
    ```
    cd /d "%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy"
    ```
2. Spuštěním následujícího příkazu nahrajte soubor: Zadejte svoji adresu URL koncového bodu Služby objektů blob pro <blob service endpoint URL> a klíč účtu úložiště Azure pro <klíč_účtu_úložiště_azure>.
   
    ```
    .\AzCopy.exe /Source:C:\Temp\ /Dest:<blob service endpoint URL> /datacontainer/datedimension/ /DestKey:<azure_storage_account_key> /Pattern:DimDate2.txt
    ```

Viz také [Začínáme s nástrojem příkazového řádku AzCopy][latest version of AzCopy].

### <a name="e-explore-your-blob-storage-container"></a>E. Prozkoumání vašeho kontejneru úložiště objektů blob
Zobrazení souboru, který jste nahráli do úložiště objektů blob:

1. Přejděte zpět do okna vaší Služby objektů blob.
2. V části Kontejnery poklikejte na **datacontainer**.
3. Pokud chcete prozkoumat cestu ke svým datům, klikněte na složku **datedimension**. Zobrazí se nahraný soubor **DimDate2.txt**.
4. Pokud chcete zobrazit vlastnosti, klikněte na **DimDate2.txt**.
5. Poznámka: V okně vlastností objektu blob můžete stáhnout nebo odstranit soubor.
   
    ![Zobrazení objektu blob úložiště Azure](./media/sql-data-warehouse-get-started-load-with-polybase/view-blob.png)

## <a name="step-2-create-an-external-table-for-the-sample-data"></a>Krok 2: Vytvoření externí tabulky pro ukázková data
V této části vytvoříme externí tabulku, která definuje ukázková data.

Funkce PolyBase používá pro přístup k datům v Azure Blob Storage externí tabulky. Vzhledem k tomu, že data nejsou uložená v SQL Data Warehouse, zpracovává PolyBase ověřování pro externí data pomocí přihlašovacích údajů platných pro databázi.

V příkladě v tomto kroku se k vytvoření externí tabulky používají následující příkazy jazyka Transact-SQL.

* [Create Master Key (Transact-SQL)][Create Master Key (Transact-SQL)] k šifrování tajného klíče vašich přihlašovacích údajů pro vaši databázi
* [Create Database Scoped Credential (Transact-SQL)][Create Database Scoped Credential (Transact-SQL)] k zadání ověřovacích informací pro váš účet úložiště Azure
* [Create External Data Source (Transact-SQL)][Create External Data Source (Transact-SQL)] k určení umístění vaší služby Azure Blob Storage
* [Create External File Format (Transact-SQL)][Create External File Format (Transact-SQL)] k určení formátu vašich dat
* [Create External Table (Transact-SQL)][Create External Table (Transact-SQL)] k určení definice tabulky a umístění dat

Spusťte tento dotaz na databázi SQL Data Warehouse. Ten ve schématu dbo vytvoří externí tabulku s názvem DimDate2External, která odkazuje na ukázková data DimDate2.txt v Azure Blob Storage.

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


-- D: Create an external file format
-- FORMAT_TYPE: Type of file format in Azure storage (supported: DELIMITEDTEXT, RCFILE, ORC, PARQUET).
-- FORMAT_OPTIONS: Specify field terminator, string delimiter, date format etc. for delimited text files.
-- Specify DATA_COMPRESSION method if data is compressed.

CREATE EXTERNAL FILE FORMAT TextFile
WITH (
    FORMAT_TYPE = DelimitedText,
    FORMAT_OPTIONS (FIELD_TERMINATOR = ',')
);


-- E: Create the external table
-- Specify column names and data types. This needs to match the data in the sample file.
-- LOCATION: Specify path to file or directory that contains the data (relative to the blob container).
-- To point to all files under the blob container, use LOCATION='.'

CREATE EXTERNAL TABLE dbo.DimDate2External (
    DateId INT NOT NULL,
    CalendarQuarter TINYINT NOT NULL,
    FiscalQuarter TINYINT NOT NULL
)
WITH (
    LOCATION='/datedimension/',
    DATA_SOURCE=AzureStorage,
    FILE_FORMAT=TextFile
);


-- Run a query on the external table

SELECT count(*) FROM dbo.DimDate2External;

```


V Průzkumníku objektů systému SQL Server v sadě Visual Studio uvidíte formát externích souborů, externí zdroj dat a tabulku DimDate2External.

![Zobrazení externí tabulky](./media/sql-data-warehouse-get-started-load-with-polybase/external-table.png)

## <a name="step-3-load-data-into-sql-data-warehouse"></a>Krok 3: Načtení dat do SQL Data Warehouse
Po vytvoření externí tabulky můžete buď načíst data do nové tabulky, nebo je vložit do existující tabulky.

* Pokud chcete načíst data do nové tabulky, spusťte příkaz [CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)]. Nová tabulka bude mít sloupce pojmenované v dotazu. Datové typy sloupců budou odpovídat datovým typům v definici externí tabulky.
* Pokud chcete načíst data do existující tabulky, použijte příkaz [INSERT...SELECT (Transact-SQL)][INSERT...SELECT (Transact-SQL)].

```sql
-- Load the data from Azure blob storage to SQL Data Warehouse

CREATE TABLE dbo.DimDate2
WITH
(   
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT * FROM [dbo].[DimDate2External];
```

## <a name="step-4-create-statistics-on-your-newly-loaded-data"></a>Krok 4: Vytvoření statistiky pro nově načtená data
SQL Data Warehouse nevytváří ani neaktualizuje statistiku automaticky. Pro dosažení vysokého výkonu dotazu je proto důležité vytvořit statistiku pro každý sloupec každé tabulky po prvním načtení. Důležité je také aktualizovat statistiku po důležitých změnách v datech.

Tento příklad vytvoří jednosloupcovou statistiku pro novou tabulku DimDate2.

```sql
CREATE STATISTICS [DateId] on [DimDate2] ([DateId]);
CREATE STATISTICS [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
CREATE STATISTICS [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
```

Další informace viz [Statistika][Statistics].  

## <a name="next-steps"></a>Další kroky
Projděte si [průvodce funkcí PolyBase][PolyBase guide], kde najdete další informace, které byste měli mít, když budete vyvíjet řešení využívající funkci PolyBase.

<!--Image references-->


<!--Article references-->
[PolyBase in SQL Data Warehouse Tutorial]: ./sql-data-warehouse-get-started-load-with-polybase.md
[Load data with bcp]: ./sql-data-warehouse-load-with-bcp.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[PolyBase guide]: ./sql-data-warehouse-load-polybase-guide.md
[latest version of AzCopy]:../storage/common/storage-use-azcopy.md

<!--External references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]:https://msdn.microsoft.com/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]:https://msdn.microsoft.com/library/dn935026.aspx
[CREATE EXTERNAL TABLE (Transact-SQL)]:https://msdn.microsoft.com/library/dn935021.aspx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]:https://msdn.microsoft.com/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]:https://msdn.microsoft.com/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]:https://msdn.microsoft.com/library/mt130698.aspx

[CREATE TABLE AS SELECT (Transact-SQL)]:https://msdn.microsoft.com/library/mt204041.aspx
[INSERT...SELECT (Transact-SQL)]:https://msdn.microsoft.com/library/ms174335.aspx
[CREATE MASTER KEY (Transact-SQL)]:https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]:https://msdn.microsoft.com/library/ms189522.aspx
[CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)]:https://msdn.microsoft.com/library/mt270260.aspx
[DROP CREDENTIAL (Transact-SQL)]:https://msdn.microsoft.com/library/ms189450.aspx
