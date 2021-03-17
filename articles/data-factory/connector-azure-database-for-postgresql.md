---
title: Kopírování a transformace dat v Azure Database for PostgreSQL
description: Naučte se, jak kopírovat a transformovat data v Azure Database for PostgreSQL pomocí Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/25/2021
ms.openlocfilehash: ec4ea645e325ef48d4cb5951cd39fd4e9cbe1617
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738051"
---
# <a name="copy-and-transform-data-in-azure-database-for-postgresql-by-using-azure-data-factory"></a>Kopírování a transformace dat v Azure Database for PostgreSQL pomocí Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

V tomto článku se naučíte, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z a do Azure Database for PostgreSQL a jak transformovat data v Azure Database for PostgreSQL pomocí toku dat. Pokud se chcete dozvědět o Azure Data Factory, přečtěte si [úvodní článek](introduction.md).

Tento konektor je specializovaný pro [službu Azure Database for PostgreSQL](../postgresql/overview.md). Pokud chcete kopírovat data z obecné databáze PostgreSQL místně nebo v cloudu, použijte [konektor PostgreSQL](connector-postgresql.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Azure Database for PostgreSQL se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Mapování toku dat](concepts-data-flow-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)

Tok dat v současné době podporuje službu Azure Database for PostgreSQL Single server, ale ne flexibilní Server nebo škálovatelné (Citus).

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které slouží k definování Data Factory entit specifických pro Azure Database for PostgreSQL konektor.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu Azure Database for PostgreSQL jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type musí být nastavená na: **AzurePostgreSql**. | Ano |
| připojovací řetězec | Připojovací řetězec ODBC pro připojení k Azure Database for PostgreSQL.<br/>Můžete také vložit heslo do Azure Key Vault a získat `password` konfiguraci z připojovacího řetězce. Další podrobnosti najdete v následujících ukázkách a [přihlašovací údaje pro uložení v Azure Key Vault](store-credentials-in-key-vault.md) . | Ano |
| connectVia | Tato vlastnost představuje [prostředí Integration runtime](concepts-integration-runtime.md) , které se má použít pro připojení k úložišti dat. Můžete použít Azure Integration Runtime nebo místní Integration Runtime (Pokud je úložiště dat umístěné v privátní síti). Pokud není zadaný, použije se výchozí Azure Integration Runtime. |Ne |

Typický připojovací řetězec je `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>` . Tady je více vlastností, které můžete nastavit na váš případ:

| Vlastnost | Popis | Možnosti | Vyžadováno |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| Metoda, kterou ovladač používá k šifrování dat posílaných mezi ovladačem a databázovým serverem. Například  `EncryptionMethod=<0/1/6>;`| 0 (bez šifrování) **(výchozí)** /1 (SSL)/6 (RequestSSL) | Ne |
| ValidateServerCertificate (VSC) | Určuje, zda ovladač ověřuje certifikát, který je odeslán databázovým serverem, pokud je povoleno šifrování SSL (metoda šifrování = 1). Například  `ValidateServerCertificate=<0/1>;`| 0 (zakázáno) **(výchozí)** /1 (povoleno) | Ne |

