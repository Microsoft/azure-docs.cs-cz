---
title: Kopírovat data do indexu vyhledávání
description: Přečtěte si, jak vložit nebo zkopírovat data do indexu Azure Search pomocí aktivity kopírování v kanálu Azure Data Factory.
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
ms.openlocfilehash: dfa1ad318ccc9e891b646ec050f6a0776e108206
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "81418231"
---
# <a name="copy-data-to-an-azure-cognitive-search-index-using-azure-data-factory"></a>Kopírování dat do indexu služby Azure Kognitivní hledání pomocí Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-azure-search-connector.md)
> * [Aktuální verze](connector-azure-search.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data do indexu Azure Kognitivní hledání. Sestaví se v článku [Přehled aktivity kopírování](copy-activity-overview.md) , který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Data můžete kopírovat z libovolného podporovaného zdrojového úložiště dat do vyhledávacího indexu. Seznam úložišť dat, která jsou v rámci aktivity kopírování podporovaná jako zdroje a jímky, najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které slouží k definování Data Factory entit specifických pro Azure Kognitivní hledání Connector.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu Azure Kognitivní hledání se podporují tyto vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type musí být nastavená na: **AzureSearch** . | Ano |
| url | Adresa URL služby vyhledávání | Ano |
| key | Klíč správce pro vyhledávací službu. Označte toto pole jako SecureString, abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| connectVia | [Integration runtime](concepts-integration-runtime.md) , která se má použít pro připojení k úložišti dat Můžete použít Azure Integration Runtime nebo místní Integration Runtime (Pokud je úložiště dat umístěné v privátní síti). Pokud není zadaný, použije se výchozí Azure Integration Runtime. |No |

> [!IMPORTANT]
> Když kopírujete data z cloudového úložiště dat do indexu vyhledávání, v propojené službě Azure Kognitivní hledání musíte odkazovat Azure Integration Runtime s explicitní oblastí v connactVia. Nastavte oblast jako tu, kde se nachází vaše vyhledávací služba. Další informace o [Azure Integration runtime](concepts-integration-runtime.md#azure-integration-runtime).

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . V této části najdete seznam vlastností podporovaných datovou sadou Azure Kognitivní hledání.

Pokud chcete kopírovat data do Azure Kognitivní hledání, podporují se tyto vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type datové sady musí být nastavená na: **AzureSearchIndex** . | Ano |
| indexName | Název indexu hledání Data Factory nevytváří index. Index musí existovat v Azure Kognitivní hledání. | Ano |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností podporovaných zdrojem Azure Kognitivní hledání.

### <a name="azure-cognitive-search-as-sink"></a>Azure Kognitivní hledání jako jímka

Pokud chcete kopírovat data do Azure Kognitivní hledání, nastavte typ zdroje v aktivitě kopírování na **AzureSearchIndexSink**. V části **jímka** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type zdroje aktivity kopírování musí být nastavená na: **AzureSearchIndexSink** . | Ano |
| writeBehavior | Určuje, zda se má sloučit nebo nahradit, když dokument v indexu již existuje. Podívejte se na [vlastnost WriteBehavior](#writebehavior-property).<br/><br/>Povolené hodnoty jsou: **Merge** (výchozí) a **nahrání**. | No |
| writeBatchSize | Když velikost vyrovnávací paměti dosáhne writeBatchSize, nahraje data do indexu vyhledávání. Podrobnosti najdete ve [vlastnosti WriteBatchSize](#writebatchsize-property) .<br/><br/>Povolené hodnoty jsou: celé číslo 1 až 1 000; Výchozí hodnota je 1000. | No |

### <a name="writebehavior-property"></a>Vlastnost WriteBehavior

AzureSearchSink upsertuje při zápisu dat. Jinými slovy, když při psaní dokumentu už klíč dokumentu ve vyhledávacím indexu existuje, Azure Kognitivní hledání aktualizuje existující dokument, ale nevyvolává výjimku konfliktu.

AzureSearchSink poskytuje následující dvě chování Upsert (pomocí sady AzureSearch SDK):

- **Merge**: kombinovat všechny sloupce v novém dokumentu s existujícím. U sloupců s hodnotou null v novém dokumentu je zachována hodnota existující.
- **Nahrání**: nový dokument nahradí stávající. Pro sloupce, které nejsou zadány v novém dokumentu, je hodnota nastavena na hodnotu null, zda je v existujícím dokumentu hodnota jiná než null, nebo ne.

Výchozí chování je **sloučeno**.

### <a name="writebatchsize-property"></a>Vlastnost WriteBatchSize

Služba Azure Kognitivní hledání podporuje zápis dokumentů jako dávky. Dávka může obsahovat 1 až 1 000 akcí. Akce zpracovává jeden dokument k provedení operace Odeslat/sloučit.

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

## <a name="data-type-support"></a>Podpora datových typů

Následující tabulka určuje, jestli je datový typ Azure Kognitivní hledání podporovaný nebo ne.

| Datový typ Azure Kognitivní hledání | Podporováno v jímky Azure Kognitivní hledání |
| ---------------------- | ------------------------------ |
| Řetězec | Y |
| Int32 | Y |
| Int64 | Y |
| dvojité | Y |
| Logická hodnota | Y |
| DataTimeOffset | Y |
| Pole řetězců | N |
| GeographyPoint | N |

V současné době nejsou podporovány jiné datové typy, například ComplexType. Úplný seznam podporovaných datových typů Azure Kognitivní hledání najdete v tématu [podporované datové typy (Azure kognitivní hledání)](https://docs.microsoft.com/rest/api/searchservice/supported-data-types).

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Azure Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
