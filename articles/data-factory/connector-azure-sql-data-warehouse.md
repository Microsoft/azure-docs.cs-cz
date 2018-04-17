---
title: Kopírování dat do/z Azure SQL Data Warehouse pomocí služby Data Factory | Microsoft Docs
description: Zjistěte, jak zkopírovat data z podporované zdrojové úložiště do Azure SQL Data Warehouse (nebo) z SQL Data Warehouse na podporované podřízený úložiště pomocí služby Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2018
ms.author: jingwang
ms.openlocfilehash: 0bc24fb0206455c723acf5e6f4b82d82002f727c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="copy-data-to-or-from-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Kopírovat data do nebo z Azure SQL Data Warehouse pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Verze 2 – Preview](connector-azure-sql-data-warehouse.md)

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory ke zkopírování dat do a z Azure SQL Data Warehouse. Vychází [zkopírujte aktivity přehled](copy-activity-overview.md) článek, který představuje obecný přehled aktivity kopírování.

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [konektor Azure SQL Data Warehouse v V1](v1/data-factory-azure-sql-data-warehouse-connector.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Můžete zkopírovat data z/do Azure SQL Data Warehouse do úložiště dat žádné podporované podřízený nebo zkopírování dat z jakékoli úložiště podporované zdroje dat do Azure SQL Data Warehouse. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor Azure SQL Data Warehouse podporuje:

- Kopírování dat pomocí **ověřování SQL**, a **ověření pomocí tokenu aplikace Azure Active Directory** s instanční objekt nebo spravovat Identity služby (MSI).
- Jako zdroj načítání dat pomocí dotazu SQL nebo uloženou proceduru.
- Jako jímku, načítání dat pomocí **PolyBase** nebo příkaz bulk insert. Je **doporučená** pro lepší výkon kopírování.

> [!IMPORTANT]
> Poznámka: PolyBase podporují pouze SQL authentcation, ale není ověřování Azure Active Directory.

> [!IMPORTANT]
> Pokud zkopírujete data pomocí Runtime integrace Azure, nakonfigurujte [brány Firewall serveru SQL Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) k [povolit službám Azure přístup k serveru](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Pokud zkopírujete data pomocí Self-hosted integrace Runtime, nakonfigurujte bránu firewall serveru SQL Azure umožňující odpovídající rozsah IP adres včetně IP počítače, který se používá k připojení k databázi SQL Azure.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnosti, které se používají k definování konkrétní entity služby Data Factory pro konektor Azure SQL Data Warehouse.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro Azure SQL Data Warehouse propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu musí být nastavena na: **AzureSqlDW** | Ano |
| připojovací řetězec |Zadejte informace potřebné pro připojení k Azure SQL Data Warehouse instance pro vlastnost connectionString. Toto pole označit jako SecureString bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). |Ano |
| servicePrincipalId | Zadejte ID aplikace klienta. | Ano, při použití ověřování AAD s instanční objekt. |
| servicePrincipalKey | Zadejte klíč aplikace. Toto pole označit jako SecureString bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). | Ano, při použití ověřování AAD s instanční objekt. |
| tenant | Zadejte informace o klienta (název nebo klienta domény ID) v rámci které se nachází aplikace. Můžete ji načíst podržením ukazatele myši v pravém horním rohu portálu Azure. | Ano, při použití ověřování AAD s instanční objekt. |
| connectVia | [Integrace Runtime](concepts-integration-runtime.md) který se má použít pro připojení k úložišti. (Pokud je vaše úložiště dat se nachází v privátní síti), můžete použít modul Runtime integrace Azure nebo Self-hosted integrace Runtime. Pokud není zadaný, použije výchozí Runtime integrace Azure. |Ne |

Různými typy ověřování najdete v následujících částech na požadavky a ukázky JSON v uvedeném pořadí:

