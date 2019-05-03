---
title: Podmíněné kognitivního vyhledávání dovedností (Azure Search) | Dokumentace Microsoftu
description: Podmíněné dovednosti, která umožňuje filtrování, vytváření výchozích hodnot a slučování hodnoty.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: luisca
ms.openlocfilehash: 6a203a38437ccb6a9c325e6594289744e0148c84
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028422"
---
#   <a name="conditional-skill"></a>Podmíněné dovedností

**Podmíněné dovednosti** umožňuje širokou škálu scénářů, které vyžadují logická operace rozhodnout data, která by měla být přiřazena výstup. Mezi tyto scénáře patří: filtrování, přiřazení výchozí hodnoty a slučování dat na základě podmínky.

Následujícím pseudokódu vysvětluje, co provede podmíněné dovedností:

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> Tato dovednosti není vázán na API služeb Cognitive Services a se vám neúčtují poplatky k jeho používání. Měli stále [připojit prostředek služeb Cognitive Services](cognitive-search-attach-cognitive-services.md), ale k přepsání **Free** resource – možnost, která omezuje vám malý počet denních obohacení za den.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>Vyhodnocený pole

Tato dovednosti je speciální, protože jeho vstupů Vyhodnocená pole.

Následují platné hodnoty výrazu:

-   Cesty poznámky (musí být odděleny cesty ve výrazech "$(" and ")") <br/>
    Příklady:
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  Literály (řetězců, čísel, true, false, null) <br/>
    Příklady:
    ```
       "= 'this is a string'"   // string, note the single quotes
       "= 34"                   // number
       "= true"                 // boolean
       "= null"                 // null value
    ```

-  Výrazy, které používají operátor porovnání (==,! =, > =, >, < =, <) <br/>
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

-   Výrazy, které používají číselné – operátor (+, -, \*, /, %) <br/>
    Příklady: 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

Z důvodu vyhodnocení nepodporuje podmíněné dovednosti slouží pro menší transformace scénáře. Najdete v ukázce [dovednosti definice 4](#transformation-examples) příklad.

## <a name="skill-inputs"></a>Vstupy dovedností
Vstupy jsou malá a velká písmena.

| Vstupy      | Popis |
|-------------|-------------|
| condition   | Tento vstup je [vyhodnotí pole](#evaluated-fields) , která představuje podmínku, která má vyhodnotit. Tato podmínka by se měl vyhodnotit logická hodnota (true nebo false).   <br/>  Příklady: <br/> "= true" <br/> "= $(/document/language) =="fr"" <br/> "= $(/ / stránkydokumentů/\*/language) == $(/ dokument/expectedLanguage)" <br/> |
| whenTrue    | Tento vstup je [vyhodnotí pole](#evaluated-fields). Hodnota, která má vrátit, pokud je podmínka vyhodnocena na hodnotu true. Konstanty řetězců by měla být vrácena v ' ' uvozovky. <br/>Ukázkové hodnoty: <br/> "="smlouva""<br/>"= $(/ dokument/contractType)" <br/> "= $(/ / entitydokumentu/\*)" <br/> |
| whenFalse   | Tento vstup je [vyhodnotí pole](#evaluated-fields). Hodnota k vrácení, pokud je podmínka vyhodnocena jako false.  <br/>Ukázkové hodnoty: <br/> "="smlouva""<br/>"= $(/ dokument/contractType)" <br/> "= $(/ / entitydokumentu/\*)" <br/>

## <a name="skill-outputs"></a>Výstupy dovedností
Existuje jeden výstup nazvaný "výstupní". Vrátí hodnotu whenFalse Pokud podmínka není splněna nebo whenTrue Pokud je podmínka pravdivá.

## <a name="examples"></a>Příklady

### <a name="sample-skill-definition-1-filtering-documents-to-return-only-french-documents"></a>Ukázková definice dovednosti 1: Filtrování dokumenty, které se vrátí jenom "Francouzské" dokumenty

Vrátí následující výstup pole vět ("/ dokument/frenchSentences"), pokud je jazyk dokumentu francouzština. Pokud jazyk není francouzské, tato hodnota se nastaví na hodnotu null.

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
Pokud "/ dokument/frenchSentences" slouží jako *kontextu* Další dovednosti, dovednosti, se spustí jenom v případě "/ dokument/frenchSentences" není nastaven na hodnotu null


### <a name="sample-skill-definition-2-setting-a-default-value-when-it-does-not-exist"></a>Ukázková definice dovednosti 2: Nastavení výchozí hodnoty, pokud neexistuje.

Poznámka ("/ dokument/languageWithDefault"), který je nastaven jazyk dokumentu nebo "es" Pokud jazyk není nastaven se vytvoří následující výstup.

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

### <a name="sample-skill-definition-3-merging-values-from-two-different-fields-into-a-single-field"></a>Ukázková definice dovednosti 3: Slučování hodnoty ze dvou různých polí do jednoho pole

V tomto příkladu jste některé věty *frenchSentiment* vlastnost. Pokaždé, když *frenchSentiment* vlastnost má hodnotu null, rádi bychom se použít *englishSentiment* hodnotu. Člen s názvem jednoduše jsme přiřadit výstup *mínění* ("/ dokumentu/mínění / * / mínění").

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

## <a name="transformation-examples"></a>Příklady transformace
### <a name="sample-skill-definition-4-performing-data-transformations-on-a-single-field"></a>Ukázková definice dovednosti 4: Provedení transformace dat na jednom poli

V tomto příkladu státem mínění 0 až 1 a rádi bychom se transformovat tak, aby se mezi -1 a 1. Toto je malý matematické transformace, že můžeme udělat pomocí podmíněného dovedností.

V tomto konkrétním příkladu jsme nikdy nepoužívejte podmíněné aspekt dovednosti jako podmínka má vždycky hodnotu true. 

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
Proto budete muset pečlivě zejména následující vzor dokument, mějte na paměti, že některé parametry jsou vyhodnocovány. Výrazy musí začínat znakem se rovná znak "=" a cesty musí být odděleny "$(" and ")". Ujistěte se, že do vašeho řetězce v 'jednoduché uvozovky' jako chyba při vyhodnocování rozlišovat mezi řetězci a operátory a skutečné cesty, která vám pomůže. Také, ujistěte se, že vložit mezery kolem operátorů (například * v cestě obsahuje jiný význam než operátor násobení).


## <a name="next-steps"></a>Další postup

+ [Předdefinované dovednosti](cognitive-search-predefined-skills.md)
+ [Definování dovedností](cognitive-search-defining-skillset.md)
