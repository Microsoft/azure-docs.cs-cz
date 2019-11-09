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
ms.date: 03/29/2018
ms.author: swmachan
ms.openlocfilehash: daa3ff7cb9006a0ec940a57a4db31746dcb0550a
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888096"
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

<table width="100%">
  <th width="20%">Parametr dotazu</th>
  <th>Popis</th>
  <tr>
    <td>verze API-Version</td>
    <td>*Povinný parametr*.<br/>Verze rozhraní API, kterou klient požaduje. Hodnota musí být `3.0`.</td>
  </tr>
  <tr>
    <td>Výsledkem</td>
    <td>*Povinný parametr*.<br/>Určuje jazyk vstupního textu. Zdrojový jazyk musí být jeden z [podporovaných jazyků](./v3-0-languages.md) , které jsou zahrnuté v oboru `dictionary`.</td>
  </tr>
  <tr>
    <td>na</td>
    <td>*Povinný parametr*.<br/>Určuje jazyk výstupního textu. Cílový jazyk musí být jeden z [podporovaných jazyků](./v3-0-languages.md) , které jsou zahrnuté v oboru `dictionary`.</td>
  </tr>
</table>

Hlavičky požadavku zahrnují:

<table width="100%">
  <th width="20%">Hlavičky</th>
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
    <td>X – ClientTraceId</td>
    <td>*Volitelné*.<br/>Identifikátor GUID generovaný klientem pro jednoznačnou identifikaci požadavku. Tuto hlavičku můžete vynechat, pokud zahrnete ID trasování do řetězce dotazu pomocí parametru dotazu s názvem `ClientTraceId`.</td>
  </tr>
</table> 

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

## <a name="response-body"></a>Tělo odpovědi

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
