---
title: Kurz se načítá z Azure Data Lake Storage do Azure SQL Data Warehouse | Microsoft Docs
description: Pomocí základních externích tabulek načtěte data z Azure Data Lake Storage do Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load-data
ms.date: 07/26/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 7bb775184a0d567fedf9da07cee60e5ba5a2097f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562373"
---
# <a name="load-data-from-azure-data-lake-storage-to-sql-data-warehouse"></a>Načtení dat z Azure Data Lake Storage do SQL Data Warehouse
Pomocí základních externích tabulek načtěte data z Azure Data Lake Storage do Azure SQL Data Warehouse. I když můžete spouštět dotazy ad hoc s daty uloženými v Data Lake Storage, doporučujeme data importovat do SQL Data Warehouse pro dosažení co nejvyššího výkonu.

> [!div class="checklist"]
> * Vytváření databázových objektů potřebných k načtení z Data Lake Storage.
> * Připojte se k adresáři Data Lake Storage.
> * Načte data do Azure SQL Data Warehouse.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="before-you-begin"></a>Před zahájením
Než začnete s tímto kurzem, stáhněte a nainstalujte nejnovější verzi aplikace [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

Pro spuštění tohoto kurzu budete potřebovat:

* Azure Active Directory aplikaci, která se má použít pro ověřování mezi službami Chcete-li vytvořit, postupujte podle [ověřování služby Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)

* Azure SQL Data Warehouse. Viz [Vytvoření a dotazování a Azure SQL Data Warehouse](create-data-warehouse-portal.md).

* Účet Data Lake Storage. Viz Začínáme [s Azure Data Lake Storage](../data-lake-store/data-lake-store-get-started-portal.md). 

##  <a name="create-a-credential"></a>Vytvoření přihlašovacích údajů
Pokud chcete získat přístup k účtu Data Lake Storage, budete muset vytvořit hlavní klíč databáze k šifrování tajného kódu přihlašovacích údajů, který jste použili v dalším kroku. Pak vytvoříte databázi s rozsahem pověření. Při ověřování pomocí instančních objektů ukládá přihlašovací údaje v oboru databáze nastavené přihlašovací údaje instančního objektu v AAD. Můžete také použít klíč účtu úložiště v databázi s rozsahem pověření pro Gen2. 

Pokud se chcete připojit k Data Lake Storage pomocí instančních objektů, musíte **nejdřív** vytvořit aplikaci Azure Active Directory, vytvořit přístupový klíč a udělit aplikaci přístup k účtu Data Lake Storage. Pokyny najdete v tématu [ověření pro Azure Data Lake Storage pomocí služby Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md).

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://msdn.microsoft.com/library/ms174382.aspx?f=255&MSPPError=-2147217396

CREATE MASTER KEY;


-- B (for service principal authentication): Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://msdn.microsoft.com/library/mt270260.aspx

CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    IDENTITY = '<client_id>@<OAuth_2.0_Token_EndPoint>',
    SECRET = '<key>'
;

-- B (for Gen2 storage key authentication): Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.

CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;

-- It should look something like this when authenticating using service principals:
CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    IDENTITY = '536540b4-4239-45fe-b9a3-629f97591c0c@https://login.microsoftonline.com/42f988bf-85f1-41af-91ab-2d2cd011da47/oauth2/token',
    SECRET = 'BjdIlmtKp4Fpyh9hIvr8HJlUida/seM5kQ3EpLAmeDI='
;
```

## <a name="create-the-external-data-source"></a>Vytvoření externího zdroje dat
Pomocí tohoto příkazu [Create External data source](/sql/t-sql/statements/create-external-data-source-transact-sql) uložte umístění dat. 

```sql
-- C (for Gen1): Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Storage.
-- LOCATION: Provide Data Lake Storage Gen1 account name and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'adl://<datalakestoregen1accountname>.azuredatalakestore.net',
    CREDENTIAL = ADLSCredential
);

-- C (for Gen2): Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Storage.
-- LOCATION: Provide Data Lake Storage Gen2 account name and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStorage
WITH (
    TYPE = HADOOP,
    LOCATION='abfs[s]://<container>@<AzureDataLake account_name>.dfs.core.windows.net', -- Please note the abfss endpoint for when your account has secure transfer enabled
    CREDENTIAL = ADLSCredential
);
```

## <a name="configure-data-format"></a>Konfigurace formátu dat
Chcete-li importovat data z Data Lake Storage, je nutné zadat formát externího souboru. Tento objekt definuje způsob, jakým jsou soubory zapisovány v Data Lake Storage.
Úplný seznam najdete v naší dokumentaci k T-SQL [Vytvoření formátu externího souboru](/sql/t-sql/statements/create-external-file-format-transact-sql) .

```sql
-- D: Create an external file format
-- FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text file
-- STRING_DELIMITER: Specifies the field terminator for data of type string in the text-delimited file.
-- DATE_FORMAT: Specifies a custom format for all date and time data that might appear in a delimited text file.
-- Use_Type_Default: Store missing values as default for datatype.

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
Teď, když jste zadali zdroj dat a formát souboru, jste připraveni vytvořit externí tabulky. Externí tabulky představují způsob interakce s externími daty. Parametr Location může určovat soubor nebo adresář. Pokud se určí adresář, načtou se všechny soubory v adresáři.

