---
title: Kopírování dat do nebo ze služby Azure SQL Database pomocí služby Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data z podporované zdrojové úložiště dat do služby Azure SQL Database nebo SQL Database do úložiště dat jímky podporované pomocí služby Data Factory.
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
ms.date: 05/05/2018
ms.author: jingwang
ms.openlocfilehash: ce3a3d28a25c8e904eeebbfc4cf68003fdda07a5
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2018
ms.locfileid: "42443628"
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>Kopírování dat do nebo ze služby Azure SQL Database s použitím služby Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you use:"]
> * [Verze 1](v1/data-factory-azure-sql-connector.md)
> * [Aktuální verze](connector-azure-sql-database.md)

Tento článek vysvětluje, jak použít aktivitu kopírování ke kopírování dat z nebo do služby Azure SQL Database ve službě Azure Data Factory. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Z nebo do služby Azure SQL Database můžete zkopírovat data do jakékoli podporovaného úložiště dat jímky. A kopírování dat ze všech podporovaných zdrojů úložišť dat do Azure SQL Database. Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování najdete v tématu [podporovaných úložišť dat a formáty](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor Azure SQL Database podporuje tyto funkce:

- Kopírování dat pomocí ověřování SQL a ověřování pomocí tokenu aplikace Azure Active Directory (Azure AD) pomocí instančního objektu služby nebo Identity spravované služby (MSI).
- Jako zdroj načtení dat pomocí jazyka SQL nebo uloženou proceduru.
- Jako jímka připojit data do cílové tabulky nebo vyvolat uloženou proceduru s vlastní logikou během kopírování.

> [!IMPORTANT]
> Pokud zkopírujete data pomocí Azure Data Factory Integration Runtime, nakonfigurujte [brány firewall serveru Azure SQL](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) tak, aby přístup k serveru služby Azure.
> Pokud zkopírujete data s využitím místního prostředí integration runtime, konfigurace brány firewall serveru Azure SQL umožňující odpovídající rozsah IP adres. Tento rozsah obsahuje IP počítače, který se používá k připojení ke službě Azure SQL Database.

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory, které jsou specifické pro konektor služby Azure SQL Database.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro služby Azure SQL Database, která je propojená se podporují tyto vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** musí být vlastnost nastavena na **AzureSqlDatabase**. | Ano |
| připojovací řetězec | Zadejte informace potřebné pro připojení k instanci Azure SQL Database **connectionString** vlastnost. Označte toto pole jako **SecureString** bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| servicePrincipalId | Zadejte ID klienta vaší aplikace. | Ano, pokud používáte ověřování Azure AD se instanční objekt služby. |
| servicePrincipalKey | Zadejte klíč aplikace. Označte toto pole jako **SecureString** bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano, pokud používáte ověřování Azure AD se instanční objekt služby. |
| tenant | Zadejte informace o tenantovi (domény ID tenanta nebo název) v rámci které se nachází vaše aplikace. Načtení podržením ukazatele myši v pravém horním rohu webu Azure portal. | Ano, pokud používáte ověřování Azure AD se instanční objekt služby. |
| connectVia | [Prostředí integration runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Pokud vaše úložiště dat se nachází v privátní síti, můžete použít prostředí Azure Integration Runtime nebo místní prostředí integration runtime. Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. | Ne |

Různými typy ověřování najdete v následujících částech na požadavky a ukázky JSON v uvedeném pořadí:

- [Ověřování SQL](#sql-authentication)
- [Azure ověřování tokenu aplikací AD: instanční objekt služby](#service-principal-authentication)
- [Azure AD aplikace ověřování pomocí tokenu: Identita spravované služby](#managed-service-identity-authentication)

>[!TIP]
>Pokud spuštění chybě s kódem chyby jako "UserErrorFailedToConnectToSqlServer" a zpráva jako "limit relace pro databázi je XXX a bylo ho dosaženo.", přidejte `Pooling=false` připojovací řetězec a zkuste to znovu.

### <a name="sql-authentication"></a>Ověřování pomocí SQL

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Příklad propojené služby, který používá ověřování SQL

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
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

Pokud chcete používat ověřování tokenu aplikací služeb na základě instanční objekt Azure AD, postupujte takto:

1. **[Vytvoření aplikace Azure Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application)**  z portálu Azure portal. Poznamenejte si název aplikace a následující hodnoty, které definují propojené služby:

    - ID aplikace
    - Klíč aplikace
    - ID tenanta

1. **[Zřízení správce Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  pro váš server Azure SQL na portálu Azure portal, pokud jste tak již neučinili. Správce Azure AD musíte mít roli uživatele Azure AD nebo skupině Azure AD, ale nemůže být instančního objektu. Tento krok se provádí tak, aby v dalším kroku, můžete použít Azure AD identity uživatele databáze s omezením pro službu vytvořit instanční objekt.

1. **[Vytvořit uživatele databáze s omezením](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  pro instanční objekt. Připojení k databázi z nebo na který se má ke kopírování dat pomocí nástrojů, jako je SSMS, identity Azure AD, který má alespoň oprávnění ALTER ANY uživatele. Spusťte následující příkaz T-SQL: 
    
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

1. **Udělte nezbytná oprávnění instančního objektu** SQL uživatelů nebo jiné obvyklým způsobem. Spusťte následující kód:

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

1. **Konfigurace služby Azure SQL Database, která je propojená** ve službě Azure Data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Příklad propojené služby, který používá ověřování instančních objektů

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
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

Je možné přidružit datové továrny [identita spravované služby](data-factory-service-identity.md) , která představuje konkrétní datové továrny. Tuto identitu služby můžete použít pro ověřování Azure SQL Database. Přístup k určené objekt pro vytváření a kopírování dat z nebo do databáze s použitím této identity.

Pokud chcete používat ověřování tokenu aplikací Azure AD na základě Instalační služby MSI, postupujte takto:

1. **Vytvoření skupiny ve službě Azure AD.** Nastavte objekt pro vytváření MSI jako člena skupiny.

    a. Najdete identitu služby data factory na webu Azure Portal. Přejděte do služby data factory **vlastnosti**. Zkopírujte ID služby IDENTIT.

    b. Nainstalujte [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) modulu. Přihlaste se pomocí `Connect-AzureAD` příkazu. Spusťte následující příkazy k vytvoření skupiny a přidání služby data factory MSI jako člena.
    ```powershell
    $Group = New-AzureADGroup -DisplayName "<your group name>" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId "<your data factory service identity ID>"
    ```

1. **[Zřízení správce Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  pro váš server Azure SQL na portálu Azure portal, pokud jste tak již neučinili. Správce Azure AD může být skupina Azure AD nebo uživatel Azure AD. Když udělíte roli správce skupiny pomocí MSI, přeskočte kroky 3 a 4. Správce bude mít plný přístup k databázi.

1. **[Vytvořit uživatele databáze s omezením](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  pro skupiny služby Azure AD. Připojení k databázi z nebo na který se má ke kopírování dat pomocí nástrojů, jako je SSMS, identity Azure AD, který má alespoň oprávnění ALTER ANY uživatele. Spusťte následující příkaz T-SQL: 
    
    ```sql
    CREATE USER [your AAD group name] FROM EXTERNAL PROVIDER;
    ```

1. **Udělte skupině Azure AD nezbytná oprávnění** obvyklým způsobem pro uživatele serveru SQL a další. Například spusťte následující kód:

    ```sql
    EXEC sp_addrolemember [role name], [your AAD group name];
    ```

1. **Konfigurace služby Azure SQL Database, která je propojená** ve službě Azure Data Factory.

#### <a name="linked-service-example-that-uses-msi-authentication"></a>Příklad propojené služby, která používá ověřování MSI

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](https://docs.microsoft.com/en-us/azure/data-factory/concepts-datasets-linked-services) článku. Tato část obsahuje seznam vlastností, které podporuje datová sada Azure SQL Database.

Chcete-li kopírovat data z nebo do služby Azure SQL Database, nastavte **typ** vlastnosti datové sady na **AzureSqlTable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** musí být nastavena vlastnost datové sady **AzureSqlTable**. | Ano |
| tableName | Název tabulky nebo zobrazení v instanci Azure SQL Database, která odkazuje propojenou službu. | Ano |

#### <a name="dataset-properties-example"></a>Příklad vlastnosti datové sady

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastnosti podporované službou Azure SQL Database zdroje a jímky.

### <a name="azure-sql-database-as-the-source"></a>Azure SQL Database jako zdroj

Ke zkopírování dat z Azure SQL Database, nastavte **typ** vlastnosti ve zdroji aktivitu kopírování k **SqlSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** musí být nastavena vlastnost zdroje aktivity kopírování **SqlSource**. | Ano |
| sqlReaderQuery | Použijte vlastní dotaz SQL číst data. Příklad: `select * from MyTable`. | Ne |
| sqlReaderStoredProcedureName | Název uložené procedury, která čte data ze zdrojové tabulky. Příkaz SELECT v uložené proceduře musí být poslední příkaz jazyka SQL. | Ne |
| storedProcedureParameters | Parametry pro uloženou proceduru.<br/>Povolené hodnoty jsou páry název nebo hodnota. Názvy a použití malých a velkých parametry musí odpovídat názvům a použití malých a velkých parametrů uložené procedury. | Ne |

### <a name="points-to-note"></a>Odkazuje na mějte na paměti

- Pokud **sqlReaderQuery** je určená pro **SqlSource**, spustí aktivita kopírování tohoto dotazu na zdroji Azure SQL Database se mají získat data. Nebo můžete zadat uloženou proceduru. Zadejte **sqlReaderStoredProcedureName** a **storedProcedureParameters** pokud používá parametry uložené procedury.
- Pokud nezadáte buď **sqlReaderQuery** nebo **sqlReaderStoredProcedureName**, sloupce definované v **struktura** část datové sady JSON se používají pro Sestavte dotaz. `select column1, column2 from mytable` spouští se pro Azure SQL Database. Pokud nemá definici datové sady **struktura**, jsou vybrány všechny sloupce z tabulky.
- Při použití **sqlReaderStoredProcedureName**, stále je třeba zadat fiktivní **tableName** vlastnost v datové sadě JSON.

#### <a name="sql-query-example"></a>Příklad dotazu SQL

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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
                "type": "SqlSource",
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
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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
                "type": "SqlSource",
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

### <a name="azure-sql-database-as-the-sink"></a>Azure SQL Database jako jímka

Chcete-li kopírovat data do služby Azure SQL Database, nastavte **typ** vlastnost v aktivitě kopírování jímky pro **SqlSink**. Následující vlastnosti jsou podporovány v aktivitě kopírování **jímky** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** vlastnost jímky aktivity kopírování musí být nastavena na **SqlSink**. | Ano |
| WriteBatchSize | Vloží data do tabulky SQL, když dosáhne velikosti vyrovnávací paměti **writeBatchSize**.<br/> Je povolená hodnota **celé číslo** (počet řádků). | Ne. Výchozí hodnota je 10000. |
| writeBatchTimeout | Doba čekání pro dávku vložte na dokončení před vypršením časového limitu operace.<br/> Je povolená hodnota **timespan**. Příklad: "00: 30:00" (30 minut). | Ne |
| preCopyScript | Zadejte dotaz SQL pro aktivitu kopírování ke spuštění před zápisem dat do Azure SQL Database. Pouze vyvolá se jednou za kopírování spustit. Tuto vlastnost použijte k vyčištění dat předem. | Ne |
| sqlWriterStoredProcedureName | Název uložené procedury, která definuje, jak použít zdroj dat do cílové tabulky. Příkladem je upsertuje nebo transformovat pomocí vlastní obchodní logikou. <br/><br/>Tuto uloženou proceduru se **za batch**. Pro operace, které pouze spustit jednou a nemají co dělat se zdrojovými daty, použijte `preCopyScript` vlastnost. Příklad operace jsou delete a zkrátit. | Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru.<br/>Povolené hodnoty jsou páry název-hodnota. Názvy a použití malých a velkých parametry musí odpovídat názvům a použití malých a velkých parametrů uložené procedury. | Ne |
| sqlWriterTableType | Zadejte název tabulky typu použitého v uložené proceduře. Aktivitu kopírování, která zpřístupňuje data přesouvá v dočasné tabulce s tímto typem tabulky. Uloženou proceduru kód pak sloučit data kopírovaná s existujícími daty. | Ne |

> [!TIP]
> Při kopírování dat do služby Azure SQL Database, aktivita kopírování připojí data do tabulky jímky ve výchozím nastavení. Pokud chcete provést s funkcí upsert nebo další obchodní logiku, použijte uloženou proceduru v **SqlSink**. Přečtěte si další podrobnosti o [volání uložené procedury SQL jímky](#invoking-stored-procedure-for-sql-sink).

#### <a name="append-data-example"></a>Připojit příklad dat

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

#### <a name="invoke-a-stored-procedure-during-copy-for-upsert-example"></a>Vyvolání uložené procedury během kopírování například upsertovat

Přečtěte si další podrobnosti o [volání uložené procedury SQL jímky](#invoking-stored-procedure-for-sql-sink).

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "sqlWriterTableType": "CopyTestTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="identity-columns-in-the-target-database"></a>Sloupce identity v cílové databázi

V této části se dozvíte, jak kopírovat data ze zdrojové tabulky bez sloupec identity do cílové tabulky se sloupcem identity.

#### <a name="source-table"></a>Zdrojová tabulka

```sql
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```

#### <a name="destination-table"></a>Cílová tabulka

```sql
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```

> [!NOTE]
> Cílová tabulka obsahuje sloupec identity.

#### <a name="source-dataset-json-definition"></a>Zdroj definice JSON datové sady

```json
{
    "name": "SampleSource",
    "properties": {
        "type": " AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTbl"
        }
    }
}
```

#### <a name="destination-dataset-json-definition"></a>Určení definice JSON datové sady

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "TargetTbl"
        }
    }
}
```

> [!NOTE]
> Zdrojové a cílové tabulce mají jiné schéma. 

Cíl obsahuje další sloupec s identitou. V tomto scénáři je nutné zadat **struktura** vlastnost v definici datové sady target, která neobsahuje sloupec identity.

## <a name="invoking-stored-procedure-for-sql-sink"></a> Vyvolání uložené procedury SQL jímky

Při kopírování dat do Azure SQL Database, můžete také nakonfigurovat a vyvolat uloženou proceduru s další parametry zadané uživatelem.

Uloženou proceduru můžete použít, když integrovaná funkce kopírování mechanismy neslouží účel. Se obvykle používá při upsert, insert a update nebo další zpracování je třeba provést před posledním vložení zdrojová data do cílové tabulky. Některé příklady vysokého jsou sloučení sloupců, vyhledat další hodnoty a vložení do více než jednou tabulkou.

Následující příklad ukazuje, jak provést funkcí upsert do tabulky ve službě Azure SQL Database pomocí uložené procedury. Předpokládejme, který vstupní data a jímku **marketingové** tabulka jednotlivých obsahovat tři sloupce: **ProfileID**, **stavu**, a **kategorie**. Proveďte upsert na základě **ProfileID** sloupce a použijte je jenom pro konkrétní kategorie.

#### <a name="output-dataset"></a>Výstupní datová sada

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "Marketing"
        }
    }
}
```

Definovat **SqlSink** části v aktivitě kopírování:

```json
"sink": {
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing",
    "storedProcedureParameters": {
        "category": {
            "value": "ProductA"
        }
    }
}
```

V databázi, definovat uložená procedura se stejným názvem jako **SqlWriterStoredProcedureName**. Zpracovává vstupní data ze zadaného zdroje a sloučí do výstupní tabulky. Název parametru typu tabulky v uložené proceduře by měl být stejný jako **tableName** definované v datové sadě.

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
AS
BEGIN
  MERGE [dbo].[Marketing] AS target
  USING @Marketing AS source
  ON (target.ProfileID = source.ProfileID and target.Category = @category)
  WHEN MATCHED THEN
      UPDATE SET State = source.State
  WHEN NOT MATCHED THEN
      INSERT (ProfileID, State, Category)
      VALUES (source.ProfileID, source.State, source.Category)
END
```

V databázi, definujte typ tabulky se stejným názvem jako **sqlWriterTableType**. Schéma typu tabulky musí být stejná jako schéma vrácené funkcí vstupní data.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL,
    [Category] [varchar](256) NOT NULL,
)
```

Uložená procedura funkce využívá možnosti [Table-Valued parametry](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="data-type-mapping-for-azure-sql-database"></a>Mapování datového typu pro službu Azure SQL Database

Při kopírování dat z nebo do služby Azure SQL Database, se používají následující mapování z datových typů v databázi SQL Azure do služby Azure Data Factory dočasné datových typů. Zobrazit [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) se dozvíte, jak aktivitu kopírování, která mapuje typ zdroje schéma a data na jímce.

| Azure SQL Database datový typ | Data Factory dočasné datový typ |
|:--- |:--- |
| bigint |Int64 |
| Binární |Byte] |
| Bit |Logická hodnota |
| Char |Řetězec, Char] |
| datum |DateTime |
| Datum a čas |DateTime |
| datetime2 |DateTime |
| DateTimeOffset |DateTimeOffset |
| Decimal |Decimal |
| Atribut FILESTREAM (varbinary(max)) |Byte] |
| Float |Double |
| image |Byte] |
| int |Datový typ Int32 |
| peníze |Decimal |
| nchar |Řetězec, Char] |
| ntext |Řetězec, Char] |
| Číselné |Decimal |
| nvarchar |Řetězec, Char] |
| Real |Jednoduchá |
| ROWVERSION |Byte] |
| smalldatetime |DateTime |
| smallint |Int16 |
| Smallmoney |Decimal |
| SQL_VARIANT |Objekt * |
| text |Řetězec, Char] |
| time |Časový interval |
| časové razítko |Byte] |
| tinyint |Bajt |
| UniqueIdentifier |Guid |
| varbinary |Byte] |
| varchar |Řetězec, Char] |
| xml |XML |

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky, aktivita kopírování ve službě Azure Data Factory najdete v tématu [podporovaných úložišť dat a formáty](copy-activity-overview.md##supported-data-stores-and-formats).
