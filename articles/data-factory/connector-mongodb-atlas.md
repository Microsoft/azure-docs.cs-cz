---
title: Kopírování dat z MongoDB Atlas
description: Naučte se, jak kopírovat data z MongoDB Atlas na podporovaná úložiště dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 09/28/2020
ms.openlocfilehash: b2f77e4bd8df66084937da3dd203ebb71d9a3511
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100368791"
---
# <a name="copy-data-from-mongodb-atlas-using-azure-data-factory"></a>Kopírování dat z MongoDB Atlas pomocí Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory kopírovat data z databáze nástroje MongoDB Atlas. Sestaví se v článku [Přehled aktivity kopírování](copy-activity-overview.md) , který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Data z databáze MongoDB Atlas můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která jsou v rámci aktivity kopírování podporovaná jako zdroje a jímky, najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

Konkrétně tento konektor MongoDB Atlas podporuje **verze až 4,2**.

## <a name="prerequisites"></a>Předpoklady

Pokud používáte Azure Integration Runtime pro kopírování, nezapomeňte do seznamu IP adres pro MongoDB Atlas přidat IP [adresy Azure Integration runtime](azure-integration-runtime-ip-addresses.md) efektivní oblasti.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které se používají k definování Data Factory entit specifických pro konektor MongoDB Atlas.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu MongoDB Atlas jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ |Vlastnost Type musí být nastavená na: **MongoDbAtlas** . |Yes |
| připojovací řetězec |Zadejte připojovací řetězec MongoDB Atlas, např. `mongodb+srv://<username>:<password>@<clustername>.<randomString>.<hostName>/<dbname>?<otherProperties>` . <br/><br /> Připojovací řetězec můžete také vložit do Azure Key Vault. Další podrobnosti najdete [v tématu uložení přihlašovacích údajů v Azure Key Vault](store-credentials-in-key-vault.md) . |Yes |
| database | Název databáze, ke které chcete získat přístup. | Yes |
| connectVia | [Integration runtime](concepts-integration-runtime.md) , která se má použít pro připojení k úložišti dat Další informace najdete v části [požadavky](#prerequisites) . Pokud není zadaný, použije se výchozí Azure Integration Runtime. |No |

**Příklad:**

```json
{
    "name": "MongoDbAtlasLinkedService",
    "properties": {
        "type": "MongoDbAtlas",
        "typeProperties": {
            "connectionString": "mongodb+srv://<username>:<password>@<clustername>.<randomString>.<hostName>/<dbname>?<otherProperties>",
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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, najdete v tématu [datové sady a propojené služby](concepts-datasets-linked-services.md). Pro datovou sadu MongoDB Atlas jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type datové sady musí být nastavená na: **MongoDbAtlasCollection** . | Yes |
| collectionName |Název kolekce v databázi MongoDB Atlas |Yes |

**Příklad:**

```json
{
    "name": "MongoDbAtlasDataset",
    "properties": {
        "type": "MongoDbAtlasCollection",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MongoDB Atlas linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností podporovaných zdrojem MongoDB Atlas.

### <a name="mongodb-atlas-as-source"></a>MongoDB Atlas jako zdroj

V části **zdroj** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type zdroje aktivity kopírování musí být nastavená na: **MongoDbAtlasSource** . | Yes |
| filter | Určuje filtr výběru pomocí operátorů dotazu. Chcete-li vrátit všechny dokumenty v kolekci, vynechejte tento parametr nebo předejte prázdný dokument ( {} ). | No |
| cursorMethods. Project | Určuje pole, která se mají vrátit v dokumentech pro projekci. Chcete-li vrátit všechna pole v porovnání dokumentů, vynechejte tento parametr. | No |
| cursorMethods. Sort | Určuje pořadí, ve kterém dotaz vrátí vyhovující dokumenty. Podívejte se na [kurzor. Sort ()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | No |
| cursorMethods. limit | Určuje maximální počet dokumentů vrácených serverem. Viz [Cursor. limit ()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | No |
| cursorMethods. Skip | Určuje počet dokumentů, které se mají přeskočit a kde MongoDB Atlas začíná vracet výsledky. Podívejte se na [kurzor. Skip ()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | No |
| batchSize | Určuje počet dokumentů, které se mají vrátit v každé dávce odpovědi z instance MongoDB Atlas. Ve většině případů Změna velikosti dávky nebude mít vliv na uživatele nebo aplikaci. Cosmos DB omezí, že každá dávka nemůže překročit velikost 40MB, což je součet batchSize počtu dokumentů, takže tuto hodnotu snížíte, pokud je velikost dokumentu velká. | No<br/>(výchozí hodnota je **100**) |

>[!TIP]
>Podpora ADF spotřebovává BSON dokument ve **striktním režimu**. Zajistěte, aby byl dotaz filtru v režimu Strict, nikoli v režimu Shell. Další popis najdete na adrese [MongoDB ručně](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromMongoDbAtlas",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB Atlas input dataset name>",
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
                "type": "MongoDbAtlasSource",
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

Pomocí tohoto konektoru MongoDB Atlas můžete exportovat dokumenty JSON tak, jak jsou z kolekce MongoDB Atlas, do různých úložišť založených na souborech nebo Azure Cosmos DB. Chcete-li dosáhnout takového schématu – nezávislá kopírování, přeskočte oddíl Structure (označuje se také jako *schéma*) v datové sadě a mapování schématu v aktivitě kopírování.

## <a name="schema-mapping"></a>Mapování schématu

Chcete-li kopírovat data z MongoDB Atlas do tabulkové jímky, přečtěte si téma [mapování schématu](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Azure Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
