---
title: Kopírování dat z rozhraní API Azure Cosmos DB pro MongoDB
description: Naučte se, jak kopírovat data z podporovaných zdrojových úložišť dat do nebo z rozhraní API Azure Cosmos DB pro MongoDB pro podporovaná úložiště jímky pomocí Data Factory.
services: data-factory, cosmosdb
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/20/2019
ms.openlocfilehash: 0b38bc3309d8cf265a554a10e36311f53e6fe8a9
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929920"
---
# <a name="copy-data-to-or-from-azure-cosmos-dbs-api-for-mongodb-by-using-azure-data-factory"></a>Kopírování dat do nebo z rozhraní API Azure Cosmos DB pro MongoDB pomocí Azure Data Factory

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory kopírovat data z a do Azure Cosmos DB API pro MongoDB. Tento článek vychází [aktivita kopírování ve službě Azure Data Factory](copy-activity-overview.md), který nabízí obecný přehled o aktivitě kopírování.

>[!NOTE]
>Tento konektor podporuje pouze kopírování dat do a z rozhraní API Azure Cosmos DB pro MongoDB. Informace o rozhraní SQL API najdete v tématu [Cosmos DB SQL API Connector](connector-azure-cosmos-db.md). Jiné typy rozhraní API se teď nepodporují.

## <a name="supported-capabilities"></a>Podporované funkce

Data z rozhraní API Azure Cosmos DB pro MongoDB můžete kopírovat do libovolného podporovaného úložiště dat jímky nebo zkopírovat data z libovolného podporovaného zdrojového úložiště dat do rozhraní API Azure Cosmos DB pro MongoDB. Seznam dat ukládá podporovanou aktivitou kopírování jako zdroje a jímky, najdete v části [podporovaných úložišť dat a formáty](copy-activity-overview.md#supported-data-stores-and-formats).

K MongoDB konektoru můžete použít rozhraní API Azure Cosmos DB pro:

- Kopírovat data z a do [rozhraní API Azure Cosmos DB pro MongoDB](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction).
- Zapsat do služby Azure Cosmos DB jako **vložit** nebo **upsert**.
- Import a export dokumentů JSON jako-je, nebo zkopírovat data z nebo do tabulkové datové sady. Mezi příklady patří SQL database a soubor CSV. Pokud chcete kopírovat dokumenty tak, jak jsou, nebo ze souborů JSON nebo do jiné kolekce Azure Cosmos DB, přečtěte si téma import nebo export dokumentů JSON.

## <a name="get-started"></a>Začít

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které můžete použít k definování Data Factory entit, které jsou specifické pro rozhraní API Azure Cosmos DB pro MongoDB.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu MongoDB se podporují následující vlastnosti rozhraní API pro Azure Cosmos DB:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **Type** musí být nastavená na **CosmosDbMongoDbApi**. | Ano |
| connectionString |Zadejte připojovací řetězec pro rozhraní API Azure Cosmos DB pro MongoDB. Můžete ji najít v Azure Portal – Cosmos DB > primárním nebo sekundárním připojovacím řetězcem >, a to se vzorem `mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb`. <br/><br />Označte toto pole jako **SecureString** typ bezpečně uložit ve službě Data Factory. Můžete také [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). |Ano |
| databáze | Název databáze, ke které chcete získat přístup. | Ano |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) používat pro připojení k úložišti. (Pokud je vaše úložiště dat se nachází v privátní síti), můžete použít modul Runtime integrace v Azure nebo místního prostředí integration runtime. Pokud není tato vlastnost určena, použije se výchozí prostředí Azure Integration Runtime. |Ne |

**Příklad**

