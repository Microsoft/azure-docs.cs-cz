---
title: Příklady slovníku překladatelů
titleSuffix: Azure Cognitive Services
description: Příklady slovníku překladatelů poskytují příklady, které ukazují, jak se ve slovníku používají výrazy v kontextu.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: swmachan
ms.openlocfilehash: 1fe5a5669173733e3d0cf1b56b9997d15a3ff138
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021299"
---
# <a name="translator-30-dictionary-examples"></a>Translator 3,0: příklady slovníku

Obsahuje příklady, které ukazují, jak jsou výrazy ve slovníku použity v kontextu. Tato operace se používá společně s [vyhledáváním slovníku](./v3-0-dictionary-lookup.md).

## <a name="request-url"></a>Adresa URL požadavku

Odeslat `POST` požadavek na:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0
```

## <a name="request-parameters"></a>Parametry žádosti

Parametry žádosti předané řetězci dotazu jsou:

| Parametr dotazu | Popis |
| --------- | ----------- |
| verze-api <img width=200/> | **Povinný parametr**.<br/>Verze rozhraní API, kterou klient požaduje. Hodnota musí být `3.0` . |
| Výsledkem | **Povinný parametr**.<br/>Určuje jazyk vstupního textu. Zdrojový jazyk musí být jeden z [podporovaných jazyků](./v3-0-languages.md) zahrnutých v `dictionary` oboru. |
| na | **Povinný parametr**.<br/>Určuje jazyk výstupního textu. Cílový jazyk musí být jeden z [podporovaných jazyků](./v3-0-languages.md) zahrnutých v `dictionary` oboru.  | 

Hlavičky požadavku zahrnují:

| Hlavičky  | Popis |
| ------ | ----------- |
| Ověřovací hlavičky (y) <img width=200/>  | **Požadovaná hlavička žádosti**<br/>Podívejte se <a href="/azure/cognitive-services/translator/reference/v3-0-reference#authentication">na dostupné možnosti ověřování</a>. |
| Typ obsahu | **Požadovaná hlavička žádosti**<br/>Určuje typ obsahu datové části. Možné hodnoty jsou: `application/json` . |
| Délka obsahu   | **Požadovaná hlavička žádosti**<br/>Délka textu žádosti |
| X – ClientTraceId   | **Volitelné**.<br/>Identifikátor GUID generovaný klientem pro jednoznačnou identifikaci požadavku. Tuto hlavičku můžete vynechat, pokud zahrnete ID trasování do řetězce dotazu pomocí parametru dotazu s názvem `ClientTraceId` . |

## <a name="request-body"></a>Text požadavku

Tělo požadavku je pole JSON. Každý prvek pole je objekt JSON s následujícími vlastnostmi:

  * `Text`: Řetězec určující termín, který se má vyhledat. Mělo by se jednat o hodnotu `normalizedText` pole ze zpětných překladů předchozího požadavku na [vyhledávání slovníku](./v3-0-dictionary-lookup.md) . Může to být také hodnota `normalizedSource` pole.

  * `Translation`: Řetězec určující přeložený text, který dříve vrátila operace [vyhledávání slovníku](./v3-0-dictionary-lookup.md) . Mělo by se jednat o hodnotu z `normalizedTarget` pole v `translations` seznamu odpovědi [vyhledávání slovníku](./v3-0-dictionary-lookup.md) . Služba vrátí příklady pro určitý textový pár v cílovém zdroji.

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

  * `normalizedSource`: Řetězec, který poskytuje normalizovanou formu zdrojového výrazu. Obecně platí, že by mělo být stejné jako hodnota `Text` pole v indexu odpovídajícího seznamu v těle požadavku.
    
  * `normalizedTarget`: Řetězec, který poskytuje normalizovanou formu cílového období. Obecně platí, že by mělo být stejné jako hodnota `Translation` pole v indexu odpovídajícího seznamu v těle požadavku.
  
  * `examples`: Seznam příkladů pro dvojici (zdrojový termín, cílový termín). Každý prvek seznamu je objekt s následujícími vlastnostmi:

    * `sourcePrefix`: Řetězec, který má být zřetězen _před_ hodnotou `sourceTerm` pro vytvoření úplného příkladu. Nepřidávat mezeru, protože je již tam, kde by měla být. Tato hodnota může být prázdný řetězec.

    * `sourceTerm`: Řetězec, který se rovná skutečnému vyhledanému výrazu. Řetězec je přidán s `sourcePrefix` a `sourceSuffix` pro vytvoření kompletního příkladu. Jeho hodnota je oddělená, takže se dá označit v uživatelském rozhraní, třeba pomocí tučného písma.

    * `sourceSuffix`: Řetězec, který má být zřetězen _po_ hodnotě, `sourceTerm` Chcete-li vytvořit úplný příklad. Nepřidávat mezeru, protože je již tam, kde by měla být. Tato hodnota může být prázdný řetězec.

    * `targetPrefix`: Řetězec podobný řetězci, `sourcePrefix` ale pro cíl.

    * `targetTerm`: Řetězec podobný řetězci, `sourceTerm` ale pro cíl.

    * `targetSuffix`: Řetězec podobný řetězci, `sourceSuffix` ale pro cíl.

    > [!NOTE]
    > Pokud slovník neobsahuje žádné příklady, odpověď je 200 (OK), ale `examples` seznam je prázdný seznam.

## <a name="examples"></a>Příklady

Tento příklad ukazuje, jak vyhledat příklady pro pár, který se skládá z anglického termínu `fly` a jeho španělského překladu `volar` .

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