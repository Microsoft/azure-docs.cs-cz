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
ms.date: 12/12/2019
ms.openlocfilehash: f009b438cb0dc227289d65604d89c11fd382b675
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75892962"
---
# <a name="copy-and-transform-data-in-azure-synapse-analytics-formerly-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Kopírování a transformace dat ve službě Azure synapse Analytics (dříve Azure SQL Data Warehouse) pomocí Azure Data Factory 

> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Version1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Aktuální verze](connector-azure-sql-data-warehouse.md)

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory kopírovat data z a do Azure synapse Analytics a jak transformovat data v Azure Data Lake Storage Gen2 pomocí toku dat. Další informace o Azure Data Factory najdete v článku [úvodní článek](introduction.md).

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor Azure synapse Analytics se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou tabulkou matice zdroje/jímky](copy-activity-overview.md)
- [Mapování toku dat](concepts-data-flow-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)

U aktivity kopírování tento konektor Azure synapse Analytics podporuje tyto funkce:

- Kopírování dat pomocí ověřování SQL a ověřování pomocí tokenu aplikace Azure Active Directory (Azure AD) pomocí identity objektu zabezpečení nebo spravované služby pro prostředky Azure.
- Jako zdroj načtení dat pomocí jazyka SQL nebo uloženou proceduru.
- Jako jímky načtěte data pomocí příkazu [Base](#use-polybase-to-load-data-into-azure-sql-data-warehouse) nebo [copy](#use-copy-statement) (Preview) nebo hromadného vložení. Pro lepší výkon kopírování doporučujeme použít příkaz Base nebo COPY (Preview).

> [!IMPORTANT]
> Pokud zkopírujete data pomocí Azure Data Factory Integration Runtime, nakonfigurujte [brány firewall serveru Azure SQL](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) tak, aby službám Azure přístup k serveru.
> Pokud zkopírujete data s využitím místního prostředí integration runtime, konfigurace brány firewall serveru Azure SQL umožňující odpovídající rozsah IP adres. Tento rozsah zahrnuje IP adresu počítače, která se používá pro připojení ke službě Azure synapse Analytics.

## <a name="get-started"></a>Začít

> [!TIP]
> Abyste dosáhli nejlepšího výkonu, využijte základ k načtení dat do služby Azure synapse Analytics. Informace najdete v části [použití základu k načtení dat do služby Azure synapse Analytics](#use-polybase-to-load-data-into-azure-sql-data-warehouse) . Návod s případem použití najdete v tématu [načtení 1 TB do služby Azure synapse Analytics za 15 minut s Azure Data Factory](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které definují Data Factory entit specifických pro konektor Azure synapse Analytics.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu Azure synapse Analytics jsou podporovány následující vlastnosti:

| Vlastnost            | Popis                                                  | Požaduje se                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| type                | Vlastnost type musí být nastavená na **AzureSqlDW**.             | Ano                                                          |
| connectionString    | Zadejte informace potřebné pro připojení ke službě Azure synapse Analytics instance pro vlastnost **ConnectionString** . <br/>Označte toto pole jako SecureString a bezpečně ho uložte do Data Factory. Můžete také do Azure Key Vault umístit klíč k hlavnímu názvu hesla nebo služby a v případě, že ověřování SQL vyžádá `password` konfiguraci z připojovacího řetězce. Další podrobnosti najdete v příkladech JSON pod tabulkou a [přihlašovacími údaji pro Store v Azure Key Vault](store-credentials-in-key-vault.md) článku. | Ano                                                          |
| servicePrincipalId  | Zadejte ID klienta vaší aplikace.                         | Ano, pokud používáte ověřování Azure AD se instanční objekt služby. |
| servicePrincipalKey | Zadejte klíč aplikace. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano, pokud používáte ověřování Azure AD se instanční objekt služby. |
| tenant              | Zadejte informace o tenantovi (domény ID tenanta nebo název) v rámci které se nachází vaše aplikace. Podržením ukazatele myši v pravém horním rohu webu Azure portal můžete načíst ji. | Ano, pokud používáte ověřování Azure AD se instanční objekt služby. |
| connectVia          | [Prostředí integration runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. (Pokud je vaše úložiště dat se nachází v privátní síti), můžete použít prostředí Azure Integration Runtime nebo místní prostředí integration runtime. Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. | Ne                                                           |

Různými typy ověřování najdete v následujících částech na požadavky a ukázky JSON v uvedeném pořadí:

- [Ověřování SQL](#sql-authentication)
- Azure AD aplikace ověřování pomocí tokenu: [instančního objektu](#service-principal-authentication)
- Azure AD aplikace ověřování pomocí tokenu: [spravovaných identit pro prostředky Azure](#managed-identity)

>[!TIP]
>Pokud spuštění chybě s kódem chyby jako "UserErrorFailedToConnectToSqlServer" a zpráva jako "limit relace pro databázi je XXX a bylo ho dosaženo.", přidejte `Pooling=false` připojovací řetězec a zkuste to znovu.

### <a name="sql-authentication"></a>Ověřování pomocí SQL

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

Pokud chcete použít ověřování pomocí tokenu aplikace služby založené na instanční objekt Azure AD, postupujte takto:

1. **[Vytvoření aplikace Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)**  z portálu Azure portal. Poznamenejte si název aplikace a následující hodnoty, které definují propojené služby:

    - ID aplikace
    - Klíč aplikace
    - ID tenanta

2. **[Zřízení správce Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  pro váš server Azure SQL na portálu Azure portal, pokud jste tak již neučinili. Správce Azure AD může být skupina Azure AD nebo uživatel Azure AD. Pokud skupině udělíte roli správce spravovanou identitou, přeskočte kroky 3 a 4. Správce bude mít plný přístup k databázi.

3. **[Vytvořit uživatele databáze s omezením](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  pro instanční objekt. Připojení k datovému skladu z nebo na který se má ke kopírování dat pomocí nástrojů, jako je SSMS, identity Azure AD, který má alespoň oprávnění ALTER ANY uživatele. Spusťte následující příkaz T-SQL:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Udělte nezbytná oprávnění instančního objektu** SQL uživatelů nebo jiné obvyklým způsobem. Spusťte následující kód, nebo se [sem](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)podívejte na další možnosti. Pokud chcete k načtení dat použít základ, Projděte si [požadované oprávnění databáze](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. **Nakonfigurujte propojenou službu Azure synapse Analytics** v Azure Data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Příklad propojené služby, který používá ověřování instančních objektů

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

### <a name="managed-identity"></a> Spravovaných identit pro ověřování prostředků Azure

Je možné přidružit datové továrny [spravované identity pro prostředky Azure](data-factory-service-identity.md) , která představuje konkrétní objekt pro vytváření. Tuto spravovanou identitu můžete použít pro ověřování Azure synapse Analytics. Přístup k určené objekt pro vytváření a kopírování dat z nebo do data warehouse s použitím této identity.

Pokud chcete použít spravované ověřování identity, postupujte podle těchto kroků:

1. **[Zřízení správce Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  pro váš server Azure SQL na portálu Azure portal, pokud jste tak již neučinili. Správce Azure AD může být skupina Azure AD nebo uživatel Azure AD. Pokud skupině udělíte roli správce spravovanou identitou, přeskočte kroky 3 a 4. Správce bude mít plný přístup k databázi.

2. **[Vytvořte uživatele databáze s omezením](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** pro Data Factory spravovanou identitu. Připojení k datovému skladu z nebo na který se má ke kopírování dat pomocí nástrojů, jako je SSMS, identity Azure AD, který má alespoň oprávnění ALTER ANY uživatele. Spusťte následující příkaz T-SQL. 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Udělte Data Factory spravovaná identita potřebná** k tomu, aby se běžně daly dělat pro uživatele SQL a jiné. Spusťte následující kód, nebo se [sem](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)podívejte na další možnosti. Pokud chcete k načtení dat použít základ, Projděte si [požadované oprávnění databáze](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

5. **Nakonfigurujte propojenou službu Azure synapse Analytics** v Azure Data Factory.

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. 

Pro datovou sadu Azure synapse Analytics jsou podporovány následující vlastnosti:

| Vlastnost  | Popis                                                  | Požaduje se                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | **Typ** musí být nastavena vlastnost datové sady **AzureSqlDWTable**. | Ano                         |
| schema | Název schématu. |Ne pro zdroj, Ano pro jímku  |
| table | Název tabulky/zobrazení |Ne pro zdroj, Ano pro jímku  |
| tableName | Název tabulky nebo zobrazení se schématem. Tato vlastnost je podporována z důvodu zpětné kompatibility. Pro nové úlohy použijte `schema` a `table`. | Ne pro zdroj, Ano pro jímku |

#### <a name="dataset-properties-example"></a>Příklad vlastnosti datové sady

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. V této části najdete seznam vlastností podporovaných zdrojem a jímkou Azure synapse Analytics.

### <a name="azure-synapse-analytics-as-the-source"></a>Azure synapse Analytics jako zdroj

Pokud chcete kopírovat data z Azure synapse Analytics, nastavte vlastnost **typ** ve zdroji aktivity kopírování na **SqlDWSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost                     | Popis                                                  | Požaduje se |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | **Typ** musí být nastavena vlastnost zdroje aktivity kopírování **SqlDWSource**. | Ano      |
| sqlReaderQuery               | Použijte vlastní dotaz SQL číst data. Příklad: `select * from MyTable`. | Ne       |
| sqlReaderStoredProcedureName | Název uložené procedury, která čte data ze zdrojové tabulky. Příkaz SELECT v uložené proceduře musí být poslední příkaz jazyka SQL. | Ne       |
| storedProcedureParameters    | Parametry pro uloženou proceduru.<br/>Povolené hodnoty jsou páry název nebo hodnota. Názvy a použití malých a velkých parametry musí odpovídat názvům a použití malých a velkých parametrů uložené procedury. | Ne       |

**Příklad: použití dotazu SQL**

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

**Příklad: použití uložené procedury**

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

### <a name="azure-sql-data-warehouse-as-sink"></a>Azure synapse Analytics jako jímku

Azure Data Factory podporuje tři způsoby, jak načíst data do SQL Data Warehouse.

![Možnosti kopírování jímky SQL DW](./media/connector-azure-sql-data-warehouse/sql-dw-sink-copy-options.png)

- [Použít základ](#use-polybase-to-load-data-into-azure-sql-data-warehouse) 
- [Použít příkaz COPY (Preview)](#use-copy-statement)
- Použít hromadné vložení

Nejrychlejší a nejškálovatelný způsob, jak načíst data, je prostřednictvím [základny](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) nebo [příkazu copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (Preview).

Ke zkopírování dat do Azure SQL Data Warehouse, nastavte typ jímky v aktivitě kopírování do **SqlDWSink**. Následující vlastnosti jsou podporovány v aktivitě kopírování **jímky** části:

| Vlastnost          | Popis                                                  | Požaduje se                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | **Typ** vlastnost jímky aktivity kopírování musí být nastavena na **SqlDWSink**. | Ano                                           |
| allowPolyBase     | Označuje, zda se má k načtení dat do SQL Data Warehouse použít základ. `allowCopyCommand` a `allowPolyBase` nemohou být současně true. <br/><br/>Omezení a podrobnosti najdete v tématu [použití základu k načtení dat do Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) části.<br/><br/>Povolené hodnoty jsou **True** a **False** (výchozí). | Ne.<br/>Platí při použití základny.     |
| polyBaseSettings  | Skupina vlastností, které lze zadat, je-li vlastnost `allowPolybase` nastavena na **hodnotu true**. | Ne.<br/>Platí při použití základny. |
| allowCopyCommand | Označuje, zda se má při načítání dat do SQL Data Warehouse použít [příkaz Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (Preview). `allowCopyCommand` a `allowPolyBase` nemohou být současně true. <br/><br/>Omezení a podrobnosti najdete v tématu [použití příkazu copy k načtení dat do Azure SQL Data Warehouse](#use-copy-statement) části.<br/><br/>Povolené hodnoty jsou **True** a **False** (výchozí). | Ne.<br>Použijte při použití kopírování. |
| copyCommandSettings | Skupina vlastností, které lze zadat, je-li vlastnost `allowCopyCommand` nastavena na hodnotu TRUE. | Ne.<br/>Použijte při použití kopírování. |
| writeBatchSize    | Počet řádků, které mají být vloženy do tabulky SQL **na dávku**.<br/><br/>Je povolená hodnota **celé číslo** (počet řádků). Ve výchozím nastavení Data Factory dynamicky určí vhodnou velikost dávky na základě velikosti řádku. | Ne.<br/>Platí při použití hromadného vložení.     |
| writeBatchTimeout | Čekací doba pro dávkové operace insert dokončit před uplynutím časového limitu.<br/><br/>Je povolená hodnota **timespan**. Příklad: "00: 30:00" (30 minut). | Ne.<br/>Platí při použití hromadného vložení.        |
| preCopyScript     | Zadejte dotaz SQL pro aktivitu kopírování ke spuštění před zápisem dat do Azure SQL Data Warehouse při každém spuštění. Tuto vlastnost použijte k vyčištění dat předem. | Ne                                            |
| tableOption | Určuje, jestli se má automaticky vytvořit tabulka jímky, pokud na základě schématu zdroje neexistuje. Vytvoření automatické tabulky není podporováno, je-li v aktivitě kopírování konfigurována Příprava kopie. Povolené hodnoty jsou: `none` (výchozí), `autoCreate`. |Ne |
| disableMetricsCollection | Data Factory shromažďuje metriky, jako je například SQL Data Warehouse DWU, pro optimalizaci výkonu a doporučení pro kopírování. Pokud máte obavy s tímto chováním, zadejte `true` pro jeho vypnutí. | Ne (výchozí hodnota je `false`) |

#### <a name="sql-data-warehouse-sink-example"></a>Příklad jímku SQL Data Warehouse

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

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Použijte PolyBase k načítání dat do Azure SQL Data Warehouse

Použití [základny](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) je účinný způsob, jak načíst velké množství dat do Azure synapse Analytics s vysokou propustností. Zobrazí se vám velké zvýšení propustnosti pomocí PolyBase místo výchozího mechanismu hromadné vložení. Návod s případem použití najdete v tématu [načtení 1 TB do služby Azure synapse Analytics](v1/data-factory-load-sql-data-warehouse.md).

* Pokud jsou zdrojová data ve **službě Azure Blob, Azure Data Lake Storage Gen1 nebo Azure Data Lake Storage Gen2**a **formát je**standardně kompatibilní, můžete k přímému vyvolání základny použít aktivitu kopírování, která umožňuje Azure SQL Data Warehouse načíst data ze zdroje. Podrobnosti najdete v tématu  **[přímé kopírování pomocí PolyBase](#direct-copy-by-using-polybase)** .
* Pokud zdrojové úložiště dat a formát polybase původně nepodporuje, použijte **[fázovaného kopírování pomocí PolyBase](#staged-copy-by-using-polybase)** místo toho funkci. Funkce dvoufázové instalace kopírování také poskytuje vyšší propustnost. Automaticky převede data do formátu kompatibilního se standardem, uloží data do úložiště objektů BLOB v Azure. potom zavolá základnu, aby načetla data do SQL Data Warehouse.

>[!TIP]
>Přečtěte si další informace o [osvědčených postupech pro použití základny](#best-practices-for-using-polybase).

V části `polyBaseSettings` v aktivitě kopírování jsou podporovány následující základní nastavení:

| Vlastnost          | Popis                                                  | Požaduje se                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| rejectValue       | Určuje číslo nebo procentuální podíl řádků, které mohou být odmítnuty předtím, než se dotaz nezdaří.<br/><br/>Další informace o společnosti PolyBase možnosti odmítnutí v části argumenty [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). <br/><br/>Povolené hodnoty jsou 0 (výchozí), 1, 2, atd. | Ne                                            |
| rejectType        | Určuje, zda **rejectValue** možnost je hodnotu literálu nebo procenta.<br/><br/>Povolené hodnoty jsou **hodnotu** (výchozí) a **procento**. | Ne                                            |
| rejectSampleValue | Určuje počet řádků, načtěte před PolyBase přepočítá procento pozice zamítnutých řádků.<br/><br/>Povolené hodnoty jsou 1, 2, atd. | Ano, pokud **rejectType** je **procento**. |
| useTypeDefault    | Určuje způsob zpracování chybějící hodnoty v textových souborů s oddělovači, když PolyBase načte data z textového souboru.<br/><br/>Další informace o této vlastnosti v části argumenty [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Povolené hodnoty jsou **True** a **False** (výchozí).<br><br> | Ne                                            |

### <a name="direct-copy-by-using-polybase"></a>Kopírování s přímým přístupem pomocí PolyBase

SQL Data Warehouse základna podporuje přímo Azure Blob, Azure Data Lake Storage Gen1 a Azure Data Lake Storage Gen2. Pokud zdrojová data splňují kritéria popsaná v této části, použijte základnu pro kopírování přímo ze zdrojového úložiště dat do služby Azure synapse Analytics. Jinak použijte [fázovaného kopírování pomocí PolyBase](#staged-copy-by-using-polybase).

> [!TIP]
> Pokud chcete data efektivně kopírovat SQL Data Warehouse, přečtěte si další informace z [Azure Data Factory díky tomu je ještě snazší a pohodlnější získat poznatky z dat při použití Data Lake Store se SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Pokud požadavky nejsou splněny, Azure Data Factory zkontroluje nastavení a automaticky přejde zpět k hromadné vložení mechanismus pro přesun dat.

1. **Zdrojová propojená služba** je s následujícími typy a metodami ověřování:

    | Typ podporovaného zdrojového úložiště dat                             | Typ podporovaného ověřování zdroje                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Azure Blob](connector-azure-blob-storage.md)                | Ověřování klíčů účtu, ověřování spravované identity |
    | [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | Ověřování instančních objektů                            |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | Ověřování klíčů účtu, ověřování spravované identity |

    >[!IMPORTANT]
    >Pokud je vaše Azure Storage nakonfigurovaná pomocí koncového bodu služby virtuální sítě, musíte použít spravované ověřování identity – Přečtěte si, [Jaký je dopad použití koncových bodů služby virtuální sítě se službou Azure Storage](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Přečtěte si o požadovaných konfiguracích v Data Factory z oddílu [ověřování identity spravovaného pomocí objektů BLOB v Azure](connector-azure-blob-storage.md#managed-identity) a v části [ověřování identity spravované Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#managed-identity) .

2. **Zdrojový formát dat** je **Parquet**, **ORC**nebo **text s oddělovači**, s následujícími konfiguracemi:

   1. Cesta ke složce neobsahuje filtr zástupných znaků.
   2. Název souboru je prázdný nebo odkazuje na jeden soubor. Pokud v aktivitě kopírování zadáte název souboru se zástupnými znaky, může být pouze `*` nebo `*.*`.
   3. `rowDelimiter` je **Výchozí hodnota**, **\n**, **\r\n**nebo **\r**.
   4. `nullValue` je ponechán jako výchozí nebo nastaven na **prázdný řetězec** ("") a `treatEmptyAsNull` je ponechán jako výchozí nebo nastaven na hodnotu true.
   5. `encodingName` je ponechán jako výchozí nebo nastavený na **UTF-8**.
   6. nejsou zadány `quoteChar`, `escapeChar`a `skipLineCount`. Základní podpora – přeskočit řádek záhlaví, který se dá nakonfigurovat jako `firstRowAsHeader` v ADF.
   7. `compression` může být **bez komprese**, **GZip**, nebo **Deflate**.

3. Pokud je zdrojem složka, `recursive` v aktivitě kopírování musí být nastavená na hodnotu true.

4. nejsou zadány `wildcardFolderPath`, `wildcardFilename`, `modifiedDateTimeStart`a `modifiedDateTimeEnd`.

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

### <a name="staged-copy-by-using-polybase"></a>Fázované kopírovat pomocí funkce PolyBase

Pokud vaše zdrojová data nejsou nativně kompatibilní s základnu, povolte kopírování dat prostřednictvím dočasné pracovní instance služby Azure Blob Storage (nemůže to být Azure Premium Storage). V tomto případě Azure Data Factory automaticky převede data tak, aby splňovala požadavky na formát dat základu. Potom vyvolá základ k načtení dat do SQL Data Warehouse. Nakonec ho vyčistí dočasný data z úložiště objektů blob. Zobrazit [fázovaného kopírování](copy-activity-performance.md#staged-copy) podrobnosti o kopírování dat přes instanci pracovní úložiště objektů Blob v Azure.

Pokud chcete tuto funkci použít, vytvořte [propojenou službu azure BLOB Storage](connector-azure-blob-storage.md#linked-service-properties) , která odkazuje na účet služby Azure Storage s dočasným úložištěm objektů BLOB. Pak zadejte `enableStaging` a vlastnosti `stagingSettings` aktivity kopírování, jak je znázorněno v následujícím kódu.

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

### <a name="best-practices-for-using-polybase"></a>Doporučené postupy pro používání funkce PolyBase

Následující části obsahují osvědčené postupy kromě těch, které jsou uvedené v článku [osvědčené postupy pro Azure synapse Analytics](../sql-data-warehouse/sql-data-warehouse-best-practices.md).

#### <a name="required-database-permission"></a>Oprávnění databáze

Použití technologie PolyBase, musí mít uživatel, který načte data do SQL Data Warehouse [oprávnění "CONTROL"](https://msdn.microsoft.com/library/ms191291.aspx) na cílové databázi. Chcete-li přidat uživatele jako člena je jeden způsob, jak toho dosáhnout **db_owner** role. Zjistěte, jak to udělat [přehled SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

#### <a name="row-size-and-data-type-limits"></a>Velikost řádku a datový typ omezení

Načítání PolyBase jsou omezené na řádky, které jsou menší než 1 MB. Nedá se použít k načtení do VARCHR (MAX), NVARCHAR (MAX) nebo VARBINARY (MAX). Další informace najdete v tématu [limity kapacity služby SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Pokud zdroj dat má řádky větší než 1 MB, můžete chtít svisle rozdělit do několika malých ty zdrojové tabulky. Ujistěte se, že největší velikost každého řádku nepřekračuje limit. Menší tabulky se pak dají načíst pomocí základu a sloučí se dohromady v Azure synapse Analytics.

Alternativně můžete pro data s takovými roztažitelnémi sloupci použít nezaloženou základnu k načtení dat pomocí automatického podavače (ADF) vypnutím nastavení "zapnout základnu".

#### <a name="sql-data-warehouse-resource-class"></a>Třída prostředků SQL Data Warehouse

K dosažení nejlepší možné propustnost, přiřadíte větší třídu prostředků pro uživatele, který načte data do SQL Data Warehouse pomocí PolyBase.

#### <a name="polybase-troubleshooting"></a>Řešení potíží se základem

**Načítání do desetinného sloupce**

Pokud jsou vaše zdrojová data v textovém formátu nebo v jiných nekompatibilních úložištích, která nejsou kompatibilní s primárním úložištěm (pomocí připraveného kopírování a základu), a obsahuje prázdnou hodnotu, která se má načíst do SQL Data Warehouse desetinného sloupce, může se vyrazit následující chyba:

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

Řešením je zrušit výběr možnosti**použít výchozí typ**(jako false) ve jímky aktivity kopírování – > základní nastavení. "[USE_TYPE_DEFAULT](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)" je základní nativní konfigurace, která určuje, jak se mají zpracovat chybějící hodnoty v textových souborech s oddělovači, když základ dat načte data z textového souboru. 

**`tableName` ve službě Azure synapse Analytics**

V následující tabulce jsou uvedené příklady toho, jak zadat **tableName** vlastnost v datové sadě JSON. Ukazuje několik kombinace schéma a tabulku názvů.

| Schéma databáze | Název tabulky | **tableName** vlastnost JSON               |
| --------- | ---------- | ----------------------------------------- |
| dbo       | MyTable    | MyTable nebo dbo.MyTable nebo [dbo].[MyTable] |
| dbo1      | MyTable    | dbo1.MyTable [dbo1].[MyTable]          |
| dbo       | My.Table   | [My.Table] nebo [dbo].[My.Table]            |
| dbo1      | My.Table   | [dbo1].[My.Table]                         |

Pokud se zobrazí následující chyba, problém může být hodnota zadaná pro **tableName** vlastnost. V předchozí tabulce najdete správný způsob, jak určit hodnoty **tableName** vlastnost JSON.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

**Sloupce s výchozími hodnotami**

Funkce PolyBase ve službě Data Factory v současné době přijímá pouze stejný počet sloupců jako v cílové tabulce. Příkladem je tabulka s čtyři sloupce, kde jeden z nich je definované s výchozí hodnotou. Vstupní data stále musí mít čtyři sloupce. Vstupní datová sada třemi sloupci poskytuje zpráva podobná následující zpráva:

```
All columns of the table must be specified in the INSERT BULK statement.
```

Hodnota NULL je zvláštní forma výchozí hodnotu. Pokud je sloupec s možnou hodnotou Null, vstupní data v objektu blob pro tento sloupec může být prázdná. Ale nemůže být chybí vstupní datové sady. Základní znak pro chybějící hodnoty ve službě Azure synapse Analytics vloží hodnotu NULL.

## <a name="use-copy-statement"></a>Pomocí příkazu COPY načíst data do Azure SQL Data Warehouse (Preview)

[Příkaz COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) SQL Data Warehouse (Preview) přímo podporuje načítání dat z **Azure Blob a Azure Data Lake Storage Gen2**. Pokud zdrojová data splňují kritéria popsaná v této části, můžete použít příkaz COPY v ADF k načtení dat do Azure SQL Data Warehouse. Azure Data Factory kontroluje nastavení a v případě, že nejsou splněná kritéria, se spustí aktivita kopírování.

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
    >Pokud je vaše Azure Storage nakonfigurovaná pomocí koncového bodu služby virtuální sítě, musíte použít spravované ověřování identity – Přečtěte si, [Jaký je dopad použití koncových bodů služby virtuální sítě se službou Azure Storage](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Přečtěte si o požadovaných konfiguracích v Data Factory z oddílu [ověřování identity spravovaného pomocí objektů BLOB v Azure](connector-azure-blob-storage.md#managed-identity) a v části [ověřování identity spravované Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#managed-identity) .

2. Nastavení formátu jsou následující:

   1. Pro **Parquet**: `compression` nemůže být **žádná komprese**, **přichycení**nebo **gzip**.
   2. Pro **ORC**: `compression` nemůže být **žádná komprese**, **zlib**nebo **přichycení**.
   3. Pro **text s oddělovačem**:
      1. `rowDelimiter` je explicitně nastaven jako **jeden znak** nebo " **\r\n**", výchozí hodnota není podporována.
      2. `nullValue` je ponechán jako výchozí nebo nastaven na **prázdný řetězec** ("").
      3. `encodingName` je ponechán jako výchozí nebo nastaveno na **UTF-8 nebo UTF-16**.
      4. `escapeChar` musí být stejné jako `quoteChar`a není prázdné.
      5. `skipLineCount` je ponechán jako výchozí nebo nastavený na 0.
      6. `compression` nemůže být **žádná komprese** ani **gzip**.

3. Pokud je zdrojem složka, `recursive` v aktivitě kopírování musí být nastavená na hodnotu true.

4. nejsou zadány `wildcardFolderPath`, `wildcardFilename`, `modifiedDateTimeStart`a `modifiedDateTimeEnd`.

V části `allowCopyCommand` v aktivitě kopírování jsou podporovány následující nastavení příkazu COPY:

| Vlastnost          | Popis                                                  | Požaduje se                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| defaultValues – výchozí | Určuje výchozí hodnoty pro každý cílový sloupec v SQL DW.  Výchozí hodnoty ve vlastnosti přepíší výchozí sadu omezení v datovém skladu a sloupec identity nemůže mít výchozí hodnotu. | Ne |
| additionalOptions | Další možnosti, které budou předány příkazu kopírování SQL DW přímo v klauzuli WITH v [příkazu copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest). Zarovnejte hodnotu podle potřeby, aby odpovídala požadavkům příkazu COPY. | Ne |

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


## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Vlastnosti aktivity GetMetadata

Pokud se chcete dozvědět víc o vlastnostech, podívejte se na [aktivitu GetMetadata](control-flow-get-metadata-activity.md) . 

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Mapování datového typu pro službu Azure SQL Data Warehouse

## <a name="mapping-data-flow-properties"></a>Mapování vlastností toku dat

Při transformaci dat v toku mapování dat můžete číst a zapisovat do tabulek z Azure synapse Analytics. Další informace najdete v tématu transformace [zdroje](data-flow-source.md) a [transformace jímky](data-flow-sink.md) v tématu mapování toků dat.

### <a name="source-transformation"></a>Transformace zdroje

Nastavení specifická pro Azure synapse Analytics jsou k dispozici na kartě **zdrojové možnosti** zdrojové transformace. 

**Vstup:** Vyberte, zda chcete nasměrovat zdroj v tabulce (ekvivalent ```Select * from <table-name>```), nebo zadejte vlastní dotaz SQL.

**Dotaz**: Pokud ve vstupním poli vyberete možnost dotaz, zadejte pro zdroj dotaz SQL. Toto nastavení potlačí všechny tabulky, které jste vybrali v datové sadě. Klauzule **ORDER by** nejsou tady podporované, ale můžete nastavit úplný příkaz SELECT FROM. Můžete také použít uživatelsky definované funkce tabulky. **SELECT * FROM udfGetData ()** je UDF v SQL, který vrací tabulku. Tento dotaz vytvoří zdrojovou tabulku, kterou můžete použít v toku dat. Použití dotazů je také skvělým způsobem, jak omezit řádky pro testování nebo pro vyhledávání. 

* Příklad SQL: ```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Velikost dávky**: zadejte velikost dávky pro velké objemy dat v čtení.

**Úroveň izolace**: ve výchozím nastavení pro zdroje SQL v toku dat mapování je čtení nepotvrzené. Úroveň izolace můžete změnit tady na jednu z těchto hodnot:
* Čtení potvrzeno
* Čtení nepotvrzených
* Opakované čtení
* Serializovatelný
* Žádné (ignorovat úroveň izolace)

![Úroveň izolace](media/data-flow/isolationlevel.png "Úroveň izolace")

### <a name="sink-transformation"></a>Transformace jímky

Nastavení specifická pro Azure synapse Analytics jsou k dispozici na kartě **Nastavení** transformace jímky.

**Metoda aktualizace:** Určuje, jaké operace jsou pro cíl databáze povolené. Ve výchozím nastavení je povolen pouze vkládání. Chcete-li aktualizovat, Upsert nebo odstranit řádky, je pro označení řádků pro tyto akce nutná transformace ALTER Row. V případě aktualizací upsertuje a DELETE musí být klíčový sloupec nebo sloupce nastaveny k určení, který řádek má být změněn.

**Akce tabulky:** Určuje, zda mají být před zápisem znovu vytvořeny nebo odebrány všechny řádky z cílové tabulky.
* Žádné: v tabulce se neprovede žádná akce.
* Znovu vytvořit: tabulka se vynechá a znovu vytvoří. Požadováno při dynamickém vytváření nové tabulky.
* Zkrátit: všechny řádky z cílové tabulky se odeberou.

**Povolit přípravu:** Určuje, jestli se má při zápisu do Azure synapse Analytics použít [základ](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide?view=sql-server-ver15) .

**Velikost dávky**: Určuje, kolik řádků je v jednotlivých intervalech zapisováno. Větší velikosti dávek zlepšují kompresi a optimalizaci paměti, ale při ukládání dat do mezipaměti riskuje výjimky z paměti.

**Skripty před a po SQL**: zadejte víceřádkové skripty SQL, které se spustí před (před zpracováním) a po (po zpracování) se zapisují do databáze jímky.

![skripty pro zpracování před a po SQL](media/data-flow/prepost1.png "Skripty pro zpracování SQL")

## <a name="data-type-mapping-for-azure-synapse-analytics"></a>Mapování datových typů pro Azure synapse Analytics

Když kopírujete data z nebo do služby Azure synapse Analytics, používají se následující mapování z datových typů Azure synapse Analytics k Azure Data Factory dočasných datových typů. Zobrazit [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) se dozvíte, jak aktivitu kopírování, která mapuje typ zdroje schéma a data na jímce.

>[!TIP]
>V článku věnovaném [datovým typům tabulky v Azure synapse Analytics](../sql-data-warehouse/sql-data-warehouse-tables-data-types.md) najdete datové typy podporované SQL DW a alternativní řešení pro nepodporované.

| Datový typ Azure synapse Analytics    | Data Factory dočasné datový typ |
| :------------------------------------ | :----------------------------- |
| bigint                                | Int64                          |
| binary                                | Byte[]                         |
| bit                                   | Logická hodnota                        |
| char                                  | String, Char[]                 |
| date                                  | Datum a čas                       |
| Datetime                              | Datum a čas                       |
| datetime2                             | Datum a čas                       |
| Datetimeoffset                        | DateTimeOffset                 |
| Decimal                               | Decimal                        |
| Atribut FILESTREAM (varbinary(max)) | Byte[]                         |
| Float                                 | Double                         |
| image                                 | Byte[]                         |
| int                                   | Datový typ Int32                          |
| money                                 | Decimal                        |
| nchar                                 | String, Char[]                 |
| numeric                               | Decimal                        |
| nvarchar                              | String, Char[]                 |
| real                                  | Jednoduchá                         |
| rowversion                            | Byte[]                         |
| smalldatetime                         | Datum a čas                       |
| smallint                              | Int16                          |
| smallmoney                            | Decimal                        |
| time                                  | TimeSpan                       |
| tinyint                               | Bajtů                           |
| uniqueidentifier                      | Guid                           |
| Varbinary                             | Byte[]                         |
| varchar                               | String, Char[]                 |

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky, aktivita kopírování ve službě Azure Data Factory najdete v tématu [podporovaných úložišť dat a formáty](copy-activity-overview.md#supported-data-stores-and-formats).