```sql
-- D: Create an External Table
-- LOCATION: Folder under the Data Lake Storage root folder.
-- DATA_SOURCE: Specifies which Data Source Object to use.
-- FILE_FORMAT: Specifies which File Format Object to use
-- REJECT_TYPE: Specifies how you want to deal with rejected rows. Either Value or percentage of the total
-- REJECT_VALUE: Sets the Reject value based on the reject type.

-- DimProduct
CREATE EXTERNAL TABLE [dbo].[DimProduct_external] (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL
)
WITH
(
    LOCATION='/DimProduct/'
,   DATA_SOURCE = AzureDataLakeStorage
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

```

## <a name="external-table-considerations"></a>Otázky k externí tabulce
Vytvoření externí tabulky je jednoduché, ale existuje několik drobné odlišnostiů, které je třeba projednávat.

Externí tabulky jsou silného typu. To znamená, že každý řádek zpracovávaných dat musí splňovat definici schématu tabulky.
Pokud řádek neodpovídá definici schématu, je řádek od načtení odmítnut.

Možnosti REJECT_TYPE a REJECT_VALUE umožňují definovat, kolik řádků nebo jaké procento dat musí být v konečné tabulce přítomné. Při načtení se při dosažení hodnoty odmítnutí zatížení nezdařilo. Nejběžnější příčinou zamítnutých řádků je neshoda definice schématu. Například pokud je sloupci nesprávně přiděleno schéma int, když jsou data v souboru řetězec, každý řádek se nepodaří načíst.

Data Lake Storage Gen1 používá k řízení přístupu k datům Access Control na základě rolí (RBAC). To znamená, že instanční objekt musí mít oprávnění ke čtení pro adresáře definované v parametru Location a podřízeným složkám finálního adresáře a souborů. To umožňuje, aby základ ověřil a načetl tato data. 

## <a name="load-the-data"></a>Načtení dat
Chcete-li načíst data z Data Lake Storage použijte příkaz [CREATE TABLE AS Select (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) . 

CTAS vytvoří novou tabulku a naplní ji výsledky příkazu SELECT. CTAS definuje novou tabulku, která bude mít stejné sloupce a datové typy jako výsledky příkazu SELECT. Pokud vyberete všechny sloupce z externí tabulky, je nová tabulka replikou sloupců a datových typů v externí tabulce.

V tomto příkladu vytvoříme distribuovanou tabulku hash s názvem DimProduct z našeho externího tabulkového DimProduct_external.

```sql

CREATE TABLE [dbo].[DimProduct]
WITH (DISTRIBUTION = HASH([ProductKey]  ) )
AS
SELECT * FROM [dbo].[DimProduct_external]
OPTION (LABEL = 'CTAS : Load [dbo].[DimProduct]');
```


## <a name="optimize-columnstore-compression"></a>Optimalizace komprese columnstore
Ve výchozím nastavení SQL Data Warehouse ukládá tabulku jako clusterovaný index columnstore. Po dokončení načtení se některé řádky dat nemusí do columnstore komprimovat.  K tomu může dojít z nejrůznějších důvodů. Další informace najdete v tématu [Správa indexů columnstore](sql-data-warehouse-tables-index.md).

Pro optimalizaci výkonu dotazů a komprese columnstore po načtení znovu sestavte tabulku, aby index columnstore vynutil komprimaci všech řádků.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>Optimalizace statistik
Je nejlepší vytvořit statistiku s jedním sloupcem hned po načtení. K dispozici je několik možností pro statistiku. Pokud například vytvoříte statistiku s jedním sloupcem v každém sloupci, může opětovné sestavení všech statistik trvat dlouhou dobu. Pokud víte, že některé sloupce nejsou v predikátech dotazů, můžete na tyto sloupce přeskočit vytváření statistik.

Pokud se rozhodnete vytvořit statistiku s jedním sloupcem pro každý sloupec každé tabulky, můžete použít ukázku `prc_sqldw_create_stats` kódu uložené procedury v článku [Statistika](sql-data-warehouse-tables-statistics.md) .

Následující příklad je dobrým výchozím bodem pro vytváření statistik. Vytvoří statistiku s jedním sloupcem pro každý sloupec v tabulce dimenzí a pro každý sloupec spojování v tabulkách faktů. Můžete kdykoli přidat statistiku jednoho nebo více sloupců do dalších sloupců tabulky faktů.

## <a name="achievement-unlocked"></a>Úspěch je odemčený!
Úspěšně jste načetli data do Azure SQL Data Warehouse. Skvělá práce!

## <a name="next-steps"></a>Další postup 
V tomto kurzu jste vytvořili externí tabulky, abyste definovali strukturu pro data uložená v Data Lake Storage Gen1 a pak jste použili základní CREATE TABLE jako příkaz SELECT pro načtení dat do datového skladu. 

Provedli jste tyto akce:
> [!div class="checklist"]
> * Byly vytvořeny objekty databáze požadované pro načtení z Data Lake Storage Gen1.
> * Připojeno k adresáři Data Lake Storage Gen1.
> * Data se načetla do Azure SQL Data Warehouse.
>

Načítání dat je prvním krokem k vývoji řešení datového skladu pomocí SQL Data Warehouse. Podívejte se na naše vývojové prostředky.

> [!div class="nextstepaction"]
> [Naučte se vyvíjet tabulky v SQL Data Warehouse](sql-data-warehouse-tables-overview.md)
