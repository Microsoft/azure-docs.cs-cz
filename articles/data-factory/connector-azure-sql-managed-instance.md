---
title: Kopírování dat z a do spravované instance SQL Azure
description: Přečtěte si, jak přesunout data do spravované instance Azure SQL a z ní pomocí Azure Data Factory.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 09/21/2020
ms.openlocfilehash: 3a9216c665cfdcdaf07980ace0399fd927885262
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91332113"
---
# <a name="copy-data-to-and-from-azure-sql-managed-instance-by-using-azure-data-factory"></a>Kopírování dat do a ze spravované instance SQL Azure pomocí Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z a do spravované instance Azure SQL. Vytvoří se v článku [Přehled aktivity kopírování](copy-activity-overview.md) , který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor SQL Managed instance je podporovaný pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)

Data z spravované instance SQL můžete kopírovat do libovolného podporovaného úložiště dat jímky. Data můžete také kopírovat z libovolného podporovaného zdrojového úložiště dat do spravované instance SQL. Seznam úložišť dat, která jsou v rámci aktivity kopírování podporovaná jako zdroje a jímky, najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

Konkrétně tato spojnice spravované instance SQL podporuje:

- Kopírování dat pomocí ověřování SQL a Azure Active Directory (Azure AD) ověřování tokenu aplikace pomocí instančního objektu nebo spravovaných identit pro prostředky Azure.
- Jako zdroj načítání dat pomocí dotazu SQL nebo uložené procedury. Můžete si také vybrat paralelní kopírování ze zdroje SQL MI, podrobnosti najdete v části [paralelní kopírování z SQL mi](#parallel-copy-from-sql-mi) .
- Pokud v závislosti na zdrojovém schématu neexistuje, vytvoří se jako jímka automaticky vytváření cílové tabulky. připojení dat k tabulce nebo vyvolání uložené procedury s vlastní logikou během kopírování.

>[!NOTE]
> Tento konektor teď nepodporuje [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) spravované instance SQL. Pokud chcete tento problém obejít, můžete použít [obecný konektor ODBC](connector-odbc.md) a SQL Server ovladač ODBC prostřednictvím prostředí Integration runtime v místním prostředí. Další informace najdete v části [použití Always Encrypted](#using-always-encrypted) . 

## <a name="prerequisites"></a>Požadavky

Pro přístup ke [veřejnému koncovému bodu](../azure-sql/managed-instance/public-endpoint-overview.md)spravované instance SQL můžete použít Azure Data Factory spravované prostředí Azure Integration runtime. Ujistěte se, že jste povolili veřejný koncový bod a zároveň povolili provoz veřejného koncového bodu ve skupině zabezpečení sítě, aby se Azure Data Factory mohl připojit k vaší databázi. Další informace najdete v [těchto pokynech](../azure-sql/managed-instance/public-endpoint-configure.md).

Pro přístup k privátnímu koncovému bodu spravované instance SQL nastavte místní [prostředí Integration runtime](create-self-hosted-integration-runtime.md) , které má přístup k databázi. Pokud zřídíte místní prostředí Integration runtime ve stejné virtuální síti jako vaše spravovaná instance, ujistěte se, že je váš počítač Integration runtime v jiné podsíti než vaše spravovaná instance. Pokud zřídíte místní prostředí Integration runtime v jiné virtuální síti než vaše spravovaná instance, můžete k připojení k virtuální síti použít buď partnerský vztah virtuální sítě, nebo virtuální síť. Další informace najdete v tématu [připojení aplikace k spravované instanci SQL](../azure-sql/managed-instance/connect-application-instance.md).

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které se používají k definování Azure Data Factory entit specifických pro konektor SQL Managed instance.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu SQL Managed instance jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type musí být nastavená na **AzureSqlMI**. | Yes |
| připojovací řetězec |Tato vlastnost určuje informace **připojovacího řetězce** potřebné pro připojení k spravované instanci SQL pomocí ověřování SQL. Další informace najdete v následujících příkladech. <br/>Výchozí port je 1433. Pokud používáte spravovanou instanci SQL s veřejným koncovým bodem, explicitně zadejte port 3342.<br> Heslo můžete také přidat do Azure Key Vault. Pokud se jedná o ověřování SQL, vyžádejte si z `password` připojovacího řetězce konfiguraci. Další informace najdete v příkladech JSON, které následují po tabulce, a [ukládají přihlašovací údaje v Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| servicePrincipalId | Zadejte ID klienta aplikace. | Ano, pokud používáte ověřování Azure AD s instančním objektem |
| servicePrincipalKey | Zadejte klíč aplikace. Označte toto pole jako **SecureString** a bezpečně ho uložte do Azure Data Factory nebo [odkaz na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | Ano, pokud používáte ověřování Azure AD s instančním objektem |
| tenant | Zadejte informace o tenantovi, jako je název domény nebo ID tenanta, pod kterým se vaše aplikace nachází. Načtěte ho tak, že najedete myší v pravém horním rohu Azure Portal. | Ano, pokud používáte ověřování Azure AD s instančním objektem |
| azureCloudType | Pro ověřování instančního objektu zadejte typ cloudového prostředí Azure, ve kterém je vaše aplikace Azure AD zaregistrovaná. <br/> Povolené hodnoty jsou **AzurePublic**, **AzureChina**, **AzureUsGovernment**a **AzureGermany**. Ve výchozím nastavení se používá cloudové prostředí pro datovou továrnu. | No |
| connectVia | Tento [modul runtime integrace](concepts-integration-runtime.md) se používá pro připojení k úložišti dat. Pokud má vaše spravovaná instance veřejný koncový bod a umožňuje Azure Data Factory k němu přistupovat, můžete použít místní prostředí Integration runtime nebo prostředí Azure Integration runtime. Pokud tento parametr nezadáte, použije se výchozí prostředí Azure Integration runtime. |Yes |

Pro různé typy ověřování se podívejte na následující oddíly týkající se požadavků a ukázek JSON, v uvedeném pořadí:

- [Ověřování SQL](#sql-authentication)
- [Ověřování tokenu aplikací služby Azure AD: instanční objekt](#service-principal-authentication)
- [Ověřování tokenu aplikací Azure AD: spravované identity pro prostředky Azure](#managed-identity)

### <a name="sql-authentication"></a>Ověřování SQL

**Příklad 1: použití ověřování SQL**

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

**Příklad 2: použití ověřování SQL s heslem v Azure Key Vault**

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

Chcete-li použít ověřování pomocí tokenu aplikace služby Azure AD založené na instančním objektu, postupujte podle následujících kroků:

1. Postupujte podle kroků a [zřiďte správce Azure Active Directory pro vaši spravovanou instanci](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

2. [Vytvořte aplikaci Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) z Azure Portal. Poznamenejte si název aplikace a následující hodnoty, které definují propojenou službu:

    - ID aplikace
    - Klíč aplikace
    - ID tenanta

3. [Vytvoření přihlašovacích](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql) údajů pro Azure Data Factory spravovanou identitu. V SQL Server Management Studio (SSMS) se připojte ke svojí spravované instanci pomocí účtu SQL Server, který je **sysadmin**. V **Hlavní** databázi spusťte následující příkaz T-SQL:

    ```sql
    CREATE LOGIN [your application name] FROM EXTERNAL PROVIDER
    ```

4. [Vytvořte uživatele databáze s omezením](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) pro Azure Data Factory spravovanou identitu. Připojte se k databázi z nebo do které chcete kopírovat data, spusťte následující příkaz T-SQL: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER
    ```

5. Udělte Data Factory spravovaná identita potřebná k tomu, aby se běžně daly dělat pro uživatele SQL a jiné. Spusťte následující kód. Další možnosti najdete v [tomto dokumentu](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql).

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your application name]
    ```

6. Nakonfigurujte propojenou službu spravované instance SQL v Azure Data Factory.

**Příklad: použití ověřování instančního objektu**

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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Spravované identity pro ověřování prostředků Azure

Datová továrna může být přidružená ke [spravované identitě pro prostředky Azure](data-factory-service-identity.md) , které představují konkrétní objekt pro vytváření dat. Tuto spravovanou identitu můžete použít pro ověřování spravované instance SQL. Určená továrna má přístup k datům a jejich zkopírování z databáze nebo do databáze pomocí této identity.

Pokud chcete použít spravované ověřování identity, postupujte podle těchto kroků.

1. Postupujte podle kroků a [zřiďte správce Azure Active Directory pro vaši spravovanou instanci](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

2. [Vytvoření přihlašovacích](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql) údajů pro Azure Data Factory spravovanou identitu. V SQL Server Management Studio (SSMS) se připojte ke svojí spravované instanci pomocí účtu SQL Server, který je **sysadmin**. V **Hlavní** databázi spusťte následující příkaz T-SQL:

    ```sql
    CREATE LOGIN [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

3. [Vytvořte uživatele databáze s omezením](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) pro Azure Data Factory spravovanou identitu. Připojte se k databázi z nebo do které chcete kopírovat data, spusťte následující příkaz T-SQL: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

4. Udělte Data Factory spravovaná identita potřebná k tomu, aby se běžně daly dělat pro uživatele SQL a jiné. Spusťte následující kód. Další možnosti najdete v [tomto dokumentu](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql).

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your Data Factory name]
    ```

5. Nakonfigurujte propojenou službu spravované instance SQL v Azure Data Factory.

**Příklad: používá spravované ověřování identity.**

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro použití k definování datových sad, naleznete v článku datové sady. V této části najdete seznam vlastností podporovaných datovou sadou spravované instance SQL.

Chcete-li kopírovat data do a z spravované instance SQL, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type datové sady musí být nastavená na **AzureSqlMITable**. | Yes |
| schema | Název schématu. |Ne pro zdroj, Ano pro jímku  |
| stolu | Název tabulky/zobrazení |Ne pro zdroj, Ano pro jímku  |
| tableName | Název tabulky nebo zobrazení se schématem. Tato vlastnost je podporována z důvodu zpětné kompatibility. Pro nové úlohy použijte `schema` a `table` . | Ne pro zdroj, Ano pro jímku |

**Příklad**

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "AzureSqlMITable",
        "linkedServiceName": {
            "referenceName": "<SQL Managed Instance linked service name>",
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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro použití k definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností podporovaných zdrojem a jímkou spravované instance SQL.

### <a name="sql-managed-instance-as-a-source"></a>Spravovaná instance SQL jako zdroj

>[!TIP]
>Pokud chcete načíst data z SQL, efektivně pomocí dělení dat, přečtěte si další informace z [paralelní kopie z SQL mi](#parallel-copy-from-sql-mi).

Chcete-li kopírovat data z spravované instance SQL, jsou v části zdroje aktivity kopírování podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **SqlMISource**. | Yes |
| sqlReaderQuery |Tato vlastnost používá vlastní dotaz SQL ke čtení dat. Příklad: `select * from MyTable`. |No |
| sqlReaderStoredProcedureName |Tato vlastnost je název uložené procedury, která čte data ze zdrojové tabulky. Poslední příkaz SQL musí být příkaz SELECT v uložené proceduře. |No |
| storedProcedureParameters |Tyto parametry jsou pro uloženou proceduru.<br/>Povolené hodnoty jsou páry název-hodnota. Názvy a písmena parametrů se musí shodovat s názvy a písmeny parametrů uložené procedury. |No |
| isolationLevel | Určuje chování při zamykání transakcí pro zdroj SQL. Povolené hodnoty jsou: **ReadCommitted**, **READUNCOMMITTED**, **RepeatableRead**, **serializovatelný**, **Snapshot**. Pokud není zadaný, použije se výchozí úroveň izolace databáze. Další podrobnosti najdete v [tomto dokumentu](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) . | No |
| partitionOptions | Určuje možnosti dělení dat, které se používají k načtení dat z SQL MI. <br>Povolené hodnoty jsou: **none** (default), **PhysicalPartitionsOfTable**a **DynamicRange**.<br>Pokud je možnost oddílu povolena (tj. ne `None` ), stupeň paralelismu na souběžně načtená data z SQL mi je řízeno [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) nastavením aktivity kopírování. | No |
| partitionSettings | Určete skupinu nastavení pro dělení dat. <br>Použijte, pokud možnost partition není `None` . | No |
| ***V části `partitionSettings` :*** | | |
| partitionColumnName | Zadejte název zdrojového sloupce **v typu Integer nebo Date/DateTime** , který bude použit pro vytváření oddílů rozsahu pro paralelní kopírování. Pokud není zadaný, index nebo primární klíč tabulky se automaticky zjistí a použije se jako sloupec partition.<br>Použijte, pokud je parametr partition `DynamicRange` . Použijete-li dotaz k načtení zdrojových dat, zapojte  `?AdfDynamicRangePartitionCondition ` v klauzuli WHERE. Příklad naleznete v části [paralelní kopírování z databáze SQL](#parallel-copy-from-sql-mi) . | No |
| partitionUpperBound | Maximální hodnota sloupce oddílu pro rozdělení rozsahu oddílu Tato hodnota se používá k určení rozteči oddílu, nikoli pro filtrování řádků v tabulce. Všechny řádky v tabulce nebo výsledku dotazu budou rozděleny na oddíly a zkopírovány. Pokud není zadaný, aktivita kopírování automaticky detekuje hodnotu.  <br>Použijte, pokud je parametr partition `DynamicRange` . Příklad naleznete v části [paralelní kopírování z databáze SQL](#parallel-copy-from-sql-mi) . | No |
| partitionLowerBound | Minimální hodnota sloupce oddílu pro rozdělení rozsahu oddílů. Tato hodnota se používá k určení rozteči oddílu, nikoli pro filtrování řádků v tabulce. Všechny řádky v tabulce nebo výsledku dotazu budou rozděleny na oddíly a zkopírovány. Pokud není zadaný, aktivita kopírování automaticky detekuje hodnotu.<br>Použijte, pokud je parametr partition `DynamicRange` . Příklad naleznete v části [paralelní kopírování z databáze SQL](#parallel-copy-from-sql-mi) . | No |

**Je třeba počítat s následujícím:**

- Pokud je pro **SqlMISource**zadaný **sqlReaderQuery** , aktivita kopírování spustí tento dotaz proti zdroji spravované instance SQL a získá tak data. Uloženou proceduru lze také určit zadáním **sqlReaderStoredProcedureName** a **storedProcedureParameters** , pokud uložená procedura přijímá parametry.
- Pokud nezadáte buď vlastnost **sqlReaderQuery** nebo **sqlReaderStoredProcedureName** , použijí se k sestavení dotazu sloupce definované v oddílu Structure pro datovou sadu JSON. Dotaz se `select column1, column2 from mytable` spustí na spravované instanci SQL. Pokud definice datové sady nemá "strukturu", všechny sloupce jsou vybrány z tabulky.

**Příklad: použití dotazu SQL**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Managed Instance input dataset name>",
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

**Příklad: použití uložené procedury**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Managed Instance input dataset name>",
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

### <a name="sql-managed-instance-as-a-sink"></a>Spravovaná instance SQL jako jímka

> [!TIP]
> Přečtěte si další informace o podporovaných chováních, konfiguracích a osvědčených postupech pro zápis z [osvědčeného postupu pro načítání dat do spravované instance SQL](#best-practice-for-loading-data-into-sql-managed-instance).

Chcete-li kopírovat data do spravované instance SQL, v části jímka aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type jímky aktivity kopírování musí být nastavená na **SqlMISink**. | Yes |
| preCopyScript |Tato vlastnost určuje dotaz SQL pro aktivitu kopírování, která se má spustit před zápisem dat do spravované instance SQL. Vyvolá se jenom jednou pro každé spuštění kopírování. Tuto vlastnost můžete použít k vyčištění předem načtených dat. |No |
| tableOption | Určuje, jestli se má [automaticky vytvořit tabulka jímky](copy-activity-overview.md#auto-create-sink-tables) , pokud na základě schématu zdroje neexistuje. Vytvoření automatické tabulky není podporováno, je-li jímka určena pro uloženou proceduru. Povolené hodnoty jsou: `none` (výchozí), `autoCreate` . |No |
| sqlWriterStoredProcedureName | Název uložené procedury definující, jak se mají zdrojová data použít v cílové tabulce. <br/>Tato uložená procedura je *vyvolána pro každou dávku*. Pro operace, které se spouštějí jenom jednou a které nemají nic dělat se zdrojovými daty, například odstranit nebo zkrátit, použijte `preCopyScript` vlastnost.<br>Viz příklad [vyvolání uložené procedury z jímky SQL](#invoke-a-stored-procedure-from-a-sql-sink). | No |
| storedProcedureTableTypeParameterName |Název parametru pro typ tabulky určený v uložené proceduře.  |No |
| sqlWriterTableType |Název typu tabulky, který se má použít v uložené proceduře Aktivita kopírování zpřístupňuje data, která jsou k dispozici v dočasné tabulce s tímto typem tabulky. Uložený kód procedury pak může sloučit data, která jsou kopírována se stávajícími daty. |No |
| storedProcedureParameters |Parametry pro uloženou proceduru.<br/>Povolené hodnoty jsou páry název-hodnota. Názvy a malá písmena parametrů se musí shodovat s názvy a písmeny parametrů uložené procedury. | No |
| writeBatchSize |Počet řádků, které mají být vloženy do tabulky SQL *na dávku*.<br/>Povolené hodnoty jsou celá čísla pro počet řádků. Ve výchozím nastavení Azure Data Factory dynamicky určí vhodnou velikost dávky na základě velikosti řádku.  |No |
| writeBatchTimeout |Tato vlastnost určuje dobu čekání na dokončení operace dávkového vložení před vypršením časového limitu.<br/>Povolené hodnoty jsou pro časové rozpětí. Příkladem je "00:30:00", což je 30 minut. |No |

**Příklad 1: připojení dat**

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
                "referenceName": "<SQL Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlMISink",
                "tableOption": "autoCreate",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Příklad 2: vyvolání uložené procedury během kopírování**

Další informace o [vyvolání uložené procedury z jímky SQL mi](#invoke-a-stored-procedure-from-a-sql-sink).

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
                "referenceName": "<SQL Managed Instance output dataset name>",
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

## <a name="parallel-copy-from-sql-mi"></a>Paralelní kopírování z SQL MI

Konektor Azure SQL Managed instance v aktivitě kopírování poskytuje integrované datové oddíly pro kopírování dat paralelně. Možnosti dělení dat můžete najít na kartě **zdroj** aktivity kopírování.

![Snímek obrazovky s možnostmi oddílů](./media/connector-sql-server/connector-sql-partition-options.png)

Pokud povolíte kopírování rozdělené na oddíly, aktivita kopírování spustí paralelní dotazy na zdrojovém zdroji SQL MI, aby se načetly data podle oddílů. Paralelní míra je řízena [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) nastavením aktivity kopírování. Pokud jste například nastavili `parallelCopies` čtyři, Data Factory souběžně vygeneruje a spustí čtyři dotazy na základě zadané možnosti oddílu a nastavení a každý dotaz načte část dat ze svého SQL mi.

Navrhnete, abyste umožnili paralelní kopírování s vytvářením oddílů dat, zejména při načítání velkého množství dat ze svého SQL MI. Následují Doporučené konfigurace pro různé scénáře. Při kopírování dat do úložiště dat založeného na souborech se doporučuje zapisovat do složky jako více souborů (zadat jenom název složky). v takovém případě je výkon lepší než zápis do jediného souboru.

| Scénář                                                     | Navrhovaná nastavení                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Úplné načtení z velké tabulky s fyzickými oddíly.        | **Možnost oddílu**: fyzické oddíly tabulky. <br><br/>Během provádění Data Factory automaticky detekuje fyzické oddíly a kopíruje data podle oddílů. <br><br/>Pokud chcete zjistit, jestli má tabulka fyzický oddíl, nebo ne, můžete na [Tento dotaz](#sample-query-to-check-physical-partition)odkazovat. |
| Úplné načtení z velké tabulky bez fyzických oddílů při použití celého čísla nebo sloupce data a času pro dělení dat. | **Možnosti oddílu**: dynamický oddíl rozsahu.<br>**Sloupec oddílu** (volitelné): Zadejte sloupec, který se používá k dělení dat. Pokud není zadaný, použije se sloupec index nebo primární klíč.<br/>**Horní hranice oddílu** a **dolní mez oddílu** (volitelné): Určete, jestli chcete určit rozteč oddílu. Nejedná se o filtrování řádků v tabulce, přičemž všechny řádky v tabulce budou rozděleny na oddíly a zkopírovány. Pokud není zadaný, aktivita kopírování automaticky detekuje hodnoty.<br><br>Například pokud má sloupec oddílu "ID" rozsah hodnot od 1 do 100 a nastavíte dolní mez na hodnotu 20 a horní mez jako 80, s paralelní kopií as 4 Data Factory načte data podle 4 identifikátorů oddílů – ID v rozsahu <= 20, [21, 50], [51, 80] a >= 81, v uvedeném pořadí. |
| Načtěte velké množství dat pomocí vlastního dotazu, bez fyzických oddílů, zatímco se sloupcem typu Integer nebo Date/DateTime pro dělení dat. | **Možnosti oddílu**: dynamický oddíl rozsahu.<br>**Dotaz**: `SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>` .<br>**Partition – sloupec**: Určete sloupec, který se používá k dělení dat.<br>**Horní hranice oddílu** a **dolní mez oddílu** (volitelné): Určete, jestli chcete určit rozteč oddílu. Nejedná se o filtrování řádků v tabulce, všechny řádky ve výsledku dotazu budou rozděleny na oddíly a zkopírovány. Pokud není zadaný, aktivita kopírování automaticky detekuje hodnotu.<br><br>Během provádění Data Factory nahradí `?AdfRangePartitionColumnName` skutečným názvem sloupce a rozsahem hodnot pro každý oddíl a pošle do SQL mi. <br>Například pokud má sloupec oddílu "ID" rozsah hodnot od 1 do 100 a nastavíte dolní mez na hodnotu 20 a horní mez jako 80, s paralelní kopií as 4 Data Factory načte data podle 4 identifikátorů oddílů – ID v rozsahu <= 20, [21, 50], [51, 80] a >= 81, v uvedeném pořadí. <br><br>Tady je více ukázkových dotazů pro různé scénáře:<br> 1. dotazování celé tabulky: <br>`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition`<br> 2. dotazování z tabulky s výběrem sloupce a dalšími filtry klauzule WHERE: <br>`SELECT <column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 3. dotazování s poddotazy: <br>`SELECT <column_list> FROM (<your_sub_query>) AS T WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 4. dotazování pomocí oddílu v poddotazu: <br>`SELECT <column_list> FROM (SELECT <your_sub_query_column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition) AS T`
|

Osvědčené postupy načítání dat s možností oddílu:

1. Chcete-li se vyhnout zkosení dat, vyberte sloupec s výrazným označením jako sloupec oddílu (například primární klíč nebo jedinečný klíč). 
2. Pokud tabulka obsahuje vestavěný oddíl, použijte možnost oddíl "fyzické oddíly tabulky", abyste získali lepší výkon.  
3. Pokud ke kopírování dat použijete Azure Integration Runtime, můžete nastavit větší "[jednotky integrace dat (diú)](copy-activity-performance-features.md#data-integration-units)" (>4), abyste využili více výpočetních prostředků. Projděte si příslušné scénáře.
4. "[Stupeň kopírování paralelismus](copy-activity-performance-features.md#parallel-copy)" řídí čísla oddílů a nastavení tohoto počtu je příliš velké, neuškodí výkon, doporučuje se nastavit toto číslo jako (diú nebo počet hostitelských uzlů IR) * (2 až 4).

**Příklad: úplné načtení z velké tabulky s fyzickými oddíly**

```json
"source": {
    "type": "SqlMISource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Příklad: dotaz s dynamickým oddílem rozsahu**

```json
"source": {
    "type": "SqlMISource",
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

## <a name="best-practice-for-loading-data-into-sql-managed-instance"></a>Osvědčené postupy načítání dat do spravované instance SQL

Při kopírování dat do spravované instance SQL můžete vyžadovat jiné chování při zápisu:

- [Připojit](#append-data): zdrojová data obsahují pouze nové záznamy.
- [Upsert](#upsert-data): moje zdrojová data obsahují vložení i aktualizace.
- [Přepsat](#overwrite-the-entire-table): Chci pokaždé, když chcete znovu načíst celou tabulku dimenzí.
- [Zápis pomocí vlastní logiky](#write-data-with-custom-logic): Potřebuji dodatečné zpracování před konečným vložením do cílové tabulky. 

V příslušných částech najdete informace o tom, jak nakonfigurovat v Azure Data Factory a osvědčených postupech.

### <a name="append-data"></a>Připojit data

Připojení dat je výchozím chováním konektoru jímky spravované instance SQL. Azure Data Factory hromadné vložení do tabulky efektivně. Zdroj a jímku můžete v aktivitě kopírování nakonfigurovat odpovídajícím způsobem.

### <a name="upsert-data"></a>Upsert dat

**Možnost 1:** Pokud máte ke kopírování velké množství dat, můžete hromadně načíst všechny záznamy do pracovní tabulky pomocí aktivity kopírování a pak spustit aktivitu uložené procedury a použít příkaz [Sloučit](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql) nebo vložit nebo aktualizovat v jednom snímku. 

Aktivita kopírování aktuálně neposkytuje nativní podporu načítání dat do dočasné tabulky databáze. Existuje pokročilý způsob, jak ho nastavit s kombinací více aktivit, najdete v tématu věnovaném [optimalizaci SQL Database hromadně Upsertch scénářů](https://github.com/scoriani/azuresqlbulkupsert). Níže ukazuje ukázku použití trvalé tabulky jako přípravy.

Jako příklad můžete v Azure Data Factory vytvořit kanál s **aktivitou kopírování** zřetězenou s **aktivitou uložené procedury**. Předchozí kopie dat ze zdrojového úložiště do pracovní tabulky Azure SQL Managed instance, jako je například **UpsertStagingTable**, jako název tabulky v datové sadě. Pak druhá procedura vyvolá uloženou proceduru ke sloučení zdrojových dat z pracovní tabulky do cílové tabulky a vyčištění pracovní tabulky.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

V databázi definujte uloženou proceduru pomocí logiky sloučení, podobně jako v následujícím příkladu, který ukazuje z předchozí aktivity uložené procedury. Předpokládejme, že cílem je **marketingová** tabulka se třemi sloupci: **ProfileID**, **State**a **Category**. Proveďte Upsert na základě sloupce **ProfileID** .

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

### <a name="overwrite-the-entire-table"></a>Přepsat celou tabulku

Vlastnost **preCopyScript** můžete nakonfigurovat v jímky aktivity kopírování. V takovém případě se pro každou aktivitu kopírování, která běží, Azure Data Factory spustí skript jako první. Potom spustí kopii pro vložení dat. Chcete-li například přepsat celou tabulku nejnovějšími daty, zadejte skript, který nejprve odstraní všechny záznamy před hromadnou zátěží nových dat ze zdroje.

### <a name="write-data-with-custom-logic"></a>Zápis dat pomocí vlastní logiky

Postup pro zápis dat pomocí vlastní logiky je podobný těm, které jsou popsané v části [Upsert data](#upsert-data) . Pokud potřebujete použít dodatečné zpracování před konečným vložením zdrojových dat do cílové tabulky, můžete načíst do pracovní tabulky, vyvolat aktivitu uložené procedury nebo vyvolat uloženou proceduru v jímky aktivity kopírování pro uplatnění dat.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Vyvolat uloženou proceduru z jímky SQL

Při kopírování dat do spravované instance SQL můžete také nakonfigurovat a vyvolat uloženou proceduru zadanou uživatelem s dalšími parametry na každé dávce zdrojové tabulky. Funkce uložené procedury využívá [parametry s hodnotou tabulky](https://msdn.microsoft.com/library/bb675163.aspx).

Uloženou proceduru můžete použít, pokud předdefinované mechanismy kopírování neslouží k tomuto účelu. Příkladem je, že chcete použít dodatečné zpracování před konečným vložením zdrojových dat do cílové tabulky. Některé další příklady zpracování jsou, když chcete sloučit sloupce, vyhledat další hodnoty a vložit je do více než jedné tabulky.

Následující příklad ukazuje, jak použít uloženou proceduru k provedení Upsert do tabulky v databázi SQL Server. Předpokládejme, že vstupní data a **marketingová** tabulka jímky mají tři sloupce: **ProfileID**, **State**a **Category**. Proveďte Upsert na základě sloupce **ProfileID** a použijte ho jenom pro konkrétní kategorii s názvem "produkt".

1. V databázi Definujte typ tabulky se stejným názvem jako **sqlWriterTableType**. Schéma typu tabulky je stejné jako schéma vrácené vašimi vstupními daty.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. V databázi definujte uloženou proceduru se stejným názvem jako **sqlWriterStoredProcedureName**. Zpracovává vstupní data ze zadaného zdroje a sloučí je do výstupní tabulky. Název parametru typu tabulky v uložené proceduře je stejný jako **TableName** definovaný v datové sadě.

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

3. V Azure Data Factory v aktivitě kopírování definujte část **jímka SQL mi** , jak je znázorněno níže:

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

## <a name="data-type-mapping-for-sql-managed-instance"></a>Mapování datového typu pro spravovanou instanci SQL

Když se data zkopírují do spravované instance SQL a z ní, používají se k Azure Data Factory dočasných datových typů následující mapování z datových typů spravované instance SQL. Informace o tom, jak aktivita kopírování mapuje ze zdrojového schématu a datového typu do jímky, najdete v tématu [mapování schémat a datových typů](copy-activity-schema-and-type-mapping.md).

| Datový typ spravované instance SQL | Azure Data Factory pomocný datový typ |
|:--- |:--- |
| bigint |Int64 |
| binární |Byte [] |
| bit |Logická hodnota |
| char |Řetězec, znak [] |
| datum |DateTime |
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
| tinyint |Int16 |
| uniqueidentifier |Identifikátor GUID |
| varbinary |Byte [] |
| varchar |Řetězec, znak [] |
| xml |Řetězec |

>[!NOTE]
> Pro datové typy, které jsou mapovány na mezihodnotový průběžný typ, aktuálně aktivita kopírování podporuje přesnost až na 28. Pokud máte data, která vyžadují přesnost větší než 28, zvažte převod na řetězec v dotazu SQL.

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Vlastnosti aktivity GetMetadata

Pokud se chcete dozvědět víc o vlastnostech, podívejte se na [aktivitu GetMetadata](control-flow-get-metadata-activity.md) . 

## <a name="using-always-encrypted"></a>Použití Always Encrypted

Při kopírování dat z/do spravované instance Azure SQL pomocí [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine)použijte [obecný konektor ODBC](connector-odbc.md) a SQL Server ovladač ODBC prostřednictvím autohosta Integration runtime. Tento konektor Azure SQL Managed instance nepodporuje Always Encrypted nyní. 

A konkrétně:

1. Nastavte Integration Runtime v místním prostředí, pokud ho ještě nemáte. Podrobnosti najdete v článku [Integration runtime](create-self-hosted-integration-runtime.md) v místním prostředí.

2. Stáhněte si z [tohoto místa](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server)ovladač ODBC 64 pro SQL Server a nainstalujte ho do Integration runtime počítače. Přečtěte si další informace o tom, jak tento ovladač funguje, [pomocí Always Encrypted s ovladačem ODBC pro SQL Server](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver#using-the-azure-key-vault-provider).

3. Vytvoření propojené služby s typem ODBC pro připojení k vaší databázi SQL najdete v následujících ukázkách:

    - Použití **ověřování SQL**: zadejte připojovací řetězec ODBC, jak je uvedeno níže, a vyberte **základní** ověřování a nastavte uživatelské jméno a heslo.

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>
        ```

    - Použití **spravovaného ověřování Identity Data Factory**: 

        1. Při vytváření uživatele databáze pro spravovanou identitu použijte stejné [předpoklady](#managed-identity) a udělte v databázi správnou roli.
        2. V části propojená služba zadejte připojovací řetězec ODBC následujícím způsobem a pak vyberte **anonymní** ověřování, které označuje samotný připojovací řetězec `Authentication=ActiveDirectoryMsi` .

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>; Authentication=ActiveDirectoryMsi;
        ```

4. Odpovídajícím způsobem Vytvořte datovou sadu a aktivitu kopírování s použitím typu ODBC. Další informace najdete v článku [konektor ODBC](connector-odbc.md) .

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Azure Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
