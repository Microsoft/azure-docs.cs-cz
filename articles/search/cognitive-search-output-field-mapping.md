---
title: Mapování vstupu do výstupních polí
titleSuffix: Azure Cognitive Search
description: Extrahujte a obohaťte zdrojová datová pole a mapujte na výstupní pole v indexu Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f0537af684632a08a39e3e681900d62238365073
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74280966"
---
# <a name="how-to-map-ai-enriched-fields-to-a-searchable-index"></a>Jak mapovat pole obohacená umělou ai do indexu s možnostmi vyhledávání

V tomto článku se dozvíte, jak mapovat obohacená vstupní pole do výstupních polí v indexu, který lze prohledávat. Po [definování sady dovedností](cognitive-search-defining-skillset.md)je nutné namapovat výstupní pole libovolné dovednosti, která přímo přispívá hodnotami k danému poli v indexu vyhledávání. 

Mapování výstupního pole jsou vyžadovány pro přesunutí obsahu z obohacených dokumentů do indexu.  Obohacený dokument je opravdu strom informací a přestože je podpora pro komplexní typy v indexu, někdy můžete chtít transformovat informace z obohaceného stromu do jednodušší typ (například pole řetězců). Mapování výstupních polí umožňuje provádět transformace obrazců dat sloučením informací.

## <a name="use-outputfieldmappings"></a>Použít outputFieldMappings
Chcete-li mapovat pole, přidejte `outputFieldMappings` do definice indexeru, jak je znázorněno níže:

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

Pro každé mapování výstupního pole nastavte umístění dat ve stromu obohaceného dokumentu (sourceFieldName) a název pole, na které odkazuje index (targetFieldName).

## <a name="flattening-information-from-complex-types"></a>Informace o sloučení sloučení ze složitých typů 

Cesta ve sourceFieldName může představovat jeden prvek nebo více prvků. Ve výše uvedeném příkladu ```/document/content/sentiment``` představuje ```/document/content/organizations/*/description``` jednu číselnou hodnotu, zatímco představuje několik popisů organizace. 

V případech, kdy existuje několik prvků, jsou "sloučí" do pole, které obsahuje každý z prvků. 

Konkrétněji, pro ```/document/content/organizations/*/description``` příklad data v poli *popisy* bude vypadat jako ploché pole popisů před získá indexovány:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```

Jedná se o důležitou zásadu, proto uvedeme další příklad. Představte si, že máte pole komplexní typy jako součást stromu obohacení. Řekněme, že je člen s názvem customEntities, který má pole komplexní typy, jako je popsáno níže.

```json
"document/customEntities": 
[
    {
        "name": "heart failure",
        "matches": [
            {
                "text": "heart failure",
                "offset": 10,
                "length": 12,
                "matchDistance": 0.0
            }
        ]
    },
    {
        "name": "morquio",
        "matches": [
            {
                "text": "morquio",
                "offset": 25,
                "length": 7,
                "matchDistance": 0.0
            }
        ]
    }
    //...
]
```

Předpokládejme, že váš index má pole s názvem 'nemoci' typu Collection(Edm.String), kde chcete uložit každý z názvů entit. 

To lze provést snadno pomocí\*symbolu " " takto:

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/customEntities/*/name",
            "targetFieldName": "diseases"
        }
    ]
```

Tato operace bude jednoduše "sloučí" každý z názvů vlastníentityprvky do jednoho pole řetězců, jako je tento:

```json
  "diseases" : ["heart failure","morquio"]
```

## <a name="next-steps"></a>Další kroky
Po namapování obohacených polí na prohledávatelná pole můžete nastavit atributy polí pro každé prohledávatelné pole [jako součást definice indexu](search-what-is-an-index.md).

Další informace o mapování polí najdete [v tématu Mapování polí v indexerech Azure Cognitive Search](search-indexer-field-mappings.md).
