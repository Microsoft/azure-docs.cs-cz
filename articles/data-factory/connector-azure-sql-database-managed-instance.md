---
title: Kopírování dat do a z Azure SQL Database Spravované instance
description: Zjistěte, jak přesunout data do a z Azure SQL Database Spravované instance pomocí Azure Data Factory.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: fe803c918cbf60b8f0af76d8c9a94d022153acbb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417498"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>Kopírování dat do a z Azure SQL Database Spravované instance pomocí Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak použít aktivitu kopírování v Azure Data Factory ke kopírování dat do a z Azure SQL Database Spravované instance. Vychází z článku [Přehled aktivity kopírování,](copy-activity-overview.md) který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor spravované instance Azure SQL Database je podporovaný pro následující aktivity:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)
- [Aktivita getMetadata](control-flow-get-metadata-activity.md)

Data ze spravované instance Azure SQL Database Můžete zkopírovat do libovolného podporovaného úložiště dat jímky. Můžete také zkopírovat data z libovolného úložiště dat podporovaného zdroje do spravované instance. Seznam úložišť dat, které jsou podporovány jako zdroje a jímky aktivity kopírování, naleznete v tabulce [Podporovaná úložiště dat.](copy-activity-overview.md#supported-data-stores-and-formats)

Konkrétně tento konektor spravované instance Azure SQL Database podporuje:

- Kopírování dat pomocí ověřování SQL a ověřování tokenů aplikace Azure Active Directory (Azure AD) pomocí instančního objektu nebo spravovaných identit pro prostředky Azure.
- Jako zdroj načítání dat pomocí dotazu SQL nebo uložené procedury.
- Jako jímka, připojení dat k cílové tabulce nebo vyvolání uložené procedury s vlastní logikou během kopírování.

>[!NOTE]
>Azure SQL Database Spravované instance [vždy šifrované](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current) není podporována tímto konektorem nyní. Chcete-li obejít, můžete použít [obecný konektor ODBC](connector-odbc.md) a ovladač SQL Server ODBC prostřednictvím modulu runtime integrace s vlastním hostitelem. Postupujte [podle těchto pokynů](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-mi-current) s konfiguracemi stažení ovladače ODBC a připojovacího řetězce.

## <a name="prerequisites"></a>Požadavky

Přístup k [veřejnému koncovému bodu](../sql-database/sql-database-managed-instance-public-endpoint-securely.md)spravované instance Azure SQL Database Můžete použít modul runtime integrace Azure Spravované Azure spravovanou službou Azure. Ujistěte se, že povolíte veřejný koncový bod a také povolíte veřejný provoz koncových bodů ve skupině zabezpečení sítě, aby se Azure Data Factory mohla připojit k vaší databázi. Další informace naleznete v [těchto pokynech](../sql-database/sql-database-managed-instance-public-endpoint-configure.md).

Chcete-li získat přístup k privátnímu koncovému bodu spravované instance Azure SQL Database, nastavte [modul runtime integrace s vlastním hostitelem,](create-self-hosted-integration-runtime.md) který bude mít přístup k databázi. Pokud zřídíte runtime integrace s vlastním hostitelem ve stejné virtuální síti jako vaše spravovaná instance, ujistěte se, že váš integrační runtime počítač je v jiné podsíti než vaše spravovaná instance. Pokud zřídíte runtime integrace s vlastním hostitelem v jiné virtuální síti než spravovaná instance, můžete použít partnerský vztah virtuální sítě nebo připojení virtuální sítě k virtuální síti. Další informace najdete [v tématu Připojení aplikace ke spravované instanci Azure SQL Database .](../sql-database/sql-database-managed-instance-connect-app.md)

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit Azure Data Factory specifických pro konektor spravované instance Azure SQL Database.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu spravované instance Azure SQL Database jsou podporované následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na **AzureSqlMI**. | Ano |
| připojovací řetězec |Tato vlastnost určuje informace **connectionString,** které jsou potřebné pro připojení ke spravované instanci pomocí ověřování SQL. Další informace naleznete v následujících příkladech. <br/>Výchozí port je 1433. Pokud používáte spravovanou instanci Azure SQL Database se veřejným koncovým bodem, explicitně zadejte port 3342.<br> Můžete také umístit heslo v Azure Key Vault. Pokud se jedná o ověřování `password` SQL, vyžadujete konfiguraci z připojovacího řetězce. Další informace naleznete v příkladu JSON za tabulkou a [přihlašovacími údaji úložiště v úložišti klíčů Azure](store-credentials-in-key-vault.md). |Ano |
| servicePrincipalId | Zadejte ID klienta aplikace. | Ano, při použití ověřování Azure AD s instančním objektem |
| servicePrincipalKey | Zadejte klíč aplikace. Označte toto pole jako **SecureString,** abyste ho bezpečně uložili v Azure Data Factory nebo [odkazovali na tajný klíč uložený v azure key vaultu](store-credentials-in-key-vault.md). | Ano, při použití ověřování Azure AD s instančním objektem |
| Nájemce | Zadejte informace o klientovi, jako je název domény nebo ID klienta, pod kterým je aplikace umístěna. Načtěte ji najet myší v pravém horním rohu portálu Azure. | Ano, při použití ověřování Azure AD s instančním objektem |
| connectVia | Tento [integrační runtime](concepts-integration-runtime.md) se používá pro připojení k úložišti dat. Můžete použít runtime integrace s vlastním hostitelem nebo runtime integrace Azure, pokud vaše spravovaná instance má veřejný koncový bod a umožňuje Azure Data Factory přístup k němu. Pokud není zadán, použije se výchozí doba runtime integrace Azure. |Ano |

Pro různé typy ověřování naleznete v následujících částech na požadavky a Ukázky JSON, v uvedeném pořadí:

- [Ověřování pomocí SQL](#sql-authentication)
- [Ověřování tokenů aplikace Azure AD: Instanční objekt služby](#service-principal-authentication)
- [Ověřování tokenů aplikace Azure AD: Spravované identity pro prostředky Azure](#managed-identity)

### <a name="sql-authentication"></a>Ověřování pomocí SQL

**Příklad 1: Použití ověřování SQL**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Příklad 2: Použití ověřování SQL s heslem v trezoru klíčů Azure**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;",
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

Pokud chcete použít ověřování tokenu aplikace Azure AD založené na primárním objektu služby, postupujte takto:

1. Podle pokynů [zřídíte správce služby Azure Active Directory pro spravovanou instanci](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance).

2. [Vytvořte aplikaci Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) z webu Azure Portal. Poznamenejte si název aplikace a následující hodnoty, které definují propojenou službu:

    - ID aplikace
    - Klíč aplikace
    - ID tenanta

3. [Vytvořte přihlášení](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) pro spravovanou identitu Azure Data Factory. V sql server management studio (SSMS), připojte se k spravované instance pomocí účtu SQL Server, který je **sysadmin**. V **hlavní** databázi spusťte následující T-SQL:

    ```sql
    CREATE LOGIN [your application name] FROM EXTERNAL PROVIDER
    ```

4. [Vytvořte uživatele obsahující databáze](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) pro spravovanou identitu Azure Data Factory. Připojte se k databázi, ze které nebo do které chcete kopírovat data, spusťte následující T-SQL: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER
    ```

5. Udělte službě Data Factory potřebná oprávnění spravované identity jako obvykle pro uživatele SQL a ostatní uživatele. Spusťte následující kód. Další možnosti naleznete v [tomto dokumentu](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current).

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your application name]
    ```

6. Konfigurace propojené služby Azure SQL Database Managed Instance v Azure Data Factory.

**Příklad: Použití ověřování instančního objektu**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;",
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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Spravované identity pro ověřování prostředků Azure

Továrna dat může být přidružena ke [spravované identitě pro prostředky Azure,](data-factory-service-identity.md) která představuje konkrétní datovou továrnu. Tuto spravovanou identitu můžete použít pro ověřování spravované instance azure sql databáze. Určené továrny můžete přistupovat a kopírovat data z nebo do databáze pomocí této identity.

Chcete-li použít ověřování spravované identity, postupujte takto.

1. Podle pokynů [zřídíte správce služby Azure Active Directory pro spravovanou instanci](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance).

2. [Vytvořte přihlášení](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) pro spravovanou identitu Azure Data Factory. V sql server management studio (SSMS), připojte se k spravované instance pomocí účtu SQL Server, který je **sysadmin**. V **hlavní** databázi spusťte následující T-SQL:

    ```sql
    CREATE LOGIN [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

3. [Vytvořte uživatele obsahující databáze](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) pro spravovanou identitu Azure Data Factory. Připojte se k databázi, ze které nebo do které chcete kopírovat data, spusťte následující T-SQL: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

4. Udělte službě Data Factory potřebná oprávnění spravované identity jako obvykle pro uživatele SQL a ostatní uživatele. Spusťte následující kód. Další možnosti naleznete v [tomto dokumentu](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current).

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your Data Factory name]
    ```

5. Konfigurace propojené služby Azure SQL Database Managed Instance v Azure Data Factory.

**Příklad: používá ověřování spravované identity.**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku datových sad. Tato část obsahuje seznam vlastností podporovaných datovou sadou spravovaných instancí Azure SQL Database.

Chcete-li kopírovat data do a z Azure SQL Database Spravované instance, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady musí být nastavena na **AzureSqlMITable**. | Ano |
| Schématu | Název schématu. |Ne pro zdroj, Ano pro umyvadlo  |
| tabulka | Název tabulky/zobrazení. |Ne pro zdroj, Ano pro umyvadlo  |
| tableName | Název tabulky/zobrazení se schématem. Tato vlastnost je podporována pro zpětnou kompatibilitu. Pro nové pracovní `schema` `table`vytížení, použití a . | Ne pro zdroj, Ano pro umyvadlo |

**Příklad**

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "AzureSqlMITable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných zdrojem a jímkou spravované instance databáze Azure SQL Database.

### <a name="azure-sql-database-managed-instance-as-a-source"></a>Spravovaná instance Azure SQL Database jako zdroj

Chcete-li zkopírovat data z instance spravované databáze Azure SQL, jsou v části zdroje aktivity kopírování podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavena na **sqlmisource**. | Ano |
| sqlReaderQuery |Tato vlastnost používá vlastní dotaz SQL ke čtení dat. Příklad: `select * from MyTable`. |Ne |
| sqlReaderStoredProcedureName |Tato vlastnost je název uložené procedury, která čte data ze zdrojové tabulky. Poslední příkaz SQL musí být příkaz SELECT v uložené proceduře. |Ne |
| storedProcedureParameters |Tyto parametry jsou pro uloženou proceduru.<br/>Povolené hodnoty jsou dvojice názvů nebo hodnot. Názvy a písmena parametrů se musí shodovat s názvy a písmeny parametrů uložené procedury. |Ne |
| Isolationlevel | Určuje chování uzamčení transakce pro zdroj SQL. Povolené hodnoty jsou: **ReadCommitted** (výchozí), **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snímek**. Další podrobnosti naleznete v [tomto dokumentu.](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) | Ne |

**Je třeba počítat s následujícím:**

- Pokud je pro **SqlMISource**zadán **sqlReaderQuery** , aktivita kopírování spustí tento dotaz proti zdroji spravované instance, aby získala data. Můžete také zadat uloženou proceduru zadáním **sqlReaderStoredProcedureName** a **storedProcedureParameters,** pokud uložená procedura přebírá parametry.
- Pokud nezadáte vlastnost **sqlReaderQuery** nebo **sqlReaderStoredProcedureName,** sloupce definované v části "struktura" datové sady JSON se použijí k vytvoření dotazu. Dotaz `select column1, column2 from mytable` je spuštěn proti spravované instanci. Pokud definice datové sady nemá "strukturu", jsou z tabulky vybrány všechny sloupce.

**Příklad: Použití dotazu SQL**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
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
                "type": "SqlMISource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Příklad: Použití uložené procedury**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
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
                "type": "SqlMISource",
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

**Definice uložené procedury**

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

### <a name="azure-sql-database-managed-instance-as-a-sink"></a>Spravovaná instance Azure SQL Database jako jímka

> [!TIP]
> Další informace o podporovaných chování zápisu, konfigurace a osvědčené postupy z [osvědčených postupů pro načítání dat do Azure SQL Database Spravované instance](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

Chcete-li zkopírovat data do spravované instance Azure SQL Database, jsou v části jímky aktivity kopírování podporované následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type jímky aktivity kopírování musí být nastavena na **SqlMISink**. | Ano |
| writeBatchSize |Počet řádků, které mají být vloženy do tabulky SQL *na dávku*.<br/>Povolené hodnoty jsou celá čísla pro počet řádků. Ve výchozím nastavení Azure Data Factory dynamicky určuje příslušnou velikost dávky na základě velikosti řádku.  |Ne |
| writeBatchTimeout |Tato vlastnost určuje dobu čekání operace dávkové vložení, která má být dokončena před časovým limitem.<br/>Povolené hodnoty jsou pro časový rozsah. Příkladem je "00:30:00", což je 30 minut. |Ne |
| preCopyScript |Tato vlastnost určuje dotaz SQL pro aktivitu kopírování, která má být spuštěna před zápisem dat do spravované instance. Je vyvolána pouze jednou za kopírování spustit. Tuto vlastnost můžete použít k vyčištění předinstalovaných dat. |Ne |
| sqlWriterStoredProcedureName | Název uložené procedury, která definuje, jak použít zdrojová data do cílové tabulky. <br/>Tato uložená procedura je *vyvolána na dávku*. Pro operace, které běží pouze jednou a nemají nic společného se zdrojovými `preCopyScript` daty, například odstranit nebo zkrátit, použijte vlastnost. | Ne |
| storedProcedureTableTypeParameterName |Název parametru typu tabulky zadaný v uložené proceduře.  |Ne |
| sqlWriterTableType |Název typu tabulky, který má být použit v uložené proceduře. Aktivita kopírování zpřístupní data přesunutá v dočasné tabulce s tímto typem tabulky. Uložený kód procedury pak může sloučit data, která se kopírují s existujícími daty. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru.<br/>Povolené hodnoty jsou dvojice názvů a hodnot. Názvy a písmena parametrů se musí shodovat s názvy a písmeny parametrů uložené procedury. | Ne |
| tableOption | Určuje, zda se má automaticky vytvořit tabulka jímky, pokud neexistuje na základě zdrojového schématu. Automatické vytváření tabulek není podporováno, pokud jímka určuje uloženou proceduru nebo fázovaná kopie je nakonfigurována v aktivitě kopírování. Povolené hodnoty `none` jsou: `autoCreate`(výchozí), . |Ne |

**Příklad 1: Připojit data**

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlMISink",
                "writeBatchSize": 100000,
                "tableOption": "autoCreate"
            }
        }
    }
]
```

**Příklad 2: Vyvolání uložené procedury během kopírování**

Další podrobnosti z [vyvolat uloženou proceduru z jímky SQL MI](#invoke-a-stored-procedure-from-a-sql-sink).

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlMISink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "storedProcedureTableTypeParameterName": "MyTable",
                "sqlWriterTableType": "MyTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="best-practice-for-loading-data-into-azure-sql-database-managed-instance"></a>Osvědčený postup pro načítání dat do spravované instance Azure SQL Database

Při kopírování dat do spravované instance Azure SQL Database můžete vyžadovat různé chování zápisu:

- [Připojit](#append-data): Zdrojová data mají pouze nové záznamy.
- [Upsert](#upsert-data): Moje zdrojová data mají vložit i aktualizace.
- [Přepsat](#overwrite-the-entire-table): Chci pokaždé znovu načíst celou tabulku dimenzí.
- [Napište s vlastní logikou](#write-data-with-custom-logic): Potřebuji další zpracování před konečným vložením do cílové tabulky. 

Podívejte se na příslušné části, jak nakonfigurovat v Azure Data Factory a doporučené postupy.

### <a name="append-data"></a>Připojit data

Připojení dat je výchozí chování tohoto konektoru jímky spravované databáze Azure SQL Database. Azure Data Factory provádí hromadné vkládání pro efektivní zápis do vaší tabulky. Můžete nakonfigurovat zdroj a jímky odpovídajícím způsobem v aktivitě kopírování.

### <a name="upsert-data"></a>Upsert dat

**Možnost 1:** Pokud máte velké množství dat ke kopírování, použijte následující přístup k provedení upsert: 

- Nejprve použijte [dočasnou tabulku](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) k hromadnému načtení všech záznamů pomocí aktivity kopírování. Vzhledem k tomu, že operace s dočasnými tabulkami nejsou protokolovány, můžete načíst miliony záznamů během několika sekund.
- Spusťte aktivitu uložené procedury v Azure Data Factory a použijte příkaz [MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) nebo INSERT/UPDATE. Použijte dočasnou tabulku jako zdroj k provedení všech aktualizací nebo vložení jako jedné transakce. Tímto způsobem se sníží počet zpátečních cest a operací protokolu. Na konci aktivity uložené procedury může být tabulka temp zkrácena, aby byla připravena na další cyklus upsert.

Jako příklad v Azure Data Factory můžete vytvořit kanál s **aktivitou Copy** zřetězenou s **aktivitou uložené procedury**. První zkopíruje data ze zdrojového úložiště do dočasné tabulky, například **##UpsertTempTable**jako název tabulky v datové sadě. Pak druhý vyvolá uloženou proceduru pro sloučení zdrojových dat z dočasné tabulky do cílové tabulky a vyčištění dočasné tabulky.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

V databázi definujte uloženou proceduru s logikou MERGE, jako je následující příklad, na který se ukazuje z předchozí aktivity uložené procedury. Předpokládejme, že cílem je **tabulka Marketing** se třemi sloupci: **ProfileID**, **State**a **Category**. Proveďte upsert na základě sloupce **ProfileID.**

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

**Možnost č. 2:** Můžete také [vyvolat uloženou proceduru v rámci aktivity kopírování](#invoke-a-stored-procedure-from-a-sql-sink). Tento přístup spustí každý řádek ve zdrojové tabulce namísto použití hromadné vložení jako výchozí přístup v aktivitě kopírování, což není vhodné pro velké škálování upsert.

### <a name="overwrite-the-entire-table"></a>Přepsání celé tabulky

Vlastnost **preCopyScript** můžete nakonfigurovat v jímce aktivity kopírování. V tomto případě pro každou aktivitu kopírování, která běží, Azure Data Factory spustí skript jako první. Pak spustí kopii vložit data. Chcete-li například přepsat celou tabulku nejnovějšími daty, zadejte skript, který nejprve odstraní všechny záznamy před hromadným načtením nových dat ze zdroje.

### <a name="write-data-with-custom-logic"></a>Zápis dat pomocí vlastní logiky

Kroky pro zápis dat s vlastní logikou jsou podobné těm, které jsou popsány v části [dat Upsert.](#upsert-data) Pokud potřebujete použít další zpracování před konečným vložením zdrojových dat do cílové tabulky, můžete ve velkém měřítku provést jednu ze dvou věcí: 

- Načtěte do dočasné tabulky a potom vyvolat uloženou proceduru.
- Vyvolat uloženou proceduru během kopírování.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a>Vyvolání uložené procedury z jímky SQL

Když zkopírujete data do spravované instance Azure SQL Database, můžete také nakonfigurovat a vyvolat uživatelem specifikovanou uloženou proceduru s dalšími parametry. Funkce uložené procedury využívá [parametry s hodnotou tabulky](https://msdn.microsoft.com/library/bb675163.aspx).

> [!TIP]
> Vyvolání uložené procedury zpracovává data řádek po řádku namísto pomocí hromadné operace, kterou nedoporučujeme pro rozsáhlé kopírování. Další informace najdete v [článku Doporučené postupy pro načítání dat do spravované instance Azure SQL Database .](#best-practice-for-loading-data-into-azure-sql-database-managed-instance)

Uloženou proceduru můžete použít, když integrované kopírovací mechanismy neslouží účelu. Příkladem je, když chcete použít další zpracování před konečným vložením zdrojových dat do cílové tabulky. Některé další příklady zpracování jsou, když chcete sloučit sloupce, vyhledat další hodnoty a vložit data do více než jedné tabulky.

Následující ukázka ukazuje, jak použít uloženou proceduru provést upsert do tabulky v databázi SERVERU SQL. Předpokládejme, že vstupní data a tabulka **marketingu** jímky mají každý ze tří sloupců: **ProfileID**, **State**a **Category**. Proveďte upsert na základě sloupce **ProfileID** a použijte jej pouze pro určitou kategorii s názvem "ProductA".

1. V databázi definujte typ tabulky se stejným názvem jako **sqlWriterTableType**. Schéma typu tabulky je stejné jako schéma vrácené vstupními daty.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. V databázi definujte uloženou proceduru se stejným názvem jako **sqlWriterStoredProcedureName**. Zpracovává vstupní data ze zadaného zdroje a slučuje se do výstupní tabulky. Název parametru typu tabulky v uložené proceduře je stejný jako **název_tabulky** definovaný v datové sadě.

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

3. V Azure Data Factory definujte oddíl **jímky SQL MI** v aktivitě kopírování takto:

    ```json
    "sink": {
        "type": "SqlMISink",
        "sqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "sqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="data-type-mapping-for-azure-sql-database-managed-instance"></a>Mapování datových typů pro spravovanou instanci Azure SQL Database

Když se data zkopírují do a z Azure SQL Database Spravované instance, následující mapování se používají z datových typů Azure SQL Database Managed Instance do dočasných datových typů Azure Data Factory. Informace o tom, jak se aktivita kopírování mapuje ze zdrojového schématu a datového typu do jímky, naleznete v [tématu Schémata a mapování datových typů](copy-activity-schema-and-type-mapping.md).

| Datový typ spravované instance Azure SQL Database | Dočasný datový typ Azure Data Factory |
|:--- |:--- |
| bigint |Int64 |
| binární |Bajt[] |
| bitové |Logická hodnota |
| char |Řetězec, Znak[] |
| date |DateTime |
| Datum a čas |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Desetinné číslo |Desetinné číslo |
| ATRIBUT FILESTREAM (varbinary(max)) |Bajt[] |
| Plovoucí desetinná čárka |Double |
| image |Bajt[] |
| int |Int32 |
| Peníze |Desetinné číslo |
| Nchar |Řetězec, Znak[] |
| Ntext |Řetězec, Znak[] |
| numerické |Desetinné číslo |
| nvarchar |Řetězec, Znak[] |
| reálná |Single |
| Rowversion |Bajt[] |
| Smalldatetime |DateTime |
| smallint |Int16 |
| Smallmoney |Desetinné číslo |
| Sql_variant |Objekt |
| text |Řetězec, Znak[] |
| time |TimeSpan |
| časové razítko |Bajt[] |
| tinyint |Int16 |
| uniqueidentifier |Identifikátor GUID |
| Varbinary |Bajt[] |
| varchar |Řetězec, Znak[] |
| xml |XML |

>[!NOTE]
> U datových typů, které se mapují na desítkový prozatímní typ, aktuálně Azure Data Factory podporuje přesnost až 28. Pokud máte data, která vyžaduje přesnost větší než 28, zvažte převod na řetězec v dotazu SQL.

## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Vlastnosti aktivity GetMetadata

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v Azure Data Factory najdete v [tématu Podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
