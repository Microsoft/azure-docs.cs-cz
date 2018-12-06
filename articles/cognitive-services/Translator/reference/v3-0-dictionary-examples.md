---
title: Translator Text API slovníku příklady – metoda
titlesuffix: Azure Cognitive Services
description: Použijte metodu Translator Text API slovníku příklady.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 4b24ba4b4d83ac3f0c8291308debb6317efa4a55
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52967993"
---
# <a name="translator-text-api-30-dictionary-examples"></a>Translator Text API 3.0: Ukázky slovníků

Poskytuje příklady, které ukazují, jak ve slovníku pojmů v kontextu. Tato operace se používá v kombinaci s částí [vyhledávací slovník](./v3-0-dictionary-lookup.md).

## <a name="request-url"></a>Adresa URL požadavku

Odeslat `POST` požadavek na:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0
```

## <a name="request-parameters"></a>Parametry žádosti

Žádosti jsou parametry předané v řetězci dotazu:

<table width="100%">
  <th width="20%">Parametr dotazu</th>
  <th>Popis</th>
  <tr>
    <td>verze API-version</td>
    <td>*Povinný parametr*.<br/>Verze rozhraní API požadovaná klientem. Hodnota musí být `3.0`.</td>
  </tr>
  <tr>
    <td>od</td>
    <td>*Povinný parametr*.<br/>Určuje jazyk, který vstupního textu. Zdrojový jazyk musí být jedna z [podporované jazyky](./v3-0-languages.md) součástí `dictionary` oboru.</td>
  </tr>
  <tr>
    <td>na</td>
    <td>*Povinný parametr*.<br/>Určuje jazyk text výstupu. Cílový jazyk musí být jedna z [podporované jazyky](./v3-0-languages.md) součástí `dictionary` oboru.</td>
  </tr>
</table>

Hlavičky žádosti patří:

<table width="100%">
  <th width="20%">Hlavičky</th>
  <th>Popis</th>
  <tr>
    <td>_Jedním autorizačním_<br/>_Záhlaví_</td>
    <td>*Hlavička požadavku požadované*.<br/>Zobrazit [dostupné možnosti pro ověřování](./v3-0-reference.md#authentication).</td>
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
    <td>X-ClientTraceId</td>
    <td>*Volitelné*.<br/>Klientem generovaná identifikátor GUID k jednoznačné identifikaci požadavku. Tato hlavička můžete vynechat, pokud zahrnete ID trasování v řetězci dotazu pomocí parametru dotazu s názvem `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Text požadavku

Text požadavku je pole JSON. Každý prvek pole je objekt JSON s následujícími vlastnostmi:

  * `Text`: Řetězec určující termín, který vyhledávání. Měl by to být hodnota `normalizedText` pole z back překlady předchozí [vyhledávací slovník](./v3-0-dictionary-lookup.md) požadavku. Hodnota může být také `normalizedSource` pole.

  * `Translation`: Řetězec určující přeložený text předtím vrátila rutina [vyhledávací slovník](./v3-0-dictionary-lookup.md) operace. To by měla být hodnota od `normalizedTarget` pole `translations` seznam [vyhledávací slovník](./v3-0-dictionary-lookup.md) odpovědi. Služba vrátí příklady pro dvojice slov konkrétní zdroj cíl.

Příkladem je:

```json
[
    {"Text":"fly", "Translation":"volar"}
]
```

Platí následující omezení:

* Pole může mít maximálně 10 prvků.
* Textová hodnota elementu pole může mít maximálně 100 znaků včetně mezer.

## <a name="response-body"></a>Text odpovědi

Úspěšná odpověď je pole JSON se jeden výsledek pro každý řetězec vstupního pole. Výsledný objekt zahrnuje následující vlastnosti:

  * `normalizedSource`: Řetězec poskytující normalizovaná forma jako zdroj. Obecně platí, je třeba stejné hodnoty jako `Text` pole v indexu odpovídající seznamu v textu požadavku.
    
  * `normalizedTarget`: Řetězec poskytující normalizovaná forma jako cíl. Obecně platí, je třeba stejné hodnoty jako `Translation` pole v indexu odpovídající seznamu v textu požadavku.
  
  * `examples`: Seznam příkladů pro (zdrojový výraz, výraz cílového) pár. Každý prvek seznamu je objekt s následujícími vlastnostmi:

    * `sourcePrefix`: Řetězec, který má zřetězit _před_ hodnotu `sourceTerm` a vytvoří kompletní příklad. Protože je již nainstalováno ale měla by být nepřidávejte znak mezery. Tato hodnota může být prázdný řetězec.

    * `sourceTerm`: Řetězec stejný jako skutečný vyhledávat. Řetězec se přidá s `sourcePrefix` a `sourceSuffix` a vytvoří kompletní příklad. Její hodnota je oddělená, může být označený v uživatelském rozhraní, například podle tučné ho.

    * `sourceSuffix`: Řetězec, který má zřetězit _po_ hodnotu `sourceTerm` a vytvoří kompletní příklad. Protože je již nainstalováno ale měla by být nepřidávejte znak mezery. Tato hodnota může být prázdný řetězec.

    * `targetPrefix`: Řetězec podobný řetězci `sourcePrefix` , ale pro cíl.

    * `targetTerm`: Řetězec podobný řetězci `sourceTerm` , ale pro cíl.

    * `targetSuffix`: Řetězec podobný řetězci `sourceSuffix` , ale pro cíl.

    > [!NOTE]
    > Pokud nejsou žádné příklady ve slovníku, odpověď je 200 (OK) ale `examples` seznam je prázdný seznam.

## <a name="examples"></a>Příklady

Tento příklad ukazuje, jak vyhledat příklady pro dvojici skládá z anglické termín `fly` a španělština překladu `volar`.

# <a name="curltabcurl"></a>[Curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly', 'Translation':'volar'}]"
```

---

Text odpovědi (zkrácený pro přehlednost) je:

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