**Příklad**:

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
        }
    }
}
```

**Příklad**:

***Ukládat heslo v Azure Key Vault***

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete [v tématu datové sady v Azure Data Factory](concepts-datasets-linked-services.md). V této části najdete seznam vlastností, které Azure Database for PostgreSQL podporuje v datových sadách.

Chcete-li kopírovat data z Azure Database for PostgreSQL, nastavte vlastnost Type datové sady na **AzurePostgreSqlTable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type datové sady musí být nastavená na **AzurePostgreSqlTable** . | Ano |
| tableName | Název tabulky | Ne (Pokud je zadáno "dotaz" ve zdroji aktivity) |

**Příklad**:

```json
{
    "name": "AzurePostgreSqlDataset",
    "properties": {
        "type": "AzurePostgreSqlTable",
        "linkedServiceName": {
            "referenceName": "<AzurePostgreSql linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností dostupných pro definování aktivit najdete v tématu [kanály a aktivity v Azure Data Factory](concepts-pipelines-activities.md). V této části najdete seznam vlastností podporovaných zdrojem Azure Database for PostgreSQL.

### <a name="azure-database-for-postgresql-as-source"></a>Azure Database for PostgreSql – as source

Chcete-li kopírovat data z Azure Database for PostgreSQL, nastavte typ zdroje v aktivitě kopírování na **AzurePostgreSqlSource**. V části **zdroj** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **AzurePostgreSqlSource** . | Ano |
| query | Pro čtení dat použijte vlastní dotaz SQL. Například: `SELECT * FROM mytable` nebo `SELECT * FROM "MyTable"` . Všimněte si, že v PostgreSQL se název entity považuje za nerozlišující velká a malá písmena, pokud není uvedeno v uvozovkách. | Ne (Pokud je určena vlastnost tableName v sadě dat) |

**Příklad**:

```json
"activities":[
    {
        "name": "CopyFromAzurePostgreSql",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<AzurePostgreSql input dataset name>",
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
                "type": "AzurePostgreSqlSource",
                "query": "<custom query e.g. SELECT * FROM mytable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-postgresql-as-sink"></a>Azure Database for PostgreSQL jako jímka

Chcete-li kopírovat data do Azure Database for PostgreSQL, v části **jímka** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type jímky aktivity kopírování musí být nastavená na **AzurePostgreSQLSink**. | Ano |
| preCopyScript | Zadejte dotaz SQL pro aktivitu kopírování, která se má provést před zápisem dat do Azure Database for PostgreSQL při každém spuštění. Tuto vlastnost můžete použít k vyčištění předem načtených dat. | Ne |
| writeMethod | Metoda použitá k zápisu dat do Azure Database for PostgreSQL.<br>Povolené hodnoty jsou: **CopyCommand** (Preview, což je více výkonných), **BulkInsert** (výchozí). | Ne |
| writeBatchSize | Počet řádků načtených do Azure Database for PostgreSQL na dávku.<br>Povolená hodnota je celé číslo, které představuje počet řádků. | Ne (výchozí hodnota je 1 000 000) |
| writeBatchTimeout | Počkejte, než se operace dávkového vložení dokončí předtím, než vyprší časový limit.<br>Povolené hodnoty jsou řetězce TimeSpan. Příklad je 00:30:00 (30 minut). | Ne (výchozí hodnota je 00:30:00) |

**Příklad**:

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForPostgreSQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure PostgreSQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzurePostgreSQLSink",
                "preCopyScript": "<custom SQL script>",
                "writeMethod": "CopyCommand",
                "writeBatchSize": 1000000
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Mapování vlastností toku dat

Při transformaci dat v toku mapování dat můžete číst a zapisovat do tabulek z Azure Database for PostgreSQL. Další informace najdete v tématu transformace [zdroje](data-flow-source.md) a [transformace jímky](data-flow-sink.md) v tématu mapování toků dat. Můžete použít datovou sadu Azure Database for PostgreSQL nebo [vloženou datovou sadu](data-flow-source.md#inline-datasets) jako typ zdroje a jímky.

### <a name="source-transformation"></a>Transformace zdroje

V níže uvedené tabulce jsou uvedeny vlastnosti podporované zdrojem Azure Database for PostgreSQL. Tyto vlastnosti můžete upravit na kartě **Možnosti zdrojového kódu** .

| Název | Popis | Povinné | Povolené hodnoty | Vlastnost skriptu toku dat |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Tabulka | Pokud vyberete možnost tabulka jako vstup, tok dat načte všechna data z tabulky zadané v datové sadě. | Ne | - |*(pouze pro vloženou datovou sadu)*<br>tableName |
| Dotaz | Pokud jako vstup vyberete dotaz, zadejte dotaz SQL pro načtení dat ze zdroje, který přepíše všechny tabulky zadané v datové sadě. Použití dotazů je skvělý způsob, jak omezit řádky pro testování nebo vyhledávání.<br><br>Klauzule **ORDER by** není podporována, ale můžete nastavit úplný příkaz SELECT FROM. Můžete také použít uživatelsky definované funkce tabulky. **SELECT * FROM udfGetData ()** je UDF v SQL, který vrátí tabulku, kterou můžete použít v toku dat.<br>Příklad dotazu: `select * from mytable where customerId > 1000 and customerId < 2000` nebo `select * from "MyTable"` . Všimněte si, že v PostgreSQL se název entity považuje za nerozlišující velká a malá písmena, pokud není uvedeno v uvozovkách.| No | Řetězec | query |
| Velikost dávky | Určete velikost dávky pro velké objemy dat v dávkách. | Ne | Integer | batchSize |
| Úroveň izolace | Vyberte jednu z následujících úrovní izolace:<br>– Čtení potvrzeno<br>-Čtení nepotvrzeno (výchozí)<br>-Opakované čtení<br>– Serializovatelný<br>-None (ignorovat úroveň izolace) | Ne | <small>READ_COMMITTED<br/>READ_UNCOMMITTED<br/>REPEATABLE_READ<br/>SERIALIZOVATELNÝ<br/>NTATO</small> |isolationLevel |

#### <a name="azure-database-for-postgresql-source-script-example"></a>Příklad zdrojového skriptu Azure Database for PostgreSQL

Pokud jako typ zdroje použijete Azure Database for PostgreSQL, je k němu přidružen skript toku dat:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    isolationLevel: 'READ_UNCOMMITTED',
    query: 'select * from mytable',
    format: 'query') ~> AzurePostgreSQLSource
```

### <a name="sink-transformation"></a>Transformace jímky

V níže uvedené tabulce jsou uvedeny vlastnosti, které Azure Database for PostgreSQL jímka podporuje. Tyto vlastnosti můžete upravit na kartě **Možnosti jímky** .

| Název | Popis | Povinné | Povolené hodnoty | Vlastnost skriptu toku dat |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Update – metoda | Určete, jaké operace jsou pro cíl databáze povolené. Ve výchozím nastavení je povolen pouze vkládání.<br>Aby bylo možné aktualizovat, Upsert nebo odstraňovat řádky, je nutné transformaci řádků pro tyto akce označit [změnou řádku](data-flow-alter-row.md) . | Ano | `true` nebo `false` | lze odstranit <br/>vložitelný <br/>aktualizovatelné <br/>upsertable |
| Klíčové sloupce | Pro aktualizace, upsertuje a DELETE musí být klíčové sloupce nastavené tak, aby určily, který řádek se má změnit.<br>Název sloupce, který vyberete jako klíč, bude použit jako součást následné aktualizace, Upsert a DELETE. Proto je nutné vybrat sloupec, který existuje v mapování jímky. | Ne | Pole | keys |
| Přeskočit zápis klíčových sloupců | Pokud chcete hodnotu nezazapisovat do sloupce klíč, zaškrtněte políčko Přeskočit zápis klíčových sloupců. | Ne | `true` nebo `false` | skipKeyWrites |
| Akce tabulky |Určuje, zda mají být před zápisem znovu vytvořeny nebo odebrány všechny řádky z cílové tabulky.<br>- **Žádné**: v tabulce se neprovede žádná akce.<br>- **Znovu vytvořit**: tabulka se vynechá a znovu vytvoří. Požadováno při dynamickém vytváření nové tabulky.<br>- **Zkrátit**: všechny řádky z cílové tabulky se odeberou. | Ne | `true` nebo `false` | znovu vytvořit<br/>zkrátit |
| Velikost dávky | Určete, kolik řádků se má v každé dávce zapsat. Větší velikosti dávek zlepšují kompresi a optimalizaci paměti, ale při ukládání dat do mezipaměti riskuje výjimky z paměti. | Ne | Integer | batchSize |
| Skripty před a po SQL | Zadejte víceřádkové skripty SQL, které se spustí před (před zpracováním) a po (po zpracování) se zapisují do databáze jímky. | No | Řetězec | preSQLs<br>postSQLs |

#### <a name="azure-database-for-postgresql-sink-script-example"></a>Příklad skriptu jímky Azure Database for PostgreSQL

Když použijete Azure Database for PostgreSQL jako typ jímky, je přidružený skript toku dat:

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
    skipDuplicateMapOutputs: true) ~> AzurePostgreSQLSink
```

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Další informace o vlastnostech naleznete [v tématu Lookup Activity in Azure Data Factory](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Azure Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
