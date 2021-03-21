---
title: Kopírování a transformace dat v Azure Database for MySQL
description: umožňuje získat informace o tom, jak kopírovat a transformovat data v Azure Database for MySQL pomocí Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/10/2021
ms.openlocfilehash: 4d13f6f435a21b467cae1b8e14211a001792787f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103012598"
---
# <a name="copy-and-transform-data-in-azure-database-for-mysql-by-using-azure-data-factory"></a>Kopírování a transformace dat v Azure Database for MySQL pomocí Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

V tomto článku se naučíte, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z a do Azure Database for MySQL a jak transformovat data v Azure Database for MySQL pomocí toku dat. Pokud se chcete dozvědět o Azure Data Factory, přečtěte si [úvodní článek](introduction.md).

Tento konektor je specializovaný pro [službu Azure Database for MySQL](../mysql/overview.md). K kopírování dat z obecné databáze MySQL v místním prostředí nebo v cloudu použijte [konektor MySQL](connector-mysql.md).

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor Azure Database for MySQL se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Mapování toku dat](concepts-data-flow-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které slouží k definování Data Factory entit specifických pro Azure Database for MySQL konektor.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro Azure Database for MySQL propojenou službu jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type musí být nastavená na: **AzureMySql** . | Yes |
| připojovací řetězec | Zadejte informace potřebné pro připojení k instanci Azure Database for MySQL. <br/> Můžete také do Azure Key Vault umístit heslo a načíst konfiguraci z `password` připojovacího řetězce. Další podrobnosti najdete v následujících ukázkách a [přihlašovací údaje úložiště v Azure Key Vault](store-credentials-in-key-vault.md) článku. | Yes |
| connectVia | [Integration runtime](concepts-integration-runtime.md) , která se má použít pro připojení k úložišti dat Můžete použít Azure Integration Runtime nebo místní Integration Runtime (Pokud je úložiště dat umístěné v privátní síti). Pokud není zadaný, použije se výchozí Azure Integration Runtime. |No |

Typický připojovací řetězec je `Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>` . Další vlastnosti, které můžete nastavit pro váš případ:

| Vlastnost | Popis | Možnosti | Vyžadováno |
|:--- |:--- |:--- |:--- |
| SSLMode | Tato možnost určuje, zda ovladač při připojování k MySQL používá šifrování TLS a ověřování. Například `SSLMode=<0/1/2/3/4>`| DISABLEd (0)/PREFEROVÁNo (1) **(výchozí)** /požadováno (2)/VERIFY_CA (3)/VERIFY_IDENTITY (4) | No |
| UseSystemTrustStore | Tato možnost určuje, jestli se má použít certifikát certifikační autority z úložiště důvěryhodnosti systému nebo ze zadaného souboru PEM. Například `UseSystemTrustStore=<0/1>;`| Povoleno (1)/zakázáno (0) **(výchozí)** | No |

**Příklad:**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Příklad: uložení hesla v Azure Key Vault**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;",
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

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . V této části najdete seznam vlastností podporovaných sadou Azure Database for MySQL DataSet.

Chcete-li kopírovat data z Azure Database for MySQL, nastavte vlastnost Type datové sady na **AzureMySqlTable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type datové sady musí být nastavená na: **AzureMySqlTable** . | Yes |
| tableName | Název tabulky v databázi MySQL | Ne (Pokud je zadáno "dotaz" ve zdroji aktivity) |

**Příklad**

```json
{
    "name": "AzureMySQLDataset",
    "properties": {
        "type": "AzureMySqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure MySQL linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností podporovaných Azure Database for MySQL zdroj a jímka.

### <a name="azure-database-for-mysql-as-source"></a>Azure Database for MySQL jako zdroj

Chcete-li kopírovat data z Azure Database for MySQL, jsou v části **zdroje** aktivity kopírování podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type zdroje aktivity kopírování musí být nastavená na: **AzureMySqlSource** . | Yes |
| query | Pro čtení dat použijte vlastní dotaz SQL. Příklad: `"SELECT * FROM MyTable"`. | Ne (Pokud je zadáno "tableName" v datové sadě |
| queryCommandTimeout | Doba čekání před vypršením časového limitu požadavku na dotaz Výchozí hodnota je 120 minut (02:00:00). | No |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure MySQL input dataset name>",
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
                "type": "AzureMySqlSource",
                "query": "<custom query e.g. SELECT * FROM MyTable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-mysql-as-sink"></a>Azure Database for MySQL jako jímka

Chcete-li kopírovat data do Azure Database for MySQL, v části **jímka** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type jímky aktivity kopírování musí být nastavená na: **AzureMySqlSink** . | Yes |
| preCopyScript | Zadejte dotaz SQL pro aktivitu kopírování, která má být provedena před zápisem dat do Azure Database for MySQL při každém spuštění. Tuto vlastnost můžete použít k vyčištění předem načtených dat. | No |
| writeBatchSize | Když velikost vyrovnávací paměti dosáhne writeBatchSize, vloží data do tabulky Azure Database for MySQL.<br>Povolená hodnota je celé číslo představující počet řádků. | Ne (výchozí hodnota je 10 000) |
| writeBatchTimeout | Počkejte, než se operace dávkového vložení dokončí předtím, než vyprší časový limit.<br>Povolené hodnoty jsou TimeSpan. Příklad je 00:30:00 (30 minut). | Ne (výchozí hodnota je 00:00:30) |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure MySQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureMySqlSink",
                "preCopyScript": "<custom SQL script>",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Mapování vlastností toku dat

Při transformaci dat v toku mapování dat můžete číst a zapisovat do tabulek z Azure Database for MySQL. Další informace najdete v tématu transformace [zdroje](data-flow-source.md) a [transformace jímky](data-flow-sink.md) v tématu mapování toků dat. Můžete použít datovou sadu Azure Database for MySQL nebo [vloženou datovou sadu](data-flow-source.md#inline-datasets) jako typ zdroje a jímky.

### <a name="source-transformation"></a>Transformace zdroje

V níže uvedené tabulce jsou uvedeny vlastnosti podporované zdrojem Azure Database for MySQL. Tyto vlastnosti můžete upravit na kartě **Možnosti zdrojového kódu** .

| Název | Popis | Povinné | Povolené hodnoty | Vlastnost skriptu toku dat |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Tabulka | Pokud vyberete možnost tabulka jako vstup, tok dat načte všechna data z tabulky zadané v datové sadě. | No | - |*(pouze pro vloženou datovou sadu)*<br>tableName |
| Dotaz | Pokud jako vstup vyberete dotaz, zadejte dotaz SQL pro načtení dat ze zdroje, který přepíše všechny tabulky zadané v datové sadě. Použití dotazů je skvělý způsob, jak omezit řádky pro testování nebo vyhledávání.<br><br>Klauzule **ORDER by** není podporována, ale můžete nastavit úplný příkaz SELECT FROM. Můžete také použít uživatelsky definované funkce tabulky. **SELECT * FROM udfGetData ()** je UDF v SQL, který vrátí tabulku, kterou můžete použít v toku dat.<br>Příklad dotazu: `select * from mytable where customerId > 1000 and customerId < 2000` nebo `select * from "MyTable"` .| No | Řetězec | query |
| Velikost dávky | Určete velikost dávky pro velké objemy dat v dávkách. | No | Integer | batchSize |
| Úroveň izolace | Vyberte jednu z následujících úrovní izolace:<br>– Čtení potvrzeno<br>-Čtení nepotvrzeno (výchozí)<br>-Opakované čtení<br>– Serializovatelný<br>-None (ignorovat úroveň izolace) | No | <small>READ_COMMITTED<br/>READ_UNCOMMITTED<br/>REPEATABLE_READ<br/>SERIALIZOVATELNÝ<br/>NTATO</small> |isolationLevel |

#### <a name="azure-database-for-mysql-source-script-example"></a>Příklad zdrojového skriptu Azure Database for MySQL

Pokud jako typ zdroje použijete Azure Database for MySQL, je k němu přidružen skript toku dat:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    isolationLevel: 'READ_UNCOMMITTED',
    query: 'select * from mytable',
    format: 'query') ~> AzureMySQLSource
```

### <a name="sink-transformation"></a>Transformace jímky

V níže uvedené tabulce jsou uvedeny vlastnosti, které Azure Database for MySQL jímka podporuje. Tyto vlastnosti můžete upravit na kartě **Možnosti jímky** .

| Název | Popis | Povinné | Povolené hodnoty | Vlastnost skriptu toku dat |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Update – metoda | Určete, jaké operace jsou pro cíl databáze povolené. Ve výchozím nastavení je povolen pouze vkládání.<br>Aby bylo možné aktualizovat, Upsert nebo odstraňovat řádky, je nutné transformaci řádků pro tyto akce označit [změnou řádku](data-flow-alter-row.md) . | Yes | `true` nebo `false` | lze odstranit <br/>vložitelný <br/>aktualizovatelné <br/>upsertable |
| Klíčové sloupce | Pro aktualizace, upsertuje a DELETE musí být klíčové sloupce nastavené tak, aby určily, který řádek se má změnit.<br>Název sloupce, který vyberete jako klíč, bude použit jako součást následné aktualizace, Upsert a DELETE. Proto je nutné vybrat sloupec, který existuje v mapování jímky. | No | Pole | keys |
| Přeskočit zápis klíčových sloupců | Pokud chcete hodnotu nezazapisovat do sloupce klíč, zaškrtněte políčko Přeskočit zápis klíčových sloupců. | No | `true` nebo `false` | skipKeyWrites |
| Akce tabulky |Určuje, zda mají být před zápisem znovu vytvořeny nebo odebrány všechny řádky z cílové tabulky.<br>- **Žádné**: v tabulce se neprovede žádná akce.<br>- **Znovu vytvořit**: tabulka se vynechá a znovu vytvoří. Požadováno při dynamickém vytváření nové tabulky.<br>- **Zkrátit**: všechny řádky z cílové tabulky se odeberou. | No | `true` nebo `false` | znovu vytvořit<br/>zkrátit |
| Velikost dávky | Určete, kolik řádků se má v každé dávce zapsat. Větší velikosti dávek zlepšují kompresi a optimalizaci paměti, ale při ukládání dat do mezipaměti riskuje výjimky z paměti. | No | Integer | batchSize |
| Skripty před a po SQL | Zadejte víceřádkové skripty SQL, které se spustí před (před zpracováním) a po (po zpracování) se zapisují do databáze jímky. | No | Řetězec | preSQLs<br>postSQLs |

#### <a name="azure-database-for-mysql-sink-script-example"></a>Příklad skriptu jímky Azure Database for MySQL

Když použijete Azure Database for MySQL jako typ jímky, je přidružený skript toku dat:

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    deletable:false,
    insertable:true,
    updateable:true,
    upsertable:true,
    keys:['keyColumn'],
    format: 'table',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> AzureMySQLSink
```

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="data-type-mapping-for-azure-database-for-mysql"></a>Mapování datových typů pro Azure Database for MySQL

Při kopírování dat z Azure Database for MySQL se z datových typů MySQL používají následující mapování pro Azure Data Factory dočasných datových typů. Informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ do jímky, najdete v tématu [mapování typů schématu a dat](copy-activity-schema-and-type-mapping.md) .

| Azure Database for MySQL datový typ | Typ dat interim Data Factory |
|:--- |:--- |
| `bigint` |`Int64` |
| `bigint unsigned` |`Decimal` |
| `bit` |`Boolean` |
| `bit(M), M>1`|`Byte[]`|
| `blob` |`Byte[]` |
| `bool` |`Int16` |
| `char` |`String` |
| `date` |`Datetime` |
| `datetime` |`Datetime` |
| `decimal` |`Decimal, String` |
| `double` |`Double` |
| `double precision` |`Double` |
| `enum` |`String` |
| `float` |`Single` |
| `int` |`Int32` |
| `int unsigned` |`Int64`|
| `integer` |`Int32` |
| `integer unsigned` |`Int64` |
| `long varbinary` |`Byte[]` |
| `long varchar` |`String` |
| `longblob` |`Byte[]` |
| `longtext` |`String` |
| `mediumblob` |`Byte[]` |
| `mediumint` |`Int32` |
| `mediumint unsigned` |`Int64` |
| `mediumtext` |`String` |
| `numeric` |`Decimal` |
| `real` |`Double` |
| `set` |`String` |
| `smallint` |`Int16` |
| `smallint unsigned` |`Int32` |
| `text` |`String` |
| `time` |`TimeSpan` |
| `timestamp` |`Datetime` |
| `tinyblob` |`Byte[]` |
| `tinyint` |`Int16` |
| `tinyint unsigned` |`Int16` |
| `tinytext` |`String` |
| `varchar` |`String` |
| `year` |`Int32` |

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Azure Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
