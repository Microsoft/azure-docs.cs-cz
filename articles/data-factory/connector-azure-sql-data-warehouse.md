---
title: Kopírování dat do a z Azure SQL Data Warehouse pomocí Azure Data Factory | Microsoft Docs
description: Zjistěte, jak zkopírovat data z podporované zdrojové úložiště do Azure SQL Data Warehouse nebo z SQL Data Warehouse na podporované podřízený úložiště pomocí služby Data Factory.
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
ms.date: 05/28/2018
ms.author: jingwang
ms.openlocfilehash: 5a7ee7862e102093efa2c203eac2497b025af4e5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "36337818"
---
#  <a name="copy-data-to-or-from-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Kopírovat data do nebo z Azure SQL Data Warehouse pomocí Azure Data Factory 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you're using:"]
> * [Verze 1: GA](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Verze 2: verze Preview](connector-azure-sql-data-warehouse.md)

Tento článek vysvětluje, jak pomocí aktivity kopírování v Azure Data Factory ke zkopírování dat do nebo z Azure SQL Data Warehouse. Vychází [aktivity kopírování přehled](copy-activity-overview.md) článek, který představuje obecný přehled aktivity kopírování.

> [!NOTE]
> Tento článek se týká 2 objektu pro vytváření dat aktuálně ve verzi preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), najdete v části [konektor Azure SQL Data Warehouse v V1](v1/data-factory-azure-sql-data-warehouse-connector.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Z Azure SQL Data Warehouse může kopírovat data do úložiště dat žádné podporované jímky. A můžete zkopírovat data z úložiště dat žádné podporované zdrojové do Azure SQL Data Warehouse. Seznam úložišť dat, které jsou podporované jako zdroje nebo jímky aktivitou kopírování najdete v tématu [podporované úložiště dat a formáty](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor Azure SQL Data Warehouse podporuje tyto funkce:

- Kopírování dat pomocí ověřování SQL a ověřování tokenem aplikaci Azure Active Directory (Azure AD) s instanční objekt nebo spravovat Identity služby (MSI).
- Jako zdroj načtení dat pomocí dotazu SQL nebo uloženou proceduru.
- Jako jímku načtení dat pomocí funkce PolyBase nebo hromadného vložení. Doporučujeme, abyste PolyBase pro lepší výkon kopírování.

> [!IMPORTANT]
> Všimněte si, že PolyBase podporuje jenom ověřování SQL, ale ověřování není Azure AD.

> [!IMPORTANT]
> Pokud zkopírujete data pomocí Azure Data Factory integrace Runtime, nakonfigurovat [brány firewall serveru Azure SQL](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) tak, aby službám Azure přístup k serveru.
> Pokud zkopírujete data pomocí integrace s vlastním hostováním runtime, nakonfigurujte bránu firewall serveru Azure SQL umožňující odpovídající rozsah IP adres. Tento rozsah obsahuje IP počítače, který se používá k připojení k databázi SQL Azure.

## <a name="get-started"></a>Začínáme

> [!TIP]
> K dosažení nejlepšího výkonu, použijte k načtení dat do Azure SQL Data Warehouse PolyBase. [PolyBase používá k načtení dat do Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) obsahuje podrobnosti. Návod s případu použití najdete v tématu [načíst 1 TB do Azure SQL Data Warehouse pomocí Azure Data Factory v části 15 minut](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnosti, které definují entit služby Data Factory konkrétní do konektoru služby Azure SQL Data Warehouse.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro službu Azure SQL Data Warehouse propojené jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu musí být nastavená na **AzureSqlDW**. | Ano |
| připojovací řetězec | Zadejte informace potřebné pro připojení k instanci Azure SQL Data Warehouse **connectionString** vlastnost. Označit jako toto pole **SecureString** bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| servicePrincipalId | Zadejte ID aplikace klienta. | Ano, při použití ověřování Azure AD pomocí objektu služby. |
| servicePrincipalKey | Zadejte klíč aplikace. Toto pole označit jako SecureString bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). | Ano, při použití ověřování Azure AD pomocí objektu služby. |
| tenant | Zadejte informace o klienta (název nebo klienta domény ID) v rámci které se nachází aplikace. Můžete ji načíst podržením ukazatele myši v pravém horním rohu portálu Azure. | Ano, při použití ověřování Azure AD pomocí objektu služby. |
| connectVia | [Integrace runtime](concepts-integration-runtime.md) který se má použít pro připojení k úložišti. Můžete použít Runtime integrace Azure nebo vlastním hostováním integrace runtime (Pokud je vaše úložiště dat se nachází v privátní síti). Pokud není zadaný, použije výchozí Runtime integrace Azure. | Ne |

Různými typy ověřování najdete v následujících částech na požadavky a ukázky JSON, v uvedeném pořadí:

- [Ověřování SQL.](#sql-authentication)
- Azure AD tokenu ověřování aplikace: [instančního objektu](#service-principal-authentication)
- Azure AD tokenu ověřování aplikace: [identita spravované služby](#managed-service-identity-authentication)

### <a name="sql-authentication"></a>Ověřování pomocí SQL

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Příklad propojené služby, který používá ověřování SQL.

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

Token ověřování služby Azure AD na základě hlavní aplikace, postupujte podle těchto kroků:

1. **[Vytvoření aplikace Azure Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application)**  z portálu Azure. Poznamenejte si název aplikace a následující hodnoty, které definují propojené služby:

    - ID aplikace
    - Klíč aplikace
    - ID tenanta

2. **[Zřízení správcem služby Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  pro server Azure SQL na portálu Azure, pokud jste tak již neučinili. Správce Azure AD může být Azure AD uživatele nebo skupinu Azure AD. Pokud byste udělit skupině s MSI roli správce, přeskočte kroky 3 a 4. Správce bude mít plný přístup k databázi.

3. **[Vytvořit uživatele databáze s omezením](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  pro objekt služby. Připojení do datového skladu z nebo na které chcete zkopírovat data pomocí nástroje, například aplikace SSMS, s identitou služby Azure AD, která má alespoň oprávnění ALTER ANY uživatele. Spusťte následující T-SQL:
    
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Udělte nezbytná oprávnění objektu služby** pro uživatele SQL nebo jiné obvyklým způsobem. Spusťte následující kód:

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. **Konfigurace služby Azure SQL Data Warehouse propojené** v Azure Data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Příklad propojené služby, který používá objekt zabezpečení ověřování služby

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

### <a name="managed-service-identity-authentication"></a>Spravované ověření Identity služby

Objekt pro vytváření dat může být přidružený [identita spravované služby](data-factory-service-identity.md) představující konkrétní objekt pro vytváření. Tato identita služby můžete použít pro ověřování Azure SQL Data Warehouse. Přístup k určené objekt pro vytváření a kopírování dat z nebo na vaše data skladu pomocí tuto identitu.

> [!IMPORTANT]
> Všimněte si, že PolyBase není aktuálně podporovaná MSI ověřování.

Ověření pomocí tokenu aplikace založené na Instalační služby MSI Azure AD, postupujte podle těchto kroků:

1. **Vytvoření skupiny ve službě Azure AD.** Zkontrolujte objekt pro vytváření MSI členem skupiny.

    a. Vyhledejte identitu služby objektu pro vytváření dat z portálu Azure. Přejděte do data factory **vlastnosti**. Zkopírujte ID. IDENTITY služby

    b. Nainstalujte [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) modulu. Přihlaste se pomocí `Connect-AzureAD` příkaz. Spusťte následující příkazy a vytvořte skupinu a přidejte objekt pro vytváření dat MSI jako člena.
    ```powershell
    $Group = New-AzureADGroup -DisplayName "<your group name>" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId "<your data factory service identity ID>"
    ```

2. **[Zřízení správcem služby Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  pro server Azure SQL na portálu Azure, pokud jste tak již neučinili.

3. **[Vytvořit uživatele databáze s omezením](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  pro skupinu Azure AD. Připojení do datového skladu z nebo na které chcete zkopírovat data pomocí nástroje, například aplikace SSMS, s identitou služby Azure AD, která má alespoň oprávnění ALTER ANY uživatele. Spusťte následující T-SQL. 
    
    ```sql
    CREATE USER [your Azure AD group name] FROM EXTERNAL PROVIDER;
    ```

4. **Udělit skupině Azure AD nezbytná oprávnění** pro uživatele SQL a jiné obvyklým způsobem. Například spusťte následující kód.

    ```sql
    EXEC sp_addrolemember [role name], [your Azure AD group name];
    ```

5. **Konfigurace služby Azure SQL Data Warehouse propojené** v Azure Data Factory.

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datové sady](https://docs.microsoft.com/en-us/azure/data-factory/concepts-datasets-linked-services) článku. Tato část obsahuje seznam vlastností, které podporuje Azure SQL Data Warehouse datovou sadu.

Chcete-li kopírovat data z nebo do Azure SQL Data Warehouse, nastavte **typ** vlastnosti datové sady, která **AzureSqlDWTable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** vlastnost datovou sadu musí být nastavena na **AzureSqlDWTable**. | Ano |
| tableName | Název tabulky nebo zobrazení instance Azure SQL Data Warehouse na kterou odkazuje propojená služba. | Ano |

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

## <a name="copy-activity-properties"></a>Zkopírovat vlastnosti aktivit

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností, které podporuje Azure SQL Data Warehouse zdroj a jímka.

### <a name="azure-sql-data-warehouse-as-the-source"></a>Azure SQL Data Warehouse jako zdroj

Ke zkopírování dat z Azure SQL Data Warehouse, nastavte **typ** vlastnost v aktivitě kopírování zdroji **SqlDWSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** vlastnost aktivity kopírování zdroje musí být nastavená na **SqlDWSource**. | Ano |
| sqlReaderQuery | Čtení dat pomocí vlastního dotazu SQL. Příklad: `select * from MyTable`. | Ne |
| sqlReaderStoredProcedureName | Název uložené procedury, který čte data ze zdrojové tabulky. Poslední příkaz jazyka SQL musí být příkaz SELECT v uložené proceduře. | Ne |
| storedProcedureParameters | Parametry pro uloženou proceduru.<br/>Povolené hodnoty jsou páry název nebo hodnota. Názvy a malá a velká písmena parametry musí odpovídat názvům a malá a velká písmena parametry uložené procedury. | Ne |

### <a name="points-to-note"></a>Odkazuje na Poznámka:

- Pokud **sqlReaderQuery** je zadán pro **SqlSource**, spuštění aktivity kopírování tohoto dotazu na zdroji Azure SQL Data Warehouse se získat data. Nebo můžete zadat uložené procedury. Zadejte **sqlReaderStoredProcedureName** a **storedProcedureParameters** Pokud uložená procedura používá parametry.
- Pokud nezadáte buď **sqlReaderQuery** nebo **sqlReaderStoredProcedureName**, sloupce definované v **struktura** části datové sady JSON se používají pro Vytvořte dotaz. `select column1, column2 from mytable` spouští Azure SQL Data Warehouse. Pokud nemá definici datové sady **struktura**, jsou vybrány všechny sloupce z tabulky.
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

### <a name="azure-sql-data-warehouse-as-sink"></a> Azure SQL Data Warehouse jako jímku

Ke zkopírování dat do Azure SQL Data Warehouse, nastavte typ jímky v aktivitě kopírování do **SqlDWSink**. Následující vlastnosti jsou podporovány v aktivitě kopírování **podřízený** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** vlastnost jímky aktivity kopírování musí být nastavena na **SqlDWSink**. | Ano |
| allowPolyBase | Označuje, zda pomocí funkce PolyBase, v případě potřeby místo mechanismus hromadné vložení. <br/><br/> Doporučujeme, abyste načtení dat do SQL Data Warehouse pomocí PolyBase. Najdete v článku [PolyBase používá k načtení dat do Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) části omezení a podrobnosti.<br/><br/>Povolené hodnoty jsou **True** a **False** (výchozí).  | Ne |
| polyBaseSettings | Skupinu vlastností, které se dají zadat při **allowPolybase** je nastavena na **true**. | Ne |
| rejectValue | Určuje číslo nebo podíl řádků, které může být odmítnutá předtím, než se dotaz nezdaří.<br/><br/>Další informace o možnostech odmítněte PolyBase společnosti v části argumenty [vytvořit EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). <br/><br/>Povolené hodnoty jsou 0 (výchozí), 1, 2, atd. |Ne |
| rejectType | Určuje, zda **rejectValue** možnost je literálovou hodnotou nebo v procentech.<br/><br/>Povolené hodnoty jsou **hodnotu** (výchozí) a **procento**. | Ne |
| rejectSampleValue | Určuje počet řádků načíst před PolyBase přepočítá procento odmítnutých řádků.<br/><br/>Povolené hodnoty jsou 1, 2, atd. | Ano, pokud **rejectType** je **procento**. |
| useTypeDefault | Určuje způsob zpracování chybějící hodnoty v textových souborů s oddělovači, když PolyBase načítá data z textového souboru.<br/><br/>Další informace o této vlastnosti v části argumenty [vytvořit EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Povolené hodnoty jsou **True** a **False** (výchozí). | Ne |
| writeBatchSize | Vloží data do tabulky SQL, když velikost vyrovnávací paměti dosáhne **writeBatchSize**. Platí, pouze pokud se nepoužívá PolyBase.<br/><br/>Povolená hodnota je **celé číslo** (počet řádků). | Ne. Výchozí hodnota je 10 000. |
| writeBatchTimeout | Počkejte, než čas na dokončení předtím, než vyprší časový limit operace vložení dávky. Platí, pouze pokud se nepoužívá PolyBase.<br/><br/>Povolená hodnota je **časový interval**. Příklad: "00: 30:00" (30 minut). | Ne |
| preCopyScript | Zadejte dotaz SQL pro aktivitu kopírování ke spuštění před zápis dat do Azure SQL Data Warehouse při každém spuštění. Pomocí této vlastnosti můžete vyčistit předem načtené data. | Ne | (#repeatability během kopírování). | Příkaz dotazu. | Ne |

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

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Použijte PolyBase k načtení dat do Azure SQL Data Warehouse

Pomocí [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) je účinný způsob, jak načíst větší množství dat do Azure SQL Data Warehouse s vysokou propustností. Velký nárůst v propustnost zobrazí se místo výchozího mechanismu hromadné vložení pomocí PolyBase. V tématu [referenční dokumentace výkonu](copy-activity-performance.md#performance-reference) pro podrobné porovnání. Návod s případu použití najdete v tématu [načíst 1 TB do Azure SQL Data Warehouse](https://docs.microsoft.com/en-us/azure/data-factory/v1/data-factory-load-sql-data-warehouse).

* Pokud je zdroj dat v Azure Blob storage nebo Azure Data Lake Store a formát je kompatibilní s funkcí PolyBase, kopie přímo do Azure SQL Data Warehouse pomocí PolyBase. Podrobnosti najdete v tématu  **[směrovat kopírování pomocí PolyBase](#direct-copy-by-using-polybase)**.
* Pokud vaše zdrojového úložiště dat a formát není podporován původně polybase, použijte **[připravené kopírování pomocí PolyBase](#staged-copy-by-using-polybase)** místo toho funkci. Funkce dvoufázové instalace kopie také poskytuje lepší propustnosti. Automaticky převede data na formát kompatibilní s funkcí PolyBase. A ukládá data do úložiště Azure Blob. Potom načte data do SQL Data Warehouse.

> [!IMPORTANT]
> Všimněte si, že PolyBase není aktuálně podporovaná tokenu ověřování na základě MSI aplikace Azure AD.

### <a name="direct-copy-by-using-polybase"></a>Přímé kopírovat pomocí PolyBase

SQL Data Warehouse PolyBase přímo podporuje objektů Blob v Azure a Azure Data Lake Store. Používá instanční objekt jako zdroj a má formát požadavky na konkrétní soubor. Pokud zdrojová data splňuje kritéria popsaných v této části, zkopírujte přímo ze zdrojového úložiště dat do Azure SQL Data Warehouse pomocí PolyBase. Jinak použijte [připravené kopírování pomocí PolyBase](#staged-copy-by-using-polybase).

> [!TIP]
> Chcete-li zkopírovat efektivně dat z Data Lake Store k SQL Data Warehouse, zjistěte další informace z [Azure Data Factory usnadňuje i a pohodlný k odhalení přehled o datech, při použití Data Lake Store s SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Pokud požadavky nejsou splněny, zkontroluje nastavení Azure Data Factory a automaticky se vrátí k hromadné vložení mechanismus pro přesun dat.

1. **Zdroj propojená služba** typ je **azurestorage** nebo **AzureDataLakeStore** s objekt zabezpečení ověřování služby.
2. **Vstupní datové sady** typ je **AzureBlob** nebo **AzureDataLakeStoreFile**. Typ formátu pod `type` vlastnosti je **OrcFormat**, **ParquetFormat**, nebo **TextFormat**, s následující konfigurace:

   1. `rowDelimiter` musí být **\n**.
   2. `nullValue` je nastavena na **prázdný řetězec** (""), nebo `treatEmptyAsNull` je nastaven na **true**.
   3. `encodingName` je nastavena na **znakové sady utf-8**, což je výchozí hodnota.
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
4. Neexistuje žádné `sliceIdentifierColumnName` nastavení v části **SqlDWSink** pro aktivitu kopírování v kanálu. PolyBase zaručuje všechna data se aktualizují, nebo nic se aktualizuje v jednom spustit. K dosažení **opakovatelnosti**, použijte `sqlWriterCleanupScript`.

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

### <a name="staged-copy-by-using-polybase"></a>Dvoufázové instalace kopírovat pomocí PolyBase

Pokud vaše zdrojová data nesplňuje kritéria v předchozí části, povolte data kopírování prostřednictvím dočasné pracovní instance úložiště objektů Blob v Azure. Storage úrovně Premium nemůže být. V takovém případě Azure Data Factory automaticky spustí transformace na data, která mají vyhověli požadavkům formátu dat PolyBase. Pak PolyBase používá k načtení dat do SQL Data Warehouse. Nakonec vyčistí dočasný dat z úložiště objektů blob. V tématu [připravený kopie](copy-activity-performance.md#staged-copy) podrobnosti o kopírování dat prostřednictvím pracovní instance úložiště objektů Blob v Azure.

Chcete-li tuto funkci používat, vytvořte [propojená služba Azure Storage](connector-azure-blob-storage.md#linked-service-properties) který odkazuje na účet úložiště Azure s dočasné blob storage. Zadejte `enableStaging` a `stagingSettings` vlastnosti pro aktivitu kopírování, jak je znázorněno v následujícím kódu:

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

Následující části obsahují osvědčené postupy kromě oprávnění uvedených v [osvědčené postupy pro Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Oprávnění požadovaná databáze

Pokud chcete používat funkce PolyBase, musí mít uživatel, který načte data do SQL Data Warehouse [oprávnění "Řízení"](https://msdn.microsoft.com/library/ms191291.aspx) na cílovou databázi. Je možné dosáhnout, přidejte uživatele jako člena **db_owner** role. Zjistěte, jak to udělat [přehled SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limits"></a>Velikost řádku a datový typ omezení

PolyBase zatížení jsou omezeny na řádky, které jsou menší než 1 MB. Se nedá načíst VARCHR(MAX), NVARCHAR(MAX) nebo VARBINARY(MAX). Další informace najdete v tématu [omezení kapacity služby SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Pokud vaše zdrojová data obsahují řádky větší než 1 MB, můžete chtít svisle rozdělí na několik malých ty zdrojové tabulky. Zkontrolujte, že největší velikost každý řádek nepřekročí limit. Menší tabulky můžete pak načíst pomocí funkce PolyBase a sloučí v Azure SQL Data Warehouse.

### <a name="sql-data-warehouse-resource-class"></a>Třída prostředků SQL Data Warehouse

K dosažení nejlepší možné propustnost, přiřadíte uživatele, který načte data do SQL Data Warehouse pomocí PolyBase větší Třída prostředků.

### <a name="tablename-in-azure-sql-data-warehouse"></a>**Název tabulky** v Azure SQL Data Warehouse

V následující tabulce jsou uvedeny příklady určení **tableName** vlastnost v datové sadě JSON. Zobrazuje několik kombinace názvů schéma a tabulku.

| Schéma databáze | Název tabulky | **Název tabulky** vlastnost JSON |
| --- | --- | --- |
| dbo | MyTable | MyTable nebo dbo. MyTable nebo [dbo]. [Tabulka] |
| dbo1 | MyTable | dbo1. MyTable nebo [dbo1]. [Tabulka] |
| dbo | My.Table | [My.Table] nebo [dbo]. [My.Table] |
| dbo1 | My.Table | [dbo1]. [My.Table] |

Pokud se zobrazí chybová zpráva, problém, může být hodnota zadaná pro **tableName** vlastnost. Najdete v předchozí tabulce správný způsob, jak určit hodnoty **tableName** vlastnost JSON.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Sloupce s výchozími hodnotami

V současné době funkce PolyBase v datové továrně přijímá pouze stejný počet sloupců jako v cílové tabulce. Příkladem je tabulka s čtyři sloupce, kde jeden z nich je definován výchozí hodnotu. Vstupní data stále musí mít čtyři sloupce. Vstupní datové sady tři sloupce vypočítá chyba podobná následující zpráva:

```
All columns of the table must be specified in the INSERT BULK statement.
```

Hodnota NULL je speciální formu výchozí hodnota. Pokud je sloupec s možnou hodnotou Null, vstupní data v objektu blob pro tento sloupec může být prázdná. Ale nemůže být chybí vstupní datové sady. PolyBase vloží hodnotu NULL pro chybějící hodnoty v Azure SQL Data Warehouse.

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Mapování datového typu pro Azure SQL Data Warehouse

Při kopírování dat z nebo do Azure SQL Data Warehouse, se používají následující mapování z Azure SQL Data Warehouse datové typy k Azure Data Factory dočasné datové typy. V tématu [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) se dozvíte, jak aktivity kopírování mapuje zdroje schéma a data typ jímky.

| Azure SQL Data Warehouse datový typ | Typ průběžných dat objektu pro vytváření dat |
|:--- |:--- |
| bigint | Int64 |
| binární | Byte] |
| Bit | Logická hodnota |
| Char | Řetězec, Char] |
| datum | DateTime |
| Datum a čas | DateTime |
| datetime2 | DateTime |
| Datový typ DateTimeOffset | Datový typ DateTimeOffset |
| Decimal | Decimal |
| Atribut FILESTREAM (varbinary(max)) | Byte] |
| Float | Double |
| image | Byte] |
| celá čísla | Int32 |
| peníze | Decimal |
| nchar | Řetězec, Char] |
| ntext | Řetězec, Char] |
| číselné | Decimal |
| nvarchar | Řetězec, Char] |
| skutečné | Jednoduchá |
| ROWVERSION | Byte] |
| smalldatetime | DateTime |
| smallint | Int16 |
| Smallmoney | Decimal |
| SQL_VARIANT | Objekt * |
| text | Řetězec, Char] |
| time | Časový interval |
| časové razítko | Byte] |
| tinyint | Bajt |
| Typ UniqueIdentifier | Guid |
| varbinary | Byte] |
| varchar | Řetězec, Char] |
| xml | XML |

## <a name="next-steps"></a>Další postup
Seznam úložišť dat, které jsou podporované jako zdroje a jímky aktivitou kopírování v Azure Data Factory najdete v tématu [podporované úložiště dat a formáty](copy-activity-overview.md##supported-data-stores-and-formats).
