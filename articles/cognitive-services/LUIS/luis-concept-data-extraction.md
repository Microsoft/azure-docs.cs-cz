---
title: Extrakce dat
titleSuffix: Language Understanding - Azure Cognitive Services
description: Zjistěte, jaká data může být extrahována z Language Understanding (LUIS)
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: diberry
ms.openlocfilehash: 385e0065123b86f8f8b7c8503fe6de6daa7c6562
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55874120"
---
# <a name="data-extraction-from-intents-and-entities"></a>Extrakce dat z záměry a entity
Služba LUIS umožňuje získat informace z projevy přirozeného jazyka uživatele. Informace je extrahován tak, že jej lze použít program, aplikace nebo chatovací robot k akci. V následující částech se dozvíte, jaká data jsou vrácena z záměry a entity s příklady JSON.

Nejtěžší data k extrakci jsou data zjištěné počítače, protože není přesný text v případě shody. Extrakce dat z počítače zjistili [entity](luis-concept-entity-types.md) musí být součástí [vytváření cyklu](luis-concept-app-iteration.md) dokud nejste jisti, můžete přijímat data očekáváte, že.

## <a name="data-location-and-key-usage"></a>Umístění a klíč využití dat
Služba LUIS poskytuje data z publikovanému [koncový bod](luis-glossary.md#endpoint). **Požadavek HTTPS** (POST nebo GET), obsahuje utterance, jakož i některé volitelné konfigurace, jako je pracovní nebo produkční prostředí.

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

`appID` Je k dispozici na **nastavení** stránky vaší aplikace LUIS stejně jako část adresy URL (po `/apps/`) při úpravách aplikace LUIS. `subscription-key` Je koncový bod klíče použitého k dotazování vaší aplikace. Při vytváření/starter bezplatný klíč můžete použít při seznamování se služba LUIS, je potřeba změnit klíč koncového bodu na klíč, který podporuje vaši [očekávané využití LUIS](luis-boundaries.md#key-limits). `timezoneOffset` Jednotka je minut.

**Odpovědi HTTP** obsahuje všechny informace o záměru a entity LUIS můžete zjistit na základě aktuální publikované modelu buď koncový bod přípravném nebo produkčním prostředí. Koncový bod adresy URL se nachází na [LUIS](luis-reference-regions.md) webu v **spravovat** části na **klíče a koncových bodů** stránky.

## <a name="data-from-intents"></a>Data, od záměrů
Primární data jsou nahoře vyhodnocování **záměru název**. Použití `MyStore` [rychlý Start](luis-quickstart-intents-only.md), je odpověď na koncový bod:

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "entities": []
}
```

|Datový objekt|Typ dat|Umístění dat|Hodnota|
|--|--|--|--|
|Záměr|Řetězec|topScoringIntent.intent|"GetStoreInfo"|

Pokud chatovací robot nebo LUIS volající aplikace provádí rozhodnutí na základě více než jeden záměru skóre, vrátí výsledky všech příkazů tak, že nastavíte parametr querystring `verbose=true`. Koncový bod odpověď je:

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "intents": [
    {
      "intent": "GetStoreInfo",
      "score": 0.984749258
    },
    {
      "intent": "None",
      "score": 0.2040639
    }
  ],
  "entities": []
}
```

Příkazů jsou seřazené od nejvyšší k nejnižší skóre.

|Datový objekt|Typ dat|Umístění dat|Hodnota|Skóre|
|--|--|--|--|:--|
|Záměr|Řetězec|.intent záměry [0]|"GetStoreInfo"|0.984749258|
|Záměr|Řetězec|.intent záměry [1]|"None"|0.0168218873|

Pokud chcete přidat předem připravených domén, záměru název označuje domény, jako například `Utilties` nebo `Communication` a také záměr:

```JSON
{
  "query": "Turn on the lights next monday at 9am",
  "topScoringIntent": {
    "intent": "Utilities.ShowNext",
    "score": 0.07842206
  },
  "intents": [
    {
      "intent": "Utilities.ShowNext",
      "score": 0.07842206
    },
    {
      "intent": "Communication.StartOver",
      "score": 0.0239675418
    },
    {
      "intent": "None",
      "score": 0.0168218873
    }],
  "entities": []
}
```

|Domain (Doména)|Datový objekt|Typ dat|Umístění dat|Hodnota|
|--|--|--|--|--|
|Veřejné služby|Záměr|Řetězec|.intent záměry [0]|"<b>Nástroje</b>. ShowNext"|
|Komunikace|Záměr|Řetězec|.intent záměry [1]|<b>Komunikace</b>. StartOver"|
||Záměr|Řetězec|.intent záměry [2]|"None"|


## <a name="data-from-entities"></a>Data z entity
Většina chatovacích a aplikace potřebovat vyšší než záměru názvu. Tato data dalších, volitelných pochází z entity objeví ve službě utterance. Každý typ entity, vrátí různé informace o zjištěné shodě.

Jedno slovo nebo frázi v utterance může odpovídat více než jednu entitu. V takovém případě se vrátí každá odpovídající entita s jeho skóre.

Všechny entity jsou vráceny v **entity** pole odpovědi z koncového bodu:

```JSON
"entities": [
  {
    "entity": "bob jones",
    "type": "Name",
    "startIndex": 0,
    "endIndex": 8,
    "score": 0.473899543
  },
  {
    "entity": "3",
    "type": "builtin.number",
    "startIndex": 16,
    "endIndex": 16,
    "resolution": {
      "value": "3"
    }
  }
]
```

## <a name="tokenized-entity-returned"></a>Vrátí tokenizovaná entity
Několik [jazykové verze](luis-language-support.md#tokenization) vrátit objekt entity s `entity` hodnotu [tokenizovaného](luis-glossary.md#token). Hodnota startIndex a hodnota endIndex vrácený LUIS v objektu entity nemapovaly na nový, tokenizovaná hodnotu ale místo toho použije u původního dotazu v pořadí pro extrakci nezpracované entity prostřednictvím kódu programu. 

Například v němčině, slovo `das Bauernbrot` tokenizovaného do `das bauern brot`. Hodnotu tokenizovaná `das bauern brot`, je vrácena a původní hodnotu z startIndex a hodnota endIndex původního dotazu, získáte prostřednictvím kódu programu určit `das Bauernbrot`.

## <a name="simple-entity-data"></a>Jednoduchou entitu dat

A [jednoduchou entitu](luis-concept-entity-types.md) je hodnota zjištěné počítače. Může být slova nebo fráze.

`Bob Jones wants 3 meatball pho`

V předchozím utterance `Bob Jones` je označena jako jednoduchý `Customer` entity.

Data vrácená z koncového bodu obsahuje název entity, zjištěný text z utterance, umístění zjištěných textu a skóre:

```JSON
"entities": [
  {
  "entity": "bob jones",
  "type": "Customer",
  "startIndex": 0,
  "endIndex": 8,
  "score": 0.473899543
  }
]
```

|Datový objekt|Název entity|Hodnota|
|--|--|--|
|Jednoduché Entity|"Zákazník"|"bob jones"|

## <a name="hierarchical-entity-data"></a>Data hierarchická entity

[Hierarchické](luis-concept-entity-types.md) entity jsou zjištěné počítače a může obsahovat slova nebo fráze. Podřízené položky se identifikují podle kontextu. Pokud hledáte vztahu nadřazený podřízený se shodou přesný text, použití [seznamu](#list-entity-data) entity.

`book 2 tickets to paris`

V předchozím utterance `paris` má popisek `Location::ToLocation` podřízený `Location` hierarchické entity.

Data vrácená z koncového bodu obsahuje název entity a názvu podřízené, zjištěný text z utterance, umístění zjištěných textu a skóre:

```JSON
"entities": [
  {
    "entity": "paris",
    "type": "Location::ToLocation",
    "startIndex": 18,
    "endIndex": 22,
    "score": 0.6866132
  }
]
```

|Datový objekt|Nadřazené|Podřízené|Hodnota|
|--|--|--|--|--|
|Hierarchické Entity|Umístění|ToLocation|"Paříž"|

## <a name="composite-entity-data"></a>Složený entitu dat
[Složený](luis-concept-entity-types.md) entity jsou zjištěné počítače a může obsahovat slova nebo fráze. Představte si třeba složený entity z předem připravených `number` a `Location::ToLocation` s utterance následující:

`book 2 tickets to paris`

Všimněte si, že `2`, je číslo, a `paris`, slova mezi nimi, které nejsou součástí žádné entity, které mají ToLocation. Zelená podtržení, používané s popiskem utterance v [LUIS](luis-reference-regions.md) webu, označuje složený entity.

![Složený Entity](./media/luis-concept-data-extraction/composite-entity.png)

Složený entity jsou vráceny v `compositeEntities` pole a všechny entity v rámci složeného jsou vráceny také v `entities` pole:

```JSON
  "entities": [
    {
      "entity": "paris",
      "type": "Location::ToLocation",
      "startIndex": 18,
      "endIndex": 22,
      "score": 0.956998169
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 5,
      "endIndex": 5,
      "resolution": {
        "value": "2"
      }
    },
    {
      "entity": "2 tickets to paris",
      "type": "Order",
      "startIndex": 5,
      "endIndex": 22,
      "score": 0.7714499
    }
  ],
  "compositeEntities": [
    {
      "parentType": "Order",
      "value": "2 tickets to paris",
      "children": [
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "Location::ToLocation",
          "value": "paris"
        }
      ]
    }
  ]
```    

|Datový objekt|Název entity|Hodnota|
|--|--|--|
|Předem připravených entit – počet|"builtin.number"|"2"|
|Hierarchické entitu, umístění|"Location::ToLocation"|"Paříž"|

## <a name="list-entity-data"></a>Seznam dat entity

A [seznamu](luis-concept-entity-types.md) entity není zjištěné počítače. Se neshoduje přesný text. Představuje seznam položek v seznamu spolu s synonyma pro tyto položky. Služba LUIS označí všechny shody jako položka v seznamu jako entity v odpovědi. Synonymum může být ve více než jeden seznam.

Předpokládejme, že aplikace má seznam s názvem `Cities`, což každodenně názvy měst včetně město letiště (Sea hrají), kód letiště (SEA), poštovní směrovací číslo poštovní směrovací (98101) a phone oblasti kódu (206).

|Položka seznamu|Synonyma položky|
|---|---|
|Seattle|mořská hrají, moři, 98101, 206, + 1 |
|Paříž|cdg roissy historie, 75001, 1, +33|

`book 2 tickets to paris`

V předchozím utterance slovo `paris` je namapována na Paříž položky jako součást `Cities` seznam entit. Seznam entit odpovídá normalizovaný název položky i synonyma položky.

```JSON
"entities": [
  {
    "entity": "paris",
    "type": "Cities",
    "startIndex": 18,
    "endIndex": 22,
    "resolution": {
      "values": [
        "Paris"
      ]
    }
  }
]
```

Jiný příklad utterance pomocí synonymum pro Paříž:

`book 2 tickets to roissy`

```JSON
"entities": [
  {
    "entity": "roissy",
    "type": "Cities",
    "startIndex": 18,
    "endIndex": 23,
    "resolution": {
      "values": [
        "Paris"
      ]
    }
  }
]
```

## <a name="prebuilt-entity-data"></a>Data předem připravených entit
[Předem připravené](luis-concept-entity-types.md) entity jsou zjištěny shoda s regulárním výrazem pomocí open source [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text) projektu. Předem připravených entit se vrátí jako pole entity a použijte předponu názvu typu `builtin::`. Následující text je příkladu utterance s vrácené předem připravených entit:

`Dec 5th send to +1 360-555-1212`

```JSON
"entities": [
    {
      "entity": "dec 5th",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2017-12-05"
          },
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2018-12-05"
          }
        ]
      }
    },
    {
      "entity": "1",
      "type": "builtin.number",
      "startIndex": 18,
      "endIndex": 18,
      "resolution": {
        "value": "1"
      }
    },
    {
      "entity": "360",
      "type": "builtin.number",
      "startIndex": 20,
      "endIndex": 22,
      "resolution": {
        "value": "360"
      }
    },
    {
      "entity": "555",
      "type": "builtin.number",
      "startIndex": 26,
      "endIndex": 28,
      "resolution": {
        "value": "555"
      }
    },
    {
      "entity": "1212",
      "type": "builtin.number",
      "startIndex": 32,
      "endIndex": 35,
      "resolution": {
        "value": "1212"
      }
    },
    {
      "entity": "5th",
      "type": "builtin.ordinal",
      "startIndex": 4,
      "endIndex": 6,
      "resolution": {
        "value": "5"
      }
    },
    {
      "entity": "1 360 - 555 - 1212",
      "type": "builtin.phonenumber",
      "startIndex": 18,
      "endIndex": 35,
      "resolution": {
        "value": "1 360 - 555 - 1212"
      }
    }
  ]
```

## <a name="regular-expression-entity-data"></a>Data entity regulárního výrazu
[Regulární výraz](luis-concept-entity-types.md) entity jsou zjištěny vlastnosti autorefresh pomocí výrazu zadáte při vytváření entit shoda s regulárním výrazem. Při použití `kb[0-9]{6}` jako definice entity regulárních výrazů, je následující odpověď JSON příkladu utterance s entitami vrácené regulárního výrazu dotazu `When was kb123456 published?`:

```JSON
{
  "query": "when was kb123456 published?",
  "topScoringIntent": {
    "intent": "FindKBArticle",
    "score": 0.933641255
  },
  "intents": [
    {
      "intent": "FindKBArticle",
      "score": 0.933641255
    },
    {
      "intent": "None",
      "score": 0.04397359
    }
  ],
  "entities": [
    {
      "entity": "kb123456",
      "type": "KB number",
      "startIndex": 9,
      "endIndex": 16
    }
  ]
}
```

## <a name="extracting-names"></a>Extrahování názvů
Získávání názvů z utterance je obtížné, protože název může být téměř libovolnou kombinací písmena a slova. V závislosti na tom, jaký typ název jste už extrahování máte několik možností. Následující návrhy nejsou pravidla, ale další pokyny.

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>Přidání předem připravených entit PersonName a GeographyV2

[PersonName](luis-reference-prebuilt-person.md) a [GeographyV2](luis-reference-prebuilt-geographyV2.md) entity jsou dostupné v některých [jazykových verzí jazyka](luis-reference-prebuilt-entities.md). 

### <a name="names-of-people"></a>Jména osob
Název lidí může mít některé mírné formátu v závislosti na jazyk a jazykovou verzi. Použít hierarchická entity s jména a příjmení jako podřízené položky nebo jednoduché entity s rolemi křestní jméno a příjmení. Ujistěte se, že poskytnout příklady, které používají název první a poslední v různých částech utterance, v různých délek projevy a projevy přes všechny záměry včetně žádný záměru. [Kontrola](luis-how-to-review-endoint-utt.md) projevy koncový bod v pravidelných intervalech, aby všechny názvy, které nebyly správně předpovědět popisků.

### <a name="names-of-places"></a>Názvy míst
Názvy umístění nastavují a známé jako je například města, okresy, státy, provincie a zemí. Pokud vaše aplikace používá know sadu umístěních, zvažte seznam entit. Pokud chcete najít že všechny umístit názvy, vytvořit jednoduchou entitu a poskytují řadu příkladů. Přidáte frázi seznam místních jmen posílit jaké místo názvů vypadají ve vaší aplikaci. [Kontrola](luis-how-to-review-endoint-utt.md) projevy koncový bod v pravidelných intervalech, aby všechny názvy, které nebyly správně předpovědět popisků.

### <a name="new-and-emerging-names"></a>Nové a chystané názvy
Některé aplikace musí být schopna najít nové a chystané názvy, například produkty nebo společnosti. Tyto typy názvů je automatizování nejobtížnějších typu extrakce. Začít s jednoduchou entitu a přidat seznam frázi. [Kontrola](luis-how-to-review-endoint-utt.md) projevy koncový bod v pravidelných intervalech, aby všechny názvy, které nebyly správně předpovědět popisků.

## <a name="pattern-roles-data"></a>Vzor role dat
Role jsou kontextové rozdíly entit.

```JSON
{
  "query": "move bob jones from seattle to redmond",
  "topScoringIntent": {
    "intent": "MoveAssetsOrPeople",
    "score": 0.9999998
  },
  "intents": [
    {
      "intent": "MoveAssetsOrPeople",
      "score": 0.9999998
    },
    {
      "intent": "None",
      "score": 1.02040713E-06
    },
    {
      "intent": "GetEmployeeBenefits",
      "score": 6.12244548E-07
    },
    {
      "intent": "GetEmployeeOrgChart",
      "score": 6.12244548E-07
    },
    {
      "intent": "FindForm",
      "score": 1.1E-09
    }
  ],
  "entities": [
    {
      "entity": "bob jones",
      "type": "Employee",
      "startIndex": 5,
      "endIndex": 13,
      "score": 0.922820568,
      "role": ""
    },
    {
      "entity": "seattle",
      "type": "Location",
      "startIndex": 20,
      "endIndex": 26,
      "score": 0.948008537,
      "role": "Origin"
    },
    {
      "entity": "redmond",
      "type": "Location",
      "startIndex": 31,
      "endIndex": 37,
      "score": 0.7047979,
      "role": "Destination"
    }
  ]
}
```

## <a name="patternany-entity-data"></a>Pattern.Any entity data
Pattern.Any entity jsou proměnné délky entit používaných v projevy šablony z [vzor](luis-concept-patterns.md).

```JSON
{
  "query": "where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.999999464
  },
  "intents": [
    {
      "intent": "FindForm",
      "score": 0.999999464
    },
    {
      "intent": "GetEmployeeBenefits",
      "score": 4.883697E-06
    },
    {
      "intent": "None",
      "score": 1.02040713E-06
    },
    {
      "intent": "GetEmployeeOrgChart",
      "score": 9.278342E-07
    },
    {
      "intent": "MoveAssetsOrPeople",
      "score": 9.278342E-07
    }
  ],
  "entities": [
    {
      "entity": "understand your responsibilities as a member of the community",
      "type": "FormName",
      "startIndex": 18,
      "endIndex": 78,
      "role": ""
    }
  ]
}
```


## <a name="sentiment-analysis"></a>Analýza mínění
Pokud je nakonfigurovaná analýza mínění, LUIS odpověď json zahrnuje analýzu subjektivního hodnocení. Další informace o analýzu mínění v [rozhraní Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) dokumentaci.

### <a name="sentiment-data"></a>Data o mínění
Je skóre mezi 1 a 0 označující pozitivní mínění data (blíže 1) ani na zápornou (blíže 0) mínění data.

Když je jazyková verze `en-us`, odpověď je:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

Pro všechny jiné jazykové verze odpověď je:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```


### <a name="key-phrase-extraction-entity-data"></a>Data entity extrakce klíčových frází
Entity extrakce klíčových frází vrací klíčové fráze v utterance, poskytuje [rozhraní Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/).

```JSON
{
  "query": "Is there a map of places with beautiful views on a favorite trail?",
  "topScoringIntent": {
    "intent": "GetJobInformation",
    "score": 0.764368951
  },
  "intents": [
    ...
  ],
  "entities": [
    {
      "entity": "beautiful views",
      "type": "builtin.keyPhrase",
      "startIndex": 30,
      "endIndex": 44
    },
    {
      "entity": "map of places",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 23
    },
    {
      "entity": "favorite trail",
      "type": "builtin.keyPhrase",
      "startIndex": 51,
      "endIndex": 64
    }
  ]
}
```

## <a name="data-matching-multiple-entities"></a>Data odpovídající více entit

Služba LUIS vrátí všechny entity v utterance. V důsledku toho může váš robot třeba, aby rozhodování na základě výsledků. Utterance může mít mnoho entit v utterance:

`book me 2 adult business tickets to paris tomorrow on air france`

Koncový bod služby LUIS můžete zjistit na stejná data v různé entity:

```JSON
{
  "query": "book me 2 adult business tickets to paris tomorrow on air france",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 1.0
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 1.0
    },
    {
      "intent": "Concierge",
      "score": 0.04216196
    },
    {
      "intent": "None",
      "score": 0.03610297
    }
  ],
  "entities": [
    {
      "entity": "air france",
      "type": "Airline",
      "startIndex": 54,
      "endIndex": 63,
      "score": 0.8291798
    },
    {
      "entity": "adult",
      "type": "Category",
      "startIndex": 10,
      "endIndex": 14,
      "resolution": {
        "values": [
          "adult"
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 36,
      "endIndex": 40,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    },
    {
      "entity": "tomorrow",
      "type": "builtin.datetimeV2.date",
      "startIndex": 42,
      "endIndex": 49,
      "resolution": {
        "values": [
          {
            "timex": "2018-02-21",
            "type": "date",
            "value": "2018-02-21"
          }
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Location::ToLocation",
      "startIndex": 36,
      "endIndex": 40,
      "score": 0.9730773
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 8,
      "endIndex": 8,
      "resolution": {
        "value": "2"
      }
    },
    {
      "entity": "business",
      "type": "Seat",
      "startIndex": 16,
      "endIndex": 23,
      "resolution": {
        "values": [
          "business"
        ]
      }
    },
    {
      "entity": "2 adult business",
      "type": "TicketSeatOrder",
      "startIndex": 8,
      "endIndex": 23,
      "score": 0.8784727
    }
  ],
  "compositeEntities": [
    {
      "parentType": "TicketSeatOrder",
      "value": "2 adult business",
      "children": [
        {
          "type": "Category",
          "value": "adult"
        },
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "Seat",
          "value": "business"
        }
      ]
    }
  ]
}
```

## <a name="data-matching-multiple-list-entities"></a>Data odpovídající více seznam entit

Pokud slovo nebo frázi, odpovídá více než jednu entitu seznamu, koncový bod dotaz vrátí Každá entita seznamu.

Pro dotaz `when is the best time to go to red rock?`, a aplikace obsahuje slovo `red` ve více než jeden seznam, LUIS, rozpozná všechny entity a vrátí pole entit jako součást koncového bodu odpověď JSON: 

```JSON
{
  "query": "when is the best time to go to red rock?",
  "topScoringIntent": {
    "intent": "Calendar.Find",
    "score": 0.06701678
  },
  "entities": [
    {
      "entity": "red",
      "type": "Colors",
      "startIndex": 31,
      "endIndex": 33,
      "resolution": {
        "values": [
          "Red"
        ]
      }
    },
    {
      "entity": "red rock",
      "type": "Cities",
      "startIndex": 31,
      "endIndex": 38,
      "resolution": {
        "values": [
          "Destinations"
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>Další postup

Zobrazit [přidat entity](luis-how-to-add-entities.md) získat další informace o přidání entity do aplikace LUIS.
