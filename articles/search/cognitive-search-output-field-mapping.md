---
title: Kognitivní vyhledávání map rozšiřují vstupní pole pro výstupní pole – Azure Search
description: Extrahovat a rozšiřte pole zdroje dat a mapování pro výstupní pole v indexu Azure Search.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 506acee6cd9cd3c50e10f1c45768230564eeaaf1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65022081"
---
# <a name="how-to-map-enriched-fields-to-a-searchable-index"></a>Jak namapovat bohatších možností pole prohledávatelný index

V tomto článku se dozvíte, jak mapovat bohatších možností vstupní pole pro výstupní pole v prohledávatelný index. Jakmile budete mít [definované dovedností](cognitive-search-defining-skillset.md), je třeba namapovat pole výstup všechny dovednosti, které přímo přispívá hodnoty pro dané pole v indexu vyhledávání. Mapování polí jsou požadovány pro přesun obsahu z bohatších možností dokumentů do indexu.


## <a name="use-outputfieldmappings"></a>Použití outputFieldMappings
Chcete-li mapování polí, přidejte `outputFieldMappings` do definice indexeru jak je znázorněno níže:

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```

Text žádosti strukturovaná následujícím způsobem:

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
Pro každé pole výstup mapování, nastavte název pole bohatších možností (sourceFieldName) a název pole, který jste použili v indexu (targetFieldName).

Cesta v sourceFieldName může představovat jeden element nebo víc elementů. V příkladu výše ```/document/content/sentiment``` představuje jednu číselnou hodnotu, zatímco ```/document/content/organizations/*/description``` představuje několik popis organizace. V případech, kdy existuje několik elementů, jejich se "sloučí" do pole, které obsahuje všechny prvky. Více namítají pro ```/document/content/organizations/*/description``` například dat v *popisy* pole může vypadat třeba bez stromové struktury pole popisů před jejich indexováním:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```
## <a name="next-steps"></a>Další postup
Jakmile bohatších možností pole jsou namapovány na prohledávatelná pole, můžete nastavit atributy pole pro každý prohledávatelná pole [jako součást definice indexu](search-what-is-an-index.md).

Další informace o mapování polí najdete v tématu [mapování polí v indexerech Azure Search](search-indexer-field-mappings.md).
