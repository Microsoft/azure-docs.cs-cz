---
title: Kopírování dat z MongoDB pomocí Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data z Mongo DB do úložišť dat podporovaných jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: 1e0d6fe79d23f3d3d0679445f5120a3ec17ddd67
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54013770"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Kopírování dat z MongoDB pomocí Azure Data Factory

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory ke zkopírování dat z databáze MongoDB. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

>[!IMPORTANT]
>ADF této nové verze konektoru MongoDB, která poskytuje lepší podporu nativní MongoDB. Pokud používáte předchozí konektoru MongoDB ve vašem řešení, který je podporovaný jako-je z důvodu zpětné kompatibility najdete v tématu [konektoru MongoDB (starší verze)](connector-mongodb-legacy.md) článku.

## <a name="supported-capabilities"></a>Podporované funkce

Kopírování dat z databáze MongoDB do jakékoli podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor MongoDB podporuje **verze až 3.4**.

## <a name="prerequisites"></a>Požadavky

Ke zkopírování dat z databáze MongoDB, která není veřejně přístupná, budete muset nastavit modul Integration Runtime. Zobrazit [modul Integration Runtime](create-self-hosted-integration-runtime.md) článku se dozvíte podrobnosti.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní konektoru MongoDB.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro MongoDB propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type |Vlastnost type musí být nastavená na: **MongoDbV2** |Ano |
| připojovací řetězec |Například zadejte připojovací řetězec MongoDB `mongodb://[username:password@]host[:port][/[database][?options]]`. Odkazovat na [ruční MongoDB v připojovacím řetězci](https://docs.mongodb.com/manual/reference/connection-string/) další podrobnosti. <br/><br />Označte toto pole jako **SecureString** typ bezpečně uložit ve službě Data Factory. Můžete také [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). |Ano |
| databáze | Název databáze, které chcete získat přístup. | Ano |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Můžete použít modul Integration Runtime nebo prostředí Azure Integration Runtime (Pokud vaše úložiště dat je veřejně dostupná). Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. |Ne |

**Příklad:**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDbV2",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "mongodb://[username:password@]host[:port][/[database][?options]]"
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, naleznete v tématu [datové sady a propojené služby](concepts-datasets-linked-services.md). Následující vlastnosti jsou podporovány pro datovou sadu MongoDB:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady, musí být nastavená na: **MongoDbV2Collection** | Ano |
| collectionName |Název kolekce v databázi MongoDB. |Ano |

**Příklad:**

```json
{
     "name":  "MongoDbDataset",
    "properties": {
        "type": "MongoDbV2Collection",
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<Collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností podporovaných zdrojem MongoDB.

### <a name="mongodb-as-source"></a>MongoDB jako zdroj

Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavená na: **MongoDbV2Source** | Ano |
| Filtr | Určuje filtr výběru pomocí operátorů pro dotazování. Který vrátí všechny dokumenty v kolekci, tento parametr vynecháte nebo předat prázdný dokument ({}). | Ne |
| cursorMethods.project | Určuje pole, která vrátí v dokumentech pro projekci. Tento parametr vynechte, pokud se pokud chcete vrátit všechna pole v odpovídajících dokumentů. | Ne |
| cursorMethods.sort | Určuje pořadí, ve které dotaz vrátí odpovídajících dokumentů. Odkazovat na [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Ne |
| cursorMethods.limit | Určuje maximální počet dokumentů, který server vrátí. Odkazovat na [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Ne | 
| cursorMethods.skip | Určuje počet dokumentů, chcete-li přeskočit a od které začíná MongoDB pro vracení výsledků. Odkazovat na [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Ne |
| batchSize | Určuje počet dokumenty, které se vrátí v každé dávce odpovědi z MongoDB instance. Ve většině případů Změna velikosti dávky nebude mít vliv na uživatele nebo aplikace. Cosmos DB omezení jednotlivých dávek nemůže být delší než 40MB velikosti, který je součtem batchSize počet dokumentů velikost, takže zmenšete tuto hodnotu, pokud probíhá velké velikosti vašeho dokumentu. | Ne<br/>(výchozí hodnota je **100**) |

>[!TIP]
>ADF podporují využití návratových hodnot BSON dokumentu v **přísném režimu**. Zajistěte, aby že vašemu dotazu filtru je ve striktním režimu místo režimu prostředí. Další popis lze nalézt v [MongoDB ruční](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

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

## <a name="export-json-documents-as-is"></a>Export dokumentů JSON jako-je

Tento konektor MongoDB můžete použít pro export dokumentů JSON jako-pochází z kolekce MongoDB do různých úložišť na základě souboru nebo ke službě Azure Cosmos DB. K dosažení těchto kopírování dogmaticky na schématu, přeskočte "struktura" (také nazývané *schématu*) části datovou sadu a mapování schématu v aktivitě kopírování.

## <a name="schema-mapping"></a>mapování schématu

Kopírování dat z MongoDB do tabulky jímky, najdete v tématu [mapování schématu](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
