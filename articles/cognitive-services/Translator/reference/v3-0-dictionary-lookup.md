---
title: Translator Text API slovníku vyhledávání – metoda
titlesuffix: Azure Cognitive Services
description: Použijte metodu Translator Text API slovníku vyhledávání.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: b51067b9e854566991d49aeb1ff2b1ad13999a51
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52957738"
---
# <a name="translator-text-api-30-dictionary-lookup"></a>Translator Text API 3.0: Vyhledávání slovníku

Poskytuje alternativní překlady pro slovo a malý počet idiomatickou frází. Každý překlad obsahuje část řeči a seznam back překlady. Back překlady povolit tak uživateli pochopit překlad v kontextu. [Slovníku příklad](./v3-0-dictionary-examples.md) operace povoluje další hierarchií naleznete v příkladu se používá každá dvojice překladu.

## <a name="request-url"></a>Adresa URL požadavku

Odeslat `POST` požadavek na:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0
```

## <a name="request-parameters"></a>Parametry žádosti

Žádosti jsou parametry předané v řetězci dotazu:

<table width="100%">
  <th width="20%">Parametr dotazu</th>
  <th>Popis</th>
  <tr>
    <td>verze API-version</td>
    <td>*Povinný parametr*.<br/>Verze rozhraní API požadovaná klientem. Hodnota musí být `3.0`.</td>
  </tr>
  <tr>
    <td>od</td>
    <td>*Povinný parametr*.<br/>Určuje jazyk, který vstupního textu. Zdrojový jazyk musí být jedna z [podporované jazyky](./v3-0-languages.md) součástí `dictionary` oboru.</td>
  </tr>
  <tr>
    <td>na</td>
    <td>*Povinný parametr*.<br/>Určuje jazyk text výstupu. Cílový jazyk musí být jedna z [podporované jazyky](./v3-0-languages.md) součástí `dictionary` oboru.</td>
  </tr>
</table>

Hlavičky žádosti patří:

<table width="100%">
  <th width="20%">Hlavičky</th>
  <th>Popis</th>
  <tr>
    <td>_Jedním autorizačním_<br/>_Záhlaví_</td>
    <td>*Hlavička požadavku požadované*.<br/>Zobrazit [dostupné možnosti pro ověřování](./v3-0-reference.md#authentication).</td>
  </tr>
  <tr>
    <td>Typ obsahu</td>
    <td>*Hlavička požadavku požadované*.<br/>Určuje typ obsahu datové části. Možné hodnoty jsou: `application/json`.</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td>*Hlavička požadavku požadované*.<br/>Délka textu požadavku.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Volitelné*.<br/>Klientem generovaná identifikátor GUID k jednoznačné identifikaci požadavku. Tato hlavička můžete vynechat, pokud zahrnete ID trasování v řetězci dotazu pomocí parametru dotazu s názvem `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Text požadavku

Text požadavku je pole JSON. Každý prvek pole je objekt JSON s řetězcovou vlastnost s názvem `Text`, která představuje výraz k vyhledání.

```json
[
    {"Text":"fly"}
]
```

Platí následující omezení:

* Pole může mít maximálně 10 prvků.
* Textová hodnota elementu pole může mít maximálně 100 znaků včetně mezer.

## <a name="response-body"></a>Text odpovědi

