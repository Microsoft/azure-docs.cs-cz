---
title: Kopírování a transformace dat v Azure SQL Database
description: Zjistěte, jak kopírovat data do a z Azure SQL Database a transformovat data v Azure SQL Database pomocí Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: 8f5065a0f4a2a96a747a45f64e00e86f7990bfb8
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437798"
---
# <a name="copy-and-transform-data-in-azure-sql-database-by-using-azure-data-factory"></a>Kopírování a transformace dat v Azure SQL Database pomocí Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi Azure Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-azure-sql-connector.md)
> * [Aktuální verze](connector-azure-sql-database.md)

Tento článek popisuje, jak pomocí kopírovat aktivity v Azure Data Factory ke kopírování dat z a do Azure SQL Database a pomocí toku dat k transformaci dat v Azure SQL Database. Další informace o Azure Data Factory najdete v [úvodním článku](introduction.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Azure SQL Database je podporovaný pro následující aktivity:

- [Kopírovat aktivitu](copy-activity-overview.md) s [podporovanou tabulkou matice zdroje/jímky](copy-activity-overview.md)
- [Mapování toku dat](concepts-data-flow-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)
- [Aktivita getMetadata](control-flow-get-metadata-activity.md)

Pro aktivitu Kopírování tento konektor Azure SQL Database podporuje tyto funkce:

- Kopírování dat pomocí ověřování SQL a ověřování tokenů aplikace Azure Active Directory (Azure AD) pomocí instančního objektu nebo spravovaných identit pro prostředky Azure.
- Jako zdroj načítání dat pomocí dotazu SQL nebo uložené procedury.
- Jako jímka, připojení dat k cílové tabulce nebo vyvolání uložené procedury s vlastní logikou během kopírování.

>[!NOTE]
>Azure SQL Database [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-current) teď není podporována tímto konektorem. Chcete-li obejít, můžete použít [obecný konektor ODBC](connector-odbc.md) a ovladač SQL Server ODBC prostřednictvím modulu runtime integrace s vlastním hostitelem. Postupujte [podle těchto pokynů](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-current) s konfiguracemi stažení ovladače ODBC a připojovacího řetězce.

> [!IMPORTANT]
> Pokud kopírujete data pomocí modulu runtime integrace Azure Data Factory, nakonfigurujte [bránu firewall Azure SQL Serveru](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) tak, aby služby Azure měly přístup k serveru.
> Pokud kopírujete data pomocí modulu runtime integrace s vlastním hostitelem, nakonfigurujte bránu firewall Azure SQL Serveru tak, aby umožňovala příslušný rozsah IP adres. Tento rozsah zahrnuje IP adresy počítače, která se používá k připojení k Azure SQL Database.

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit Azure Data Factory specifických pro konektor Azure SQL Database.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Tyto vlastnosti jsou podporované pro propojenou službu Azure SQL Database:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **type** musí být nastavena na **AzureSqlDatabase**. | Ano |
| připojovací řetězec | Zadejte informace potřebné pro připojení k instanci Azure SQL Database pro vlastnost **connectionString.** <br/>Můžete také umístit heslo nebo hlavní klíč služby v Azure Key Vault. Pokud se jedná o ověřování `password` SQL, vyžadujete konfiguraci z připojovacího řetězce. Další informace naleznete v příkladu JSON za tabulkou a [přihlašovacími údaji úložiště v úložišti klíčů Azure](store-credentials-in-key-vault.md). | Ano |
| servicePrincipalId | Zadejte ID klienta aplikace. | Ano, při použití ověřování Azure AD s instančním objektem |
| servicePrincipalKey | Zadejte klíč aplikace. Označte toto pole jako **SecureString,** abyste ho bezpečně uložili v Azure Data Factory nebo [odkazovali na tajný klíč uložený v azure key vaultu](store-credentials-in-key-vault.md). | Ano, při použití ověřování Azure AD s instančním objektem |
| Nájemce | Zadejte informace o klientovi, jako je název domény nebo ID klienta, pod kterým je aplikace umístěna. Načtěte ji najet myší v pravém horním rohu portálu Azure. | Ano, při použití ověřování Azure AD s instančním objektem |
| connectVia | Tento [integrační runtime](concepts-integration-runtime.md) se používá pro připojení k úložišti dat. Pokud je vaše úložiště dat umístěné v privátní síti, můžete použít runtime integrace Azure nebo runtime integrace s vlastním hostitelem. Pokud není zadán, použije se výchozí doba runtime integrace Azure. | Ne |

Pro různé typy ověřování naleznete v následujících částech na požadavky a Ukázky JSON, v uvedeném pořadí:

- [Ověřování pomocí SQL](#sql-authentication)
- [Ověřování tokenů aplikace Azure AD: Instanční objekt služby](#service-principal-authentication)
- [Ověřování tokenů aplikace Azure AD: Spravované identity pro prostředky Azure](#managed-identity)

>[!TIP]
>Pokud stisknete chybu s kódem chyby "UserErrorFailedToConnectToSqlServer" a zprávou jako "Limit `Pooling=false` relace pro databázi je XXX a bylo dosaženo," přidejte do připojovacího řetězce a opakujte akci.

### <a name="sql-authentication"></a>Ověřování pomocí SQL

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Příklad propojené služby, který používá ověřování SQL

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Heslo v trezoru klíčů Azure** 

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
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

1. [Vytvořte aplikaci Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) z webu Azure Portal. Poznamenejte si název aplikace a následující hodnoty, které definují propojenou službu:

    - ID aplikace
    - Klíč aplikace
    - ID tenanta

2. Pokud jste to ještě neudělali, [zřídíte správce Služby Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) pro váš Azure SQL Server na webu Azure Portal. Správce Azure AD musí být uživatel Azure AD nebo skupina Azure AD, ale nemůže být instanční objekt. Tento krok se provádí tak, že v dalším kroku můžete použít identitu Azure AD k vytvoření obsaženého uživatele databáze pro instanční objekt.

3. [Vytvořte obsažené uživatele databáze](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) pro instanční objekt. Připojte se k databázi z nebo do které chcete kopírovat data pomocí nástrojů, jako je SQL Server Management Studio, s identitou Azure AD, která má alespoň ALTER oprávnění LIBOVOLNÉHO UŽIVATELE. Spusťte následující T-SQL: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. Udělte zaregistrovaný objekt služby potřebná oprávnění jako obvykle pro uživatele SQL nebo jiné uživatele. Spusťte následující kód. Další možnosti naleznete v [tomto dokumentu](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your application name];
    ```

5. Konfigurace propojené služby Azure SQL Database v Azure Data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Příklad propojené služby, který používá ověřování instančního objektu

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30",
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

Továrna dat může být přidružena ke [spravované identitě pro prostředky Azure,](data-factory-service-identity.md) která představuje konkrétní datovou továrnu. Tuto spravovanou identitu můžete použít pro ověřování databáze Azure SQL. Určené továrny můžete přistupovat a kopírovat data z nebo do databáze pomocí této identity.

Chcete-li použít ověřování spravované identity, postupujte takto.

1. Pokud jste to ještě neudělali, [zřídíte správce Služby Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) pro váš Azure SQL Server na webu Azure Portal. Správce Azure AD může být uživatel Azure AD nebo skupina Azure AD. Pokud skupině se spravovanou identitou udělíte roli správce, přeskočte kroky 3 a 4. Správce má úplný přístup k databázi.

2. [Vytvořte uživatele obsahující databáze](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) pro spravovanou identitu Azure Data Factory. Připojte se k databázi z nebo do které chcete kopírovat data pomocí nástrojů, jako je SQL Server Management Studio, s identitou Azure AD, která má alespoň ALTER oprávnění LIBOVOLNÉHO UŽIVATELE. Spusťte následující T-SQL: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. Udělte službě Data Factory potřebná oprávnění spravované identity jako obvykle pro uživatele SQL a ostatní uživatele. Spusťte následující kód. Další možnosti naleznete v [tomto dokumentu](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your Data Factory name];
    ```

4. Konfigurace propojené služby Azure SQL Database v Azure Data Factory.

**Příklad**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete [v tématu Datové sady](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services). 

Pro datovou sadu Azure SQL Database jsou podporované následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **type** datové sady musí být nastavena na **AzureSqlTable**. | Ano |
| Schématu | Název schématu. |Ne pro zdroj, Ano pro umyvadlo  |
| tabulka | Název tabulky/zobrazení. |Ne pro zdroj, Ano pro umyvadlo  |
| tableName | Název tabulky/zobrazení se schématem. Tato vlastnost je podporována pro zpětnou kompatibilitu. Pro nové pracovní `schema` `table`vytížení, použití a . | Ne pro zdroj, Ano pro umyvadlo |

#### <a name="dataset-properties-example"></a>Příklad vlastností datové sady

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
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v [tématu Potrubí](concepts-pipelines-activities.md). Tato část obsahuje seznam vlastností podporovaných zdrojem a jímkou databáze Azure SQL.

### <a name="azure-sql-database-as-the-source"></a>Azure SQL Database jako zdroj

Chcete-li zkopírovat data z Azure SQL Database, jsou podporovány následující vlastnosti v části **zdroj aktivity** kopírování:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **type** zdroje aktivity kopírování musí být nastavena na **AzureSqlSource**. Typ "SqlSource" je stále podporován pro zpětnou kompatibilitu. | Ano |
| sqlReaderQuery | Tato vlastnost používá vlastní dotaz SQL ke čtení dat. Příklad: `select * from MyTable`. | Ne |
| sqlReaderStoredProcedureName | Název uložené procedury, která čte data ze zdrojové tabulky. Poslední příkaz SQL musí být příkaz SELECT v uložené proceduře. | Ne |
| storedProcedureParameters | Parametry pro uloženou proceduru.<br/>Povolené hodnoty jsou dvojice názvů nebo hodnot. Názvy a písmena parametrů se musí shodovat s názvy a písmeny parametrů uložené procedury. | Ne |
| Isolationlevel | Určuje chování uzamčení transakce pro zdroj SQL. Povolené hodnoty jsou: **ReadCommitted** (výchozí), **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snímek**. Další podrobnosti naleznete v [tomto dokumentu.](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) | Ne |

**Body k poznámce:**

- Pokud **sqlReaderQuery** je zadán pro **AzureSqlSource**, aktivita kopírování spustí tento dotaz proti zdroji Azure SQL Database získat data. Můžete také zadat uloženou proceduru zadáním **sqlReaderStoredProcedureName** a **storedProcedureParameters,** pokud uložená procedura přebírá parametry.
- Pokud nezadáte **sqlReaderQuery** nebo **sqlReaderStoredProcedureName**, sloupce definované v části "struktura" datové sady JSON se používají k vytvoření dotazu. Dotaz `select column1, column2 from mytable` běží proti Azure SQL Database. Pokud definice datové sady nemá "strukturu", jsou z tabulky vybrány všechny sloupce.

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
                "type": "AzureSqlSource",
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
                "type": "AzureSqlSource",
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
> Další informace o podporovaných chování zápisu, konfigurace a osvědčené postupy z [osvědčených postupů pro načítání dat do Azure SQL Database](#best-practice-for-loading-data-into-azure-sql-database).

Chcete-li zkopírovat data do databáze Azure SQL Database, jsou podporovány následující vlastnosti v části **jímky** aktivity kopírování:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **type** jímky aktivity kopírování musí být nastavena na **AzureSqlSink**. Typ "SqlSink" je stále podporován pro zpětnou kompatibilitu. | Ano |
| writeBatchSize | Počet řádků, které mají být vloženy do tabulky SQL *na dávku*.<br/> Povolená hodnota je **celé číslo** (počet řádků). Ve výchozím nastavení Azure Data Factory dynamicky určuje příslušnou velikost dávky na základě velikosti řádku. | Ne |
| writeBatchTimeout | Čekací doba pro operaci dávkové vložení dokončit před časovým oponem časového režimu.<br/> Povolená hodnota je **timespan**. Příkladem je "00:30:00" (30 minut). | Ne |
| preCopyScript | Zadejte dotaz SQL pro aktivitu kopírování, která se má spustit před zápisem dat do databáze Azure SQL Database. Je vyvolána pouze jednou za kopírování spustit. Pomocí této vlastnosti vyčistit přednačtená data. | Ne |
| sqlWriterStoredProcedureName | Název uložené procedury, která definuje, jak použít zdrojová data do cílové tabulky. <br/>Tato uložená procedura je *vyvolána na dávku*. Pro operace, které běží pouze jednou a nemají nic společného se zdrojovými `preCopyScript` daty, například odstranit nebo zkrátit, použijte vlastnost. | Ne |
| storedProcedureTableTypeParameterName |Název parametru typu tabulky zadaný v uložené proceduře.  |Ne |
| sqlWriterTableType |Název typu tabulky, který má být použit v uložené proceduře. Aktivita kopírování zpřístupní data přesunutá v dočasné tabulce s tímto typem tabulky. Uložený kód procedury pak může sloučit data, která se kopírují s existujícími daty. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru.<br/>Povolené hodnoty jsou dvojice názvů a hodnot. Názvy a písmena parametrů se musí shodovat s názvy a písmeny parametrů uložené procedury. | Ne |
| tableOption | Určuje, zda se má automaticky vytvořit tabulka jímky, pokud neexistuje na základě zdrojového schématu. Automatické vytváření tabulek není podporováno, pokud jímka určuje uloženou proceduru nebo fázovaná kopie je nakonfigurována v aktivitě kopírování. Povolené hodnoty `none` jsou: `autoCreate`(výchozí), . |Ne |
| disableMetricsCollection | Data Factory shromažďuje metriky, jako je Například Azure SQL Database DTU pro optimalizaci výkonu kopírování a doporučení. Pokud se obáváte tohoto `true` chování, zadejte jej vypnout. | Ne (výchozí `false`hodnota je ) |

**Příklad 1: Připojit data**

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
                "type": "AzureSqlSink",
                "writeBatchSize": 100000,
                "tableOption": "autoCreate"
            }
        }
    }
]
```

**Příklad 2: Vyvolání uložené procedury během kopírování**

Další podrobnosti z [vyvolat uloženou proceduru z jímky SQL](#invoke-a-stored-procedure-from-a-sql-sink).

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
                "type": "AzureSqlSink",
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

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>Osvědčený postup pro načítání dat do Azure SQL Database

Při kopírování dat do Azure SQL Database můžete vyžadovat různé chování zápisu:

- [Připojit](#append-data): Zdrojová data mají pouze nové záznamy.
- [Upsert](#upsert-data): Moje zdrojová data mají vložit i aktualizace.
- [Přepsat](#overwrite-the-entire-table): Chci pokaždé znovu načíst celou tabulku dimenzí.
- [Napište s vlastní logikou](#write-data-with-custom-logic): Potřebuji další zpracování před konečným vložením do cílové tabulky.

Podívejte se na příslušné části o tom, jak nakonfigurovat v Azure Data Factory a doporučené postupy.

### <a name="append-data"></a>Připojit data

Připojení dat je výchozí chování tohoto konektoru jímky databáze Azure SQL. Azure Data Factory provádí hromadné vkládání pro efektivní zápis do vaší tabulky. Můžete nakonfigurovat zdroj a jímky odpovídajícím způsobem v aktivitě kopírování.

### <a name="upsert-data"></a>Upsert dat

**Možnost 1:** Pokud máte velké množství dat ke kopírování, použijte následující přístup k provedení upsert: 

- Nejprve použijte [dočasnou tabulku s rozsahem databáze](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current#database-scoped-global-temporary-tables-azure-sql-database) k hromadnému načtení všech záznamů pomocí aktivity kopírování. Vzhledem k tomu, že operace s dočasnými tabulkami s vymezeným rozsahem databáze nejsou protokolovány, můžete načíst miliony záznamů během několika sekund.
- Spusťte aktivitu uložené procedury v Azure Data Factory a použijte příkaz [MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) nebo INSERT/UPDATE. Použijte dočasnou tabulku jako zdroj k provedení všech aktualizací nebo vložení jako jedné transakce. Tímto způsobem se sníží počet zpátečních cest a operací protokolu. Na konci aktivity uložené procedury může být tabulka temp zkrácena, aby byla připravena na další cyklus upsert.

Jako příklad v Azure Data Factory můžete vytvořit kanál s **aktivitou Copy** zřetězenou s **aktivitou uložené procedury**. První zkopíruje data z úložiště zdrojů do dočasné tabulky Azure SQL Database, například **##UpsertTempTable**, jako název tabulky v datové sadě. Pak druhý vyvolá uloženou proceduru pro sloučení zdrojových dat z dočasné tabulky do cílové tabulky a vyčištění dočasné tabulky.

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

- Načtěte do dočasné tabulky s rozsahem databáze a potom vyvolat uloženou proceduru. 
- Vyvolat uloženou proceduru během kopírování.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a>Vyvolání uložené procedury z jímky SQL

Při kopírování dat do Azure SQL Database, můžete také nakonfigurovat a vyvolat uživatelem zadané uložené procedury s dalšími parametry. Funkce uložené procedury využívá [parametry s hodnotou tabulky](https://msdn.microsoft.com/library/bb675163.aspx).

> [!TIP]
> Vyvolání uložené procedury zpracovává data řádek po řádku namísto pomocí hromadné operace, kterou nedoporučujeme pro rozsáhlé kopírování. Další informace [najdete v článku Doporučené postupy pro načítání dat do Azure SQL Database](#best-practice-for-loading-data-into-azure-sql-database).

Uloženou proceduru můžete použít, když integrované kopírovací mechanismy neslouží účelu. Příkladem je, když chcete použít další zpracování před konečným vložením zdrojových dat do cílové tabulky. Některé další příklady zpracování jsou, když chcete sloučit sloupce, vyhledat další hodnoty a vložit do více než jedné tabulky.

Následující ukázka ukazuje, jak použít uloženou proceduru k provedení upsert do tabulky v Azure SQL Database. Předpokládejme, že vstupní data a tabulka **marketingu** jímky mají každý ze tří sloupců: **ProfileID**, **State**a **Category**. Proveďte upsert na základě sloupce **ProfileID** a použijte jej pouze pro určitou kategorii s názvem "ProductA".

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

3. V Azure Data Factory definujte oddíl **jímky SQL** v aktivitě kopírování takto:

    ```json
    "sink": {
        "type": "AzureSqlSink",
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

## <a name="mapping-data-flow-properties"></a>Mapování vlastností toku dat

Při transformaci dat v toku dat mapování můžete číst a zapisovat do tabulek z Azure SQL Database. Další informace naleznete v [transformaci zdroje](data-flow-source.md) a [jímce transformace](data-flow-sink.md) v mapování datových toků.

### <a name="source-transformation"></a>Transformace zdroje

Nastavení specifická pro Azure SQL Database jsou dostupná na kartě **Možnosti zdroje** zdrojové transformace. 

**Vstup:** Vyberte, zda navedete zdroj ```Select * from <table-name>```na tabulku (ekvivalentní) nebo zadáte vlastní dotaz SQL.

**Dotaz**: Pokud ve vstupním poli vyberete dotaz, zadejte pro zdroj dotaz SQL. Toto nastavení přepíše všechny tabulky, které jste zvolili v datové sadě. **Klauzule Order By** zde nejsou podporovány, ale můžete nastavit úplný příkaz SELECT FROM. Můžete také použít uživatelem definované funkce tabulky. **select * z udfGetData()** je UDF v SQL, který vrací tabulku. Tento dotaz vytvoří zdrojovou tabulku, kterou můžete použít v toku dat. Použití dotazů je také skvělý způsob, jak snížit řádky pro testování nebo pro vyhledávání. 

* Příklad SQL:```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Velikost dávky**: Zadejte velikost dávky pro velké datové bloky do čtení.

**Úroveň izolace**: Výchozí hodnota pro zdroje SQL v toku dat mapování je přečtena nezávazně. Zde můžete změnit úroveň izolace na jednu z těchto hodnot:
* Přečtěte si potvrzené
* Číst nesvěřené
* Opakovatelné čtení
* Serializovatelný
* Žádné (ignorovat úroveň izolace)

![Úroveň izolace](media/data-flow/isolationlevel.png "Úroveň izolace")

### <a name="sink-transformation"></a>Transformace jímky

Nastavení specifická pro Azure SQL Database jsou k dispozici na kartě **Nastavení** transformace jímky.

**Metoda aktualizace:** Určuje, jaké operace jsou povoleny v cílové masce databáze. Ve výchozím nastavení je povolit pouze vložení. Chcete-li aktualizovat, upsert nebo odstranit řádky, je nutné změnit řádek transformace označit řádky pro tyto akce. Pro aktualizace, upserts a odstraní, klíčový sloupec nebo sloupce musí být nastavena k určení, který řádek změnit.

![Klíčové sloupce](media/data-flow/keycolumn.png "Klíčové sloupce")

Název sloupce, který zde vyberete jako klíč, bude použit adf jako součást následné aktualizace, upsert, delete. Proto je nutné vybrat sloupec, který existuje v mapování jímky. Pokud nechcete hodnotu do tohoto sloupce klíče nezapisovat, klikněte na tlačítko Přeskočit zápis klíčových sloupců.

**Akce tabulky:** Určuje, zda chcete znovu vytvořit nebo odebrat všechny řádky z cílové tabulky před zápisem.
* Žádné: V tabulce nebude provedena žádná akce.
* Znovu: Tabulka bude vynechána a znovu vytvořena. Povinné při dynamickém vytváření nové tabulky.
* Zkrátit: Všechny řádky z cílové tabulky budou odebrány.

**Velikost dávky**: Určuje, kolik řádků se zapisuje v každém bloku. Větší dávkové velikosti zlepšují optimalizaci komprese a paměti, ale při ukládání dat do mezipaměti riskují výjimky z paměti.

**Skripty před a po SQL:** Zadejte víceřádkové skripty SQL, které budou spuštěny před zápisem (předběžného zpracování) a po zápisu dat (následného zpracování) do databáze Dřezu.

![skripty pro zpracování SQL před a po](media/data-flow/prepost1.png "Skripty pro zpracování SQL")

## <a name="data-type-mapping-for-azure-sql-database"></a>Mapování datových typů pro Azure SQL Database

Když se data zkopírují z nebo do Azure SQL Database, následující mapování se používají z datových typů Azure SQL Database do dočasných datových typů Azure Data Factory. Informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ do jímky, naleznete v [tématu Schémata a mapování datových typů](copy-activity-schema-and-type-mapping.md).

| Datový typ Azure SQL Database | Dočasný datový typ Azure Data Factory |
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
| tinyint |Byte |
| uniqueidentifier |Identifikátor GUID |
| Varbinary |Bajt[] |
| varchar |Řetězec, Znak[] |
| xml |XML |

>[!NOTE]
> U datových typů, které se mapují na desítkový prozatímní typ, aktuálně Azure Data Factory podporuje přesnost až 28. Pokud máte data s přesností větší než 28, zvažte převod na řetězec v dotazu SQL.

## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Vlastnosti aktivity GetMetadata

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v Azure Data Factory najdete v [tématu Podporovaná úložiště a formáty dat](copy-activity-overview.md#supported-data-stores-and-formats).
