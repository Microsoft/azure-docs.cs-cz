---
title: Kurz zatížení dat z Úložiště datových jezer Azure
description: Externí tabulky PolyBase slouží k načtení dat z Azure Data Lake Storage pro Synapse SQL.
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
ms.openlocfilehash: aeaa95090dc40c6e161f0c336c43032a50e5aad0
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983564"
---
# <a name="load-data-from-azure-data-lake-storage-for-sql-analytics"></a>Načítání dat z Azure Data Lake Storage pro SQL Analytics

Tato příručka popisuje, jak používat externí tabulky PolyBase k načtení dat z Azure Data Lake Storage. I když můžete spouštět adhoc dotazy na data uložená v úložišti datového jezera, doporučujeme importovat data pro nejlepší výkon.

> [!NOTE]  
> Alternativou k načtení je [příkaz COPY,](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) který je aktuálně ve verzi Public Preview.  Příkaz COPY poskytuje největší flexibilitu. Chcete-li poskytnout zpětnou vazbu k příkazu sqldwcopypreview@service.microsoft.comCOPY, odešlete e-mail do následujícího distribučního seznamu: .
>
> [!div class="checklist"]
>
> * Vytvořte databázové objekty potřebné k načtení z úložiště datového jezera.
> * Připojte se k adresáři Úložiště datového jezera.
> * Načtení dat do datového skladu.

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="before-you-begin"></a>Než začnete

Než začnete s tímto kurzem, stáhněte a nainstalujte nejnovější verzi aplikace [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS).

Chcete-li spustit tento kurz, potřebujete:

