---
title: Kurz – načtení dat z Azure Data Lake Storage
description: Pomocí základních externích tabulek načtěte data z Azure Data Lake Storage SQL pro synapse.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/08/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 5935efca138d156507e2e3fefa65d045f618a57b
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84020825"
---
# <a name="load-data-from-azure-data-lake-storage-for-synapse-sql"></a>Načtení dat z Azure Data Lake Storage SQL pro synapse

Tato příručka popisuje, jak použít základní externí tabulky k načtení dat z Azure Data Lake Storage. I když můžete spouštět dotazy v režimu ad hoc na data uložená v Data Lake Storage, doporučujeme data importovat pro nejlepší výkon.

> [!NOTE]  
> Alternativou k nasazování je [příkaz Copy](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) , který je aktuálně ve verzi Public Preview.  Příkaz COPY poskytuje největší flexibilitu. Pokud chcete poskytnout zpětnou vazbu k příkazu COPY, odešlete e-mail na následující distribuční seznam: sqldwcopypreview@service.microsoft.com .
>
> [!div class="checklist"]
>
> * Vytváření databázových objektů potřebných k načtení z Data Lake Storage.
> * Připojte se k adresáři Data Lake Storage.
> * Načte data do datového skladu.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="before-you-begin"></a>Před zahájením

Než začnete s tímto kurzem, stáhněte a nainstalujte nejnovější verzi aplikace [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS).

Pro spuštění tohoto kurzu budete potřebovat:

* Fond SQL. Přečtěte si téma [Vytvoření fondu SQL a data dotazů](create-data-warehouse-portal.md).
* Účet Data Lake Storage. Viz Začínáme [s Azure Data Lake Storage](../../data-lake-store/data-lake-store-get-started-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Pro tento účet úložiště budete muset nakonfigurovat nebo zadat jedno z následujících přihlašovacích údajů, které se mají načíst: klíč účtu úložiště, uživatel aplikace Azure Directory nebo uživatel AAD, který má příslušnou roli RBAC pro účet úložiště.

## <a name="create-a-credential"></a>Vytvoření přihlašovacích údajů

Tuto část můžete přeskočit a při ověřování pomocí předávacího procesu AAD přejít k části Vytvoření externího zdroje dat. Přihlašovací údaje v oboru databáze není potřeba vytvořit nebo zadat při použití průchozího AAD, ale ujistěte se, že má uživatel AAD příslušnou roli RBAC (čtečka dat objektů BLOB úložiště, přispěvatel nebo role vlastníka) k účtu úložiště. Další podrobnosti jsou popsaných [tady](https://techcommunity.microsoft.com/t5/Azure-SQL-Data-Warehouse/How-to-use-PolyBase-by-authenticating-via-AAD-pass-through/ba-p/862260).

Pokud chcete získat přístup k účtu Data Lake Storage, budete muset vytvořit hlavní klíč databáze pro šifrování přihlašovacích údajů. Pak vytvoříte databázi s rozsahem pověření pro uložení tajného kódu. Při ověřování pomocí instančních objektů (uživatel Azure Directory Application) ukládá přihlašovací údaje v oboru databáze nastavené přihlašovací údaje instančního objektu v AAD. K uložení klíče účtu úložiště pro Gen2 můžete použít taky pověření s rozsahem databáze.

Pokud se chcete připojit k Data Lake Storage pomocí instančních objektů, musíte **nejdřív** vytvořit aplikaci Azure Active Directory, vytvořit přístupový klíč a udělit aplikaci přístup k účtu Data Lake Storage. Pokyny najdete v tématu [ověření pro Azure Data Lake Storage pomocí služby Active Directory](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

Přihlaste se ke svému fondu SQL s uživatelem, který má oprávnění na úrovni ovládacího prvku a spusťte následující příkazy SQL pro vaši databázi:

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest

CREATE MASTER KEY;


-- B (for service principal authentication): Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest

CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    -- Always use the OAuth 2.0 authorization endpoint (v1)
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

-- It should look something like this when authenticating using service principal:
CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    IDENTITY = '536540b4-4239-45fe-b9a3-629f97591c0c@https://login.microsoftonline.com/42f988bf-85f1-41af-91ab-2d2cd011da47/oauth2/token',
    SECRET = 'BjdIlmtKp4Fpyh9hIvr8HJlUida/seM5kQ3EpLAmeDI='
;
```

## <a name="create-the-external-data-source"></a>Vytvoření externího zdroje dat

Pomocí tohoto příkazu [Create External data source](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) uložte umístění dat. Pokud ověřujete průchozí průchod AAD, parametr PŘIHLAŠOVACÍch údajů se nevyžaduje. Pokud ověřujete pomocí spravované identity pro koncové body služby, použijte tuto [dokumentaci](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#azure-synapse-polybase) a nastavte externí zdroj dat.

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
Úplný seznam najdete v naší dokumentaci k T-SQL [Vytvoření formátu externího souboru](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) .

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

Chcete-li načíst data z Data Lake Storage použijte příkaz [CREATE TABLE AS Select (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) .

CTAS vytvoří novou tabulku a naplní ji výsledky příkazu SELECT. CTAS definuje novou tabulku, která bude mít stejné sloupce a datové typy jako výsledky příkazu SELECT. Pokud vyberete všechny sloupce z externí tabulky, je nová tabulka replikou sloupců a datových typů v externí tabulce.

V tomto příkladu vytvoříme distribuovanou tabulku hash s názvem DimProduct z naší externí tabulky DimProduct_external.

```sql

CREATE TABLE [dbo].[DimProduct]
WITH (DISTRIBUTION = HASH([ProductKey]  ) )
AS
SELECT * FROM [dbo].[DimProduct_external]
OPTION (LABEL = 'CTAS : Load [dbo].[DimProduct]');
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

V tomto kurzu jste vytvořili externí tabulky, abyste definovali strukturu pro data uložená v Data Lake Storage Gen1 a pak jste použili základní CREATE TABLE jako příkaz SELECT pro načtení dat do datového skladu.

Provedli jste tyto akce:
> [!div class="checklist"]
>
> * Byly vytvořeny objekty databáze požadované pro načtení z Data Lake Storage.
> * Připojeno k adresáři Data Lake Storage.
> * Data byla načtena do datového skladu.
>

Načítání dat je prvním krokem k vývoji řešení datového skladu pomocí Azure synapse Analytics. Podívejte se na naše vývojové prostředky.

> [!div class="nextstepaction"]
> [Naučte se vyvíjet tabulky pro datové sklady](sql-data-warehouse-tables-overview.md)
