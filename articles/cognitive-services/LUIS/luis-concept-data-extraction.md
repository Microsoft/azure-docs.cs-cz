---
title: Základními pojmy extrakce dat v LEOŠ - Azure | Microsoft Docs
description: Zjistěte, jaký typ dat lze extrahovat z znalosti jazyka (LEOŠ)
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr;
ms.openlocfilehash: ad9eaa966aafc4a9fc526d44d57c68cdaee3a5e0
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "35343932"
---
# <a name="data-extraction"></a>Extrakce dat
LEOŠ vám dává možnost pro získání informací z utterances přirozeného jazyka uživatele. Informace se extrahuje tak, že ho můžete používat programu, aplikace nebo chatbot provádět akci.

V následujících částech se dozvíte, jaká data jsou vrácena z tříd Intent a entity s příklady JSON. Nejtěžší data extrahovat jsou data naučili počítač, protože se nejedná o přesný text shodu. Extrahování dat naučili počítač [entity](luis-concept-entity-types.md) musí být součástí [vytváření cyklu](luis-concept-app-iteration.md) dokud jste si jisti, přijmout data očekáváte. 

## <a name="data-location-and-key-usage"></a>Umístění a klíč využití dat
LEOŠ poskytuje data z k publikovanému [koncový bod](luis-glossary.md#endpoint). **Požadavek HTTPS** (Metoda POST nebo GET) obsahuje utterance, jakož i některé volitelné konfigurace, jako je například pracovním nebo produkčním prostředí. 

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

`appID` Je k dispozici na **nastavení** stránky vaší aplikace LEOŠ, stejně jako část adresy URL (po `/apps/`) při úpravách LEOŠ aplikaci. `subscription-key` Je koncový bod klíč používaný k dotazování vaší aplikace. I když můžete používat klíč pro tvorbu nebo starter volné při zjišťujete LEOŠ, je potřeba změnit předplatné klíč na klíč, který podporuje vaše [očekávaného využití LEOŠ](luis-boundaries.md#key-limits). `timezoneOffset` Jednotka je minut.

**HTTPS odpovědi** obsahuje všechny informace o záměru a entity můžete určit LEOŠ podle aktuální publikované model buď pracovním nebo produkčním koncový bod. Koncový bod adresy URL nachází na [LEOŠ] [ LUIS] webu **publikovat** stránky. 

## <a name="data-from-intents"></a>Data z tříd Intent
Primární data je horní vyhodnocování **záměrné název**. Pomocí `MyStore` [rychlý Start](luis-quickstart-intents-only.md), je odpověď na koncový bod:

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

Pokud chatbot nebo LEOŠ volání aplikace provádí rozhodnutí založené na více než jeden záměrné skóre, vrátí skóre všechny tříd Intent nastavením parametru řetězce dotazu `verbose=true`. Koncový bod odpověď je:

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

Záměry jsou seřazena od nejvyšší po nejnižší skóre.

|Datový objekt|Typ dat|Umístění dat|Hodnota|Skóre|
|--|--|--|--|:--|
|Záměr|Řetězec|.intent záměry [0]|"GetStoreInfo"|0.984749258|
|Záměr|Řetězec|.intent záměry [1]|"Žádný"|0.0168218873|

Pokud přidáte předem domén, záměrné název označuje domény, například `Utilties` nebo `Communication` a také záměr:

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
    
|Doména|Datový objekt|Typ dat|Umístění dat|Hodnota|
|--|--|--|--|--|
|Veřejné služby|Záměr|Řetězec|.intent záměry [0]|"<b>Nástroje</b>. ShowNext"|
|Komunikace|Záměr|Řetězec|.intent záměry [1]|<b>Komunikace</b>. StartOver"|
||Záměr|Řetězec|.intent záměry [2]|"Žádný"|


## <a name="data-from-entities"></a>Data z entit
Většina chatbots a aplikace potřebují více než záměrné název. Tato data dalších, volitelných pochází z entit v utterance. Každý typ entity vrátí různé informace o shodě. 

Jediné slovo nebo frázi v utterance může odpovídat více než jedna entita. V takovém případě se vrátí každé odpovídající entity s jeho skóre. 

Jsou vráceny všechny entity v **entity** pole odpovědi z koncového bodu:

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

## <a name="tokenized-entity-returned"></a>Tokenizovaná entity vrátil
Několik [jazykové verze](luis-supported-languages.md#tokenization) vrátit objekt entity se `entity` hodnotu [tokenizovaného](luis-glossary.md#token). Počáteční index a endIndex vrácený LEOŠ v objekt entity se nemapují na nové, tokenizovaná hodnotu ale na původní dotaz v pořadí pro vás k extrakci nezpracovaná entity prostřednictvím kódu programu. 

Například v němčině, slovo `das Bauernbrot` je tokenizovaného do `das bauern brot`. Tokenizovaná hodnota `das bauern brot`, je vrácen a původní hodnotu lze prostřednictvím kódu programu určit počáteční index a endIndex původní dotaz, která poskytuje `das Bauernbrot`.

## <a name="simple-entity-data"></a>Jednoduché entity data

A [jednoduché entity](luis-concept-entity-types.md) je hodnota naučili počítače. Může být slovo nebo frázi. 

`Bob Jones wants 3 meatball pho`

V předchozích utterance `Bob Jones` je označený jako jednoduchý `Customer` entity.

Data vrácená z koncového bodu zahrnuje název entity, zjištěné text z utterance, umístění zjištěných text a skóre:

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
|Jednoduché Entity|"Zákazník"|"bob Petr"|

## <a name="hierarchical-entity-data"></a>Hierarchická entity data

[Hierarchická](luis-concept-entity-types.md) entity se naučili počítač a může obsahovat slovo nebo frázi. Podřízené objekty jsou určeny kontextu. Pokud hledáte relaci nadřazený podřízený s shodou přesný text, použijte [seznamu](#list-entity-data) entity. 

`book 2 tickets to paris`

V předchozích utterance `paris` označené `Location::ToLocation` podřízeným `Location` hierarchické entity. 

Data vrácená z koncového bodu zahrnuje název entity a název podřízené, zjištěné text z utterance, umístění zjištěných text a skóre: 

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
|Hierarchická Entity|Umístění|ToLocation|"Paříž"|

## <a name="composite-entity-data"></a>Složené entity data
[Složené](luis-concept-entity-types.md) entity se naučili počítač a může obsahovat slovo nebo frázi. Představte si třeba složené entity předem `number` a `Location::ToLocation` s následující utterance:

`book 2 tickets to paris`

Všimněte si, že `2`, číslo, a `paris`, ToLocation mít slova mezi nimi, které nejsou součástí všech entit. Zelená podtržení, které jsou používány s popiskem utterance v [LEOŠ] [ LUIS] webu, označuje složené entity.

![Složené Entity](./media/luis-concept-data-extraction/composite-entity.png)

Složené entity jsou vráceny v `compositeEntities` pole a všechny entity v rámci složeného jsou vráceny také v `entities` pole:

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
|Předkompilované Entity - číslo|"builtin.number"|"2"|
|Hierarchická Entity - umístění|"Location::ToLocation"|"Paříž"|

## <a name="list-entity-data"></a>Seznam dat entity

A [seznamu](luis-concept-entity-types.md) entity není počítač naučili. Je přesný text v případě shody. Seznam představuje položky v seznamu společně s synonyma pro tyto položky. LEOŠ označí všechny shody jako položka v seznamu všechny entity v odpovědi. Synonymum může být ve více než jeden seznam. 

Předpokládejme, že aplikace má seznam s názvem `Cities`, což pro variace města názvy včetně města letiště (Sea-tac), letiště kódu (SEA), poštovní směrovací číslo PSČ (98101) a phone směrové číslo oblasti (206). 

|Položka seznamu|Položka synonyma|
|---|---|
|Seattle|SEA tac, sea, 98101, 206, + 1 |
|Paříž|cdg, roissy, historie, 75001, 1, +33|

`book 2 tickets to paris`

V předchozích utterance slovo `paris` je namapována na položce Paříž jako součást `Cities` seznamu entity. Seznam entity odpovídá normalizovaný název položky jak synonyma položky. 

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

## <a name="prebuilt-entity-data"></a>Data předem entity
[Předkompilované](luis-concept-entity-types.md) entity jsou zjištěny na základě shody regulárního výrazu pomocí open-source [rozpoznávání Text](https://github.com/Microsoft/Recognizers-Text) projektu. Předem entity se vrátí v poli entity a používat název typu s předponou `builtin::`. Tento text je na příkladu utterance s vrácené předem entity:

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

## <a name="regular-expression-entity-data"></a>Regulární výraz dat entity
[Regulární výraz](luis-concept-entity-types.md) entity jsou zjištěny na základě shody regulárního výrazu pomocí výrazu zadáte při vytváření entity. Při použití `kb[0-9]{6}` jako entity definici regulární výraz je odpověď na následující JSON utterance příklad s entity vrácené regulární výraz pro dotaz `When was kb123456 published?`:

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

## <a name="extracting-names"></a>Extrahování názvy
Získávání názvů z utterance je složité, protože název může být téměř jakoukoli kombinací písmen a slova. V závislosti na tom, jaký typ název rozbaluje máte několik možností. Tyto nejsou pravidla, ale další pokyny. 

### <a name="names-of-people"></a>Jména osob
Název uživatelů může mít některé mírné formátu v závislosti na jazyce a jazykové verzi. Použijte buď hierarchické entity s jména a příjmení jako podřízené objekty nebo použijte jednoduché entity s rolemi křestní jméno a příjmení. Nezapomeňte poskytnout příklady, které používají název první a poslední v různých částech utterance, v utterances různých délek a utterances napříč všech tříd Intent včetně žádný záměrné. [Zkontrolujte](label-suggested-utterances.md) utterances koncového bodu v pravidelných intervalech pro označení všech názvů, které nebyly správně předpovědět. 

### <a name="names-of-places"></a>Názvy míst
Umístění názvy jsou nastavit a známé například města, okresech, stavy, provincie a zemích. Pokud vaše aplikace používá sadu přehled umístěních, zvažte seznamu entity. Pokud potřebujete najít že všechny umístit názvů, vytvoření jednoduché entity a poskytují řadu příklady. Přidejte frázi seznam názvů místní posílit jaký místě názvy vypadají ve vaší aplikaci. [Zkontrolujte](label-suggested-utterances.md) utterances koncového bodu v pravidelných intervalech pro označení všech názvů, které nebyly správně předpovědět. 

### <a name="new-and-emerging-names"></a>Nové a vznikajícími názvy
Některé aplikace musí být schopna nalézt nové a vznikajícími názvy, například produkty nebo společností. Toto je nejobtížnější typ dat extrakce. Začínat jednoduché entity a přidejte seznam frázi. [Zkontrolujte](label-suggested-utterances.md) utterances koncového bodu v pravidelných intervalech pro označení všech názvů, které nebyly správně předpovědět. 

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

## <a name="patternany-entity-data"></a>Pattern.Any dat entity
Pattern.Any entity jsou použité v šabloně utterances z entity proměnlivou délkou [vzor](luis-concept-patterns.md). 

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


## <a name="sentiment-analysis"></a>Analýza subjektivního hodnocení
Pokud je nakonfigurovaná postojích analýzy, odpověď json LEOŠ obsahuje postojích analýzy. Další informace o analýze postojích v [Analýza textu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) dokumentaci.

### <a name="sentiment-data"></a>Postojích dat
Postojích dat je skóre mezi 1 a 0 označující pozitivní (blíže 1) ani na zápornou (blíže 0) postojích data.

Když je jazyková verze `en-us`, odpověď je:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

Pro všechny ostatní jazykové verze je odpověď na:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```


### <a name="key-phrase-extraction-entity-data"></a>Klíče frázi extrakce entity data
Entity extrakce klíče frázi vrátí klíče frází v utterance, poskytované [Analýza textu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/).

<!-- TBD: verify JSON-->
```JSON
"keyPhrases": [
    "places",
    "beautiful views",
    "favorite trail"
]
```

## <a name="data-matching-multiple-entities"></a>Data odpovídající více entit
LEOŠ vrátí všechny entity v utterance. V důsledku toho může vaše chatbot třeba, aby na základě výsledků rozhodnutí. Utterance může mít mnoho entit v utterance:

`book me 2 adult business tickets to paris tomorrow on air france`

Koncový bod LEOŠ může zjistit stejná data v různých entit: 

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

## <a name="next-steps"></a>Další postup

V tématu [přidat entity](luis-how-to-add-entities.md) Další informace o tom, jak přidat do aplikace LEOŠ entity.

[LUIS]:luis-reference-regions.md