Úspěšná odpověď je pole JSON se jeden výsledek pro každý řetězec vstupního pole. Výsledný objekt zahrnuje následující vlastnosti:

  * `normalizedSource`: Řetězec poskytující normalizovaná forma jako zdroj. Například pokud je požadavek "JOHN", normalizovaná forma bude "john". Obsah tohoto pole se stane vstupem do [vyhledávání příkladů](./v3-0-dictionary-examples.md).
    
  * `displaySource`: Řetězec poskytující zdrojový výraz ve formě nejlépe vhodné pro zobrazení koncových uživatelů. Například pokud je vstup "JOHN", zobrazit formulář bude odrážet obvykle správnost názvu: "John". 

  * `translations`: Seznam překlady období zdroje. Každý prvek seznamu je objekt s následujícími vlastnostmi:

    * `normalizedTarget`: Řetězec poskytující normalizovaná forma tento termín v cílovém jazyce. Tato hodnota má být použita jako vstup pro [vyhledávání příkladů](./v3-0-dictionary-examples.md).

    * `displayTarget`: Řetězec poskytující termín v cílovém jazyce a ve formě nejlépe vhodné pro zobrazení koncových uživatelů. Obecně platí, to bude pouze se liší od `normalizedTarget` z hlediska malá a velká písmena. Například vlastní jméno jako "Juan" bude mít `normalizedTarget = "juan"` a `displayTarget = "Juan"`.

    * `posTag`: Řetězec tento termín přidružení značku částí řeči.

        | Název značky | Popis  |
        |----------|--------------|
        | PŘEDCHOZÍHO ROKU      | Přídavných jmen   |
        | ADV      | Příslovcí      |
        | CONJ     | Spojky |
        | DET      | Determiners  |
        | MODÁLNÍ OKNO    | Příkazy        |
        | PODSTATNÉ JMÉNO     | Podstatná jména        |
        | PŘÍPRAVA APLIKACE     | Předložky |
        | PRON     | Zájmena     |
        | PŘÍKAZ     | Příkazy        |
        | OSTATNÍ    | Ostatní        |

        Jako poznámka implementaci tyto značky byly určeny část umožňuje označování straně anglické a následné provádění nejčastěji se vyskytujících značky pro každý pár zdroje/cíle. Proto pokud se lidé často přeložit španělské slovo do různých částí řeči značky v angličtině, značky uvíznout je nesprávné (s ohledem na španělské slovo).

    * `confidence`: Hodnota mezi 0,0 a 1,0, který představuje "důvěry" (nebo možná více přesně, "pravděpodobnost v trénovací data") tohoto páru překladu. Součet skóre spolehlivosti pro jedno slovo zdroje může nebo nemusí součet 1.0. 

    * `prefixWord`: Řetězec poskytující slovo zobrazí jako předponu překladu. V současné době je to gendered determiner podstatná jména v jazycích, které mají gendered determiners. Předpona španělské slovo "mosca" je například "la", protože "mosca" je Ženský podstatné jméno ve španělštině. Toto je pouze závislé na překlad a ne na zdroji. Pokud zde není žádná předpona, bude mít prázdný řetězec.
    
    * `backTranslations`: Seznam "zpět"překlady cíle. Například zdroj slova, která cíle jsou dobře převeditelné na. V seznamu je zaručeno, že obsahují slovo zdroj, který byl vyžádán (například pokud zdroj aplikace word se hledá "plout", pak je zaručeno, že "plout" budou `backTranslations` seznamu). Však to není zaručeno, že na první pozici a často nebude. Každý prvek `backTranslations` seznamu je objekt popsal následující vlastnosti:

        * `normalizedText`: Řetězec poskytující normalizovaná forma zdroj termín, který je back překlad cíle. Tato hodnota má být použita jako vstup pro [vyhledávání příkladů](./v3-0-dictionary-examples.md).        

        * `displayText`: Řetězec poskytující zdroje termín, který je back překlad cíle ve formě nejlepší vhodné pro zobrazení koncových uživatelů.

        * `numExamples`: Celé číslo představující počet příklady, které jsou k dispozici pro tento překlad pár. Skutečné příklady musí být načten pomocí samostatné volání [vyhledávání příkladů](./v3-0-dictionary-examples.md). Číslo je většinou určená pro usnadnění zobrazení v uživatelské prostředí Uživatelské rozhraní může například přidání hypertextového odkazu do back překlad, pokud řadu příkladů je větší než nula a zobrazení zpětného překladu jako prostý text, pokud nejsou žádné příklady. Všimněte si, že je skutečný počet příklady vrácený voláním [vyhledávání příkladů](./v3-0-dictionary-examples.md) může být kratší než `numExamples`, protože další filtrování se dá v reálném čase, chcete-li odebrat "špatné" příklady.
        
        * `frequencyCount`: Celé číslo představující frekvence tento překlad pár v datech. Hlavním účelem toto pole je poskytnout způsob řazení překlady zpět tak, aby byly první nejčastěji se vyskytujících podmínky uživatelské rozhraní.

    > [!NOTE]
    > Pokud se výraz právě vyhledaných neexistuje ve slovníku, odpověď je 200 (OK) ale `translations` seznam je prázdný seznam.

## <a name="examples"></a>Příklady

Tento příklad ukazuje, jak vyhledat alternativní překlady ve španělštině anglické termín `fly` .

# <a name="curltabcurl"></a>[Curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly'}]"
```

---

Text odpovědi (zkrácený pro přehlednost) je:

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

Tento příklad ukazuje, co se stane, když se hledá výraz neexistuje pro dvojici platný slovník.

# <a name="curltabcurl"></a>[Curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly123456'}]"
```

---

Vzhledem k tomu, že výraz nebyl nalezen ve slovníku, tělo odpovědi obsahuje prázdnou `translations` seznamu.

```
[
    {
        "normalizedSource":"fly123456",
        "displaySource":"fly123456",
        "translations":[]
    }
]
```
