---
title: V případě metody textová Microsoft překladač API slovník vyhledávání | Microsoft Docs
description: Použijte metodu Microsoft překladač Text API slovník vyhledávání.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 31435fcfca61517bfc72d534e911a1dcadbee52b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343562"
---
# <a name="text-api-30-dictionary-lookup"></a>Text API 3.0: Vyhledávání slovník

Poskytuje alternativní překladů pro slova a malý počet idiomatickou frází. Každý překlad má část řeči a seznamu překladů zpět. Back překlady povolit uživatelům pochopit překlad v kontextu. [Slovníku příklad](.\v3-0-dictionary-examples.md) operace povoluje další podrobnostem najdete příklad použití každý pár překlad.

## <a name="request-url"></a>Adresa URL požadavku

Odesílání `POST` požadavek na:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0
```

## <a name="request-parameters"></a>Parametry žádosti

Žádosti jsou parametry předané na řetězec dotazu:

<table width="100%">
  <th width="20%">Parametr dotazu</th>
  <th>Popis</th>
  <tr>
    <td>verze rozhraní API.</td>
    <td>*Požadovaný parametr*.<br/>Verze rozhraní API požadovaná klientem. Hodnota musí být `3.0`.</td>
  </tr>
  <tr>
    <td>od</td>
    <td>*Požadovaný parametr*.<br/>Určuje jazyk, ze vstupního textu. Jazyk zdrojového musí mít jednu z [podporované jazyky](.\v3-0-languages.md) součástí `dictionary` oboru.</td>
  </tr>
  <tr>
    <td>na</td>
    <td>*Požadovaný parametr*.<br/>Určuje jazyk text výstupu. Cílový jazyk musí mít jednu z [podporované jazyky](.\v3-0-languages.md) součástí `dictionary` oboru.</td>
  </tr>
</table>

Hlavičky požadavku zahrnují:

<table width="100%">
  <th width="20%">Záhlaví</th>
  <th>Popis</th>
  <tr>
    <td>_Jedním autorizačním_<br/>_Záhlaví_</td>
    <td>*Hlavička požadavku požadované*.<br/>V tématu [dostupné možnosti pro ověřování](./v3-0-reference.md#authentication).</td>
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
    <td>X ClientTraceId</td>
    <td>*Volitelné*.<br/>GUID klientem generovaná k jednoznačné identifikaci požadavku. Tuto hlavičku můžete vynechat, pokud zahrnují ID trasování v řetězci dotazu pomocí parametru dotazu s názvem `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Tělo požadavku

Text žádosti je pole JSON. Každý element pole je objekt JSON s řetězec vlastnost s názvem `Text`, která představuje termín k vyhledávání.

```json
[
    {"Text":"fly"}
]
```

Platí následující omezení:

* Pole může obsahovat maximálně 10 elementy.
* Hodnota textového elementu pole nesmí překročit 100 znaků včetně mezer.

## <a name="response-body"></a>Text odpovědi

