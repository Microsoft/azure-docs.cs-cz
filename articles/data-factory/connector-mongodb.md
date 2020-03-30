---
title: Kopírování dat z MongoDB
description: Zjistěte, jak zkopírovat data z Mongo DB do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 08/12/2019
ms.openlocfilehash: a7bb74c09b45429a160a3ec481c23073575cfe3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251710"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Kopírování dat z MongoDB pomocí Azure Data Factory

Tento článek popisuje, jak použít aktivitu kopírování v Azure Data Factory ke kopírování dat z databáze MongoDB. Vychází z článku [přehledu aktivity kopírování,](copy-activity-overview.md) který představuje obecný přehled aktivity kopírování.

>[!IMPORTANT]
>ADF vydání této nové verze konektoru MongoDB, který poskytuje lepší nativní podporu MongoDB. Pokud používáte předchozí konektor MongoDB ve vašem řešení, které je podporováno jako je pro zpětnou kompatibilitu, naleznete v článku [konektor MongoDB (starší verze).](connector-mongodb-legacy.md)

## <a name="supported-capabilities"></a>Podporované možnosti

Data z databáze MongoDB můžete zkopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitou kopírování, naleznete v tabulce [Podporovaná úložiště dat.](copy-activity-overview.md#supported-data-stores-and-formats)

Konkrétně tento konektor MongoDB podporuje **verze až 3.4**.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit Data Factory specifických pro konektor MongoDB.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu MongoDB jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type |Vlastnost type musí být nastavena na: **MongoDbV2** |Ano |
| připojovací řetězec |Zadejte připojovací řetězec MongoDB např. `mongodb://[username:password@]host[:port][/[database][?options]]` Další podrobnosti naleznete v [příručce MongoDB o připojovacím řetězci.](https://docs.mongodb.com/manual/reference/connection-string/) <br/><br /> Můžete také umístit heslo v Azure Key `password` Vault a vyžádat konfiguraci z připojovacího řetězce. Další podrobnosti najdete [v přihlašovacích údajích úložiště v azure trezoru klíčů.](store-credentials-in-key-vault.md) |Ano |
| database | Název databáze, ke které chcete získat přístup. | Ano |
| connectVia | [Prostředí Integrace Runtime,](concepts-integration-runtime.md) které se má použít k připojení k úložišti dat. Další informace naleznete v části [Požadavky.](#prerequisites) Pokud není zadán, používá výchozí Azure Integration Runtime. |Ne |

**Příklad:**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDbV2",
        "typeProperties": {
            "connectionString": "mongodb://[username:password@]host[:port][/[database][?options]]",
            "database": "myDatabase"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete [v tématu Datové sady a propojené služby](concepts-datasets-linked-services.md). Pro datovou sadu MongoDB jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady musí být nastavena na: **MongoDbV2Collection.** | Ano |
| název kolekce |Název kolekce v databázi MongoDB. |Ano |

**Příklad:**

```json
{
    "name": "MongoDbDataset",
    "properties": {
        "type": "MongoDbV2Collection",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných zdrojem MongoDB.

### <a name="mongodb-as-source"></a>MongoDB jako zdroj

V části **zdroje** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavena na: **MongoDbV2Source.** | Ano |
| filtrování | Určuje filtr výběru pomocí operátorů dotazů. Chcete-li vrátit všechny dokumenty v kolekci, vynechat tento parametr nebo předat prázdný dokument ({}). | Ne |
| cursorMethods.project | Určuje pole, která se mají vrátit v dokumentech pro projekci. Chcete-li vrátit všechna pole v odpovídajících dokumentech, vyneche tento parametr. | Ne |
| cursorMethods.sort | Určuje pořadí, ve kterém dotaz vrací odpovídající dokumenty. Viz [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Ne |
| cursorMethods.limit | Určuje maximální počet dokumentů, které server vrátí. Viz [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Ne |
| cursorMethods.skip | Určuje počet dokumentů, které mají být přeskakovány a odkud začíná mongoDB vracet výsledky. Viz [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Ne |
| batchSize | Určuje počet dokumentů, které mají být vráceny v každé dávce odpovědi z instance MongoDB. Ve většině případů změna velikosti dávky nebude mít vliv na uživatele nebo aplikace. Cosmos DB omezuje každou dávku nesmí překročit 40 MB ve velikosti, což je součet batchSize počet velikosti dokumentů, takže snížit tuto hodnotu, pokud velikost dokumentu je velký. | Ne<br/>(výchozí hodnota je **100**) |

>[!TIP]
>ADF podporuje náročné BSON dokument v **přísném režimu**. Ujistěte se, že váš filtr dotaz je v přísném režimu místo shellrežimu. Další popis lze nalézt na [manuálu MongoDB](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
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
                "type": "MongoDbV2Source",
                "filter": "{datetimeData: {$gte: ISODate(\"2018-12-11T00:00:00.000Z\"),$lt: ISODate(\"2018-12-12T00:00:00.000Z\")}, _id: ObjectId(\"5acd7c3d0000000000000000\") }",
                "cursorMethods": {
                    "project": "{ _id : 1, name : 1, age: 1, datetimeData: 1 }",
                    "sort": "{ age : 1 }",
                    "skip": 3,
                    "limit": 3
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="export-json-documents-as-is"></a>Export ovat dokumenty JSON tak, jak jsou

Tento konektor MongoDB můžete použít k exportu dokumentů JSON, jako je z kolekce MongoDB do různých úložišť založených na souborech nebo do Azure Cosmos DB. Chcete-li dosáhnout takové schéma-agnostik kopírování, přeskočte část "struktura" (také volal *schéma*) v datové sadě a mapování schématu v aktivitě kopírování.

## <a name="schema-mapping"></a>Mapování schématu

Chcete-li zkopírovat data z MongoDB do tabulkového jímky, naleznete v [mapování schématu](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v Azure Data Factory najdete v [tématu podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