```json
{
    "name": "CosmosDbMongoDBAPILinkedService",
    "properties": {
        "type": "CosmosDbMongoDbApi",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb"
            },
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, naleznete v tématu [datové sady a propojené služby](concepts-datasets-linked-services.md). Pro Azure Cosmos DB rozhraní API pro sadu MongoDB jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **Type** datové sady musí být nastavená na **CosmosDbMongoDbApiCollection**. |Ano |
| collectionName |Název kolekce Azure Cosmos DB. |Ano |

**Příklad**

```json
{
    "name": "CosmosDbMongoDBAPIDataset",
    "properties": {
        "type": "CosmosDbMongoDbApiCollection",
        "typeProperties": {
            "collectionName": "<collection name>"
        },
        "schema": [],
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB's API for MongoDB linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

V této části najdete seznam vlastností, které Azure Cosmos DB rozhraní API pro podporu zdrojů MongoDB a jímky.

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v tématu [kanály](concepts-pipelines-activities.md).

### <a name="azure-cosmos-dbs-api-for-mongodb-as-source"></a>Azure Cosmos DB rozhraní API pro MongoDB jako zdroj

Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **Type** zdroje aktivity kopírování musí být nastavená na **CosmosDbMongoDbApiSource**. |Ano |
| filter | Určuje filtr výběru pomocí operátorů dotazu. Chcete-li vrátit všechny dokumenty v kolekci, vynechejte tento parametr nebo předejte prázdný dokument ({}). | Ne |
| cursorMethods.project | Určuje pole, která se mají vrátit v dokumentech pro projekci. Chcete-li vrátit všechna pole v porovnání dokumentů, vynechejte tento parametr. | Ne |
| cursorMethods. Sort | Určuje pořadí, ve kterém dotaz vrátí vyhovující dokumenty. Podívejte se na [kurzor. Sort ()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Ne |
| cursorMethods. limit | Určuje maximální počet dokumentů vrácených serverem. Viz [Cursor. limit ()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Ne | 
| cursorMethods.skip | Určuje počet dokumentů, které se mají přeskočit a kde MongoDB začne vracet výsledky. Podívejte se na [kurzor. Skip ()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Ne |
| batchSize | Určuje počet dokumentů, které se mají vrátit v každé dávce odpovědi z instance MongoDB. Ve většině případů Změna velikosti dávky nebude mít vliv na uživatele nebo aplikaci. Cosmos DB omezí, že každá dávka nemůže překročit velikost 40MB, což je součet batchSize počtu dokumentů, takže tuto hodnotu snížíte, pokud je velikost dokumentu velká. | Ne<br/>(výchozí hodnota je **100**) |

>[!TIP]
>Podpora ADF spotřebovává BSON dokument ve **striktním režimu**. Zajistěte, aby byl dotaz filtru v režimu Strict, nikoli v režimu Shell. Další popis najdete na adrese [MongoDB ručně](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

**Příklad**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBMongoDBAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Cosmos DB's API for MongoDB input dataset name>",
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
                "type": "CosmosDbMongoDbApiSource",
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

### <a name="azure-cosmos-dbs-api-for-mongodb-as-sink"></a>Rozhraní API pro MongoDB Azure Cosmos DB jako jímku

Následující vlastnosti jsou podporovány v aktivitě kopírování **jímky** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **Type** jímky aktivity kopírování musí být nastavená na **CosmosDbMongoDbApiSink**. |Ano |
| writeBehavior |Popisuje, jak zapsat data do služby Azure Cosmos DB. Povolené hodnoty: **vložit** a **upsert**.<br/><br/>Chování **Upsert** je nahradit dokument, pokud již existuje dokument se stejným `_id`; v opačném případě vložte dokument.<br /><br />**Poznámka**: Data Factory automaticky vygeneruje `_id` pro dokument, pokud `_id` není zadán v původním dokumentu nebo mapování sloupce. To znamená, že musíte zajistit, aby, pro **upsert** fungovat podle očekávání, váš dokument nemá identifikátor. |Ne<br />(výchozí hodnota je **vložit**) |
| writeBatchSize | Vlastnost **writeBatchSize** určuje velikost dokumentů pro zápis v každé dávce. Můžete zkusit zvýšit hodnotu pro **writeBatchSize** a zvýšit tak výkon a snížit hodnotu, pokud je velikost dokumentu velká. |Ne<br />(výchozí hodnota je **10 000**) |
| writeBatchTimeout | Doba čekání na dokončení operace dávkového vložení před vypršením časového limitu. Povolená hodnota je TimeSpan. | Ne<br/>(výchozí hodnota je **00:30:00** – 30 minut) |

**Příklad**

```json
"activities":[
    {
        "name": "CopyToCosmosDBMongoDBAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "CosmosDbMongoDbApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

>[!TIP]
>Pokud chcete importovat dokumenty JSON tak, jak jsou, přečtěte si část [Import nebo export dokumentů JSON](#import-or-export-json-documents) . Chcete-li kopírovat z tabulkových dat, přečtěte si téma [mapování schématu](#schema-mapping).

## <a name="import-or-export-json-documents"></a>Import a export dokumentů JSON

Tento konektor služby Azure Cosmos DB můžete snadno:

* Importujte dokumenty JSON z různých zdrojů do služby Azure Cosmos DB, včetně z úložiště objektů Blob v Azure, Azure Data Lake Store a další souborové úložiště, které podporuje Azure Data Factory.
* Exportujte dokumentů JSON z kolekce Azure Cosmos DB do různých úložišť založená na souborech.
* Zkopírujte dokumenty mezi dvě kolekce Azure Cosmos DB jako-je.

Chcete-li dosáhnout takového schématu – nezávislá kopírování, přeskočte oddíl Structure (označuje se také jako *schéma*) v datové sadě a mapování schématu v aktivitě kopírování.

## <a name="schema-mapping"></a>Schema mapping

Chcete-li kopírovat data z rozhraní Azure Cosmos DB API pro MongoDB do tabulkové jímky nebo obráceně, přečtěte si téma [mapování schématu](copy-activity-schema-and-type-mapping.md#schema-mapping).

Konkrétně pro psaní do Cosmos DB, abyste měli jistotu, že naplníte Cosmos DB se správným ID objektu ze zdrojových dat, například sloupec ID v tabulce SQL Database a chcete použít hodnotu, která jako ID dokumentu v MongoDB pro INSERT/Upsert, je třeba nastavit správné mapování schématu podle definice MongoDB striktního režimu (`_id.$oid`), jak je uvedeno níže:

![ID mapy v jímky MongoDB](./media/connector-azure-cosmos-db-mongodb-api/map-id-in-mongodb-sink.png)

Po spuštění aktivity kopírování se v jímky vygeneruje pod BSON ObjectId:

```json
{
    "_id": ObjectId("592e07800000000000000000")
}
``` 

## <a name="next-steps"></a>Další kroky

Seznam úložišť dat, která aktivitu kopírování, která podporuje jako zdroje a jímky ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
