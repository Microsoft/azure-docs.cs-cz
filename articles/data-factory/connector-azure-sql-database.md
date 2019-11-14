---
title: Kopírování a transformace dat v Azure SQL Database pomocí Data Factory
description: Naučte se, jak kopírovat data do a z Azure SQL Database a transformovat data v Azure SQL Database pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: jingwang
ms.openlocfilehash: b899d9884a80a882ca03d3d970421227a48a3803
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075598"
---
# <a name="copy-and-transform-data-in-azure-sql-database-by-using-azure-data-factory"></a>Kopírování a transformace dat v Azure SQL Database pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Azure Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-azure-sql-connector.md)
> * [Aktuální verze](connector-azure-sql-database.md)

V tomto článku se naučíte, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z a do Azure SQL Database a jak transformovat data v Azure SQL Database pomocí toku dat. Další informace o Azure Data Factory najdete v článku [úvodní článek](introduction.md).

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor Azure SQL Database se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou tabulkou matice zdroje/jímky](copy-activity-overview.md)
- [Mapování toku dat](concepts-data-flow-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)

U aktivity kopírování tato Azure SQL Database konektor podporuje tyto funkce:

- Kopírování dat pomocí ověřování SQL a Azure Active Directory (Azure AD) ověřování tokenu aplikace pomocí instančního objektu nebo spravovaných identit pro prostředky Azure.
- Jako zdroj načítání dat pomocí dotazu SQL nebo uložené procedury.
- Jako jímky, připojení dat do cílové tabulky nebo vyvolání uložené procedury s vlastní logikou během kopírování.

>[!NOTE]
>Tento konektor teď nepodporuje Azure SQL Database [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-current) . Pokud chcete tento problém obejít, můžete použít [obecný konektor ODBC](connector-odbc.md) a SQL Server ovladač ODBC prostřednictvím prostředí Integration runtime v místním prostředí. Postupujte [podle pokynů ke](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-current) stažení ovladače ODBC a konfigurací připojovacích řetězců.

