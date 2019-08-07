---
title: Mapování rozpoznávání vyhledávání obohacená vstupní pole do výstupních polí – Azure Search
description: Extrahování a obohacení zdrojových datových polí a mapování na výstupní pole v indexu Azure Search.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: 1fae611c202b77d222436b090f7e0c2f432de1f2
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68841173"
---
# <a name="how-to-map-enriched-fields-to-a-searchable-index"></a>Mapování obohacených polí na index s možností prohledávání

V tomto článku se dozvíte, jak namapovat obohacená vstupní pole na výstupní pole v indexu s možností prohledávání. Po [Definování dovednosti](cognitive-search-defining-skillset.md)je nutné namapovat výstupní pole libovolné dovednosti, která přímo přispívá k danému poli v indexu vyhledávání. Pro přesun obsahu z obohacených dokumentů do indexu jsou vyžadovány mapování polí.


## <a name="use-outputfieldmappings"></a>Použití outputFieldMappings
Pro mapování polí přidejte `outputFieldMappings` do definice indexeru, jak je znázorněno níže:

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

Cesta v sourceFieldName může představovat jeden nebo více elementů. V předchozím ```/document/content/sentiment``` příkladu představuje jednu číselnou hodnotu, zatímco ```/document/content/organizations/*/description``` představuje několik popisů organizace. V případech, kdy existuje několik prvků, jsou "shrnuty" do pole, které obsahuje každý prvek. Čím více z nich je v ```/document/content/organizations/*/description``` příkladu, data v poli *descriptions* budou vypadat jako ploché pole popisů předtím, než se naindexuje:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```
## <a name="next-steps"></a>Další postup
Po namapování obohacených polí na prohledávatelné pole můžete [jako součást definice indexu](search-what-is-an-index.md)nastavit atributy polí pro každé z polí, která lze prohledávat.

Další informace o mapování polí najdete v tématu [mapování polí v Azure Search indexerech](search-indexer-field-mappings.md).