Úspěšná odpověď je pole JSON s jeden výsledek pro každý řetězec v vstupního pole. Výsledný objekt zahrnuje následující vlastnosti:

  * `normalizedSource`: Řetězec poskytnutí normalizovaný formu termín zdroje. Například pokud se jedná o žádost "Jan", bude normalizovaný formulář "Jan". Obsah v tomto poli se změní na vstup [Příklady vyhledávání](.\v3-0-dictionary-examples.md).
    
  * `displaySource`: Řetězec poskytnutí termín zdroje ve formuláři nejlépe vhodné pro zobrazení koncového uživatele. Například pokud vstup je "Jan", zobrazení formuláře se projeví obvyklé správnost názvu: "Jan". 

  * `translations`: Seznamu překladů pro termín zdroje. Každý element seznamu je objekt s následujícími vlastnostmi:

    * `normalizedTarget`: Řetězec poskytnutí normalizovaný formu tento termín v jazyce cíl. Tato hodnota má být použit jako vstup pro [Příklady vyhledávání](.\v3-0-dictionary-examples.md).

    * `displayTarget`: Řetězec poskytnutí termín v jazyce, cíl a ve formuláři nejlépe vhodné pro zobrazení koncového uživatele. Obecně to bude pouze lišit od `normalizedTarget` z hlediska malá a velká písmena. Například správné spojení podstatného jména jako "Juan" bude mít `normalizedTarget = "juan"` a `displayTarget = "Juan"`.

    * `posTag`: Řetězec přidružením značku část řeči tento termín.

        | Název značky | Popis  |
        |----------|--------------|
        | PŘEDCHOZÍHO ROKU      | Přídavných jmen   |
        | ADV      | Příslovcí      |
        | CONJ     | Spojky |
        | DET      | Determiners  |
        | MODÁLNÍ    | Příkazy        |
        | PODSTATNÉ JMÉNO     | Podstatná jména        |
        | PŘÍPRAVA APLIKACE     | Předložky |
        | PRON     | Zájmena     |
        | PŘÍKAZ     | Příkazy        |
        | DALŠÍ    | Ostatní        |

        Jako poznámka implementaci byly tyto značky určuje část z – řeči označování anglické straně a pak přepnutím nejčastěji se vyskytující značky pro jednotlivé páry zdroj a cíl. Proto pokud se uživatelé často přeložit španělské slovo na jinou část řeči značky v angličtině, značky může stát se nesprávný (s ohledem na španělské word).

    * `confidence`: Hodnota mezi 0,0 a 1,0, která představuje "spolehlivosti" (nebo případně více přesně, "pravděpodobnosti v Cvičná data") z tohoto dvojice překlad. Součet spolehlivosti skóre pro jeden zdroj slovo může nebo nemusí součet, kterou se 1.0. 

    * `prefixWord`Poskytnutí: řetězec slovo zobrazuje jako předponu překladu. Toto je v současné době gendered determiner podstatná jména v jazycích, které mají gendered determiners. Například předpona španělské slovo "mosca" je "la", protože "mosca" ženského podstatné jméno ve španělštině. Toto je pouze závislé na překlad a ne na zdroji. Pokud není žádná předpona., bude prázdný řetězec.
    
    * `backTranslations`: Seznam "back překlady" cíle. Například zdroje slova, která cíl může překládat na. V seznamu záruku, že se tak, aby obsahovala word zdroj, který byl vyžádán (například pokud se aplikace word zdroj prohledávat se "fyzicky dostavili", pak je zaručeno, že "fyzicky dostavili" bude v `backTranslations` seznamu). Ale ho nemusí být v první pozici a často nebude. Každý prvek `backTranslations` seznamu je objekt popsaného následující vlastnosti:

        * `normalizedText`: Řetězec poskytnutí normalizovaný formu zdroj termín, který je back překlad cíle. Tato hodnota má být použit jako vstup pro [Příklady vyhledávání](.\v3-0-dictionary-examples.md).        

        * `displayText`: Řetězec poskytnutí zdroj termín, který je back překlad cíle ve formuláři nejlepší vhodné pro zobrazení koncového uživatele.

        * `numExamples`: Celé číslo představující počet příklady, které jsou k dispozici pro tento pár překlad. Skutečné příklady musí načíst pomocí samostatné volání [Příklady vyhledávání](.\v3-0-dictionary-examples.md). Číslo je většinou určeno k usnadnění zobrazení v UX Uživatelské rozhraní může například přidat hypertextový odkaz na back překlad, pokud počet příklady je větší než nula a zobrazit zpětný překlad jako prostý text, pokud nejsou žádné příklady. Všimněte si, že skutečný počet příklady vrácený volání [Příklady vyhledávání](.\v3-0-dictionary-examples.md) může být menší než `numExamples`, protože další filtrování může použít k odebrání "bad" Příklady za chodu.
        
        * `frequencyCount`: Celé číslo představující frekvenci tento pár překlad v datech. Hlavním účelem tohoto pole je prostředek k řazení back překladů, nejčastěji se vyskytující podmínky jsou uvedeny první nabízí uživatelské rozhraní.

    > [!NOTE]
    > Pokud se vyhledaných termín neexistuje ve slovníku, odpověď je 200 (OK) ale `translations` seznam je prázdný seznam.

## <a name="examples"></a>Příklady

Tento příklad ukazuje, jak vyhledat alternativní překlady v Španělština anglické podmínek `fly` .

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly'}]"
```

---

Text odpovědi (zkratka pro přehlednost) je:

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

Tento příklad ukazuje, co se stane, když termín vyhledávaných pro dvojici platný adresář neexistuje.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly123456'}]"
```

---

Protože výraz nebyl nalezen ve slovníku, text odpovědi obsahuje prázdnou `translations` seznamu.

```
[
    {
        "normalizedSource":"fly123456",
        "displaySource":"fly123456",
        "translations":[]
    }
]
```
