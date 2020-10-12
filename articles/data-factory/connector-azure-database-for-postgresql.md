---
title: Kopírování dat do a z Azure Database for PostgreSQL
description: Naučte se, jak kopírovat data do a z Azure Database for PostgreSQL pomocí aktivity kopírování v kanálu Azure Data Factory.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "81410469"
---
# <a name="copy-data-to-and-from-azure-database-for-postgresql-by-using-azure-data-factory"></a>Kopírování dat do a z Azure Database for PostgreSQL pomocí Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí funkce kopírování aktivity v nástroji Azure Data Factory kopírovat data z Azure Database for PostgreSQL. Sestaví se na [aktivitu kopírování v Azure Data Factory](copy-activity-overview.md) článku, která představuje obecný přehled aktivity kopírování.

Tento konektor je specializovaný pro [službu Azure Database for PostgreSQL](../postgresql/overview.md). Pokud chcete kopírovat data z obecné databáze PostgreSQL místně nebo v cloudu, použijte [konektor PostgreSQL](connector-postgresql.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Azure Database for PostgreSQL se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)

Data z Azure Database for PostgreSQL můžete kopírovat do libovolného podporovaného úložiště dat jímky. Nebo můžete zkopírovat data z libovolného podporovaného zdrojového úložiště dat do Azure Database for PostgreSQL. Seznam úložišť dat, která aktivita kopírování podporuje jako zdroje a jímky, najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

Azure Data Factory poskytuje integrovaný ovladač, který umožňuje připojení. Proto pro použití tohoto konektoru nemusíte ručně instalovat žádný ovladač.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které slouží k definování Data Factory entit specifických pro Azure Database for PostgreSQL konektor.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu Azure Database for PostgreSQL jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type musí být nastavená na: **AzurePostgreSql**. | Ano |
| připojovací řetězec | Připojovací řetězec ODBC pro připojení k Azure Database for PostgreSQL.<br/>Můžete také vložit heslo do Azure Key Vault a získat `password` konfiguraci z připojovacího řetězce. Další podrobnosti najdete v následujících ukázkách a [přihlašovací údaje pro uložení v Azure Key Vault](store-credentials-in-key-vault.md) . | Ano |
| connectVia | Tato vlastnost představuje [prostředí Integration runtime](concepts-integration-runtime.md) , které se má použít pro připojení k úložišti dat. Můžete použít Azure Integration Runtime nebo místní Integration Runtime (Pokud je úložiště dat umístěné v privátní síti). Pokud není zadaný, použije se výchozí Azure Integration Runtime. |No |

Typický připojovací řetězec je `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>` . Tady je více vlastností, které můžete nastavit na váš případ:

| Vlastnost | Popis | Možnosti | Požaduje se |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| Metoda, kterou ovladač používá k šifrování dat posílaných mezi ovladačem a databázovým serverem. Například  `EncryptionMethod=<0/1/6>;`| 0 (bez šifrování) **(výchozí)** /1 (SSL)/6 (RequestSSL) | No |
| ValidateServerCertificate (VSC) | Určuje, zda ovladač ověřuje certifikát, který je odeslán databázovým serverem, pokud je povoleno šifrování SSL (metoda šifrování = 1). Například  `ValidateServerCertificate=<0/1>;`| 0 (zakázáno) **(výchozí)** /1 (povoleno) | No |

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
| query | Pro čtení dat použijte vlastní dotaz SQL. Příklad: `"SELECT * FROM MyTable"` | Ne (Pokud je určena vlastnost tableName v sadě dat) |

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

### <a name="azure-database-for-postgresql-as-sink"></a>Azure Database for PostgreSQL jako jímka

Chcete-li kopírovat data do Azure Database for PostgreSQL, v části **jímka** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type jímky aktivity kopírování musí být nastavená na **AzurePostgreSQLSink**. | Ano |
| preCopyScript | Zadejte dotaz SQL pro aktivitu kopírování, která se má provést před zápisem dat do Azure Database for PostgreSQL při každém spuštění. Tuto vlastnost můžete použít k vyčištění předem načtených dat. | No |
| writeBatchSize | Když velikost vyrovnávací paměti dosáhne writeBatchSize, vloží data do tabulky Azure Database for PostgreSQL.<br>Povolená hodnota je celé číslo, které představuje počet řádků. | Ne (výchozí hodnota je 10 000) |
| writeBatchTimeout | Počkejte, než se operace dávkového vložení dokončí předtím, než vyprší časový limit.<br>Povolené hodnoty jsou řetězce TimeSpan. Příklad je 00:30:00 (30 minut). | Ne (výchozí hodnota je 00:00:30) |

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

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Další informace o vlastnostech naleznete [v tématu Lookup Activity in Azure Data Factory](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Azure Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
