---
title: Kopírování a transformace dat v Azure SQL Database
description: Naučte se, jak kopírovat data do a z Azure SQL Database a transformovat data v Azure SQL Database pomocí Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/12/2020
ms.openlocfilehash: a4eab4b6d028f0a0cc22f92e072bc646d70c02da
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92901611"
---
# <a name="copy-and-transform-data-in-azure-sql-database-by-using-azure-data-factory"></a>Kopírování a transformace dat v Azure SQL Database pomocí Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi Azure Data Factory, kterou používáte:"]
>
> - [Verze 1](v1/data-factory-azure-sql-connector.md)
> - [Aktuální verze](connector-azure-sql-database.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

V tomto článku se naučíte, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z a do Azure SQL Database a jak transformovat data v Azure SQL Database pomocí toku dat. Pokud se chcete dozvědět o Azure Data Factory, přečtěte si [úvodní článek](introduction.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Azure SQL Database se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou tabulkou matice zdroje/jímky](copy-activity-overview.md)
- [Mapování toku dat](concepts-data-flow-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)

U aktivity kopírování tato Azure SQL Database konektor podporuje tyto funkce:

- Kopírování dat pomocí ověřování SQL a Azure Active Directory (Azure AD) ověřování tokenu aplikace pomocí instančního objektu nebo spravovaných identit pro prostředky Azure.
- Jako zdroj načítání dat pomocí dotazu SQL nebo uložené procedury. Můžete také zvolit paralelní kopírování ze zdroje Azure SQL Database, podrobnosti najdete v části [paralelní kopírování z databáze SQL](#parallel-copy-from-sql-database) .
- Pokud v závislosti na zdrojovém schématu neexistuje, vytvoří se jako jímka automaticky vytváření cílové tabulky. připojení dat k tabulce nebo vyvolání uložené procedury s vlastní logikou během kopírování.

Pokud používáte Azure SQL Database [úroveň bez serveru](../azure-sql/database/serverless-tier-overview.md), Všimněte si, že pokud je server pozastaven, spuštění aktivity se nezdařila a čeká na přípravu automatického obnovení. Můžete přidat opakování aktivity nebo řetězit další aktivity, abyste se ujistili, že je server po samotném spuštění živý.

>[!NOTE]
> Tento konektor teď nepodporuje Azure SQL Database [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) . Pokud chcete tento problém obejít, můžete použít [obecný konektor ODBC](connector-odbc.md) a SQL Server ovladač ODBC prostřednictvím prostředí Integration runtime v místním prostředí. Další informace najdete v části [použití Always Encrypted](#using-always-encrypted) . 

> [!IMPORTANT]
> Pokud kopírujete data pomocí prostředí Azure Integration runtime, nakonfigurujte [pravidlo brány firewall na úrovni serveru](../azure-sql/database/firewall-configure.md) tak, aby služby Azure mohly získat přístup k serveru.
> Pokud kopírujete data pomocí místního prostředí Integration runtime, nakonfigurujte bránu firewall tak, aby povolovala příslušný rozsah IP adres. Tento rozsah zahrnuje IP adresu počítače, která se používá pro připojení k Azure SQL Database.

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které slouží k definování Azure Data Factory entit specifických pro Azure SQL Database konektor.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Tyto vlastnosti jsou podporovány pro propojenou službu Azure SQL Database:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost **Type** musí být nastavená na **AzureSqlDatabase** . | Ano |
| připojovací řetězec | Zadejte informace potřebné pro připojení k instanci Azure SQL Database pro vlastnost **ConnectionString** . <br/>Do Azure Key Vault také můžete vložit heslo nebo hlavní klíč služby. Pokud se jedná o ověřování SQL, vyžádejte si z `password` připojovacího řetězce konfiguraci. Další informace najdete v příkladech JSON, které následují po tabulce, a [ukládají přihlašovací údaje v Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| servicePrincipalId | Zadejte ID klienta aplikace. | Ano, pokud používáte ověřování Azure AD s instančním objektem |
| servicePrincipalKey | Zadejte klíč aplikace. Označte toto pole jako **SecureString** a bezpečně ho uložte do Azure Data Factory nebo [odkaz na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | Ano, pokud používáte ověřování Azure AD s instančním objektem |
| tenant | Zadejte informace o tenantovi, jako je název domény nebo ID tenanta, pod kterým se vaše aplikace nachází. Načtěte ho tak, že najedete myší v pravém horním rohu Azure Portal. | Ano, pokud používáte ověřování Azure AD s instančním objektem |
| azureCloudType | Pro ověřování instančního objektu zadejte typ cloudového prostředí Azure, ve kterém je vaše aplikace Azure AD zaregistrovaná. <br/> Povolené hodnoty jsou **AzurePublic** , **AzureChina** , **AzureUsGovernment** a **AzureGermany** . Ve výchozím nastavení se používá cloudové prostředí pro datovou továrnu. | Ne |
| connectVia | Tento [modul runtime integrace](concepts-integration-runtime.md) se používá pro připojení k úložišti dat. Pokud se vaše úložiště dat nachází v privátní síti, můžete použít prostředí Azure Integration runtime nebo místní prostředí Integration runtime. Pokud tento parametr nezadáte, použije se výchozí prostředí Azure Integration runtime. | Ne |

Pro různé typy ověřování se podívejte na následující oddíly týkající se požadavků a ukázek JSON, v uvedeném pořadí:

- [Ověřování SQL](#sql-authentication)
- [Ověřování tokenu aplikací služby Azure AD: instanční objekt](#service-principal-authentication)
- [Ověřování tokenu aplikací Azure AD: spravované identity pro prostředky Azure](#managed-identity)

>[!TIP]
>Pokud dojde k chybě s kódem chyby "UserErrorFailedToConnectToSqlServer" a zprávou, jako je "omezení relace pro databázi je XXX a bylo dosaženo," přidejte `Pooling=false` do připojovacího řetězce a zkuste to znovu.

### <a name="sql-authentication"></a>Ověřování SQL

**Příklad: použití ověřování SQL**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Příklad: heslo v Azure Key Vault**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
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

1. [Vytvořte aplikaci Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) z Azure Portal. Poznamenejte si název aplikace a následující hodnoty, které definují propojenou službu:

    - ID aplikace
    - Klíč aplikace
    - ID tenanta

2. Pokud jste to ještě neudělali, [zřídí správce Azure Active Directory](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database) serveru na Azure Portal. Správce Azure AD musí být uživatel služby Azure AD nebo skupina Azure AD, ale nemůže se jednat o instanční objekt. Tento krok provedete tak, že v dalším kroku můžete pomocí identity Azure AD vytvořit uživatele databáze s omezením pro instanční objekt.

3. [Vytvořte uživatele databáze s omezením](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) pro instanční objekt. Připojte se k databázi z nebo na kterou chcete kopírovat data pomocí nástrojů, jako je SQL Server Management Studio, s identitou Azure AD, která má alespoň změnu oprávnění uživatele. Spusťte následující příkaz T-SQL:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. Udělte instančnímu objektu potřebná oprávnění, jako byste to udělali normálně pro uživatele SQL nebo jiné. Spusťte následující kód. Další možnosti najdete v [tomto dokumentu](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql).

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your application name];
    ```

5. Nakonfigurujte propojenou službu Azure SQL Database v Azure Data Factory.

#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Příklad propojené služby, který používá ověřování instančního objektu

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;Connection Timeout=30",
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

Datová továrna může být přidružená ke [spravované identitě pro prostředky Azure](data-factory-service-identity.md) , které představují konkrétní objekt pro vytváření dat. Tuto spravovanou identitu můžete použít pro ověřování Azure SQL Database. Určená továrna má přístup k datům a jejich zkopírování z databáze nebo do databáze pomocí této identity.

Pokud chcete použít spravované ověřování identity, postupujte podle těchto kroků.

1. Pokud jste to ještě neudělali, [zřídí správce Azure Active Directory](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database) serveru na Azure Portal. Správce Azure AD může být uživatel Azure AD nebo skupina Azure AD. Pokud skupině udělíte roli správce spravovanou identitou, přeskočte kroky 3 a 4. Správce má úplný přístup k databázi.

2. [Vytvořte uživatele databáze s omezením](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) pro Azure Data Factory spravovanou identitu. Připojte se k databázi z nebo na kterou chcete kopírovat data pomocí nástrojů, jako je SQL Server Management Studio, s identitou Azure AD, která má alespoň změnu oprávnění uživatele. Spusťte následující příkaz T-SQL:
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. Udělte Data Factory spravovaná identita potřebná k tomu, aby se běžně daly dělat pro uživatele SQL a jiné. Spusťte následující kód. Další možnosti najdete v [tomto dokumentu](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql).

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your Data Factory name];
    ```

4. Nakonfigurujte propojenou službu Azure SQL Database v Azure Data Factory.

**Příklad**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam sekcí a vlastností dostupných pro definování datových sad najdete v tématu [datové sady](./concepts-datasets-linked-services.md).

Pro Azure SQL Database datovou sadu jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost **Type** datové sady musí být nastavená na **AzureSqlTable** . | Ano |
| schema | Název schématu. |Ne pro zdroj, Ano pro jímku  |
| stolu | Název tabulky/zobrazení |Ne pro zdroj, Ano pro jímku  |
| tableName | Název tabulky nebo zobrazení se schématem. Tato vlastnost je podporována z důvodu zpětné kompatibility. Pro nové úlohy použijte `schema` a `table` . | Ne pro zdroj, Ano pro jímku |

### <a name="dataset-properties-example"></a>Příklad vlastností datové sady

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

Úplný seznam oddílů a vlastností dostupných pro definování aktivit najdete v tématu [kanály](concepts-pipelines-activities.md). V této části najdete seznam vlastností podporovaných zdrojem a jímkou Azure SQL Database.

### <a name="azure-sql-database-as-the-source"></a>Azure SQL Database jako zdroj

>[!TIP]
>Pokud chcete data z Azure SQL Database efektivně načíst pomocí dělení dat, přečtěte si další informace z [paralelní kopie z databáze SQL](#parallel-copy-from-sql-database).

Chcete-li kopírovat data z Azure SQL Database, jsou v části **zdroje** aktivity kopírování podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost **Type** zdroje aktivity kopírování musí být nastavená na **AzureSqlSource** . Typ "SqlSource" je stále podporován z důvodu zpětné kompatibility. | Ano |
| sqlReaderQuery | Tato vlastnost používá vlastní dotaz SQL ke čtení dat. Příklad: `select * from MyTable`. | Ne |
| sqlReaderStoredProcedureName | Název uložené procedury, která čte data ze zdrojové tabulky. Poslední příkaz SQL musí být příkaz SELECT v uložené proceduře. | Ne |
| storedProcedureParameters | Parametry pro uloženou proceduru.<br/>Povolené hodnoty jsou páry název-hodnota. Názvy a velikost písmen parametrů se musí shodovat s názvy a písmeny parametrů uložené procedury. | Ne |
| isolationLevel | Určuje chování při zamykání transakcí pro zdroj SQL. Povolené hodnoty jsou: **ReadCommitted** , **READUNCOMMITTED** , **RepeatableRead** , **serializovatelný** , **Snapshot** . Pokud není zadaný, použije se výchozí úroveň izolace databáze. Další podrobnosti najdete v [tomto dokumentu](/dotnet/api/system.data.isolationlevel) . | Ne |
| partitionOptions | Určuje možnosti dělení dat, které se používají k načtení dat z Azure SQL Database. <br>Povolené hodnoty jsou: **none** (default), **PhysicalPartitionsOfTable** a **DynamicRange** .<br>Pokud je možnost oddílu povolena (tj. ne `None` ), stupeň paralelismu na souběžně načtená data z Azure SQL Database řídí [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) Nastavení aktivity kopírování. | Ne |
| partitionSettings | Určete skupinu nastavení pro dělení dat. <br>Použijte, pokud možnost partition není `None` . | Ne |
| **_Pod `partitionSettings` :_* _ | | |
| partitionColumnName | Zadejte název zdrojového sloupce _ *v typu Integer nebo Date/DateTime* * ( `int` , `smallint` , `bigint` , `date` , `smalldatetime` , `datetime` , `datetime2` nebo `datetimeoffset` ), který bude použit pro dělení rozsahu pro paralelní kopírování. Pokud není zadaný, index nebo primární klíč tabulky se automaticky zjistí a použije se jako sloupec partition.<br>Použijte, pokud je parametr partition `DynamicRange` . Použijete-li dotaz k načtení zdrojových dat, zapojte  `?AdfDynamicRangePartitionCondition ` v klauzuli WHERE. Příklad naleznete v části [paralelní kopírování z databáze SQL](#parallel-copy-from-sql-database) . | Ne |
| partitionUpperBound | Maximální hodnota sloupce oddílu pro rozdělení rozsahu oddílu Tato hodnota se používá k určení rozteči oddílu, nikoli pro filtrování řádků v tabulce. Všechny řádky v tabulce nebo výsledku dotazu budou rozděleny na oddíly a zkopírovány. Pokud není zadaný, aktivita kopírování automaticky detekuje hodnotu.  <br>Použijte, pokud je parametr partition `DynamicRange` . Příklad naleznete v části [paralelní kopírování z databáze SQL](#parallel-copy-from-sql-database) . | Ne |
| partitionLowerBound | Minimální hodnota sloupce oddílu pro rozdělení rozsahu oddílů. Tato hodnota se používá k určení rozteči oddílu, nikoli pro filtrování řádků v tabulce. Všechny řádky v tabulce nebo výsledku dotazu budou rozděleny na oddíly a zkopírovány. Pokud není zadaný, aktivita kopírování automaticky detekuje hodnotu.<br>Použijte, pokud je parametr partition `DynamicRange` . Příklad naleznete v části [paralelní kopírování z databáze SQL](#parallel-copy-from-sql-database) . | Ne |

**Ukazuje na poznámku:**

- Pokud je pro **AzureSqlSource** zadaný **sqlReaderQuery** , aktivita kopírování spustí tento dotaz na zdroj Azure SQL Database, aby se data získala. Uloženou proceduru lze také určit zadáním **sqlReaderStoredProcedureName** a **storedProcedureParameters** , pokud uložená procedura přijímá parametry.
- Pokud nezadáte buď **sqlReaderQuery** nebo **sqlReaderStoredProcedureName** , budou použity sloupce definované v oddílu Structure pro datovou sadu JSON pro vytvoření dotazu. Dotaz se `select column1, column2 from mytable` spouští proti Azure SQL Database. Pokud definice datové sady nemá "strukturu", všechny sloupce jsou vybrány z tabulky.

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
> Přečtěte si další informace o podporovaných chováních, konfiguracích a osvědčených postupech pro zápis z [osvědčeného postupu pro načítání dat do Azure SQL Database](#best-practice-for-loading-data-into-azure-sql-database).

Chcete-li kopírovat data do Azure SQL Database, v části **jímka** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost **Type** jímky aktivity kopírování musí být nastavená na **AzureSqlSink** . Typ "SqlSink" je stále podporován z důvodu zpětné kompatibility. | Ano |
| preCopyScript | Zadejte dotaz SQL pro aktivitu kopírování, která se má spustit před zápisem dat do Azure SQL Database. Vyvolá se jenom jednou pro každé spuštění kopírování. Tato vlastnost slouží k vyčištění předem načtených dat. | Ne |
| tableOption | Určuje, jestli se má [automaticky vytvořit tabulka jímky](copy-activity-overview.md#auto-create-sink-tables) , pokud na základě schématu zdroje neexistuje. <br>Vytvoření automatické tabulky není podporováno, je-li jímka určena pro uloženou proceduru. <br>Povolené hodnoty jsou: `none` (výchozí), `autoCreate` . | Ne |
| sqlWriterStoredProcedureName | Název uložené procedury definující, jak se mají zdrojová data použít v cílové tabulce. <br/>Tato uložená procedura je *vyvolána pro každou dávku* . Pro operace, které se spouštějí jenom jednou a které nemají nic dělat se zdrojovými daty, například odstranit nebo zkrátit, použijte `preCopyScript` vlastnost.<br>Viz příklad [vyvolání uložené procedury z jímky SQL](#invoke-a-stored-procedure-from-a-sql-sink). | Ne |
| storedProcedureTableTypeParameterName |Název parametru pro typ tabulky určený v uložené proceduře.  |Ne |
| sqlWriterTableType |Název typu tabulky, který se má použít v uložené proceduře Aktivita kopírování zpřístupňuje data, která jsou k dispozici v dočasné tabulce s tímto typem tabulky. Uložený kód procedury pak může sloučit data, která jsou kopírována se stávajícími daty. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru.<br/>Povolené hodnoty jsou páry název-hodnota. Názvy a malá písmena parametrů se musí shodovat s názvy a písmeny parametrů uložené procedury. | Ne |
| writeBatchSize | Počet řádků, které mají být vloženy do tabulky SQL *na dávku* .<br/> Povolená hodnota je **Integer** (počet řádků). Ve výchozím nastavení Azure Data Factory dynamicky určí vhodnou velikost dávky na základě velikosti řádku. | Ne |
| writeBatchTimeout | Doba čekání na dokončení operace dávkového vložení před vypršením časového limitu.<br/> Povolená hodnota je **TimeSpan** . Příkladem je "00:30:00" (30 minut). | Ne |
| disableMetricsCollection | Data Factory shromažďuje metriky, jako je například Azure SQL Database DTU, pro optimalizaci výkonu a doporučení pro kopírování. Pokud se s tímto chováním obáváte, určete, jestli `true` ho chcete vypnout. | Ne (výchozí nastavení je `false` ) |

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
                "type": "AzureSqlSink",
                "tableOption": "autoCreate",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Příklad 2: vyvolání uložené procedury během kopírování**

Další informace o [vyvolání uložené procedury z jímky SQL](#invoke-a-stored-procedure-from-a-sql-sink).

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

## <a name="parallel-copy-from-sql-database"></a>Paralelní kopírování z databáze SQL

Konektor Azure SQL Database v aktivitě kopírování poskytuje integrované datové oddíly pro kopírování dat paralelně. Možnosti dělení dat můžete najít na kartě **zdroj** aktivity kopírování.

![Snímek obrazovky s možnostmi oddílů](./media/connector-sql-server/connector-sql-partition-options.png)

Povolíte-li kopírování rozdělené na oddíly, aktivita kopírování spustí paralelní dotazy na zdroj Azure SQL Database a načte data podle oddílů. Paralelní míra je řízena [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) nastavením aktivity kopírování. Pokud jste například nastavili `parallelCopies` na čtyři, Data Factory souběžně generuje a spustí čtyři dotazy na základě zadané možnosti oddílu a nastavení a každý dotaz načte část dat z vašeho Azure SQL Database.

Navrhnete, abyste umožnili paralelní kopírování s vytvářením oddílů dat zvlášť při načítání velkého množství dat z Azure SQL Database. Následují Doporučené konfigurace pro různé scénáře. Při kopírování dat do úložiště dat založeného na souborech se doporučuje zapisovat do složky jako více souborů (zadat jenom název složky). v takovém případě je výkon lepší než zápis do jediného souboru.

| Scénář                                                     | Navrhovaná nastavení                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Úplné načtení z velké tabulky s fyzickými oddíly.        | **Možnost oddílu** : fyzické oddíly tabulky. <br><br/>Během provádění Data Factory automaticky detekuje fyzické oddíly a kopíruje data podle oddílů. <br><br/>Pokud chcete zjistit, jestli má tabulka fyzický oddíl, nebo ne, můžete na [Tento dotaz](#sample-query-to-check-physical-partition)odkazovat. |
| Úplné načtení z velké tabulky bez fyzických oddílů při použití celého čísla nebo sloupce data a času pro dělení dat. | **Možnosti oddílu** : dynamický oddíl rozsahu.<br>**Sloupec oddílu** (volitelné): Zadejte sloupec, který se používá k dělení dat. Pokud není zadaný, použije se sloupec index nebo primární klíč.<br/>**Horní hranice oddílu** a **dolní mez oddílu** (volitelné): Určete, jestli chcete určit rozteč oddílu. Nejedná se o filtrování řádků v tabulce, přičemž všechny řádky v tabulce budou rozděleny na oddíly a zkopírovány. Pokud není zadaný, aktivita kopírování automaticky detekuje hodnoty.<br><br>Například pokud má sloupec oddílu "ID" rozsah hodnot od 1 do 100 a nastavíte dolní mez na hodnotu 20 a horní mez jako 80, s paralelní kopií as 4 Data Factory načte data podle 4 identifikátorů oddílů – ID v rozsahu <= 20, [21, 50], [51, 80] a >= 81, v uvedeném pořadí. |
| Načtěte velké množství dat pomocí vlastního dotazu, bez fyzických oddílů, zatímco se sloupcem typu Integer nebo Date/DateTime pro dělení dat. | **Možnosti oddílu** : dynamický oddíl rozsahu.<br>**Dotaz** : `SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>` .<br>**Partition – sloupec** : Určete sloupec, který se používá k dělení dat.<br>**Horní hranice oddílu** a **dolní mez oddílu** (volitelné): Určete, jestli chcete určit rozteč oddílu. Nejedná se o filtrování řádků v tabulce, všechny řádky ve výsledku dotazu budou rozděleny na oddíly a zkopírovány. Pokud není zadaný, aktivita kopírování automaticky detekuje hodnotu.<br><br>Během provádění Data Factory nahradí `?AdfRangePartitionColumnName` skutečným názvem sloupce a rozsahem hodnot pro každý oddíl a odešle do Azure SQL Database. <br>Například pokud má sloupec oddílu "ID" rozsah hodnot od 1 do 100 a nastavíte dolní mez na hodnotu 20 a horní mez jako 80, s paralelní kopií as 4 Data Factory načte data podle 4 identifikátorů oddílů – ID v rozsahu <= 20, [21, 50], [51, 80] a >= 81, v uvedeném pořadí. <br><br>Tady je více ukázkových dotazů pro různé scénáře:<br> 1. dotazování celé tabulky: <br>`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition`<br> 2. dotazování z tabulky s výběrem sloupce a dalšími filtry klauzule WHERE: <br>`SELECT <column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 3. dotazování s poddotazy: <br>`SELECT <column_list> FROM (<your_sub_query>) AS T WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 4. dotazování pomocí oddílu v poddotazu: <br>`SELECT <column_list> FROM (SELECT <your_sub_query_column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition) AS T`
|

Osvědčené postupy načítání dat s možností oddílu:

1. Chcete-li se vyhnout zkosení dat, vyberte sloupec s výrazným označením jako sloupec oddílu (například primární klíč nebo jedinečný klíč). 
2. Pokud tabulka obsahuje vestavěný oddíl, použijte možnost oddíl "fyzické oddíly tabulky", abyste získali lepší výkon.    
3. Pokud ke kopírování dat použijete Azure Integration Runtime, můžete nastavit větší "[jednotky integrace dat (diú)](copy-activity-performance-features.md#data-integration-units)" (>4), abyste využili více výpočetních prostředků. Projděte si příslušné scénáře.
4. "[Stupeň kopírování paralelismus](copy-activity-performance-features.md#parallel-copy)" řídí čísla oddílů a nastavení tohoto počtu je příliš velké, neuškodí výkon, doporučuje se nastavit toto číslo jako (diú nebo počet hostitelských uzlů IR) * (2 až 4).

**Příklad: úplné načtení z velké tabulky s fyzickými oddíly**

```json
"source": {
    "type": "AzureSqlSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Příklad: dotaz s dynamickým oddílem rozsahu**

```json
"source": {
    "type": "AzureSqlSource",
    "query": "SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column (optional) to decide the partition stride, not as data filter>",
        "partitionLowerBound": "<lower_value_of_partition_column (optional) to decide the partition stride, not as data filter>"
    }
}
```

### <a name="sample-query-to-check-physical-partition"></a>Ukázkový dotaz pro kontrolu fyzického oddílu

```sql
SELECT DISTINCT s.name AS SchemaName, t.name AS TableName, pf.name AS PartitionFunctionName, c.name AS ColumnName, iif(pf.name is null, 'no', 'yes') AS HasPartition
FROM sys.tables AS t
LEFT JOIN sys.objects AS o ON t.object_id = o.object_id
LEFT JOIN sys.schemas AS s ON o.schema_id = s.schema_id
LEFT JOIN sys.indexes AS i ON t.object_id = i.object_id 
LEFT JOIN sys.index_columns AS ic ON ic.partition_ordinal > 0 AND ic.index_id = i.index_id AND ic.object_id = t.object_id 
LEFT JOIN sys.columns AS c ON c.object_id = ic.object_id AND c.column_id = ic.column_id 
LEFT JOIN sys.partition_schemes ps ON i.data_space_id = ps.data_space_id 
LEFT JOIN sys.partition_functions pf ON pf.function_id = ps.function_id 
WHERE s.name='[your schema]' AND t.name = '[your table name]'
```

Pokud má tabulka fyzický oddíl, zobrazí se "HasPartition" jako "Ano" podobně jako v následujícím.

![Výsledek dotazu SQL](./media/connector-azure-sql-database/sql-query-result.png)

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>Osvědčené postupy načítání dat do Azure SQL Database

Při kopírování dat do Azure SQL Database může být nutné vyžadovat jiné chování při zápisu:

- [Připojit](#append-data): zdrojová data obsahují pouze nové záznamy.
- [Upsert](#upsert-data): moje zdrojová data obsahují vložení i aktualizace.
- [Přepsat](#overwrite-the-entire-table): Chci pokaždé, když chcete znovu načíst celou tabulku dimenzí.
- [Zápis pomocí vlastní logiky](#write-data-with-custom-logic): Potřebuji dodatečné zpracování před konečným vložením do cílové tabulky.

V příslušných částech najdete informace o tom, jak nakonfigurovat v Azure Data Factory a osvědčených postupech.

### <a name="append-data"></a>Připojit data

Připojení dat je výchozím chováním tohoto konektoru Azure SQL Database jímky. Azure Data Factory hromadné vložení do tabulky efektivně. Zdroj a jímku můžete v aktivitě kopírování nakonfigurovat odpovídajícím způsobem.

### <a name="upsert-data"></a>Upsert dat

**Možnost 1:** Pokud máte ke kopírování velké množství dat, můžete hromadně načíst všechny záznamy do pracovní tabulky pomocí aktivity kopírování a pak spustit aktivitu uložené procedury a použít příkaz [Sloučit](/sql/t-sql/statements/merge-transact-sql) nebo vložit nebo aktualizovat v jednom snímku. 

Aktivita kopírování aktuálně neposkytuje nativní podporu načítání dat do dočasné tabulky databáze. Existuje pokročilý způsob, jak ho nastavit s kombinací více aktivit, najdete v tématu věnovaném [optimalizaci Azure SQL Database hromadně Upsertch scénářů](https://github.com/scoriani/azuresqlbulkupsert). Níže ukazuje ukázku použití trvalé tabulky jako přípravy.

Jako příklad můžete v Azure Data Factory vytvořit kanál s **aktivitou kopírování** zřetězenou s **aktivitou uložené procedury** . Předchozí kopie dat ze zdrojového úložiště do Azure SQL Database pracovní tabulky, například **UpsertStagingTable** , jako název tabulky v datové sadě. Pak druhá procedura vyvolá uloženou proceduru ke sloučení zdrojových dat z pracovní tabulky do cílové tabulky a vyčištění pracovní tabulky.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

V databázi definujte uloženou proceduru pomocí logiky sloučení, podobně jako v následujícím příkladu, který ukazuje z předchozí aktivity uložené procedury. Předpokládejme, že cílem je **marketingová** tabulka se třemi sloupci: **ProfileID** , **State** a **Category** . Proveďte Upsert na základě sloupce **ProfileID** .

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
   MERGE TargetTable AS target
   USING UpsertStagingTable AS source
   ON (target.[ProfileID] = source.[ProfileID])
   WHEN MATCHED THEN
      UPDATE SET State = source.State
    WHEN NOT matched THEN
       INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    TRUNCATE TABLE UpsertStagingTable
END
```

**Možnost 2:** Můžete zvolit, aby se [v rámci aktivity kopírování vyvolala uložená procedura](#invoke-a-stored-procedure-from-a-sql-sink). Tento přístup spustí každou dávku (podle `writeBatchSize` Vlastnosti) ve zdrojové tabulce namísto použití možnosti hromadné vložení jako výchozího přístupu v aktivitě kopírování.

**Možnost 3:** Můžete použít [mapování toku dat](#sink-transformation) , který nabízí integrované metody Insert/Upsert/Update.

### <a name="overwrite-the-entire-table"></a>Přepsat celou tabulku

V jímky aktivity kopírování můžete nakonfigurovat vlastnost **preCopyScript** . V takovém případě se pro každou aktivitu kopírování, která běží, Azure Data Factory spustí skript jako první. Potom spustí kopii pro vložení dat. Chcete-li například přepsat celou tabulku nejnovějšími daty, zadejte skript, který nejprve odstraní všechny záznamy před hromadnou zátěží nových dat ze zdroje.

### <a name="write-data-with-custom-logic"></a>Zápis dat pomocí vlastní logiky

Postup pro zápis dat pomocí vlastní logiky je podobný těm, které jsou popsané v části [Upsert data](#upsert-data) . Pokud potřebujete uplatnit dodatečné zpracování před konečným vložením zdrojových dat do cílové tabulky, můžete načíst do pracovní tabulky, vyvolat aktivitu uložené procedury nebo vyvolat uloženou proceduru v jímky aktivity kopírování pro uplatnění dat, nebo použít tok mapování dat.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Vyvolat uloženou proceduru z jímky SQL

Když kopírujete data do Azure SQL Database, můžete také nakonfigurovat a vyvolat uloženou proceduru zadanou uživatelem s dalšími parametry na každé dávce zdrojové tabulky. Funkce uložené procedury využívá [parametry s hodnotou tabulky](/dotnet/framework/data/adonet/sql/table-valued-parameters).

Uloženou proceduru můžete použít, pokud předdefinované mechanismy kopírování neslouží k tomuto účelu. Příkladem je, že chcete použít dodatečné zpracování před konečným vložením zdrojových dat do cílové tabulky. Některé další příklady zpracování jsou, když chcete sloučit sloupce, vyhledat další hodnoty a vložit je do více než jedné tabulky.

Následující příklad ukazuje, jak použít uloženou proceduru k provedení Upsert do tabulky v Azure SQL Database. Předpokládejme, že vstupní data a **marketingová** tabulka jímky mají tři sloupce: **ProfileID** , **State** a **Category** . Proveďte Upsert na základě sloupce **ProfileID** a použijte ho jenom pro konkrétní kategorii s názvem "produkt".

1. V databázi Definujte typ tabulky se stejným názvem jako **sqlWriterTableType** . Schéma typu tabulky je stejné jako schéma vrácené vašimi vstupními daty.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. V databázi definujte uloženou proceduru se stejným názvem jako **sqlWriterStoredProcedureName** . Zpracovává vstupní data ze zadaného zdroje a sloučí je do výstupní tabulky. Název parametru typu tabulky v uložené proceduře je stejný jako **TableName** definovaný v datové sadě.

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

3. V Azure Data Factory v aktivitě kopírování definujte oddíl **jímky SQL** následujícím způsobem:

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

Při transformaci dat v toku mapování dat můžete číst a zapisovat do tabulek z Azure SQL Database. Další informace najdete v tématu transformace [zdroje](data-flow-source.md) a [transformace jímky](data-flow-sink.md) v tématu mapování toků dat.

### <a name="source-transformation"></a>Transformace zdroje

Nastavení specifická pro Azure SQL Database jsou k dispozici na kartě **Možnosti zdroje** ve zdrojové transformaci.

**Vstup:** Vyberte, zda se má zdroj nasměrovat v tabulce (odpovídající hodnotě ```Select * from <table-name>``` ), nebo zadejte vlastní dotaz SQL.

**Dotaz** : Pokud ve vstupním poli vyberete možnost dotaz, zadejte pro zdroj dotaz SQL. Toto nastavení potlačí všechny tabulky, které jste vybrali v datové sadě. Klauzule **ORDER by** nejsou tady podporované, ale můžete nastavit úplný příkaz SELECT FROM. Můžete také použít uživatelsky definované funkce tabulky. **SELECT * FROM udfGetData ()** je UDF v SQL, který vrací tabulku. Tento dotaz vytvoří zdrojovou tabulku, kterou můžete použít v toku dat. Použití dotazů je také skvělým způsobem, jak omezit řádky pro testování nebo pro vyhledávání.

- Příklad SQL: ```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Velikost dávky** : zadejte velikost dávky pro velké objemy dat v čtení.

**Úroveň izolace** : ve výchozím nastavení pro zdroje SQL v toku dat mapování je čtení nepotvrzené. Úroveň izolace můžete změnit tady na jednu z těchto hodnot:

- Čtení potvrzeno
- Čtení nepotvrzených
- Opakované čtení
- Serializovatelný
- Žádné (ignorovat úroveň izolace)

![Úroveň izolace](media/data-flow/isolationlevel.png "Úroveň izolace")

### <a name="sink-transformation"></a>Transformace jímky

Nastavení specifická pro Azure SQL Database jsou k dispozici na kartě **Nastavení** transformace jímky.

**Metoda aktualizace:** Určuje, jaké operace jsou pro cíl databáze povolené. Ve výchozím nastavení je povolen pouze vkládání. Chcete-li aktualizovat, Upsert nebo odstranit řádky, je pro označení řádků pro tyto akce nutná transformace ALTER Row. V případě aktualizací upsertuje a DELETE musí být klíčový sloupec nebo sloupce nastaveny k určení, který řádek má být změněn.

![Klíčové sloupce](media/data-flow/keycolumn.png "Klíčové sloupce")

Název sloupce, který vyberete jako klíč, bude v rámci této aktualizace použit jako součást následné aktualizace, Upsert a DELETE. Proto je nutné vybrat sloupec, který existuje v mapování jímky. Pokud chcete do tohoto klíčového sloupce nezazapisovat hodnotu, klikněte na přeskočit zápis klíčových sloupců.

Pro aktualizaci cílové Azure SQL Database tabulky můžete použít klíčové sloupce. Pokud máte pro složený klíč více sloupců, klikněte na vlastní výraz a budete moct přidat dynamický obsah pomocí jazyka výrazů toku dat ADF, který může zahrnovat pole řetězců s názvy sloupců pro složený klíč.

**Akce tabulky:** Určuje, zda mají být před zápisem znovu vytvořeny nebo odebrány všechny řádky z cílové tabulky.

- Žádné: v tabulce se neprovede žádná akce.
- Znovu vytvořit: tabulka se vynechá a znovu vytvoří. Požadováno při dynamickém vytváření nové tabulky.
- Zkrátit: všechny řádky z cílové tabulky se odeberou.

**Velikost dávky** : Určuje, kolik řádků je v jednotlivých intervalech zapisováno. Větší velikosti dávek zlepšují kompresi a optimalizaci paměti, ale při ukládání dat do mezipaměti riskuje výjimky z paměti.

**Skripty před a po SQL** : zadejte víceřádkové skripty SQL, které se spustí před (před zpracováním) a po (po zpracování) se zapisují do databáze jímky.

![skripty pro zpracování před a po SQL](media/data-flow/prepost1.png "Skripty pro zpracování SQL")

## <a name="data-type-mapping-for-azure-sql-database"></a>Mapování datových typů pro Azure SQL Database

Když se data zkopírují z nebo do Azure SQL Database, použijí se následující mapování Azure SQL Database datových typů, abyste mohli Azure Data Factory dočasné datové typy. Informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ k jímky, najdete v tématu [mapování schémat a datových typů](copy-activity-schema-and-type-mapping.md).

| Azure SQL Database datový typ | Azure Data Factory pomocný datový typ |
|:--- |:--- |
| bigint |Int64 |
| binární |Byte [] |
| bit |Logická hodnota |
| char |Řetězec, znak [] |
| date |DateTime |
| Datum a čas |DateTime |
| datetime2 |DateTime |
| DateTimeOffset |DateTimeOffset |
| Decimal |Decimal |
| Atribut FILESTREAM (varbinary (max)) |Byte [] |
| Float |dvojité |
| image |Byte [] |
| int |Int32 |
| papír |Decimal |
| nchar |Řetězec, znak [] |
| ntext |Řetězec, znak [] |
| numerické |Decimal |
| nvarchar |Řetězec, znak [] |
| real |Jednoduché |
| rowversion |Byte [] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Objekt |
| text |Řetězec, znak [] |
| time |TimeSpan |
| časové razítko |Byte [] |
| tinyint |Byte |
| uniqueidentifier |Identifikátor GUID |
| varbinary |Byte [] |
| varchar |Řetězec, znak [] |
| xml |Řetězec |

>[!NOTE]
> Pro datové typy, které jsou mapovány na mezihodnotový průběžný typ, aktuálně aktivita kopírování podporuje přesnost až na 28. Pokud máte data s přesností větší než 28, zvažte převod na řetězec v dotazu SQL.

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Vlastnosti aktivity GetMetadata

Pokud se chcete dozvědět víc o vlastnostech, podívejte se na [aktivitu GetMetadata](control-flow-get-metadata-activity.md) .

## <a name="using-always-encrypted"></a>Použití Always Encrypted

Když kopírujete data z/do Azure SQL Database pomocí [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine), použijte [obecný konektor ODBC](connector-odbc.md) a SQL Server ovladač ODBC prostřednictvím místně hostovaného Integration runtime. Tento konektor Azure SQL Database nepodporuje nyní Always Encrypted. 

A konkrétně:

1. Nastavte Integration Runtime v místním prostředí, pokud ho ještě nemáte. Podrobnosti najdete v článku [Integration runtime](create-self-hosted-integration-runtime.md) v místním prostředí.

2. Stáhněte si z [tohoto místa](/sql/connect/odbc/download-odbc-driver-for-sql-server)ovladač ODBC 64 pro SQL Server a nainstalujte ho do Integration runtime počítače. Přečtěte si další informace o tom, jak tento ovladač funguje, [pomocí Always Encrypted s ovladačem ODBC pro SQL Server](/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver#using-the-azure-key-vault-provider).

3. Vytvoření propojené služby s typem ODBC pro připojení k vaší databázi SQL najdete v následujících ukázkách:

    - Použití **ověřování SQL** : zadejte připojovací řetězec ODBC, jak je uvedeno níže, a vyberte **základní** ověřování a nastavte uživatelské jméno a heslo.

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>
        ```

    - Použití **spravovaného ověřování Identity Data Factory** : 

        1. Při vytváření uživatele databáze pro spravovanou identitu použijte stejné [předpoklady](#managed-identity) a udělte v databázi správnou roli.
        2. V části propojená služba zadejte připojovací řetězec ODBC následujícím způsobem a pak vyberte **anonymní** ověřování, které označuje samotný připojovací řetězec `Authentication=ActiveDirectoryMsi` .

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>; Authentication=ActiveDirectoryMsi;
        ```

4. Odpovídajícím způsobem Vytvořte datovou sadu a aktivitu kopírování s použitím typu ODBC. Další informace najdete v článku [konektor ODBC](connector-odbc.md) .

## <a name="next-steps"></a>Další kroky

Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Azure Data Factory najdete v tématu [podporované úložiště dat a formáty](copy-activity-overview.md#supported-data-stores-and-formats).