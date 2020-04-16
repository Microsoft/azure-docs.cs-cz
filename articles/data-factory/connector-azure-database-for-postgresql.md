---
title: Kopírování dat do a z databáze Azure pro PostgreSQL
description: Zjistěte, jak kopírovat data do a z Databáze Azure pro PostgreSQL pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/16/2019
ms.openlocfilehash: b85e72ae6698cd9fa018c940e158bfcf25279ed5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81410469"
---
# <a name="copy-data-to-and-from-azure-database-for-postgresql-by-using-azure-data-factory"></a>Kopírování dat do a z databáze Azure pro PostgreSQL pomocí Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí funkce aktivity kopírování v Azure Data Factory kopírovat data z Databáze Azure pro PostgreSQL. Vychází z [aktivity Kopírování v](copy-activity-overview.md) azure data factory článku, který představuje obecný přehled aktivity kopírování.

Tento konektor se specializuje na [službu Azure Database for PostgreSQL](../postgresql/overview.md). Chcete-li kopírovat data z obecné databáze PostgreSQL umístěné místně nebo v cloudu, použijte [konektor PostgreSQL](connector-postgresql.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Azure Database for PostgreSQL je podporovaný pro následující aktivity:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)

Data z Azure Database for PostgreSQL můžete zkopírovat do libovolného podporovaného úložiště dat jímky. Nebo můžete zkopírovat data z libovolného úložiště dat podporovaného zdroje do Databáze Azure pro PostgreSQL. Seznam úložišť dat, které aktivita kopírování podporuje jako zdroje a propady, naleznete v tabulce [Podporovaná data.](copy-activity-overview.md#supported-data-stores-and-formats)

Azure Data Factory poskytuje integrovaný ovladač pro povolení připojení. Proto není nutné ručně instalovat žádný ovladač používat tento konektor.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit Datové továrny specifické pro konektor Azure Database for PostgreSQL.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu Azure Database for PostgreSQL jsou podporované následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na: **AzurePostgreSql**. | Ano |
| připojovací řetězec | Připojovací řetězec ODBC pro připojení k Azure Database pro PostgreSQL.<br/>Můžete také umístit heslo v Azure Key `password` Vault a vyžádat konfiguraci z připojovacího řetězce. Další podrobnosti najdete v následujících ukázkách a [přihlašovacích údajích úložiště v azure key vaultu.](store-credentials-in-key-vault.md) | Ano |
| connectVia | Tato vlastnost představuje [integrační za běhu,](concepts-integration-runtime.md) který se má použít k připojení k úložišti dat. Můžete použít Azure Integration Runtime nebo Self-hostované integrace Runtime (pokud vaše úložiště dat se nachází v privátní síti). Pokud není zadán, používá výchozí Azure Integration Runtime. |Ne |

Typický připojovací řetězec je `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`. Zde jsou další vlastnosti, které můžete nastavit pro váš případ:

| Vlastnost | Popis | Možnosti | Požaduje se |
|:--- |:--- |:--- |:--- |
| Metoda šifrování (EM)| Metoda, kterou ovladač používá k šifrování dat odeslaných mezi ovladačem a databázovým serverem. Například`EncryptionMethod=<0/1/6>;`| 0 (bez šifrování) **(výchozí)** / 1 (SSL) / 6 (Requestssl) | Ne |
| Ověřit serverový certifikát (VSC) | Určuje, zda ovladač ověří certifikát odeslaný databázovým serverem, pokud je povoleno šifrování SSL (Metoda šifrování =1). Například`ValidateServerCertificate=<0/1>;`| 0 (zakázáno) **(výchozí)** / 1 (povoleno) | Ne |

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

***Uložit heslo v trezoru klíčů Azure***

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, najdete [v tématu Datové sady v Azure Data Factory](concepts-datasets-linked-services.md). Tato část obsahuje seznam vlastností, které Azure Database pro PostgreSQL podporuje v datových sadách.

Chcete-li zkopírovat data z Databáze Azure pro PostgreSQL, nastavte vlastnost type datové sady na **AzurePostgreSqlTable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady musí být nastavena na **AzurePostgreSqlTable** | Ano |
| tableName | Název tabulky | Ne (pokud je zadán "dotaz" ve zdroji aktivity) |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete [v tématu Kanály a aktivity v Azure Data Factory](concepts-pipelines-activities.md). Tato část obsahuje seznam vlastností podporovaných azure databáze pro postgresql zdroj.

### <a name="azure-database-for-postgresql-as-source"></a>Databáze Azure pro PostgreSql jako zdroj

Chcete-li zkopírovat data z Databáze Azure pro PostgreSQL, nastavte typ zdroje v aktivitě kopírování na **AzurePostgreSqlSource**. V části **zdroje** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavena na **AzurePostgreSqlSource** | Ano |
| query | Ke čtení dat použijte vlastní dotaz SQL. Příklad: `"SELECT * FROM MyTable"` | Ne (pokud je zadána vlastnost tableName v datové sadě) |

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
                "query": "<custom query e.g. SELECT * FROM MyTable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-postgresql-as-sink"></a>Databáze Azure pro PostgreSQL jako jímky

Chcete-li zkopírovat data do databáze Azure pro PostgreSQL, jsou podporovány následující vlastnosti v části **jímky** aktivity kopírování:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type jímky aktivity kopírování musí být nastavena na **AzurePostgreSQLSink**. | Ano |
| preCopyScript | Zadejte dotaz SQL pro aktivitu kopírování, která se má spustit před zápisem dat do databáze Azure pro PostgreSQL v každém spuštění. Tuto vlastnost můžete použít k vyčištění přednačtených dat. | Ne |
| writeBatchSize | Vloží data do tabulky Azure Database for PostgreSQL, když velikost vyrovnávací paměti dosáhne writeBatchSize.<br>Povolená hodnota je celé číslo, které představuje počet řádků. | Ne (výchozí hodnota je 10 000) |
| writeBatchTimeout | Počkejte čas pro operaci dávkové vložení k dokončení před časovým výpadkem.<br>Povolené hodnoty jsou řetězce Timespan. Příkladem je 00:30:00 (30 minut). | Ne (výchozí hodnota je 00:00:30) |

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
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Další informace o vlastnostech najdete [v tématu Aktivita vyhledávání v Azure Data Factory](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v Azure Data Factory najdete v [tématu Podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
