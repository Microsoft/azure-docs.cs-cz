---
title: Způsob vyhledávání Translator Text API slovníku
titleSuffix: Azure Cognitive Services
description: Použijte metodu vyhledávání slovníku Translator Text API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: swmachan
ms.openlocfilehash: 9a06c8e3b50c3f54971694f8d3924a3a5ba5f071
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595032"
---
# <a name="translator-text-api-30-dictionary-lookup"></a>Translator Text API 3,0: Slovníkové vyhledávání

Poskytuje alternativní překlady pro slovo a malý počet idiomatickou frází. Každý překlad má část řeči a seznam back-vrácených překladů. Back-překlady umožňují uživateli pochopit překlad v kontextu. Operace [ukázkového slovníku](./v3-0-dictionary-examples.md) umožňuje další přechod k podrobnostem, aby se zobrazil příklad použití každého páru překladu.

## <a name="request-url"></a>Adresa URL požadavku

`POST` Odeslat požadavek na:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0
```

## <a name="request-parameters"></a>Parametry žádosti

Parametry žádosti předané řetězci dotazu jsou:

<table width="100%">
  <th width="20%">Parametr dotazu</th>
  <th>Popis</th>
  <tr>
    <td>api-version</td>
    <td>*Povinný parametr*.<br/>Verze rozhraní API, kterou klient požaduje. Hodnota musí být `3.0`.</td>
  </tr>
  <tr>
    <td>from</td>
    <td>*Povinný parametr*.<br/>Určuje jazyk vstupního textu. Zdrojový jazyk musí být jeden z [podporovaných jazyků](./v3-0-languages.md) zahrnutých v `dictionary` oboru.</td>
  </tr>
  <tr>
    <td>na</td>
    <td>*Povinný parametr*.<br/>Určuje jazyk výstupního textu. Cílový jazyk musí být jeden z [podporovaných jazyků](./v3-0-languages.md) zahrnutých v `dictionary` oboru.</td>
  </tr>
</table>

Hlavičky požadavku zahrnují:

<table width="100%">
  <th width="20%">Záhlaví</th>
  <th>Popis</th>
  <tr>
    <td>Ověřovací hlavičky (y)</td>
    <td><em>Požadovaná hlavička žádosti</em><br/>Podívejte se <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">na dostupné možnosti ověřování</a>.</td>
  </tr>
  <tr>
    <td>Typ obsahu</td>
    <td>*Požadovaná hlavička žádosti*<br/>Určuje typ obsahu datové části. Možné hodnoty jsou: `application/json`.</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td>*Požadovaná hlavička žádosti*<br/>Délka textu žádosti</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Volitelné*.<br/>Identifikátor GUID generovaný klientem pro jednoznačnou identifikaci požadavku. Tuto hlavičku můžete vynechat, pokud zahrnete ID trasování do řetězce dotazu pomocí parametru dotazu s názvem `ClientTraceId`.</td>
  </tr>
</table> 

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

  * `normalizedSource`: Řetězec, který poskytuje normalizovanou formu zdrojového termínu. Například pokud je žádost "Jan", normalizovaná forma bude "Jan". Obsah tohoto pole se zobrazí jako vstup do [příkladů vyhledávání](./v3-0-dictionary-examples.md).
    
  * `displaySource`: Řetězec poskytující zdrojový termín ve formuláři nejvhodnější pro zobrazení koncového uživatele. Například pokud je vstup "Jan", formulář pro zobrazení bude odrážet obvyklý pravopis názvu: "Jan". 

  * `translations`: Seznam překladů pro zdrojový termín. Každý prvek seznamu je objekt s následujícími vlastnostmi:

    * `normalizedTarget`: Řetězec, který poskytuje normalizovanou formu tohoto termínu v cílovém jazyce. Tato hodnota by měla být použita jako vstup do [příkladů vyhledávání](./v3-0-dictionary-examples.md).

    * `displayTarget`: Řetězec, který dává termín v cílovém jazyce a ve formuláři, který nejlépe vyhovuje zobrazení pro koncové uživatele. Obecně platí, že `normalizedTarget` se bude lišit pouze z pohledu na velká a malá písmena. Například správné podstatné jméno, například "Juan", bude mít `normalizedTarget = "juan"` a `displayTarget = "Juan"`.

    * `posTag`: Řetězec, který se přidruží k tomuto termínu se značkou, která je součástí mluveného slova.

        | Název značky | Popis  |
        |----------|--------------|
        | ADJ      | Přídavná jména   |
        | ADV      | Příslovné      |
        | CONJ     | Spojky |
        | DET      | Determiners  |
        | PŘEVODU    | Příkazy        |
        | PODSTATNÉ JMÉNO     | Podstatná jména        |
        | PROSTŘEDÍ     | Předložkami |
        | CHYB     | Podstatná jména     |
        | OPERACE     | Příkazy        |
        | JINÁ    | Ostatní        |

        Jako poznámku k implementaci byly tyto značky určeny funkcí označení částí v jazyce Speech a následně jsou pro každou dvojici zdrojového/cíle předány nejčastěji se značkami. Takže pokud lidé často převádějí španělské slovo na jinou značku v jazyce Speech v angličtině, může dojít k chybě značek (s ohledem na španělské slovo).

    * `confidence`: Hodnota mezi 0,0 a 1,0, která představuje "jistotu" (nebo možná přesněji, "pravděpodobnost v školicích datech") daného páru překladu. Součet hodnocení spolehlivosti pro jedno zdrojové slovo může nebo nemusí být v součtu 1,0. 

    * `prefixWord`: Řetězec, který aplikaci zobrazí jako předponu překladu. V současné době se jedná o nejpodstatnější rozhodnutí v jazycích, které mají determiners v rovnosti. Například předpona španělského slova "Mosca" je "La", protože "Mosca" je Feminine podstatnou příponou v španělštině. Tato je závislá pouze na překladu, nikoli na zdroji. Pokud není žádná předpona, bude to prázdný řetězec.
    
    * `backTranslations`: Seznam back-překlady cíle. Například zdrojová slova, na která může cíl překládat. V seznamu je zaručeno, že obsahuje zdrojové slovo, které bylo vyžádáno (například pokud je zdrojové slovo "přílet", je zaručeno, že bude v `backTranslations` seznamu "přílet". Není však zaručena, že by měla být na první pozici a často nebude. Každý prvek `backTranslations` seznamu je objekt popsaný následujícími vlastnostmi:

        * `normalizedText`: Řetězec, který poskytuje normalizovanou formu zdrojového termínu, který je zpětný překlad cíle. Tato hodnota by měla být použita jako vstup do [příkladů vyhledávání](./v3-0-dictionary-examples.md).        

        * `displayText`: Řetězec, který představuje zdrojový termín, který je zpětný překlad cíle ve formuláři, který nejlépe vyhovuje zobrazení pro koncové uživatele.

        * `numExamples`: Celé číslo představující počet příkladů, které jsou k dispozici pro tento dvojici překladu. Skutečné příklady musí být načteny pomocí samostatného volání [příkladů vyhledávání](./v3-0-dictionary-examples.md). Toto číslo je většinou určeno k usnadnění zobrazení v uživatelském rozhraní. Uživatelské rozhraní může například přidat hypertextový odkaz na zpětný překlad, pokud je počet příkladů větší než nula, a pokud chcete zobrazit zpětný překlad jako prostý text, pokud nejsou k dispozici žádné příklady. Počítejte s tím, že skutečný počet příkladů vrácených voláním [příkladů vyhledávání](./v3-0-dictionary-examples.md) může být menší než `numExamples`, protože další filtrování může být provedeno za běhu pro odebrání "špatných" příkladů.
        
        * `frequencyCount`: Celé číslo představující frekvenci tohoto páru překladu v datech. Hlavním účelem tohoto pole je poskytnout uživatelské rozhraní se prostředkem pro řazení zpětných překladů, takže nejčastěji se jedná o nejčastější výrazy.

    > [!NOTE]
    > Pokud hledaný termín neexistuje ve slovníku, odpověď je 200 (ok), ale `translations` seznam je prázdný seznam.

## <a name="examples"></a>Příklady

Tento příklad ukazuje, jak vyhledat alternativní překlady ve španělštině anglického období `fly` .

# <a name="curltabcurl"></a>[Curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly'}]"
```

---

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

# <a name="curltabcurl"></a>[Curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly123456'}]"
```

---

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
