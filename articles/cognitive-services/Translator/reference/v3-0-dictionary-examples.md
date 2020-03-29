---
title: Metoda příkladů slovníku překladače textových rozhraní API
titleSuffix: Azure Cognitive Services
description: Metoda Translator Text API Dictionary Examples poskytuje příklady, které ukazují, jak se používají termíny ve slovníku v kontextu.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: swmachan
ms.openlocfilehash: b3551a8df19e47178c7bacd9218cfa60b66d81f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "76548063"
---
# <a name="translator-text-api-30-dictionary-examples"></a>Překladač Text API 3.0: Příklady slovníku

Obsahuje příklady, které ukazují, jak jsou termíny ve slovníku používány v kontextu. Tato operace se používá v tandemu s [vyhledávání slovníku](./v3-0-dictionary-lookup.md).

## <a name="request-url"></a>Adresa URL požadavku

Odeslání `POST` žádosti na:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0
```

## <a name="request-parameters"></a>Parametry požadavku

Parametry požadavku předané v řetězci dotazu jsou:

| Parametr dotazu | Popis |
| --------- | ----------- |
| verze-api <img width=200/> | **Povinný parametr**.<br/>Verze rozhraní API požadované klientem. Hodnota musí `3.0`být . |
| Z | **Povinný parametr**.<br/>Určuje jazyk vstupního textu. Zdrojový jazyk musí být jedním z [podporovaných jazyků](./v3-0-languages.md) zahrnutých v `dictionary` oboru. |
| na | **Povinný parametr**.<br/>Určuje jazyk výstupního textu. Cílový jazyk musí být jedním z [podporovaných jazyků](./v3-0-languages.md) zahrnutých v `dictionary` oboru.  | 

Mezi hlavičky požadavku patří:

| Hlavičky  | Popis |
| ------ | ----------- |
| Autentizační hlavička (záhlaví) <img width=200/>  | **Hlavička požadavku .**<br/>Viz <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">dostupné možnosti ověřování</a>. |
| Typ obsahu | **Hlavička požadavku .**<br/>Určuje typ obsahu datové části. Možné hodnoty `application/json`jsou: . |
| Délka obsahu   | **Hlavička požadavku .**<br/>Délka těla požadavku. |
| X-ClientTraceId   | **Nepovinné**.<br/>Identifikátor GUID generovaný klientem k jednoznačné identifikaci požadavku. Toto záhlaví můžete vynechat, pokud do řetězce dotazu zahrnete `ClientTraceId`ID trasování pomocí parametru dotazu s názvem . |

## <a name="request-body"></a>Text požadavku

Tělo požadavku je pole JSON. Každý prvek pole je objekt JSON s následujícími vlastnostmi:

  * `Text`: Řetězec určující termín vyhledávání. To by měla být `normalizedText` hodnota pole z zpětných překladů předchozí ho slovníku [vyhledávací](./v3-0-dictionary-lookup.md) požadavek. Může to být také `normalizedSource` hodnota pole.

  * `Translation`: Řetězec určující přeložený text, který byl dříve vrácen vyhledávací operací [Slovník.](./v3-0-dictionary-lookup.md) To by měla být `normalizedTarget` hodnota `translations` z pole v seznamu [slovník vyhledávací](./v3-0-dictionary-lookup.md) odpověď. Služba vrátí příklady pro konkrétní dvojici slov cíl zdroj.

Příkladem je:

```json
[
    {"Text":"fly", "Translation":"volar"}
]
```

Platí následující omezení:

* Pole může mít maximálně 10 prvků.
* Textová hodnota prvku pole nesmí přesáhnout 100 znaků včetně mezer.

## <a name="response-body"></a>Text odpovědi

Úspěšná odpověď je pole JSON s jedním výsledkem pro každý řetězec ve vstupním poli. Výsledný objekt obsahuje následující vlastnosti:

  * `normalizedSource`: Řetězec poskytující normalizovanou formu zdrojového termínu. Obecně by to mělo být totožné s hodnotou `Text` pole v indexu odpovídajícího seznamu v těle požadavku.
    
  * `normalizedTarget`: Řetězec poskytující normalizovanou formu cílového termínu. Obecně by to mělo být totožné s hodnotou `Translation` pole v indexu odpovídajícího seznamu v těle požadavku.
  
  * `examples`: Seznam příkladů dvojice (zdrojový termín, cílový termín). Každý prvek seznamu je objekt s následujícími vlastnostmi:

    * `sourcePrefix`: Řetězec zřetězit _před_ hodnotou `sourceTerm` tvoří úplný příklad. Nepřidávejte znak mezery, protože je již tam, když by měl být. Tato hodnota může být prázdný řetězec.

    * `sourceTerm`: Řetězec rovnající se skutečnému termínu vyhledal. Řetězec je přidán `sourcePrefix` `sourceSuffix` s a tvoří úplný příklad. Jeho hodnota je oddělena tak, aby mohla být označena v uživatelském rozhraní, například tučným písmem.

    * `sourceSuffix`: Řetězec zřetězit _za_ hodnotou `sourceTerm` tvoří úplný příklad. Nepřidávejte znak mezery, protože je již tam, když by měl být. Tato hodnota může být prázdný řetězec.

    * `targetPrefix`: Řetězec `sourcePrefix` podobný, ale pro cíl.

    * `targetTerm`: Řetězec `sourceTerm` podobný, ale pro cíl.

    * `targetSuffix`: Řetězec `sourceSuffix` podobný, ale pro cíl.

    > [!NOTE]
    > Pokud ve slovníku nejsou žádné příklady, je odpověď 200 `examples` (OK), ale seznam je prázdný seznam.

## <a name="examples"></a>Příklady

Tento příklad ukazuje, jak vyhledat příklady pro dvojici `fly` tvořenou `volar`anglickým termínem a jeho španělským překladem .

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly', 'Translation':'volar'}]"
```

Reakční těleso (zkráceně pro přehlednost) je:

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