- [Pomocí ověřování SQL.](#using-sql-authentication)
- [Pomocí ověření pomocí tokenu aplikace AAD - instanční objekt](#using-service-principal-authentication)
- [Pomocí ověřování tokenu AAD aplikace - identita spravované služby](#using-managed-service-identity-authentication)

### <a name="using-sql-authentication"></a>Pomocí ověřování SQL.

**Propojenou službu příkladu pomocí ověřování SQL:**

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

### <a name="using-service-principal-authentication"></a>Pomocí objektu zabezpečení ověřování služby

Služba hlavní ověřování založené na AAD aplikace tokenu, postupujte podle těchto kroků:

1. **[Vytvoření aplikace Azure Active Directory z portálu Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application).**  Poznamenejte si název aplikace a následující hodnoty, které můžete použít k definování propojené služby:

    - ID aplikace
    - Klíč aplikace
    - ID tenanta

2. **[Zřízení správcem služby Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#create-an-azure-ad-administrator-for-azure-sql-server)**  pro Server SQL Azure na portálu Azure, pokud jste tak dosud neučinili. AAD správce může být AAD uživatele nebo skupinu AAD aplikace. Pokud udělit skupině s MSI roli správce, přeskočte krok 3 a 4 níže jako správce bude mít plný přístup k databázi.

3. **Vytvořte uživatele databáze s omezením pro objekt služby**, propojením do datového skladu z/do které chcete kopírovat data pomocí nástroje, například aplikace SSMS s AAD identity s nejméně ALTER žádné oprávnění uživatele a provádění následující T-SQL . Další informace v databázi s omezením uživatele z [zde](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities).
    
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Udělte nezbytná oprávnění objektu služby** obvyklým způsobem pro uživatele SQL, například spuštěním níže:

    ```sql
    EXEC sp_addrolemember '[your application name]', 'readonlyuser';
    ```

5. Ve službě ADF nakonfigurujte služby Azure SQL Data Warehouse propojená.


**Příklad propojené služby pomocí ověřování hlavní služby:**

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

### <a name="using-managed-service-identity-authentication"></a>Pomocí ověření identity spravované služby

Objekt pro vytváření dat může být přidružen [identita spravované služby (MSI)](data-factory-service-identity.md), který představuje tuto konkrétní data factory. Tato identita služby můžete použít pro ověřování Azure SQL Data Warehouse, která umožní tento určený objekt pro vytváření pro přístup a kopírování dat z/do datového skladu.

Použití Instalační služby MSI na základě tokenu ověřování AAD aplikace, postupujte takto:

1. **Vytvoření skupiny ve službě Azure AD a nastavte objektu pro vytváření MSI jako člena skupiny**.

    a. Vyhledejte identitu služby objektu pro vytváření dat z portálu Azure. Přejděte na datovou továrnu -> Vlastnosti -> kopie **ID služby pro IDENTITU**.

    b. Nainstalujte [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) modul přihlašování pomocí `Connect-AzureAD` příkaz, a spusťte následující příkazy a vytvořte skupinu a přidejte objekt pro vytváření dat MSI jako člena.
    ```powershell
    $Group = New-AzureADGroup -DisplayName "<your group name>" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId "<your data factory service identity ID>"
    ```

2. **[Zřízení správcem služby Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#create-an-azure-ad-administrator-for-azure-sql-server)**  pro Server SQL Azure na portálu Azure, pokud jste tak dosud neučinili.

3. **Vytvořte uživatele databáze s omezením pro skupinu AAD**, propojením do datového skladu z/do které chcete kopírovat data pomocí nástroje, například aplikace SSMS s AAD identity s nejméně ALTER žádné oprávnění uživatele a provádění následující T-SQL. Další informace v databázi s omezením uživatele z [zde](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities).
    
    ```sql
    CREATE USER [your AAD group name] FROM EXTERNAL PROVIDER;
    ```

4. **Udělit skupině AAD nezbytná oprávnění** obvyklým způsobem pro uživatele SQL, například spuštěním níže:

    ```sql
    EXEC sp_addrolemember '[your AAD group name]', 'readonlyuser';
    ```

5. Ve službě ADF nakonfigurujte služby Azure SQL Data Warehouse propojená.

**Propojenou službu příkladu pomocí Instalační služby MSI ověřování:**

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady najdete v článku datové sady. Tato část obsahuje seznam vlastností, které podporuje Azure SQL Data Warehouse datovou sadu.

Ke zkopírování dat z/do Azure SQL Data Warehouse, nastavte vlastnost typu datové sady, která **AzureSqlDWTable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu datové sady musí být nastavena na: **AzureSqlDWTable** | Ano |
| tableName |Název tabulky nebo zobrazení instance Azure SQL Data Warehouse na kterou odkazuje propojená služba. | Ano |

**Příklad:**

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností, které podporuje Azure SQL Data Warehouse zdroj a jímka.

### <a name="azure-sql-data-warehouse-as-source"></a>Azure SQL Data Warehouse jako zdroj

Ke zkopírování dat z Azure SQL Data Warehouse, nastavte typ zdroje v aktivitě kopírování do **SqlDWSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ zdroje kopie aktivity musí být nastavena na: **SqlDWSource** | Ano |
| sqlReaderQuery |Čtení dat pomocí vlastního dotazu SQL. Příklad: `select * from MyTable`. |Ne |
| sqlReaderStoredProcedureName |Název uložené procedury, který čte data ze zdrojové tabulky. Poslední příkaz jazyka SQL musí být příkaz SELECT v uložené proceduře. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru.<br/>Povolené hodnoty jsou: páry název/hodnota. Názvy a malá a velká písmena parametry musí odpovídat názvům a malá a velká písmena parametry uložené procedury. |Ne |

**Všimněte si body:**

- Pokud **sqlReaderQuery** je zadán pro SqlSource, spuštění aktivity kopírování tohoto dotazu na zdroji Azure SQL Data Warehouse se získat data. Alternativně můžete zadat uložené procedury zadáním **sqlReaderStoredProcedureName** a **storedProcedureParameters** (Pokud uložená procedura přebírá parametry).
- Pokud nezadáte "sqlReaderQuery" nebo "sqlReaderStoredProcedureName", sloupce definované v části "struktura" sady dat JSON se používají k vytvoření dotazu (`select column1, column2 from mytable`) ke spouštění na Azure SQL Data Warehouse. Pokud definice datové sady nemá strukturu"", jsou vybrány všechny sloupce z tabulky.
- Při použití **sqlReaderStoredProcedureName**, stále je třeba zadat fiktivní **tableName** vlastnost v datové sadě JSON.

**Příklad: pomocí jazyka SQL**

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

**Příklad: pomocí uložené procedury**

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

**Uložená procedura definice:**

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

### <a name="azure-sql-data-warehouse-as-sink"></a>Azure SQL Data Warehouse jako jímku

Ke zkopírování dat do Azure SQL Data Warehouse, nastavte typ jímky v aktivitě kopírování do **SqlDWSink**. Následující vlastnosti jsou podporovány v aktivitě kopírování **podřízený** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ jímky kopie aktivity musí nastavena: **SqlDWSink** | Ano |
| allowPolyBase |Označuje, zda místo hromadné vložení mechanismus použít PolyBase (v případě potřeby). <br/><br/> **Pomocí PolyBase je doporučeným způsobem, jak načíst data do SQL Data Warehouse.** V tématu [PolyBase používá k načtení dat do Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) části omezení a podrobnosti.<br/><br/>Povolené hodnoty jsou: **True** (výchozí), a **False**.  |Ne |
| polyBaseSettings |Skupinu vlastností, které se dají zadat při **allowPolybase** je nastavena na **true**. |Ne |
| rejectValue |Určuje číslo nebo podíl řádků, které může být odmítnutá předtím, než se dotaz nezdaří.<br/><br/>Další informace o možnostech odmítněte používání funkce PolyBase v **argumenty** části [vytvořit EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) tématu. <br/><br/>Povolené hodnoty jsou: 0 (výchozí), 1, 2,... |Ne |
| rejectType |Určuje, zda je hodnota literálu nebo jako procento zadána možnost rejectValue.<br/><br/>Povolené hodnoty jsou: **hodnotu** (výchozí), a **procento**. |Ne |
| rejectSampleValue |Určuje počet řádků k načtení předtím, než PolyBase přepočítá procento odmítnutých řádků.<br/><br/>Povolené hodnoty jsou: 1, 2,... |Ano, pokud **rejectType** je **procento** |
| useTypeDefault |Určuje způsob zpracování chybějící hodnoty v textových souborů s oddělovači, když PolyBase načítá data z textového souboru.<br/><br/>Další informace o této vlastnosti v části argumenty [vytvořit EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Povolené hodnoty jsou: **True**, **False** (výchozí). |Ne |
| writeBatchSize |Vloží data do tabulky SQL, když velikost vyrovnávací paměti dosáhne writeBatchSize. Platí pouze při používání funkce PolyBase nepoužívá.<br/><br/>Povolené hodnoty jsou: celé číslo (počet řádků). |Ne (výchozí hodnota je 10000) |
| writeBatchTimeout |Počkejte, než čas na dokončení předtím, než vyprší časový limit operace dávkové vložení. Platí pouze při používání funkce PolyBase nepoužívá.<br/><br/>Povolené hodnoty jsou: časový interval. Příklad: "00: 30:00" (30 minut). |Ne |
| preCopyScript |Zadejte dotaz SQL pro aktivitu kopírování ke spuštění před zápis dat do Azure SQL Data Warehouse při každém spuštění. Tato vlastnost slouží k vyčištění předem načtená data. |Ne |(#repeatability během kopírování). |Příkaz dotazu. |Ne |

**Příklad:**

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

Další informace o tom, jak pomocí funkce PolyBase načteme do SQL Data Warehouse efektivně z další části.

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Použijte PolyBase k načtení dat do Azure SQL Data Warehouse

Pomocí **[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)** je účinný způsob načítání velké množství dat do Azure SQL Data Warehouse s vysokou propustností. Velký nárůst v propustnost můžete zobrazit pomocí PolyBase místo výchozího mechanismu hromadné vložení. V tématu [zkopírujte výkonu referenční číslo](copy-activity-performance.md#performance-reference) s podrobné porovnání. Návod s případu použití najdete v tématu [načíst 1 TB do Azure SQL Data Warehouse pomocí Azure Data Factory v části 15 minut](connector-azure-sql-data-warehouse.md).

* Pokud je zdroj dat v **objektů Blob v Azure nebo Azure Data Lake Store**a formát je kompatibilní s funkcí PolyBase, můžete zkopírovat přímo do Azure SQL Data Warehouse pomocí PolyBase. V tématu **[přímé kopírování pomocí PolyBase](#direct-copy-using-polybase)** s podrobnostmi.
* Pokud vaše zdrojového úložiště dat a formát není podporován původně polybase, můžete použít **[připravený kopírování pomocí PolyBase](#staged-copy-using-polybase)** místo toho funkci. Také poskytuje lepší propustnosti automaticky převod dat do formátu kompatibilní s funkcí PolyBase a ukládání dat v úložišti objektů Blob v Azure. Potom načte data do SQL Data Warehouse.

> [!IMPORTANT]
> Všimněte si, podporuje se jen PolyBase authentcation Azure SQL Data Warehouse SQL, ale není ověřování Azure Active Directory.

### <a name="direct-copy-using-polybase"></a>Přímé kopírování pomocí PolyBase

SQL Data Warehouse PolyBase přímo podporují objektů Blob v Azure a Azure Data Lake Store (pomocí instanční objekt) jako zdroj a s požadavky na konkrétní soubor formátu. Pokud vaše zdrojová data splňuje kritéria popsaných v této části, můžete přímo zkopírovat ze zdrojového úložiště dat do Azure SQL Data Warehouse pomocí PolyBase. Jinak můžete použít [připravený kopírování pomocí PolyBase](#staged-copy-using-polybase).

> [!TIP]
> Ke zkopírování dat z Data Lake Store k SQL Data Warehouse efektivně, další informace z [Azure Data Factory usnadňuje i a pohodlný k odhalení přehled o datech, při použití Data Lake Store s SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Pokud požadavky nejsou splněny, zkontroluje nastavení Azure Data Factory a automaticky se vrátí k hromadné vložení mechanismus pro přesun dat.

1. **Zdroj propojené služby** je typu: **azurestorage** nebo **AzureDataLakeStore** s objekt zabezpečení ověřování služby.
2. **Vstupní datové sady** je typu: **AzureBlob** nebo **AzureDataLakeStoreFile**a zadejte v části formát `type` vlastnosti je **OrcFormat** , **ParquetFormat**, nebo **TextFormat** s následující konfigurace:

   1. `rowDelimiter` musí být **\n**.
   2. `nullValue` je nastavena na **prázdný řetězec** (""), nebo `treatEmptyAsNull` je nastaven na **true**.
   3. `encodingName` je nastavena na **znakové sady utf-8**, což je **výchozí** hodnotu.
   4. `escapeChar`, `quoteChar`, `firstRowAsHeader`, a `skipLineCount` nejsou zadané.
   5. `compression` může být **bez komprese**, **GZip**, nebo **Deflate**.

    ```json
    "typeProperties": {
       "folderPath": "<blobpath>",
       "format": {
           "type": "TextFormat",
           "columnDelimiter": "<any delimiter>",
           "rowDelimiter": "\n",
           "nullValue": "",
           "encodingName": "utf-8"
       },
       "compression": {
           "type": "GZip",
           "level": "Optimal"
       }
    },
    ```

3. Neexistuje žádné `skipHeaderLineCount` nastavení v části **BlobSource** nebo **AzureDataLakeStore** pro aktivitu kopírování v kanálu.
4. Neexistuje žádné `sliceIdentifierColumnName` nastavení v části **SqlDWSink** pro aktivitu kopírování v kanálu. (PolyBase zaručuje, že se aktualizuje všechna data nebo nic se aktualizuje v jednom spustit. K dosažení **opakovatelnosti**, můžete použít `sqlWriterCleanupScript`).

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

### <a name="staged-copy-using-polybase"></a>Dvoufázové instalace kopírování pomocí PolyBase

Pokud vaše zdrojová data nesplňuje kritéria byla zavedená v předchozí části, můžete povolit kopírování dat prostřednictvím dočasné pracovní Azure Blob Storage (úložiště úrovně Premium nemůže být). V takovém případě Azure Data Factory automaticky provede transformace dat, které splňují požadavky formát dat PolyBase, potom použijte PolyBase k načtení dat do SQL Data Warehouse a pak čištění Vaše dočasná data z úložiště objektů Blob. V tématu [připravený kopie](copy-activity-performance.md#staged-copy) informace o tom, jak kopírování dat prostřednictvím pracovní objektů Blob v Azure funguje obecně.

Chcete-li tuto funkci používat, vytvořte [propojená služba Azure Storage](connector-azure-blob-storage.md#linked-service-properties) který odkazuje na účet úložiště Azure, který má dočasné objektu blob úložiště, zadejte `enableStaging` a `stagingSettings` vlastnosti pro aktivitu kopírování, jak je znázorněno v následujícím kódu:

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

## <a name="best-practices-when-using-polybase"></a>Osvědčené postupy při používání funkce PolyBase

Následující části obsahují další osvědčené postupy pro ty, které jsou uvedené v [osvědčené postupy pro Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Oprávnění požadovaná databáze

Pokud chcete používat funkce PolyBase, vyžaduje má uživatel používaný k načtení dat do SQL Data Warehouse [oprávnění "Řízení"](https://msdn.microsoft.com/library/ms191291.aspx) na cílovou databázi. Jeden způsob, jak dosáhnout, který je přidejte tohoto uživatele jako člen role "db_owner". Zjistěte, jak to provést pomocí následujících [v této části](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limitation"></a>Velikost řádku a datový typ omezení

Polybase zatížení jsou omezeny na obou načítání řádků menší než **1 MB** a nelze načíst VARCHR(MAX), NVARCHAR(MAX) nebo VARBINARY(MAX). Odkazovat na [zde](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Pokud máte zdroj dat s řádky o velikosti větší než 1 MB, můžete rozdělit zdrojové tabulky svisle na několik malých sítí, kde největší velikost řádku každého z nich není delší než limit. Menší tabulky pak lze načíst pomocí PolyBase a sloučí v Azure SQL Data Warehouse.

### <a name="sql-data-warehouse-resource-class"></a>Třída prostředků SQL Data Warehouse

K dosažení nejlepší možné propustnost, vezměte v úvahu přiřazení větší Třída prostředků uživateli používaný k načtení dat do SQL Data Warehouse pomocí PolyBase.

### <a name="tablename-in-azure-sql-data-warehouse"></a>Název tabulky v Azure SQL Data Warehouse

Následující tabulka obsahuje příklady, jak můžete zadat **tableName** vlastnost v datové sadě JSON pro různé kombinace názvu schéma a tabulku.

| Schéma databáze | Název tabulky | hodnotu vlastnosti tableName JSON |
| --- | --- | --- |
| dbo |MyTable |MyTable nebo dbo. MyTable nebo [dbo]. [Tabulka] |
| dbo1 |MyTable |dbo1. MyTable nebo [dbo1]. [Tabulka] |
| dbo |My.Table |[My.Table] nebo [dbo]. [My.Table] |
| dbo1 |My.Table |[dbo1]. [My.Table] |

Pokud se zobrazí následující chyba, může to být problém s hodnotou, kterou jste zadali pro vlastnost tableName. Najdete v tabulce pro správný způsob, jak určit hodnoty pro vlastnost tableName JSON.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Sloupce s výchozími hodnotami

V současné době funkce PolyBase v datové továrně přijímá pouze stejný počet sloupců jako v cílové tabulce. Řekněme, máte tabulku s čtyři sloupce a jeden z nich je definovaná pomocí výchozí hodnotu. Vstupní data stále by mělo obsahovat čtyři sloupce. Poskytnutí 3sloupce vstupní datové sady, které by poskytují chyba podobná následující zpráva:

```
All columns of the table must be specified in the INSERT BULK statement.
```

Hodnota NULL je speciální forma výchozí hodnotu. Pokud je sloupec s možnou hodnotou Null, vstupní data (v objektu blob) pro tento sloupec může být prázdná (nelze chybí vstupní datové sady). PolyBase vloží NULL u nich v Azure SQL Data Warehouse.

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Mapování datového typu pro Azure SQL Data Warehouse

Při kopírování dat z/do Azure SQL Data Warehouse, se používají následující mapování z Azure SQL Data Warehouse datové typy k Azure Data Factory dočasné datové typy. V tématu [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) a zjistěte, jak aktivity kopírování mapuje zdroje schéma a data typ jímky.

| Azure SQL Data Warehouse datový typ | Typ průběžných dat objektu pro vytváření dat |
|:--- |:--- |
| bigint |Int64 |
| Binární |Byte] |
| Bit |Logická hodnota |
| Char |Řetězec, Char] |
| datum |DateTime |
| Datum a čas |DateTime |
| datetime2 |DateTime |
| Datový typ DateTimeOffset |Datový typ DateTimeOffset |
| Decimal |Decimal |
| Atribut FILESTREAM (varbinary(max)) |Byte] |
| Plovoucí desetinná čárka |Dvojitý |
| Bitové kopie |Byte] |
| celá čísla |Int32 |
| peníze |Decimal |
| nchar |Řetězec, Char] |
| ntext |Řetězec, Char] |
| číselné |Decimal |
| nvarchar |Řetězec, Char] |
| skutečné |Jednoduchá |
| ROWVERSION |Byte] |
| smalldatetime |DateTime |
| smallint |Int16 |
| Smallmoney |Decimal |
| SQL_VARIANT |Objekt * |
| Text |Řetězec, Char] |
| time |Časový interval |
| časové razítko |Byte] |
| tinyint |Bajtů |
| Typ UniqueIdentifier |Guid |
| varbinary |Byte] |
| varchar |Řetězec, Char] |
| xml |XML |

## <a name="next-steps"></a>Další postup
Seznam úložišť dat jako zdroje a jímky nepodporuje aktivitu kopírování v Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).