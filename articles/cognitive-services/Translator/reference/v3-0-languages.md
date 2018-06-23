---
title: V případě metody textová API jazyky Microsoft překladač | Microsoft Docs
description: Použijte metodu Microsoft překladač Text API jazyky.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 93c06218a560faf439f05903438d021b372ce257
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343289"
---
# <a name="text-api-30-languages"></a>Text API 3.0: jazyky

Získá sadu jazyky jiné operace rozhraní API Text v současné době podporuje. 

## <a name="request-url"></a>Adresa URL požadavku

Odesílání `GET` požadavek na:
```HTTP
https://api.cognitive.microsofttranslator.com/languages?api-version=3.0
```

## <a name="request-parameters"></a>Parametry žádosti

Žádosti jsou parametry předané na řetězec dotazu:

<table width="100%">
  <th width="20%">Parametr dotazu</th>
  <th>Popis</th>
  <tr>
    <td>verze rozhraní API.</td>
    <td>*Požadovaný parametr*.<br/>Verze rozhraní API požadovaná klientem. Hodnota musí být `3.0`.</td>
  </tr>
  <tr>
    <td>scope</td>
    <td>*Volitelný parametr*.<br/>Seznam názvy definování skupiny jazyky vrátit oddělených čárkami. Povolené jsou názvy skupin: `translation`, `transliteration` a `dictionary`. Pokud je zadaný žádný obor, pak jsou vráceny všechny skupiny, což je totéž jako předávání `scope=translation,transliteration,dictionary`. Rozhodnout, která sada podporované jazyky je vhodné pro váš scénář, viz popis [objektu odpovědi](#response-body).</td>
  </tr>
</table> 

Hlavičky žádosti jsou:

<table width="100%">
  <th width="20%">Záhlaví</th>
  <th>Popis</th>
  <tr>
    <td>Přijměte jazyk</td>
    <td>*Hlavička požadavku volitelné*.<br/>Jazyk pro použití řetězců uživatelského rozhraní. Některá pole v odpovědi jsou názvy jazyků a oblastí. Tento parametr použijte k definování jazyk, ve kterém jsou vráceny tyto názvy. Jazyk je určena poskytnutím značku ve správném formátu BCP 47 jazyk. Například použijte hodnotu `fr` požadovat názvy ve francouzštině, nebo použijte hodnotu `zh-Hant` na žádost o názvy v tradiční čínštině.<br/>Názvy jsou uvedeny v anglickém jazyce, když není zadaný cílový jazyk nebo když lokalizace není k dispozici.
    </td>
  </tr>
  <tr>
    <td>X ClientTraceId</td>
    <td>*Hlavička požadavku volitelné*.<br/>GUID klientem generovaná k jednoznačné identifikaci požadavku.</td>
  </tr>
</table> 

Ověřování není potřeba získat jazyk prostředky.

## <a name="response-body"></a>Text odpovědi

Klient používá `scope` parametr k definování skupiny jazyků, které je zájmu v dotazu.

* `scope=translation` poskytuje jazyky podporované přeložit text z jednoho jazyka na jiný jazyk;

* `scope=transliteration` poskytuje možnosti pro převod textu v jednom jazyce z jednoho skriptu na jiném skriptu;

* `scope=dictionary` poskytuje dvojice jazyků pro kterou `Dictionary` operations vrátit data.

Klient může načíst několika skupin současně zadáním čárkami oddělený seznam názvů. Například `scope=translation,transliteration,dictionary` by vrátit podporované jazyky pro všechny skupiny.

Úspěšná odpověď je objekt JSON s jednu vlastnost pro každou požadovanou skupinu:

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

  Hodnota `translation` vlastnost je slovník (klíč a hodnotu) páry. Každý klíč je značku BCP 47 jazyk. Klíč identifikuje jazyk, pro kterou text převedeny na nebo z přeložit. Hodnota přidružená klíč je objekt JSON s vlastnostmi, které popisují jazyka:

  * `name`: Zobrazovaný název jazyka, který v národním prostředí požadovaný prostřednictvím `Accept-Language` záhlaví.

  * `nativeName`: Zobrazovaný název jazyka pro nativní prostředí pro tento jazyk.

  * `dir`: Orientaci, což je `rtl` jazyků zprava doleva nebo `ltr` jazyků zleva doprava.

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

  Hodnota `transliteration` vlastnost je slovník (klíč a hodnotu) páry. Každý klíč je značku BCP 47 jazyk. Klíč identifikuje jazyk, pro který možné převést text z jednoho skriptu na jiném skriptu. Hodnota přidružená klíč je objekt JSON s vlastnostmi, které popisují jazyk a jeho podporovaných skriptů:

  * `name`: Zobrazovaný název jazyka, který v národním prostředí požadovaný prostřednictvím `Accept-Language` záhlaví.

  * `nativeName`: Zobrazovaný název jazyka pro nativní prostředí pro tento jazyk.

  * `scripts`: Seznam skripty převést z. Každý prvek `scripts` seznamu má vlastnosti:

    * `code`: Kód identifikující skript.

    * `name`: Zobrazovaný název skriptu v národním prostředí požadovaný prostřednictvím `Accept-Language` záhlaví.

    * `nativeName`: Zobrazovaný název jazyka pro nativní prostředí pro jazyk.

    * `dir`: Orientaci, což je `rtl` jazyků zprava doleva nebo `ltr` jazyků zleva doprava.

    * `toScripts`: Seznam skripty, které jsou k dispozici pro převod textu na. Každý element `toScripts` seznamu má vlastnosti `code`, `name`, `nativeName`, a `dir` jak bylo popsáno výše.

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

  Hodnota `dictionary` vlastnost je slovník (klíč a hodnotu) páry. Každý klíč je značku BCP 47 jazyk. Klíč identifikuje jazyk, pro které jsou k dispozici alternativní překlady a back překladů. Hodnota je objekt JSON, který popisuje jazyk zdrojového a cílového jazyků s překlady k dispozici:

  * `name`: Zobrazovaný název jazyka zdroje v národním prostředí požadovaný prostřednictvím `Accept-Language` záhlaví.

  * `nativeName`: Zobrazovaný název jazyka pro nativní prostředí pro tento jazyk.

  * `dir`: Orientaci, což je `rtl` jazyků zprava doleva nebo `ltr` jazyků zleva doprava.

  * `translations`: Seznam jazyků s v.42 překlady a příklady pro dotaz vyjádřené v jazyce zdroje. Každý prvek `translations` seznamu má vlastnosti:

    * `name`: Název zobrazení jazyka cíl v národním prostředí požadovaný prostřednictvím `Accept-Language` záhlaví.

    * `nativeName`: Zobrazovaný název pro cílový jazyk cílový jazyk v nativní národního prostředí.

    * `dir`: Orientaci, což je `rtl` jazyků zprava doleva nebo `ltr` jazyků zleva doprava.
    
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

Struktura objektu odpovědi nedojde ke změně bez změn v této verzi rozhraní API. Pro stejnou verzi rozhraní API seznam dostupných jazyků v průběhu času mění protože Microsoft Translator průběžně rozšiřuje seznam jazyků podporovaných jeho služby.

Seznam podporovaných jazyků nedojde ke změně často. Šetřit šířku pásma sítě a zlepšit odezvu, klientská aplikace zvažte ukládání do mezipaměti jazyk zdrojů a odpovídající značky entity (`ETag`). Potom klientská aplikace může pravidelně (například každých 24 hodin) zadat dotaz na službu načíst nejnovější sadu podporovaných jazyků. Předávání aktuální `ETag` hodnotu `If-None-Match` pole hlavičky povolí službu, kterou chcete optimalizovat odpovědi. Pokud prostředek byl změněn, službu vrátí stavový kód 304 a text prázdnou odpověď.

## <a name="response-headers"></a>Hlavičky odpovědi

<table width="100%">
  <th width="20%">Záhlaví</th>
  <th>Popis</th>
  <tr>
    <td>ETag</td>
    <td>Aktuální hodnota značky entity pro požadované skupiny podporované jazyky. Chcete-li následné žádosti efektivnější, může odeslat klientovi `ETag` hodnotu `If-None-Match` pole hlavičky.
    </td>
  </tr>
  <tr>
    <td>X-RequestId</td>
    <td>Hodnota vygenerovaná službu tak, aby svou žádost identifikovat. Používá se pro účely odstraňování potíží.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Kódy stavu odpovědi

Níže jsou uvedeny možné stavové kódy HTTP, které vrací žádost. 

<table width="100%">
  <th width="20%">Stavový kód</th>
  <th>Popis</th>
  <tr>
    <td>200</td>
    <td>Úspěch</td>
  </tr>
  <tr>
    <td>304</td>
    <td>Prostředek se nezměnil od verze zadaná v hlavičky požadavku `If-None-Match`.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Jeden z parametrů dotazu nebyl nalezen nebo není platný. Před opakováním opravte parametry žádosti.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Volající odesílá příliš mnoho požadavků.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Došlo k neočekávané chybě. Pokud chyba přetrvává, nahlaste to s: datum a čas selhání identifikátor žádosti z hlavičky odpovědi `X-RequestId`a identifikátor klienta z hlavičky požadavku `X-ClientTraceId`.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Server není dočasně k dispozici. Opakujte žádost. Pokud chyba přetrvává, nahlaste to s: datum a čas selhání identifikátor žádosti z hlavičky odpovědi `X-RequestId`a identifikátor klienta z hlavičky požadavku `X-ClientTraceId`.</td>
  </tr>
</table> 

## <a name="examples"></a>Příklady

Následující příklad ukazuje, jak načíst jazyky podporované pro překlad textu.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl "https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation"
```

---
