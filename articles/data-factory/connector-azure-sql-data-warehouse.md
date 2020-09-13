---
title: Kopírování a transformace dat v Azure synapse Analytics
description: Naučte se kopírovat data do a z Azure synapse Analytics a transformovat data v Azure synapse Analytics pomocí Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/28/2020
ms.openlocfilehash: 01e2a2db3757e8d13749faf53b47300c8188915e
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2020
ms.locfileid: "89484472"
---
# <a name="copy-and-transform-data-in-azure-synapse-analytics-formerly-sql-data-warehouse-by-using-azure-data-factory"></a>Kopírování a transformace dat ve službě Azure synapse Analytics (dříve SQL Data Warehouse) pomocí Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
>
> - [Version1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> - [Aktuální verze](connector-azure-sql-data-warehouse.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory kopírovat data z a do Azure synapse Analytics a jak transformovat data v Azure Data Lake Storage Gen2 pomocí toku dat. Pokud se chcete dozvědět o Azure Data Factory, přečtěte si [úvodní článek](introduction.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Azure synapse Analytics se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou tabulkou matice zdroje/jímky](copy-activity-overview.md)
- [Mapování toku dat](concepts-data-flow-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)

U aktivity kopírování tento konektor Azure synapse Analytics podporuje tyto funkce:

- Zkopírujte data pomocí ověřování SQL a Azure Active Directory (Azure AD) ověřování tokenu aplikace pomocí instančního objektu nebo spravovaných identit pro prostředky Azure.
- Jako zdroj načte data pomocí dotazu SQL nebo uložené procedury. Můžete si také vybrat paralelní kopírování ze zdroje Azure synapse Analytics. Podrobnosti najdete v části [paralelní kopírování z synapse Analytics](#parallel-copy-from-synapse-analytics) .
- Jako jímky načtěte data pomocí příkazu [Base](#use-polybase-to-load-data-into-azure-synapse-analytics) nebo [copy](#use-copy-statement) (Preview) nebo hromadného vložení. Pro lepší výkon kopírování doporučujeme použít příkaz Base nebo COPY (Preview). Konektor také podporuje automatické vytváření cílových tabulek, pokud na základě zdrojového schématu neexistuje.

> [!IMPORTANT]
> Pokud kopírujete data pomocí Azure Data Factory Integration Runtime, nakonfigurujte [pravidlo brány firewall na úrovni serveru](../azure-sql/database/firewall-configure.md) tak, aby služby Azure mohly přistupovat k [logickému SQL serveru](../azure-sql/database/logical-servers.md).
> Pokud kopírujete data pomocí místního prostředí Integration runtime, nakonfigurujte bránu firewall tak, aby povolovala příslušný rozsah IP adres. Tento rozsah zahrnuje IP adresu počítače, která se používá pro připojení ke službě Azure synapse Analytics.

## <a name="get-started"></a>Začínáme

> [!TIP]
> Abyste dosáhli nejlepšího výkonu, využijte základ k načtení dat do služby Azure synapse Analytics. Informace najdete v části [použití základu k načtení dat do služby Azure synapse Analytics](#use-polybase-to-load-data-into-azure-synapse-analytics) . Návod s případem použití najdete v tématu [načtení 1 TB do služby Azure synapse Analytics za 15 minut s Azure Data Factory](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které definují Data Factory entit specifických pro konektor Azure synapse Analytics.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu Azure synapse Analytics jsou podporovány následující vlastnosti:

| Vlastnost            | Popis                                                  | Povinné                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| typ                | Vlastnost Type musí být nastavená na **AzureSqlDW**.             | Yes                                                          |
| připojovací řetězec    | Zadejte informace potřebné pro připojení ke službě Azure synapse Analytics instance pro vlastnost **ConnectionString** . <br/>Označte toto pole jako SecureString a bezpečně ho uložte do Data Factory. Můžete také do Azure Key Vault umístit klíč k hlavnímu názvu hesla nebo služby a v případě, že ověřování SQL vyžádá `password` konfiguraci z připojovacího řetězce. Další podrobnosti najdete v příkladech JSON pod tabulkou a [přihlašovacími údaji pro Store v Azure Key Vault](store-credentials-in-key-vault.md) článku. | Yes                                                          |
| servicePrincipalId  | Zadejte ID klienta aplikace.                         | Ano, pokud použijete ověřování Azure AD s instančním objektem. |
| servicePrincipalKey | Zadejte klíč aplikace. Označte toto pole jako SecureString, abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | Ano, pokud použijete ověřování Azure AD s instančním objektem. |
| tenant              | Zadejte informace o tenantovi (název domény nebo ID tenanta), pod kterým se vaše aplikace nachází. Můžete ho načíst tak, že najedete myší v pravém horním rohu Azure Portal. | Ano, pokud použijete ověřování Azure AD s instančním objektem. |
| azureCloudType | Pro ověřování instančního objektu zadejte typ cloudového prostředí Azure, ve kterém je vaše aplikace Azure AD zaregistrovaná. <br/> Povolené hodnoty jsou **AzurePublic**, **AzureChina**, **AzureUsGovernment**a **AzureGermany**. Ve výchozím nastavení se používá cloudové prostředí pro datovou továrnu. | No |
| connectVia          | [Prostředí Integration runtime](concepts-integration-runtime.md) , které se má použít pro připojení k úložišti dat. Můžete použít Azure Integration Runtime nebo místní prostředí Integration runtime (Pokud se vaše úložiště dat nachází v privátní síti). Pokud není zadaný, použije se výchozí Azure Integration Runtime. | No                                                           |

Pro různé typy ověřování se podívejte na následující oddíly týkající se požadavků a ukázek JSON, v uvedeném pořadí:

- [Ověřování SQL](#sql-authentication)
- Ověřování tokenu aplikací služby Azure AD: [instanční objekt](#service-principal-authentication)
- Ověřování tokenu aplikací Azure AD: [spravované identity pro prostředky Azure](#managed-identity)

>[!TIP]
>Pokud jste narazili na chybu s kódem chyby jako "UserErrorFailedToConnectToSqlServer" a zprávu, jako je "omezení relace pro databázi je XXX a bylo dosaženo.", přidejte `Pooling=false` do svého připojovacího řetězce a zkuste to znovu.

### <a name="sql-authentication"></a>Ověřování SQL

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Příklad propojené služby, který používá ověřování SQL

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

**Heslo v Azure Key Vault:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
            "password": {
                "type": "AzureKeyVaultSecret",
                "store": {
                    "referenceName": "<Azure Key Vault linked service name>",
                    "type": "LinkedServiceReference"
                },
                "secretName": "<secretName>"
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

Chcete-li použít ověřování pomocí tokenu aplikace služby Azure AD založené na instančním objektu, postupujte podle následujících kroků:

1. **[Vytvořte aplikaci Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)** z Azure Portal. Poznamenejte si název aplikace a následující hodnoty, které definují propojenou službu:

   - ID aplikace
   - Klíč aplikace
   - ID tenanta

2. Pokud jste to ještě neudělali, **[zřídí správce Azure Active Directory](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database)** pro server v Azure Portal. Správce Azure AD může být uživatel Azure AD nebo skupina Azure AD. Pokud skupině udělíte roli správce spravovanou identitou, přeskočte kroky 3 a 4. Správce bude mít úplný přístup k databázi.

3. **[Vytvořte uživatele databáze s omezením](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)** pro instanční objekt. Připojte se k datovému skladu z nebo do kterého chcete kopírovat data pomocí nástrojů jako SSMS, s identitou Azure AD, která má alespoň změnu oprávnění uživatele. Spusťte následující příkaz T-SQL:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Udělte instančnímu objektu potřebná oprávnění** , jako byste to udělali normálně pro uživatele SQL nebo jiné. Spusťte následující kód, nebo se [sem](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)podívejte na další možnosti. Pokud chcete k načtení dat použít základ, Projděte si [požadované oprávnění databáze](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. **Nakonfigurujte propojenou službu Azure synapse Analytics** v Azure Data Factory.

#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Příklad propojené služby, který používá ověřování instančního objektu

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Spravované identity pro ověřování prostředků Azure

Datová továrna může být přidružená ke [spravované identitě pro prostředky Azure](data-factory-service-identity.md) , které představují konkrétní továrnu. Tuto spravovanou identitu můžete použít pro ověřování Azure synapse Analytics. Určená továrna má přístup k datovému skladu a jejich kopírování pomocí této identity.

Pokud chcete použít spravované ověřování identity, postupujte podle těchto kroků:

1. Pokud jste to ještě neudělali, **[zřídí správce Azure Active Directory](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database)** serveru na Azure Portal. Správce Azure AD může být uživatel Azure AD nebo skupina Azure AD. Pokud skupině udělíte roli správce spravovanou identitou, přeskočte kroky 3 a 4. Správce bude mít úplný přístup k databázi.

2. **[Vytvořte uživatele databáze s omezením](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)** pro Data Factory spravovanou identitu. Připojte se k datovému skladu z nebo do kterého chcete kopírovat data pomocí nástrojů jako SSMS, s identitou Azure AD, která má alespoň změnu oprávnění uživatele. Spusťte následující příkaz T-SQL.
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Udělte Data Factory spravovaná identita potřebná** k tomu, aby se běžně daly dělat pro uživatele SQL a jiné. Spusťte následující kód, nebo se [sem](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)podívejte na další možnosti. Pokud chcete k načtení dat použít základ, Projděte si [požadované oprávnění databáze](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

4. **Nakonfigurujte propojenou službu Azure synapse Analytics** v Azure Data Factory.

**Příklad:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) .

Pro datovou sadu Azure synapse Analytics jsou podporovány následující vlastnosti:

| Vlastnost  | Popis                                                  | Povinné                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| typ      | Vlastnost **Type** datové sady musí být nastavená na **AzureSqlDWTable**. | Yes                         |
| schema | Název schématu. |Ne pro zdroj, Ano pro jímku  |
| stolu | Název tabulky/zobrazení |Ne pro zdroj, Ano pro jímku  |
| tableName | Název tabulky nebo zobrazení se schématem. Tato vlastnost je podporována z důvodu zpětné kompatibility. Pro nové úlohy použijte `schema` a `table` . | Ne pro zdroj, Ano pro jímku |

### <a name="dataset-properties-example"></a>Příklad vlastností datové sady

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure Synapse Analytics linked service name>",
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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností podporovaných zdrojem a jímkou Azure synapse Analytics.

### <a name="azure-synapse-analytics-as-the-source"></a>Azure synapse Analytics jako zdroj

>[!TIP]
>Pokud chcete data z Azure synapse Analytics efektivně načíst pomocí dělení dat, přečtěte si další informace z [paralelního kopírování z synapse Analytics](#parallel-copy-from-synapse-analytics).

Pokud chcete kopírovat data z Azure synapse Analytics, nastavte vlastnost **typ** ve zdroji aktivity kopírování na **SqlDWSource**. V části **zdroj** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost                     | Popis                                                  | Povinné |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| typ                         | Vlastnost **Type** zdroje aktivity kopírování musí být nastavená na **SqlDWSource**. | Yes      |
| sqlReaderQuery               | Pro čtení dat použijte vlastní dotaz SQL. Příklad: `select * from MyTable`. | No       |
| sqlReaderStoredProcedureName | Název uložené procedury, která čte data ze zdrojové tabulky. Poslední příkaz SQL musí být příkaz SELECT v uložené proceduře. | No       |
| storedProcedureParameters    | Parametry pro uloženou proceduru.<br/>Povolené hodnoty jsou páry název-hodnota. Názvy a malá písmena parametrů se musí shodovat s názvy a písmeny parametrů uložené procedury. | No       |
| isolationLevel | Určuje chování při zamykání transakcí pro zdroj SQL. Povolené hodnoty jsou: **ReadCommitted**, **READUNCOMMITTED**, **RepeatableRead**, **serializovatelný**, **Snapshot**. Pokud není zadaný, použije se výchozí úroveň izolace databáze. Další podrobnosti najdete v [tomto dokumentu](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) . | No |
| partitionOptions | Určuje možnosti dělení dat používané při načítání dat ze služby Azure synapse Analytics. <br>Povolené hodnoty jsou: **none** (default), **PhysicalPartitionsOfTable**a **DynamicRange**.<br>Když je povolená možnost oddílu (to znamená, že ne `None` ), stupeň paralelismu na souběžně načtená data z Azure synapse Analytics se řídí [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) nastavením aktivity kopírování. | No |
| partitionSettings | Určete skupinu nastavení pro dělení dat. <br>Použijte, pokud možnost partition není `None` . | No |
| ***V části `partitionSettings` :*** | | |
| partitionColumnName | Zadejte název zdrojového sloupce **v typu Integer nebo Date/DateTime** , který bude použit pro vytváření oddílů rozsahu pro paralelní kopírování. Pokud není zadaný, index nebo primární klíč tabulky se automaticky zjistí a použije se jako sloupec partition.<br>Použijte, pokud je parametr partition `DynamicRange` . Použijete-li dotaz k načtení zdrojových dat, zapojte  `?AdfDynamicRangePartitionCondition ` v klauzuli WHERE. Příklad naleznete v části [paralelní kopírování z databáze SQL](#parallel-copy-from-synapse-analytics) . | No |
| partitionUpperBound | Maximální hodnota sloupce oddílu pro rozdělení rozsahu oddílu Tato hodnota se používá k určení rozteči oddílu, nikoli pro filtrování řádků v tabulce. Všechny řádky v tabulce nebo výsledku dotazu budou rozděleny na oddíly a zkopírovány. Pokud není zadaný, aktivita kopírování automaticky detekuje hodnotu.  <br>Použijte, pokud je parametr partition `DynamicRange` . Příklad naleznete v části [paralelní kopírování z databáze SQL](#parallel-copy-from-synapse-analytics) . | No |
| partitionLowerBound | Minimální hodnota sloupce oddílu pro rozdělení rozsahu oddílů. Tato hodnota se používá k určení rozteči oddílu, nikoli pro filtrování řádků v tabulce. Všechny řádky v tabulce nebo výsledku dotazu budou rozděleny na oddíly a zkopírovány. Pokud není zadaný, aktivita kopírování automaticky detekuje hodnotu.<br>Použijte, pokud je parametr partition `DynamicRange` . Příklad naleznete v části [paralelní kopírování z databáze SQL](#parallel-copy-from-synapse-analytics) . | No |

**Příklad: použití dotazu SQL**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Synapse Analytics input dataset name>",
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

**Příklad: použití uložené procedury**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Synapse Analytics input dataset name>",
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

**Ukázková uložená procedura:**

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

### <a name="azure-synapse-analytics-as-sink"></a><a name="azure-sql-data-warehouse-as-sink"></a> Azure synapse Analytics jako jímku

Azure Data Factory podporuje tři způsoby, jak načíst data do služby Azure synapse Analytics.

![Možnosti kopírování jímky Azure synapse Analytics](./media/connector-azure-sql-data-warehouse/sql-dw-sink-copy-options.png)

- [Použít základ](#use-polybase-to-load-data-into-azure-synapse-analytics)
- [Použít příkaz COPY (Preview)](#use-copy-statement)
- Použít hromadné vložení

Nejrychlejší a nejškálovatelný způsob, jak načíst data, je prostřednictvím [základny](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) nebo [příkazu copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (Preview).

Pokud chcete kopírovat data do služby Azure synapse Analytics, nastavte typ jímky v aktivitě kopírování na **SqlDWSink**. V části **jímka** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost          | Popis                                                  | Povinné                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| typ              | Vlastnost **Type** jímky aktivity kopírování musí být nastavená na **SqlDWSink**. | Yes                                           |
| allowPolyBase     | Označuje, jestli se má k načtení dat do služby Azure synapse Analytics použít základ. `allowCopyCommand` a `allowPolyBase` nemůže být současně true. <br/><br/>Omezení a podrobnosti najdete v části [použití základu k načtení dat do služby Azure synapse Analytics](#use-polybase-to-load-data-into-azure-synapse-analytics) .<br/><br/>Povolené hodnoty jsou **true** a **false** (výchozí). | No.<br/>Platí při použití základny.     |
| polyBaseSettings  | Skupina vlastností, které lze zadat, pokud `allowPolybase` je vlastnost nastavena na **hodnotu true**. | No.<br/>Platí při použití základny. |
| allowCopyCommand | Označuje, jestli se má při načítání dat do služby Azure synapse Analytics použít [příkaz Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (Preview). `allowCopyCommand` a `allowPolyBase` nemůže být současně true. <br/><br/>Omezení a podrobnosti najdete v části [použití příkazu copy k načtení dat do služby Azure synapse Analytics](#use-copy-statement) .<br/><br/>Povolené hodnoty jsou **true** a **false** (výchozí). | No.<br>Použijte při použití kopírování. |
| copyCommandSettings | Skupina vlastností, které lze zadat, pokud `allowCopyCommand` je vlastnost nastavena na hodnotu true. | No.<br/>Použijte při použití kopírování. |
| writeBatchSize    | Počet řádků, které mají být vloženy do tabulky SQL **na dávku**.<br/><br/>Povolená hodnota je **Integer** (počet řádků). Ve výchozím nastavení Data Factory dynamicky určí vhodnou velikost dávky na základě velikosti řádku. | No.<br/>Platí při použití hromadného vložení.     |
| writeBatchTimeout | Počkejte, než se operace dávkového vložení dokončí, než vyprší časový limit.<br/><br/>Povolená hodnota je **TimeSpan**. Příklad: "00:30:00" (30 minut). | No.<br/>Platí při použití hromadného vložení.        |
| preCopyScript     | Zadejte dotaz SQL pro aktivitu kopírování, která se spustí před zápisem dat do služby Azure synapse Analytics v každém spuštění. Tato vlastnost slouží k vyčištění předem načtených dat. | No                                            |
| tableOption | Určuje, jestli se má [automaticky vytvořit tabulka jímky](copy-activity-overview.md#auto-create-sink-tables) , pokud na základě schématu zdroje neexistuje. Povolené hodnoty jsou: `none` (výchozí), `autoCreate` . |No |
| disableMetricsCollection | Data Factory shromažďuje metriky, jako je Azure synapse Analytics DWU, pro optimalizaci výkonu a doporučení pro kopírování. Pokud se s tímto chováním obáváte, určete, jestli `true` ho chcete vypnout. | Ne (výchozí nastavení je `false` ) |

#### <a name="azure-synapse-analytics-sink-example"></a>Příklad jímky Azure synapse Analytics

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

## <a name="parallel-copy-from-synapse-analytics"></a>Paralelní kopírování z synapse Analytics

Konektor Azure synapse Analytics v aktivitě kopírování poskytuje integrované datové oddíly pro kopírování dat paralelně. Možnosti dělení dat můžete najít na kartě **zdroj** aktivity kopírování.

![Snímek obrazovky s možnostmi oddílů](./media/connector-sql-server/connector-sql-partition-options.png)

Pokud povolíte kopírování rozdělené na oddíly, aktivita kopírování spustí paralelní dotazy na zdrojovém zdroji služby Azure synapse Analytics a načte data podle oddílů. Paralelní míra je řízena [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) nastavením aktivity kopírování. Pokud jste například nastavili `parallelCopies` čtyři, Data Factory souběžně vygeneruje a spustí čtyři dotazy na základě zadané možnosti oddílu a nastavení a každý dotaz načte část dat ze služby Azure synapse Analytics.

Navrhnete, abyste umožnili paralelní kopírování s vytvářením oddílů dat, zejména pokud načítáte velké množství dat z Azure synapse Analytics. Následují Doporučené konfigurace pro různé scénáře. Při kopírování dat do úložiště dat založeného na souborech se doporučuje zapisovat do složky jako více souborů (zadat jenom název složky). v takovém případě je výkon lepší než zápis do jediného souboru.

| Scénář                                                     | Navrhovaná nastavení                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Úplné načtení z velké tabulky s fyzickými oddíly.        | **Možnost oddílu**: fyzické oddíly tabulky. <br><br/>Během provádění Data Factory automaticky detekuje fyzické oddíly a kopíruje data podle oddílů. |
| Úplné načtení z velké tabulky bez fyzických oddílů při použití celého čísla nebo sloupce data a času pro dělení dat. | **Možnosti oddílu**: dynamický oddíl rozsahu.<br>**Sloupec oddílu** (volitelné): Zadejte sloupec, který se používá k dělení dat. Pokud není zadaný, použije se sloupec index nebo primární klíč.<br/>**Horní hranice oddílu** a **dolní mez oddílu** (volitelné): Určete, jestli chcete určit rozteč oddílu. Nejedná se o filtrování řádků v tabulce, přičemž všechny řádky v tabulce budou rozděleny na oddíly a zkopírovány. Pokud není zadaný, aktivita kopírování automaticky detekuje hodnoty.<br><br>Například pokud má sloupec oddílu "ID" rozsah hodnot od 1 do 100 a nastavíte dolní mez na hodnotu 20 a horní mez jako 80, s paralelní kopií as 4 Data Factory načte data podle 4 identifikátorů oddílů – ID v rozsahu <= 20, [21, 50], [51, 80] a >= 81, v uvedeném pořadí. |
| Načtěte velké množství dat pomocí vlastního dotazu, bez fyzických oddílů, zatímco se sloupcem typu Integer nebo Date/DateTime pro dělení dat. | **Možnosti oddílu**: dynamický oddíl rozsahu.<br>**Dotaz**: `SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>` .<br>**Partition – sloupec**: Určete sloupec, který se používá k dělení dat.<br>**Horní hranice oddílu** a **dolní mez oddílu** (volitelné): Určete, jestli chcete určit rozteč oddílu. Nejedná se o filtrování řádků v tabulce, všechny řádky ve výsledku dotazu budou rozděleny na oddíly a zkopírovány. Pokud není zadaný, aktivita kopírování automaticky detekuje hodnotu.<br><br>Při provádění Data Factory nahrazuje `?AdfRangePartitionColumnName` skutečný název sloupce a rozsahy hodnot pro každý oddíl a odesílá je do Azure synapse Analytics. <br>Například pokud má sloupec oddílu "ID" rozsah hodnot od 1 do 100 a nastavíte dolní mez na hodnotu 20 a horní mez jako 80, s paralelní kopií as 4 Data Factory načte data podle 4 identifikátorů oddílů – ID v rozsahu <= 20, [21, 50], [51, 80] a >= 81, v uvedeném pořadí. |

Osvědčené postupy načítání dat s možností oddílu:

1. Chcete-li se vyhnout zkosení dat, vyberte sloupec s výrazným označením jako sloupec oddílu (například primární klíč nebo jedinečný klíč). 
2. Pokud tabulka obsahuje vestavěný oddíl, použijte možnost oddíl "fyzické oddíly tabulky", abyste získali lepší výkon.
3. Pokud ke kopírování dat použijete Azure Integration Runtime, můžete nastavit větší "[jednotky integrace dat (diú)](copy-activity-performance-features.md#data-integration-units)" (>4), abyste využili více výpočetních prostředků. Projděte si příslušné scénáře.
4. "[Stupeň kopírování paralelismus](copy-activity-performance-features.md#parallel-copy)" řídí čísla oddílů a nastavení tohoto počtu je příliš velké, neuškodí výkon, doporučuje se nastavit toto číslo jako (diú nebo počet hostitelských uzlů IR) * (2 až 4).
5. Poznámka: Azure synapse Analytics může během chvilky spustit maximálně 32 dotazů, přičemž nastavení "stupeň kopírování paralelismus" je moc velké, může způsobit problém s omezením synapse.

**Příklad: úplné načtení z velké tabulky s fyzickými oddíly**

```json
"source": {
    "type": "SqlDWSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Příklad: dotaz s dynamickým oddílem rozsahu**

```json
"source": {
    "type": "SqlDWSource",
    "query": "SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column (optional) to decide the partition stride, not as data filter>",
        "partitionLowerBound": "<lower_value_of_partition_column (optional) to decide the partition stride, not as data filter>"
    }
}
```

## <a name="use-polybase-to-load-data-into-azure-synapse-analytics"></a>Použití základny k načtení dat do služby Azure synapse Analytics

Použití [základny](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) je účinný způsob, jak načíst velké množství dat do Azure synapse Analytics s vysokou propustností. Místo výchozího mechanismu BULKINSERT se zobrazí velký nárůst propustnosti pomocí základu. Návod s případem použití najdete v tématu [načtení 1 TB do služby Azure synapse Analytics](v1/data-factory-load-sql-data-warehouse.md).

- Pokud jsou vaše zdrojová data ve **službě Azure Blob, Azure Data Lake Storage Gen1 nebo Azure Data Lake Storage Gen2**a **formát je**standardně kompatibilní, můžete k přímému vyvolání základny použít aktivitu kopírování a umožnit tak službě Azure synapse Analytics načíst data ze zdroje. Podrobnosti najdete v tématu **[Přímá kopie pomocí základu](#direct-copy-by-using-polybase)**.
- Pokud se vaše zdrojové úložiště dat a formát v základu nepodporují, použijte místo toho funkci **[dvoufázové kopie pomocí základní](#staged-copy-by-using-polybase)** funkce. Funkce dvoufázové kopírování nabízí také lepší propustnost. Automaticky převádí data do formátu kompatibilního se standardem, ukládá data do úložiště objektů BLOB v Azure a pak volá základnu, aby načetla data do Azure synapse Analytics.

> [!TIP]
> Přečtěte si další informace o [osvědčených postupech pro použití základny](#best-practices-for-using-polybase).

V rámci aktivity kopírování jsou podporovány následující základní nastavení `polyBaseSettings` :

| Vlastnost          | Popis                                                  | Povinné                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| rejectValue       | Určuje počet nebo procento řádků, které lze odmítnout před tím, než se dotaz nezdařil.<br/><br/>Další informace o možnostech odmítnutí základní třídy najdete v oddílu argumenty v tématu [vytvoření externí tabulky (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). <br/><br/>Povolené hodnoty jsou 0 (výchozí), 1, 2 atd. | No                                            |
| rejectType        | Určuje, zda je možnost **rejectValue** hodnotou literálu nebo procentem.<br/><br/>Povolené hodnoty jsou **hodnota** (výchozí) a **procento**. | No                                            |
| Rejecttype rejectsamplevalue | Určuje počet řádků, které se mají načíst před tím, než základ přepočítá procento odmítnutých řádků.<br/><br/>Povolené hodnoty jsou 1, 2 atd. | Ano, pokud je **rejectType** **procent**. |
| useTypeDefault    | Určuje, jak se mají zpracovat chybějící hodnoty v textových souborech s oddělovači, když základ dat načte data z textového souboru.<br/><br/>Přečtěte si další informace o této vlastnosti z oddílu argumenty v tématu [Create External File Format (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Povolené hodnoty jsou **true** a **false** (výchozí).<br><br> | No                                            |

### <a name="direct-copy-by-using-polybase"></a>Přímá kopie pomocí základu

Základna Azure synapse Analytics podporuje přímo Azure Blob, Azure Data Lake Storage Gen1 a Azure Data Lake Storage Gen2. Pokud zdrojová data splňují kritéria popsaná v této části, použijte základnu pro kopírování přímo ze zdrojového úložiště dat do služby Azure synapse Analytics. V opačném případě použijte [dvoufázové kopírování pomocí základny](#staged-copy-by-using-polybase).

> [!TIP]
> Chcete-li efektivně kopírovat data do služby Azure synapse Analytics, přečtěte si další informace z [Azure Data Factory je ještě snazší a pohodlnější při použití Data Lake Store s Azure synapse Analytics](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Pokud požadavky nejsou splněné, Azure Data Factory zkontroluje nastavení a automaticky se vrátí k BULKINSERT mechanismu přesunu dat.

1. **Zdrojová propojená služba** je s následujícími typy a metodami ověřování:

    | Typ podporovaného zdrojového úložiště dat                             | Typ podporovaného ověřování zdroje                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Azure Blob](connector-azure-blob-storage.md)                | Ověřování klíčů účtu, ověřování spravované identity |
    | [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | Ověřování instančních objektů                            |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | Ověřování klíčů účtu, ověřování spravované identity |

    >[!IMPORTANT]
    >Pokud je vaše Azure Storage nakonfigurovaná pomocí koncového bodu služby virtuální sítě, musíte použít spravované ověřování identity – Přečtěte si, [Jaký je dopad použití koncových bodů služby virtuální sítě se službou Azure Storage](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Přečtěte si o požadovaných konfiguracích v Data Factory z oddílu [ověřování identity spravovaného pomocí objektů BLOB v Azure](connector-azure-blob-storage.md#managed-identity) a v části [ověřování identity spravované Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#managed-identity) .

2. **Zdrojový formát dat** je **Parquet**, **ORC**nebo **text s oddělovači**, s následujícími konfiguracemi:

   1. Cesta ke složce neobsahuje filtr zástupných znaků.
   2. Název souboru je prázdný nebo odkazuje na jeden soubor. Pokud v aktivitě kopírování zadáte název souboru se zástupnými znaky, může to být pouze `*` nebo `*.*` .
   3. `rowDelimiter` je **Výchozí hodnota**, **\n**, **\r\n**nebo **\r**.
   4. `nullValue` je ponechán jako výchozí nebo nastaven na **prázdný řetězec** ("") a `treatEmptyAsNull` je ponechán jako výchozí nebo nastaven na hodnotu true.
   5. `encodingName` je ponechán jako výchozí nebo nastavený na **UTF-8**.
   6. `quoteChar`, `escapeChar` a `skipLineCount` nejsou zadány. Základní podpora – přeskočit řádek záhlaví, který se dá nakonfigurovat jako `firstRowAsHeader` v ADF.
   7. `compression` nemůže být **žádná komprese**, **gzip**nebo **Deflate**.

3. Pokud je zdrojem složka, `recursive` musí být v aktivitě kopírování nastavena hodnota true (pravda).

4. `wildcardFolderPath` nejsou `wildcardFilename` zadány,,, `modifiedDateTimeStart` `modifiedDateTimeEnd` , `prefix` `enablePartitionDiscovery` a `additionalColumns` .

>[!NOTE]
>Pokud je zdrojem složka, Poznámka základem načte soubory ze složky a všech jejích podsložek a nenačte data ze souborů, pro které název souboru začíná podtržítkem (_) nebo tečkou (.), jak je uvedeno [tady: argument Location](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest#arguments-2).

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "ParquetDataset",
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
                "type": "ParquetSource",
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-by-using-polybase"></a>Příprava kopírování pomocí základu

Pokud vaše zdrojová data nejsou nativně kompatibilní s základnu, povolte kopírování dat prostřednictvím dočasné pracovní instance služby Azure Blob Storage (nemůže to být Azure Premium Storage). V tomto případě Azure Data Factory automaticky převede data tak, aby splňovala požadavky na formát dat základu. Potom vyvolá základ k načtení dat do služby Azure synapse Analytics. Nakonec vymaže vaše dočasná data z úložiště objektů BLOB. Podrobnosti o kopírování dat pomocí pracovní instance úložiště objektů BLOB v Azure najdete v tématu [připravené kopírování](copy-activity-performance-features.md#staged-copy) .

Pokud chcete tuto funkci použít, vytvořte [propojenou službu azure BLOB Storage](connector-azure-blob-storage.md#linked-service-properties) , která odkazuje na účet služby Azure Storage s dočasným úložištěm objektů BLOB. Pak zadejte `enableStaging` vlastnosti a `stagingSettings` aktivity kopírování, jak je znázorněno v následujícím kódu.

>[!IMPORTANT]
>Pokud je vaše pracovní Azure Storage nakonfigurovaná pomocí koncového bodu služby virtuální sítě, musíte použít spravované ověřování identity – Přečtěte si [dopad použití koncových bodů služby virtuální sítě se službou Azure Storage](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Přečtěte si o požadovaných konfiguracích v Data Factory z [ověřování identity spravovaného objektem BLOB v Azure](connector-azure-blob-storage.md#managed-identity).

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

### <a name="best-practices-for-using-polybase"></a>Osvědčené postupy pro použití základu

Následující části obsahují osvědčené postupy kromě těch, které jsou uvedené v článku [osvědčené postupy pro Azure synapse Analytics](../synapse-analytics/sql/best-practices-sql-pool.md).

#### <a name="required-database-permission"></a>Požadovaná oprávnění databáze

Pokud chcete použít základnu, uživatel, který načte data do služby Azure synapse Analytics, musí mít v cílové databázi [oprávnění Control](https://msdn.microsoft.com/library/ms191291.aspx) . Jedním ze způsobů, jak toho dosáhnout, je přidání uživatele jako člena role **db_owner** . Přečtěte si, jak to udělat v [přehledu Azure synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

#### <a name="row-size-and-data-type-limits"></a>Omezení velikosti řádku a datového typu

Základní zatížení je omezeno na řádky menší než 1 MB. Nedá se použít k načtení do VARCHR (MAX), NVARCHAR (MAX) nebo VARBINARY (MAX). Další informace najdete v tématu [omezení kapacity služby Azure synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Pokud má zdrojová data řádky větší než 1 MB, možná budete chtít svisle rozdělit zdrojové tabulky na několik malých. Ujistěte se, že největší velikost každého řádku nepřekračuje limit. Menší tabulky se pak dají načíst pomocí základu a sloučí se dohromady v Azure synapse Analytics.

Alternativně můžete pro data s takovými roztažitelnémi sloupci použít nezaloženou základnu k načtení dat pomocí automatického podavače (ADF) vypnutím nastavení "zapnout základnu".

#### <a name="azure-synapse-analytics-resource-class"></a>Třída prostředků Azure synapse Analytics

Abyste dosáhli nejlepší možné propustnosti, přiřaďte uživateli větší třídu prostředků, která načte data do služby Azure synapse Analytics prostřednictvím základu.

#### <a name="polybase-troubleshooting"></a>Řešení potíží se základem

**Načítání do desetinného sloupce**

Pokud jsou vaše zdrojová data v textovém formátu nebo v jiných nekompatibilních úložištích, která nejsou kompatibilní s primárním úložištěm (pomocí připraveného kopírování a základu), a obsahuje prázdnou hodnotu, která se načte do sloupce Decimal synapse Analytics pro Azure, může dojít k následující chybě:

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

Řešením je zrušit výběr možnosti**použít výchozí typ**(jako false) ve jímky aktivity kopírování – > základní nastavení. "[USE_TYPE_DEFAULT](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)" je základní nativní konfigurace, která určuje, jak se mají zpracovat chybějící hodnoty v textových souborech s oddělovači, když základ dat načte data z textového souboru.

**`tableName` ve službě Azure synapse Analytics**

V následující tabulce jsou uvedeny příklady, jak zadat vlastnost **TableName** v datové sadě JSON. Zobrazuje několik kombinací názvů schémat a tabulek.

| Schéma databáze | Název tabulky | **TableName** Vlastnost JSON               |
| --------- | ---------- | ----------------------------------------- |
| dbo       | MyTable    | MyTable nebo dbo. MyTable nebo [dbo]. MyTable |
| dbo1      | MyTable    | dbo1. MyTable nebo [dbo1]. MyTable          |
| dbo       | Moje tabulka   | [My. Table] nebo [dbo]. [My. Table]            |
| dbo1      | Moje tabulka   | [dbo1]. [My. Table]                         |

Pokud se zobrazí následující chyba, může být problémem hodnota, kterou jste zadali pro vlastnost **TableName** . Správný způsob, jak zadat hodnoty pro vlastnost **TableName** JSON, najdete v předchozí tabulce.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

**Sloupce s výchozími hodnotami**

V současné době funkce Base v Data Factory přijímá pouze stejný počet sloupců jako v cílové tabulce. Příkladem je tabulka se čtyřmi sloupci, kde jedna z nich je definována s výchozí hodnotou. Vstupní data stále musí mít čtyři sloupce. Vstupní datová sada se třemi sloupci poskytuje chybu podobnou této zprávě:

```
All columns of the table must be specified in the INSERT BULK statement.
```

Hodnota NULL je speciální forma výchozí hodnoty. Pokud sloupec může mít hodnotu null, vstupní data v objektu BLOB pro tento sloupec můžou být prázdná. Ve vstupní datové sadě ale nemůže chybět. Základní znak pro chybějící hodnoty ve službě Azure synapse Analytics vloží hodnotu NULL.

## <a name="use-copy-statement-to-load-data-into-azure-synapse-analytics-preview"></a><a name="use-copy-statement"></a> Použití příkazu COPY k načtení dat do služby Azure synapse Analytics (Preview)

[Příkaz Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) pro Azure synapse Analytics (Preview) přímo podporuje načítání dat z **Azure Blob a Azure Data Lake Storage Gen2**. Pokud zdrojová data splňují kritéria popsaná v této části, můžete použít příkaz COPY v ADF k načtení dat do služby Azure synapse Analytics. Azure Data Factory kontroluje nastavení a v případě, že nejsou splněná kritéria, se spustí aktivita kopírování.

>[!NOTE]
>V současné době Data Factory pouze podpora kopírování ze zdrojů kompatibilních se KOPÍROVÁNÍm, které jsou uvedeny níže.

Použití příkazu COPY podporuje následující konfiguraci:

1. **Zdrojová propojená služba a formát** mají následující typy a metody ověřování:

    | Typ podporovaného zdrojového úložiště dat                             | Podporovaný formát           | Typ podporovaného ověřování zdroje                         |
    | :----------------------------------------------------------- | -------------------------- | :----------------------------------------------------------- |
    | [Azure Blob](connector-azure-blob-storage.md)                | [Text oddělený textem](format-delimited-text.md)             | Ověřování klíčů účtu, ověřování sdíleného přístupového podpisu, ověřování instančního objektu, ověřování spravované identity |
    | &nbsp;                                                       | [Parquet](format-parquet.md)                    | Ověřování klíčů účtu, ověřování sdíleného přístupového podpisu |
    | &nbsp;                                                       | [ORC](format-orc.md)                        | Ověřování klíčů účtu, ověřování sdíleného přístupového podpisu |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | [Text oddělený textem](format-delimited-text.md)<br/>[Parquet](format-parquet.md)<br/>[ORC](format-orc.md) | Ověřování klíčů účtu, ověřování instančního objektu, ověřování spravované identity |

    >[!IMPORTANT]
    >Pokud je vaše Azure Storage nakonfigurovaná pomocí koncového bodu služby virtuální sítě, musíte použít spravované ověřování identity – Přečtěte si, [Jaký je dopad použití koncových bodů služby virtuální sítě se službou Azure Storage](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Přečtěte si o požadovaných konfiguracích v Data Factory z oddílu [ověřování identity spravovaného pomocí objektů BLOB v Azure](connector-azure-blob-storage.md#managed-identity) a v části [ověřování identity spravované Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#managed-identity) .

2. Nastavení formátu jsou následující:

   1. Pro **Parquet**: `compression` nemůže být **žádná komprese**, **přichycení**nebo **gzip**.
   2. Pro **ORC**: `compression` nemůže být **žádná komprese**, **```zlib```** nebo **přichycení**.
   3. Pro **text s oddělovačem**:
      1. `rowDelimiter` je explicitně nastaveno jako **jeden znak** nebo "**\r\n**", výchozí hodnota není podporována.
      2. `nullValue` je ponechán jako výchozí nebo nastaven na **prázdný řetězec** ("").
      3. `encodingName` je ponechán jako výchozí nebo nastavený na **UTF-8 nebo UTF-16**.
      4. `escapeChar` musí být stejný jako `quoteChar` a není prázdný.
      5. `skipLineCount` je ponechán jako výchozí nebo nastavený na 0.
      6. `compression` nemůže být **žádná komprese** ani **gzip**.

3. Je-li zdrojem složka, `recursive` musí být v aktivitě kopírování nastavena hodnota true a musí `wildcardFilename` být `*` . 

4. `wildcardFolderPath` nejsou `wildcardFilename` zadány, (jiné než `*` ),,,, `modifiedDateTimeStart` `modifiedDateTimeEnd` `prefix` `enablePartitionDiscovery` a `additionalColumns` .

V části aktivita kopírování jsou podporovány následující nastavení příkazu COPY `allowCopyCommand` :

| Vlastnost          | Popis                                                  | Povinné                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| defaultValues – výchozí | Určuje výchozí hodnoty pro každý cílový sloupec ve službě Azure synapse Analytics.  Výchozí hodnoty ve vlastnosti přepíší výchozí sadu omezení v datovém skladu a sloupec identity nemůže mít výchozí hodnotu. | No |
| additionalOptions | Další možnosti, které budou předány příkazu COPY Azure synapse Analytics přímo v klauzuli WITH v [příkazu copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest). Zarovnejte hodnotu podle potřeby, aby odpovídala požadavkům příkazu COPY. | No |

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaCOPY",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "ParquetDataset",
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
                "type": "ParquetSource",
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowCopyCommand": true,
                "copyCommandSettings": {
                    "defaultValues": [
                        {
                            "columnName": "col_string",
                            "defaultValue": "DefaultStringValue"
                        }
                    ],
                    "additionalOptions": {
                        "MAXERRORS": "10000",
                        "DATEFORMAT": "'ymd'"
                    }
                }
            },
            "enableSkipIncompatibleRow": true
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Mapování vlastností toku dat

Při transformaci dat v toku mapování dat můžete číst a zapisovat do tabulek z Azure synapse Analytics. Další informace najdete v tématu transformace [zdroje](data-flow-source.md) a [transformace jímky](data-flow-sink.md) v tématu mapování toků dat.

### <a name="source-transformation"></a>Transformace zdroje

Nastavení specifická pro Azure synapse Analytics jsou k dispozici na kartě **zdrojové možnosti** zdrojové transformace.

**Vstup** Vyberte, zda se má zdroj nasměrovat v tabulce (odpovídající hodnotě ```Select * from <table-name>``` ), nebo zadejte vlastní dotaz SQL.

**Povolit přípravu** Tato možnost se důrazně doporučuje používat v produkčních úlohách se zdroji synapse DW. Po spuštění aktivity toku dat se zdroji Synapase z kanálu vám ADF zobrazí výzvu k zadání účtu úložiště pro pracovní umístění a použije ho pro připravené načítání dat. Je to nejrychlejší mechanismus načítání dat z synapse DW.

**Dotaz**: Pokud ve vstupním poli vyberete možnost dotaz, zadejte pro zdroj dotaz SQL. Toto nastavení potlačí všechny tabulky, které jste vybrali v datové sadě. Klauzule **ORDER by** nejsou tady podporované, ale můžete nastavit úplný příkaz SELECT FROM. Můžete také použít uživatelsky definované funkce tabulky. **SELECT * FROM udfGetData ()** je UDF v SQL, který vrací tabulku. Tento dotaz vytvoří zdrojovou tabulku, kterou můžete použít v toku dat. Použití dotazů je také skvělým způsobem, jak omezit řádky pro testování nebo pro vyhledávání.

Příklad SQL: ```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Velikost dávky**: zadejte velikost dávky pro velké objemy dat v čtení. V datových tocích bude ADF pomocí tohoto nastavení nastavit sloupcovou mezipaměť Spark. Toto je pole možností, které použije výchozí hodnoty Sparku, pokud je ponecháno prázdné.

**Úroveň izolace**: ve výchozím nastavení pro zdroje SQL v toku dat mapování je čtení nepotvrzené. Úroveň izolace můžete změnit tady na jednu z těchto hodnot:

- Čtení potvrzeno
- Čtení nepotvrzených
- Opakované čtení
- Serializovatelné *-None (ignorovat úroveň izolace)

![Úroveň izolace](media/data-flow/isolationlevel.png "Úroveň izolace")

### <a name="sink-transformation"></a>Transformace jímky

Nastavení specifická pro Azure synapse Analytics jsou k dispozici na kartě **Nastavení** transformace jímky.

**Metoda aktualizace:** Určuje, jaké operace jsou pro cíl databáze povolené. Ve výchozím nastavení je povolen pouze vkládání. Chcete-li aktualizovat, Upsert nebo odstranit řádky, je pro označení řádků pro tyto akce nutná transformace ALTER Row. V případě aktualizací upsertuje a DELETE musí být klíčový sloupec nebo sloupce nastaveny k určení, který řádek má být změněn.

**Akce tabulky:** Určuje, zda mají být před zápisem znovu vytvořeny nebo odebrány všechny řádky z cílové tabulky.

- Žádné: v tabulce se neprovede žádná akce.
- Znovu vytvořit: tabulka se vynechá a znovu vytvoří. Požadováno při dynamickém vytváření nové tabulky.
- Zkrátit: všechny řádky z cílové tabulky se odeberou.

**Povolit přípravu:** Určuje, jestli se má při zápisu do Azure synapse Analytics použít [základ](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide?view=sql-server-ver15) .

**Velikost dávky**: Určuje, kolik řádků je v jednotlivých intervalech zapisováno. Větší velikosti dávek zlepšují kompresi a optimalizaci paměti, ale při ukládání dat do mezipaměti riskuje výjimky z paměti.

**Skripty před a po SQL**: zadejte víceřádkové skripty SQL, které se spustí před (před zpracováním) a po (po zpracování) se zapisují do databáze jímky.

![skripty pro zpracování před a po SQL](media/data-flow/prepost1.png "Skripty pro zpracování SQL")

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Vlastnosti aktivity GetMetadata

Pokud se chcete dozvědět víc o vlastnostech, podívejte se na [aktivitu GetMetadata](control-flow-get-metadata-activity.md) .

## <a name="data-type-mapping-for-azure-synapse-analytics"></a>Mapování datových typů pro Azure synapse Analytics

Když kopírujete data z nebo do služby Azure synapse Analytics, používají se následující mapování z datových typů Azure synapse Analytics k Azure Data Factory dočasných datových typů. Informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ do jímky, najdete v tématu [mapování typů schématu a dat](copy-activity-schema-and-type-mapping.md) .

>[!TIP]
>V článku věnovaném [datovým typům tabulky ve službě Azure synapse Analytics](../synapse-analytics/sql/develop-tables-data-types.md) najdete datové typy a alternativní řešení podporované v Azure synapse Analytics.

| Datový typ Azure synapse Analytics    | Data Factory pomocný datový typ |
| :------------------------------------ | :----------------------------- |
| bigint                                | Int64                          |
| binární                                | Byte []                         |
| bit                                   | Logická hodnota                        |
| char                                  | Řetězec, znak []                 |
| date                                  | Datum a čas                       |
| Datum a čas                              | Datum a čas                       |
| datetime2                             | Datum a čas                       |
| DateTimeOffset                        | DateTimeOffset                 |
| Decimal                               | Decimal                        |
| Atribut FILESTREAM (varbinary (max)) | Byte []                         |
| Float                                 | dvojité                         |
| image                                 | Byte []                         |
| int                                   | Int32                          |
| papír                                 | Decimal                        |
| nchar                                 | Řetězec, znak []                 |
| numerické                               | Decimal                        |
| nvarchar                              | Řetězec, znak []                 |
| real                                  | Jednoduché                         |
| rowversion                            | Byte []                         |
| smalldatetime                         | Datum a čas                       |
| smallint                              | Int16                          |
| smallmoney                            | Decimal                        |
| time                                  | TimeSpan                       |
| tinyint                               | Byte                           |
| uniqueidentifier                      | Identifikátor GUID                           |
| varbinary                             | Byte []                         |
| varchar                               | Řetězec, znak []                 |

## <a name="next-steps"></a>Další kroky

Seznam úložišť dat podporovaných jako zdroje a jímky pomocí aktivity kopírování v Azure Data Factory najdete v tématu [podporovaná úložiště dat a formáty](copy-activity-overview.md#supported-data-stores-and-formats).
