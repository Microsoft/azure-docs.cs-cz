---
title: Kopírování dat do indexu vyhledávání pomocí služby Azure Data Factory | Dokumentace Microsoftu
description: Další informace o tom, jak vložit nebo kopírování dat do indexu Azure search s využitím aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: aa6c6a35a66569d5db182e1871012b9697c2802c
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54023341"
---
# <a name="copy-data-to-an-azure-search-index-using-azure-data-factory"></a>Kopírování dat do indexu Azure Search pomocí Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-azure-search-connector.md)
> * [Aktuální verze](connector-azure-search.md)

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory ke zkopírování dat do indexu Azure Search. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Kopírovat data ze všech podporovaných zdrojů úložišť dat do indexu Azure Search. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory, které jsou specifické pro konektor Azure Search.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro službu Azure Search propojené se podporují následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na: **AzureSearch** | Ano |
| url | Adresa URL pro službu Azure Search. | Ano |
| key | Klíč správce pro službu Azure Search. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. (Pokud je vaše úložiště dat se nachází v privátní síti), můžete použít prostředí Azure Integration Runtime nebo modul Integration Runtime. Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. |Ne |

> [!IMPORTANT]
> Při kopírování dat z cloudového úložiště dat do indexu Azure Search ve službě Azure Search propojenou službu, musíte odkazovat modul Runtime integrace Azure s explicitní oblasti v connactVia. Nastavte oblast jako ten, který se nachází Azure Search. Další informace z [prostředí Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime).

**Příklad:**

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": {
                "type": "SecureString",
                "value": "<AdminKey>"
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady najdete v článku datové sady. Tato část obsahuje seznam vlastností, které podporuje datové sady Azure Search.

Můžete kopírovat data do Azure Search, nastavte vlastnost typ datové sady na **RelationalTable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady, musí být nastavená na: **AzureSearchIndex** | Ano |
| indexName | Název indexu Azure Search. Objekt pro vytváření dat, nevytvoří index. Index musí existovat ve službě Azure Search. | Ano |

**Příklad:**

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": {
            "referenceName": "<Azure Search linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties" : {
            "indexName": "products"
        }
   }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastnosti podporované službou Azure Search zdroje.

### <a name="azure-search-as-sink"></a>Služba Azure Search jako jímka

Můžete kopírovat data do Azure Search, nastavte typ zdroje v aktivitě kopírování do **AzureSearchIndexSink**. Následující vlastnosti jsou podporovány v aktivitě kopírování **jímky** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavená na: **AzureSearchIndexSink** | Ano |
| WriteBehavior | Určuje, jestli se má sloučit nebo nahradit, již existuje dokument v indexu. Zobrazit [WriteBehavior vlastnost](#writebehavior-property).<br/><br/>Povolené hodnoty jsou: **Sloučit** (výchozí), a **nahrát**. | Ne |
| WriteBatchSize | Nahrání dat do indexu Azure Search writeBatchSize dosáhne velikosti vyrovnávací paměti. Zobrazit [WriteBatchSize vlastnost](#writebatchsize-property) podrobnosti.<br/><br/>Povolené hodnoty jsou: celé číslo 1 až 1 000; Výchozí hodnota je 1000. | Ne |

### <a name="writebehavior-property"></a>Vlastnost WriteBehavior

Při zápisu dat AzureSearchSink upsertuje. Jinými slovy při zápisu dokumentu, pokud klíč dokumentu již existuje v indexu Azure Search, Azure Search aktualizuje stávající dokument namísto vyvolání výjimky konflikt.

AzureSearchSink poskytuje následujících dvou upsert chování (pomocí sady SDK AzureSearch):

- **Sloučit**: kombinovat všechny sloupce v nový dokument s existujícím. Pro sloupce s hodnotou null v nového dokumentu je hodnota v existujícím zachována.
- **Nahrát**: Nový dokument nahradí stávající. U sloupců není zadáno v nového dokumentu je hodnota nastavena na hodnotu null, zda je hodnota jiná než null v k existujícímu dokumentu nebo ne.

Výchozí chování je **sloučit**.

### <a name="writebatchsize-property"></a>Vlastnost WriteBatchSize

Služba Azure Search podporuje psaní dokumentů v dávce. Batch může obsahovat 1 až 1 000 akcí. Akce zpracovává jeden dokument k provedení této operace nahrávání/merge.

**Příklad:**

```json
"activities":[
    {
        "name": "CopyToAzureSearch",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Search output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSearchIndexSink",
                "writeBehavior": "Merge"
            }
        }
    }
]
```

### <a name="data-type-support"></a>Podpora typ dat

Následující tabulka určuje, zda je nebo není podporováno typem dat Azure Search.

| Azure Search datový typ | Podporované v Azure Search jímky |
| ---------------------- | ------------------------------ |
| Řetězec | Ano |
| Datový typ Int32 | Ano |
| Int64 | Ano |
| Double | Ano |
| Logická hodnota | Ano |
| DataTimeOffset | Ano |
| Pole řetězců | Ne |
| GeographyPoint | Ne |

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
