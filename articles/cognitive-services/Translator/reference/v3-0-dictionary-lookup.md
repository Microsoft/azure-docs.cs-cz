---
title: Metoda vyhledávání překladačového textového rozhraní API
titleSuffix: Azure Cognitive Services
description: Metoda Vyhledávání slovníku poskytuje alternativní překlady pro slovo a malý počet idiomatických frází.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: swmachan
ms.openlocfilehash: bd27827441082698bb4e0b43e7dd22d5b7e66539
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "76548947"
---
# <a name="translator-text-api-30-dictionary-lookup"></a>Překladač Text API 3.0: Slovník vyhledávání

Poskytuje alternativní překlady pro slovo a malý počet idiomatických frází. Každý překlad má část řeči a seznam zpětných překladů. Zpětné překlady umožňují uživateli pochopit překlad v kontextu. Operace [Příklad slovníku](./v3-0-dictionary-examples.md) umožňuje další přechod k podrobnostem, aby se zovíraly příklady použití jednotlivých dvojic překladů.

## <a name="request-url"></a>Adresa URL požadavku

Odeslání `POST` žádosti na:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0
```

## <a name="request-parameters"></a>Parametry požadavku

Parametry požadavku předané v řetězci dotazu jsou:

| Parametr dotazu  | Popis |
| ------ | ----------- |
| verze-api <img width=200/>   | **Povinný parametr**.<br/>Verze rozhraní API požadované klientem. Hodnota musí být`3.0` |
| Z | **Povinný parametr**.<br/>Určuje jazyk vstupního textu. Zdrojový jazyk musí být jedním z [podporovaných jazyků](./v3-0-languages.md) zahrnutých v `dictionary` oboru. |
| na   | **Povinný parametr**.<br/>Určuje jazyk výstupního textu. Cílový jazyk musí být jedním z [podporovaných jazyků](v3-0-languages.md) zahrnutých v `dictionary` oboru. |


Mezi hlavičky požadavku patří:

| Hlavičky  | Popis |
| ------ | ----------- |
| Autentizační hlavička (záhlaví) <img width=200/>  | **Hlavička požadavku .**<br/>Viz <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">dostupné možnosti ověřování</a>. |
| Typ obsahu | **Hlavička požadavku .**<br/>Určuje typ obsahu datové části. Možné hodnoty `application/json`jsou: . |
| Délka obsahu   | **Hlavička požadavku .**<br/>Délka těla požadavku. |
| X-ClientTraceId   | **Nepovinné**.<br/>Identifikátor GUID generovaný klientem k jednoznačné identifikaci požadavku. Toto záhlaví můžete vynechat, pokud do řetězce dotazu zahrnete `ClientTraceId`ID trasování pomocí parametru dotazu s názvem . |

## <a name="request-body"></a>Text požadavku

Tělo požadavku je pole JSON. Každý prvek pole je objekt JSON `Text`s vlastností string s názvem , která představuje termín vyhledávání.

```json
[
    {"Text":"fly"}
]
```

Platí následující omezení:

* Pole může mít maximálně 10 prvků.
* Textová hodnota prvku pole nesmí přesáhnout 100 znaků včetně mezer.

## <a name="response-body"></a>Text odpovědi

Úspěšná odpověď je pole JSON s jedním výsledkem pro každý řetězec ve vstupním poli. Výsledný objekt obsahuje následující vlastnosti:

  * `normalizedSource`: Řetězec poskytující normalizovanou formu zdrojového termínu. Například pokud je požadavek "JOHN", normalizovaný formulář bude "john". Obsah tohoto pole se stane vstupem [do příkladů vyhledávání](./v3-0-dictionary-examples.md).
    
  * `displaySource`: Řetězec udávající zdrojový termín ve formě, která je nejvhodnější pro zobrazení koncovým uživatelem. Pokud je například vstup "JOHN", bude zobrazovaný formulář odrážet obvyklý pravopis názvu: "Jan". 

  * `translations`: Seznam překladů pro zdrojový termín. Každý prvek seznamu je objekt s následujícími vlastnostmi:

    * `normalizedTarget`: Řetězec poskytující normalizovanou formu tohoto termínu v cílovém jazyce. Tato hodnota by měla být použita jako vstup pro [vyhledávání příklady](./v3-0-dictionary-examples.md).

    * `displayTarget`: Řetězec udávající termín v cílovém jazyce a ve formě, která je nejvhodnější pro zobrazení koncovým uživatelem. Obecně se to bude `normalizedTarget` lišit pouze z hlediska velkých písmen. Například vlastní sypké místo `normalizedTarget = "juan"` jako `displayTarget = "Juan"`"Juan" bude mít a .

    * `posTag`: Řetězec sousto tohoto termínu se značkou části řeči.

        | Název značky | Popis  |
        |----------|--------------|
        | Adj      | Přídavná jména   |
        | Adv      | Příslovce      |
        | CONJ     | Spojky |
        | Det      | Determinanty  |
        | Modální    | Příkazy        |
        | Podstatné jméno     | Podstatná jména        |
        | Přípravka     | Předložky |
        | Pron     | Zájmena     |
        | Sloveso     | Příkazy        |
        | Další    | Ostatní        |

        Jako poznámka implementace byly tyto značky určeny označením části řeči na anglické straně a následným převzetím nejčastější značky pro každý zdrojový nebo cílový pár. Takže pokud lidé často překládají španělské slovo na jinou značku části řeči v angličtině, značky mohou skončit jako špatné (s ohledem na španělské slovo).

    * `confidence`: Hodnota mezi 0,0 a 1,0, která představuje "důvěru" (nebo možná přesněji, "pravděpodobnost v trénovacích datech") tohoto páru překladů. Součet skóre spolehlivosti pro jedno zdrojové slovo může nebo nemusí součet 1,0. 

    * `prefixWord`: Řetězec, který dává slovo k zobrazení jako předponu překladu. V současné době se jedná o genderově determinant nosných ňadrů, v jazycích, které mají genderové determinanty. Například předpona španělského slova "mosca" je "la", protože "mosca" je ženské jmetě ve španělštině. To závisí pouze na překladu a nikoli na zdroji. Pokud neexistuje žádná předpona, bude prázdný řetězec.
    
    * `backTranslations`: Seznam "zpětných překladů" cíle. Například zdrojová slova, do kterých může cíl přeložit. Seznam je zaručeno, že obsahuje zdrojové slovo, které bylo požadováno (např. pokud zdrojové slovo, které se vyhledává, je "fly", pak je zaručeno, že "fly" bude v `backTranslations` seznamu). Nicméně, to není zaručeno, že bude na první pozici, a často nebude. Každý prvek `backTranslations` seznamu je objekt popsaný následujícími vlastnostmi:

        * `normalizedText`: Řetězec poskytující normalizovanou formu zdrojového termínu, který je zpětný překlad cíle. Tato hodnota by měla být použita jako vstup pro [vyhledávání příklady](./v3-0-dictionary-examples.md).        

        * `displayText`: Řetězec poskytující zdrojový termín, který je zpětný překlad cíle ve formě nejvhodnější pro zobrazení koncového uživatele.

        * `numExamples`: Celé číslo představující počet příkladů, které jsou k dispozici pro tento pár překladů. Skutečné příklady musí být načteny se samostatným voláním k [příkladu vyhledávání](./v3-0-dictionary-examples.md). Číslo je většinou určeno k usnadnění zobrazení v uživatelském uživatelském uživatelském, neochutný. Uživatelské rozhraní může například přidat hypertextový odkaz do zpětného překladu, pokud je počet příkladů větší než nula a zobrazí zpětný překlad jako prostý text, pokud neexistují žádné příklady. Všimněte si, že skutečný počet příkladů vrácených volání vyhledávání `numExamples` [příklady](./v3-0-dictionary-examples.md) mohou být menší než , protože další filtrování může být použita za běhu odebrat "chybné" příklady.
        
        * `frequencyCount`: Celé číslo představující frekvenci tohoto páru překladu v datech. Hlavním účelem tohoto pole je poskytnout uživatelské mutovi prostředky k řazení zpětných překladů, takže nejčastější termíny jsou první.

    > [!NOTE]
    > Pokud termín vyhledávaný ve slovníku neexistuje, odpověď je 200 (OK), ale `translations` seznam je prázdný seznam.

## <a name="examples"></a>Příklady

Tento příklad ukazuje, jak vyhledat alternativní překlady `fly` anglického termínu ve španělštině .

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly'}]"
```

Reakční těleso (zkráceně pro přehlednost) je:

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

Tento příklad ukazuje, co se stane, když termín vyhledávaný neexistuje pro platný slovník dvojice.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly123456'}]"
```

Vzhledem k tomu, že termín není nalezen ve `translations` slovníku, obsahuje tělo odpovědi prázdný seznam.

```
[
    {
        "normalizedSource":"fly123456",
        "displaySource":"fly123456",
        "translations":[]
    }
]
```
