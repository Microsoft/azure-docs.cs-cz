---
title: Kopírování dat do a z Azure Database for PostgreSQL pomocí Azure Data Factory | Microsoft Docs
description: Naučte se, jak kopírovat data do a z Azure Database for PostgreSQL pomocí aktivity kopírování v kanálu Azure Data Factory.
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
ms.openlocfilehash: d6b860f43abae2283b4889bff0913bac25c821f5
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2019
ms.locfileid: "71017785"
---
# <a name="copy-data-to-and-from-azure-database-for-postgresql-using-azure-data-factory"></a>Kopírování dat do a z Azure Database for PostgreSQL pomocí Azure Data Factory

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory pro kopírování dat ze služby Azure Database for PostgreSQL. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

Tento konektor je specializovaný pro [službu Azure Database for PostgreSQL](../postgresql/overview.md). Pokud chcete kopírovat data z obecné databáze PostgreSQL místně nebo v cloudu, použijte [konektor PostgreSQL](connector-postgresql.md).

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor Azure Database for PostgreSQL se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou zdrojovou maticí](copy-activity-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)

Můžete kopírovat data ze Azure Database for PostgreSQL na jakékoli podporovaného úložiště dat jímky. Nebo můžete zkopírovat data z libovolného podporovaného zdrojového úložiště dat do Azure Database for PostgreSQL. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Poskytuje integrované ovladače chcete umožnit připojení k Azure Data Factory, proto není nutné ručně nainstalovat všechny ovladače používání tohoto konektoru.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování konkrétní entity služby Data Factory ke službě Azure Database for postgresql – konektoru.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Následující vlastnosti jsou podporovány pro službu Azure Database for postgresql –, propojené služby:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavená na: **AzurePostgreSql** | Ano |
| connectionString | ODBC připojovací řetězec služby pro připojení ke službě Azure Database for PostgreSQL.<br/>Označte toto pole jako SecureString a bezpečně ho uložte do Data Factory. Můžete také do Azure Key Vault umístit heslo a načíst `password` konfiguraci z připojovacího řetězce. Další podrobnosti najdete v následujících ukázkách a [přihlašovací údaje úložiště v Azure Key Vault](store-credentials-in-key-vault.md) článku. | Ano |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. (Pokud je vaše úložiště dat se nachází v privátní síti), můžete použít prostředí Azure Integration Runtime nebo modul Integration Runtime. Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. |Ne |

Připojovací řetězec je `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`. Další vlastnosti, které můžete nastavit na váš případ:

| Vlastnost | Popis | Možnosti | Požaduje se |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| Metoda ovladač používá k šifrování dat posílaných mezi ovladač a databázový server. Např.:`EncryptionMethod=<0/1/6>;`| 0 (žádné šifrování) **(výchozí)** / 1 (SSL) / 6 (RequestSSL) | Ne |
| ValidateServerCertificate (virtuální čipové karty) | Určuje, jestli ovladač ověřil certifikát, který je odesláno serverem databáze, když je povoleno šifrování protokolu SSL (metoda šifrování = 1). Např.:`ValidateServerCertificate=<0/1>;`| 0 (zakázáno) **(výchozí)** / 1 (povoleno) | Ne |

**Příklad:**

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
            }
        }
    }
}
```

**Příklad: uložení hesla v Azure Key Vault**

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;"
            },
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. Tato část obsahuje seznam vlastností, které podporuje – Azure Database for postgresql – datové sady.

Ke kopírování dat ze služby Azure Database for PostgreSQL, nastavte vlastnost typ datové sady na **AzurePostgreSqlTable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type datové sady musí být nastavená na: **AzurePostgreSqlTable** | Ano |
| tableName | Název tabulky. | Ne (když je zadán zdroj aktivity "query") |

**Příklad**

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností, které podporuje – Azure Database for postgresql – zdroje.

### <a name="azure-database-for-postgresql-as-source"></a>Azure Database for PostgreSql jako zdroj

Ke kopírování dat ze služby Azure Database for PostgreSQL, nastavte typ zdroje v aktivitě kopírování do **AzurePostgreSqlSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type zdroje aktivity kopírování musí být nastavená na: **AzurePostgreSqlSource** | Ano |
| query | Použijte vlastní dotaz SQL číst data. Například: `"SELECT * FROM MyTable"`. | Ne (když je "tableName" v datové sadě zadán) |

**Příklad:**

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

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type jímky aktivity kopírování musí být nastavená na: **AzurePostgreSQLSink** | Ano |
| preCopyScript | Zadejte dotaz SQL pro aktivitu kopírování, která má být provedena před zápisem dat do Azure Database for PostgreSQL při každém spuštění. Tuto vlastnost můžete použít k vyčištění předem načtených dat. | Ne |
| writeBatchSize | Když velikost vyrovnávací paměti dosáhne writeBatchSize, vloží data do tabulky Azure Database for PostgreSQL.<br>Povolená hodnota je celé číslo představující počet řádků. | Ne (výchozí hodnota je 10 000) |
| writeBatchTimeout | Počkejte, než se operace dávkového vložení dokončí předtím, než vyprší časový limit.<br>Povolené hodnoty jsou TimeSpan. Příklad je 00:30:00 (30 minut). | Ne (výchozí hodnota je 00:00:30) |

**Příklad:**

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

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