> [!IMPORTANT]
> Pokud kopírujete data pomocí prostředí Azure Data Factory Integration runtime, nakonfigurujte [bránu azure SQL Server Firewall](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) , aby mohli služby Azure přistupovat k serveru.
> Pokud kopírujete data pomocí místního prostředí Integration runtime, nakonfigurujte bránu firewall Azure SQL Server tak, aby umožňovala odpovídající rozsah IP adres. Tento rozsah zahrnuje IP adresu počítače, která se používá pro připojení k Azure SQL Database.

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které slouží k definování Azure Data Factory entit specifických pro Azure SQL Database konektor.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Tyto vlastnosti jsou podporovány pro propojenou službu Azure SQL Database:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **Type** musí být nastavená na **AzureSqlDatabase**. | Ano |
| connectionString | Zadejte informace potřebné pro připojení k instanci Azure SQL Database pro vlastnost **ConnectionString** . <br/>Označte toto pole jako **SecureString** a bezpečně ho uložte do Azure Data Factory. Do Azure Key Vault také můžete vložit heslo nebo hlavní klíč služby. Pokud se jedná o ověřování SQL, vyžádejte si z připojovacího řetězce `password`ou konfiguraci. Další informace najdete v příkladech JSON, které následují po tabulce, a [ukládají přihlašovací údaje v Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| servicePrincipalId | Zadejte ID klienta vaší aplikace. | Ano, pokud používáte ověřování Azure AD s instančním objektem |
| servicePrincipalKey | Zadejte klíč aplikace. Označte toto pole jako **SecureString** a bezpečně ho uložte do Azure Data Factory nebo [odkaz na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | Ano, pokud používáte ověřování Azure AD s instančním objektem |
| tenant | Zadejte informace o tenantovi, jako je název domény nebo ID tenanta, pod kterým se vaše aplikace nachází. Načtěte ho tak, že najedete myší v pravém horním rohu Azure Portal. | Ano, pokud používáte ověřování Azure AD s instančním objektem |
| connectVia | Tento [modul runtime integrace](concepts-integration-runtime.md) se používá pro připojení k úložišti dat. Pokud se vaše úložiště dat nachází v privátní síti, můžete použít prostředí Azure Integration runtime nebo místní prostředí Integration runtime. Pokud tento parametr nezadáte, použije se výchozí prostředí Azure Integration runtime. | Ne |

Různými typy ověřování najdete v následujících částech na požadavky a ukázky JSON v uvedeném pořadí:

- [Ověřování SQL](#sql-authentication)
- [Ověřování tokenu aplikací služby Azure AD: instanční objekt](#service-principal-authentication)
- [Ověřování tokenu aplikací Azure AD: spravované identity pro prostředky Azure](#managed-identity)

>[!TIP]
>Pokud dojde k chybě s kódem chyby "UserErrorFailedToConnectToSqlServer" a zprávou jako "omezení relace pro databázi je XXX a bylo dosaženo," přidejte `Pooling=false` do svého připojovacího řetězce a akci opakujte.

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

**Heslo v Azure Key Vault** 

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

Chcete-li použít ověřování pomocí tokenu aplikace služby Azure AD založené na instančním objektu, postupujte podle následujících kroků:

1. [Vytvořte aplikaci Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) z Azure Portal. Poznamenejte si název aplikace a následující hodnoty, které definují propojené služby:

    - ID aplikace
    - Klíč aplikace
    - ID tenanta

2. Pokud jste to ještě neudělali, [zřiďte správce Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) pro Azure SQL Server na Azure Portal. Správce Azure AD musí být uživatel služby Azure AD nebo skupina Azure AD, ale nemůže se jednat o instanční objekt. Tento krok provedete tak, že v dalším kroku můžete pomocí identity Azure AD vytvořit uživatele databáze s omezením pro instanční objekt.

3. [Vytvořte uživatele databáze s omezením](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) pro instanční objekt. Připojte se k databázi z nebo na kterou chcete kopírovat data pomocí nástrojů, jako je SQL Server Management Studio, s identitou Azure AD, která má alespoň změnu oprávnění uživatele. Spusťte následující příkaz T-SQL: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. Udělte instančnímu objektu potřebná oprávnění, jako byste to udělali normálně pro uživatele SQL nebo jiné. Spusťte následující kód. Další možnosti najdete v [tomto dokumentu](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. Nakonfigurujte propojenou službu Azure SQL Database v Azure Data Factory.


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

Datová továrna může být přidružená ke [spravované identitě pro prostředky Azure](data-factory-service-identity.md) , které představují konkrétní objekt pro vytváření dat. Tuto spravovanou identitu můžete použít pro ověřování Azure SQL Database. Určená továrna má přístup k datům a jejich zkopírování z databáze nebo do databáze pomocí této identity.

Pokud chcete použít spravované ověřování identity, postupujte podle těchto kroků.

1. Pokud jste to ještě neudělali, [zřiďte správce Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) pro Azure SQL Server na Azure Portal. Správce Azure AD může být uživatel Azure AD nebo skupina Azure AD. Pokud skupině udělíte roli správce spravovanou identitou, přeskočte kroky 3 a 4. Správce má úplný přístup k databázi.

2. [Vytvořte uživatele databáze s omezením](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) pro Azure Data Factory spravovanou identitu. Připojte se k databázi z nebo na kterou chcete kopírovat data pomocí nástrojů, jako je SQL Server Management Studio, s identitou Azure AD, která má alespoň změnu oprávnění uživatele. Spusťte následující příkaz T-SQL: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. Udělte Data Factory spravovaná identita potřebná k tomu, aby se běžně daly dělat pro uživatele SQL a jiné. Spusťte následující kód. Další možnosti najdete v [tomto dokumentu](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    EXEC sp_addrolemember [role name], [your Data Factory name];
    ```

4. Nakonfigurujte propojenou službu Azure SQL Database v Azure Data Factory.

**Příklad**

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

Úplný seznam sekcí a vlastností dostupných pro definování datových sad najdete v tématu [datové sady](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services). 

Pro Azure SQL Database datovou sadu jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **Type** datové sady musí být nastavená na **AzureSqlTable**. | Ano |
| schema | Název schématu. |Ne pro zdroj, Ano pro jímku  |
| table | Název tabulky/zobrazení |Ne pro zdroj, Ano pro jímku  |
| tableName | Název tabulky nebo zobrazení se schématem. Tato vlastnost je podporována z důvodu zpětné kompatibility. Pro nové úlohy použijte `schema` a `table`. | Ne pro zdroj, Ano pro jímku |

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
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností dostupných pro definování aktivit najdete v tématu [kanály](concepts-pipelines-activities.md). V této části najdete seznam vlastností podporovaných zdrojem a jímkou Azure SQL Database.

### <a name="azure-sql-database-as-the-source"></a>Azure SQL Database jako zdroj

Chcete-li kopírovat data z Azure SQL Database, jsou v části **zdroje** aktivity kopírování podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **Type** zdroje aktivity kopírování musí být nastavená na **AzureSqlSource**. Typ "SqlSource" je stále podporován z důvodu zpětné kompatibility. | Ano |
| sqlReaderQuery | Tato vlastnost používá vlastní dotaz SQL ke čtení dat. Příklad: `select * from MyTable`. | Ne |
| sqlReaderStoredProcedureName | Název uložené procedury, která čte data ze zdrojové tabulky. Příkaz SELECT v uložené proceduře musí být poslední příkaz jazyka SQL. | Ne |
| storedProcedureParameters | Parametry pro uloženou proceduru.<br/>Povolené hodnoty jsou páry název nebo hodnota. Názvy a velikost písmen parametrů se musí shodovat s názvy a písmeny parametrů uložené procedury. | Ne |

**Ukazuje na poznámku:**

- Pokud je pro **AzureSqlSource**zadaný **sqlReaderQuery** , aktivita kopírování spustí tento dotaz na zdroj Azure SQL Database, aby se data získala. Uloženou proceduru lze také určit zadáním **sqlReaderStoredProcedureName** a **storedProcedureParameters** , pokud uložená procedura přijímá parametry.
- Pokud nezadáte buď **sqlReaderQuery** nebo **sqlReaderStoredProcedureName**, budou použity sloupce definované v oddílu Structure pro datovou sadu JSON pro vytvoření dotazu. Dotaz `select column1, column2 from mytable` spouští proti Azure SQL Database. Pokud definice datové sady nemá "strukturu", všechny sloupce jsou vybrány z tabulky.

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

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **Type** jímky aktivity kopírování musí být nastavená na **AzureSqlSink**. Typ "SqlSink" je stále podporován z důvodu zpětné kompatibility. | Ano |
| writeBatchSize | Počet řádků, které mají být vloženy do tabulky SQL *na dávku*.<br/> Je povolená hodnota **celé číslo** (počet řádků). Ve výchozím nastavení Azure Data Factory dynamicky určí vhodnou velikost dávky na základě velikosti řádku. | Ne |
| writeBatchTimeout | Doba čekání na dokončení operace dávkového vložení před vypršením časového limitu.<br/> Je povolená hodnota **timespan**. Příkladem je "00:30:00" (30 minut). | Ne |
| preCopyScript | Zadejte dotaz SQL pro aktivitu kopírování, která se má spustit před zápisem dat do Azure SQL Database. Vyvolá se jenom jednou pro každé spuštění kopírování. Tuto vlastnost použijte k vyčištění dat předem. | Ne |
| sqlWriterStoredProcedureName | Název uložené procedury definující, jak se mají zdrojová data použít v cílové tabulce. <br/>Tato uložená procedura je *vyvolána pro každou dávku*. U operací, které se spouští jenom jednou a které nemají nic dělat se zdrojovými daty, třeba odstranění nebo zkrácení, použijte vlastnost `preCopyScript`. | Ne |
| storedProcedureTableTypeParameterName |Název parametru pro typ tabulky určený v uložené proceduře.  |Ne |
| sqlWriterTableType |Název typu tabulky, který se má použít v uložené proceduře Aktivita kopírování zpřístupňuje data, která jsou k dispozici v dočasné tabulce s tímto typem tabulky. Uložený kód procedury pak může sloučit data, která jsou kopírována se stávajícími daty. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru.<br/>Povolené hodnoty jsou páry název-hodnota. Názvy a použití malých a velkých parametry musí odpovídat názvům a použití malých a velkých parametrů uložené procedury. | Ne |
| tableOption | Určuje, jestli se má automaticky vytvořit tabulka jímky, pokud na základě schématu zdroje neexistuje. Automatické vytváření tabulek není podporované, pokud jímka určuje uloženou proceduru nebo připravenou kopii nakonfigurovanou v aktivitě kopírování. Povolené hodnoty jsou: `none` (výchozí), `autoCreate`. |Ne |
| disableMetricsCollection | Data Factory shromažďuje metriky, jako je například Azure SQL Database DTU, pro optimalizaci výkonu a doporučení pro kopírování. Pokud máte obavy s tímto chováním, zadejte `true` pro jeho vypnutí. | Ne (výchozí hodnota je `false`) |

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
                "writeBatchSize": 100000,
                "tableOption": "autoCreate"
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

**Možnost 1:** Pokud máte ke kopírování velké množství dat, použijte následující postup k Upsert: 

- Nejdřív použijte [dočasnou tabulku s rozsahem databáze](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current#database-scoped-global-temporary-tables-azure-sql-database) k hromadnému načtení všech záznamů pomocí aktivity kopírování. Vzhledem k tomu, že operace s dočasnými tabulkami s rozsahem databáze nejsou protokolovány, můžete načíst miliony záznamů během několika sekund.
- Spuštěním aktivity uložené procedury v Azure Data Factory použijte příkaz [Merge](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) nebo INSERT/Update. Použijte dočasnou tabulku jako zdroj k provedení všech aktualizací nebo vložení jako jedné transakce. Tímto způsobem se sníží počet operací Round Trip a log. Na konci aktivity uložená procedura můžete dočasnou tabulku zkrátit, aby byla připravená na další Upsert cyklus.

Jako příklad můžete v Azure Data Factory vytvořit kanál s **aktivitou kopírování** zřetězenou s **aktivitou uložené procedury**. Předchozí kopie dat ze zdrojového úložiště do Azure SQL Database dočasné tabulky, například **# #UpsertTempTable**, jako název tabulky v datové sadě. Potom druhá potom vyvolá uloženou proceduru ke sloučení zdrojových dat z dočasné tabulky do cílové tabulky a vyčištění dočasné tabulky.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

V databázi definujte uloženou proceduru pomocí logiky sloučení, podobně jako v následujícím příkladu, který ukazuje z předchozí aktivity uložené procedury. Předpokládejme, že cílem je **marketingová** tabulka se třemi sloupci: **ProfileID**, **State**a **Category**. Proveďte Upsert na základě sloupce **ProfileID** .

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

**Možnost 2:** Také se můžete rozhodnout [vyvolat uloženou proceduru v rámci aktivity kopírování](#invoke-a-stored-procedure-from-a-sql-sink). Tento přístup spustí každý řádek ve zdrojové tabulce namísto použití hromadného vkládání jako výchozího přístupu v aktivitě kopírování, což není vhodné pro velké Upsert.

### <a name="overwrite-the-entire-table"></a>Přepsat celou tabulku

V jímky aktivity kopírování můžete nakonfigurovat vlastnost **preCopyScript** . V takovém případě se pro každou aktivitu kopírování, která běží, Azure Data Factory spustí skript jako první. Potom spustí kopii pro vložení dat. Chcete-li například přepsat celou tabulku nejnovějšími daty, zadejte skript, který nejprve odstraní všechny záznamy před hromadnou zátěží nových dat ze zdroje.

### <a name="write-data-with-custom-logic"></a>Zápis dat pomocí vlastní logiky

Postup pro zápis dat pomocí vlastní logiky je podobný těm, které jsou popsané v části [Upsert data](#upsert-data) . Pokud potřebujete použít dodatečné zpracování před konečným vložením zdrojových dat do cílové tabulky, můžete pro velkou škálu provést jednu z následujících akcí:

- Načtěte do dočasné tabulky s vymezeným oborem databáze a pak vyvolejte uloženou proceduru. 
- Vyvolat uloženou proceduru během kopírování.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a>Vyvolat uloženou proceduru z jímky SQL

Když kopírujete data do Azure SQL Database, můžete také nakonfigurovat a vyvolat uloženou proceduru zadanou uživatelem s dalšími parametry. Funkce uložené procedury využívá [parametry s hodnotou tabulky](https://msdn.microsoft.com/library/bb675163.aspx).

> [!TIP]
> Vyvolání uložené procedury zpracuje řádek data na řádku místo pomocí hromadné operace, kterou nedoporučujeme pro velké kopírování. Další informace z [osvědčeného postupu pro načítání dat do Azure SQL Database](#best-practice-for-loading-data-into-azure-sql-database).

Uloženou proceduru můžete použít, pokud předdefinované mechanismy kopírování neslouží k tomuto účelu. Příkladem je, že chcete použít dodatečné zpracování před konečným vložením zdrojových dat do cílové tabulky. Některé další příklady zpracování jsou, když chcete sloučit sloupce, vyhledat další hodnoty a vložit je do více než jedné tabulky.

Následující příklad ukazuje, jak použít uloženou proceduru k provedení Upsert do tabulky v Azure SQL Database. Předpokládejme, že vstupní data a **marketingová** tabulka jímky mají tři sloupce: **ProfileID**, **State**a **Category**. Proveďte Upsert na základě sloupce **ProfileID** a použijte ho jenom pro konkrétní kategorii s názvem "produkt".

1. V databázi Definujte typ tabulky se stejným názvem jako **sqlWriterTableType**. Schéma typu tabulky je stejné jako schéma vrácené vašimi vstupními daty.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL，
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

Přečtěte si podrobnosti o [transformaci zdrojového kódu](data-flow-source.md) a [transformaci jímky](data-flow-sink.md) v mapování toku dat.

## <a name="data-type-mapping-for-azure-sql-database"></a>Mapování datových typů pro Azure SQL Database

Když se data zkopírují z nebo do Azure SQL Database, použijí se následující mapování Azure SQL Database datových typů, abyste mohli Azure Data Factory dočasné datové typy. Informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ k jímky, najdete v tématu [mapování schémat a datových typů](copy-activity-schema-and-type-mapping.md).

| Azure SQL Database datový typ | Azure Data Factory pomocný datový typ |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Logická hodnota |
| char |String, Char[] |
| date |Datum a čas |
| Datum a čas |Datum a čas |
| datetime2 |Datum a čas |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| Atribut FILESTREAM (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Datový typ Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |Datum a čas |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Objekt |
| text |String, Char[] |
| time |Časový interval |
| časové razítko |Byte[] |
| tinyint |Bajt |
| uniqueidentifier |identifikátor GUID |
| Varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

>[!NOTE]
> Pro datové typy, které jsou mapovány na mezihodnotový průběžný typ, aktuálně Azure Data Factory podporuje přesnost až 28. Pokud máte data s přesností větší než 28, zvažte převod na řetězec v dotazu SQL.

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Vlastnosti aktivity GetMetadata

Pokud se chcete dozvědět víc o vlastnostech, podívejte se na [aktivitu GetMetadata](control-flow-get-metadata-activity.md) . 

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Azure Data Factory najdete v tématu [podporované úložiště dat a formáty](copy-activity-overview.md##supported-data-stores-and-formats).