* Fond SQL. Viz [Vytvoření fondu SQL a dat dotazů](create-data-warehouse-portal.md).
* Účet úložiště datového jezera. Viz [Začínáme s Azure Data Lake Storage](../../data-lake-store/data-lake-store-get-started-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Pro tento účet úložiště budete muset nakonfigurovat nebo zadat jedno z následujících přihlašovacích údajů k načtení: klíč účtu úložiště, uživatel aplikace Adresář Azure nebo uživatel AAD, který má odpovídající roli RBAC pro účet úložiště.

## <a name="create-a-credential"></a>Vytvoření pověření

Tuto část můžete přeskočit a při ověřování pomocí předávací ho souboru AAD přejít k části "Vytvořit externí zdroj dat". Pověření s rozsahem databáze není nutné vytvořit nebo zadat při použití předávací ho dorážky AAD, ale ujistěte se, že váš uživatel AAD má příslušnou roli RBAC (čtečka dat objektů blob úložiště, přispěvatel nebo role vlastníka) pro účet úložiště. Více informací je uvedeno [zde](https://techcommunity.microsoft.com/t5/Azure-SQL-Data-Warehouse/How-to-use-PolyBase-by-authenticating-via-AAD-pass-through/ba-p/862260).

Chcete-li získat přístup k účtu úložiště datového jezera, budete muset vytvořit hlavní klíč databáze k šifrování tajného klíče pověření. Potom vytvořit pověření s rozsahem databáze pro uložení tajného klíče. Při ověřování pomocí instančních objektů (uživatel aplikace adresáře Azure) ukládá pověření oboru databáze pověření instančního objektu nastavené v aad. Pověření oboru databáze můžete také použít k uložení klíče účtu úložiště pro Gen2.

Chcete-li se připojit k úložišti Data Lake Storage pomocí instančních objektů, musíte **nejprve** vytvořit aplikaci Azure Active Directory, vytvořit přístupový klíč a udělit aplikaci přístup k účtu úložiště datového jezera. Pokyny najdete [v tématu Ověření úložiště datových jezer Azure pomocí služby Active Directory](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

Přihlaste se do fondu SQL s uživatelem s oprávněními na úrovni CONTROL a spusťte následující příkazy SQL proti databázi:

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

Pomocí tohoto příkazu [VYTVOŘIT EXTERNÍ ZDROJ DAT](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) uložte umístění dat. Pokud ověřujete s předávací masou, parametr CREDENTIAL není vyžadován. Pokud ověřujete pomocí spravované identity pro koncové body služby, postupujte podle této [dokumentace](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#azure-sql-data-warehouse-polybase) k nastavení externího zdroje dat. 

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

Chcete-li importovat data z úložiště datových jezer, musíte zadat externí formát souboru. Tento objekt definuje, jak jsou soubory zapsány v úložišti datového jezera.
Kompletní seznam nazpřete v naší t-SQL dokumentaci [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

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

Nyní, když jste zadali zdroj dat a formát souboru, jste připraveni vytvořit externí tabulky. Externí tabulky jsou způsob interakce s externími daty. Parametr umístění může určit soubor nebo adresář. Pokud určuje adresář, budou načteny všechny soubory v adresáři.

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

## <a name="external-table-considerations"></a>Důležité informace o externí tabulce

Vytvoření externí tabulky je snadné, ale existují některé nuance, které je třeba diskutovat.

Externí tabulky jsou silně zadávány. To znamená, že každý řádek pojídaných dat musí splňovat definici schématu tabulky.
Pokud řádek neodpovídá definici schématu, řádek je odmítnut ze zatížení.

Možnosti REJECT_TYPE a REJECT_VALUE umožňují definovat, kolik řádků nebo jaké procento dat musí být přítomno v konečné tabulce. Během načítání, pokud je dosaženo hodnoty odmítnutí, zatížení se nezdaří. Nejčastější příčinou odmítnutých řádků je neshoda definice schématu. Například pokud sloupec je nesprávně uvedeny schéma int při data v souboru je řetězec, každý řádek se nezdaří načíst.

Data Lake Storage Gen1 používá řízení přístupu na základě rolí (RBAC) k řízení přístupu k datům. To znamená, že instanční objekt musí mít oprávnění ke čtení adresářů definovaných v parametru umístění a pro podřízené objekty konečného adresáře a souborů. To umožňuje PolyBase k ověření a načtení těchto dat.

## <a name="load-the-data"></a>Načtení dat

Chcete-li načíst data z úložiště datových jezer, použijte příkaz [CREATE TABLE AS SELECT (Transact-SQL).](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

CTAS vytvoří novou tabulku a naplní ji výsledky příkazu select. CTAS definuje novou tabulku mít stejné sloupce a datové typy jako výsledky příkazu select. Pokud vyberete všechny sloupce z externí tabulky, nová tabulka bude replikou sloupců a datových typů v externí tabulce.

V tomto příkladu vytváříme distribuovanou tabulku hash s názvem DimProduct z naší externí tabulky DimProduct_external.

```sql

CREATE TABLE [dbo].[DimProduct]
WITH (DISTRIBUTION = HASH([ProductKey]  ) )
AS
SELECT * FROM [dbo].[DimProduct_external]
OPTION (LABEL = 'CTAS : Load [dbo].[DimProduct]');
```

## <a name="optimize-columnstore-compression"></a>Optimalizace komprese columnstore

Ve výchozím nastavení jsou tabulky definovány jako clusterovaný index columnstore. Po dokončení zatížení některé řádky dat nemusí být komprimovány do columnstore.  Existuje celá řada důvodů, proč se to může stát. Další informace naleznete v [tématu správa indexů columnstore](sql-data-warehouse-tables-index.md).

Chcete-li optimalizovat výkon dotazu a kompresi columnstore po zatížení, znovu sestavit tabulku vynutit columnstore index komprimovat všechny řádky.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>Optimalizace statistik

Nejlepší je vytvořit statistiku s jedním sloupcem ihned po zatížení. Existuje několik možností pro statistiky. Pokud například vytvoříte statistiku jednoho sloupce pro každý sloupec, může trvat dlouho, než se všechny statistiky znovu vytvoří. Pokud víte, že některé sloupce nebudou v predikátech dotazů, můžete přeskočit vytváření statistik v těchto sloupcích.

Pokud se rozhodnete vytvořit statistiku jednoho sloupce na každém sloupci každé `prc_sqldw_create_stats` tabulky, můžete použít ukázku kódu uložené procedury v článku [statistiky.](sql-data-warehouse-tables-statistics.md)

Následující příklad je dobrým výchozím bodem pro vytváření statistik. Vytvoří jednosloupcové statistiky pro každý sloupec v tabulce dimenzí a na každém spojovacím sloupci v tabulkách faktů. Statistiky s jedním nebo více sloupci můžete vždy přidat do jiných sloupců tabulky faktů později.

## <a name="achievement-unlocked"></a>Úspěch odemčený!

Úspěšně jste načetli data do datového skladu. Skvělá práce!

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili externí tabulky k definování struktury pro data uložená v Datové masce úložiště dat Gen1 a potom jste použili příkaz PolyBase CREATE TABLE AS SELECT k načtení dat do datového skladu.

Provedli jste tyto akce:
> [!div class="checklist"]
>
> * Vytvořené databázové objekty potřebné k načtení z úložiště datového jezera.
> * Připojeno k adresáři Úložiště datového jezera.
> * Načte data do datového skladu.
>

Načítání dat je prvním krokem k vývoji řešení datového skladu pomocí Azure Synapse Analytics. Podívejte se na naše vývojové zdroje.

> [!div class="nextstepaction"]
> [Naučte se vyvíjet tabulky pro ukládání dat](sql-data-warehouse-tables-overview.md)
