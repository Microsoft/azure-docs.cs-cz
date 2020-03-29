---
title: Metoda jazyků textového rozhraní API překladače
titleSuffix: Azure Cognitive Services
description: Metoda Languages získá sadu jazyků, které jsou aktuálně podporovány jinými operacemi rozhraní Translator Text API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: 37f70399e8125db559098869cdfffdf4533498d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73835834"
---
# <a name="translator-text-api-30-languages"></a>Překladač Text API 3.0: Jazyky

Získá sadu jazyků aktuálně podporovaných jinými operacemi překladače text rozhraní API. 

## <a name="request-url"></a>Adresa URL požadavku

Odeslání `GET` žádosti na:
```HTTP
https://api.cognitive.microsofttranslator.com/languages?api-version=3.0
```

## <a name="request-parameters"></a>Parametry požadavku

Parametry požadavku předané v řetězci dotazu jsou:

<table width="100%">
  <th width="20%">Parametr dotazu</th>
  <th>Popis</th>
  <tr>
    <td>verze-api</td>
    <td><em>Povinný parametr</em>.<br/>Verze rozhraní API požadované klientem. Hodnota musí `3.0`být .</td>
  </tr>
  <tr>
    <td>scope</td>
    <td>*Volitelný parametr*.<br/>Seznam názvů oddělených čárkami definující skupinu jazyků, které mají být vráceny. Povolené názvy `translation`skupin `transliteration` `dictionary`jsou: a . Pokud není uveden žádný obor, jsou vráceny všechny `scope=translation,transliteration,dictionary`skupiny, což je ekvivalentní předávání . Chcete-li se rozhodnout, která sada podporovaných jazyků je pro váš scénář vhodná, naleznete v popisu [objektu odpovědi](#response-body).</td>
  </tr>
</table> 

Hlavičky požadavku jsou:

<table width="100%">
  <th width="20%">Hlavičky</th>
  <th>Popis</th>
  <tr>
    <td>Accept-Language</td>
    <td>*Volitelná hlavička požadavku*.<br/>Jazyk, který se má použít pro řetězce uživatelského rozhraní. Některá pole v odpovědi jsou názvy jazyků nebo názvy oblastí. Tento parametr slouží k definování jazyka, ve kterém jsou tyto názvy vráceny. Jazyk je určen poskytnutím dobře formátované značky jazyka BCP 47. Například použijte hodnotu `fr` k vyžádání názvů ve `zh-Hant` francouzštině nebo použijte hodnotu k vyžádání názvů v tradiční čínštině.<br/>Názvy jsou k dispozici v anglickém jazyce, pokud není zadán cílový jazyk nebo pokud není k dispozici lokalizace.
    </td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Volitelná hlavička požadavku*.<br/>Identifikátor GUID generovaný klientem k jednoznačné identifikaci požadavku.</td>
  </tr>
</table> 

Ověřování není nutné k získání jazykových prostředků.

## <a name="response-body"></a>Text odpovědi

Klient používá `scope` parametr dotazu k definování skupin jazyků, které ho zajímají.

* `scope=translation`poskytuje jazyky podporované pro překlad textu z jednoho jazyka do jiného;

* `scope=transliteration`poskytuje možnosti pro převod textu v jednom jazyce z jednoho skriptu do jiného skriptu;

* `scope=dictionary`poskytuje dvojice jazyků, `Dictionary` pro které operace vrátí data.

Klient může načíst několik skupin současně zadáním seznamu názvů oddělených čárkami. Například `scope=translation,transliteration,dictionary` vrátí podporované jazyky pro všechny skupiny.

Úspěšná odpověď je objekt JSON s jednou vlastností pro každou požadovanou skupinu:

```json
{
    "translation": {
        //... set of languages supported to translate text (scope=translation)
    },
    "transliteration": {
        //... set of languages supported to convert between scripts (scope=transliteration)
    },
    "dictionary": {
        //... set of languages supported for alternative translations and examples (scope=dictionary)
    }
}
```

Hodnota pro každou vlastnost je následující.

* `translation`Vlastnost

  Hodnota vlastnosti `translation` je slovník (klíč, hodnota) párů. Každý klíč je jazyková značka BCP 47. Klíč identifikuje jazyk, pro který lze text přeložit nebo z kterého lze přeložit. Hodnota přidružená ke klíči je objekt JSON s vlastnostmi, které popisují jazyk:

  * `name`: Zobrazovaný název jazyka v národním `Accept-Language` prostředí požadovaném prostřednictvím záhlaví.

  * `nativeName`: Zobrazit název jazyka v nativním národním prostředí pro tento jazyk.

  * `dir`: Směrovost, `rtl` která je pro jazyky se zbytkem doleva nebo `ltr` pro jazyky se zbytkem doleva.

  Příkladem je:
          
  ```json
  {
    "translation": {
      ...
      "fr": {
        "name": "French",
        "nativeName": "Français",
        "dir": "ltr"
      },
      ...
    }
  }
  ```

* `transliteration`Vlastnost

  Hodnota vlastnosti `transliteration` je slovník (klíč, hodnota) párů. Každý klíč je jazyková značka BCP 47. Klíč identifikuje jazyk, pro který lze text převést z jednoho skriptu na jiný. Hodnota přidružená ke klíči je objekt JSON s vlastnostmi, které popisují jazyk a jeho podporované skripty:

  * `name`: Zobrazovaný název jazyka v národním `Accept-Language` prostředí požadovaném prostřednictvím záhlaví.

  * `nativeName`: Zobrazit název jazyka v nativním národním prostředí pro tento jazyk.

  * `scripts`: Seznam skriptů, ze kterých se má převést. Každý prvek `scripts` seznamu má vlastnosti:

    * `code`: Kód identifikující skript.

    * `name`: Zobrazovaný název skriptu v `Accept-Language` národním prostředí požadovaném prostřednictvím záhlaví.

    * `nativeName`: Zobrazit název jazyka v národním prostředí nativní pro jazyk.

    * `dir`: Směrovost, `rtl` která je pro jazyky se zbytkem doleva nebo `ltr` pro jazyky se zbytkem doleva.

    * `toScripts`: Seznam skriptů, na které je k dispozici převod textu. Každý prvek `toScripts` seznamu má `code` `name`vlastnosti , , `nativeName`, a `dir` jak je popsáno výše.

  Příkladem je:

  ```json
  {
    "transliteration": {
      ...
      "ja": {
        "name": "Japanese",
        "nativeName": "日本語",
        "scripts": [
          {
            "code": "Jpan",
            "name": "Japanese",
            "nativeName": "日本語",
            "dir": "ltr",
            "toScripts": [
              {
                "code": "Latn",
                "name": "Latin",
                "nativeName": "ラテン語",
                "dir": "ltr"
              }
            ]
          },
          {
            "code": "Latn",
            "name": "Latin",
            "nativeName": "ラテン語",
            "dir": "ltr",
            "toScripts": [
              {
                "code": "Jpan",
                "name": "Japanese",
                "nativeName": "日本語",
                "dir": "ltr"
              }
            ]
          }
        ]
      },
      ...
    }
  }
  ```

* `dictionary`Vlastnost

  Hodnota vlastnosti `dictionary` je slovník (klíč, hodnota) párů. Každý klíč je jazyková značka BCP 47. Klíč identifikuje jazyk, pro který jsou k dispozici alternativní překlady a zpětné překlady. Hodnota je objekt JSON, který popisuje zdrojový jazyk a cílové jazyky s dostupnými překlady:

  * `name`: Zobrazovaný název zdrojového jazyka v `Accept-Language` národním prostředí požadovaném prostřednictvím záhlaví.

  * `nativeName`: Zobrazit název jazyka v nativním národním prostředí pro tento jazyk.

  * `dir`: Směrovost, `rtl` která je pro jazyky se zbytkem doleva nebo `ltr` pro jazyky se zbytkem doleva.

  * `translations`: Seznam jazyků se alterativem a příklady dotazu vyjádřené ve zdrojovém jazyce. Každý prvek `translations` seznamu má vlastnosti:

    * `name`: Zobrazovaný název cílového jazyka v `Accept-Language` národním prostředí požadovaném prostřednictvím záhlaví.

    * `nativeName`: Zobrazit název cílového jazyka v nativním národním prostředí pro cílový jazyk.

    * `dir`: Směrovost, `rtl` která je pro jazyky se zbytkem doleva nebo `ltr` pro jazyky se zbytkem doleva.
    
    * `code`: Kód jazyka identifikující cílový jazyk.

  Příkladem je:

  ```json
  "es": {
    "name": "Spanish",
    "nativeName": "Español",
    "dir": "ltr",
    "translations": [
      {
        "name": "English",
        "nativeName": "English",
        "dir": "ltr",
        "code": "en"
      }
    ]
  },
  ```

Struktura objektu odpovědi se nezmění bez změny ve verzi rozhraní API. Pro stejnou verzi rozhraní API seznam dostupných jazyků se může v průběhu času měnit, protože aplikace Microsoft Translator neustále rozšiřuje seznam jazyků podporovaných jeho službami.

Seznam podporovaných jazyků se často nemění. Chcete-li ušetřit šířku pásma sítě a zlepšit odezvu, měla by`ETag`klientská aplikace zvážit ukládání prostředků jazyka do mezipaměti a odpovídající značky entity ( ). Potom klientská aplikace může pravidelně (například jednou za 24 hodin) dotaz služby načíst nejnovější sadu podporovaných jazyků. Předání aktuální `ETag` hodnoty `If-None-Match` v poli záhlaví umožní službě optimalizovat odpověď. Pokud prostředek nebyl změněn, služba vrátí stavový kód 304 a prázdné tělo odpovědi.

## <a name="response-headers"></a>Hlavičky odpovědi

<table width="100%">
  <th width="20%">Hlavičky</th>
  <th>Popis</th>
  <tr>
    <td>Etag</td>
    <td>Aktuální hodnota značky entity pro požadované skupiny podporovaných jazyků. Chcete-li zefektivnění následných požadavků, `ETag` může `If-None-Match` klient odeslat hodnotu v poli záhlaví.
    </td>
  </tr>
  <tr>
    <td>X-RequestId</td>
    <td>Hodnota generovaná službou k identifikaci požadavku. Používá se pro účely řešení potíží.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Stavové kódy odpovědi

Následují možné stavové kódy HTTP, které požadavek vrátí. 

<table width="100%">
  <th width="20%">Stavový kód</th>
  <th>Popis</th>
  <tr>
    <td>200</td>
    <td>Úspěch</td>
  </tr>
  <tr>
    <td>304</td>
    <td>Prostředek nebyl změněn od verze určené hlavičkami `If-None-Match`požadavku .</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Jeden z parametrů dotazu chybí nebo je neplatný. Před opakováním opravte parametry požadavku.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Server požadavek zamítl, protože klient překročil limity požadavků.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Došlo k neočekávané chybě. Pokud chyba přetrvává, oznamte ji s: datem a `X-RequestId`časem selhání, `X-ClientTraceId`identifikátorem požadavku z hlavičky odpovědi a identifikátorem klienta z hlavičky požadavku .</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Server je dočasně nedostupný. Opakujte požadavek. Pokud chyba přetrvává, oznamte ji s: datem a `X-RequestId`časem selhání, `X-ClientTraceId`identifikátorem požadavku z hlavičky odpovědi a identifikátorem klienta z hlavičky požadavku .</td>
  </tr>
</table> 

Pokud dojde k chybě, požadavek také vrátí odpověď na chybu JSON. Kód chyby je šestimístné číslo kombinující 3místný stavový kód HTTP následovaný třímístným číslem pro další kategorizaci chyby. Běžné kódy chyb naleznete na [referenční stránce v3 Translator Text API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors). 

## <a name="examples"></a>Příklady

Následující příklad ukazuje, jak načíst jazyky podporované pro překlad textu.

```curl
curl "https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation"
```
