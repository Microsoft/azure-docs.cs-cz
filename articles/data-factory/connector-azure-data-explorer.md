---
title: Kopírování dat do nebo z Průzkumníku dat Azure pomocí Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data do nebo z Průzkumníku dat Azure s využitím aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: orspod
ms.openlocfilehash: 8f2a7a953ce2964645c281d9454a73b0cf1a8ff6
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55747184"
---
# <a name="copy-data-to-or-from-azure-data-explorer-using-azure-data-factory"></a>Kopírování dat do nebo z Průzkumníku dat Azure pomocí Azure Data Factory

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory pro kopírování dat do nebo z [Průzkumník dat Azure](../data-explorer/data-explorer-overview.md). Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Kopírovat data ze všech podporovaných zdrojů úložišť dat do Průzkumníku dat Azure. Můžete také kopírování dat z Průzkumníka služby Azure Data do jakékoli podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md) tabulky.

>[!NOTE]
>V současné době kopírování dat do a z Průzkumníku dat Azure z/do úložiště dat v místním prostředí pomocí modul Integration Runtime se zatím nepodporuje.

Průzkumník dat Azure konektor umožňuje postupujte takto:

* Kopírování dat pomocí ověřování tokenu aplikací Azure Active Directory (Azure AD) s **instanční objekt služby**.
* Jako zdroj načtení dat pomocí dotazů KQL (Kusto).
* Jako jímka připojte data do cílové tabulky.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní konektor Průzkumník dat Azure.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Průzkumník dat Azure, propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** musí být vlastnost nastavena na **AzureDataExplorer** | Ano |
| endpoint | Adresa URL koncového bodu clusteru Průzkumník dat Azure, ve formátu jako `https://<clusterName>.<regionName>.kusto.windows.net `. | Ano |
| databáze | Název databáze. | Ano |
| tenant | Zadejte informace o tenantovi (domény ID tenanta nebo název) v rámci které se nachází vaše aplikace. Načtení ukázáním pomocí myši v pravém horním rohu webu Azure portal. | Ano |
| servicePrincipalId | Zadejte ID klienta vaší aplikace. | Ano |
| servicePrincipalKey | Zadejte klíč aplikace. Označte toto pole jako **SecureString** bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano |

**Příklad vlastnosti propojené služby:**

```json
{
    "name": "AzureDataExplorerLinkedService",
    "properties": {
        "type": "AzureDataExplorer",
        "typeProperties": {
            "endpoint": "https://<clusterName>.<regionName>.kusto.windows.net ",
            "database": "<database name>",
            "tenant": "<tenant name/id e.g. microsoft.onmicrosoft.com>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. Tato část obsahuje seznam vlastností, které jsou podporovány v objektu DataSet Průzkumník dat Azure.

Pro kopírování dat do Průzkumníku dat Azure, nastavte vlastnost typ datové sady na **AzureDataExplorerTable**.

Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** musí být vlastnost nastavena na **AzureDataExplorerTable** | Ano |
| tabulka | Název tabulky, která odkazuje propojenou službu. | Ano pro jímku; Ne pro zdroj |

**Příklad vlastnosti datové sady**

```json
{
   "name": "AzureDataExplorerDataset",
    "properties": {
        "type": "AzureDataExplorerTable",
        "linkedServiceName": {
            "referenceName": "<Azure Data Explorer linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "table": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností, které podporuje Průzkumník služby Azure Data zdroje a jímky.

### <a name="azure-data-explorer-as-source"></a>Průzkumník služby Azure Data jako zdroj

Ke zkopírování dat z Průzkumníka služby Azure Data, nastavte **typ** vlastnosti ve zdroji aktivitu kopírování k **AzureDataExplorerSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** vlastnost zdroje aktivity kopírování musí být nastavena na: **AzureDataExplorerSource** | Ano |
| query | Podle požadavku jen pro čtení [KQL formátu](/azure/kusto/query/). Použijte vlastní dotaz KQL jako odkaz. | Ano |
| queryTimeout | Doba čekání před požadavkem dotazu vyprší časový limit. Výchozí hodnota je 10 minut (00:10:00); povolené maximum je 1 hodina (01: 00:00). | Ne |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataExplorerSource",
                "query": "TestTable1 | take 10",
                "queryTimeout": "00:10:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Explorer input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

### <a name="azure-data-explorer-as-sink"></a>Průzkumník služby Azure Data jako jímka

Pro kopírování dat do Průzkumníku dat Azure, nastavte vlastnost typ v jímky aktivity kopírování k **AzureDataExplorerSink**. Následující vlastnosti jsou podporovány v aktivitě kopírování **jímky** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** vlastnost jímky aktivity kopírování musí být nastavena na: **AzureDataExplorerSink** | Ano |
| ingestionMappingName | Název předem vytvořené [sdíleného svazku clusteru mapování](/azure/kusto/management/mappings#csv-mapping) v tabulce Kusto. Pokud chcete namapovat sloupce ze zdroje, které chcete prozkoumat Data Azure, můžete použít také aktivita kopírování [mapování sloupců](copy-activity-schema-and-type-mapping.md). | Ne |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyToAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataExplorerSink",
                "ingestionMappingName": "<optional csv mapping name>"
            }
        },
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Explorer output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).