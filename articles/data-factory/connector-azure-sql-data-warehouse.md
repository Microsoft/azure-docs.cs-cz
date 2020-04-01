---
title: Kopírování a transformace dat v Azure Synapse Analytics
description: Zjistěte, jak kopírovat data do a z Azure Synapse Analytics a transformovat data v Azure Synapse Analytics pomocí Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/25/2020
ms.openlocfilehash: 822a981b84919670aa476567625cdf914206eaa8
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422187"
---
# <a name="copy-and-transform-data-in-azure-synapse-analytics-formerly-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Kopírování a transformace dat v Azure Synapse Analytics (dříve Azure SQL Data Warehouse) pomocí Azure Data Factory 

> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Aktuální verze](connector-azure-sql-data-warehouse.md)

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory kopírovat data z a do Azure Synapse Analytics a pomocí toku dat k transformaci dat v Azure Data Lake Storage Gen2. Další informace o Azure Data Factory najdete v [úvodním článku](introduction.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Azure Synapse Analytics je podporovaný pro následující aktivity:

- [Kopírovat aktivitu](copy-activity-overview.md) s [podporovanou tabulkou matice zdroje/jímky](copy-activity-overview.md)
- [Mapování toku dat](concepts-data-flow-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)
- [Aktivita getMetadata](control-flow-get-metadata-activity.md)

Pro aktivitu kopírování tento konektor Azure Synapse Analytics podporuje tyto funkce:

- Kopírování dat pomocí ověřování SQL a ověřování tokenů aplikace Azure Active Directory (Azure AD) pomocí instančního objektu nebo spravovaných identit pro prostředky Azure.
- Jako zdroj načtěte data pomocí dotazu SQL nebo uložené procedury.
- Jako jímka načtěte data pomocí [příkazu PolyBase](#use-polybase-to-load-data-into-azure-sql-data-warehouse) nebo [COPY](#use-copy-statement) (náhled) nebo hromadné ho vložení. Doporučujeme Příkaz PolyBase nebo COPY (náhled) pro lepší výkon kopírování.

> [!IMPORTANT]
> Pokud kopírujete data pomocí modulu Runtime integrace Azure Data Factory, nakonfigurujte [bránu firewall serveru Azure SQL,](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) aby měly služby Azure přístup k serveru.
> Pokud kopírujete data pomocí modulu runtime integrace s vlastním hostitelem, nakonfigurujte bránu firewall serveru Azure SQL, aby byla povolena příslušná oblast IP adres. Tento rozsah zahrnuje IP adresu počítače, která se používá k připojení k Azure Synapse Analytics.

## <a name="get-started"></a>Začínáme

> [!TIP]
> Chcete-li dosáhnout nejlepšího výkonu, použijte PolyBase k načtení dat do Azure Synapse Analytics. [Použití PolyBase k načtení dat do části Azure Synapse Analytics](#use-polybase-to-load-data-into-azure-sql-data-warehouse) obsahuje podrobnosti. Návod k případu použití najdete v [tématu Načtení 1 TB do Azure Synapse Analytics do 15 minut s Azure Data Factory](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které definují entity Datové továrny specifické pro konektor Azure Synapse Analytics.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu Azure Synapse Analytics jsou podporovány následující služby:

| Vlastnost            | Popis                                                  | Požaduje se                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| type                | Vlastnost type musí být nastavena na **AzureSqlDW**.             | Ano                                                          |
| připojovací řetězec    | Zadejte informace potřebné pro připojení k instanci Azure Synapse Analytics pro vlastnost **connectionString.** <br/>Označte toto pole jako securestring bezpečně ukládat v datové továrně. Můžete také umístit heslo/service principal key v Azure Key Vault `password` a pokud je ověřování SQL vytáhnout konfiguraci z připojovacího řetězce. Podívejte se na příklad JSON pod tabulkou a [přihlašovací údaje úložiště v](store-credentials-in-key-vault.md) článku Azure Key Vault s další podrobnosti. | Ano                                                          |
| servicePrincipalId  | Zadejte ID klienta aplikace.                         | Ano, při použití ověřování Azure AD s instančním objektem. |
| servicePrincipalKey | Zadejte klíč aplikace. Označte toto pole jako SecureString bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Ano, při použití ověřování Azure AD s instančním objektem. |
| Nájemce              | Zadejte informace o klientovi (název domény nebo ID klienta), pod kterým se aplikace nachází. Můžete načíst na jenom myši v pravém horním rohu portálu Azure. | Ano, při použití ověřování Azure AD s instančním objektem. |
| connectVia          | [Integrační runtime,](concepts-integration-runtime.md) který se má použít k připojení k úložišti dat. Můžete použít Azure Integration Runtime nebo runtime integrace s vlastním hostitelem (pokud vaše úložiště dat je umístěno v privátní síti). Pokud není zadán, používá výchozí Azure Integration Runtime. | Ne                                                           |

Pro různé typy ověřování naleznete v následujících částech na požadavky a Ukázky JSON, v uvedeném pořadí:

- [Ověřování pomocí SQL](#sql-authentication)
- Ověřování tokenů aplikace Azure AD: [Instanční objekt služby](#service-principal-authentication)
- Ověřování tokenů aplikace Azure AD: [Spravované identity pro prostředky Azure](#managed-identity)

>[!TIP]
>Pokud stisknete chybu s kódem chyby jako "UserErrorFailedToConnectToSqlServer" a zprávou jako `Pooling=false` "Limit relace pro databázi je XXX a bylo dosaženo.", přidejte do připojovacího řetězce a opakujte akci.

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

**Heslo v trezoru klíčů Azure:**

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

Pokud chcete použít ověřování tokenu aplikace Azure AD založené na primárním zabezpečení služby, postupujte takto:

1. **[Vytvořte aplikaci Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)** z webu Azure Portal. Poznamenejte si název aplikace a následující hodnoty, které definují propojenou službu:

    - ID aplikace
    - Klíč aplikace
    - ID tenanta

2. Pokud jste to ještě neudělali, **[zřídíte správce Služby Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** pro váš Server Azure SQL na webu Azure Portal. Správce Azure AD může být uživatel Azure AD nebo skupina Azure AD. Pokud skupině se spravovanou identitou udělíte roli správce, přeskočte kroky 3 a 4. Správce bude mít úplný přístup k databázi.

3. **[Vytvořte obsažené uživatele databáze](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** pro instanční objekt. Připojte se k datovému skladu, ze kterého nebo do kterého chcete kopírovat data pomocí nástrojů, jako je SSMS, s identitou Azure AD, která má alespoň alter oprávnění LIBOVOLNÉHO UŽIVATELE. Spusťte následující T-SQL:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Udělte zaregistrovaný objekt služby potřebná oprávnění** jako obvykle pro uživatele SQL nebo jiné uživatele. Spusťte následující kód nebo se podívejte na další možnosti [zde](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Pokud chcete použít PolyBase k načtení dat, naučte [se požadované oprávnění databáze](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. **Konfigurace propojené služby Azure Synapse Analytics** v Azure Data Factory.


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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Spravované identity pro ověřování prostředků Azure

Továrna dat může být přidružena ke [spravované identitě pro prostředky Azure,](data-factory-service-identity.md) která představuje konkrétní továrnu. Tuto spravovanou identitu můžete použít pro ověřování Azure Synapse Analytics. Určená továrna může přistupovat k datům z nebo do datového skladu a kopírovat do nich pomocí této identity.

Chcete-li použít ověřování spravované identity, postupujte takto:

1. Pokud jste to ještě neudělali, **[zřídíte správce Služby Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** pro váš Server Azure SQL na webu Azure Portal. Správce Azure AD může být uživatel Azure AD nebo skupina Azure AD. Pokud skupině se spravovanou identitou udělíte roli správce, přeskočte kroky 3 a 4. Správce bude mít úplný přístup k databázi.

2. **[Vytvořte obsažené uživatele databáze](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** pro identitu spravovanou datovou továrnou. Připojte se k datovému skladu, ze kterého nebo do kterého chcete kopírovat data pomocí nástrojů, jako je SSMS, s identitou Azure AD, která má alespoň alter oprávnění LIBOVOLNÉHO UŽIVATELE. Spusťte následující T-SQL. 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Udělte datová továrna spravovaná identita potřebná oprávnění** jako obvykle pro uživatele SQL a ostatní. Spusťte následující kód nebo se podívejte na další možnosti [zde](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Pokud chcete použít PolyBase k načtení dat, naučte [se požadované oprávnění databáze](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

5. **Konfigurace propojené služby Azure Synapse Analytics** v Azure Data Factory.

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [Datové sady.](concepts-datasets-linked-services.md) 

Pro datovou sadu Azure Synapse Analytics jsou podporovány následující vlastnosti:

| Vlastnost  | Popis                                                  | Požaduje se                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | Vlastnost **type** datové sady musí být nastavena na **AzureSqlDWTable**. | Ano                         |
| Schématu | Název schématu. |Ne pro zdroj, Ano pro umyvadlo  |
| tabulka | Název tabulky/zobrazení. |Ne pro zdroj, Ano pro umyvadlo  |
| tableName | Název tabulky/zobrazení se schématem. Tato vlastnost je podporována pro zpětnou kompatibilitu. Pro nové pracovní `schema` `table`vytížení, použití a . | Ne pro zdroj, Ano pro umyvadlo |

#### <a name="dataset-properties-example"></a>Příklad vlastností datové sady

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

## <a name="copy-activity-properties"></a>Kopírovat vlastnosti aktivity

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných zdrojem a jímkou Azure Synapse Analytics.

### <a name="azure-synapse-analytics-as-the-source"></a>Azure Synapse Analytics jako zdroj

Chcete-li zkopírovat data z Azure Synapse Analytics, nastavte vlastnost **type** ve zdroji Kopírovat aktivitu na **SqlDWSource**. V části Zdroj **kopírování aktivity** jsou podporovány následující vlastnosti:

| Vlastnost                     | Popis                                                  | Požaduje se |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | Vlastnost **type** zdroje Aktivity kopírování musí být nastavena na **sqldwsource**. | Ano      |
| sqlReaderQuery               | Ke čtení dat použijte vlastní dotaz SQL. Příklad: `select * from MyTable`. | Ne       |
| sqlReaderStoredProcedureName | Název uložené procedury, která čte data ze zdrojové tabulky. Poslední příkaz SQL musí být příkaz SELECT v uložené proceduře. | Ne       |
| storedProcedureParameters    | Parametry pro uloženou proceduru.<br/>Povolené hodnoty jsou dvojice názvů nebo hodnot. Názvy a písmena parametrů se musí shodovat s názvy a písmeny parametrů uložené procedury. | Ne       |
| Isolationlevel | Určuje chování uzamčení transakce pro zdroj SQL. Povolené hodnoty jsou: **ReadCommitted** (výchozí), **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snímek**. Další podrobnosti naleznete v [tomto dokumentu.](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) | Ne |

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

**Vzorek uložená procedura:**

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

### <a name="azure-synapse-analytics-as-sink"></a><a name="azure-sql-data-warehouse-as-sink"></a>Azure Synapse Analytics jako umyvadlo

Azure Data Factory podporuje tři způsoby načítání dat do datového skladu SQL.

![Možnosti kopírování jímky SQL DW](./media/connector-azure-sql-data-warehouse/sql-dw-sink-copy-options.png)

- [Použít polybase](#use-polybase-to-load-data-into-azure-sql-data-warehouse) 
- [Použít příkaz COPY (náhled)](#use-copy-statement)
- Použití hromadné vložky

Nejrychlejší a nejškálovatelnější způsob, jak načíst data je prostřednictvím [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) nebo [COPY prohlášení](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (náhled).

Chcete-li zkopírovat data do datového skladu Azure SQL, nastavte typ jímky v okně Kopírovat aktivitu do **aplikace SqlDWSink**. V části **Jímka** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost          | Popis                                                  | Požaduje se                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | Vlastnost **type** jímky aktivity kopírování musí být nastavena na **sqldwsink**. | Ano                                           |
| allowPolyBase     | Označuje, zda se má použít PolyBase k načtení dat do datového skladu SQL. `allowCopyCommand`a `allowPolyBase` nemůže být obojí pravdivé. <br/><br/>Najdete [v článku Použití PolyBase k načtení dat do](#use-polybase-to-load-data-into-azure-sql-data-warehouse) oddílu Azure SQL Data Warehouse pro omezení a podrobnosti.<br/><br/>Povolené hodnoty jsou **True** a **False** (výchozí). | Ne.<br/>Použít při použití PolyBase.     |
| polyBaseSettings  | Skupina vlastností, které lze `allowPolybase` zadat, pokud je vlastnost nastavena na **hodnotu true**. | Ne.<br/>Použít při použití PolyBase. |
| allowCopyCommand | Označuje, zda se má použít [příkaz COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (preview) k načtení dat do datového skladu SQL. `allowCopyCommand`a `allowPolyBase` nemůže být obojí pravdivé. <br/><br/>V článku [Použití příkazu COPY k načtení dat do](#use-copy-statement) oddílu Azure SQL Data Warehouse pro omezení a podrobnosti.<br/><br/>Povolené hodnoty jsou **True** a **False** (výchozí). | Ne.<br>Použít při použití COPY. |
| copyCommandSettings | Skupina vlastností, které lze `allowCopyCommand` zadat, když je vlastnost nastavena na hodnotu TRUE. | Ne.<br/>Použít při použití COPY. |
| writeBatchSize    | Počet řádků, které mají být vloženy do tabulky SQL **na dávku**.<br/><br/>Povolená hodnota je **celé číslo** (počet řádků). Ve výchozím nastavení Data Factory dynamicky určuje příslušnou velikost dávky na základě velikosti řádku. | Ne.<br/>Použít při použití hromadné vložky.     |
| writeBatchTimeout | Počkejte čas, než bude operace dávkového vložení dokončena, než bude časový režim ukončen.<br/><br/>Povolená hodnota je **timespan**. Příklad: "00:30:00" (30 minut). | Ne.<br/>Použít při použití hromadné vložky.        |
| preCopyScript     | Zadejte dotaz SQL pro kopírování aktivity spustit před zápisem dat do Datového skladu Azure SQL v každém spuštění. Pomocí této vlastnosti vyčistit přednačtená data. | Ne                                            |
| tableOption | Určuje, zda se má automaticky vytvořit tabulka jímky, pokud neexistuje na základě zdrojového schématu. Automatické vytváření tabulek není podporováno, pokud je fázovaná kopie nakonfigurována v aktivitě kopírování. Povolené hodnoty `none` jsou: `autoCreate`(výchozí), . |Ne |
| disableMetricsCollection | Data Factory shromažďuje metriky, jako je NAPŘÍKLAD SQL Data Warehouse DWU pro optimalizaci výkonu kopírování a doporučení. Pokud se obáváte tohoto `true` chování, zadejte jej vypnout. | Ne (výchozí `false`hodnota je ) |

#### <a name="sql-data-warehouse-sink-example"></a>Příklad jímky datového skladu SQL

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

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Použití PolyBase k načtení dat do datového skladu Azure SQL

Použití [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) je efektivní způsob, jak načíst velké množství dat do Azure Synapse Analytics s vysokou propustností. Uvidíte velký zisk v propustnosti pomocí PolyBase namísto výchozího mechanismu BULKINSERT. Návod k případu použití najdete v tématu [načtení 1 TB do Azure Synapse Analytics](v1/data-factory-load-sql-data-warehouse.md).

* Pokud jsou vaše zdrojová data v **objektech Blob Azure, Azure Data Lake Storage Gen1 nebo Azure Data Lake Storage Gen2**a **formát je kompatibilní s PolyBase**, můžete použít aktivitu kopírování k přímému vyvolání PolyBase a nechat Azure SQL Data Warehouse vyžádat data ze zdroje. Podrobnosti naleznete **[v tématu Přímá kopie pomocí PolyBase](#direct-copy-by-using-polybase)**.
* Pokud úložiště a formát zdrojových dat není původně podporováno službou PolyBase, použijte místo toho **[fázovanou kopii pomocí funkce PolyBase.](#staged-copy-by-using-polybase)** Funkce fázované kopie také poskytuje lepší propustnost. Automaticky převede data do formátu kompatibilního s PolyBase, uloží data v úložišti objektů Blob Azure.

>[!TIP]
>Další informace o [doporučených postupech pro používání služby PolyBase](#best-practices-for-using-polybase).

V části Aktivita kopírování `polyBaseSettings` jsou podporována následující nastavení PolyBase:

| Vlastnost          | Popis                                                  | Požaduje se                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| rejectValue       | Určuje počet nebo procento řádků, které lze odmítnout před selháním dotazu.<br/><br/>Další informace o možnostech odmítnutí polyBase naleznete v části Argumenty v části [CREATE EXTERNAL TABLE (Transact-SQL).](https://msdn.microsoft.com/library/dn935021.aspx) <br/><br/>Povolené hodnoty jsou 0 (výchozí), 1, 2 atd. | Ne                                            |
| rejectType        | Určuje, zda je možnost **rejectValue** hodnotou literálu nebo procentem.<br/><br/>Povolené hodnoty jsou **Hodnota** (výchozí) a **Procento**. | Ne                                            |
| rejectSampleValue | Určuje počet řádků, které mají být načteny před přepočtem PolyBase procento odmítnutých řádků.<br/><br/>Povolené hodnoty jsou 1, 2 atd. | Ano, pokud **rejectType** je **procento**. |
| useTypeDefault    | Určuje, jak zpracovat chybějící hodnoty v oddělených textových souborech, když PolyBase načte data z textového souboru.<br/><br/>Další informace o této vlastnosti naleznete v části Argumenty v [části CREATE EXTERNAL FILE FORMAT (Transact-SQL).](https://msdn.microsoft.com/library/dn935026.aspx)<br/><br/>Povolené hodnoty jsou **True** a **False** (výchozí).<br><br> | Ne                                            |

### <a name="direct-copy-by-using-polybase"></a>Přímá kopie pomocí PolyBase

SQL Data Warehouse PolyBase přímo podporuje Azure Blob, Azure Data Lake Storage Gen1 a Azure Data Lake Storage Gen2. Pokud vaše zdrojová data splňují kritéria popsaná v této části, použijte PolyBase ke kopírování přímo ze zdrojového úložiště dat do Azure Synapse Analytics. V opačném případě použijte [fázovanou kopii pomocí polybase](#staged-copy-by-using-polybase).

> [!TIP]
> Chcete-li efektivně kopírovat data do SQL Data Warehouse, další informace z [Azure Data Factory je ještě jednodušší a pohodlnější odhalit poznatky z dat při použití Data Lake Store s SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Pokud nejsou splněny požadavky, Azure Data Factory zkontroluje nastavení a automaticky přejde zpět do bulkinsert mechanismus pro přesun dat.

1. **Zdrojová propojená služba** je s následujícími typy a metodami ověřování:

    | Podporovaný typ úložiště zdrojových dat                             | Podporovaný typ ověřování zdroje                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Azure Blob](connector-azure-blob-storage.md)                | Ověřování pomocí klíče účtu, ověřování spravované identity |
    | [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | Ověřování instančních objektů                            |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | Ověřování pomocí klíče účtu, ověřování spravované identity |

    >[!IMPORTANT]
    >Pokud je vaše Azure Storage nakonfigurovaná s koncovým bodem služby Virtuální sítě, musíte použít spravované ověřování identity – přečtěte si [odkaz dopad používání koncových bodů služby Virtuální sítě s úložištěm Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Seznamte se s požadovanými konfiguracemi v Datové továrně z [Azure Blob – spravované ověřování identity](connector-azure-blob-storage.md#managed-identity) a Azure Data Lake Storage Gen2 – část [ověřování identity spravované.](connector-azure-data-lake-storage.md#managed-identity)

2. **Zdrojový formát dat** je z **parket**, **ORC**nebo **oddělovače textu**, s následujícími konfiguracemi:

   1. Cesta ke složce neobsahuje filtr se zástupnými symboly.
   2. Název souboru je prázdný nebo odkazuje na jeden soubor. Pokud zadáte název souboru se zástupnými symboly v aktivitě kopírování, může být `*` pouze nebo `*.*`.
   3. `rowDelimiter`je **výchozí ,** **\n**, **\r\n**nebo **\r**.
   4. `nullValue`je ponecháno jako výchozí nebo nastaveno `treatEmptyAsNull` na prázdný **řetězec** (""), a je ponecháno jako výchozí nebo nastaveno na hodnotu true.
   5. `encodingName`je ponecháno jako výchozí nebo nastaveno na **utf-8**.
   6. `quoteChar`, `escapeChar`a `skipLineCount` nejsou zadány. Podpora podpory PolyBase přeskakuje `firstRowAsHeader` řádek záhlaví, který lze konfigurovat jako v adf.
   7. `compression`nemůže být **žádná komprese**, **GZip**nebo **Deflate**.

3. Pokud je zdrojem `recursive` složka, musí být aktivita kopírování nastavena na hodnotu true.

4. `wildcardFolderPath`, `wildcardFilename` `modifiedDateTimeStart`, `modifiedDateTimeEnd` `additionalColumns` , a nejsou zadány.

>[!NOTE]
>Pokud je zdrojem složka, poznámka PolyBase načte soubory ze složky a všech jejích podsložek a nenačte data ze souborů, pro které název souboru začíná podtržením (_) nebo tečkou (.), jak je popsáno [zde - ARGUMENT UMÍSTĚNÍ](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest#arguments-2).

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

### <a name="staged-copy-by-using-polybase"></a>Fázovaná kopie pomocí PolyBase

Pokud vaše zdrojová data nejsou nativně kompatibilní s PolyBase, povolte kopírování dat prostřednictvím dočasné pracovní instance úložiště objektů blob Azure (nemůže to být Azure Premium Storage). V takovém případě Azure Data Factory automaticky převede data tak, aby splňovaly požadavky na formát dat PolyBase. Pak vyvolá PolyBase načíst data do datového skladu SQL. Nakonec vyčistí vaše dočasná data z úložiště objektů blob. Podrobnosti o kopírování dat prostřednictvím instancí instancí úložiště objektů Blob Azure najdete v tématu [Staged copy.](copy-activity-performance-features.md#staged-copy)

Chcete-li tuto funkci použít, vytvořte [propojenou službu Azure Blob Storage,](connector-azure-blob-storage.md#linked-service-properties) která odkazuje na účet úložiště Azure s dočasným úložištěm objektů blob. Pak `enableStaging` zadejte `stagingSettings` vlastnosti a aktivity kopírování, jak je znázorněno v následujícím kódu.

>[!IMPORTANT]
>Pokud je vaše pracovní Azure Storage nakonfigurovaná s koncovým bodem služby Virtuální sítě, musíte použít spravované ověřování identity – přečtěte si [odkaz dopad používání koncových bodů služby Virtuální sítě s úložištěm Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Seznamte se s požadovanými konfiguracemi v Datové továrně z [Azure Blob – ověřování spravované identity](connector-azure-blob-storage.md#managed-identity).

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

### <a name="best-practices-for-using-polybase"></a>Doporučené postupy pro používání PolyBase

Následující části obsahují kromě těch, které jsou uvedeny v [doporučených postupech pro Azure Synapse Analytics ,](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-best-practices.md)také doporučené postupy.

#### <a name="required-database-permission"></a>Požadované oprávnění k databázi

Chcete-li použít PolyBase, uživatel, který načte data do datového skladu SQL musí mít [oprávnění "CONTROL"](https://msdn.microsoft.com/library/ms191291.aspx) v cílové databázi. Jedním ze způsobů, jak toho dosáhnout, je přidat uživatele jako člena **db_owner** role. Jak to udělat v [přehledu datového skladu SQL](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

#### <a name="row-size-and-data-type-limits"></a>Omezení velikosti řádků a datového typu

Zatížení PolyBase jsou omezena na řádky menší než 1 MB. Nelze jej použít k načtení do VARCHR(MAX), NVARCHAR(MAX) nebo VARBINARY(MAX). Další informace naleznete v tématu [omezení kapacity služby SQL Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Pokud zdrojová data obsahují řádky větší než 1 MB, můžete chtít zdrojové tabulky svisle rozdělit na několik malých. Ujistěte se, že největší velikost každého řádku nepřekračuje limit. Menší tabulky pak lze načíst pomocí PolyBase a sloučeny v Azure Synapse Analytics.

Případně pro data s tak širokými sloupci můžete použít non-PolyBase k načtení dat pomocí adf, vypnutím nastavení "povolit PolyBase".

#### <a name="sql-data-warehouse-resource-class"></a>Třída prostředků datového skladu SQL

Chcete-li dosáhnout nejlepší možné propustnost, přiřaďte větší třídu prostředků uživateli, který načte data do datového skladu SQL prostřednictvím PolyBase.

#### <a name="polybase-troubleshooting"></a>Řešení potíží s polybasem

**Načítání do sloupce Desetinné číslo**

Pokud jsou zdrojová data v textovém formátu nebo jiných úložištích kompatibilních s polybase (pomocí fázované kopie a PolyBase) a obsahuje prázdnou hodnotu, která se má načíst do desítkové kolony datového skladu SQL, můžete chybovat následující chybu:

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

Řešením je zrušit výběr možnosti **"Použít typ výchozí**" (jako false) v nastavení prohloubení aktivity kopírování -> PolyBase. "[USE_TYPE_DEFAULT](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)" je nativní konfigurace PolyBase, která určuje, jak zacházet s chybějícími hodnotami v oddělených textových souborech, když PolyBase načítá data z textového souboru. 

**`tableName`v Azure Synapse Analytics**

V následující tabulce jsou uvedeny příklady, jak zadat vlastnost **tableName** v datové sadě JSON. Zobrazuje několik kombinací schématu a názvů tabulek.

| Schéma DB | Název tabulky | **název_tabulky** JSON, vlastnost               |
| --------- | ---------- | ----------------------------------------- |
| Dbo       | Mytable    | MyTable nebo dbo. MyTable nebo [dbo]. [MyTable] |
| dbo1      | Mytable    | dbo1. MyTable nebo [dbo1]. [MyTable]          |
| Dbo       | My.Table   | [My.Table] nebo [dbo]. [My.Table]            |
| dbo1      | My.Table   | [dbo1]. [My.Table]                         |

Pokud se zobrazí následující chyba, problém může být hodnota, kterou jste zadali pro **vlastnost tableName.** Správný způsob zadání hodnot pro vlastnost **tableName** JSON naleznete v předchozí tabulce.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

**Sloupce s výchozími hodnotami**

V současné době funkce PolyBase v továrně dat přijímá pouze stejný počet sloupců jako v cílové tabulce. Příkladem je tabulka se čtyřmi sloupci, kde je jeden z nich definován s výchozí hodnotou. Vstupní data stále musí mít čtyři sloupce. Vstupní datová sada se třemi sloupci poskytuje chybu podobnou následující zprávě:

```
All columns of the table must be specified in the INSERT BULK statement.
```

Hodnota NULL je zvláštní forma výchozí hodnoty. Pokud je sloupec nullable, vstupní data v objektu blob pro tento sloupec může být prázdný. Ale nemůže chybět ze vstupní datové sady. PolyBase vloží hodnotu NULL pro chybějící hodnoty v Azure Synapse Analytics.

## <a name="use-copy-statement-to-load-data-into-azure-sql-data-warehouse-preview"></a><a name="use-copy-statement"></a>Načtení dat do Datového skladu Azure SQL (preview) pomocí příkazu COPY

Příkaz SQL Data Warehouse [COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (preview) přímo podporuje načítání dat z **objektů Blob Azure a Azure Data Lake Storage Gen2**. Pokud vaše zdrojová data splňují kritéria popsaná v této části, můžete použít příkaz COPY v ADF k načtení dat do Azure SQL Data Warehouse. Azure Data Factory zkontroluje nastavení a selže spuštění aktivity kopírování, pokud není splněna kritéria.

>[!NOTE]
>V současné době Data Factory podporuje pouze kopii z copy prohlášení kompatibilní zdroje uvedené níže.

Příkaz Using COPY podporuje následující konfiguraci:

1. **Zdrojová propojená služba a formát** jsou s následujícími typy a metodami ověřování:

    | Podporovaný typ úložiště zdrojových dat                             | Podporovaný formát           | Podporovaný typ ověřování zdroje                         |
    | :----------------------------------------------------------- | -------------------------- | :----------------------------------------------------------- |
    | [Azure Blob](connector-azure-blob-storage.md)                | [Oddělovač textu](format-delimited-text.md)             | Ověřování pomocí klíče účtu, ověřování podpisů se sdíleným přístupem, ověřování instančního objektu, ověřování spravované identity |
    | &nbsp;                                                       | [Parketové](format-parquet.md)                    | Ověřování pomocí klíče účtu, ověřování podpisu sdíleného přístupu |
    | &nbsp;                                                       | [Orc](format-orc.md)                        | Ověřování pomocí klíče účtu, ověřování podpisu sdíleného přístupu |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | [Oddělovač textu](format-delimited-text.md)<br/>[Parketové](format-parquet.md)<br/>[Orc](format-orc.md) | Ověřování pomocí klíče účtu, ověřování na objektu zabezpečení služby, ověřování spravované identity |

    >[!IMPORTANT]
    >Pokud je vaše Azure Storage nakonfigurovaná s koncovým bodem služby Virtuální sítě, musíte použít spravované ověřování identity – přečtěte si [odkaz dopad používání koncových bodů služby Virtuální sítě s úložištěm Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Seznamte se s požadovanými konfiguracemi v Datové továrně z [Azure Blob – spravované ověřování identity](connector-azure-blob-storage.md#managed-identity) a Azure Data Lake Storage Gen2 – část [ověřování identity spravované.](connector-azure-data-lake-storage.md#managed-identity)

2. Nastavení formátu jsou následující:

   1. Pro **parkety** `compression` : nemůže být **komprese**, **Snappy**, nebo **GZip**.
   2. Pro **ORC** `compression` : může být žádná **komprese**, **```zlib```** nebo **Snappy**.
   3. Pro **oddělované znění**:
      1. `rowDelimiter`je explicitně nastavena jako **jeden znak** nebo **"\r\n**", výchozí hodnota není podporována.
      2. `nullValue`je ponecháno jako výchozí nebo nastaveno na **prázdný řetězec** ("").
      3. `encodingName`je ponecháno jako výchozí nebo nastaveno na **utf-8 nebo utf-16**.
      4. `escapeChar`musí být `quoteChar`stejný jako , a není prázdný.
      5. `skipLineCount`je ponecháno jako výchozí nebo nastaveno na hodnotu 0.
      6. `compression`nemůže být **komprese** nebo **GZip**.

3. Pokud je zdrojem `recursive` složka, musí být aktivita kopírování nastavena na hodnotu true.

4. `wildcardFolderPath`, `wildcardFilename` `modifiedDateTimeStart`, `modifiedDateTimeEnd` `additionalColumns` , a nejsou zadány.

V části `allowCopyCommand` aktivita kopírování jsou podporována následující nastavení příkazu COPY:

| Vlastnost          | Popis                                                  | Požaduje se                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| výchozí hodnoty | Určuje výchozí hodnoty pro každý cílový sloupec v SQL DW.  Výchozí hodnoty ve vlastnosti přepíší výchozí omezení nastavené v datovém skladu a sloupec identity nemůže mít výchozí hodnotu. | Ne |
| additionalOptions | Další možnosti, které budou předány příkazu SQL DW COPY přímo v klauzuli "With" v [příkazu COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest). Uveďte hodnotu podle potřeby zarovnat s požadavky příkazu COPY. | Ne |

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


## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Vlastnosti aktivity GetMetadata

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Mapování datových typů pro Datový sklad Azure SQL

## <a name="mapping-data-flow-properties"></a>Mapování vlastností toku dat

Při transformaci dat v toku dat mapování můžete číst a zapisovat do tabulek z Azure Synapse Analytics. Další informace naleznete v [transformaci zdroje](data-flow-source.md) a [jímce transformace](data-flow-sink.md) v mapování datových toků.

### <a name="source-transformation"></a>Transformace zdroje

Nastavení specifická pro Azure Synapse Analytics jsou k dispozici na kartě **Možnosti zdroje** v transformaci zdroje. 

**Vstup:** Vyberte, zda navedete zdroj ```Select * from <table-name>```na tabulku (ekvivalentní) nebo zadáte vlastní dotaz SQL.

**Dotaz**: Pokud ve vstupním poli vyberete dotaz, zadejte pro zdroj dotaz SQL. Toto nastavení přepíše všechny tabulky, které jste zvolili v datové sadě. **Klauzule Order By** zde nejsou podporovány, ale můžete nastavit úplný příkaz SELECT FROM. Můžete také použít uživatelem definované funkce tabulky. **select * z udfGetData()** je UDF v SQL, který vrací tabulku. Tento dotaz vytvoří zdrojovou tabulku, kterou můžete použít v toku dat. Použití dotazů je také skvělý způsob, jak snížit řádky pro testování nebo pro vyhledávání. 

* Příklad SQL:```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Velikost dávky**: Zadejte velikost dávky pro velké datové bloky do čtení. V datových tocích použije ADF toto nastavení k nastavení sloupcového ukládání do mezipaměti spark. Toto je pole možností, které bude používat výchozí hodnoty Spark, pokud zůstane prázdné.

**Úroveň izolace**: Výchozí hodnota pro zdroje SQL v toku dat mapování je přečtena nezávazně. Zde můžete změnit úroveň izolace na jednu z těchto hodnot:
* Přečtěte si potvrzené
* Číst nesvěřené
* Opakovatelné čtení
* Serializovatelný
* Žádné (ignorovat úroveň izolace)

![Úroveň izolace](media/data-flow/isolationlevel.png "Úroveň izolace")

### <a name="sink-transformation"></a>Transformace jímky

Nastavení specifická pro Azure Synapse Analytics jsou k dispozici na kartě **Nastavení** transformace jímky.

**Metoda aktualizace:** Určuje, jaké operace jsou povoleny v cílové masce databáze. Ve výchozím nastavení je povolit pouze vložení. Chcete-li aktualizovat, upsert nebo odstranit řádky, je nutné změnit řádek transformace označit řádky pro tyto akce. Pro aktualizace, upserts a odstraní, klíčový sloupec nebo sloupce musí být nastavena k určení, který řádek změnit.

**Akce tabulky:** Určuje, zda chcete znovu vytvořit nebo odebrat všechny řádky z cílové tabulky před zápisem.
* Žádné: V tabulce nebude provedena žádná akce.
* Znovu: Tabulka bude vynechána a znovu vytvořena. Povinné při dynamickém vytváření nové tabulky.
* Zkrátit: Všechny řádky z cílové tabulky budou odebrány.

**Povolit pracovní:** Určuje, zda se má používat [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide?view=sql-server-ver15) při zápisu do Azure Synapse Analytics.

**Velikost dávky**: Určuje, kolik řádků se zapisuje v každém bloku. Větší dávkové velikosti zlepšují optimalizaci komprese a paměti, ale při ukládání dat do mezipaměti riskují výjimky z paměti.

**Skripty před a po SQL:** Zadejte víceřádkové skripty SQL, které budou spuštěny před zápisem (předběžného zpracování) a po zápisu dat (následného zpracování) do databáze Dřezu.

![skripty pro zpracování SQL před a po](media/data-flow/prepost1.png "Skripty pro zpracování SQL")

## <a name="data-type-mapping-for-azure-synapse-analytics"></a>Mapování datových typů pro Azure Synapse Analytics

Když kopírujete data z Azure Synapse Analytics nebo do ní, použijí se z datových typů Azure Synapse Analytics následující mapování do dočasných datových typů Azure Data Factory. Podívejte se na schéma a mapování datových typů se [dozvíte,](copy-activity-schema-and-type-mapping.md) jak aktivita kopírování mapuje zdrojové schéma a datový typ do jímky.

>[!TIP]
>Podívejte se na [datové typy tabulky v článku Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-data-types.md) o datových typech podporovaných SQL DW a o zástupných řešeních pro nepodporované datové typy.

| Datový typ Azure Synapse Analytics    | Dočasný datový typ datové továrny |
| :------------------------------------ | :----------------------------- |
| bigint                                | Int64                          |
| binární                                | Bajt[]                         |
| bitové                                   | Logická hodnota                        |
| char                                  | Řetězec, Znak[]                 |
| date                                  | DateTime                       |
| Datum a čas                              | DateTime                       |
| datetime2                             | DateTime                       |
| Datetimeoffset                        | DateTimeOffset                 |
| Desetinné číslo                               | Desetinné číslo                        |
| ATRIBUT FILESTREAM (varbinary(max)) | Bajt[]                         |
| Plovoucí desetinná čárka                                 | Double                         |
| image                                 | Bajt[]                         |
| int                                   | Int32                          |
| Peníze                                 | Desetinné číslo                        |
| Nchar                                 | Řetězec, Znak[]                 |
| numerické                               | Desetinné číslo                        |
| nvarchar                              | Řetězec, Znak[]                 |
| reálná                                  | Single                         |
| Rowversion                            | Bajt[]                         |
| Smalldatetime                         | DateTime                       |
| smallint                              | Int16                          |
| Smallmoney                            | Desetinné číslo                        |
| time                                  | TimeSpan                       |
| tinyint                               | Byte                           |
| uniqueidentifier                      | Identifikátor GUID                           |
| Varbinary                             | Bajt[]                         |
| varchar                               | Řetězec, Znak[]                 |

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a propady podle aktivity kopírování v Azure Data Factory najdete v [tématu podporovaná úložiště dat a formáty](copy-activity-overview.md#supported-data-stores-and-formats).
