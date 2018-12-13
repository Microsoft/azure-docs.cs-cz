---
title: Entita rozpoznávání kognitivního vyhledávání dovednosti – Azure Search
description: Extrahujte různé typy entit z textu v kanálu služby Azure Search kognitivního vyhledávání.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 9745934891cd7ba99fa821377318e38134b7d2a5
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53311860"
---
#    <a name="entity-recognition-cognitive-skill"></a>Kognitivní dovednosti rozpoznávání entit

**Rozpoznávání entit** dovednosti z textu extrahuje entit různých typů. 

> [!NOTE]
> Od 21. prosince 2018 se budou moct přidružit dovednosti Azure Search prostředku služeb Cognitive Services. To vám umožní spouštění poplatků za využití jeho dovedností. K tomuto datu také začneme pro extrakci image jako součást fáze hádání dokumentu. Extrakce textu z dokumentů se bude dál nabízet bez dalších poplatků.
>
> Provádění předdefinované dovednosti budou účtovat stávající [přejít ceny služeb Cognitive Services, platit jako můžete](https://azure.microsoft.com/pricing/details/cognitive-services/) . Ceny za extrakce Image se bude účtovat ceny verze preview a je popsaný na [stránce s cenami Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400). Přečtěte si [Další](cognitive-search-attach-cognitive-services.md).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.EntityRecognitionSkill

## <a name="data-limits"></a>Omezení dat
Maximální velikost záznamu by měla být 50 000 znaků pohledu `String.Length`. Pokud je potřeba rozdělit data před odesláním k extrakci klíčových frází, zvažte použití [dovedností rozdělit textové](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametry dovedností

Parametry jsou malá a velká písmena a jsou nepovinné.

| Název parametru     | Popis |
|--------------------|-------------|
| categories    | Pole kategorií, které má být extrahován.  Typy možných kategorií: `"Person"`, `"Location"`, `"Organization"`, `"Quantity"`, `"Datetime"`, `"URL"`, `"Email"`. Pokud je k dispozici žádná kategorie, budou vráceny všechny typy.|
|defaultLanguageCode |  Kód jazyka vstupního textu. Jsou podporovány následující jazyky: `de, en, es, fr, it`|
|minimumPrecision | Nevyužité. Vyhrazeno pro budoucí použití. |
|includeTypelessEntites | Pokud je nastavený na hodnotu true, pokud text obsahuje dobře známé entity, ale nelze zařadit do jedné z podporovaných kategorií, se vrátí jako součást `"entities"` komplexní výstupního pole. Výchozí hodnota je `false` |


## <a name="skill-inputs"></a>Vstupy dovedností

| Název vstupu      | Popis                   |
|---------------|-------------------------------|
| languageCode  | Volitelné. Výchozí hodnota je `"en"`.  |
| text          | Text, který se má analyzovat          |

## <a name="skill-outputs"></a>Výstupy dovedností

**POZNÁMKA:**: Pro všechny jazyky jsou podporovány všechny kategorie entity.
Pouze _en_, _es_ podporují extrakce `"Quantity"`, `"Datetime"`, `"URL"`, `"Email"` typy.

| Název výstupního     | Popis                   |
|---------------|-------------------------------|
| osoby      | Pole řetězců, přičemž každý řetězec představuje jméno osoby. |
| umístění  | Pole řetězců, kde každý řetězec představuje umístění. |
| organizations  | Pole řetězců, přičemž každý řetězec představuje organizace. |
| množství  | Pole řetězců, přičemž každý řetězec představuje množství. |
| data a času  | Pole řetězců, přičemž každý řetězec představuje datum a čas (jak se zobrazí v textu) hodnotu. |
| adresy URL | Pole řetězců, přičemž každý řetězec představuje adresu URL |
| e-maily | Pole řetězců, přičemž každý řetězec představuje e-mailu |
| namedEntities | Pole komplexní typy, které obsahují následující pole: <ul><li>category</li> <li>hodnota (název skutečné entity)</li><li>posun (umístění, kde se nachází v textu)</li><li>spolehlivosti (nepoužitý teď. Bude nastavena na hodnotu-1)</li></ul> |
| entity | Pole komplexní typy, které obsahuje podrobné informace o entitách extrahovat z textu u následujících polí <ul><li> Name (název skutečné entity. To představuje "normalizovaných" formuláře)</li><li> wikipediaId</li><li>wikipediaLanguage</li><li>wikipediaUrl (odkaz na stránku Wikipedia entity)</li><li>bingId</li><li>Typ (kategorie entity rozpoznán)</li><li>Podtyp (k dispozici pouze pro určité kategorie, to dává s podrobnějším zobrazením typu entity)</li><li> odpovídá (komplexní kolekci, která obsahuje)<ul><li>text (nezpracovaný text entity)</li><li>posun (umístění, kde se nachází)</li><li>Length (délka textu nezpracované entity)</li></ul></li></ul> |

##  <a name="sample-definition"></a>Ukázková definice

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person", "Email"],
    "defaultLanguageCode": "en",
    "includeTypelessEntities": true,
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
      },
      {
        "name": "emails",
        "targetName": "contact"
      },
      {
        "name": "entities"
      }
    ]
  }
```
##  <a name="sample-input"></a>Ukázkový vstup

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Contoso corporation was founded by John Smith. They can be reached at contact@contoso.com",
             "languageCode": "en"
           }
      }
    ]
}
```

##  <a name="sample-output"></a>Ukázkový výstup

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "persons": [ "John Smith"],
        "emails":["contact@contoso.com"],
        "namedEntities": 
        [
          {
            "category":"Person",
            "value": "John Smith",
            "offset": 35,
            "confidence": -1
          }
        ],
        "entities":  
        [
          {
            "name":"John Smith",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Person",
            "subType": null,
            "matches": [{
                "text": "John Smith",
                "offset": 35,
                "length": 10
            }]
          },
          {
            "name": "contact@contoso.com",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Email",
            "subType": null,
            "matches": [
            {
                "text": "contact@contoso.com",
                "offset": 70,
                "length": 19
            }]
          },
          {
            "name": "Contoso",
            "wikipediaId": "Contoso",
            "wikipediaLanguage": "en",
            "wikipediaUrl": "https://en.wikipedia.org/wiki/Contoso",
            "bingId": "349f014e-7a37-e619-0374-787ebb288113",
            "type": null,
            "subType": null,
            "matches": [
            {
                "text": "Contoso",
                "offset": 0,
                "length": 7
            }]
          }
        ]
      }
    }
  ]
}
```


## <a name="error-cases"></a>Případy chyb
Pokud kód jazyka pro dokument není podporován, vrátí se chyba a jsou extrahovány žádné entity.

## <a name="see-also"></a>Další informace najdete v tématech

+ [Předdefinované dovednosti](cognitive-search-predefined-skills.md)
+ [Definování dovedností](cognitive-search-defining-skillset.md)