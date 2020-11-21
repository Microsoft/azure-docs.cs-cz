---
title: Metoda Translator languages
titleSuffix: Azure Cognitive Services
description: Metoda languages Získá sadu jazyků aktuálně podporovaných jinými operacemi překladatele.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: d9abd1f305e47eca743dc9274e0ec1f0a950dd6a
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021265"
---
# <a name="translator-30-languages"></a>Překladatel 3,0: jazyky

Získá sadu jazyků aktuálně podporovaných jinými operacemi překladatele. 

## <a name="request-url"></a>Adresa URL požadavku

Odeslat `GET` požadavek na:
```HTTP
https://api.cognitive.microsofttranslator.com/languages?api-version=3.0
```

## <a name="request-parameters"></a>Parametry žádosti

Parametry žádosti předané řetězci dotazu jsou:

<table width="100%">
  <th width="20%">Parametr dotazu</th>
  <th>Popis</th>
  <tr>
    <td>verze-api</td>
    <td><em>Povinný parametr</em>.<br/>Verze rozhraní API, kterou klient požaduje. Hodnota musí být `3.0` .</td>
  </tr>
  <tr>
    <td>scope</td>
    <td>*Volitelný parametr*.<br/>Čárkami oddělený seznam názvů definující skupinu jazyků, které se mají vrátit. Povolené názvy skupin jsou: `translation` `transliteration` a `dictionary` . Pokud není zadán žádný obor, budou vráceny všechny skupiny, což je ekvivalentem předání `scope=translation,transliteration,dictionary` . Chcete-li se rozhodnout, která sada podporovaných jazyků je vhodná pro váš scénář, přečtěte si popis [objektu Response](#response-body).</td>
  </tr>
</table> 

Hlavičky požadavku jsou:

<table width="100%">
  <th width="20%">Hlavičky</th>
  <th>Popis</th>
  <tr>
    <td>Accept-Language</td>
    <td>*Nepovinná Hlavička požadavku*<br/>Jazyk, který se má použít pro řetězce uživatelského rozhraní. Některá pole v odpovědi jsou názvy jazyků nebo názvů oblastí. Tento parametr použijte k definování jazyka, ve kterém jsou tyto názvy vraceny. Jazyk je určen tak, že poskytuje značku jazyka BCP 47 ve správném formátu. Použijte například hodnotu `fr` pro vyžádání názvů ve francouzštině nebo použijte hodnotu `zh-Hant` pro vyžádání názvů v tradiční čínštině.<br/>Názvy jsou k dispozici v anglickém jazyce, pokud není zadán cílový jazyk nebo pokud není lokalizace k dispozici.
    </td>
  </tr>
  <tr>
    <td>X – ClientTraceId</td>
    <td>*Nepovinná Hlavička požadavku*<br/>Identifikátor GUID generovaný klientem pro jednoznačnou identifikaci požadavku.</td>
  </tr>
</table> 

K získání prostředků jazyka není vyžadováno ověřování.

## <a name="response-body"></a>Text odpovědi

Klient používá `scope` parametr dotazu k definování skupin jazyků, které vás zajímají.

* `scope=translation` poskytuje jazyky podporované k překladu textu z jednoho jazyka do jiného jazyka.

* `scope=transliteration` poskytuje funkce pro převod textu v jednom jazyce z jednoho skriptu na jiný skript.

* `scope=dictionary` poskytuje páry jazyků, pro které `Dictionary` operace vracejí data.

Klient může načíst několik skupin současně zadáním seznamu názvů oddělených čárkami. Například `scope=translation,transliteration,dictionary` by vracely podporované jazyky pro všechny skupiny.

Úspěšná odpověď je objekt JSON, který má jednu vlastnost pro každou požadovanou skupinu:

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

* `translation` majetek

  Hodnota `translation` vlastnosti je slovník dvojic (klíč, hodnota). Každý klíč je značka jazyka BCP 47. Klíč identifikuje jazyk, pro který je možné převést text na nebo přeložit. Hodnota přidružená ke klíči je objekt JSON s vlastnostmi, které popisují jazyk:

  * `name`: Zobrazovaný název jazyka v národním prostředí, které je požadováno přes `Accept-Language` hlavičku.

  * `nativeName`: Zobrazovaný název jazyka v národním prostředí, které je pro tento jazyk nativní.

  * `dir`: Směrová orientace, která je určena `rtl` pro jazyky zprava doleva nebo `ltr` pro jazyky zleva doprava.

  Příklad:
          
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

* `transliteration` majetek

  Hodnota `transliteration` vlastnosti je slovník dvojic (klíč, hodnota). Každý klíč je značka jazyka BCP 47. Klíč identifikuje jazyk, pro který je možné převést text z jednoho skriptu na jiný skript. Hodnota přidružená ke klíči je objekt JSON s vlastnostmi, které popisují jazyk a jeho podporované skripty:

  * `name`: Zobrazovaný název jazyka v národním prostředí, které je požadováno přes `Accept-Language` hlavičku.

  * `nativeName`: Zobrazovaný název jazyka v národním prostředí, které je pro tento jazyk nativní.

  * `scripts`: Seznam skriptů pro převod. Každý prvek `scripts` seznamu má vlastnosti:

    * `code`: Kód identifikující skript.

    * `name`: Zobrazovaný název skriptu v národním prostředí, které je požadováno přes `Accept-Language` hlavičku.

    * `nativeName`: Zobrazovaný název jazyka v národním prostředí, které je pro jazyk nativní.

    * `dir`: Směrová orientace, která je určena `rtl` pro jazyky zprava doleva nebo `ltr` pro jazyky zleva doprava.

    * `toScripts`: Seznam skriptů dostupných pro převod textu na. Každý prvek `toScripts` seznamu má vlastnosti,, `code` `name` `nativeName` a `dir` jak je popsáno výše.

  Příklad:

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

* `dictionary` majetek

  Hodnota `dictionary` vlastnosti je slovník dvojic (klíč, hodnota). Každý klíč je značka jazyka BCP 47. Klíč identifikuje jazyk, pro který jsou k dispozici alternativní překlady a back-překlady. Hodnota je objekt JSON, který popisuje zdrojový jazyk a cílové jazyky s dostupnými překlady:

  * `name`: Zobrazovaný název zdrojového jazyka v národním prostředí, které je požadováno přes `Accept-Language` hlavičku.

  * `nativeName`: Zobrazovaný název jazyka v národním prostředí, které je pro tento jazyk nativní.

  * `dir`: Směrová orientace, která je určena `rtl` pro jazyky zprava doleva nebo `ltr` pro jazyky zleva doprava.

  * `translations`: Seznam jazyků s překlady alterative a příklady pro dotaz vyjádřený ve zdrojovém jazyce. Každý prvek `translations` seznamu má vlastnosti:

    * `name`: Zobrazovaný název cílového jazyka v národním prostředí, které je požadováno přes `Accept-Language` hlavičku.

    * `nativeName`: Zobrazovaný název cílového jazyka v národním prostředí Native pro cílový jazyk.

    * `dir`: Směrová orientace, která je určena `rtl` pro jazyky zprava doleva nebo `ltr` pro jazyky zleva doprava.
    
    * `code`: Kód jazyka identifikující cílový jazyk.

  Příklad:

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

Struktura objektu Response se nemění bez změny ve verzi rozhraní API. Pro stejnou verzi rozhraní API se seznam dostupných jazyků může v průběhu času měnit, protože Microsoft Translator průběžně rozšiřuje seznam jazyků podporovaných jeho službami.

Seznam podporovaných jazyků se často nemění. Aby se ušetřila šířka pásma sítě a vylepšila odezva, měla by klientská aplikace zvážit ukládání jazykových prostředků do mezipaměti a odpovídající značku entity ( `ETag` ). Klientská aplikace se pak může pravidelně (například každých 24 hodin) dotazovat služby a načíst nejnovější sadu podporovaných jazyků. Předáním aktuální `ETag` hodnoty v `If-None-Match` poli záhlaví umožníte službě optimalizovat odpověď. Pokud prostředek nebyl upraven, služba vrátí stavový kód 304 a prázdné tělo odpovědi.

## <a name="response-headers"></a>Hlavičky odpovědi

<table width="100%">
  <th width="20%">Hlavičky</th>
  <th>Popis</th>
  <tr>
    <td>Značk</td>
    <td>Aktuální hodnota značky entity pro požadované skupiny podporovaných jazyků. Aby bylo možné následné žádosti zefektivnit, může klient odeslat `ETag` hodnotu v `If-None-Match` poli záhlaví.
    </td>
  </tr>
  <tr>
    <td>X-RequestId</td>
    <td>Hodnota, kterou služba vygenerovala k identifikaci požadavku. Používá se pro účely řešení potíží.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Stavové kódy odpovědí

Níže jsou uvedené možné stavové kódy HTTP, které požadavek vrátí. 

<table width="100%">
  <th width="20%">Stavový kód</th>
  <th>Popis</th>
  <tr>
    <td>200</td>
    <td>Úspěch.</td>
  </tr>
  <tr>
    <td>304</td>
    <td>Prostředek se od verze určeného hlavičkou požadavků nezměnil `If-None-Match` .</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Jeden z parametrů dotazu chybí nebo je neplatný. Před opakováním pokusu proveďte správné parametry žádosti.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Server odmítl požadavek, protože klient překročil omezení požadavků.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Došlo k neočekávané chybě. Pokud chyba přetrvává, ohlaste ji pomocí: datum a čas selhání, identifikátor požadavku z hlavičky odpovědi `X-RequestId` a identifikátor klienta z hlavičky požadavku `X-ClientTraceId` .</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Server je dočasně nedostupný. Opakujte požadavek. Pokud chyba přetrvává, ohlaste ji pomocí: datum a čas selhání, identifikátor požadavku z hlavičky odpovědi `X-RequestId` a identifikátor klienta z hlavičky požadavku `X-ClientTraceId` .</td>
  </tr>
</table> 

Pokud dojde k chybě, požadavek vrátí také odpověď na chybu JSON. Kód chyby je číslo na 6 číslic, ve kterém se kombinují stavový kód HTTP s kódem, za nímž následuje 3 číslice a další kategorizace chyby. Běžné kódy chyb najdete na [referenční stránce překladatele V3](./v3-0-reference.md#errors). 

## <a name="examples"></a>Příklady

Následující příklad ukazuje, jak načíst jazyky podporované pro překlad textu.

```curl
curl "https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation"
```