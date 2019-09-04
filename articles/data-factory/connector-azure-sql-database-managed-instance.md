---
title: Kopírování dat do a z Azure SQL Database spravované instance pomocí Azure Data Factory | Microsoft Docs
description: Přečtěte si, jak přesunout data do a z Azure SQL Database spravované instance pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: f664e0419396eaf60c037c2adfde70df0034cc5b
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70276002"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>Kopírování dat do a z Azure SQL Database spravované instance pomocí Azure Data Factory

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data do a z Azure SQL Database spravované instance. Vytvoří se v článku [Přehled aktivity kopírování](copy-activity-overview.md) , který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Data z Azure SQL Database spravované instance můžete kopírovat do libovolného podporovaného úložiště dat jímky. Data můžete také kopírovat z libovolného podporovaného zdrojového úložiště dat do spravované instance. Seznam úložišť dat, která jsou v rámci aktivity kopírování podporovaná jako zdroje a jímky, najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

Konkrétně tato Azure SQL Database konektor spravované instance podporuje:

- Kopírování dat pomocí ověřování SQL a Azure Active Directory (Azure AD) ověřování tokenu aplikace pomocí instančního objektu nebo spravovaných identit pro prostředky Azure.
- Jako zdroj načítání dat pomocí dotazu SQL nebo uložené procedury.
- Jako jímky, připojení dat do cílové tabulky nebo vyvolání uložené procedury s vlastní logikou během kopírování.

>[!NOTE]
>Tento konektor teď nepodporuje Azure SQL Database Managed instance [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current) . Pokud chcete tento problém obejít, můžete použít [obecný konektor ODBC](connector-odbc.md) a SQL Server ovladač ODBC prostřednictvím prostředí Integration runtime v místním prostředí. Postupujte [podle pokynů ke](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-mi-current) stažení ovladače ODBC a konfigurací připojovacích řetězců.

>[!NOTE]
>Tento konektor aktuálně nepodporuje objekt služby a ověřování spravované identity. Chcete-li se vyhnout, vyberte konektor Azure SQL Database a ručně zadejte server vaší spravované instance.

## <a name="prerequisites"></a>Požadavky

Pokud chcete získat přístup k [veřejnému koncovému bodu](../sql-database/sql-database-managed-instance-public-endpoint-securely.md)spravované instance Azure SQL Database, můžete použít Azure Data Factory spravované prostředí Azure Integration runtime. Ujistěte se, že jste povolili veřejný koncový bod a zároveň povolili provoz veřejného koncového bodu ve skupině zabezpečení sítě, aby se Azure Data Factory mohl připojit k vaší databázi. Další informace najdete v [těchto pokynech](../sql-database/sql-database-managed-instance-public-endpoint-configure.md).

