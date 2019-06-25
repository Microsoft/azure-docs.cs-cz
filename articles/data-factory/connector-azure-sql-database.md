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
ms.topic: conceptual
ms.date: 06/14/2019
ms.author: jingwang
ms.openlocfilehash: 90126a607065bdb10e2ff81ce6ab52809ecc3f36
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273761"
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>Kopírování dat do nebo ze služby Azure SQL Database s použitím služby Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, které používáte:"]
> * [Verze 1](v1/data-factory-azure-sql-connector.md)
> * [Aktuální verze](connector-azure-sql-database.md)

Tento článek popisuje, jak kopírovat data do a z Azure SQL Database. Další informace o Azure Data Factory najdete v článku [úvodní článek](introduction.md).

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor Azure SQL Database je podporována pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporované zdroj/jímka matice](copy-activity-overview.md) tabulky
- [Mapování toku dat](concepts-data-flow-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)

Konkrétně tento konektor Azure SQL Database podporuje tyto funkce:

- Kopírování dat pomocí ověřování SQL a ověřování pomocí tokenu aplikace Azure Active Directory (Azure AD) pomocí identity objektu zabezpečení nebo spravované služby pro prostředky Azure.
- Jako zdroj načtení dat pomocí jazyka SQL nebo uloženou proceduru.
- Jako jímka připojit data do cílové tabulky nebo vyvolat uloženou proceduru s vlastní logikou během kopírování.

