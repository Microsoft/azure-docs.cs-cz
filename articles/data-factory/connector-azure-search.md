---
title: Kopírování dat do indexu vyhledávání pomocí Azure Data Factory | Microsoft Docs
description: Přečtěte si, jak vložit nebo zkopírovat data do indexu Azure Search pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: jingwang
ms.openlocfilehash: c2165d0ff16233766918f9e274324b02d1bf1ac8
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962102"
---
# <a name="copy-data-to-an-azure-search-index-using-azure-data-factory"></a>Kopírování dat do indexu Azure Search pomocí Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-azure-search-connector.md)
> * [Aktuální verze](connector-azure-search.md)

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data do indexu Azure Search. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Data můžete kopírovat z libovolného podporovaného zdrojového úložiště dat do indexu Azure Search. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které slouží k definování Data Factory entit specifických pro Azure Search konektor.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro Azure Search propojenou službu jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavená na: **AzureSearch** | Ano |
| url | Adresa URL služby Azure Search | Ano |
| key | Klíč správce pro službu Azure Search. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. (Pokud je vaše úložiště dat se nachází v privátní síti), můžete použít prostředí Azure Integration Runtime nebo modul Integration Runtime. Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. |Ne |

> [!IMPORTANT]
> Při kopírování dat z cloudového úložiště dat do indexu Azure Search v Azure Search propojená služba musíte odkazovat na Azure Integration Runtime s explicitní oblastí v connactVia. Nastavte oblast jako tu, kde se Azure Search. Další informace o [Azure Integration runtime](concepts-integration-runtime.md#azure-integration-runtime).

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. V této části najdete seznam vlastností podporovaných sadou Azure Search DataSet.

Chcete-li kopírovat data do Azure Search, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type datové sady musí být nastavená na: **AzureSearchIndex** | Ano |
| indexName | Název indexu Azure Search. Data Factory nevytváří index. Index musí existovat v Azure Search. | Ano |

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
            "referenceName": "<Azure Search linked service name>",
            "type": "LinkedServiceReference"
        }
   }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. V této části najdete seznam vlastností podporovaných zdrojem Azure Search.

### <a name="azure-search-as-sink"></a>Azure Search jako jímka

Chcete-li kopírovat data do Azure Search, nastavte typ zdroje v aktivitě kopírování na **AzureSearchIndexSink**. Následující vlastnosti jsou podporovány v aktivitě kopírování **jímky** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type zdroje aktivity kopírování musí být nastavená na: **AzureSearchIndexSink** | Ano |
| writeBehavior | Určuje, zda se má sloučit nebo nahradit, když dokument v indexu již existuje. Podívejte se na [vlastnost WriteBehavior](#writebehavior-property).<br/><br/>Povolené hodnoty jsou: **Sloučit** (výchozí) a **nahrání**. | Ne |
| writeBatchSize | Nahraje data do indexu Azure Search, když velikost vyrovnávací paměti dosáhne writeBatchSize. Podrobnosti najdete ve [vlastnosti WriteBatchSize](#writebatchsize-property) .<br/><br/>Povolené hodnoty jsou: celé číslo 1 až 1 000; Výchozí hodnota je 1000. | Ne |

### <a name="writebehavior-property"></a>Vlastnost WriteBehavior

AzureSearchSink upsertuje při zápisu dat. Jinými slovy, při psaní dokumentu, pokud klíč dokumentu již existuje v indexu Azure Search, Azure Search aktualizuje existující dokument namísto vyvolání výjimky konfliktu.

AzureSearchSink poskytuje následující dvě chování Upsert (pomocí sady AzureSearch SDK):

- **Merge**: kombinovat všechny sloupce v novém dokumentu s existujícím. U sloupců s hodnotou null v novém dokumentu je zachována hodnota existující.
- **Nahrát**: Nový dokument nahradí stávající. Pro sloupce, které nejsou zadány v novém dokumentu, je hodnota nastavena na hodnotu null, zda je v existujícím dokumentu hodnota jiná než null, nebo ne.

Výchozí chování je **sloučeno**.

### <a name="writebatchsize-property"></a>Vlastnost WriteBatchSize

Služba Azure Search podporuje psaní dokumentů jako dávky. Dávka může obsahovat 1 až 1 000 akcí. Akce zpracovává jeden dokument k provedení operace Odeslat/sloučit.

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

## <a name="data-type-support"></a>Podpora datových typů

Následující tabulka určuje, zda je podporován datový typ Azure Search.

| Azure Search datový typ | Podporované v Azure Search jímka |
| ---------------------- | ------------------------------ |
| String | Ano |
| Int32 | Ano |
| Int64 | Ano |
| Double | Ano |
| Boolean | Ano |
| DataTimeOffset | Ano |
| String Array | Ne |
| GeographyPoint | Ne |

V současné době nejsou podporovány jiné datové typy, například ComplexType. Úplný seznam datového typu podporovaného službou Azure vyhledávání najdete v tématu [podporované datové typy (Azure Search)](https://docs.microsoft.com/rest/api/searchservice/supported-data-types).

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
