---
title: Kopírování dat do vyhledávacího indexu
description: Přečtěte si, jak nabízená nebo zkopírovaná data do indexu vyhledávání Azure pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/13/2019
ms.openlocfilehash: 418026d5569cd7e4a7c5239f99650833b1b9514d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75892940"
---
# <a name="copy-data-to-an-azure-cognitive-search-index-using-azure-data-factory"></a>Kopírování dat do indexu Azure Cognitive Search pomocí Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-azure-search-connector.md)
> * [Aktuální verze](connector-azure-search.md)

Tento článek popisuje, jak použít aktivitu kopírování v Azure Data Factory ke kopírování dat do indexu Azure Cognitive Search. Vychází z článku [přehledu aktivity kopírování,](copy-activity-overview.md) který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Do vyhledávacího indexu můžete kopírovat data z libovolného podporovaného zdrojového úložiště dat. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitou kopírování, naleznete v tabulce [Podporovaná úložiště dat.](copy-activity-overview.md#supported-data-stores-and-formats)

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit datové továrny specifických pro konektor Azure Cognitive Search.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu Azure Cognitive Search jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na: **AzureSearch.** | Ano |
| url | Adresa URL vyhledávací služby. | Ano |
| key | Klíč správce vyhledávací služby. Označte toto pole jako SecureString bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Ano |
| connectVia | [Prostředí Integrace Runtime,](concepts-integration-runtime.md) které se má použít k připojení k úložišti dat. Můžete použít Azure Integration Runtime nebo Self-hostované integrace Runtime (pokud vaše úložiště dat se nachází v privátní síti). Pokud není zadán, používá výchozí Azure Integration Runtime. |Ne |

> [!IMPORTANT]
> Při kopírování dat z úložiště cloudových dat do indexu vyhledávání ve propojené službě Azure Cognitive Search potřebujete odkazovat na prostředí Azure Integration Runtime s explicitní oblastí v connactVia. Nastavte oblast jako oblast, ve které se nachází vyhledávací služba. Další informace najdete v [prostředí Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime).

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datových sad.](concepts-datasets-linked-services.md) Tato část obsahuje seznam vlastností podporovaných datovou sadou Azure Cognitive Search.

Chcete-li zkopírovat data do Azure Cognitive Search, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady musí být nastavena na: **AzureSearchIndex.** | Ano |
| název_indexu | Název indexu vyhledávání. Data Factory nevytvoří index. Index musí existovat v Azure Cognitive Search. | Ano |

**Příklad:**

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "typeProperties" : {
            "indexName": "products"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Cognitive Search linked service name>",
            "type": "LinkedServiceReference"
        }
   }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných zdrojem Azure Cognitive Search.

### <a name="azure-cognitive-search-as-sink"></a>Azure Kognitivní vyhledávání jako umyvadlo

Chcete-li zkopírovat data do Azure Cognitive Search, nastavte typ zdroje v aktivitě kopírování na **AzureSearchIndexSink**. V části **jímka** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavena na: **AzureSearchIndexSink.** | Ano |
| writeBehavior | Určuje, zda má být sloučen nebo nahrazen, pokud dokument již v indexu existuje. Viz [WriteBehavior vlastnost](#writebehavior-property).<br/><br/>Povolené hodnoty jsou: **Sloučení** (výchozí) a **Nahrání**. | Ne |
| writeBatchSize | Nahraje data do indexu vyhledávání, když velikost vyrovnávací paměti dosáhne writeBatchSize. Podrobnosti naleznete v [vlastnosti WriteBatchSize.](#writebatchsize-property)<br/><br/>Povolené hodnoty jsou: celé číslo 1 až 1 000; výchozí hodnota je 1000. | Ne |

### <a name="writebehavior-property"></a>WriteBehavior, vlastnost

AzureSearchSink upserts při zápisu dat. Jinými slovy při psaní dokumentu, pokud klíč dokumentu již existuje v indexu vyhledávání, Azure Cognitive Search aktualizuje existující dokument spíše než vyvolání výjimky konfliktu.

AzureSearchSink poskytuje následující dvě upsert chování (pomocí AzureSearch SDK):

- **Sloučení**: zkombinujte všechny sloupce v novém dokumentu s existujícím. Pro sloupce s hodnotou null v novém dokumentu je zachována hodnota v existujícím dokumentu.
- **Upload**: Nový dokument nahradí stávající dokument. U sloupců, které nejsou zadány v novém dokumentu, je hodnota nastavena na hodnotu null, zda je v existujícím dokumentu hodnota nenulové nebo ne.

Výchozí chování je **Sloučit**.

### <a name="writebatchsize-property"></a>WriteBatchSize, vlastnost

Služba Azure Cognitive Search podporuje psaní dokumentů jako dávkovou dávku. Dávka může obsahovat 1 až 1 000 akcí. Akce zpracovává jeden dokument k provedení operace nahrávání a sloučení.

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
                "referenceName": "<Azure Cognitive Search output dataset name>",
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

## <a name="data-type-support"></a>Podpora datového typu

Následující tabulka určuje, jestli je datový typ Azure Cognitive Search podporovaný nebo ne.

| Datový typ Azure Cognitive Search | Podporované v Azure kognitivní vyhledávání jímky |
| ---------------------- | ------------------------------ |
| Řetězec | Ano |
| Int32 | Ano |
| Int64 | Ano |
| Double | Ano |
| Logická hodnota | Ano |
| DataTimeOffset | Ano |
| Pole řetězců | Ne |
| GeografiePoint | Ne |

V současné době nejsou podporovány jiné datové typy, například ComplexType. Úplný seznam datových typů podporovaných Azure Cognitive Search najdete v [tématu Podporované datové typy (Azure Cognitive Search).](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v Azure Data Factory najdete v [tématu podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
