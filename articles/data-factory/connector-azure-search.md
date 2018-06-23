---
title: Kopírování dat do indexu vyhledávání pomocí Azure Data Factory | Microsoft Docs
description: Další informace o tom, jak nabízené nebo zkopírovat data do indexu Azure search pomocí aktivity kopírování v kanál služby Azure Data Factory.
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
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 85c854e697e7178bbdb3429c5384d67731770117
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "36335508"
---
# <a name="copy-data-to-an-azure-search-index-using-azure-data-factory"></a>Kopírování dat do indexu Azure Search pomocí Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](v1/data-factory-azure-search-connector.md)
> * [Verze 2 – Preview](connector-azure-search.md)

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory ke zkopírování dat do indexu Azure Search. Vychází [zkopírujte aktivity přehled](copy-activity-overview.md) článek, který představuje obecný přehled aktivity kopírování.

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [konektor Azure Search v V1](v1/data-factory-azure-search-connector.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Jakékoli úložiště podporované zdroje dat může kopírovat data do indexu Azure Search. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnosti, které se používají k definování entit služby Data Factory, které jsou specifické pro konektor Azure Search.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro službu Azure Search propojené jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu musí být nastavena na: **AzureSearch** | Ano |
| url | Adresa URL pro službu Azure Search. | Ano |
| key | Klíč správce pro službu Azure Search. Toto pole označit jako SecureString bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| connectVia | [Integrace Runtime](concepts-integration-runtime.md) který se má použít pro připojení k úložišti. (Pokud je vaše úložiště dat se nachází v privátní síti), můžete použít modul Runtime integrace Azure nebo Self-hosted integrace Runtime. Pokud není zadaný, použije výchozí Runtime integrace Azure. |Ne |

> [!IMPORTANT]
> Při kopírování dat z cloudu jiného úložiště dat do indexu Azure Search, ve službě Azure Search propojená služba, budete muset odkazovat modul Runtime integrace Azure pomocí explicitní oblast v connactVia. Nastavte oblasti jako ten, který se nachází Azure Search. Další informace z [Runtime integrace Azure](concepts-integration-runtime.md#azure-integration-runtime).

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady najdete v článku datové sady. Tato část obsahuje seznam vlastností nepodporuje datové sady Azure Search.

Chcete-li kopírovat data do Azure Search, nastavte vlastnost type datové sady, která **RelationalTable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu datové sady musí být nastavena na: **AzureSearchIndex** | Ano |
| indexName | Název indexu Azure Search. Objekt pro vytváření dat vytvořit index. Index musí existovat ve službě Azure Search. | Ano |

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností, které podporuje Azure Search zdroje.

### <a name="azure-search-as-sink"></a>Služba Azure Search jako jímku

Chcete-li kopírovat data do Azure Search, nastavte typ zdroje v aktivitě kopírování do **AzureSearchIndexSink**. Následující vlastnosti jsou podporovány v aktivitě kopírování **podřízený** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ zdroje kopie aktivity musí být nastavena na: **AzureSearchIndexSink** | Ano |
| writeBehavior | Určuje, jestli se má sloučit nebo nahradit, pokud již dokument v indexu existuje. Najdete v článku [WriteBehavior vlastnost](#writebehavior-property).<br/><br/>Povolené hodnoty jsou: **sloučení** (výchozí), a **nahrát**. | Ne |
| writeBatchSize | Ukládání dat do indexu Azure Search, když velikost vyrovnávací paměti dosáhne writeBatchSize. Najdete v článku [WriteBatchSize vlastnost](#writebatchsize-property) podrobnosti.<br/><br/>Povolené hodnoty jsou: číslo 1 do 1000; Výchozí hodnota je 1 000. | Ne |

### <a name="writebehavior-property"></a>Vlastnost WriteBehavior

Při zápisu dat AzureSearchSink upserts. Jinými slovy při zápisu dokumentu, pokud klíč dokumentu již existuje v indexu Azure Search, Azure Search aktualizuje stávající dokument místo vyvolání k výjimce konflikt.

AzureSearchSink poskytuje následujících dvou upsert chování (pomocí AzureSearch SDK):

- **Sloučení**: kombinovat všechny sloupce v nový dokument s existujícím. Pro sloupce s hodnotou null v novém dokumentu je hodnota v existujícím zachovaná.
- **Nahrát**: nový dokument nahrazuje stávající. Pro sloupce, které nebyly zadány v nový dokument je hodnota nastavena na hodnotu null, zda je jinou hodnotu než null v existujícího dokumentu nebo ne.

Výchozí chování je **sloučení**.

### <a name="writebatchsize-property"></a>Vlastnost WriteBatchSize

Služba Azure Search podporuje zápis dokumentů jako dávku. Batch může obsahovat akce 1 do 1000. Akce zpracovává jeden dokument k provedení operace nahrávání či sloučení.

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

### <a name="data-type-support"></a>Podpora datového typu

Následující tabulka určuje, zda datového typu Azure Search je podporováno, nebo ne.

| Azure Search datový typ | Podporované ve službě Azure Search jímka |
| ---------------------- | ------------------------------ |
| Řetězec | Ano |
| Int32 | Ano |
| Int64 | Ano |
| Double | Ano |
| Logická hodnota | Ano |
| DataTimeOffset | Ano |
| Pole řetězců | Ne |
| GeographyPoint | Ne |

## <a name="next-steps"></a>Další postup
Seznam úložišť dat jako zdroje a jímky nepodporuje aktivitu kopírování v Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
