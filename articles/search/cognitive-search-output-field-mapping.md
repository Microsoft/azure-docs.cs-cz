---
title: Mapa kognitivní vyhledávání obohacená známým vstupní pole výstup pole v indexů Azure Search | Microsoft Docs
description: Extrahování a zlepšit komunikaci oddělení zdroj datová pole a mapování na výstup pole v indexu Azure Search.
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 67e4798070a73eebb8f61b0b260e3104e9ae6237
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-map-enriched-fields-to-a-searchable-index"></a>Jak provádět rozšířené mapování na index s možností vyhledávání

V tomto článku zjistěte, jak k mapování provádět rozšířené vstupní a výstupní pole v indexu prohledávatelné. Jakmile máte [definované skillset](cognitive-search-defining-skillset.md), je nutné mapovat pole výstup všech odborností, která přímo přispívá hodnoty pro dané pole v indexu vyhledávání. Mapování polí jsou požadovány pro přesun obsahu z provádět rozšířené dokumentů do indexu.


## <a name="use-outputfieldmappings"></a>Použití outputFieldMappings
Mapování polí, přidejte `outputFieldMappings` do vaší definice indexer, jak je uvedeno níže:

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```

Text žádosti je strukturu:

```json
{
    "name": "myIndexer",
    "dataSourceName": "myDataSource",
    "targetIndexName": "myIndex",
    "skillsetName": "myFirstSkillSet",
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction": {
                "name": "base64Encode"
            }
        }
    ],
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/content/organizations/*/description",
            "targetFieldName": "descriptions"
        },
        {
            "sourceFieldName": "/document/content/organizations",
            "targetFieldName": "orgNames"
        },
        {
            "sourceFieldName": "/document/content/sentiment",
            "targetFieldName": "sentiment"
        }
    ]
}
```
Pro každé pole výstup nastavit mapování, názvu pole provádět rozšířené (sourceFieldName) a názvu pole jako odkazovaná v indexu (targetFieldName).

Cesta v sourceFieldName může představovat element jeden nebo více elementů. V příkladu nahoře ```/document/content/sentiment``` představuje jednu číselnou hodnotu, při ```/document/content/organizations/*/description``` představuje několik popisů organizace. V případech, kde existuje několik prvků, budou se "sloučí" do pole, které obsahuje jednotlivých prvků. Více namítají pro ```/document/content/organizations/*/description``` příklad, data v *popisy* pole bude vypadat ploché pole popisů předtím, než získá indexované:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```
## <a name="next-steps"></a>Další postup
Jakmile vaše provádět rozšířené pole jsou namapovány na prohledávatelné pole, můžete nastavit atributy pole pro každé pole prohledávatelné [jako součást definice indexu](search-what-is-an-index.md).

Další informace o mapování polí najdete v tématu [pole mapování v Azure Search indexery](search-indexer-field-mappings.md).