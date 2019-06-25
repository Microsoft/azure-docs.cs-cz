---
title: Podmíněné kognitivního vyhledávání dovedností (Azure Search) | Dokumentace Microsoftu
description: Podmíněné dovednosti umožňuje filtrování, vytváření výchozích hodnot a slučování hodnoty.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: luisca
ms.openlocfilehash: 149b701d4a1700787656448e2bdd0d92d2a93844
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65791520"
---
#   <a name="conditional-skill"></a>Podmíněné dovedností

*Podmíněné dovednosti* umožňuje používat scénáře s Azure Search, které vyžadují logické operaci. k určení dat k přiřazení do výstupu. Mezi tyto scénáře patří filtrování, přiřazení výchozí hodnoty a slučování dat na základě podmínky.

Následujícím pseudokódu ukazuje, co provede podmíněné dovedností:

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> Tato dovednosti není vázán na rozhraní API Azure Cognitive Services a se vám neúčtují poplatky k jeho používání. Ale měli stále [připojit prostředek služeb Cognitive Services](cognitive-search-attach-cognitive-services.md) přepsání možnost "Free" prostředek, který omezuje vám malý počet obohacení za den.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>Vyhodnocený pole

Tato dovednosti je speciální, protože jeho vstupů Vyhodnocená pole.

Následující položky jsou platné hodnoty výrazu:

-   Cesty poznámky (musí být odděleny cesty ve výrazech "$(" and ")")
 <br/>
    Příklady:
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  Literály (řetězců, čísel, true, false, null) <br/>
    Příklady:
    ```
       "= 'this is a string'"   // string (note the single quotation marks)
       "= 34"                   // number
       "= true"                 // Boolean
       "= null"                 // null value
    ```

-  Výrazy s operátory porovnání (==,! =, > =, >, < =, <) <br/>
    Příklady:
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   Výrazy, které používají logické operátory (& &, ||,!, ^) <br/>
    Příklady:
    ```
        "= $(/document/language) == 'en' && $(/document/sentiment) > 0.5"
        "= !true"
    ```

-   Výrazy, které používají číselné operátory (+, -, \*, /, %) <br/>
    Příklady: 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

Protože podmíněné dovednosti podporuje vyhodnocení, můžete ho ve scénářích menší transformace. Viz například [dovednosti definice 4](#transformation-example).

## <a name="skill-inputs"></a>Vstupy dovedností
Vstupy jsou malá a velká písmena.

| Vstup   | Popis |
|-------------|-------------|
| Podmínka   | Tento vstup je [vyhodnotí pole](#evaluated-fields) , která představuje podmínku, která má vyhodnotit. Tato podmínka by se měl vyhodnotit na logickou hodnotu (*true* nebo *false*).   <br/>  Příklady: <br/> "= true" <br/> "= $(/document/language) =="fr"" <br/> "= $(/ / stránkydokumentů/\*/language) == $(/ dokument/expectedLanguage)" <br/> |
| whenTrue    | Tento vstup je [vyhodnotí pole](#evaluated-fields) , který představuje hodnotu vrátit, pokud je podmínka vyhodnocena *true*. Konstanty řetězců by měla být vrácena v jednoduchých uvozovkách ("a"). <br/>Ukázkové hodnoty: <br/> "="smlouva""<br/>"= $(/ dokument/contractType)" <br/> "= $(/ / entitydokumentu/\*)" <br/> |
| whenFalse   | Tento vstup je [vyhodnotí pole](#evaluated-fields) , který představuje hodnotu vrátit, pokud je podmínka vyhodnocena *false*. <br/>Ukázkové hodnoty: <br/> "="smlouva""<br/>"= $(/ dokument/contractType)" <br/> "= $(/ / entitydokumentu/\*)" <br/>

## <a name="skill-outputs"></a>Výstupy dovedností
Existuje jeden výstup, který se nazývá "výstupní". Vrátí hodnotu *whenFalse* Pokud podmínka není splněna nebo *whenTrue* Pokud je podmínka pravdivá.

## <a name="examples"></a>Příklady

### <a name="sample-skill-definition-1-filter-documents-to-return-only-french-documents"></a>Ukázková definice dovednosti 1: Filtrovat dokumenty, které se vrátí jenom francouzské dokumenty

Následující výstup vrátí pole vět ("/ dokument/frenchSentences"), pokud z jazyka dokumentu je francouzština. Pokud jazyk není francouzština, hodnota nastavená na *null*.

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
Pokud "/ dokument/frenchSentences" slouží jako *kontextu* Další dovednosti, dovednosti, spustí jenom v případě "/ dokument/frenchSentences" není nastavená na *null*.


### <a name="sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist"></a>Ukázková definice dovednosti 2: Nastavení výchozí hodnoty na hodnotu, která neexistuje.

Vytvoří následující výstup poznámku ("/ dokument/languageWithDefault"), který je nastaven na jazykovou verzi dokumentu nebo "es", pokud jazyk není nastaven.

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

### <a name="sample-skill-definition-3-merge-values-from-two-fields-into-one"></a>Ukázková definice dovednosti 3: Hodnoty ze dvou polí sloučit do větve

V tomto příkladu jste některé věty *frenchSentiment* vlastnost. Pokaždé, když *frenchSentiment* vlastnost má hodnotu null, chceme použít *englishSentiment* hodnotu. Jsme přiřaďte výstup k člena, který se nazývá *mínění* ("/ dokumentu/mínění / * / mínění").

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
### <a name="sample-skill-definition-4-data-transformation-on-a-single-field"></a>Ukázková definice dovednosti 4: Transformace dat na jednom poli

V tomto příkladu, státem *mínění* , která je od 0 do 1. Chcete transformovat být v rozmezí od -1 a 1. Můžeme použít podmíněný dovednosti provádět tento dílčí transformaci.

V tomto příkladu nebudeme používat podmíněný aspekt dovednosti podmínka má vždycky *true*.

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

## <a name="special-considerations"></a>Zvláštní upozornění
Některé parametry jsou vyhodnocovány, proto musíte pečlivě zejména zdokumentovaných tvar. Výrazy musí začínat symbolem rovná. Cesta musí být odděleny "$(" and ")". Ujistěte se, že do řetězce v jednoduchých uvozovkách. To pomáhá Chyba při vyhodnocování rozlišovat mezi řetězci a operátory a skutečné cesty. Také, ujistěte se, že vložit mezery kolem operátorů (například "*" v cestě znamená něco jiného než multiply).


## <a name="next-steps"></a>Další postup

+ [Předdefinované dovednosti](cognitive-search-predefined-skills.md)
+ [Definování dovedností](cognitive-search-defining-skillset.md)
