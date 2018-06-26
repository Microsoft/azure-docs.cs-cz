---
title: Kopírovat data do nebo z Azure SQL Database pomocí služby Data Factory | Microsoft Docs
description: Zjistěte, jak zkopírovat data z podporované zdrojové úložiště dat do Azure SQL Database nebo SQL Database k úložištím dat. podporované podřízený pomocí služby Data Factory.
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
ms.openlocfilehash: 9b2acf622f33f5d1748c503ab4765b72c3d921e2
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751574"
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>Kopírovat data do nebo z Azure SQL Database pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you use:"]
> * [Verze 1, GA](v1/data-factory-azure-sql-connector.md)
> * [Verze 2, verze Preview](connector-azure-sql-database.md)

Tento článek vysvětluje, jak pomocí aktivity kopírování v Azure Data Factory ke zkopírování dat z nebo do Azure SQL Database. Vychází [aktivity kopírování přehled](copy-activity-overview.md) článek, který poskytne obecný přehled aktivity kopírování.

> [!NOTE]
> Tento článek se týká 2 objektu pro vytváření dat aktuálně ve verzi preview. Pokud používáte verzi 1 služby Data Factory, všeobecně dostupná (GA), najdete v části [konektor Azure SQL Database v V1](v1/data-factory-azure-sql-connector.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Z nebo do Azure SQL Database můžete zkopírovat data do úložiště dat žádné podporované jímky. A může kopírovat data z jakékoli úložiště podporované zdroje dat do Azure SQL Database. Seznam úložišť dat, které jsou podporované jako zdroje nebo jímky aktivitou kopírování najdete v tématu [podporované úložiště dat a formáty](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor Azure SQL Database podporuje tyto funkce:

- Kopírování dat pomocí ověřování SQL a ověřování tokenem aplikaci Azure Active Directory (Azure AD) s instanční objekt nebo spravovat Identity služby (MSI).
- Jako zdroj načtení dat pomocí dotazu SQL nebo uloženou proceduru.
- Jako jímku připojovat data do cílové tabulky nebo vyvolat uloženou proceduru s vlastní logikou během kopírování.

> [!IMPORTANT]
> Pokud zkopírujete data pomocí Azure Data Factory integrace Runtime, nakonfigurovat [brány firewall serveru Azure SQL](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) tak, aby službám Azure přístup k serveru.
> Pokud zkopírujete data pomocí integrace s vlastním hostováním runtime, nakonfigurujte bránu firewall serveru Azure SQL umožňující odpovídající rozsah IP adres. Tento rozsah obsahuje IP počítače, který se používá k připojení k databázi SQL Azure.

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnosti, které slouží k určení entit služby Data Factory konkrétní do konektoru služby Azure SQL Database.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Tyto vlastnosti jsou podporovány pro službu Azure SQL Database propojené:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** musí být nastavena na **azuresqldatabase**. | Ano |
| připojovací řetězec | Zadejte informace potřebné pro připojení k instanci databáze SQL Azure pro **connectionString** vlastnost. Označit jako toto pole **SecureString** bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| servicePrincipalId | Zadejte ID aplikace klienta. | Ano, při použití ověřování Azure AD pomocí objektu služby. |
| servicePrincipalKey | Zadejte klíč aplikace. Označit jako toto pole **SecureString** bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). | Ano, při použití ověřování Azure AD pomocí objektu služby. |
| tenant | Zadejte informace o klienta (název nebo klienta domény ID) v rámci které se nachází aplikace. Načtěte ho podržením ukazatele myši v pravém horním rohu portálu Azure. | Ano, při použití ověřování Azure AD pomocí objektu služby. |
| connectVia | [Integrace runtime](concepts-integration-runtime.md) který se má použít pro připojení k úložišti. Pokud se data store nachází v privátní síti, můžete použít Runtime integrace Azure nebo vlastním hostováním integrace runtime. Pokud není zadaný, použije výchozí Runtime integrace Azure. | Ne |

Různými typy ověřování najdete v následujících částech na požadavky a ukázky JSON, v uvedeném pořadí:

- [Ověřování SQL.](#sql-authentication)
- [Azure AD tokenu ověřování aplikace: instančního objektu](#service-principal-authentication)
- [Azure AD tokenu ověřování aplikace: Identita spravované služby](#managed-service-identity-authentication)

### <a name="sql-authentication"></a>Ověřování pomocí SQL

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Příklad propojené služby, který používá ověřování SQL.

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

Ověření tokenu služby Azure AD na základě hlavní aplikace, postupujte podle těchto kroků:

1. **[Vytvoření aplikace Azure Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application)**  z portálu Azure. Poznamenejte si název aplikace a následující hodnoty, které definují propojené služby:

    - ID aplikace
    - Klíč aplikace
    - ID tenanta

2. **[Zřízení správcem služby Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  pro server Azure SQL na portálu Azure, pokud jste tak již neučinili. Správce Azure AD musí to být uživatele Azure AD nebo skupiny Azure Active Directory, ale nemůže být hlavní název služby. Tento krok se provádí tak, aby v dalším kroku, můžete použít Azure AD identity a vytvořte uživatele databáze s omezením pro službu objektu zabezpečení.

3. **[Vytvořit uživatele databáze s omezením](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  pro objekt služby. Připojení k databázi z nebo na které chcete zkopírovat data pomocí nástroje, například aplikace SSMS, s identitou služby Azure AD, která má alespoň oprávnění ALTER ANY uživatele. Spusťte následující T-SQL: 
    
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Udělte nezbytná oprávnění objektu služby** pro uživatele SQL nebo jiné obvyklým způsobem. Spusťte následující kód:

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. **Konfigurace služby Azure SQL Database propojené** v Azure Data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Příklad propojené služby, který používá objekt zabezpečení ověřování služby

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

### <a name="managed-service-identity-authentication"></a>Spravované ověření Identity služby

Objekt pro vytváření dat může být přidružený [identita spravované služby](data-factory-service-identity.md) představující konkrétní datové továrně. Tato identita služby můžete použít pro ověřování Azure SQL Database. Určený objekt factory přístup a kopírování dat z nebo k vaší databázi pomocí tuto identitu.

Ověření pomocí tokenu aplikace založené na Instalační služby MSI Azure AD, postupujte podle těchto kroků:

1. **Vytvoření skupiny ve službě Azure AD.** Zkontrolujte objekt pro vytváření MSI členem skupiny.

    a. Vyhledejte identitu služby objektu pro vytváření dat z portálu Azure. Přejděte do data factory **vlastnosti**. Zkopírujte ID. IDENTITY služby

    b. Nainstalujte [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) modulu. Přihlaste se pomocí `Connect-AzureAD` příkaz. Spusťte následující příkazy a vytvořte skupinu a přidejte objekt pro vytváření dat MSI jako člena.
    ```powershell
    $Group = New-AzureADGroup -DisplayName "<your group name>" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId "<your data factory service identity ID>"
    ```

2. **[Zřízení správcem služby Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  pro server Azure SQL na portálu Azure, pokud jste tak již neučinili. Správce Azure AD může být Azure AD uživatele nebo skupinu Azure AD. Pokud byste udělit skupině s MSI roli správce, přeskočte kroky 3 a 4. Správce bude mít plný přístup k databázi.

3. **[Vytvořit uživatele databáze s omezením](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  pro skupinu Azure AD. Připojení k databázi z nebo na které chcete zkopírovat data pomocí nástroje, například aplikace SSMS, s identitou služby Azure AD, která má alespoň oprávnění ALTER ANY uživatele. Spusťte následující T-SQL: 
    
    ```sql
    CREATE USER [your AAD group name] FROM EXTERNAL PROVIDER;
    ```

4. **Udělit skupině Azure AD nezbytná oprávnění** pro uživatele SQL a jiné obvyklým způsobem. Například spusťte následující kód:

    ```sql
    EXEC sp_addrolemember [role name], [your AAD group name];
    ```

5. **Konfigurace služby Azure SQL Database propojené** v Azure Data Factory.

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datové sady](https://docs.microsoft.com/en-us/azure/data-factory/concepts-datasets-linked-services) článku. Tato část obsahuje seznam vlastností, které podporuje Azure SQL Database datovou sadu.

Chcete-li kopírovat data z nebo do Azure SQL Database, nastavte **typ** vlastnosti datové sady, která **AzureSqlTable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** vlastnost datovou sadu musí být nastavena na **AzureSqlTable**. | Ano |
| tableName | Název tabulky nebo zobrazení instance Azure SQL Database na kterou odkazuje propojená služba. | Ano |

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

## <a name="copy-activity-properties"></a>Zkopírovat vlastnosti aktivit

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností, které podporuje Azure SQL Database zdroj a jímka.

### <a name="azure-sql-database-as-the-source"></a>Azure SQL Database jako zdroj

Ke zkopírování dat z Azure SQL Database, nastavte **typ** vlastnost v aktivitě kopírování zdroji **SqlSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** vlastnost aktivity kopírování zdroje musí být nastavená na **SqlSource**. | Ano |
| sqlReaderQuery | Čtení dat pomocí vlastního dotazu SQL. Příklad: `select * from MyTable`. | Ne |
| sqlReaderStoredProcedureName | Název uložené procedury, který čte data ze zdrojové tabulky. Poslední příkaz jazyka SQL musí být příkaz SELECT v uložené proceduře. | Ne |
| storedProcedureParameters | Parametry pro uloženou proceduru.<br/>Povolené hodnoty jsou páry název nebo hodnota. Názvy a malá a velká písmena parametry musí odpovídat názvům a malá a velká písmena parametry uložené procedury. | Ne |

### <a name="points-to-note"></a>Odkazuje na Poznámka:

- Pokud **sqlReaderQuery** je zadán pro **SqlSource**, aktivity kopírování spouští tento dotaz zdrojů databáze SQL Azure a získat data. Nebo můžete zadat uložené procedury. Zadejte **sqlReaderStoredProcedureName** a **storedProcedureParameters** Pokud uložená procedura používá parametry.
- Pokud nezadáte buď **sqlReaderQuery** nebo **sqlReaderStoredProcedureName**, sloupce definované v **struktura** části datové sady JSON se používají pro Vytvořte dotaz. `select column1, column2 from mytable` Spustí proti databázi SQL Azure. Pokud nemá definici datové sady **struktura**, jsou vybrány všechny sloupce z tabulky.
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

Chcete-li kopírovat data do Azure SQL Database, nastavte **typ** vlastnost v aktivitě kopírování jímky k **SqlSink**. Následující vlastnosti jsou podporovány v aktivitě kopírování **podřízený** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** vlastnost jímky aktivity kopírování musí být nastavena na **SqlSink**. | Ano |
| writeBatchSize | Vloží data do tabulky SQL, když velikost vyrovnávací paměti dosáhne **writeBatchSize**.<br/> Povolená hodnota je **celé číslo** (počet řádků). | Ne. Výchozí hodnota je 10 000. |
| writeBatchTimeout | Doba čekání pro dávku vložte na dokončeno předtím, než vyprší časový limit operace.<br/> Povolená hodnota je **časový interval**. Příklad: "00: 30:00" (30 minut). | Ne |
| preCopyScript | Zadejte dotaz SQL pro aktivitu kopírování ke spuštění před zápis dat do Azure SQL Database. Pouze vyvolání jednou za kopie spustit. Pomocí této vlastnosti můžete vyčistit předem načtené data. | Ne |
| sqlWriterStoredProcedureName | Název uložené procedury, která definuje, jak použít zdroj dat do cílové tabulky. Příkladem je provést upserts nebo transformovat pomocí vlastní obchodní logiku. <br/><br/>Tato uložená procedura je **vyvolat na jednu dávku**. Pro operace, které pouze spuštěny jednou a nemají co dělat s zdrojová data, použijte `preCopyScript` vlastnost. Příklad operace jsou odstranit a zkrátit. | Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru.<br/>Povolené hodnoty jsou páry název-hodnota. Názvy a malá a velká písmena parametry musí odpovídat názvům a malá a velká písmena parametry uložené procedury. | Ne |
| sqlWriterTableType | Zadejte název typu tabulky má být použit v uložené proceduře. Aktivita kopírování zpřístupní přesouvání dat v dočasné tabulce s tímto typem tabulky. Uložená procedura kód pak sloučit data kopírovány s existujícími daty. | Ne |

> [!TIP]
> Při kopírování dat do Azure SQL Database, připojí aktivity kopírování dat do tabulky jímky ve výchozím nastavení. Pokud chcete provést na upsert nebo jiné obchodní logiky, použijte uloženou proceduru v **SqlSink**. Podrobné informace z [volání uložené procedury SQL jímky](#invoking-stored-procedure-for-sql-sink).

#### <a name="append-data-example"></a>Append – příklad dat

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

#### <a name="invoke-a-stored-procedure-during-copy-for-upsert-example"></a>Během kopírování například upsert volání uložené procedury

Podrobné informace z [volání uložené procedury SQL jímky](#invoking-stored-procedure-for-sql-sink).

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

V této části se dozvíte, jak ke kopírování dat ze zdrojové tabulky bez sloupec identity do cílové tabulky se sloupcem identity.

#### <a name="source-table"></a>Zdrojová tabulka

```sql
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```

#### <a name="destination-table"></a>Cílové tabulky

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

#### <a name="source-dataset-json-definition"></a>Definice JSON datové sady zdroje

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

#### <a name="destination-dataset-json-definition"></a>Cílový definici JSON datové sady

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
> Vaše tabulka zdrojové a cílové mít jiné schéma. 

Cíl má sloupec s identitou. V tomto scénáři je nutné zadat **struktura** vlastnost v definici datové sady cíl, který neobsahuje sloupec identity.

## <a name="invoking-stored-procedure-for-sql-sink"></a> Volání uložené procedury jímku SQL

Při kopírování dat do Azure SQL Database, můžete také konfigurovat a vyvolat uloženou proceduru s další parametry definované uživatelem.

Uloženou proceduru můžete použít při mechanismy předdefinované kopie není sloužit účel. Se obvykle používá při upsert, insert a update nebo další zpracování je třeba provést před posledním vkládání zdrojová data do cílové tabulky. Některé příklady další zpracování jsou sloupce sloučení, vyhledávání další hodnoty a vkládání do více než jedna tabulka.

Následující příklad ukazuje, jak pomocí uložené procedury provádět upsert do tabulky v databázi SQL Azure. Předpokládejme, který vstupní data a jímky **Marketing** tabulka každý obsahovat tři sloupce: **ProfileID**, **stavu**, a **kategorie**. Proveďte upsert na základě **ProfileID** sloupce a použijte ji pouze pro určité kategorie.

#### <a name="output-dataset"></a>Výstupní datové sady

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

Definování **SqlSink** v aktivitě kopírování části:

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

V databázi, zadejte uložená procedura se stejným názvem jako **SqlWriterStoredProcedureName**. Ji zpracovává vstupní data ze zadaného zdroje a sloučí do výstupní tabulky. Název parametru uložené procedury by měla být stejná jako **tableName** definované v datové sadě.

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

V databázi, zadejte typ tabulky se stejným názvem jako **sqlWriterTableType**. Schéma typu tabulky musí být stejná jako schéma vrácený vstupní data.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL,
    [Category] [varchar](256) NOT NULL,
)
```

Uložené procedury funkce využívá [zavolat parametry](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="data-type-mapping-for-azure-sql-database"></a>Mapování datového typu pro databázi SQL Azure

Při kopírování dat z nebo do Azure SQL Database, se používají následující mapování z Azure SQL Database datové typy k Azure Data Factory dočasné datové typy. V tématu [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) se dozvíte, jak aktivity kopírování mapuje zdroje schéma a data typ jímky.

| Azure SQL Database datový typ | Typ průběžných dat objektu pro vytváření dat |
|:--- |:--- |
| bigint |Int64 |
| binární |Byte] |
| Bit |Logická hodnota |
| Char |Řetězec, Char] |
| datum |DateTime |
| Datum a čas |DateTime |
| datetime2 |DateTime |
| Datový typ DateTimeOffset |Datový typ DateTimeOffset |
| Decimal |Decimal |
| Atribut FILESTREAM (varbinary(max)) |Byte] |
| Float |Double |
| image |Byte] |
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
| text |Řetězec, Char] |
| time |Časový interval |
| časové razítko |Byte] |
| tinyint |Bajt |
| Typ UniqueIdentifier |Guid |
| varbinary |Byte] |
| varchar |Řetězec, Char] |
| xml |XML |

## <a name="next-steps"></a>Další postup
Seznam úložišť dat, které jsou podporované jako zdroje a jímky aktivitou kopírování v Azure Data Factory najdete v tématu [podporované úložiště dat a formáty](copy-activity-overview.md##supported-data-stores-and-formats).
