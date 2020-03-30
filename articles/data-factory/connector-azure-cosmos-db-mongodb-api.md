---
title: Kopírování dat z rozhraní API služby Azure Cosmos DB pro MongoDB
description: Zjistěte, jak kopírovat data z podporovaných zdrojových úložišť dat do nebo z rozhraní API Služby Azure Cosmos DB pro MongoDB do podporovaných úložišť jímek pomocí datové továrny.
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
ms.openlocfilehash: 9b13920252b3a5626fd192c6e899154efd31a3de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75893246"
---
# <a name="copy-data-to-or-from-azure-cosmos-dbs-api-for-mongodb-by-using-azure-data-factory"></a>Kopírování dat do a z rozhraní API služby Azure Cosmos DB pro MongoDB pomocí služby Azure Data Factory

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory kopírovat data do a z rozhraní API služby Azure Cosmos DB pro MongoDB. Článek vychází [z aktivity kopírování v Azure Data Factory](copy-activity-overview.md), která představuje obecný přehled aktivity kopírování.

>[!NOTE]
>Tento konektor podporuje jenom kopírování dat do/z rozhraní API Azure Cosmos DB pro MongoDB. Sql API naleznete [v konektoru rozhraní COSMOS DB SQL API](connector-azure-cosmos-db.md). Jiné typy rozhraní API nejsou nyní podporovány.

## <a name="supported-capabilities"></a>Podporované možnosti

