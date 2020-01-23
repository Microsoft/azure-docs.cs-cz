---
title: Příklady Translator Text API slovníku
titleSuffix: Azure Cognitive Services
description: Příklady Translator Text API slovníku obsahují příklady, které ukazují, jak se výrazy ve slovníku používají v kontextu.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: swmachan
ms.openlocfilehash: b3551a8df19e47178c7bacd9218cfa60b66d81f9
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548063"
---
# <a name="translator-text-api-30-dictionary-examples"></a>Translator Text API 3,0: příklady slovníku

Obsahuje příklady, které ukazují, jak jsou výrazy ve slovníku použity v kontextu. Tato operace se používá společně s [vyhledáváním slovníku](./v3-0-dictionary-lookup.md).

## <a name="request-url"></a>Adresa URL požadavku

Odeslat žádost o `POST`:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0
```

## <a name="request-parameters"></a>Parametry žádosti

Parametry žádosti předané řetězci dotazu jsou:

| Parametr dotazu | Popis |
| --------- | ----------- |
| api-version <img width=200/> | **Povinný parametr**.<br/>Verze rozhraní API, kterou klient požaduje. Hodnota musí být `3.0`. |
| od | **Povinný parametr**.<br/>Určuje jazyk vstupního textu. Zdrojový jazyk musí být jeden z [podporovaných jazyků](./v3-0-languages.md) , které jsou zahrnuté v oboru `dictionary`. |
| na | **Povinný parametr**.<br/>Určuje jazyk výstupního textu. Cílový jazyk musí být jeden z [podporovaných jazyků](./v3-0-languages.md) , které jsou zahrnuté v oboru `dictionary`.  | 

Hlavičky požadavku zahrnují:

| Hlavičky  | Popis |
| ------ | ----------- |
| Ověřovací hlavičky (y) <img width=200/>  | **Požadovaná hlavička žádosti**<br/>Podívejte se <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">na dostupné možnosti ověřování</a>. |
| Content-Type | **Požadovaná hlavička žádosti**<br/>Určuje typ obsahu datové části. Možné hodnoty jsou: `application/json`. |
| Délka obsahu   | **Požadovaná hlavička žádosti**<br/>Délka textu žádosti |
| X-ClientTraceId   | **Volitelné**.<br/>Identifikátor GUID generovaný klientem pro jednoznačnou identifikaci požadavku. Tuto hlavičku můžete vynechat, pokud zahrnete ID trasování do řetězce dotazu pomocí parametru dotazu s názvem `ClientTraceId`. |

## <a name="request-body"></a>Text požadavku

Tělo požadavku je pole JSON. Každý prvek pole je objekt JSON s následujícími vlastnostmi:

  * `Text`: řetězec určující termín, který se má vyhledat. Mělo by se jednat o hodnotu `normalizedText` pole z back-vrácených předchozích požadavků na [vyhledávání slovníku](./v3-0-dictionary-lookup.md) . Může to být také hodnota pole `normalizedSource`.

  * `Translation`: řetězec určující přeložený text, který dříve vrátila operace [vyhledávání slovníku](./v3-0-dictionary-lookup.md) . Mělo by se jednat o hodnotu z pole `normalizedTarget` v seznamu `translations` odpovědi [vyhledávání slovníku](./v3-0-dictionary-lookup.md) . Služba vrátí příklady pro určitý textový pár v cílovém zdroji.

Příklad:

```json
[
    {"Text":"fly", "Translation":"volar"}
]
```

Platí následující omezení:

* Pole může mít maximálně 10 prvků.
* Textová hodnota prvku pole nesmí být delší než 100 znaků včetně mezer.

## <a name="response-body"></a>Text odpovědi

Úspěšná odpověď je pole JSON s jedním výsledkem pro každý řetězec ve vstupním poli. Objekt výsledku obsahuje následující vlastnosti:

  * `normalizedSource`: řetězec, který poskytuje normalizovanou formu zdrojového výrazu. Obecně platí, že by se mělo shodovat s hodnotou pole `Text` v seznamu odpovídajícího indexu v těle požadavku.
    
  * `normalizedTarget`: řetězec, který poskytuje normalizovanou formu cílového období. Obecně platí, že by se mělo shodovat s hodnotou pole `Translation` v seznamu odpovídajícího indexu v těle požadavku.
  
  * `examples`: seznam příkladů pro dvojici (zdrojový termín, cílový termín). Každý prvek seznamu je objekt s následujícími vlastnostmi:

    * `sourcePrefix`: řetězec, který se má zřetězit _před_ hodnotou `sourceTerm` pro vytvoření kompletního příkladu. Nepřidávat mezeru, protože je již tam, kde by měla být. Tato hodnota může být prázdný řetězec.

    * `sourceTerm`: řetězec, který se rovná skutečnému vyhledanému výrazu. Řetězec je přidán s `sourcePrefix` a `sourceSuffix` k vytvoření kompletního příkladu. Jeho hodnota je oddělená, takže se dá označit v uživatelském rozhraní, třeba pomocí tučného písma.

    * `sourceSuffix`: řetězec, který se má zřetězit _za_ hodnotou `sourceTerm`, která vytvoří úplný příklad. Nepřidávat mezeru, protože je již tam, kde by měla být. Tato hodnota může být prázdný řetězec.

    * `targetPrefix`: řetězec podobný `sourcePrefix`, ale pro cíl.

    * `targetTerm`: řetězec podobný `sourceTerm`, ale pro cíl.

    * `targetSuffix`: řetězec podobný `sourceSuffix`, ale pro cíl.

    > [!NOTE]
    > Pokud slovník neobsahuje žádné příklady, odpověď je 200 (OK), ale seznam `examples` je prázdný seznam.

## <a name="examples"></a>Příklady

Tento příklad ukazuje, jak vyhledat příklady pro pár, který se skládá z anglického termínu `fly` a jeho Španělská `volar`překladu.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly', 'Translation':'volar'}]"
```

Tělo odpovědi (zkrácené pro přehlednost) je:

```
[
    {
        "normalizedSource":"fly",
        "normalizedTarget":"volar",
        "examples":[
            {
                "sourcePrefix":"They need machines to ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Necesitan máquinas para ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },      
            {
                "sourcePrefix":"That should really ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Eso realmente debe ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },
            //
            // ...list abbreviated for documentation clarity
            //
        ]
    }
]
```
