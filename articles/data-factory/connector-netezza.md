---
title: Kopírování dat z Netezza pomocí služby Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data z Netezza úložišť dat podporovaných jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: jingwang
ms.openlocfilehash: 676eac6853c8cead40cb702855090eac5e2ce7d8
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54025643"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>Kopírování dat z Netezza pomocí služby Azure Data Factory 

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory ke zkopírování dat z Netezza. Tento článek vychází [aktivita kopírování ve službě Azure Data Factory](copy-activity-overview.md), který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Kopírování dat z Netezza do jakékoli podporovaného úložiště dat jímky. Seznam dat ukládá podporovanou aktivitou kopírování jako zdroje a jímky, najdete v části [podporovaných úložišť dat a formáty](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory poskytuje integrovaný ovladač umožňující připojení. Není nutné ručně nainstalovat všechny ovladače, které chcete použít tento konektor.

## <a name="get-started"></a>Začínáme

Můžete vytvořit kanál, který používá aktivitu kopírování pomocí sady .NET SDK, Python SDK, Azure Powershellu, rozhraní REST API nebo šablony Azure Resource Manageru. Najdete v článku [kurz aktivity kopírování](quickstart-create-data-factory-dot-net.md) pro podrobné pokyny k vytvoření kanálu obsahujícího aktivitu kopírování.

Následující části obsahují podrobnosti o vlastnostech, které lze použít k definování entit služby Data Factory, které jsou specifické pro konektor Netezza.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Netezza propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** musí být vlastnost nastavena na **Netezza**. | Ano |
| připojovací řetězec | ODBC připojovací řetězec služby pro připojení k Netezza. Označte toto pole jako **SecureString** typ bezpečně uložit ve službě Data Factory. Můžete také [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) používat pro připojení k úložišti. Můžete vybrat v místním prostředí Integration Runtime nebo prostředí Azure Integration Runtime (Pokud vaše úložiště dat je veřejně dostupná). Pokud není zadán, použije se výchozí prostředí Azure Integration Runtime. |Ne |

Připojovací řetězec je `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`. Následující tabulka popisuje další vlastnosti, které můžete nastavit:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| SecurityLevel | Úroveň zabezpečení (SSL/TLS), který používá ovladač pro připojení k úložišti. Příklad: `SecurityLevel=preferredSecured`. Podporované hodnoty jsou:<br/>- **Pouze nezabezpečené** (**onlyUnSecured**): Ovladač nebude používat protokol SSL.<br/>- **Upřednostňovaný nezabezpečené (preferredUnSecured) (výchozí)**: Pokud server poskytuje možnost volby, ovladač nebude používat protokol SSL. <br/>- **Upřednostňovaný zabezpečené (preferredSecured)**: Pokud server poskytuje možnost volby, ovladač používá protokol SSL. <br/>- **Pouze zabezpečené (onlySecured)**: Ovladač nelze připojit, dokud je k dispozici připojení SSL. | Ne |
| Soubor_certifikátu_cú | Úplná cesta k certifikátu SSL, který se používá serverem. Příklad: `CaCertFile=<cert path>;`| Ano, pokud je povolen protokol SSL |

**Příklad**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
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

Tato část obsahuje seznam vlastností, které podporuje Netezza datové sady.

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, naleznete v tématu [datových sad](concepts-datasets-linked-services.md). 

Chcete-li kopírovat data z Netezza, nastavte **typ** vlastnosti datové sady na **NetezzaTable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady, musí být nastavená na: **NetezzaTable** | Ano |
| tableName | Název tabulky. | Ne (když je zadán zdroj aktivity "dotaz") |

**Příklad**

```json
{
    "name": "NetezzaDataset",
    "properties": {
        "type": "NetezzaTable",
        "linkedServiceName": {
            "referenceName": "<Netezza linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Tato část obsahuje seznam vlastností, které podporuje Netezza zdroje.

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v tématu [kanály](concepts-pipelines-activities.md). 

### <a name="netezza-as-source"></a>Netezza jako zdroj

Ke zkopírování dat z Netezza, nastavte **zdroj** typ v aktivitě kopírování do **NetezzaSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** musí být nastavena vlastnost zdroje aktivity kopírování **NetezzaSource**. | Ano |
| query | Použijte vlastní dotaz SQL číst data. Příklad: `"SELECT * FROM MyTable"` | Ne (když je "tableName" v datové sadě zadán) |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromNetezza",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Netezza input dataset name>",
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
                "type": "NetezzaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Další postup

Seznam úložišť dat, která aktivitu kopírování, která podporuje jako zdroje a jímky ve službě Azure Data Factory najdete v tématu [podporovaných úložišť dat a formáty](copy-activity-overview.md#supported-data-stores-and-formats).