>[!NOTE]
>Azure SQL Database **[s funkcí Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-current)** není nyní podporován tímto konektorem. Chcete-li vyřešit, můžete použít [obecný konektor ODBC](connector-odbc.md) a ovladač ODBC systému SQL Server přes modul Integration Runtime. Postupujte podle [návod](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-current) konfigurací ODBC ovladač stahování a připojovací řetězec.

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
| connectionString | Zadejte informace potřebné pro připojení k instanci Azure SQL Database **connectionString** vlastnost. <br/>Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory. Heslo/klíč instančního objektu můžete také vložit do služby Azure Key Vault, a pokud se jedná o přijetí změn ověřování SQL `password` konfigurace z připojovacího řetězce. Podívejte se na příklad JSON pod tabulkou a [Store přihlašovacích údajů ve službě Azure Key Vault](store-credentials-in-key-vault.md) článku s dalšími podrobnostmi. | Ano |
| servicePrincipalId | Zadejte ID klienta vaší aplikace. | Ano, pokud používáte ověřování Azure AD se instanční objekt služby. |
| servicePrincipalKey | Zadejte klíč aplikace. Označte toto pole jako **SecureString** bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano, pokud používáte ověřování Azure AD se instanční objekt služby. |
| tenant | Zadejte informace o tenantovi (domény ID tenanta nebo název) v rámci které se nachází vaše aplikace. Načtení podržením ukazatele myši v pravém horním rohu webu Azure portal. | Ano, pokud používáte ověřování Azure AD se instanční objekt služby. |
| connectVia | [Prostředí integration runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Pokud vaše úložiště dat se nachází v privátní síti, můžete použít prostředí Azure Integration Runtime nebo místní prostředí integration runtime. Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. | Ne |

Různými typy ověřování najdete v následujících částech na požadavky a ukázky JSON v uvedeném pořadí:

- [Ověřování SQL](#sql-authentication)
- [Azure AD ověřování tokenu aplikací: Instanční objekt služby](#service-principal-authentication)
- [Azure AD ověřování tokenu aplikací: Spravované identity pro prostředky Azure](#managed-identity)

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

**Heslo ve službě Azure Key Vault:** 

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

1. **[Vytvoření aplikace Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)**  z portálu Azure portal. Poznamenejte si název aplikace a následující hodnoty, které definují propojené služby:

    - ID aplikace
    - Klíč aplikace
    - ID tenanta

2. **[Zřízení správce Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  pro váš server Azure SQL na portálu Azure portal, pokud jste tak již neučinili. Správce Azure AD musíte mít roli uživatele Azure AD nebo skupině Azure AD, ale nemůže být instančního objektu. Tento krok se provádí tak, aby v dalším kroku, můžete použít Azure AD identity uživatele databáze s omezením pro službu vytvořit instanční objekt.

3. **[Vytvořit uživatele databáze s omezením](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  pro instanční objekt. Připojení k databázi z nebo na který se má ke kopírování dat pomocí nástrojů, jako je SSMS, identity Azure AD, který má alespoň oprávnění ALTER ANY uživatele. Spusťte následující příkaz T-SQL: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Udělte nezbytná oprávnění instančního objektu** SQL uživatelů nebo jiné obvyklým způsobem. Spusťte následující kód, nebo si přečtěte další možnosti [tady](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. **Konfigurace služby Azure SQL Database, která je propojená** ve službě Azure Data Factory.


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

### <a name="managed-identity"></a> Spravovaných identit pro ověřování prostředků Azure

Je možné přidružit datové továrny [spravované identity pro prostředky Azure](data-factory-service-identity.md) , která představuje konkrétní datové továrny. Tuto spravovanou identitu můžete použít pro ověřování Azure SQL Database. Přístup k určené objekt pro vytváření a kopírování dat z nebo do databáze s použitím této identity.

Použití spravované identity ověřování, postupujte podle těchto kroků:

1. **[Zřízení správce Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  pro váš server Azure SQL na portálu Azure portal, pokud jste tak již neučinili. Správce Azure AD může být skupina Azure AD nebo uživatel Azure AD. Když udělíte skupině pomocí spravované identity roli správce, přeskočte kroky 3 a 4. Správce bude mít plný přístup k databázi.

2. **[Vytvořit uživatele databáze s omezením](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  pro identitu spravované Data Factory. Připojení k databázi z nebo na který se má ke kopírování dat pomocí nástrojů, jako je SSMS, identity Azure AD, který má alespoň oprávnění ALTER ANY uživatele. Spusťte následující příkaz T-SQL: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Identita spravované objekt pro vytváření dat nezbytná oprávnění udělit** obvyklým způsobem pro uživatele serveru SQL a další. Spusťte následující kód, nebo si přečtěte další možnosti [tady](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    EXEC sp_addrolemember [role name], [your Data Factory name];
    ```

4. **Konfigurace služby Azure SQL Database, která je propojená** ve službě Azure Data Factory.

**Příklad:**

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services) článku. Tato část obsahuje seznam vlastností, které podporuje datová sada Azure SQL Database.

Pro kopírování dat z nebo do služby Azure SQL Database, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** musí být nastavena vlastnost datové sady **AzureSqlTable**. | Ano |
| tableName | Název tabulky nebo zobrazení v instanci Azure SQL Database, která odkazuje propojenou službu. | Ne pro zdroj, Ano pro jímku |

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
        "schema": [ < physical schema, optional, retrievable during authoring > ],
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

> [!TIP]
> Další informace o podporovaných zápisu chování, konfigurace a osvědčených postupů z [osvědčený postup pro načítání dat do služby Azure SQL Database](#best-practice-for-loading-data-into-azure-sql-database).

Chcete-li kopírovat data do služby Azure SQL Database, nastavte **typ** vlastnost v aktivitě kopírování jímky pro **SqlSink**. Následující vlastnosti jsou podporovány v aktivitě kopírování **jímky** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** vlastnost jímky aktivity kopírování musí být nastavena na **SqlSink**. | Ano |
| writeBatchSize | Počet řádků, která se vloží do tabulky SQL **dávce**.<br/> Je povolená hodnota **celé číslo** (počet řádků). Ve výchozím nastavení služby Data Factory dynamicky určí příslušné batch velikost podle velikosti řádku. | Ne |
| writeBatchTimeout | Doba čekání pro dávku vložte na dokončení před vypršením časového limitu operace.<br/> Je povolená hodnota **timespan**. Příklad: "00: 30:00" (30 minut). | Ne |
| preCopyScript | Zadejte dotaz SQL pro aktivitu kopírování ke spuštění před zápisem dat do Azure SQL Database. Pouze vyvolá se jednou za kopírování spustit. Tuto vlastnost použijte k vyčištění dat předem. | Ne |
| sqlWriterStoredProcedureName | Název uložené procedury, která definuje, jak použít zdroj dat do cílové tabulky. <br/>Tuto uloženou proceduru se **za batch**. Pro operace, které pouze spustit jednou a nesouvisí se zdrojovými daty, například delete nebo truncate, použijte `preCopyScript` vlastnost. | Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru.<br/>Povolené hodnoty jsou páry název-hodnota. Názvy a použití malých a velkých parametry musí odpovídat názvům a použití malých a velkých parametrů uložené procedury. | Ne |
| sqlWriterTableType | Zadejte název tabulky typu použitého v uložené proceduře. Aktivitu kopírování, která zpřístupňuje data přesouvá v dočasné tabulce s tímto typem tabulky. Uloženou proceduru kód pak sloučit data kopírovaná s existujícími daty. | Ne |

**Příklad 1: připojení dat**

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

**Příklad 2: volání uložené procedury během kopírování**

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

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>Osvědčeným postupem pro načítání dat do služby Azure SQL Database

Při kopírování dat do Azure SQL Database může vyžadovat chování různých zápisu:

- **[Připojit](#append-data)** : zdroj data obsahují pouze nové záznamy;
- **[Upsert](#upsert-data)** : Moje zdrojová data mají vkládání a aktualizace.
- **[Přepsat](#overwrite-entire-table)** : Chcete znovu načíst tabulku celé dimenze pokaždé, když;
- **[Zápis o vlastní logiku](#write-data-with-custom-logic)** : Potřebuji další zpracování před posledním vkládání do cílové tabulky.

Odkazovat uvedeném pořadí oddíly o tom, jak nakonfigurovat v ADF a osvědčené postupy.

### <a name="append-data"></a>Připojení dat

Toto je výchozí chování tohoto konektoru jímky Azure SQL Database a proveďte ADF **hromadné vložení** efektivně zapisovat do tabulky. Můžete jednoduše nakonfigurovat zdroj a odpovídajícím způsobem jímky v aktivitě kopírování.

### <a name="upsert-data"></a>Upsert dat

**Možnost mám** (navrhované zejména v případě, že máte ke kopírování velkých objemů dat): **přístup většina výkonné** provedete upsert je následující: 

- Za prvé, využívat [dočasné tabulky v rozsahu databáze](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current#database-scoped-global-temporary-tables-azure-sql-database) hromadné načtení všech záznamů pomocí aktivit kopírování. Operace u databáze s rozsahem dočasné tabulky se neprotokolují, načtete milióny záznamů v řádu sekund.
- Aktivita uložená procedura spuštění ve službě ADF použít [sloučit](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) (nebo INSERT/UPDATE) prohlášení a dočasné tabulky jako zdroje k provedení všech aktualizaci nebo vložení jako jedna transakce, snižuje množství výměn dat a protokolovat operace. Na konci aktivity uložené procedury lze až bude připravená na další cyklus upsert oříznutí dočasné tabulky. 

Jako příklad, ve službě Azure Data Factory vytvoříte kanál s **aktivita kopírování** zřetězené s **aktivity uložené procedury** v případě úspěchu. Nejprve kopíruje data ze zdrojového úložiště do dočasné tabulky Azure SQL Database, třeba " **##UpsertTempTable**" jako název tabulky v datové sadě, pak ten volá uloženou proceduru ke sloučení do cílového zdroje dat z dočasné tabulky tabulky a vyčistit dočasné tabulky.

![Upsertovat](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

V databázi definujte uložená procedura s logikou SLOUČENÍ, podobně jako následující text, který ukazuje z výše uvedených aktivity uložené procedury. Za předpokladu, že cíl **marketingové** tabulky se třemi sloupci: **ID profilu**, **stavu**, a **kategorie**, a proveďte upsert na základě **ProfileID** sloupce.

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING ##UpsertTempTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE ##UpsertTempTable
END
```

**Možnost II:** Alternativně můžete se rozhodnout [vyvolat uloženou proceduru v aktivitě kopírování](#invoking-stored-procedure-for-sql-sink), při Poznámka: Tento přístup je provést pro každý řádek ve zdrojové tabulce místo využívání hromadné vložení jako výchozí přístup v aktivitě kopírování proto se nevejde pro upsert velkého rozsahu.

### <a name="overwrite-entire-table"></a>Přepsat celou tabulku

Můžete nakonfigurovat **preCopyScript** vlastnost v aktivitě kopírování jímky, v takovém případě pro každé spuštění aktivity kopírování ADF spustí skript nejprve spusťte kopírování vložte data. Například pokud chcete přepsat celou tabulku s nejnovější data, můžete určit skript, který nejprve odstranit všechny záznamy před hromadného načtení nová data ze zdroje.

### <a name="write-data-with-custom-logic"></a>Zápis dat o vlastní logiku

Podobné, jak je popsáno v [Upsert data](#upsert-data) oddílu, když potřebujete provést další zpracování před posledním vložení zdrojová data do cílové tabulky, můžete) pro široké možnosti škálování a načíst do tabulky s vymezeným oborem dočasné databáze a vyvoláte uložená procedura nebo b) vyvolání uložené procedury během kopírování.

## <a name="invoking-stored-procedure-for-sql-sink"></a> Vyvolání uložené procedury SQL jímky

Při kopírování dat do Azure SQL Database, můžete také nakonfigurovat a vyvolat uloženou proceduru s další parametry zadané uživatelem.

> [!TIP]
> Volání uložené procedury zpracovává na data řádek po řádku namísto hromadné operace, která není určeno pro kopírování velkého rozsahu. Další informace z [osvědčený postup pro načítání dat do služby Azure SQL Database](#best-practice-for-loading-data-into-azure-sql-database).

Uloženou proceduru můžete použít, když integrovaná funkce kopírování mechanismy neslouží účel, například použít zvláštní zpracování před posledním vložení zdrojová data do cílové tabulky. Některé příklady vysokého jsou sloučení sloupců, vyhledat další hodnoty a vložení do více než jednou tabulkou.

Následující příklad ukazuje, jak provést funkcí upsert do tabulky ve službě Azure SQL Database pomocí uložené procedury. Předpokládejme, který vstupní data a jímku **marketingové** tabulka jednotlivých obsahovat tři sloupce: **ID profilu**, **stavu**, a **kategorie**. Proveďte upsert na základě **ProfileID** sloupce a použijte je jenom pro konkrétní kategorie.

**Výstupní datová sada:** "tableName" by měl být stejný název parametru typu tabulky v uložené proceduře (viz níže uvedený skript uložené procedury).

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

Definovat **SQL jímky** následující části v aktivitě kopírování.

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
      VALUES (source.ProfileID, source.State, source.Category);
END
```

V databázi, definujte typ tabulky se stejným názvem jako **sqlWriterTableType**. Schéma typu tabulky musí být stejná jako schéma vrácené funkcí vstupní data.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL,
    [Category] [varchar](256) NOT NULL
)
```

Uložená procedura funkce využívá možnosti [Table-Valued parametry](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="mapping-data-flow-properties"></a>Mapování vlastností toku dat

Další podrobnosti o [zdroje transformace](data-flow-source.md) a [jímky transformace](data-flow-sink.md) v mapování se předávají Data.

## <a name="data-type-mapping-for-azure-sql-database"></a>Mapování datového typu pro službu Azure SQL Database

Při kopírování dat z nebo do služby Azure SQL Database, se používají následující mapování z datových typů v databázi SQL Azure do služby Azure Data Factory dočasné datových typů. Zobrazit [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) se dozvíte, jak aktivitu kopírování, která mapuje typ zdroje schéma a data na jímce.

| Azure SQL Database datový typ | Data Factory dočasné datový typ |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |Datetimeoffset |
| Decimal |Decimal |
| Atribut FILESTREAM (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| ROWVERSION |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object |
| text |String, Char[] |
| time |TimeSpan |
| časové razítko |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

>[!NOTE]
> Pro mapování typů dat na typ desetinné dočasné aktuálně ADF podporují přesnost až 28. Pokud máte data s přesností větší než 28, zvažte možnost převést na řetězec v dotazu SQL.

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky, aktivita kopírování ve službě Azure Data Factory najdete v tématu [podporovaných úložišť dat a formáty](copy-activity-overview.md##supported-data-stores-and-formats).