Můžete zkopírovat data z rozhraní API Azure Cosmos DB pro MongoDB do libovolného podporovaného úložiště dat jímky nebo zkopírovat data z libovolného úložiště dat podporovaného zdroje do rozhraní API Azure Cosmos DB pro MongoDB. Seznam úložišť dat, která podporují aktivita kopírování jako zdroje a propady, naleznete v [tématu Podporovaná úložiště a formáty dat](copy-activity-overview.md#supported-data-stores-and-formats).

Rozhraní API Azure Cosmos DB pro konektor MongoDB můžete použít k:

- Kopírování dat z a do [rozhraní API Azure Cosmos DB pro MongoDB](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction).
- Zapište do Azure Cosmos DB jako **vložit** nebo **upsert**.
- Importaje a exportuje dokumenty JSON tak, jak jsou, nebo zkopírujte data z tabulkové datové sady nebo do ní. Příklady zahrnují databázi SQL a soubor CSV. Pokud chcete kopírovat dokumenty tak, jak jsou do nebo ze souborů JSON nebo do nebo z jiné kolekce Azure Cosmos DB, přečtěte si článek Import nebo export dokumentů JSON.

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které můžete použít k definování entit Datové továrny, které jsou specifické pro rozhraní API Azure Cosmos DB pro MongoDB.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro rozhraní API Azure Cosmos DB pro propojenou službu MongoDB jsou podporované následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **type** musí být nastavena na **CosmosDbMongoDbApi**. | Ano |
| připojovací řetězec |Zadejte připojovací řetězec pro rozhraní API databáze Azure Cosmos pro MongoDB. Najdete ji na portálu Azure -> okno Cosmos DB -> primární nebo `mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb`sekundární připojovací řetězec, se vzorem . <br/><br />Můžete také umístit heslo v Azure Key `password` Vault a vyžádat konfiguraci z připojovacího řetězce.Další podrobnosti najdete [v přihlašovacích údajích úložiště v azure trezoru](store-credentials-in-key-vault.md) klíčů.|Ano |
| database | Název databáze, ke které chcete získat přístup. | Ano |
| connectVia | [Prostředí Integrace Runtime](concepts-integration-runtime.md) pro připojení k úložišti dat. Můžete použít Prostředí Azure Integration Runtime nebo runtime integrace s vlastním hostitelem (pokud je úložiště dat umístěné v privátní síti). Pokud tato vlastnost není zadána, použije se výchozí prostředí Azure Integration Runtime. |Ne |

**Příklad**

```json
{
    "name": "CosmosDbMongoDBAPILinkedService",
    "properties": {
        "type": "CosmosDbMongoDbApi",
        "typeProperties": {
            "connectionString": "mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb",
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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete [v tématu Datové sady a propojené služby](concepts-datasets-linked-services.md). Pro rozhraní API Azure Cosmos DB pro datovou sadu MongoDB jsou podporované následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **type** datové sady musí být nastavena na **CosmosDbMongoDbApiCollection**. |Ano |
| název kolekce |Název kolekce Azure Cosmos DB. |Ano |

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

## <a name="copy-activity-properties"></a>Kopírovat vlastnosti aktivity

Tato část obsahuje seznam vlastností, které rozhraní API Azure Cosmos DB pro podporu zdroje a jímky MongoDB.

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v [tématu Potrubí](concepts-pipelines-activities.md).

### <a name="azure-cosmos-dbs-api-for-mongodb-as-source"></a>Rozhraní API Azure Cosmos DB pro MongoDB jako zdroj

V části Zdroj **kopírování aktivity** jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **type** zdroje aktivity kopírování musí být nastavena na **CosmosDbMongoDbApiSource**. |Ano |
| filtrování | Určuje filtr výběru pomocí operátorů dotazů. Chcete-li vrátit všechny dokumenty v kolekci, vynechat tento parametr nebo předat prázdný dokument ({}). | Ne |
| cursorMethods.project | Určuje pole, která se mají vrátit v dokumentech pro projekci. Chcete-li vrátit všechna pole v odpovídajících dokumentech, vyneche tento parametr. | Ne |
| cursorMethods.sort | Určuje pořadí, ve kterém dotaz vrací odpovídající dokumenty. Viz [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Ne |
| cursorMethods.limit | Určuje maximální počet dokumentů, které server vrátí. Viz [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Ne | 
| cursorMethods.skip | Určuje počet dokumentů, které mají být přeskakovány a odkud začíná mongoDB vracet výsledky. Viz [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Ne |
| batchSize | Určuje počet dokumentů, které mají být vráceny v každé dávce odpovědi z instance MongoDB. Ve většině případů změna velikosti dávky nebude mít vliv na uživatele nebo aplikace. Cosmos DB omezuje každou dávku nesmí překročit 40 MB ve velikosti, což je součet batchSize počet velikosti dokumentů, takže snížit tuto hodnotu, pokud velikost dokumentu je velký. | Ne<br/>(výchozí hodnota je **100**) |

>[!TIP]
>ADF podporuje náročné BSON dokument v **přísném režimu**. Ujistěte se, že váš filtr dotaz je v přísném režimu místo shellrežimu. Další popis lze nalézt na [manuálu MongoDB](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

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

### <a name="azure-cosmos-dbs-api-for-mongodb-as-sink"></a>Rozhraní API Azure Cosmos DB pro MongoDB jako jímku

V části **Jímka** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **type** jímky aktivity kopírování musí být nastavena na **CosmosDbMongoDbApiSink**. |Ano |
| writeBehavior |Popisuje, jak zapisovat data do Azure Cosmos DB. Povolené hodnoty: **insert** a **upsert**.<br/><br/>Chování **upsert** je nahradit dokument, pokud dokument `_id` se stejným již existuje; v opačném případě vložte dokument.<br /><br />**Poznámka:** Data Factory automaticky `_id` vygeneruje `_id` pro dokument, pokud není zadán v původním dokumentu nebo pomocí mapování sloupců. To znamená, že je nutné zajistit, aby **upsert** fungoval podle očekávání, že váš dokument má ID. |Ne<br />(výchozí hodnota je **vložit**) |
| writeBatchSize | Vlastnost **writeBatchSize** určuje velikost dokumentů, které mají být zapsány v každé dávce. Můžete zkusit zvýšit hodnotu pro **writeBatchSize** ke zlepšení výkonu a snížení hodnoty, pokud je velikost dokumentu velká. |Ne<br />(výchozí hodnota je **10 000**) |
| writeBatchTimeout | Čekací doba pro operaci dávkové vložení dokončit před časovým oponem časového režimu. Povolená hodnota je časový rozsah. | Ne<br/>(výchozí hodnota je **00:30:00** - 30 minut) |

>[!TIP]
>Chcete-li importovat dokumenty JSON tak, jak jsou, podívejte se do části [Import nebo export dokumentů JSON;](#import-and-export-json-documents) chcete-li kopírovat z tabulkových dat, naleznete v [části Mapování schématu](#schema-mapping).

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

## <a name="import-and-export-json-documents"></a>Import a export dokumentů JSON

Tento konektor Azure Cosmos DB můžete snadno použít k:

* Zkopírujte dokumenty mezi dvěma kolekcemi Azure Cosmos DB tak, jak jsou.
* Importujte dokumenty JSON z různých zdrojů do Azure Cosmos DB, včetně mongoDB, úložiště objektů blob Azure, Azure Data Lake Store a dalších úložišť založených na souborech, které Azure Data Factory podporuje.
* Exportujte dokumenty JSON z kolekce Azure Cosmos DB do různých úložišť založených na souborech.

Chcete-li dosáhnout nezávislá kopie schématu:

* Při použití nástroje Kopírovat data vyberte volbu **Exportovat jako je do souborů JSON nebo kolekce Cosmos DB.**
* Při použití vytváření aktivit zvolte formát JSON s odpovídajícím úložištěm souborů pro zdroj nebo jímku.

## <a name="schema-mapping"></a>Mapování schématu

Chcete-li zkopírovat data z rozhraní API Služby Azure Cosmos DB pro MongoDB do tabulkového jímky nebo stornovat, přečtěte si [mapování schématu](copy-activity-schema-and-type-mapping.md#schema-mapping).

Konkrétně pro zápis do Cosmos DB, abyste se ujistili, že naplníte Cosmos DB pravým ID objektu ze zdrojových dat, například máte sloupec "id" v databázové tabulce SQL a chcete použít hodnotu jako ID dokumentu v`_id.$oid`MongoDB pro vložení/upsert, musíte nastavit správné mapování schématu podle definice přísného režimu MongoDB ( ) jako následující:

![ID mapy v umyvadle MongoDB](./media/connector-azure-cosmos-db-mongodb-api/map-id-in-mongodb-sink.png)

Po spuštění aktivity kopírování pod BSON ObjectId je generován v jímce:

```json
{
    "_id": ObjectId("592e07800000000000000000")
}
``` 

## <a name="next-steps"></a>Další kroky

Seznam úložišť dat, která podporují aktivity kopírování jako zdroje a propady v Azure Data Factory, najdete v [tématu podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
