---
title: Dovednost podmíněného vnímání
titleSuffix: Azure Cognitive Search
description: Podmíněná dovednost v Azure Kognitivní hledání umožňuje filtrování, vytváření výchozích hodnot a slučování hodnot v definici dovednosti.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f47ca56fa1b40422edeb0d4e11c24be6f60e49e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101666363"
---
# <a name="conditional-cognitive-skill"></a>Dovednost podmíněného vnímání

**Podmíněná** dovednost umožňuje scénářům Azure kognitivní hledání, které vyžadují logickou operaci k určení dat, která se mají přiřadit k výstupu. Mezi tyto scénáře patří filtrování, přiřazení výchozí hodnoty a sloučení dat na základě podmínky.

Následující pseudokódu demonstruje, co podmíněných dovedností dosáhne:

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> Tato dovednost není vázaná na rozhraní API Azure Cognitive Services a neúčtují se za jejich použití. Přesto byste ale měli k dispozici [prostředek Cognitive Services](cognitive-search-attach-cognitive-services.md) , abyste mohli přepsat možnost "Free" prostředku, která omezuje na malý počet rozšíření za den.

## <a name="odatatype"></a>@odata.type  
Microsoft. dovednosti. util. ConditionalSkill


## <a name="evaluated-fields"></a>Vyhodnocená pole

Tato dovednost je speciální, protože její vstupy jsou vyhodnoceny jako pole.

Následující položky jsou platné hodnoty výrazu:

-   Cesty k anotaci (cesty ve výrazech musí být odděleny znakem "$ (" a ")")
 <br/>
    Příklady:
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  Literály (řetězce, čísla, true, false, null) <br/>
    Příklady:
    ```
       "= 'this is a string'"   // string (note the single quotation marks)
       "= 34"                   // number
       "= true"                 // Boolean
       "= null"                 // null value
    ```

-  Výrazy, které používají relační operátory (= =,! =, >=, >, <=, <) <br/>
    Příklady:
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   Výrazy, které používají logické operátory (&&, | |,!, ^) <br/>
    Příklady:
    ```
        "= $(/document/language) == 'en' && $(/document/sentiment) > 0.5"
        "= !true"
    ```

-   Výrazy, které používají číselné operátory (+,-, \* ,/,%) <br/>
    Příklady: 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

Vzhledem k tomu, že podmíněná dovednost podporuje vyhodnocení, můžete ji použít ve scénářích s menší transformací. Podívejte se například na článek [definice dovedností 4](#transformation-example).

## <a name="skill-inputs"></a>Vstupy dovedností
Vstupy rozlišují velká a malá písmena.

| Vstup   | Description |
|-------------|-------------|
| pomocné   | Tento vstup je [vyhodnoceným polem](#evaluated-fields) , které představuje podmínku pro vyhodnocení. Tato podmínka by se měla vyhodnotit na logickou hodnotu (*true* nebo *false*).   <br/>  Příklady: <br/> "= true" <br/> "= $ (/Document/Language) = =" fr "" <br/> "= $ (/Document/Pages/ \* /Language) = = $ (/Document/expectedLanguage)" <br/> |
| whenTrue    | Tento vstup je [vyhodnoceným polem](#evaluated-fields) , které představuje hodnotu, která se má vrátit, pokud je podmínka vyhodnocena na *hodnotu true*. Řetězcové konstanty by měly být vráceny v jednoduchých uvozovkách (a). <br/>Ukázkové hodnoty: <br/> "=" kontrakt ""<br/>"= $ (/document/contractType)" <br/> "= $ (/Document/Entities/ \* )" <br/> |
| whenFalse   | Tento vstup je [vyhodnoceným polem](#evaluated-fields) , které představuje hodnotu, která se má vrátit, pokud je podmínka vyhodnocena jako *NEPRAVDA*. <br/>Ukázkové hodnoty: <br/> "=" kontrakt ""<br/>"= $ (/document/contractType)" <br/> "= $ (/Document/Entities/ \* )" <br/>

## <a name="skill-outputs"></a>Výstupy dovedností
Existuje jeden výstup, který se jednoduše nazývá "výstup". Vrátí hodnotu *whenFalse* , pokud je podmínka false, nebo *whenTrue* , pokud je podmínka pravdivá.

## <a name="examples"></a>Příklady

### <a name="sample-skill-definition-1-filter-documents-to-return-only-french-documents"></a>Ukázka definice dovednosti 1: filtrování dokumentů pro vrácení pouze francouzských dokumentů

Následující výstup vrátí pole vět ("/document/frenchSentences"), pokud je jazyk dokumentu francouzsky. Pokud jazyk není Francouzštin, hodnota je nastavena na *hodnotu null*.

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
Pokud je "/document/frenchSentences" použit jako *kontext* jiné dovednosti, tato dovednost se spustí pouze v případě, že hodnota "/Document/frenchSentences" není nastavena na *hodnotu null*.


### <a name="sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist"></a>Ukázka definice dovednosti 2: Nastavte výchozí hodnotu pro hodnotu, která neexistuje.

Následující výstup vytvoří anotaci ("/document/languageWithDefault"), která je nastavená na jazyk dokumentu, nebo na "ES", pokud není nastavený jazyk.

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

### <a name="sample-skill-definition-3-merge-values-from-two-fields-into-one"></a>Ukázková dovednost definice 3: sloučení hodnot ze dvou polí do jedné

V tomto příkladu mají některé věty vlastnost *frenchSentiment* . Pokaždé, když vlastnost *frenchSentiment* má hodnotu null, chceme použít hodnotu *englishSentiment* . Výstup přiřadíme členovi s názvem *mínění* ("/Document/Sentences/*/sentiment").

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
### <a name="sample-skill-definition-4-data-transformation-on-a-single-field"></a>Ukázka definice dovedností 4: transformace dat v jednom poli

V tomto příkladu se zobrazuje *mínění* , který je v rozsahu 0 až 1. Chceme ho transformovat tak, aby byl mezi-1 a 1. Tuto vedlejší transformaci můžeme použít pro podmíněnou dovednost.

V tomto příkladu nepoužíváme podmíněný aspekt dovednosti, protože podmínka je vždycky *true*.

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

## <a name="special-considerations"></a>Zvláštní požadavky
Některé parametry jsou vyhodnoceny, takže musíte být obzvláště opatrní, abyste mohli postupovat podle zdokumentovaného vzoru. Výrazy musí začínat symbolem rovná se. Cesta musí být oddělená znaky "$ (" a "") ". Nezapomeňte vkládat řetězce do jednoduchých uvozovek. Který pomáhá vyhodnocovacímu vyhodnocení rozlišovat mezi řetězci a skutečnými cestami a operátory. Také nezapomeňte vložit prázdné znaky kolem operátorů (např. "*" v cestě znamená něco jiného než násobení).


## <a name="next-steps"></a>Další kroky

+ [Integrované dovednosti](cognitive-search-predefined-skills.md)
+ [Jak definovat dovednosti](cognitive-search-defining-skillset.md)
