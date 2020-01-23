---
title: Způsob vyhledávání Translator Text API slovníku
titleSuffix: Azure Cognitive Services
description: Metoda vyhledávání slovníku poskytuje alternativní překlady pro slovo a malý počet idiomatickou frází.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: swmachan
ms.openlocfilehash: bd27827441082698bb4e0b43e7dd22d5b7e66539
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548947"
---
# <a name="translator-text-api-30-dictionary-lookup"></a>Translator Text API 3,0: vyhledávání ve slovníku

Poskytuje alternativní překlady pro slovo a malý počet idiomatickou frází. Každý překlad má část řeči a seznam back-vrácených překladů. Back-překlady umožňují uživateli pochopit překlad v kontextu. Operace [ukázkového slovníku](./v3-0-dictionary-examples.md) umožňuje další přechod k podrobnostem, aby se zobrazil příklad použití každého páru překladu.

## <a name="request-url"></a>Adresa URL požadavku

Odeslat žádost o `POST`:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0
```

## <a name="request-parameters"></a>Parametry žádosti

Parametry žádosti předané řetězci dotazu jsou:

| Parametr dotazu  | Popis |
| ------ | ----------- |
| api-version <img width=200/>   | **Povinný parametr**.<br/>Verze rozhraní API, kterou klient požaduje. Hodnota musí být `3.0` |
| od | **Povinný parametr**.<br/>Určuje jazyk vstupního textu. Zdrojový jazyk musí být jeden z [podporovaných jazyků](./v3-0-languages.md) , které jsou zahrnuté v oboru `dictionary`. |
| na   | **Povinný parametr**.<br/>Určuje jazyk výstupního textu. Cílový jazyk musí být jeden z [podporovaných jazyků](v3-0-languages.md) , které jsou zahrnuté v oboru `dictionary`. |


Hlavičky požadavku zahrnují:

| Hlavičky  | Popis |
| ------ | ----------- |
| Ověřovací hlavičky (y) <img width=200/>  | **Požadovaná hlavička žádosti**<br/>Podívejte se <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">na dostupné možnosti ověřování</a>. |
| Content-Type | **Požadovaná hlavička žádosti**<br/>Určuje typ obsahu datové části. Možné hodnoty jsou: `application/json`. |
| Délka obsahu   | **Požadovaná hlavička žádosti**<br/>Délka textu žádosti |
| X-ClientTraceId   | **Volitelné**.<br/>Identifikátor GUID generovaný klientem pro jednoznačnou identifikaci požadavku. Tuto hlavičku můžete vynechat, pokud zahrnete ID trasování do řetězce dotazu pomocí parametru dotazu s názvem `ClientTraceId`. |

## <a name="request-body"></a>Text požadavku

Tělo požadavku je pole JSON. Každý prvek pole je objekt JSON s vlastností řetězce s názvem `Text`, který představuje termín pro vyhledání.

```json
[
    {"Text":"fly"}
]
```

Platí následující omezení:

* Pole může mít maximálně 10 prvků.
* Textová hodnota prvku pole nesmí být delší než 100 znaků včetně mezer.

## <a name="response-body"></a>Text odpovědi

Úspěšná odpověď je pole JSON s jedním výsledkem pro každý řetězec ve vstupním poli. Objekt výsledku obsahuje následující vlastnosti:

  * `normalizedSource`: řetězec, který poskytuje normalizovanou formu zdrojového výrazu. Například pokud je žádost "Jan", normalizovaná forma bude "Jan". Obsah tohoto pole se zobrazí jako vstup do [příkladů vyhledávání](./v3-0-dictionary-examples.md).
    
  * `displaySource`: řetězec poskytující zdrojový termín ve formuláři nejvhodnější pro zobrazení koncového uživatele. Například pokud je vstup "Jan", formulář pro zobrazení bude odrážet obvyklý pravopis názvu: "Jan". 

  * `translations`: seznam překladů pro zdrojový termín. Každý prvek seznamu je objekt s následujícími vlastnostmi:

    * `normalizedTarget`: řetězec, který poskytuje normalizovanou formu tohoto termínu v cílovém jazyce. Tato hodnota by měla být použita jako vstup do [příkladů vyhledávání](./v3-0-dictionary-examples.md).

    * `displayTarget`: řetězec, který dává termín v cílovém jazyce a ve formuláři, který nejlépe vyhovuje zobrazení pro koncové uživatele. Obecně se to liší od `normalizedTarget` z pohledu velkých a malých písmen. Například správné podstatné jméno, například "Juan", bude mít `normalizedTarget = "juan"` a `displayTarget = "Juan"`.

    * `posTag`: řetězec, který je asociativní s tímto termínem značkou, která je součástí řeči.

        | Název značky | Popis  |
        |----------|--------------|
        | ADJ      | Přídavná jména   |
        | ADV      | Příslovné      |
        | CONJ     | Spojky |
        | DET      | Determiners  |
        | PŘEVODU    | Příkazy        |
        | PODSTATNÉ jméno     | Podstatná jména        |
        | PROSTŘEDÍ     | Předložkami |
        | CHYB     | Podstatná jména     |
        | OPERACE     | Příkazy        |
        | JINÉ    | Jiné        |

        Jako poznámku k implementaci byly tyto značky určeny funkcí označení částí v jazyce Speech a následně jsou pro každou dvojici zdrojového/cíle předány nejčastěji se značkami. Takže pokud lidé často převádějí španělské slovo na jinou značku v jazyce Speech v angličtině, může dojít k chybě značek (s ohledem na španělské slovo).

    * `confidence`: hodnota mezi 0,0 a 1,0, která představuje "jistotu" (nebo možná přesněji, "pravděpodobnost v školicích datech") daného páru překladu. Součet hodnocení spolehlivosti pro jedno zdrojové slovo může nebo nemusí být v součtu 1,0. 

    * `prefixWord`: řetězec, který aplikaci Word zobrazí jako předponu překladu. V současné době se jedná o nejpodstatnější rozhodnutí v jazycích, které mají determiners v rovnosti. Například předpona španělského slova "Mosca" je "La", protože "Mosca" je Feminine podstatnou příponou v španělštině. Tato je závislá pouze na překladu, nikoli na zdroji. Pokud není žádná předpona, bude to prázdný řetězec.
    
    * `backTranslations`: seznam back-překlady cíle. Například zdrojová slova, na která může cíl překládat. V seznamu je zaručeno, že obsahuje zdrojové slovo, které bylo vyžádáno (například pokud je zdrojové slovo "přílet", je zaručeno, že "přílet" bude v seznamu `backTranslations`). Není však zaručena, že by měla být na první pozici a často nebude. Každý prvek seznamu `backTranslations` je objekt popsaný následujícími vlastnostmi:

        * `normalizedText`: řetězec, který poskytuje normalizovanou formu zdrojového termínu, který je zpětný překlad cíle. Tato hodnota by měla být použita jako vstup do [příkladů vyhledávání](./v3-0-dictionary-examples.md).        

        * `displayText`: řetězec, který dává zdrojový termín, který je back-posunutím cíle ve formuláři, který nejlépe vyhovuje zobrazení koncového uživatele.

        * `numExamples`: celé číslo představující počet příkladů, které jsou k dispozici pro tento dvojici překladu. Skutečné příklady musí být načteny pomocí samostatného volání [příkladů vyhledávání](./v3-0-dictionary-examples.md). Toto číslo je většinou určeno k usnadnění zobrazení v uživatelském rozhraní. Uživatelské rozhraní může například přidat hypertextový odkaz na zpětný překlad, pokud je počet příkladů větší než nula, a pokud chcete zobrazit zpětný překlad jako prostý text, pokud nejsou k dispozici žádné příklady. Počítejte s tím, že skutečný počet příkladů vrácených voláním [příkladů vyhledávání](./v3-0-dictionary-examples.md) může být menší než `numExamples`, protože další filtrování lze použít za účelem odebrání "špatných" příkladů.
        
        * `frequencyCount`: celé číslo představující frekvenci tohoto páru překladu v datech. Hlavním účelem tohoto pole je poskytnout uživatelské rozhraní se prostředkem pro řazení zpětných překladů, takže nejčastěji se jedná o nejčastější výrazy.

    > [!NOTE]
    > Pokud hledaný termín neexistuje ve slovníku, odpověď je 200 (OK), ale seznam `translations` je prázdný seznam.

## <a name="examples"></a>Příklady

Tento příklad ukazuje, jak vyhledat alternativní překlady ve španělštině `fly` anglického termínu.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly'}]"
```

