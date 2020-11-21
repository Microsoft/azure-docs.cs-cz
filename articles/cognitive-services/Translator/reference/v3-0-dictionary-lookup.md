---
title: Metoda Lookup slovníku translatoru
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
ms.openlocfilehash: 6ada9c06f0befbea4b9aa85f95651e96dec595b7
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021282"
---
# <a name="translator-30-dictionary-lookup"></a>Překladatel 3,0: vyhledávání slovníku

Poskytuje alternativní překlady pro slovo a malý počet idiomatickou frází. Každý překlad má část řeči a seznam back-vrácených překladů. Back-překlady umožňují uživateli pochopit překlad v kontextu. Operace [ukázkového slovníku](./v3-0-dictionary-examples.md) umožňuje další přechod k podrobnostem, aby se zobrazil příklad použití každého páru překladu.

## <a name="request-url"></a>Adresa URL požadavku

Odeslat `POST` požadavek na:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0
```

## <a name="request-parameters"></a>Parametry žádosti

Parametry žádosti předané řetězci dotazu jsou:

| Parametr dotazu  | Popis |
| ------ | ----------- |
| verze-api <img width=200/>   | **Povinný parametr**.<br/>Verze rozhraní API, kterou klient požaduje. Hodnota musí být `3.0` |
| Výsledkem | **Povinný parametr**.<br/>Určuje jazyk vstupního textu. Zdrojový jazyk musí být jeden z [podporovaných jazyků](./v3-0-languages.md) zahrnutých v `dictionary` oboru. |
| na   | **Povinný parametr**.<br/>Určuje jazyk výstupního textu. Cílový jazyk musí být jeden z [podporovaných jazyků](v3-0-languages.md) zahrnutých v `dictionary` oboru. |


Hlavičky požadavku zahrnují:

| Hlavičky  | Popis |
| ------ | ----------- |
| Ověřovací hlavičky (y) <img width=200/>  | **Požadovaná hlavička žádosti**<br/>Podívejte se <a href="/azure/cognitive-services/translator/reference/v3-0-reference#authentication">na dostupné možnosti ověřování</a>. |
| Typ obsahu | **Požadovaná hlavička žádosti**<br/>Určuje typ obsahu datové části. Možné hodnoty jsou: `application/json` . |
| Délka obsahu   | **Požadovaná hlavička žádosti**<br/>Délka textu žádosti |
| X – ClientTraceId   | **Volitelné**.<br/>Identifikátor GUID generovaný klientem pro jednoznačnou identifikaci požadavku. Tuto hlavičku můžete vynechat, pokud zahrnete ID trasování do řetězce dotazu pomocí parametru dotazu s názvem `ClientTraceId` . |

## <a name="request-body"></a>Text požadavku

Tělo požadavku je pole JSON. Každý prvek pole je objekt JSON s vlastností řetězce s názvem `Text` , který představuje termín pro vyhledání.

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

  * `normalizedSource`: Řetězec, který poskytuje normalizovanou formu zdrojového výrazu. Například pokud je žádost "Jan", normalizovaná forma bude "Jan". Obsah tohoto pole se zobrazí jako vstup do [příkladů vyhledávání](./v3-0-dictionary-examples.md).
    
  * `displaySource`: Řetězec poskytující zdrojový termín ve formuláři nejvhodnější pro zobrazení koncového uživatele. Například pokud je vstup "Jan", formulář pro zobrazení bude odrážet obvyklý pravopis názvu: "Jan". 

  * `translations`: Seznam překladů pro zdrojový termín. Každý prvek seznamu je objekt s následujícími vlastnostmi:

    * `normalizedTarget`: Řetězec, který poskytuje normalizovanou formu tohoto termínu v cílovém jazyce. Tato hodnota by měla být použita jako vstup do [příkladů vyhledávání](./v3-0-dictionary-examples.md).

    * `displayTarget`: Řetězec, který poskytuje termín v cílovém jazyce a ve formuláři nejvhodnější pro zobrazení koncového uživatele. Obecně platí, že se bude lišit pouze z `normalizedTarget` pohledu na velká a malá písmena. Například správné podstatné jméno, například "Juan", bude mít `normalizedTarget = "juan"` a `displayTarget = "Juan"` .

    * `posTag`: Řetězec, který je přidružen k tomuto termínu se značkou, která je součástí mluveného slova.

        | Název značky | Popis  |
        |----------|--------------|
        | ROKU      | Přídavná jména   |
        | ZÁLOH      | Příslovné      |
        | CONJ     | Spojky |
        | DET      | Determiners  |
        | PŘEVODU    | Příkazy        |
        | PODSTATNÉ jméno     | Podstatná jména        |
        | PROSTŘEDÍ     | Předložkami |
        | CHYB     | Zájmena     |
        | OPERACE     | Příkazy        |
        | JINÁ    | Ostatní        |

        Jako poznámku k implementaci byly tyto značky určeny funkcí označení částí v jazyce Speech a následně jsou pro každou dvojici zdrojového/cíle předány nejčastěji se značkami. Takže pokud lidé často převádějí španělské slovo na jinou značku v jazyce Speech v angličtině, může dojít k chybě značek (s ohledem na španělské slovo).

    * `confidence`: Hodnota mezi 0,0 a 1,0, která představuje "jistotu" (nebo možná přesněji, "pravděpodobnost v školicích datech") daného páru překladu. Součet hodnocení spolehlivosti pro jedno zdrojové slovo může nebo nemusí být v součtu 1,0. 

    * `prefixWord`: Řetězec, který aplikaci zobrazí jako předponu překladu. V současné době se jedná o nejpodstatnější rozhodnutí v jazycích, které mají determiners v rovnosti. Například předpona španělského slova "Mosca" je "La", protože "Mosca" je Feminine podstatnou příponou v španělštině. Tato je závislá pouze na překladu, nikoli na zdroji. Pokud není žádná předpona, bude to prázdný řetězec.
    
    * `backTranslations`: Seznam back-překlady cíle. Například zdrojová slova, na která může cíl překládat. V seznamu je zaručeno, že obsahuje zdrojové slovo, které bylo vyžádáno (například pokud je zdrojové slovo "přílet", je zaručeno, že bude v seznamu "přílet" `backTranslations` . Není však zaručena, že by měla být na první pozici a často nebude. Každý prvek `backTranslations` seznamu je objekt popsaný následujícími vlastnostmi:

        * `normalizedText`: Řetězec, který poskytuje normalizovanou formu zdrojového termínu, který je zpětný překlad cíle. Tato hodnota by měla být použita jako vstup do [příkladů vyhledávání](./v3-0-dictionary-examples.md).        

        * `displayText`: Řetězec, který představuje zdrojový termín, který je zpětný překlad cíle ve formuláři, který nejlépe vyhovuje zobrazení pro koncové uživatele.

        * `numExamples`: Celé číslo představující počet příkladů, které jsou k dispozici pro tento dvojici překladu. Skutečné příklady musí být načteny pomocí samostatného volání [příkladů vyhledávání](./v3-0-dictionary-examples.md). Toto číslo je většinou určeno k usnadnění zobrazení v uživatelském rozhraní. Uživatelské rozhraní může například přidat hypertextový odkaz na zpětný překlad, pokud je počet příkladů větší než nula, a pokud chcete zobrazit zpětný překlad jako prostý text, pokud nejsou k dispozici žádné příklady. Počítejte s tím, že skutečný počet příkladů vrácených voláním [příkladů vyhledávání](./v3-0-dictionary-examples.md) může být menší než `numExamples` , protože další filtrování může být provedeno za běhu pro odebrání "špatných" příkladů.
        
        * `frequencyCount`: Celé číslo představující frekvenci tohoto páru překladu v datech. Hlavním účelem tohoto pole je poskytnout uživatelské rozhraní se prostředkem pro řazení zpětných překladů, takže nejčastěji se jedná o nejčastější výrazy.

    > [!NOTE]
    > Pokud hledaný termín neexistuje ve slovníku, odpověď je 200 (OK), ale `translations` seznam je prázdný seznam.

## <a name="examples"></a>Příklady

Tento příklad ukazuje, jak vyhledat alternativní překlady ve španělštině anglického období `fly` .

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

Vzhledem k tomu, že výraz nebyl nalezen ve slovníku, tělo odpovědi obsahuje prázdný `translations` seznam.

```
[
    {
        "normalizedSource":"fly123456",
        "displaySource":"fly123456",
        "translations":[]
    }
]
```