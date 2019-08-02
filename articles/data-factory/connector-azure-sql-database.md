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
ms.openlocfilehash: 5dbd739070b1f66fe5ff04f319a3818269d0bdaa
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449611"
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>Kopírování dat do nebo ze služby Azure SQL Database s použitím služby Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Azure Data Factory, který používáte:"]
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

- Kopírování dat pomocí ověřování SQL a ověřování pomocí tokenu aplikace Azure Active Directory (Azure AD) identity objektu zabezpečení nebo spravované služby pro prostředky Azure.
- Jako zdroj načítání dat pomocí jazyka SQL nebo uloženou proceduru.
- Jako jímka, připojení dat do cílové tabulky nebo volání uložené procedury s vlastní logikou během kopírování.

>[!NOTE]
>Azure SQL Database [s funkcí Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-current) není nyní podporován tímto konektorem. Chcete-li vyřešit, můžete použít [obecný konektor ODBC](connector-odbc.md) a ovladač ODBC systému SQL Server prostřednictvím místního prostředí integration runtime. Postupujte podle [návod](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-current) konfigurací ODBC ovladač stahování a připojovací řetězec.

> [!IMPORTANT]
> Pokud zkopírujete data pomocí modulu runtime integrace služby Azure Data Factory, nakonfigurujte [bránu firewall serveru SQL Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) tak, aby službám Azure přístup k serveru.
> Při kopírování dat s využitím místního prostředí integration runtime, konfigurace brány firewall Azure SQL serveru umožňující odpovídající rozsah IP adres. Tento rozsah obsahuje IP počítače, který se používá k připojení ke službě Azure SQL Database.

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Azure Data Factory konkrétní konektor služby Azure SQL Database.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro služby Azure SQL Database, která je propojená se podporují tyto vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** musí být vlastnost nastavena na **AzureSqlDatabase**. | Ano |
| connectionString | Zadejte informace potřebné pro připojení k instanci Azure SQL Database **connectionString** vlastnost. <br/>Označte toto pole jako **SecureString** bezpečně uložit ve službě Azure Data Factory. Můžete také vložit heslo nebo službu klíč instančního objektu ve službě Azure Key Vault. Pokud je ověřování SQL, o přijetí změn `password` konfigurace z připojovacího řetězce. Další informace, podívejte se na příklad JSON pod tabulkou a [Store přihlašovacích údajů ve službě Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| servicePrincipalId | Zadejte ID klienta vaší aplikace. | Ano, pokud používáte ověřování Azure AD pomocí instančního objektu |
| servicePrincipalKey | Zadejte klíč aplikace. Označte toto pole jako **SecureString** bezpečně uložit ve službě Azure Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano, pokud používáte ověřování Azure AD pomocí instančního objektu |
| tenant | Zadejte informace o tenantovi, jako je název domény nebo tenant ID, ve kterém se nachází vaše aplikace. Načtení podržením ukazatele myši v pravém horním rohu webu Azure portal. | Ano, pokud používáte ověřování Azure AD pomocí instančního objektu |
| connectVia | To [prostředí integration runtime](concepts-integration-runtime.md) se používá pro připojení k úložišti. Pokud vaše úložiště dat se nachází v privátní síti, můžete použít prostředí Azure integration runtime nebo místní prostředí integration runtime. Pokud není zadán, je použít výchozí prostředí Azure integration runtime. | Ne |

Různými typy ověřování najdete v následujících částech na požadavky a ukázky JSON v uvedeném pořadí:

- [Ověřování SQL](#sql-authentication)
- [Azure AD ověřování tokenu aplikací: Instanční objekt služby](#service-principal-authentication)
- [Azure AD ověřování tokenu aplikací: Spravované identity pro prostředky Azure](#managed-identity)

>[!TIP]
>Pokud jako "limit relace pro databázi je XXX a bylo ho dosaženo" přístupů k chybě s kódem chyby "UserErrorFailedToConnectToSqlServer" a napište zprávu, přidejte `Pooling=false` připojovací řetězec a zkuste to znovu.

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

**Heslo ve službě Azure Key Vault** 

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

1. [Vytvoření aplikace Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) z portálu Azure portal. Poznamenejte si název aplikace a následující hodnoty, které definují propojené služby:

    - ID aplikace
    - Klíč aplikace
    - ID tenanta

2. [Zřízení správce Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) pro Server SQL Azure na portálu Azure portal, pokud jste tak již neučinili. Správce Azure AD musíte mít roli uživatele Azure AD nebo skupině Azure AD, ale nemůže být instančního objektu. Tento krok se provádí tak, aby v dalším kroku, můžete použít Azure AD identity uživatele databáze s omezením pro službu vytvořit instanční objekt.

3. [Vytvořit uživatele databáze s omezením](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) pro instanční objekt. Připojení k databázi z nebo na který se má ke kopírování dat pomocí nástrojů, jako je SQL Server Management Studio identity Azure AD, který má alespoň oprávnění ALTER ANY uživatele. Spusťte následující příkaz T-SQL: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. Udělte že instanční objekt služby nezbytná oprávnění pro uživatele SQL nebo jiné obvyklým způsobem. Spusťte následující kód. Další možnosti najdete v tématu [tento dokument](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. Konfigurace služby Azure SQL Database, propojený ve službě Azure Data Factory.


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

Použití spravované identity ověřování, postupujte podle těchto kroků.

1. [Zřízení správce Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) pro Server SQL Azure na portálu Azure portal, pokud jste tak již neučinili. Správce Azure AD může být uživatele služby Azure AD nebo skupiny služby Azure AD. Když udělíte skupině pomocí spravované identity roli správce, přeskočte kroky 3 a 4. Správce má úplný přístup k databázi.

2. [Vytvořit uživatele databáze s omezením](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) se identita spravované služby Azure Data Factory. Připojení k databázi z nebo na který se má ke kopírování dat pomocí nástrojů, jako je SQL Server Management Studio identity Azure AD, který má alespoň oprávnění ALTER ANY uživatele. Spusťte následující příkaz T-SQL: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. Udělte že objektu pro vytváření dat se identita spravované potřebné oprávnění, jako je obvykle udělali v případě uživatelů SQL a další. Spusťte následující kód. Další možnosti najdete v tématu [tento dokument](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    EXEC sp_addrolemember [role name], [your Data Factory name];
    ```

4. Konfigurace služby Azure SQL Database, propojený ve službě Azure Data Factory.

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, naleznete v tématu [datových sad](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services). Tato část obsahuje seznam vlastností, které podporuje datová sada Azure SQL Database.

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v tématu [kanály](concepts-pipelines-activities.md). Tato část obsahuje seznam vlastnosti podporované službou Azure SQL Database zdroje a jímky.

### <a name="azure-sql-database-as-the-source"></a>Azure SQL Database jako zdroj

Chcete-li kopírovat data ze služby Azure SQL Database, nastavte **typ** vlastnosti ve zdroji aktivitu kopírování k **SqlSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** musí být nastavena vlastnost zdroje aktivity kopírování **SqlSource**. | Ano |
| sqlReaderQuery | Tuto vlastnost používá vlastní dotaz SQL číst data. Příklad: `select * from MyTable`. | Ne |
| sqlReaderStoredProcedureName | Název uložené procedury, která čte data ze zdrojové tabulky. Příkaz SELECT v uložené proceduře musí být poslední příkaz jazyka SQL. | Ne |
| storedProcedureParameters | Parametry pro uloženou proceduru.<br/>Povolené hodnoty jsou páry název nebo hodnota. Názvy a použití malých a velkých parametry musí odpovídat názvům a použití malých a velkých parametrů uložené procedury. | Ne |

**Odkazuje na mějte na paměti:**

- Pokud **sqlReaderQuery** je určená pro **SqlSource**, spustí aktivita kopírování tohoto dotazu na zdroji Azure SQL Database se mají získat data. Můžete také zadat uložené procedury tak, že zadáte **sqlReaderStoredProcedureName** a **storedProcedureParameters** pokud používá parametry uložené procedury.
- Pokud nezadáte buď **sqlReaderQuery** nebo **sqlReaderStoredProcedureName**, sloupce definované v oddílu "struktura" datové sady JSON se používají k vytvoření dotazu. Dotaz `select column1, column2 from mytable` spouští pro Azure SQL Database. Pokud není definice datové sady "struktura", jsou vybrány všechny sloupce z tabulky.

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
> Další informace o podporovaných zápisu chování, konfigurace a osvědčené postupy z [osvědčený postup pro načítání dat do služby Azure SQL Database](#best-practice-for-loading-data-into-azure-sql-database).

Chcete-li kopírovat data do služby Azure SQL Database, nastavte **typ** vlastnost v aktivitě kopírování jímky pro **SqlSink**. Následující vlastnosti jsou podporovány v aktivitě kopírování **jímky** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** vlastnost jímky aktivity kopírování musí být nastavena na **SqlSink**. | Ano |
| writeBatchSize | Počet řádků k vložení do tabulky SQL *dávce*.<br/> Je povolená hodnota **celé číslo** (počet řádků). Ve výchozím nastavení Azure Data Factory dynamicky určuje velikost dávky odpovídající na základě velikosti řádku. | Ne |
| writeBatchTimeout | Doba čekání pro dávku vložte na dokončení před vypršením časového limitu operace.<br/> Je povolená hodnota **timespan**. Příkladem je "00: 30:00" (30 minut). | Ne |
| preCopyScript | Zadejte dotaz SQL pro aktivitu kopírování ke spuštění před zápisem dat do Azure SQL Database. Je vyvolána pouze jednou za kopírování spustit. Tuto vlastnost použijte k vyčištění dat předem. | Ne |
| sqlWriterStoredProcedureName | Název uložené procedury, která definuje, jak použít zdroj dat do cílové tabulky. <br/>Tuto uloženou proceduru se *za batch*. Pro operace, které spustí pouze jednou a nesouvisí se zdrojovými daty, například delete nebo truncate, použijte `preCopyScript` vlastnost. | Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru.<br/>Povolené hodnoty jsou páry název-hodnota. Názvy a použití malých a velkých parametry musí odpovídat názvům a použití malých a velkých parametrů uložené procedury. | Ne |
| sqlWriterTableType | Zadejte název tabulky typu použitého v uložené proceduře. Aktivita kopírování zpřístupňuje data přesouvá v dočasné tabulce s tímto typem tabulky. Uložená procedura kód pak sloučit data, která se kopíruje s existujícími daty. | Ne |

**Příklad 1: Připojení dat**

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

**Příklad 2: Vyvolání uložené procedury během kopírování**

Přečtěte si další podrobnosti o [vyvolat uloženou proceduru z SQL jímky](#invoke-a-stored-procedure-from-a-sql-sink).

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

Při kopírování dat do Azure SQL Database, můžete vyžadovat chování různých zápisu:

- [Připojit](#append-data): Zdroj data obsahují pouze nové záznamy.
- [Upsert](#upsert-data): Moje zdrojová data mají vkládání a aktualizace.
- [Přepsat](#overwrite-the-entire-table): Chci se pokaždé, když znovu načíst tabulku celé dimenze.
- [Zápis o vlastní logiku](#write-data-with-custom-logic): Potřebuji další zpracování před posledním vkládání do cílové tabulky.

Naleznete v příslušných částech o tom, jak nakonfigurovat v Azure Data Factory a osvědčené postupy.

### <a name="append-data"></a>Připojení dat

Připojení dat je výchozí chování tohoto konektoru Azure SQL Database jímky. Azure Data Factory nepodporuje příkaz bulk insert efektivně zapisovat do tabulky. Můžete nastavit zdroj a odpovídajícím způsobem jímky v aktivitě kopírování.

### <a name="upsert-data"></a>Upsert dat

**Možnost 1:** Pokud máte velké množství dat. Chcete-li zkopírovat, provést funkcí upsert, použijte následující postup: 

- Nejprve [dočasné tabulky v rozsahu databáze](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current#database-scoped-global-temporary-tables-azure-sql-database) k hromadné načtení všech záznamů pomocí aktivity kopírování. Protože operace u dočasných tabulek s rozsahem databáze se neprotokolují, můžete načíst milióny záznamů v řádu sekund.
- Spustit uloženou proceduru aktivitu ve službě Azure Data Factory k použití [sloučit](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) nebo příkazu INSERT nebo UPDATE. Použijte dočasné tabulky jako zdroje k provedení všech aktualizaci nebo vložení jako jedna transakce. Tímto způsobem je snížen počet zpátečních cest a operací protokolu. Na konci aktivita uložená procedura můžete až bude připravená na další cyklus upsert oříznutí dočasnou tabulku.

Jako příklad, ve službě Azure Data Factory vytvoříte kanál s **aktivita kopírování** zřetězené s **aktivity uložené procedury**. Nejprve kopíruje data ze zdrojového úložiště do dočasné tabulky Azure SQL Database, například **##UpsertTempTable**, jako název tabulky v datové sadě. Ten pak volá uloženou proceduru sloučit zdroj dat z dočasné tabulky do cílové tabulky a vyčistit dočasné tabulky.

![Upsertovat](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

V databázi definujte uloženou proceduru s logikou SLOUČENÍ, stejně jako v následujícím příkladu, který ukazuje z předchozí aktivity uložené procedury. Předpokládejme, že cíl je **marketingové** tabulky se třemi sloupci: **ID profilu**, **stavu**, a **kategorie**. Proveďte upsert na základě **ProfileID** sloupce.

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

**Možnost 2:** Je také možné [vyvolat uloženou proceduru v aktivitě kopírování](#invoke-a-stored-procedure-from-a-sql-sink). Tento postup spouští každý řádek ve zdrojové tabulce namísto použití hromadné vložení jako výchozí přístup v aktivitě kopírování, která není vhodné pro velké objemy upsert.

### <a name="overwrite-the-entire-table"></a>Přepsat celou tabulku

Můžete nakonfigurovat **preCopyScript** vlastnost jímky aktivity kopírování. V takovém případě pro každou aktivitu kopírování, na kterém běží, Azure Data Factory spustí skript nejprve. Pak spustí kopírování vložte data. Například pomocí nejnovějších dat přepsat celou tabulku, zadejte skript nejprve odstranit všechny záznamy a teprve potom hromadné načtení nových dat ze zdroje.

### <a name="write-data-with-custom-logic"></a>Zápis dat o vlastní logiku

Postup zápisu dat pomocí vlastní logiky je podobný jako postup popsaný v [Upsert data](#upsert-data) oddílu. Když budete chtít použít zvláštní zpracování před posledním vložení zdrojová data do cílové tabulky pro široké možnosti škálování a máte jeden ze dvou kroků:

- Načíst do tabulky s vymezeným oborem dočasné databáze a pak vyvolejte uloženou proceduru. 
- Vyvolání uložené procedury během kopírování.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Vyvolání uložené procedury z SQL jímky

Při kopírování dat do Azure SQL Database také můžete nakonfigurovat a volat uloženou proceduru s další parametry zadané uživatelem.

> [!TIP]
> Volání uložené procedury zpracovává data řádek po řádku namísto pomocí hromadné operace, které nedoporučujeme pro kopírování ve velkém měřítku. Další informace z [osvědčený postup pro načítání dat do služby Azure SQL Database](#best-practice-for-loading-data-into-azure-sql-database).

Uloženou proceduru můžete použít, když integrovaná funkce kopírování mechanismy neslouží účel. Příkladem je, když chcete použít zvláštní zpracování před posledním vložení zdrojová data do cílové tabulky. Některé příklady vysokého jsou, pokud chcete sloučit sloupce, vyhledat další hodnoty a vložit do více než jednou tabulkou.

Následující příklad ukazuje, jak provést funkcí upsert do tabulky ve službě Azure SQL Database pomocí uložené procedury. Předpokládejme, že vstupní data a jímku **marketingové** tabulka jednotlivých obsahovat tři sloupce: **ID profilu**, **stavu**, a **kategorie**. Proveďte upsert na základě **ProfileID** sloupce a použijte je jenom pro konkrétní kategorie.

**Výstupní datovou sadu:** "tableName" je stejný název parametru typu tabulky v uložené proceduře, jak je znázorněno v následujícím skriptu uložené procedury:

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

Definovat **SQL jímky** části v aktivitě kopírování následujícím způsobem:

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

V databázi, definovat uložená procedura se stejným názvem jako **SqlWriterStoredProcedureName**. Zpracovává vstupní data ze zadaného zdroje a sloučí do výstupní tabulky. Název parametru typu tabulky v uložené proceduře je stejný jako **tableName** definované v datové sadě.

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

V databázi, definujte typ tabulky se stejným názvem jako **sqlWriterTableType**. Schéma typu tabulky je stejný jako schéma vrácené funkcí vstupní data.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL,
    [Category] [varchar](256) NOT NULL
)
```

Uložená procedura funkce využívá možnosti [parametrů table-valued](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="mapping-data-flow-properties"></a>Mapování vlastnosti toku dat

Další podrobnosti o [zdroje transformace](data-flow-source.md) a [jímky transformace](data-flow-sink.md) v mapování datového toku.

## <a name="data-type-mapping-for-azure-sql-database"></a>Mapování datového typu pro službu Azure SQL Database

Po zkopírování dat z nebo do služby Azure SQL Database se používají následující mapování z datových typů v databázi SQL Azure do služby Azure Data Factory dočasné datových typů. Informace o tom, jak aktivity kopírování mapuje typ zdroje schéma a data jímky, najdete v článku [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md).

| Azure SQL Database datový typ | Azure Data Factory dočasné datový typ |
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
| rowversion |Byte[] |
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
> Datové typy, které mapují na typ desetinné dočasné aktuálně Azure Data Factory podporuje přesnost až 28. Pokud máte data s přesností větší než 28, vezměte v úvahu převodu na řetězec v dotazu SQL.

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovaných úložišť dat a formáty](copy-activity-overview.md##supported-data-stores-and-formats).