Tělo odpovědi (zkrácené pro přehlednost) je:

```
[
    {
        "normalizedSource":"fly",
        "displaySource":"fly",
        "translations":[
            {
                "normalizedTarget":"volar",
                "displayTarget":"volar",
                "posTag":"VERB",
                "confidence":0.4081,
                "prefixWord":"",
                "backTranslations":[
                    {"normalizedText":"fly","displayText":"fly","numExamples":15,"frequencyCount":4637},
                    {"normalizedText":"flying","displayText":"flying","numExamples":15,"frequencyCount":1365},
                    {"normalizedText":"blow","displayText":"blow","numExamples":15,"frequencyCount":503},
                    {"normalizedText":"flight","displayText":"flight","numExamples":15,"frequencyCount":135}
                ]
            },
            {
                "normalizedTarget":"mosca",
                "displayTarget":"mosca",
                "posTag":"NOUN",
                "confidence":0.2668,
                "prefixWord":"",
                "backTranslations":[
                    {"normalizedText":"fly","displayText":"fly","numExamples":15,"frequencyCount":1697},
                    {"normalizedText":"flyweight","displayText":"flyweight","numExamples":0,"frequencyCount":48},
                    {"normalizedText":"flies","displayText":"flies","numExamples":9,"frequencyCount":34}
                ]
            },
            //
            // ...list abbreviated for documentation clarity
            //
        ]
    }
]
```

Tento příklad ukazuje, co se stane, když hledaný termín neexistuje pro platnou dvojici slovníku.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly123456'}]"
```

Vzhledem k tomu, že výraz nebyl nalezen ve slovníku, tělo odpovědi obsahuje prázdný seznam `translations`.

```
[
    {
        "normalizedSource":"fly123456",
        "displaySource":"fly123456",
        "translations":[]
    }
]
```
