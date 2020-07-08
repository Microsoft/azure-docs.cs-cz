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
ms.openlocfilehash: c9b0b34202f35babcaa3dce37331d31edf641254
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85557274"
---
# <a name="how-to-map-ai-enriched-fields-to-a-searchable-index"></a>Jak mapovat pole obohacená AI na index s možností prohledávání

V tomto článku se dozvíte, jak namapovat obohacená vstupní pole na výstupní pole v indexu s možností prohledávání. Po [Definování dovednosti](cognitive-search-defining-skillset.md)je nutné namapovat výstupní pole libovolné dovednosti, která přímo přispívá k danému poli v indexu vyhledávání. 

Pro přesun obsahu z obohacených dokumentů do indexu jsou vyžadovány mapování polí výstupu.  Obohacený dokument je ve skutečnosti stromovou strukturou informací, a to i v případě, že existuje podpora složitých typů v indexu, někdy můžete chtít transformovat informace z obohaceného stromu na jednoduchý typ (např. pole řetězců). Mapování polí výstupu vám umožní provádět transformace datových tvarů sloučením informací.

> [!NOTE]
> Nedávno jsme povolili funkci mapování funkcí pro mapování polí výstupu. Další podrobnosti o funkcích mapování najdete v tématu [funkce mapování polí](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#field-mapping-functions) .

## <a name="use-outputfieldmappings"></a>Použití outputFieldMappings
Pro mapování polí přidejte `outputFieldMappings` do definice indexeru, jak je znázorněno níže:

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
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
            "targetFieldName": "descriptions",
            "mappingFunction": {
                "name": "base64Decode"
            }
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

Pro každé mapování polí výstupu nastavte umístění dat ve stromu obohaceného dokumentu (sourceFieldName) a název pole, jak je odkazováno v indexu (targetFieldName).

## <a name="flattening-information-from-complex-types"></a>Sloučení informací ze složitých typů 

Cesta v sourceFieldName může představovat jeden nebo více elementů. V předchozím příkladu ```/document/content/sentiment``` představuje jednu číselnou hodnotu, zatímco ```/document/content/organizations/*/description``` představuje několik popisů organizace. 

V případech, kdy existuje několik prvků, jsou "shrnuty" do pole, které obsahuje každý prvek. 

Čím více z ```/document/content/organizations/*/description``` nich je v příkladu, data v poli *descriptions* budou vypadat jako ploché pole popisů předtím, než se naindexuje:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```

Toto je důležitý princip, takže budeme poskytovat další příklad. Představte si, že máte pole komplexních typů jako součást stromu rozšíření. Řekněme, že je členem s názvem customEntities, který obsahuje pole komplexních typů, jako je ten, který je popsán níže.

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

Předpokládejme, že váš index obsahuje pole s názvem "nemoci" typu Collection (EDM. String), kde byste chtěli uložit všechny názvy entit. 

To lze provést snadno pomocí \* symbolu "", jak je znázorněno níže:

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/customEntities/*/name",
            "targetFieldName": "diseases"
        }
    ]
```

Tato operace bude jednoduše "shrnout" jednotlivé názvy customEntities prvků do jediného pole řetězců takto:

```json
  "diseases" : ["heart failure","morquio"]
```

## <a name="next-steps"></a>Další kroky
Po namapování obohacených polí na prohledávatelné pole můžete [jako součást definice indexu](search-what-is-an-index.md)nastavit atributy polí pro každé z polí, která lze prohledávat.

Další informace o mapování polí najdete v tématu [mapování polí v indexerech Azure kognitivní hledání](search-indexer-field-mappings.md).
