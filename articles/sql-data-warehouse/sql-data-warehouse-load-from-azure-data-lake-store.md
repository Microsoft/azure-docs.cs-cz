---
title: 'Kurz: Načtení z Azure Data Lake Storage do služby Azure SQL Data Warehouse | Dokumentace Microsoftu'
description: Načtení dat ze služby Azure Data Lake Storage do Azure SQL Data Warehouse pomocí PolyBase externí tabulky.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/26/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 6b5083d6b4cf6758997e4e0551e5f3c2968a31c1
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65145967"
---
# <a name="load-data-from-azure-data-lake-storage-to-sql-data-warehouse"></a>Načtení dat ze služby Azure Data Lake Storage do SQL Data Warehouse
Načtení dat ze služby Azure Data Lake Storage do Azure SQL Data Warehouse pomocí PolyBase externí tabulky. I když můžete spouštět ad hoc dotazy s daty uloženými ve službě Data Lake Storage, doporučujeme importování dat do SQL Data Warehouse pro zajištění nejlepšího výkonu.

> [!div class="checklist"]
> * Vytváření databázových objektů, které jsou nutné k načtení z úložiště Data Lake.
> * Připojení k adresáři Data Lake Storage.
> * Načtení dat do Azure SQL Data Warehouse.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="before-you-begin"></a>Než začnete
Než začnete s tímto kurzem, stáhněte a nainstalujte nejnovější verzi aplikace [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

Ke spuštění v tomto kurzu, budete potřebovat:

* Azure Active Directory aplikace pro použití ověřování služba-služba, pokud načítáte z Gen1. Pokud chcete vytvořit, postupujte podle [ověřování služby Active directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)

>[!NOTE] 
> Pokud načítáte z Azure Data Lake úložiště Gen1, potřebujete ID klienta, klíče a OAuth 2.0 Token Endpoint hodnotu aplikace Active Directory pro připojení k účtu úložiště ze služby SQL Data Warehouse. Podrobnosti o tom, jak tyto hodnoty jsou v výše uvedený odkaz. Pro registrace Azure Active Directory aplikace pomocí ID aplikace jako ID klienta.
> 

* Službu Azure SQL Data Warehouse. Zobrazit [vytvořit a dotaz a Azure SQL Data Warehouse](create-data-warehouse-portal.md).

* Účet Data Lake Storage. Zobrazit [Začínáme s Azure Data Lake Storage](../data-lake-store/data-lake-store-get-started-portal.md). 

##  <a name="create-a-credential"></a>Vytvoření přihlašovacích údajů
Přístup ke svému účtu Data Lake Storage, musíte vytvořit hlavní klíč databáze k zašifrování vaší tajného kódu přihlašovacích údajů použít v dalším kroku. Pak vytvoříte Database Scoped Credential. Database Scoped Credential pro Gen1, uloží pověření instančního objektu služby nastavení v adresáři AAD. Klíč účtu úložiště musí používat v Database Scoped Credential pro Gen2. 

Pro připojení k Data Lake Storage Gen1, je nutné **první** vytvoření aplikace Azure Active Directory, vytvořte přístupový klíč a umožnit aplikaci přístup k Data Lake Storage Gen1 prostředku. Pokyny najdete v tématu [ověřování pro Azure Data Lake Storage Gen1 pomocí služby Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md).

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://msdn.microsoft.com/library/ms174382.aspx?f=255&MSPPError=-2147217396

CREATE MASTER KEY;


-- B (for Gen1): Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://msdn.microsoft.com/library/mt270260.aspx

CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    IDENTITY = '<client_id>@<OAuth_2.0_Token_EndPoint>',
    SECRET = '<key>'
;

-- B (for Gen2): Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.

CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;

-- It should look something like this for Gen1:
CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    IDENTITY = '536540b4-4239-45fe-b9a3-629f97591c0c@https://login.microsoftonline.com/42f988bf-85f1-41af-91ab-2d2cd011da47/oauth2/token',
    SECRET = 'BjdIlmtKp4Fpyh9hIvr8HJlUida/seM5kQ3EpLAmeDI='
;
```

## <a name="create-the-external-data-source"></a>Vytvoření externího zdroje dat.
Použijte tento [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql) příkaz k uložení umístění data. 

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
    LOCATION='abfs://<container>@<AzureDataLake account_name>.dfs.core.windows.net', -- Please note the abfs endpoint
    CREDENTIAL = ADLSCredential
);
```

## <a name="configure-data-format"></a>Konfigurovat formát dat
Pro import dat ze služby Data Lake Storage, budete muset zadat External File Format. Tento objekt definuje, jak soubory jsou zapsány v Data Lake Storage.
Úplný seznam najdete v naší dokumentaci jazyka T-SQL [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql)

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
Teď, když zadáte zdroj a soubor formátu data, jste připraveni k vytvoření externí tabulky. Externí tabulky, které se, jak pracovat s externími daty. Parametr umístění můžete určit soubor nebo adresář. Pokud Určuje adresář, se načtou všechny soubory v adresáři.

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

## <a name="external-table-considerations"></a>Důležité informace o externí tabulky
Vytvoření externí tabulky je jednoduché, ale existují nějaké drobné rozdíly, které je třeba být popsány.

Externí tabulky, které jsou silného typu. To znamená, že každý řádek dat ingestuje musí splňovat definice schématu tabulky.
Pokud řádek neodpovídá definici schématu, řádek zamítnuto zatížení.

Možnosti REJECT_TYPE a REJECT_VALUE umožňují definovat, kolik řádků nebo jaké procento dat musí být k dispozici v konečné tabulky. Během procesu načítání Pokud je dosaženo hodnoty odmítnout, zatížení se nezdaří. Nejčastější příčinou pozice zamítnutých řádků se neshodují definice schématu. Například pokud sloupec je nesprávně přiřazena schématu int, když data v souboru je řetězec, každý řádek se nepovedlo se načíst.

Data Lake Storage Gen1 používá k řízení přístupu k datům na základě řízení přístupu Role (RBAC). To znamená, že instanční objekt musí mít oprávnění ke čtení adresáře, které jsou definované v parametru umístění a podřízených prvků konečné adresáře a soubory. To umožňuje PolyBase k ověření a načíst data. 

## <a name="load-the-data"></a>Načtení dat
K načtení dat z Data Lake Storage použijte [CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) příkazu. 

Funkce CTAS vytvoří novou tabulku a naplní ji výsledky příkazu select. Funkce CTAS definuje novou tabulku pro mají stejné sloupce a datové typy jako výsledky příkazu select. Je-li vybrat všechny sloupce z externí tabulky, je nová tabulka repliky sloupců a datové typy v externí tabulky.

V tomto příkladu vytváříme distribuovaná zatřiďovací tabulka volat DimProduct z našich DimProduct_external externí tabulky.

```sql

CREATE TABLE [dbo].[DimProduct]
WITH (DISTRIBUTION = HASH([ProductKey]  ) )
AS
SELECT * FROM [dbo].[DimProduct_external]
OPTION (LABEL = 'CTAS : Load [dbo].[DimProduct]');
```


## <a name="optimize-columnstore-compression"></a>Optimalizace komprese columnstore
Ve výchozím nastavení SQL Data Warehouse ukládá jako clusterovaný index columnstore v tabulce. Po dokončení zátěžového některé řádky dat nemusí být komprimovaná do indexu columnstore.  Není k dispozici z různých důvodů, proč k tomu může dojít. Další informace najdete v tématu [spravovat indexy columnstore](sql-data-warehouse-tables-index.md).

Pokud chcete optimalizovat výkon dotazů a provádí kompresi columnstore po zatížení, tabulku sestavte v přinutit index columnstore tak, aby se komprimoval všechny řádky.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>Optimalizace statistiky
Doporučujeme vytvořit jednosloupcovou statistiku okamžitě po zatížení. Existuje několik možností pro statistiku. Například pokud vytvořit jednosloupcovou statistiku pro každý sloupec může trvat dlouhou dobu znovu sestavit všechny statistiky. Pokud víte, že některé sloupce se bude v predikátech dotazu, můžete přeskočit vytvoření statistiky pro tyto sloupce.

Pokud se rozhodnete vytvořit jednosloupcovou statistiku pro každý sloupec každé tabulky, můžete použít vzorek kódu uloženou proceduru `prc_sqldw_create_stats` v [statistiky](sql-data-warehouse-tables-statistics.md) článku.

V následujícím příkladu je dobrým výchozím bodem pro vytváření statistik. Vytvoří jednosloupcovou statistiku pro každý sloupec v tabulce dimenzí a pro každý sloupec spojovacího v tabulkách faktů. Můžete vždy přidat jeden nebo více sloupci statistiky ostatním sloupcům tabulky faktů později.

## <a name="achievement-unlocked"></a>Odemknout úspěch!
Úspěšně jste načetli data do Azure SQL Data Warehouse. Skvělá práce!

## <a name="next-steps"></a>Další postup 
V tomto kurzu jste vytvořili externí tabulky pro definici struktury dat uložených v Data Lake Storage Gen1 a pak použít příkazu PolyBase CREATE TABLE AS SELECT k načtení dat do datového skladu. 

Provedli jste tyto akce:
> [!div class="checklist"]
> * Vytvořené databázové objekty nezbytné k načtení z Data Lake Storage Gen1.
> * Připojení k adresáři Data Lake Storage Gen1.
> * Načtená data do Azure SQL Data Warehouse.
> 

Načítání dat je prvním krokem při vývoji řešení datového skladu, použití služby SQL Data Warehouse. Projděte si naše prostředky pro vývoj.

> [!div class="nextstepaction"]
>[Další informace jak vyvíjet tabulek v SQL Data Warehouse](sql-data-warehouse-tables-overview.md)




