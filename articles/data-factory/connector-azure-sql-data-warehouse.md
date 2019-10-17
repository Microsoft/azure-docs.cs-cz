---
title: Kopírování dat do a z Azure SQL Data Warehouse pomocí Azure Data Factory | Microsoft Docs
description: Naučte se, jak kopírovat data z podporovaných úložišť zdrojů do Azure SQL Data Warehouse nebo z SQL Data Warehouse na podporovaná úložiště jímky pomocí Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: jingwang
ms.openlocfilehash: 5351f7f01bbe99b1e3ebc3c94a0805f0419cc1cf
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387917"
---
# <a name="copy-data-to-or-from-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Kopírování dat do nebo z Azure SQL Data Warehouse pomocí Azure Data Factory 
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Version1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Aktuální verze](connector-azure-sql-data-warehouse.md)

Tento článek popisuje, jak kopírovat data do a z Azure SQL Data Warehouse. Pokud se chcete dozvědět o Azure Data Factory, přečtěte si [úvodní článek](introduction.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Azure SQL Data Warehouse se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou tabulkou matice zdroje/jímky](copy-activity-overview.md)
- [Mapování toku dat](concepts-data-flow-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)

Konkrétně tento konektor Azure SQL Data Warehouse podporuje tyto funkce:

- Zkopírujte data pomocí ověřování SQL a Azure Active Directory (Azure AD) ověřování tokenu aplikace pomocí instančního objektu nebo spravovaných identit pro prostředky Azure.
- Jako zdroj načte data pomocí dotazu SQL nebo uložené procedury.
- Jako jímky načtěte data pomocí základu nebo hromadného vložení. Pro lepší výkon kopírování doporučujeme použít základnu.

> [!IMPORTANT]
> Pokud kopírujete data pomocí Azure Data Factory Integration Runtime, nakonfigurujte [bránu firewall serveru SQL Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) , aby služby Azure mohly získat přístup k serveru.
> Pokud kopírujete data pomocí místního prostředí Integration runtime, nakonfigurujte bránu firewall serveru SQL Azure tak, aby povolovala příslušný rozsah IP adres. Tento rozsah zahrnuje IP adresu počítače, která se používá pro připojení k Azure SQL Database.

## <a name="get-started"></a>Začít