Pokud chcete získat přístup k privátnímu koncovému bodu spravované instance Azure SQL Database, nastavte místní [prostředí Integration runtime](create-self-hosted-integration-runtime.md) , které má přístup k databázi. Pokud zřídíte místní prostředí Integration runtime ve stejné virtuální síti jako vaše spravovaná instance, ujistěte se, že je váš počítač Integration runtime v jiné podsíti než vaše spravovaná instance. Pokud zřídíte místní prostředí Integration runtime v jiné virtuální síti než vaše spravovaná instance, můžete k připojení k virtuální síti použít buď partnerský vztah virtuální sítě, nebo virtuální síť. Další informace najdete v tématu [připojení aplikace k Azure SQL Database Managed instance](../sql-database/sql-database-managed-instance-connect-app.md).

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech najdete podrobné informace o vlastnostech, které slouží k definování Azure Data Factory entit specifických pro konektor Azure SQL Database Managed instance.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu Azure SQL Database spravované instance jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavená na **AzureSqlMI**. | Ano |
| connectionString |Tato vlastnost určuje informace **připojovacího řetězce** potřebné pro připojení ke spravované instanci pomocí ověřování SQL. Další informace najdete v následujících příkladech. <br/>Výchozí port je 1433. Pokud používáte Azure SQL Database spravovanou instanci s veřejným koncovým bodem, explicitně zadejte port 3342.<br>Označte toto pole jako **SecureString** a bezpečně ho uložte do Azure Data Factory. Heslo můžete také přidat do Azure Key Vault. Pokud se jedná o ověřování SQL, vyžádejte si `password` z připojovacího řetězce konfiguraci. Další informace najdete v příkladech JSON, které následují po tabulce, a [ukládají přihlašovací údaje v Azure Key Vault](store-credentials-in-key-vault.md). |Ano |
| servicePrincipalId | Zadejte ID klienta vaší aplikace. | Ano, pokud používáte ověřování Azure AD s instančním objektem |
| servicePrincipalKey | Zadejte klíč aplikace. Označte toto pole jako **SecureString** a bezpečně ho uložte do Azure Data Factory nebo [odkaz na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | Ano, pokud používáte ověřování Azure AD s instančním objektem |
| tenant | Zadejte informace o tenantovi, jako je název domény nebo ID tenanta, pod kterým se vaše aplikace nachází. Načtěte ho tak, že najedete myší v pravém horním rohu Azure Portal. | Ano, pokud používáte ověřování Azure AD s instančním objektem |
| connectVia | Tento [modul runtime integrace](concepts-integration-runtime.md) se používá pro připojení k úložišti dat. Pokud má vaše spravovaná instance veřejný koncový bod a umožňuje Azure Data Factory k němu přistupovat, můžete použít místní prostředí Integration runtime nebo prostředí Azure Integration runtime. Pokud tento parametr nezadáte, použije se výchozí prostředí Azure Integration runtime. |Ano |

Různými typy ověřování najdete v následujících částech na požadavky a ukázky JSON v uvedeném pořadí:

- [Ověřování SQL](#sql-authentication)
- [Ověřování tokenu aplikací Azure AD: Instanční objekt](#service-principal-authentication)
- [Ověřování tokenu aplikací Azure AD: Spravované identity pro prostředky Azure](#managed-identity)

### <a name="sql-authentication"></a>Ověřování pomocí SQL

**Příklad 1: použití ověřování SQL**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
            }
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
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;"
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

1. Postupujte podle kroků a [zřiďte správce Azure Active Directory pro vaši spravovanou instanci](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance).

2. [Vytvořte aplikaci Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) z Azure Portal. Poznamenejte si název aplikace a následující hodnoty, které definují propojené služby:

    - ID aplikace
    - Klíč aplikace
    - ID tenanta

3. [Vytvoření přihlašovacích](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) údajů pro Azure Data Factory spravovanou identitu. V SQL Server Management Studio (SSMS) se připojte ke svojí spravované instanci pomocí účtu SQL Server, který je **sysadmin**. V **Hlavní** databázi spusťte následující příkaz T-SQL:

    ```sql
    CREATE LOGIN [your application name] FROM EXTERNAL PROVIDER
    ```

4. [Vytvořte uživatele databáze s omezením](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) pro Azure Data Factory spravovanou identitu. Připojte se k databázi z nebo do které chcete kopírovat data, spusťte následující příkaz T-SQL: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER
    ```

5. Udělte Data Factory spravovaná identita potřebná k tomu, aby se běžně daly dělat pro uživatele SQL a jiné. Spusťte následující kód. Další možnosti najdete v [tomto dokumentu](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current).

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your application name]
    ```

6. Nakonfigurujte propojenou službu Azure SQL Database spravované instance v Azure Data Factory.

**Příklad: použití ověřování instančního objektu**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<hostname,port>;Initial Catalog=<databasename>;"
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

Datová továrna může být přidružená ke [spravované identitě pro prostředky Azure](data-factory-service-identity.md) , které představují konkrétní objekt pro vytváření dat. Tuto spravovanou identitu můžete použít pro Azure SQL Database ověřování spravované instance. Určená továrna má přístup k datům a jejich zkopírování z databáze nebo do databáze pomocí této identity.

Pokud chcete použít spravované ověřování identity, postupujte podle těchto kroků.

1. Postupujte podle kroků a [zřiďte správce Azure Active Directory pro vaši spravovanou instanci](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance).

2. [Vytvoření přihlašovacích](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) údajů pro Azure Data Factory spravovanou identitu. V SQL Server Management Studio (SSMS) se připojte ke svojí spravované instanci pomocí účtu SQL Server, který je **sysadmin**. V **Hlavní** databázi spusťte následující příkaz T-SQL:

    ```sql
    CREATE LOGIN [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

3. [Vytvořte uživatele databáze s omezením](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) pro Azure Data Factory spravovanou identitu. Připojte se k databázi z nebo do které chcete kopírovat data, spusťte následující příkaz T-SQL: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

4. Udělte Data Factory spravovaná identita potřebná k tomu, aby se běžně daly dělat pro uživatele SQL a jiné. Spusťte následující kód. Další možnosti najdete v [tomto dokumentu](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current).

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your Data Factory name]
    ```

5. Nakonfigurujte propojenou službu Azure SQL Database spravované instance v Azure Data Factory.

**Příklad: používá spravované ověřování identity.**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<hostname,port>;Initial Catalog=<databasename>;"
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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro použití k definování datových sad, naleznete v článku datové sady. V této části najdete seznam vlastností podporovaných datovou sadou spravované instance Azure SQL Database.

Chcete-li kopírovat data do a z Azure SQL Database spravované instance, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type datové sady musí být nastavená na **AzureSqlMITable**. | Ano |
| schema | Název schématu. |Ne pro zdroj, Ano pro jímku  |
| table | Název tabulky/zobrazení |Ne pro zdroj, Ano pro jímku  |
| tableName | Název tabulky nebo zobrazení se schématem. Tato vlastnost je podporována z důvodu zpětné kompatibility. Pro nové úlohy použijte `schema` a. `table` | Ne pro zdroj, Ano pro jímku |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro použití k definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností podporovaných zdrojem a jímkou spravované instance Azure SQL Database.

### <a name="azure-sql-database-managed-instance-as-a-source"></a>Azure SQL Database spravovanou instanci jako zdroj

Chcete-li kopírovat data z Azure SQL Database spravované instance, jsou v části zdroje aktivity kopírování podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **SqlMISource**. | Ano |
| sqlReaderQuery |Tato vlastnost používá vlastní dotaz SQL ke čtení dat. Příklad: `select * from MyTable`. |Ne |
| sqlReaderStoredProcedureName |Tato vlastnost je název uložené procedury, která čte data ze zdrojové tabulky. Příkaz SELECT v uložené proceduře musí být poslední příkaz jazyka SQL. |Ne |
| storedProcedureParameters |Tyto parametry jsou pro uloženou proceduru.<br/>Povolené hodnoty jsou páry název nebo hodnota. Názvy a písmena parametrů se musí shodovat s názvy a písmeny parametrů uložené procedury. |Ne |

**Je třeba počítat s následujícím:**

- Pokud je pro **SqlMISource**zadána možnost **sqlReaderQuery** , aktivita kopírování spustí tento dotaz proti zdroji spravované instance, aby získala data. Uloženou proceduru lze také určit zadáním **sqlReaderStoredProcedureName** a **storedProcedureParameters** , pokud uložená procedura přijímá parametry.
- Pokud nezadáte buď vlastnost **sqlReaderQuery** nebo **sqlReaderStoredProcedureName** , použijí se k sestavení dotazu sloupce definované v oddílu Structure pro datovou sadu JSON. Dotaz `select column1, column2 from mytable` se spustí na spravované instanci. Pokud definice datové sady nemá "strukturu", všechny sloupce jsou vybrány z tabulky.

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

**Příklad: Použít uloženou proceduru**

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

### <a name="azure-sql-database-managed-instance-as-a-sink"></a>Azure SQL Database spravovanou instanci jako jímku

> [!TIP]
> Přečtěte si další informace o podporovaných chováních, konfiguracích a osvědčených postupech pro zápis z [osvědčeného postupu pro načítání dat do Azure SQL Database spravované instance](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

Chcete-li kopírovat data do Azure SQL Database spravované instance, jsou v části jímka aktivity kopírování podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type jímky aktivity kopírování musí být nastavená na **SqlMISink**. | Ano |
| writeBatchSize |Počet řádků, které mají být vloženy do tabulky SQL *na dávku*.<br/>Povolené hodnoty jsou celá čísla pro počet řádků. Ve výchozím nastavení Azure Data Factory dynamicky určí vhodnou velikost dávky na základě velikosti řádku.  |Ne |
| writeBatchTimeout |Tato vlastnost určuje dobu čekání na dokončení operace dávkového vložení před vypršením časového limitu.<br/>Povolené hodnoty jsou pro časové rozpětí. Příkladem je "00:30:00", což je 30 minut. |Ne |
| preCopyScript |Tato vlastnost určuje dotaz SQL pro aktivitu kopírování, která se má spustit před zápisem dat do spravované instance. Vyvolá se jenom jednou pro každé spuštění kopírování. Tuto vlastnost můžete použít k vyčištění předem načtených dat. |Ne |
| sqlWriterStoredProcedureName | Název uložené procedury definující, jak se mají zdrojová data použít v cílové tabulce. <br/>Tato uložená procedura je *vyvolána pro každou dávku*. Pro operace, které se spouštějí jenom jednou a které nemají nic dělat se zdrojovými daty, například odstranit nebo zkrátit, použijte `preCopyScript` vlastnost. | Ne |
| storedProcedureTableTypeParameterName |Název parametru pro typ tabulky určený v uložené proceduře.  |Ne |
| sqlWriterTableType |Název typu tabulky, který se má použít v uložené proceduře Aktivita kopírování zpřístupňuje data, která jsou k dispozici v dočasné tabulce s tímto typem tabulky. Uložený kód procedury pak může sloučit data, která jsou kopírována se stávajícími daty. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru.<br/>Povolené hodnoty jsou páry název-hodnota. Názvy a použití malých a velkých parametry musí odpovídat názvům a použití malých a velkých parametrů uložené procedury. | Ne |

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
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Příklad 2: Vyvolat uloženou proceduru během kopírování**

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

## <a name="best-practice-for-loading-data-into-azure-sql-database-managed-instance"></a>Osvědčené postupy načítání dat do spravované instance Azure SQL Database

Když kopírujete data do Azure SQL Database spravované instance, budete možná potřebovat jiné chování při zápisu:

- [Připojit](#append-data): Moje zdrojová data obsahují pouze nové záznamy.
- [Upsert](#upsert-data): Moje zdrojová data obsahují jak vložené, tak i aktualizace.
- [Přepsat](#overwrite-the-entire-table): Chci pokaždé, když se pokaždé znovu načte celá tabulka dimenze.
- [Psaní pomocí vlastní logiky](#write-data-with-custom-logic): Potřebuji dodatečné zpracování před konečným vložením do cílové tabulky. 

V příslušných částech najdete informace o tom, jak nakonfigurovat v Azure Data Factory a osvědčených postupech.

### <a name="append-data"></a>Připojit data

Připojení dat je výchozím chováním tohoto Azure SQL Database konektoru jímky spravované instance. Azure Data Factory hromadné vložení do tabulky efektivně. Zdroj a jímku můžete v aktivitě kopírování nakonfigurovat odpovídajícím způsobem.

### <a name="upsert-data"></a>Upsert dat

**Možnost 1:** Pokud máte ke kopírování velké množství dat, použijte následující postup k Upsert: 

- Nejdřív použijte [dočasnou tabulku](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) k hromadnému načtení všech záznamů pomocí aktivity kopírování. Vzhledem k tomu, že operace s dočasnými tabulkami nejsou protokolovány, můžete načíst miliony záznamů během několika sekund.
- Spuštěním aktivity uložené procedury v Azure Data Factory použijte příkaz [Merge](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) nebo INSERT/Update. Použijte dočasnou tabulku jako zdroj k provedení všech aktualizací nebo vložení jako jedné transakce. Tímto způsobem se sníží počet operací Round Trip a log. Na konci aktivity uložená procedura můžete dočasnou tabulku zkrátit, aby byla připravená na další Upsert cyklus.

Jako příklad můžete v Azure Data Factory vytvořit kanál s **aktivitou kopírování** zřetězenou s **aktivitou uložené procedury**. Předchozí kopie dat ze zdrojového úložiště do dočasné tabulky, například **# #UpsertTempTable**, jako název tabulky v datové sadě. Potom druhá potom vyvolá uloženou proceduru ke sloučení zdrojových dat z dočasné tabulky do cílové tabulky a vyčištění dočasné tabulky.

![Upsertovat](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

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

Vlastnost **preCopyScript** můžete nakonfigurovat v jímky aktivity kopírování. V takovém případě se pro každou aktivitu kopírování, která běží, Azure Data Factory spustí skript jako první. Potom spustí kopii pro vložení dat. Chcete-li například přepsat celou tabulku nejnovějšími daty, zadejte skript, který nejprve odstraní všechny záznamy před hromadnou zátěží nových dat ze zdroje.

### <a name="write-data-with-custom-logic"></a>Zápis dat pomocí vlastní logiky

Postup pro zápis dat pomocí vlastní logiky je podobný těm, které jsou popsané v části [Upsert data](#upsert-data) . Pokud potřebujete použít dodatečné zpracování před konečným vložením zdrojových dat do cílové tabulky, můžete pro velkou škálu provést jednu z následujících akcí: 

- Načíst do dočasné tabulky a poté vyvolat uloženou proceduru.
- Vyvolat uloženou proceduru během kopírování.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a>Vyvolat uloženou proceduru z jímky SQL

Když kopírujete data do Azure SQL Database spravované instance, můžete také nakonfigurovat a vyvolat uloženou proceduru zadanou uživatelem s dalšími parametry. Funkce uložené procedury využívá [parametry s hodnotou tabulky](https://msdn.microsoft.com/library/bb675163.aspx).

> [!TIP]
> Vyvolání uložené procedury zpracuje řádek data na řádku místo pomocí hromadné operace, kterou nedoporučujeme pro velké kopírování. Další informace z [osvědčeného postupu pro načítání dat do Azure SQL Database spravované instance](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

Uloženou proceduru můžete použít, pokud předdefinované mechanismy kopírování neslouží k tomuto účelu. Příkladem je, že chcete použít dodatečné zpracování před konečným vložením zdrojových dat do cílové tabulky. Některé další příklady zpracování jsou, když chcete sloučit sloupce, vyhledat další hodnoty a vložit data do více než jedné tabulky.

Následující příklad ukazuje, jak použít uloženou proceduru k provedení Upsert do tabulky v databázi SQL Server. Předpokládejme, že vstupní data a **marketingová** tabulka jímky mají tři sloupce: **ProfileID**, **State**a **Category**. Proveďte Upsert na základě sloupce **ProfileID** a použijte ho jenom pro konkrétní kategorii s názvem "produkt".

1. V databázi Definujte typ tabulky se stejným názvem jako **sqlWriterTableType**. Schéma typu tabulky je stejné jako schéma vrácené vašimi vstupními daty.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL，
        [Category] [varchar](256) NOT NULL
    )
    ```

2. V databázi definujte uloženou proceduru se stejným názvem jako **SqlWriterStoredProcedureName**. Zpracovává vstupní data ze zadaného zdroje a sloučí je do výstupní tabulky. Název parametru typu tabulky v uložené proceduře je stejný jako **TableName** definovaný v datové sadě.

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
        "SqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "SqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="data-type-mapping-for-azure-sql-database-managed-instance"></a>Mapování datových typů pro Azure SQL Database spravovanou instanci

Když se data zkopírují do Azure SQL Database spravované instance a z ní, použijí se následující mapování z datových typů Azure SQL Database spravované instance, aby se do nich Azure Data Factory dočasné datové typy. Informace o tom, jak aktivita kopírování mapuje ze zdrojového schématu a datového typu do jímky, najdete v tématu [mapování schémat a datových typů](copy-activity-schema-and-type-mapping.md).

| Azure SQL Database datový typ spravované instance | Azure Data Factory pomocný datový typ |
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
| tinyint |Int16 |
| uniqueidentifier |Guid |
| Varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

>[!NOTE]
> Pro datové typy, které jsou mapovány na mezihodnotový průběžný typ, aktuálně Azure Data Factory podporuje přesnost až 28. Pokud máte data, která vyžadují přesnost větší než 28, zvažte převod na řetězec v dotazu SQL.

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Azure Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md##supported-data-stores-and-formats).
