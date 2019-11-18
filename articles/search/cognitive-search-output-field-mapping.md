---
title: Mapování vstupu na výstupní pole
titleSuffix: Azure Cognitive Search
description: Extrahování a obohacení zdrojových datových polí a mapování na výstupní pole v indexu služby Azure Kognitivní hledání.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d2d5e717154d16cc5579c1495aff9c1eebf54b17
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132374"
---
# <a name="how-to-map-ai-enriched-fields-to-a-searchable-index"></a>Jak mapovat pole obohacená AI na index s možností prohledávání

V tomto článku se dozvíte, jak namapovat obohacená vstupní pole na výstupní pole v indexu s možností prohledávání. Po [Definování dovednosti](cognitive-search-defining-skillset.md)je nutné namapovat výstupní pole libovolné dovednosti, která přímo přispívá k danému poli v indexu vyhledávání. Pro přesun obsahu z obohacených dokumentů do indexu jsou vyžadovány mapování polí.


## <a name="use-outputfieldmappings"></a>Použití outputFieldMappings
K mapování polí přidejte `outputFieldMappings` do definice indexeru, jak je znázorněno níže:

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```

Tělo žádosti je strukturováno takto:

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
Pro každé mapování polí výstupu nastavte název obohaceného pole (sourceFieldName) a název pole, jak je odkazováno v indexu (targetFieldName).

Cesta v sourceFieldName může představovat jeden nebo více elementů. V příkladu výše ```/document/content/sentiment``` představuje jednu číselnou hodnotu, zatímco ```/document/content/organizations/*/description``` představuje několik popisů organizace. V případech, kdy existuje několik prvků, jsou "shrnuty" do pole, které obsahuje každý prvek. Čím více z nich bude ```/document/content/organizations/*/description``` například, data v poli *descriptions* budou vypadat jako ploché pole popisů předtím, než se naindexuje:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```
## <a name="next-steps"></a>Další kroky
Po namapování obohacených polí na prohledávatelné pole můžete [jako součást definice indexu](search-what-is-an-index.md)nastavit atributy polí pro každé z polí, která lze prohledávat.

Další informace o mapování polí najdete v tématu [mapování polí v indexerech Azure kognitivní hledání](search-indexer-field-mappings.md).
