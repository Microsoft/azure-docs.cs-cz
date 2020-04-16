---
title: Kopírování dat z přechodu k podrobnostem pomocí Azure Data Factory
description: Zjistěte, jak zkopírovat data z přechodu na podporovaná úložiště dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: jingwang
ms.openlocfilehash: 050037748969fe76dd1be9db80d68fb23ccd7940
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417390"
---
# <a name="copy-data-from-drill-using-azure-data-factory"></a>Kopírování dat z přechodu k podrobnostem pomocí Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak použít aktivitu kopírování v Azure Data Factory ke kopírování dat z přejídat. Vychází z článku [přehledu aktivity kopírování,](copy-activity-overview.md) který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor vrtáku je podporován pro následující činnosti:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)

Data můžete zkopírovat z přejídat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitou kopírování, naleznete v tabulce [Podporovaná úložiště dat.](copy-activity-overview.md#supported-data-stores-and-formats)

Azure Data Factory poskytuje integrovaný ovladač pro povolení připojení, proto není nutné ručně instalovat žádný ovladač pomocí tohoto konektoru.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit Factory dat specifických pro konektor drill.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro službu Propojenou s přechodem na podrobnosti jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na: **Přejít** | Ano |
| připojovací řetězec | Připojovací řetězec ODBC pro připojení k programu Drill. <br/>Můžete také umístit heslo do azure `pwd` key vault a vyžádat konfiguraci z připojovacího řetězce. Další podrobnosti najdete v následujících ukázkách a [přihlašovacích údajích úložiště v](store-credentials-in-key-vault.md) článku Azure Key Vault. | Ano |
| connectVia | [Prostředí Integrace Runtime,](concepts-integration-runtime.md) které se má použít k připojení k úložišti dat. Další informace naleznete v části [Požadavky.](#prerequisites) Pokud není zadán, používá výchozí Azure Integration Runtime. |Ne |

**Příklad:**

```json
{
    "name": "DrillLinkedService",
    "properties": {
        "type": "Drill",
        "typeProperties": {
            "connectionString": "ConnectionType=Direct;Host=<host>;Port=<port>;AuthenticationType=Plain;UID=<user name>;PWD=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Příklad: ukládání hesla v trezoru klíčů Azure**

```json
{
    "name": "DrillLinkedService",
    "properties": {
        "type": "Drill",
        "typeProperties": {
            "connectionString": "ConnectionType=Direct;Host=<host>;Port=<port>;AuthenticationType=Plain;UID=<user name>;",
            "pwd": { 
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


## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datových sad.](concepts-datasets-linked-services.md) Tato část obsahuje seznam vlastností podporovaných datovou sadou Drill.

Chcete-li zkopírovat data z přejímky, nastavte vlastnost type datové sady na **DrillTable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady musí být nastavena na: **DrillTable** | Ano |
| Schématu | Název schématu. |Ne (pokud je zadán "dotaz" ve zdroji aktivity)  |
| tabulka | Název tabulky. |Ne (pokud je zadán "dotaz" ve zdroji aktivity)  |
| tableName | Název tabulky se schématem. Tato vlastnost je podporována pro zpětnou kompatibilitu. Použití `schema` `table` a pro nové pracovní zatížení. | Ne (pokud je zadán "dotaz" ve zdroji aktivity) |

**Příklad**

```json
{
    "name": "DrillDataset",
    "properties": {
        "type": "DrillTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Drill linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných zdrojem přechodu k podrobnostem.

### <a name="drillsource-as-source"></a>DrillSource jako zdroj

Chcete-li kopírovat data z přejímky, nastavte typ zdroje v aktivitě kopírování na **DrillSource**. V části **zdroje** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavena **na: DrillSource** | Ano |
| query | Ke čtení dat použijte vlastní dotaz SQL. Například: `"SELECT * FROM MyTable"`. | Ne (pokud je v datové sadě zadán "název_tabulky") |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromDrill",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Drill input dataset name>",
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
                "type": "DrillSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v Azure Data Factory najdete v [tématu podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
