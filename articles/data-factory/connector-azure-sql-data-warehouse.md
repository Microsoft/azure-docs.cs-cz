---
title: Kopírování dat do a z Azure SQL Data Warehouse pomocí Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data z podporované zdrojové úložiště do služby Azure SQL Data Warehouse nebo SQL Data Warehouse do jímky podporované úložišť pomocí služby Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/28/2018
ms.author: jingwang
ms.openlocfilehash: 7a9adc8e9b7bcf69cce6b8ecf00e44477c1b0da3
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39430735"
---
#  <a name="copy-data-to-or-from-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Kopírování dat do nebo z Azure SQL Data Warehouse pomocí Azure Data Factory 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you're using:"]
> * [Version1 ](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Aktuální verze](connector-azure-sql-data-warehouse.md)

Tento článek vysvětluje, jak pomocí aktivity kopírování ve službě Azure Data Factory pro kopírování dat do nebo z Azure SQL Data Warehouse. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Kopírování dat z Azure SQL Data Warehouse do jakékoli podporovaného úložiště dat jímky. A kopírování dat ze všech podporovaných zdrojů úložišť dat do Azure SQL Data Warehouse. Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování najdete v tématu [podporovaných úložišť dat a formáty](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor Azure SQL Data Warehouse podporuje tyto funkce:

- Kopírování dat pomocí ověřování SQL a ověřování pomocí tokenu aplikace Azure Active Directory (Azure AD) pomocí instančního objektu služby nebo Identity spravované služby (MSI).
- Jako zdroj načtení dat pomocí jazyka SQL nebo uloženou proceduru.
- Jako jímka načítání dat pomocí PolyBase nebo hromadného vložení. PolyBase vám doporučujeme pro lepší výkon kopírování.

> [!IMPORTANT]
> Všimněte si, že PolyBase podporuje jenom ověřování SQL, ale ověřování AD Azure to neplatí.

> [!IMPORTANT]
> Pokud zkopírujete data pomocí Azure Data Factory Integration Runtime, nakonfigurujte [brány firewall serveru Azure SQL](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) tak, aby službám Azure přístup k serveru.
> Pokud zkopírujete data s využitím místního prostředí integration runtime, konfigurace brány firewall serveru Azure SQL umožňující odpovídající rozsah IP adres. Tento rozsah obsahuje IP počítače, který se používá k připojení ke službě Azure SQL Database.

## <a name="get-started"></a>Začínáme

> [!TIP]
> K dosažení nejlepšího výkonu dosáhnete, načtení dat do Azure SQL Data Warehouse pomocí PolyBase. [Použití PolyBase k načítání dat do Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) část obsahuje podrobné informace o. Návod s případu použití, naleznete v tématu [načtení 1 TB do Azure SQL Data Warehouse pomocí Azure Data Factory v oblasti 15 minut](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které určují konkrétní entity služby Data Factory k konektoru služby Azure SQL Data Warehouse.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro službu Azure SQL Data Warehouse propojené jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na **AzureSqlDW**. | Ano |
| připojovací řetězec | Zadejte informace potřebné pro připojení k instanci Azure SQL Data Warehouse **connectionString** vlastnost. Označte toto pole jako **SecureString** bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| servicePrincipalId | Zadejte ID klienta vaší aplikace. | Ano, pokud používáte ověřování Azure AD se instanční objekt služby. |
| servicePrincipalKey | Zadejte klíč aplikace. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano, pokud používáte ověřování Azure AD se instanční objekt služby. |
| tenant | Zadejte informace o tenantovi (domény ID tenanta nebo název) v rámci které se nachází vaše aplikace. Podržením ukazatele myši v pravém horním rohu webu Azure portal můžete načíst ji. | Ano, pokud používáte ověřování Azure AD se instanční objekt služby. |
| connectVia | [Prostředí integration runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. (Pokud je vaše úložiště dat se nachází v privátní síti), můžete použít prostředí Azure Integration Runtime nebo místní prostředí integration runtime. Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. | Ne |

Různými typy ověřování najdete v následujících částech na požadavky a ukázky JSON v uvedeném pořadí:

- [Ověřování SQL](#sql-authentication)
- Azure AD aplikace ověřování pomocí tokenu: [instančního objektu](#service-principal-authentication)
- Azure AD aplikace ověřování pomocí tokenu: [identita spravované služby](#managed-service-identity-authentication)

### <a name="sql-authentication"></a>Ověřování pomocí SQL

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Příklad propojené služby, který používá ověřování SQL

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>Ověřování instančních objektů

Pokud chcete použít ověřování pomocí tokenu aplikace služby založené na instanční objekt Azure AD, postupujte takto:

1. **[Vytvoření aplikace Azure Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application)**  z portálu Azure portal. Poznamenejte si název aplikace a následující hodnoty, které definují propojené služby:

    - ID aplikace
    - Klíč aplikace
    - ID tenanta

1. **[Zřízení správce Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  pro váš server Azure SQL na portálu Azure portal, pokud jste tak již neučinili. Správce Azure AD může být skupina Azure AD nebo uživatel Azure AD. Když udělíte roli správce skupiny pomocí MSI, přeskočte kroky 3 a 4. Správce bude mít plný přístup k databázi.

1. **[Vytvořit uživatele databáze s omezením](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  pro instanční objekt. Připojení k datovému skladu z nebo na který se má ke kopírování dat pomocí nástrojů, jako je SSMS, identity Azure AD, který má alespoň oprávnění ALTER ANY uživatele. Spusťte následující příkaz T-SQL:
    
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

1. **Udělte nezbytná oprávnění instančního objektu** SQL uživatelů nebo jiné obvyklým způsobem. Spusťte následující kód:

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

1. **Konfigurace služby Azure SQL Data Warehouse propojené** ve službě Azure Data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Příklad propojené služby, který používá ověřování instančních objektů

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            },
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-service-identity-authentication"></a>Spravované ověřování Identity služby

Je možné přidružit datové továrny [identita spravované služby](data-factory-service-identity.md) , která představuje konkrétní objekt pro vytváření. Tuto identitu služby můžete použít pro ověřování v Azure SQL Data Warehouse. Přístup k určené objekt pro vytváření a kopírování dat z nebo do data warehouse s použitím této identity.

> [!IMPORTANT]
> Všimněte si, že se momentálně nepodporuje PolyBase ověřování MSI.

Pokud chcete používat ověřování tokenu aplikací Azure AD na základě Instalační služby MSI, postupujte takto:

1. **Vytvoření skupiny ve službě Azure AD.** Nastavte objekt pro vytváření MSI jako člena skupiny.

    a. Najdete identitu služby data factory na webu Azure Portal. Přejděte do služby data factory **vlastnosti**. Zkopírujte ID služby IDENTIT.

    b. Nainstalujte [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) modulu. Přihlaste se pomocí `Connect-AzureAD` příkazu. Spusťte následující příkazy k vytvoření skupiny a přidání služby data factory MSI jako člena.
    ```powershell
    $Group = New-AzureADGroup -DisplayName "<your group name>" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId "<your data factory service identity ID>"
    ```

1. **[Zřízení správce Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  pro váš server Azure SQL na portálu Azure portal, pokud jste tak již neučinili.

1. **[Vytvořit uživatele databáze s omezením](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  pro skupiny služby Azure AD. Připojení k datovému skladu z nebo na který se má ke kopírování dat pomocí nástrojů, jako je SSMS, identity Azure AD, který má alespoň oprávnění ALTER ANY uživatele. Spusťte následující příkaz T-SQL. 
    
    ```sql
    CREATE USER [your Azure AD group name] FROM EXTERNAL PROVIDER;
    ```

1. **Udělte skupině Azure AD nezbytná oprávnění** obvyklým způsobem pro uživatele serveru SQL a další. Například spusťte následující kód.

    ```sql
    EXEC sp_addrolemember [role name], [your Azure AD group name];
    ```

1. **Konfigurace služby Azure SQL Data Warehouse propojené** ve službě Azure Data Factory.

#### <a name="linked-service-example-that-uses-msi-authentication"></a>Příklad propojené služby, která používá ověřování MSI

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](https://docs.microsoft.com/en-us/azure/data-factory/concepts-datasets-linked-services) článku. Tato část obsahuje seznam vlastností, které podporuje datová sada Azure SQL Data Warehouse.

Chcete-li kopírovat data z nebo do služby Azure SQL Data Warehouse, nastavte **typ** vlastnosti datové sady na **AzureSqlDWTable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** musí být nastavena vlastnost datové sady **AzureSqlDWTable**. | Ano |
| tableName | Název tabulky nebo zobrazení v instanci Azure SQL Data Warehouse, která odkazuje propojenou službu. | Ano |

#### <a name="dataset-properties-example"></a>Příklad vlastnosti datové sady

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Data Warehouse linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastnosti podporované službou Azure SQL Data Warehouse zdroje a jímky.

### <a name="azure-sql-data-warehouse-as-the-source"></a>Azure SQL Data Warehouse jako zdroj

Ke zkopírování dat z Azure SQL Data Warehouse, nastavte **typ** vlastnosti ve zdroji aktivitu kopírování k **SqlDWSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** musí být nastavena vlastnost zdroje aktivity kopírování **SqlDWSource**. | Ano |
| sqlReaderQuery | Použijte vlastní dotaz SQL číst data. Příklad: `select * from MyTable`. | Ne |
| sqlReaderStoredProcedureName | Název uložené procedury, která čte data ze zdrojové tabulky. Příkaz SELECT v uložené proceduře musí být poslední příkaz jazyka SQL. | Ne |
| storedProcedureParameters | Parametry pro uloženou proceduru.<br/>Povolené hodnoty jsou páry název nebo hodnota. Názvy a použití malých a velkých parametry musí odpovídat názvům a použití malých a velkých parametrů uložené procedury. | Ne |

### <a name="points-to-note"></a>Odkazuje na mějte na paměti

- Pokud **sqlReaderQuery** je určená pro **SqlSource**, spustí aktivita kopírování tohoto dotazu na zdroji Azure SQL Data Warehouse se mají získat data. Nebo můžete zadat uloženou proceduru. Zadejte **sqlReaderStoredProcedureName** a **storedProcedureParameters** pokud používá parametry uložené procedury.
- Pokud nezadáte buď **sqlReaderQuery** nebo **sqlReaderStoredProcedureName**, sloupce definované v **struktura** část datové sady JSON se používají pro Sestavte dotaz. `select column1, column2 from mytable` spouští se pro Azure SQL Data Warehouse. Pokud nemá definici datové sady **struktura**, jsou vybrány všechny sloupce z tabulky.
- Při použití **sqlReaderStoredProcedureName**, stále je třeba zadat fiktivní **tableName** vlastnost v datové sadě JSON.

#### <a name="sql-query-example"></a>Příklad dotazu SQL

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlDWSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="stored-procedure-example"></a>Příklad uložené procedury

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlDWSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="stored-procedure-definition"></a>Definice uložené procedury

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
     select *
     from dbo.UnitTestSrcTable
     where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="azure-sql-data-warehouse-as-sink"></a> Azure SQL Data Warehouse jako jímka

Ke zkopírování dat do Azure SQL Data Warehouse, nastavte typ jímky v aktivitě kopírování do **SqlDWSink**. Následující vlastnosti jsou podporovány v aktivitě kopírování **jímky** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** vlastnost jímky aktivity kopírování musí být nastavena na **SqlDWSink**. | Ano |
| allowPolyBase | Označuje, zda místo mechanismus hromadné vložení pomocí PolyBase, v případě potřeby. <br/><br/> Doporučujeme vám, načtení dat do SQL Data Warehouse pomocí PolyBase. Zobrazit [použití PolyBase k načítání dat do Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) části omezení a podrobnosti.<br/><br/>Povolené hodnoty jsou **True** a **False** (výchozí).  | Ne |
| polyBaseSettings | Skupina vlastností, které může být zadán při **allowPolybase** je nastavena na **true**. | Ne |
| rejectValue | Určuje číslo nebo procentuální podíl řádků, které mohou být odmítnuty předtím, než se dotaz nezdaří.<br/><br/>Další informace o společnosti PolyBase možnosti odmítnutí v části argumenty [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). <br/><br/>Povolené hodnoty jsou 0 (výchozí), 1, 2, atd. |Ne |
| rejectType | Určuje, zda **rejectValue** možnost je hodnotu literálu nebo procenta.<br/><br/>Povolené hodnoty jsou **hodnotu** (výchozí) a **procento**. | Ne |
| rejectSampleValue | Určuje počet řádků, načtěte před PolyBase přepočítá procento pozice zamítnutých řádků.<br/><br/>Povolené hodnoty jsou 1, 2, atd. | Ano, pokud **rejectType** je **procento**. |
| useTypeDefault | Určuje způsob zpracování chybějící hodnoty v textových souborů s oddělovači, když PolyBase načte data z textového souboru.<br/><br/>Další informace o této vlastnosti v části argumenty [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Povolené hodnoty jsou **True** a **False** (výchozí). | Ne |
| WriteBatchSize | Vloží data do tabulky SQL, když dosáhne velikosti vyrovnávací paměti **writeBatchSize**. Platí, pouze pokud není použit PolyBase.<br/><br/>Je povolená hodnota **celé číslo** (počet řádků). | Ne. Výchozí hodnota je 10000. |
| writeBatchTimeout | Čekací doba pro dávkové operace insert dokončit před uplynutím časového limitu. Platí, pouze pokud není použit PolyBase.<br/><br/>Je povolená hodnota **timespan**. Příklad: "00: 30:00" (30 minut). | Ne |
| preCopyScript | Zadejte dotaz SQL pro aktivitu kopírování ke spuštění před zápisem dat do Azure SQL Data Warehouse při každém spuštění. Tuto vlastnost použijte k vyčištění dat předem. | Ne | (#repeatability během – kopírování). | Příkaz dotazu. | Ne |

#### <a name="sql-data-warehouse-sink-example"></a>Příklad jímku SQL Data Warehouse

```json
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

Další informace o tom, jak pomocí funkce PolyBase načteme efektivně SQL Data Warehouse v další části.

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Použijte PolyBase k načítání dat do Azure SQL Data Warehouse

Pomocí [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) je účinný způsob, jak načíst větší množství dat do Azure SQL Data Warehouse s vysokou propustností. Zobrazí se vám velké zvýšení propustnosti pomocí PolyBase místo výchozího mechanismu hromadné vložení. Zobrazit [výkonu](copy-activity-performance.md#performance-reference) podrobné porovnání. Návod s případu použití, naleznete v tématu [načtení 1 TB do Azure SQL Data Warehouse](https://docs.microsoft.com/en-us/azure/data-factory/v1/data-factory-load-sql-data-warehouse).

* Pokud vaše zdrojová data jsou v Azure Blob storage nebo Azure Data Lake Store a formát je kompatibilní s technologií PolyBase, kopii přímo do Azure SQL Data Warehouse pomocí PolyBase. Podrobnosti najdete v tématu  **[přímé kopírování pomocí PolyBase](#direct-copy-by-using-polybase)**.
* Pokud zdrojové úložiště dat a formát polybase původně nepodporuje, použijte **[fázovaného kopírování pomocí PolyBase](#staged-copy-by-using-polybase)** místo toho funkci. Funkce dvoufázové instalace kopírování také poskytuje vyšší propustnost. Automaticky převádí data do formátu kompatibilním PolyBase. A ukládá data do úložiště objektů Blob v Azure. Pak načte data do SQL Data Warehouse.

> [!IMPORTANT]
> Všimněte si, že se momentálně nepodporuje PolyBase pro ověřování pomocí tokenu aplikace Azure AD na základě Instalační služby MSI.

### <a name="direct-copy-by-using-polybase"></a>Kopírování s přímým přístupem pomocí PolyBase

SQL Data Warehouse PolyBase přímo podporuje objektů Blob v Azure a Azure Data Lake Store. Instanční objekt se používá jako zdroj a má požadavky na formát konkrétní soubor. Pokud vaše zdrojová data splňuje kritéria popsané v této části, zkopírovat přímo ze zdrojového úložiště dat do Azure SQL Data Warehouse pomocí PolyBase. Jinak použijte [fázovaného kopírování pomocí PolyBase](#staged-copy-by-using-polybase).

> [!TIP]
> Ke zkopírování dat efektivně z Data Lake Store do služby SQL Data Warehouse, přečtěte si informace z [Azure Data Factory umožňuje ještě snadněji a pohodlné odhalit další poznatky z dat při použití Data Lake Store s využitím SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Pokud požadavky nejsou splněny, Azure Data Factory zkontroluje nastavení a automaticky přejde zpět k hromadné vložení mechanismus pro přesun dat.

1. **Zdroj propojená služba** typ je **AzureStorage** nebo **AzureDataLakeStore** s ověřování instančních objektů.
1. **Vstupní datová sada** typ je **AzureBlob** nebo **AzureDataLakeStoreFile**. Typ formátu podle `type` vlastnosti je **OrcFormat**, **ParquetFormat**, nebo **TextFormat**, s následující konfigurací:

   1. `rowDelimiter` musí být **\n**.
   1. `nullValue` je buď nastavit na **prázdný řetězec** ("") nebo jako výchozí, vlevo a `treatEmptyAsNull` není nastaven na hodnotu false.
   1. `encodingName` je nastavena na **utf-8**, což je výchozí hodnota.
   1. `escapeChar`, `quoteChar` a `skipLineCount` nejsou zadány. Podpora technologie PolyBase přeskočit řádek záhlaví, které se dají konfigurovat jako `firstRowAsHeader` ve službě ADF.
   1. `compression` může být **bez komprese**, **GZip**, nebo **Deflate**.

    ```json
    "typeProperties": {
       "folderPath": "<blobpath>",
       "format": {
           "type": "TextFormat",
           "columnDelimiter": "<any delimiter>",
           "rowDelimiter": "\n",
           "nullValue": "",
           "encodingName": "utf-8",
           "firstRowAsHeader": <any>
       },
       "compression": {
           "type": "GZip",
           "level": "Optimal"
       }
    },
    ```

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "BlobDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-by-using-polybase"></a>Fázované kopírovat pomocí funkce PolyBase

Pokud vaše zdrojová data nesplňuje kritéria v předchozí části, povolte data kopírování prostřednictvím dočasné instance pracovní úložiště objektů Blob v Azure. Nemůže být Azure Storage úrovně Premium. V tomto případě služby Azure Data Factory automaticky spustí transformace na data, aby splňovala požadavky na formát dat PolyBase. Potom použije PolyBase k načtení dat do SQL Data Warehouse. Nakonec ho vyčistí dočasný data z úložiště objektů blob. Zobrazit [fázovaného kopírování](copy-activity-performance.md#staged-copy) podrobnosti o kopírování dat přes instanci pracovní úložiště objektů Blob v Azure.

Pokud chcete tuto funkci používat, vytvořte [propojenou službu Azure Storage](connector-azure-blob-storage.md#linked-service-properties) , který odkazuje na účet úložiště Azure s prozatímní blob storage. Zadejte `enableStaging` a `stagingSettings` vlastnosti pro aktivitu kopírování, jak je znázorněno v následujícím kódu:

```json
"activities":[
    {
        "name": "CopyFromSQLServerToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "SQLServerDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
]
```

## <a name="best-practices-for-using-polybase"></a>Doporučené postupy pro používání funkce PolyBase

Následující části obsahují osvědčené postupy kromě oprávnění uvedených v [osvědčené postupy pro službu Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Oprávnění databáze

Použití technologie PolyBase, musí mít uživatel, který načte data do SQL Data Warehouse [oprávnění "CONTROL"](https://msdn.microsoft.com/library/ms191291.aspx) na cílové databázi. Chcete-li přidat uživatele jako člena je jeden způsob, jak toho dosáhnout **db_owner** role. Zjistěte, jak to udělat [přehled SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limits"></a>Velikost řádku a datový typ omezení

Načítání PolyBase jsou omezené na řádky, které jsou menší než 1 MB. Nelze načíst do VARCHR(MAX) či NVARCHAR(MAX) nebo VARBINARY(MAX). Další informace najdete v tématu [limity kapacity služby SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Pokud zdroj dat má řádky větší než 1 MB, můžete chtít svisle rozdělit do několika malých ty zdrojové tabulky. Ujistěte se, že největší velikost každého řádku nepřekračuje limit. Menší tabulky můžete pak načíst pomocí PolyBase a sloučeny ve službě Azure SQL Data Warehouse.

### <a name="sql-data-warehouse-resource-class"></a>Třída prostředků SQL Data Warehouse

K dosažení nejlepší možné propustnost, přiřadíte větší třídu prostředků pro uživatele, který načte data do SQL Data Warehouse pomocí PolyBase.

### <a name="tablename-in-azure-sql-data-warehouse"></a>**tableName** ve službě Azure SQL Data Warehouse

V následující tabulce jsou uvedené příklady toho, jak zadat **tableName** vlastnost v datové sadě JSON. Ukazuje několik kombinace schéma a tabulku názvů.

| Schéma databáze | Název tabulky | **tableName** vlastnost JSON |
| --- | --- | --- |
| vlastník databáze | Tabulka | Tabulka nebo vlastník databáze. Tabulka nebo [dbo]. [MyTable] |
| dbo1 | Tabulka | dbo1. Tabulka nebo [dbo1]. [MyTable] |
| vlastník databáze | My.Table | [My.Table] nebo [dbo]. [My.Table] |
| dbo1 | My.Table | [dbo1]. [My.Table] |

Pokud se zobrazí následující chyba, problém může být hodnota zadaná pro **tableName** vlastnost. V předchozí tabulce najdete správný způsob, jak určit hodnoty **tableName** vlastnost JSON.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Sloupce s výchozími hodnotami.

Funkce PolyBase ve službě Data Factory v současné době přijímá pouze stejný počet sloupců jako v cílové tabulce. Příkladem je tabulka s čtyři sloupce, kde jeden z nich je definované s výchozí hodnotou. Vstupní data stále musí mít čtyři sloupce. Vstupní datová sada třemi sloupci poskytuje zpráva podobná následující zpráva:

```
All columns of the table must be specified in the INSERT BULK statement.
```

Hodnota NULL je zvláštní forma výchozí hodnotu. Pokud je sloupec s možnou hodnotou Null, vstupní data v objektu blob pro tento sloupec může být prázdná. Ale nemůže být chybí vstupní datové sady. PolyBase se vloží hodnota NULL pro chybějící hodnoty ve službě Azure SQL Data Warehouse.

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Mapování datového typu pro službu Azure SQL Data Warehouse

Při kopírování dat z nebo do služby Azure SQL Data Warehouse, se používají následující mapování z Azure SQL Data Warehouse datových typů na Azure Data Factory dočasné datové typy. Zobrazit [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) se dozvíte, jak aktivitu kopírování, která mapuje typ zdroje schéma a data na jímce.

| Azure SQL Data Warehouse datový typ | Data Factory dočasné datový typ |
|:--- |:--- |
| bigint | Int64 |
| Binární | Byte] |
| Bit | Logická hodnota |
| Char | Řetězec, Char] |
| datum | DateTime |
| Datum a čas | DateTime |
| datetime2 | DateTime |
| DateTimeOffset | DateTimeOffset |
| Decimal | Decimal |
| Atribut FILESTREAM (varbinary(max)) | Byte] |
| Float | Double |
| image | Byte] |
| int | Datový typ Int32 |
| peníze | Decimal |
| nchar | Řetězec, Char] |
| ntext | Řetězec, Char] |
| Číselné | Decimal |
| nvarchar | Řetězec, Char] |
| Real | Jednoduchá |
| ROWVERSION | Byte] |
| smalldatetime | DateTime |
| smallint | Int16 |
| Smallmoney | Decimal |
| SQL_VARIANT | Objekt * |
| text | Řetězec, Char] |
| time | Časový interval |
| časové razítko | Byte] |
| tinyint | Bajt |
| UniqueIdentifier | Guid |
| varbinary | Byte] |
| varchar | Řetězec, Char] |
| xml | XML |

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky, aktivita kopírování ve službě Azure Data Factory najdete v tématu [podporovaných úložišť dat a formáty](copy-activity-overview.md##supported-data-stores-and-formats).
