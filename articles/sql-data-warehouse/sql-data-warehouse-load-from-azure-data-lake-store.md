---
title: 'Kurz: Načíst z Azure Data Lake Store k Azure SQL Data Warehouse | Microsoft Docs'
description: Načtení dat z Azure Data Lake Store do Azure SQL Data Warehouse pomocí PolyBase externí tabulky.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/12/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 3c6907e8eb4ae4bbfae76a5a220d670427afd703
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="load-data-from-azure-data-lake-store-to-sql-data-warehouse"></a>Načtení dat z Azure Data Lake Store k SQL Data Warehouse
Načtení dat z Azure Data Lake Store do Azure SQL Data Warehouse pomocí PolyBase externí tabulky. I když můžete spustit ad hoc dotazy na data uložená v ADLS, doporučujeme importu dat do SQL Data Warehouse pro nejlepší výkon.

> [!div class="checklist"]
> * Vytvořte databázové objekty nezbytné načítat z Azure Data Lake Store.
> * Připojení k adresáři Azure Data Lake Store.
> * Načtení dat do Azure SQL Data Warehouse.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="before-you-begin"></a>Než začnete
Než začnete s tímto kurzem, stáhněte a nainstalujte nejnovější verzi aplikace [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

Chcete-li spustit tento kurz, je třeba:

* Azure Active Directory aplikace bude používat pro ověření Service-to-Service. Pokud chcete vytvořit, postupujte podle [ověřování služby Active directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)

>[!NOTE] 
> Potřebujete ID klienta, klíč a hodnotu tokenu koncového bodu OAuth2.0 Active Directory aplikace pro připojení k vaší Azure Data Lake z SQL Data Warehouse. Podrobnosti o tom, jak získat tyto hodnoty jsou ve výše uvedený odkaz. Pro Azure Active Directory aplikace registrace pomocí ID aplikace jako ID klienta.
> 

* Azure SQL Data Warehouse. V tématu [vytvořit a dotaz a Azure SQL Data Warehouse](create-data-warehouse-portal.md).

* Azure Data Lake Store. V tématu [Začínáme s Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md). 

##  <a name="create-a-credential"></a>Vytvoření přihlašovacích údajů
Pro přístup k Azure Data Lake Store, musíte vytvořit hlavní klíč databáze pro zašifrování váš tajný klíč pověření použít v dalším kroku. Pak vytvořte obor přihlašovací údaje databáze, která ukládá hlavní přihlašovací údaje služby nastavit v AAD. Pro ty z vás kdo PolyBase použili pro připojení k systému Windows Azure Storage Blobs syntaxe přihlašovacích údajů se liší.

Abyste mohli připojit k Azure Data Lake Store, musíte **první** vytvoření aplikace Azure Active Directory, vytvořte přístupový klíč a udělit aplikaci přístup k prostředku Azure Data Lake. Pokyny najdete v tématu [ověřit do Azure Data Lake Store pomocí služby Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md).

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://msdn.microsoft.com/en-us/library/ms174382.aspx?f=255&MSPPError=-2147217396

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://msdn.microsoft.com/en-us/library/mt270260.aspx

CREATE DATABASE SCOPED CREDENTIAL ADLCredential
WITH
    IDENTITY = '<client_id>@<OAuth_2.0_Token_EndPoint>',
    SECRET = '<key>'
;

-- It should look something like this:
CREATE DATABASE SCOPED CREDENTIAL ADLCredential
WITH
    IDENTITY = '536540b4-4239-45fe-b9a3-629f97591c0c@https://login.microsoftonline.com/42f988bf-85f1-41af-91ab-2d2cd011da47/oauth2/token',
    SECRET = 'BjdIlmtKp4Fpyh9hIvr8HJlUida/seM5kQ3EpLAmeDI='
;
```

## <a name="create-the-external-data-source"></a>Vytvoření externího zdroje dat.
Použít [vytvořit externí zdroj dat](/sql/t-sql/statements/create-external-data-source-transact-sql) příkazu umístění dat úložiště. 

```sql
-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Store.
-- LOCATION: Provide Azure Data Lake accountname and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
WITH (
    TYPE = HADOOP,
    LOCATION = 'adl://<AzureDataLake account_name>.azuredatalakestore.net',
    CREDENTIAL = ADLCredential
);
```

## <a name="configure-data-format"></a>Konfigurovat formát dat
Pro import dat z ADLS, budete muset zadat External File Format. Tento objekt definuje, jak tyto soubory jsou zapsány v ADLS.
Úplný seznam najdete v dokumentaci T-SQL [vytvořit EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql)

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

## <a name="create-the-external-tables"></a>Vytvoření externí tabulky
Formát dat zdroje a soubor jste zadali, jste připraveni k vytvoření externí tabulky. Externí tabulky se, jak pracovat s externí data. Parametr umístění můžete určit soubor nebo adresář. Pokud Určuje adresář, budou načteny všechny soubory v adresáři.

```sql
-- D: Create an External Table
-- LOCATION: Folder under the ADLS root folder.
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
,   DATA_SOURCE = AzureDataLakeStore
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

```

## <a name="external-table-considerations"></a>Aspekty externí tabulky
Vytvoření externí tabulky je jednoduché, ale existují některé drobné odlišnosti, které musí být popsané.

Externí tabulky jsou silného typu. To znamená, že každý řádek dat probíhá požity musí splňovat definice schématu tabulky.
Pokud řádek neodpovídá definici schématu, řádek byl odmítnut z zatížení.

Možnosti REJECT_TYPE a REJECT_VALUE umožňují definovat, kolik řádků nebo jaké procento dat musí být v posledním tabulce. Během procesu načítání Pokud je dosaženo hodnoty odmítněte, zatížení se nezdaří. Nejčastější příčinou odmítnutých řádků je neshody definice schématu. Například pokud sloupec je nesprávně zadána schéma int, když jsou data v souboru řetězec, každý řádek nebude možné načíst.

 Azure Data Lake store využívá k řízení přístupu k datům na základě řízení přístupu Role (RBAC). To znamená, že objekt služby musí mít oprávnění ke čtení adresáře definované v parametru umístění a podřízené objekty daného konečné adresář a soubory. To umožňuje PolyBase k ověření a načíst data. 

## <a name="load-the-data"></a>Načtení dat
Načtení dat z Azure Data Lake Store pomocí [CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) příkaz. 

Funkce CTAS vytvoří novou tabulku a naplní s výsledky příkazu select. Funkce CTAS definuje novou tabulku tak, aby měl stejné sloupce a typy dat jako výsledky příkazu select. Pokud vyberete všechny sloupce z externí tabulky, je nová tabulka repliku sloupce a typy dat v externí tabulky.

V tomto příkladu vytváříme zatřiďovací tabulku distribuované volat DimProduct z našich DimProduct_external externí tabulky.

```sql

CREATE TABLE [dbo].[DimProduct]
WITH (DISTRIBUTION = HASH([ProductKey]  ) )
AS
SELECT * FROM [dbo].[DimProduct_external]
OPTION (LABEL = 'CTAS : Load [dbo].[DimProduct]');
```


## <a name="optimize-columnstore-compression"></a>Optimalizace columnstore komprese
Ve výchozím nastavení SQL Data Warehouse ukládá jako clusterovaný index columnstore v tabulce. Po dokončení zatížení některé řádky dat nemusí být komprimovány do columnstore.  Je z různých důvodů, proč k tomu může dojít. Další informace najdete v tématu [spravovat indexy columnstore](sql-data-warehouse-tables-index.md).

Chcete-li optimalizovat výkon dotazů a komprese columnstore po zatížení, znovu sestavte vynutit index columnstore komprimovat všechny řádky v tabulce.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>Optimalizace statistiky
Je vhodné vytvořit jednosloupcovou statistiku okamžitě po zatížení. Existuje několik možností pro statistiky. Například pokud vytvoříte jednosloupcovou statistiku pro každý sloupec může trvat dlouhou dobu znovu vytvořit všechny statistiky. Pokud víte, že některé sloupce nejsou má být v predikátech dotazu, můžete přeskočit vytvoření statistiky pro tyto sloupce.

Pokud se rozhodnete vytvořit jednosloupcovou statistiku pro každý sloupec každé tabulky, můžete použít ukázka kódu uložené procedury `prc_sqldw_create_stats` v [statistiky](sql-data-warehouse-tables-statistics.md) článku.

V následujícím příkladu je to dobrý výchozí bod pro vytvoření statistiky. Vytvoří jednosloupcovou statistiku pro každý sloupec v tabulce dimenze a pro každý sloupec spojující v tabulkách faktů. Vždy přidáním statistiky jeden nebo více sloupců do ostatních sloupců tabulky faktů později.

## <a name="achievement-unlocked"></a>Dosažení odemčený!
Úspěšně jste načetli data do Azure SQL Data Warehouse. Skvělá práce!

## <a name="next-steps"></a>Další postup 
V tomto kurzu vytvořili externí tabulky můžete definovat strukturu dat uložených v Azure Data Lake Store a pak použít příkaz PolyBase CREATE TABLE AS SELECT k načtení dat do datového skladu. 

Provedli jste tyto akce:
> [!div class="checklist"]
> * Vytvoření databázové objekty nezbytné načítat z Azure Data Lake Store.
> * Připojení k adresář služby Azure Data Lake Store.
> * Načíst data do Azure SQL Data Warehouse.
> 

Načítání dat je prvním krokem k vývoji řešení datového skladu pomocí SQL Data Warehouse. Podívejte se na naše vývoj prostředky.

> [!div class="nextstepaction"]
>[Další informace jak vyvíjet tabulek v SQL Data Warehouse](sql-data-warehouse-tables-overview.md)




