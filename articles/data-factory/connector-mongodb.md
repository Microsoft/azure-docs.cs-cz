---
title: Kopírovat data z MongoDB
description: Naučte se, jak kopírovat data z Mongo DB do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
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
ms.date: 01/08/2021
ms.openlocfilehash: 71096334f46531bba26f0ead66169340107627cf
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028688"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Kopírování dat z MongoDB pomocí Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z databáze MongoDB. Sestaví se v článku [Přehled aktivity kopírování](copy-activity-overview.md) , který představuje obecný přehled aktivity kopírování.

>[!IMPORTANT]
>Vydávání ADF Tato nová verze konektoru MongoDB, která poskytuje lepší podporu nativních MongoDB. Pokud používáte předchozí konektor MongoDB ve vašem řešení, které je podporované tak, jak je kvůli zpětné kompatibilitě, přečtěte si článek [MongoDB Connector (starší verze)](connector-mongodb-legacy.md) .


## <a name="supported-capabilities"></a>Podporované možnosti

Data z databáze MongoDB můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která jsou v rámci aktivity kopírování podporovaná jako zdroje a jímky, najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

Konkrétně tento konektor MongoDB podporuje **verze až 4,2**.


## <a name="prerequisites"></a>Požadavky

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]


## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které slouží k definování Data Factory entit specifických pro konektor MongoDB.


## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu MongoDB jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ |Vlastnost Type musí být nastavená na: **MongoDbV2** . |Ano |
| připojovací řetězec |Zadejte připojovací řetězec MongoDB, např. `mongodb://[username:password@]host[:port][/[database][?options]]` . Další podrobnosti najdete [v MongoDB manuálně na připojovacím řetězci](https://docs.mongodb.com/manual/reference/connection-string/) . <br/><br /> Připojovací řetězec můžete také vložit do Azure Key Vault. Další podrobnosti najdete [v tématu uložení přihlašovacích údajů v Azure Key Vault](store-credentials-in-key-vault.md) . |Ano |
| database | Název databáze, ke které chcete získat přístup. | Ano |
| connectVia | [Integration runtime](concepts-integration-runtime.md) , která se má použít pro připojení k úložišti dat Další informace najdete v části [požadavky](#prerequisites) . Pokud není zadaný, použije se výchozí Azure Integration Runtime. |Ne |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, najdete v tématu [datové sady a propojené služby](concepts-datasets-linked-services.md). Pro datovou sadu MongoDB jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type datové sady musí být nastavená na: **MongoDbV2Collection** . | Ano |
| collectionName |Název kolekce v databázi MongoDB |Ano |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností podporovaných MongoDB zdrojem.

### <a name="mongodb-as-source"></a>MongoDB as source

V části **zdroj** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type zdroje aktivity kopírování musí být nastavená na: **MongoDbV2Source** . | Ano |
| filter | Určuje filtr výběru pomocí operátorů dotazu. Chcete-li vrátit všechny dokumenty v kolekci, vynechejte tento parametr nebo předejte prázdný dokument ( {} ). | Ne |
| cursorMethods. Project | Určuje pole, která se mají vrátit v dokumentech pro projekci. Chcete-li vrátit všechna pole v porovnání dokumentů, vynechejte tento parametr. | Ne |
| cursorMethods. Sort | Určuje pořadí, ve kterém dotaz vrátí vyhovující dokumenty. Podívejte se na [kurzor. Sort ()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Ne |
| cursorMethods. limit | Určuje maximální počet dokumentů vrácených serverem. Viz [Cursor. limit ()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Ne |
| cursorMethods. Skip | Určuje počet dokumentů, které se mají přeskočit a kde MongoDB začne vracet výsledky. Podívejte se na [kurzor. Skip ()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Ne |
| batchSize | Určuje počet dokumentů, které se mají vrátit v každé dávce odpovědi z instance MongoDB. Ve většině případů Změna velikosti dávky nebude mít vliv na uživatele nebo aplikaci. Cosmos DB omezuje každou dávku velikost, která nesmí být větší než 40 MB, což je součet velikosti batchSize počtu dokumentů, takže tuto hodnotu sníží, pokud je velikost dokumentu velká. | Ne<br/>(výchozí hodnota je **100**) |

>[!TIP]
>Podpora ADF spotřebovává BSON dokument ve **striktním režimu**. Zajistěte, aby byl dotaz filtru v režimu Strict, nikoli v režimu Shell. Další popis najdete na adrese [MongoDB ručně](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

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


## <a name="export-json-documents-as-is"></a>Exportovat dokumenty JSON tak, jak jsou

Pomocí tohoto konektoru MongoDB můžete exportovat dokumenty JSON tak, jak jsou z kolekce MongoDB, do různých úložišť založených na souborech nebo Azure Cosmos DB. Chcete-li dosáhnout takového schématu – nezávislá kopírování, přeskočte oddíl Structure (označuje se také jako *schéma*) v datové sadě a mapování schématu v aktivitě kopírování.


## <a name="schema-mapping"></a>Mapování schématu

Chcete-li kopírovat data z MongoDB do tabulkové jímky, přečtěte si téma [mapování schématu](copy-activity-schema-and-type-mapping.md#schema-mapping).


## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Azure Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