> [!TIP]
> Chcete-li dosáhnout nejlepšího výkonu, načtěte data do Azure SQL Data Warehouse pomocí základu. Část [použití základu k načtení dat do části Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) obsahuje podrobnosti. Návod s případem použití najdete v tématu [načtení 1 TB do Azure SQL Data Warehouse za 15 minut s Azure Data Factory](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které definují Data Factory entit specifických pro Azure SQL Data Warehouse konektor.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu Azure SQL Data Warehouse jsou podporovány následující vlastnosti:

| Vlastnost            | Popis                                                  | Požaduje se                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| type                | Vlastnost Type musí být nastavená na **AzureSqlDW**.             | Ano                                                          |
| Vlastnosti    | Zadejte informace potřebné pro připojení k instanci Azure SQL Data Warehouse pro vlastnost **ConnectionString** . <br/>Označte toto pole jako SecureString a bezpečně ho uložte do Data Factory. Můžete také do Azure Key Vault umístit heslo nebo hlavní klíč služby, a pokud ověřování SQL vyžádá z připojovacího řetězce konfiguraci `password`. Další podrobnosti najdete v příkladech JSON pod tabulkou a [přihlašovacími údaji pro Store v Azure Key Vault](store-credentials-in-key-vault.md) článku. | Ano                                                          |
| servicePrincipalId  | Zadejte ID klienta aplikace.                         | Ano, pokud použijete ověřování Azure AD s instančním objektem. |
| servicePrincipalKey | Zadejte klíč aplikace. Označte toto pole jako SecureString, abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | Ano, pokud použijete ověřování Azure AD s instančním objektem. |
| tenant              | Zadejte informace o tenantovi (název domény nebo ID tenanta), pod kterým se vaše aplikace nachází. Můžete ho načíst tak, že najedete myší v pravém horním rohu Azure Portal. | Ano, pokud použijete ověřování Azure AD s instančním objektem. |
| connectVia          | [Prostředí Integration runtime](concepts-integration-runtime.md) , které se má použít pro připojení k úložišti dat. Můžete použít Azure Integration Runtime nebo místní prostředí Integration runtime (Pokud se vaše úložiště dat nachází v privátní síti). Pokud není zadaný, použije se výchozí Azure Integration Runtime. | Ne                                                           |

Pro různé typy ověřování se podívejte na následující oddíly týkající se požadavků a ukázek JSON, v uvedeném pořadí:

- [Ověřování SQL](#sql-authentication)
- Ověřování tokenu aplikací služby Azure AD: [instanční objekt](#service-principal-authentication)
- Ověřování tokenu aplikací Azure AD: [spravované identity pro prostředky Azure](#managed-identity)

>[!TIP]
>Pokud jste narazili na chybu s kódem chyby jako "UserErrorFailedToConnectToSqlServer" a zprávu, jako je "omezení relace pro databázi je XXX a bylo dosaženo.", přidejte do připojovacího řetězce `Pooling=false` a zkuste to znovu.

### <a name="sql-authentication"></a>Ověřování pomocí SQL

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Příklad propojené služby, který používá ověřování SQL

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

**Heslo v Azure Key Vault:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            },
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

1. **[Vytvořte aplikaci Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)** z Azure Portal. Poznamenejte si název aplikace a následující hodnoty, které definují propojenou službu:

    - ID aplikace
    - Klíč aplikace
    - ID tenanta

2. Pokud jste to ještě neudělali, **[zřídí správce služby Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** pro Azure SQL server na Azure Portal. Správce Azure AD může být uživatel Azure AD nebo skupina Azure AD. Pokud skupině udělíte roli správce spravovanou identitou, přeskočte kroky 3 a 4. Správce bude mít úplný přístup k databázi.

3. **[Vytvořte uživatele databáze s omezením](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** pro instanční objekt. Připojte se k datovému skladu z nebo do kterého chcete kopírovat data pomocí nástrojů jako SSMS, s identitou Azure AD, která má alespoň změnu oprávnění uživatele. Spusťte následující příkaz T-SQL:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Udělte instančnímu objektu potřebná oprávnění** , jako byste to udělali normálně pro uživatele SQL nebo jiné. Spusťte následující kód, nebo se [sem](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)podívejte na další možnosti. Pokud chcete k načtení dat použít základ, Projděte si [požadované oprávnění databáze](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. **Nakonfigurujte propojenou službu Azure SQL Data Warehouse** v Azure Data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Příklad propojené služby, který používá ověřování instančního objektu

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

### <a name="managed-identity"></a>Spravované identity pro ověřování prostředků Azure

Datová továrna může být přidružená ke [spravované identitě pro prostředky Azure](data-factory-service-identity.md) , které představují konkrétní továrnu. Tuto spravovanou identitu můžete použít pro ověřování Azure SQL Data Warehouse. Určená továrna má přístup k datovému skladu a jejich kopírování pomocí této identity.

Pokud chcete použít spravované ověřování identity, postupujte podle těchto kroků:

1. Pokud jste to ještě neudělali, **[zřídí správce služby Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** pro Azure SQL server na Azure Portal. Správce Azure AD může být uživatel Azure AD nebo skupina Azure AD. Pokud skupině udělíte roli správce spravovanou identitou, přeskočte kroky 3 a 4. Správce bude mít úplný přístup k databázi.

2. **[Vytvořte uživatele databáze s omezením](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** pro Data Factory spravovanou identitu. Připojte se k datovému skladu z nebo do kterého chcete kopírovat data pomocí nástrojů jako SSMS, s identitou Azure AD, která má alespoň změnu oprávnění uživatele. Spusťte následující příkaz T-SQL. 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Udělte Data Factory spravovaná identita potřebná** k tomu, aby se běžně daly dělat pro uživatele SQL a jiné. Spusťte následující kód, nebo se [sem](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)podívejte na další možnosti. Pokud chcete k načtení dat použít základ, Projděte si [požadované oprávnění databáze](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

5. **Nakonfigurujte propojenou službu Azure SQL Data Warehouse** v Azure Data Factory.

**Příklad:**

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . V této části najdete seznam vlastností podporovaných datovou sadou Azure SQL Data Warehouse.

Chcete-li kopírovat data z nebo do Azure SQL Data Warehouse, jsou podporovány následující vlastnosti:

| Vlastnost  | Popis                                                  | Požaduje se                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | Vlastnost **Type** datové sady musí být nastavená na **AzureSqlDWTable**. | Ano                         |
| XSD | Název schématu. |Ne pro zdroj, Ano pro jímku  |
| Stolní | Název tabulky/zobrazení |Ne pro zdroj, Ano pro jímku  |
| tableName | Název tabulky nebo zobrazení se schématem. Tato vlastnost je podporována z důvodu zpětné kompatibility. Pro nové úlohy použijte `schema` a `table`. | Ne pro zdroj, Ano pro jímku |

#### <a name="dataset-properties-example"></a>Příklad vlastností datové sady

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
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností podporovaných zdrojem a jímkou Azure SQL Data Warehouse.

### <a name="azure-sql-data-warehouse-as-the-source"></a>Azure SQL Data Warehouse jako zdroj

Chcete-li kopírovat data z Azure SQL Data Warehouse, nastavte vlastnost **typ** ve zdroji aktivity kopírování na **SqlDWSource**. V části **zdroj** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost                     | Popis                                                  | Požaduje se |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | Vlastnost **Type** zdroje aktivity kopírování musí být nastavená na **SqlDWSource**. | Ano      |
| sqlReaderQuery               | Pro čtení dat použijte vlastní dotaz SQL. Příklad: `select * from MyTable`. | Ne       |
| sqlReaderStoredProcedureName | Název uložené procedury, která čte data ze zdrojové tabulky. Poslední příkaz SQL musí být příkaz SELECT v uložené proceduře. | Ne       |
| storedProcedureParameters    | Parametry pro uloženou proceduru.<br/>Povolené hodnoty jsou páry název-hodnota. Názvy a malá písmena parametrů se musí shodovat s názvy a písmeny parametrů uložené procedury. | Ne       |

### <a name="points-to-note"></a>Ukazuje na poznámku

- Pokud je pro **SqlSource**určena **SqlReaderQuery** , aktivita kopírování spustí tento dotaz proti zdroji Azure SQL Data Warehouse, aby získala data. Nebo můžete zadat uloženou proceduru. Zadejte **sqlReaderStoredProcedureName** a **storedProcedureParameters** , pokud uložená procedura přijímá parametry.
- Pokud nezadáte buď **sqlReaderQuery** nebo **sqlReaderStoredProcedureName**, budou použity sloupce definované v oddílu **struktury** JSON datové sady pro vytvoření dotazu. `select column1, column2 from mytable` se spouští proti Azure SQL Data Warehouse. Pokud definice datové sady nemá **strukturu**, všechny sloupce jsou vybrány z tabulky.

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

### <a name="azure-sql-data-warehouse-as-sink"></a>Azure SQL Data Warehouse jako jímka

Chcete-li kopírovat data do Azure SQL Data Warehouse, nastavte typ jímky v aktivitě kopírování na **SqlDWSink**. V části **jímka** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost          | Popis                                                  | Požaduje se                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | Vlastnost **Type** jímky aktivity kopírování musí být nastavená na **SqlDWSink**. | Ano                                           |
| allowPolyBase     | Označuje, zda se má použít základ, pokud je k dispozici, namísto mechanismu BULKINSERT. <br/><br/> Doporučujeme načíst data do SQL Data Warehouse pomocí základu. Omezení a podrobnosti najdete v části [použití základu k načtení dat do Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) části.<br/><br/>Povolené hodnoty jsou **true** a **false** (výchozí). | Ne                                            |
| polyBaseSettings  | Skupina vlastností, které lze zadat, je-li vlastnost **allowPolybase** nastavena na **hodnotu true**. | Ne                                            |
| rejectValue       | Určuje počet nebo procento řádků, které lze odmítnout před tím, než se dotaz nezdařil.<br/><br/>Další informace o možnostech odmítnutí základní třídy najdete v oddílu argumenty v tématu [vytvoření externí tabulky (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). <br/><br/>Povolené hodnoty jsou 0 (výchozí), 1, 2 atd. | Ne                                            |
| rejectType        | Určuje, zda je možnost **rejectValue** hodnotou literálu nebo procentem.<br/><br/>Povolené hodnoty jsou **hodnota** (výchozí) a **procento**. | Ne                                            |
| Rejecttype rejectsamplevalue | Určuje počet řádků, které se mají načíst před tím, než základ přepočítá procento odmítnutých řádků.<br/><br/>Povolené hodnoty jsou 1, 2 atd. | Ano, pokud je **rejectType** **procent**. |
| useTypeDefault    | Určuje, jak se mají zpracovat chybějící hodnoty v textových souborech s oddělovači, když základ dat načte data z textového souboru.<br/><br/>Přečtěte si další informace o této vlastnosti z oddílu argumenty v tématu [Create External File Format (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Povolené hodnoty jsou **true** a **false** (výchozí).<br><br> | Ne                                            |
| writeBatchSize    | Počet řádků, které mají být vloženy do tabulky SQL **na dávku**. Platí pouze v případě, že se používá základ.<br/><br/>Povolená hodnota je **Integer** (počet řádků). Ve výchozím nastavení Data Factory dynamicky určí vhodnou velikost dávky na základě velikosti řádku. | Ne                                            |
| writeBatchTimeout | Počkejte, než se operace dávkového vložení dokončí, než vyprší časový limit. Platí pouze v případě, že se používá základ.<br/><br/>Povolená hodnota je **TimeSpan**. Příklad: "00:30:00" (30 minut). | Ne                                            |
| preCopyScript     | Zadejte dotaz SQL pro aktivitu kopírování, která se spustí před zápisem dat do Azure SQL Data Warehouse při každém spuštění. Tato vlastnost slouží k vyčištění předem načtených dat. | Ne                                            |
| tableOption | Určuje, jestli se má automaticky vytvořit tabulka jímky, pokud na základě schématu zdroje neexistuje. Vytvoření automatické tabulky není podporováno, je-li v aktivitě kopírování konfigurována Příprava kopie. Povolené hodnoty jsou: `none` (výchozí), `autoCreate`. |Ne |
| disableMetricsCollection | Data Factory shromažďuje metriky, jako je například SQL Data Warehouse DWU, pro optimalizaci výkonu a doporučení pro kopírování. Pokud máte obavy s tímto chováním, zadejte `true` pro jeho vypnutí. | Ne (výchozí hodnota je `false`) |

#### <a name="sql-data-warehouse-sink-example"></a>Příklad jímky SQL Data Warehouse

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

Přečtěte si další informace o tom, jak použít základnu k efektivnímu načítání SQL Data Warehouse v další části.

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Použít základ k načtení dat do Azure SQL Data Warehouse

Použití [základny](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) je účinný způsob, jak načíst velké množství dat do Azure SQL Data Warehouse s vysokou propustností. Místo výchozího mechanismu BULKINSERT se zobrazí velký nárůst propustnosti pomocí základu. Návod s případem použití najdete v tématu [načtení 1 TB do Azure SQL Data Warehouse](v1/data-factory-load-sql-data-warehouse.md).

* Pokud jsou zdrojová data ve **službě Azure Blob, Azure Data Lake Storage Gen1 nebo Azure Data Lake Storage Gen2**a **formát je**standardně kompatibilní, můžete k přímému vyvolání základny použít aktivitu kopírování, která umožňuje Azure SQL Data Warehouse načíst data ze zdroje. Podrobnosti najdete v tématu **[Přímá kopie pomocí základu](#direct-copy-by-using-polybase)** .
* Pokud se vaše zdrojové úložiště dat a formát v základu nepodporují, použijte místo toho funkci **[dvoufázové kopie pomocí základní](#staged-copy-by-using-polybase)** funkce. Funkce dvoufázové kopírování nabízí také lepší propustnost. Automaticky převede data do formátu kompatibilního se základem. A ukládají data v úložišti objektů BLOB v Azure. Poté načte data do SQL Data Warehouse.

>[!TIP]
>Přečtěte si další informace o [osvědčených postupech pro použití základny](#best-practices-for-using-polybase).

### <a name="direct-copy-by-using-polybase"></a>Přímá kopie pomocí základu

SQL Data Warehouse základna podporuje přímo Azure Blob, Azure Data Lake Storage Gen1 a Azure Data Lake Storage Gen2. Pokud zdrojová data vyhovují kritériím popsaným v této části, použijte základnu pro kopírování přímo ze zdrojového úložiště dat do Azure SQL Data Warehouse. V opačném případě použijte [dvoufázové kopírování pomocí základny](#staged-copy-by-using-polybase).

> [!TIP]
> Pokud chcete data efektivně kopírovat SQL Data Warehouse, přečtěte si další informace z [Azure Data Factory díky tomu je ještě snazší a pohodlnější získat poznatky z dat při použití Data Lake Store se SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Pokud požadavky nejsou splněné, Azure Data Factory zkontroluje nastavení a automaticky se vrátí k BULKINSERT mechanismu přesunu dat.

1. **Zdrojová propojená služba** je s následujícími typy a metodami ověřování:

    | Typ podporovaného zdrojového úložiště dat                             | Typ podporovaného ověřování zdroje                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Objekt blob Azure](connector-azure-blob-storage.md)                | Ověřování klíčů účtu, ověřování spravované identity |
    | [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | Ověřování instančních objektů                            |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | Ověřování klíčů účtu, ověřování spravované identity |

    >[!IMPORTANT]
    >Pokud je vaše Azure Storage nakonfigurovaná pomocí koncového bodu služby virtuální sítě, musíte použít spravované ověřování identity – Přečtěte si, [Jaký je dopad použití koncových bodů služby virtuální sítě se službou Azure Storage](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Přečtěte si o požadovaných konfiguracích v Data Factory z oddílu [ověřování identity spravovaného pomocí objektů BLOB v Azure](connector-azure-blob-storage.md#managed-identity) a v části [ověřování identity spravované Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#managed-identity) .

2. **Zdrojový formát dat** je **Parquet**, **ORC**nebo **text s oddělovači**, s následujícími konfiguracemi:

   1. Cesta ke složce neobsahuje filtr zástupných znaků.
   2. Název souboru je prázdný nebo odkazuje na jeden soubor. Pokud v aktivitě kopírování zadáte název souboru se zástupnými znaky, může to být jenom `*` nebo `*.*`.
   3. `rowDelimiter` je **výchozí**, **\n**, **\r\n**nebo **\r**.
   4. `nullValue` je ponechán jako výchozí nebo nastaven na **prázdný řetězec** ("") a `treatEmptyAsNull` je ponecháno jako výchozí nebo nastaveno na hodnotu true.
   5. `encodingName` je ponecháno jako výchozí nebo nastaveno na **UTF-8**.
   6. `quoteChar`, `escapeChar` a `skipLineCount` nejsou zadány. Základní podpora – přeskočit řádek záhlaví, který se dá nakonfigurovat jako `firstRowAsHeader` v ADF.
   7. `compression` nemůže být **žádná komprese**, **gzip**nebo **Deflate**.

3. Pokud je zdrojem složka, `recursive` v aktivitě kopírování musí být nastaven na hodnotu true (pravda).

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
                    "type": "AzureBlobStorageReadSetting",
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

Pokud zdrojová data nesplňují kritéria v předchozí části, povolte kopírování dat prostřednictvím dočasné pracovní instance služby Azure Blob Storage. Nemůže to být Azure Premium Storage. V tomto případě Azure Data Factory automaticky spouští transformace dat, aby splňovaly požadavky na formát dat základu. Pak použije základnu k načtení dat do SQL Data Warehouse. Nakonec vymaže vaše dočasná data z úložiště objektů BLOB. Podrobnosti o kopírování dat pomocí pracovní instance úložiště objektů BLOB v Azure najdete v tématu [připravené kopírování](copy-activity-performance.md#staged-copy) .

Pokud chcete tuto funkci použít, vytvořte [propojenou službu azure BLOB Storage](connector-azure-blob-storage.md#linked-service-properties) , která odkazuje na účet služby Azure Storage s dočasným úložištěm objektů BLOB. Pak zadejte vlastnosti `enableStaging` a `stagingSettings` pro aktivitu kopírování, jak je znázorněno v následujícím kódu.

>[!IMPORTANT]
>Pokud je vaše pracovní Azure Storage nakonfigurovaná pomocí koncového bodu služby virtuální sítě, musíte použít spravované ověřování identity – Přečtěte si [dopad použití koncových bodů služby virtuální sítě se službou Azure Storage](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Přečtěte si o požadovaných konfiguracích v Data Factory z [ověřování identity spravovaného objektem BLOB v Azure](connector-azure-blob-storage.md#managed-identity).

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

## <a name="best-practices-for-using-polybase"></a>Osvědčené postupy pro použití základu

Následující části obsahují osvědčené postupy kromě těch, které jsou uvedené v článku [osvědčené postupy pro Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Požadovaná oprávnění databáze

Chcete-li použít základnu, uživatel, který načte data do SQL Data Warehouse, musí mít v cílové databázi [oprávnění "Control"](https://msdn.microsoft.com/library/ms191291.aspx) . Jedním ze způsobů, jak toho dosáhnout, je přidání uživatele jako člena role **db_owner** . Přečtěte si, jak to udělat v [přehledu SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limits"></a>Omezení velikosti řádku a datového typu

Základní zatížení je omezeno na řádky menší než 1 MB. Nedá se použít k načtení do VARCHR (MAX), NVARCHAR (MAX) nebo VARBINARY (MAX). Další informace najdete v tématu [omezení kapacity služby SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Pokud má zdrojová data řádky větší než 1 MB, možná budete chtít svisle rozdělit zdrojové tabulky na několik malých. Ujistěte se, že největší velikost každého řádku nepřekračuje limit. Menší tabulky se pak dají načíst pomocí základu a sloučí se společně v Azure SQL Data Warehouse.

Alternativně můžete pro data s takovými roztažitelnémi sloupci použít nezaloženou základnu k načtení dat pomocí automatického podavače (ADF) vypnutím nastavení "zapnout základnu".

### <a name="sql-data-warehouse-resource-class"></a>SQL Data Warehouse – Třída prostředků

Abyste dosáhli nejlepší možné propustnosti, přiřaďte uživateli větší třídu prostředků, která načte data do SQL Data Warehouse prostřednictvím základu.

### <a name="polybase-troubleshooting"></a>Řešení potíží se základem

**Načítání do desetinného sloupce**

Pokud jsou vaše zdrojová data v textovém formátu nebo v jiných nekompatibilních úložištích, která nejsou kompatibilní s primárním úložištěm (pomocí připraveného kopírování a základu), a obsahuje prázdnou hodnotu, která se má načíst do SQL Data Warehouse desetinného sloupce, může se vyrazit následující chyba:

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

Řešením je zrušit výběr možnosti**použít výchozí typ**(jako false) ve jímky aktivity kopírování – > základní nastavení. "[USE_TYPE_DEFAULT](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)" je základní nativní konfigurace, která určuje, jak se mají zpracovat chybějící hodnoty v textových souborech s oddělovači, když základ dat načte data z textového souboru. 

**`tableName` v Azure SQL Data Warehouse**

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

Hodnota NULL je speciální forma výchozí hodnoty. Pokud sloupec může mít hodnotu null, vstupní data v objektu BLOB pro tento sloupec můžou být prázdná. Ve vstupní datové sadě ale nemůže chybět. Základní znak Insert pro chybějící hodnoty v Azure SQL Data Warehouse je NULL.

## <a name="mapping-data-flow-properties"></a>Mapování vlastností toku dat

Přečtěte si podrobnosti o [transformaci zdrojového kódu](data-flow-source.md) a [transformaci jímky](data-flow-sink.md) v mapování toku dat.

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Mapování datových typů pro Azure SQL Data Warehouse

Při kopírování dat z nebo do Azure SQL Data Warehouse se z Azure SQL Data Warehouse datových typů používají následující mapování pro Azure Data Factory dočasných datových typů. Informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ do jímky, najdete v tématu [mapování typů schématu a dat](copy-activity-schema-and-type-mapping.md) .

>[!TIP]
>V Azure SQL Data Warehouse článku o datových typech podporovaných SQL DW a alternativním řešení pro nepodporované typy dat [v tabulce](../sql-data-warehouse/sql-data-warehouse-tables-data-types.md) najdete informace.

| Azure SQL Data Warehouse datový typ    | Data Factory pomocný datový typ |
| :------------------------------------ | :----------------------------- |
| bigint                                | Int64                          |
| Tvaru                                | Byte []                         |
| 40bitového                                   | Logická hodnota                        |
| char                                  | Řetězec, znak []                 |
| date                                  | Datum a čas                       |
| Hodnotu                              | Datum a čas                       |
| datetime2                             | Datum a čas                       |
| DateTimeOffset                        | DateTimeOffset                 |
| Notaci                               | Notaci                        |
| Atribut FILESTREAM (varbinary (max)) | Byte []                         |
| Plovák                                 | Klepat                         |
| image                                 | Byte []                         |
| int                                   | Uvedena                          |
| papír                                 | Notaci                        |
| nchar                                 | Řetězec, znak []                 |
| číselné                               | Notaci                        |
| nvarchar                              | Řetězec, znak []                 |
| nemovitostí                                  | Jednoduchá                         |
| rowversion                            | Byte []                         |
| smalldatetime                         | Datum a čas                       |
| smallint                              | Int16                          |
| smallmoney                            | Notaci                        |
| time                                  | TimeSpan                       |
| tinyint                               | Bytové                           |
| uniqueidentifier                      | Guid                           |
| varbinary                             | Byte []                         |
| varchar                               | Řetězec, znak []                 |

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Vlastnosti aktivity GetMetadata

Pokud se chcete dozvědět víc o vlastnostech, podívejte se na [aktivitu GetMetadata](control-flow-get-metadata-activity.md) . 

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky pomocí aktivity kopírování v Azure Data Factory najdete v tématu [podporovaná úložiště dat a formáty](copy-activity-overview.md##supported-data-stores-and-formats).
