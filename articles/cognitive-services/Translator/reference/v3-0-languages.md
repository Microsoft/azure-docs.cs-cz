---
title: Translator Text API jazyků – metoda
titlesuffix: Azure Cognitive Services
description: Použijte metodu Translator Text API jazyků.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 51f15bd9c75f24be0d477d10de55c93a51cfbf3f
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129637"
---
# <a name="translator-text-api-30-languages"></a>Translator Text API 3.0: jazyků

Získá sadu jazyků aktuálně podporuje jiné operace rozhraní Translator Text API. 

## <a name="request-url"></a>Adresa URL požadavku

Odeslat `GET` požadavek na:
```HTTP
https://api.cognitive.microsofttranslator.com/languages?api-version=3.0
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
    <td>scope</td>
    <td>*Volitelný parametr*.<br/>Čárkou oddělený seznam názvů definování skupin jazyků k vrácení. Povolené jsou názvy skupin: `translation`, `transliteration` a `dictionary`. Pokud je zadaný žádný obor, pak jsou vráceny všechny skupiny, což je ekvivalentní k předávání `scope=translation,transliteration,dictionary`. Při rozhodování, která sada podporovaných jazyků je vhodné pro váš scénář, viz popis [objektu odpovědi](#response-body).</td>
  </tr>
</table> 

Hlavičky žádosti jsou:

<table width="100%">
  <th width="20%">Hlavičky</th>
  <th>Popis</th>
  <tr>
    <td>Přijměte jazyka</td>
    <td>*Hlavička požadavku volitelné*.<br/>Jazyk, který se má použít pro uživatelské rozhraní řetězce. Některá pole v odpovědi jsou názvy jazyků nebo názvy oblastí. Použijte tento parametr se definuje jazyk, ve kterém jsou vráceny tyto názvy. Jazyk je zadán zadáním značky jazyka ve správném formátu BCP 47. Například použijte hodnotu `fr` žádosti názvy ve francouzštině, nebo použijte hodnotu `zh-Hant` požadavek názvy v tradiční čínštině.<br/>Názvy jsou poskytovány v angličtině, když není zadaný cílový jazyk, nebo pokud lokalizace není k dispozici.
    </td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Hlavička požadavku volitelné*.<br/>Klientem generovaná identifikátor GUID k jednoznačné identifikaci požadavku.</td>
  </tr>
</table> 

Chcete-li získat jazykové prostředky se vyžaduje ověření.

## <a name="response-body"></a>Text odpovědi

Klient použije `scope` parametr k definování skupiny jazyky, které ho zajímají dotazu.

* `scope=translation` poskytuje pro převod textu z jednoho jazyka na jiný jazyk; podporované jazyky

* `scope=transliteration` poskytuje funkce pro převod textu v jednom jazyce z jednoho skriptu na jiný skript;

* `scope=dictionary` poskytuje dvojice jazyků pro kterou `Dictionary` operace vrátit data.

Klient může načíst několika skupin současně zadat čárkou oddělený seznam názvů. Například `scope=translation,transliteration,dictionary` vracel podporované jazyky pro všechny skupiny.

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

Hodnota pro každou vlastnost je následujícím způsobem.

* `translation` Vlastnost

  Hodnota `translation` slovník (klíče, hodnota) je vlastnost dvojice. Každý klíč je značka jazyka BCP 47. Klíč identifikuje jazyk pro kterou může přeložit na text nebo přeložit z. Hodnotu přiřazenou klíči je objekt JSON s vlastnostmi, které popisují jazyk:

  * `name`: Zobrazovaný název jazyka v národním prostředí požadovaný prostřednictvím `Accept-Language` záhlaví.

  * `nativeName`: Zobrazí název jazyka v nativní pro tento jazyk národního prostředí.

  * `dir`: Text, který je `rtl` pro jazyky zprava doleva nebo `ltr` jazyků zleva doprava.

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

* `transliteration` Vlastnost

  Hodnota `transliteration` slovník (klíče, hodnota) je vlastnost dvojice. Každý klíč je značka jazyka BCP 47. Klíč identifikuje jazyk, pro kterou je možné převést text z jednoho skriptu na jiný skript. Hodnotu přiřazenou klíči je objekt JSON s vlastnostmi, které popisují jazyka a jeho podporované skriptů:

  * `name`: Zobrazovaný název jazyka v národním prostředí požadovaný prostřednictvím `Accept-Language` záhlaví.

  * `nativeName`: Zobrazí název jazyka v nativní pro tento jazyk národního prostředí.

  * `scripts`: Seznam skripty pro převod z. Každý prvek `scripts` má seznam vlastností:

    * `code`: Kód identifikující skriptu.

    * `name`: Zobrazovaný název souboru, který v národním prostředí požadovaný prostřednictvím `Accept-Language` záhlaví.

    * `nativeName`: Zobrazí název jazyka v nativní pro jazyk národního prostředí.

    * `dir`: Text, který je `rtl` pro jazyky zprava doleva nebo `ltr` jazyků zleva doprava.

    * `toScripts`: Seznam, které nejsou k dispozici pro převod textu na skripty. Každý prvek `toScripts` seznamu má vlastnosti `code`, `name`, `nativeName`, a `dir` jak je popsáno výše.

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

* `dictionary` Vlastnost

  Hodnota `dictionary` slovník (klíče, hodnota) je vlastnost dvojice. Každý klíč je značka jazyka BCP 47. Klíč identifikuje jazyk, pro které jsou k dispozici alternativní překlady a back překlady. Hodnota je objekt JSON, který popisuje jazyk zdrojového a cílového jazyků s překlady k dispozici:

  * `name`: Zobrazovaný název zdrojového jazyka v národním prostředí požadovaný prostřednictvím `Accept-Language` záhlaví.

  * `nativeName`: Zobrazí název jazyka v nativní pro tento jazyk národního prostředí.

  * `dir`: Text, který je `rtl` pro jazyky zprava doleva nebo `ltr` jazyků zleva doprava.

  * `translations`: Seznam jazyků s v.42 překlady a příklady pro dotaz vyjádřeny Zdrojový jazyk. Každý prvek `translations` má seznam vlastností:

    * `name`: Zobrazovaný název cílového jazyka v národním prostředí požadovaný prostřednictvím `Accept-Language` záhlaví.

    * `nativeName`: Zobrazí název cílového jazyka v nativní pro cílový jazyk národního prostředí.

    * `dir`: Text, který je `rtl` pro jazyky zprava doleva nebo `ltr` jazyků zleva doprava.
    
    * `code`: Kód jazyka identifikace cílový jazyk.

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

Struktura objektu odpovědi nedojde ke změně bez změny ve verzi rozhraní API. Pro stejnou verzi rozhraní API seznam dostupných jazyků může postupem času změnit protože Microsoft Translatoru neustále rozšiřuje seznam jazyků podporovaných jeho služeb.

Seznam podporovaných jazyků se často mění. Pokud chcete šetřit šířku pásma sítě a vylepšovat rychlost odezvy, klientská aplikace zvažte ukládání do mezipaměti jazykové prostředky a odpovídající značka entity (`ETag`). Potom může klientská aplikace pravidelně (třeba jednou za 24 hodin) dotaz službě a načíst nejnovější sadu podporované jazyky. Předání aktuální `ETag` hodnotu `If-None-Match` pole hlavičky vám umožní službě a optimalizovat odpovědi. Prostředek se nezměnil, služba vrátí stavový kód 304 a prázdné tělo odpovědi.

## <a name="response-headers"></a>Hlavičky odpovědi

<table width="100%">
  <th width="20%">Hlavičky</th>
  <th>Popis</th>
  <tr>
    <td>ETag</td>
    <td>Aktuální hodnota značka entity pro požadované skupiny podporované jazyky. Chcete-li následné žádosti více efektivní, může klient odeslat `ETag` hodnota v `If-None-Match` pole hlavičky.
    </td>
  </tr>
  <tr>
    <td>X-RequestId</td>
    <td>Hodnota vygenerované službou k identifikaci požadavku. Používá se pro účely odstraňování potíží.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Stavové kódy odezvy

Tady jsou možné stavové kódy HTTP, které vrátí žádost o. 

<table width="100%">
  <th width="20%">Stavový kód</th>
  <th>Popis</th>
  <tr>
    <td>200</td>
    <td>Úspěch</td>
  </tr>
  <tr>
    <td>304</td>
    <td>Prostředek nebyl změněn od verze určené hlavičky požadavku `If-None-Match`.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Jeden z parametrů dotazu je chybí nebo není platný. Opravte parametry požadavku než to zkusíte znovu.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Volající odesílá příliš mnoho požadavků.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Došlo k neočekávané chybě. Pokud chyba přetrvává, nahlaste to s: datum a čas selhání žádost s identifikátorem v hlavičce odpovědi `X-RequestId`a identifikátor klienta v hlavičce požadavku `X-ClientTraceId`.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Server je dočasně nedostupný. Zkuste požadavek. Pokud chyba přetrvává, nahlaste to s: datum a čas selhání žádost s identifikátorem v hlavičce odpovědi `X-RequestId`a identifikátor klienta v hlavičce požadavku `X-ClientTraceId`.</td>
  </tr>
</table> 

## <a name="examples"></a>Příklady

Následující příklad ukazuje, jak načíst jazyky podporované pro překlad textu.

# <a name="curltabcurl"></a>[Curl](#tab/curl)

```
curl "https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation"
```

---
