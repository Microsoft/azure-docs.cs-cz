---
title: Příklady Translator Text API slovníku
titleSuffix: Azure Cognitive Services
description: Použijte příklady Translator Text API slovníku.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: swmachan
ms.openlocfilehash: 419e11862b2c584686922cfc8d1db72ee4751a03
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934034"
---
# <a name="translator-text-api-30-dictionary-examples"></a>Translator Text API 3,0: Příklady slovníku

Obsahuje příklady, které ukazují, jak jsou výrazy ve slovníku použity v kontextu. Tato operace se používá společně s [vyhledáváním slovníku](./v3-0-dictionary-lookup.md).

## <a name="request-url"></a>Adresa URL požadavku

`POST` Odeslat požadavek na:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0
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

Tělo požadavku je pole JSON. Každý prvek pole je objekt JSON s následujícími vlastnostmi:

  * `Text`: Řetězec určující termín, který má být hledán. Mělo by se jednat o hodnotu `normalizedText` pole ze zpětných překladů předchozího požadavku na [vyhledávání slovníku](./v3-0-dictionary-lookup.md) . Může to být také hodnota `normalizedSource` pole.

  * `Translation`: Řetězec určující přeložený text, který dříve vrátila operace [vyhledávání slovníku](./v3-0-dictionary-lookup.md) . Mělo by se jednat o hodnotu z `normalizedTarget` pole `translations` v seznamu odpovědi [vyhledávání slovníku](./v3-0-dictionary-lookup.md) . Služba vrátí příklady pro určitý textový pár v cílovém zdroji.

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

  * `normalizedSource`: Řetězec, který poskytuje normalizovanou formu zdrojového termínu. Obecně platí, že by mělo být stejné jako hodnota `Text` pole v indexu odpovídajícího seznamu v těle požadavku.
    
  * `normalizedTarget`: Řetězec, který poskytuje normalizovanou formu cílového období. Obecně platí, že by mělo být stejné jako hodnota `Translation` pole v indexu odpovídajícího seznamu v těle požadavku.
  
  * `examples`: Seznam příkladů pro dvojici (zdrojový termín, cílový termín). Každý prvek seznamu je objekt s následujícími vlastnostmi:

    * `sourcePrefix`: Řetězec, který se má zřetězit _před_ hodnotou `sourceTerm` pro vytvoření úplného příkladu. Nepřidávat mezeru, protože je již tam, kde by měla být. Tato hodnota může být prázdný řetězec.

    * `sourceTerm`: Řetězec, který se rovná skutečnému vyhledanému výrazu. Řetězec je přidán s `sourcePrefix` a `sourceSuffix` pro vytvoření kompletního příkladu. Jeho hodnota je oddělená, takže se dá označit v uživatelském rozhraní, třeba pomocí tučného písma.

    * `sourceSuffix`: Řetězec, který má být zřetězen _po_ hodnotě `sourceTerm` pro vytvoření úplného příkladu. Nepřidávat mezeru, protože je již tam, kde by měla být. Tato hodnota může být prázdný řetězec.

    * `targetPrefix`: Řetězec podobný `sourcePrefix` řetězci, ale pro cíl.

    * `targetTerm`: Řetězec podobný `sourceTerm` řetězci, ale pro cíl.

    * `targetSuffix`: Řetězec podobný `sourceSuffix` řetězci, ale pro cíl.

    > [!NOTE]
    > Pokud slovník neobsahuje žádné příklady, odpověď je 200 (ok), ale `examples` seznam je prázdný seznam.

## <a name="examples"></a>Příklady

Tento příklad ukazuje, jak vyhledat příklady pro pár, který se skládá z anglického termínu `fly` a jeho španělského překladu. `volar`

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
