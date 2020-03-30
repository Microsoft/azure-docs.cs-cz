---
title: Podmíněné kognitivní dovednosti
titleSuffix: Azure Cognitive Search
description: Podmíněné dovednosti v Azure Cognitive Search umožňuje filtrování, vytváření výchozích hodnot a slučování hodnot v definici sady dovedností.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b5f1fc7f877854dd06fbbe09ff82e47208fa12d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792049"
---
# <a name="conditional-cognitive-skill"></a>Podmíněné kognitivní dovednosti

**Podmíněné** dovednosti umožňuje Azure Cognitive Search scénáře, které vyžadují logickou operaci k určení dat přiřadit k výstupu. Tyto scénáře zahrnují filtrování, přiřazení výchozí hodnoty a slučování dat na základě podmínky.

Následující pseudokód ukazuje, čeho podmíněná dovednost dosahuje:

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> Tato dovednost není vázána na rozhraní API Azure Cognitive Services a za jeho použití se vám neúčtují poplatky. Měli byste však stále [připojit prostředek služeb Cognitive Services](cognitive-search-attach-cognitive-services.md) přepsat možnost "Free" prostředek, který omezuje na malý počet obohacení za den.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>Vyhodnocená pole

Tato dovednost je zvláštní, protože jeho vstupy jsou vyhodnocovány pole.

Následující položky jsou platné hodnoty výrazu:

-   Cesty poznámky (cesty ve výrazech musí být odděleny "$(( a ")")
 <br/>
    Příklady:
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  Literály (řetězce, čísla, pravda, nepravda, null) <br/>
    Příklady:
    ```
       "= 'this is a string'"   // string (note the single quotation marks)
       "= 34"                   // number
       "= true"                 // Boolean
       "= null"                 // null value
    ```

-  Výrazy, které používají operátory porovnání (==, !=, >=, >, <=, <) <br/>
    Příklady:
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   Výrazy, které používají logické operátory (&&, ||, !, ^) <br/>
    Příklady:
    ```
        "= $(/document/language) == 'en' && $(/document/sentiment) > 0.5"
        "= !true"
    ```

-   Výrazy používající číselné operátory \*(+, -, , /, %) <br/>
    Příklady: 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

Vzhledem k tomu, že podmíněné dovednosti podporují hodnocení, můžete jej použít ve scénářích menší transformace. Viz například [definice dovednosti 4](#transformation-example).

## <a name="skill-inputs"></a>Vstupy dovedností
Vstupy rozlišují malá a velká písmena.

| Vstup   | Popis |
|-------------|-------------|
| Podmínka   | Tento vstup je [vyhodnoceno pole,](#evaluated-fields) které představuje podmínku k vyhodnocení. Tato podmínka by měla být vyhodnocena jako logická hodnota *(true* nebo *false).*   <br/>  Příklady: <br/> "= pravda" <br/> "= $(/document/language) =='fr'" <br/> "= $(/document/pages/\*/language) == $(/document/expectedLanguage)" <br/> |
| kdyžTrue    | Tento vstup je [vyhodnoceno pole,](#evaluated-fields) které představuje hodnotu, která má být vrácena, pokud je podmínka vyhodnocena jako *true*. Řetězce konstant by měly být vráceny v jednoduchých uvozovkách (" a '). <br/>Ukázkové hodnoty: <br/> "= "smlouva"<br/>"= $(/document/contractType)" <br/> "= $(/document/entities/\*)" <br/> |
| kdyžFalse   | Tento vstup je [vyhodnoceno pole,](#evaluated-fields) které představuje hodnotu, která má být vrácena, pokud je podmínka vyhodnocena jako *false*. <br/>Ukázkové hodnoty: <br/> "= "smlouva"<br/>"= $(/document/contractType)" <br/> "= $(/document/entities/\*)" <br/>

## <a name="skill-outputs"></a>Výstupy dovedností
Je tu jeden výstup, který se jednoduše nazývá "výstup". Vrátí hodnotu *whenFalse,* pokud je podmínka false nebo *whenTrue,* pokud je podmínka true.

## <a name="examples"></a>Příklady

### <a name="sample-skill-definition-1-filter-documents-to-return-only-french-documents"></a>Ukázková definice dovedností 1: Filtrování dokumentů pro vrácení pouze francouzských dokumentů

Následující výstup vrátí pole vět ("/document/frenchSentences"), pokud je jazyk eminace francouzština. Pokud jazyk není francouzština, hodnota je nastavena na *hodnotu null*.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'fr'" },
        { "name": "whenTrue", "source": "/document/sentences" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "frenchSentences" } ]
}
```
Pokud "/document/frenchSentences" se používá jako *kontext* jiné dovednosti, tato dovednost se spustí pouze v případě, že "/document/frenchSentences" není nastavena na *hodnotu null*.


### <a name="sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist"></a>Ukázková definice dovedností 2: Nastavení výchozí hodnoty pro hodnotu, která neexistuje

Následující výstup vytvoří poznámku ("/document/languageWithDefault"), která je nastavena na jazyk dokumentu nebo na "es", pokud jazyk není nastaven.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'es'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

### <a name="sample-skill-definition-3-merge-values-from-two-fields-into-one"></a>Ukázková definice dovedností 3: Sloučení hodnot ze dvou polí do jednoho

V tomto příkladu mají některé věty vlastnost *frenchSentiment.* Vždy, když *frenchSentiment* vlastnost je null, chceme použít *englishSentiment* hodnotu. Výstup přiřadíme členovi, který se nazývá *sentiment* ("/document/sentiment/*/sentiment").

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= $(/document/sentences/*/frenchSentiment) == null" },
        { "name": "whenTrue", "source": "/document/sentences/*/englishSentiment" },
        { "name": "whenFalse", "source": "/document/sentences/*/frenchSentiment" }
    ],
    "outputs": [ { "name": "output", "targetName": "sentiment" } ]
}
```

## <a name="transformation-example"></a>Příklad transformace
### <a name="sample-skill-definition-4-data-transformation-on-a-single-field"></a>Ukázková definice dovedností 4: Transformace dat v jednom poli

V tomto příkladu obdržíme *mínění,* které je mezi 0 a 1. Chceme ji přeměnit na -1 až 1. Můžeme použít podmíněné dovednosti k tomu to menší transformace.

V tomto příkladu nepoužíváme podmíněný aspekt dovednosti, protože podmínka je vždy *true*.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= true" },
        { "name": "whenTrue", "source": "= $(/document/sentences/*/sentiment) * 2 - 1" },
        { "name": "whenFalse", "source": "= 0" }
    ],
    "outputs": [ { "name": "output", "targetName": "normalizedSentiment" } ]
}
```

## <a name="special-considerations"></a>Zvláštní aspekty
Některé parametry jsou vyhodnocovány, takže musíte být obzvláště opatrní, abyste sledovali zdokumentovaný vzor. Výrazy musí začínat znaménkem rovná se. Cesta musí být oddělena "$(" a ")". Ujistěte se, že jste vložili řetězce do jednoduchých uvozovek. To pomáhá hodnotiteli rozlišovat mezi řetězci a skutečnými cestami a operátory. Také nezapomeňte dát prázdné místo kolem operátorů (například "*" v cestě znamená něco jiného než násobit).


## <a name="next-steps"></a>Další kroky

+ [Integrované dovednosti](cognitive-search-predefined-skills.md)
+ [Jak definovat sadu dovedností](cognitive-search-defining-skillset.md)
