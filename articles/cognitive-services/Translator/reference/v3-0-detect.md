---
title: Translator Text API rozpoznat – metoda
titlesuffix: Azure Cognitive Services
description: Použijte metodu zjišťování Translator Text API.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: v-jansko
ms.openlocfilehash: 57b82bf65ccf002173df90fe19db444d1c087905
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55857885"
---
# <a name="translator-text-api-30-detect"></a>Translator Text API 3.0: Detect

Určuje jazyk část textu.

## <a name="request-url"></a>Adresa URL požadavku

Odeslat `POST` požadavek na:

```HTTP
https://api.cognitive.microsofttranslator.com/detect?api-version=3.0
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
</table> 

Hlavičky žádosti patří:

<table width="100%">
  <th width="20%">Hlavičky</th>
  <th>Popis</th>
  <tr>
    <td>_Jedním autorizačním_<br/>_header_</td>
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
    <td>*Volitelné*.<br/>Klientem generovaná identifikátor GUID k jednoznačné identifikaci požadavku. Všimněte si, že můžete tuto hlavičku vynechat, pokud zahrnují ID trasování v řetězci dotazu pomocí parametru dotazu s názvem `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Text požadavku

Text požadavku je pole JSON. Každý prvek pole je objekt JSON s řetězcovou vlastnost s názvem `Text`. Rozpoznávání jazyka se použije pro hodnotu `Text` vlastnost. Text požadavku ukázka vypadá tímto způsobem:

```json
[
    { "Text": "Ich würde wirklich gern Ihr Auto um den Block fahren ein paar Mal." }
]
```

Platí následující omezení:

* Pole může mít maximálně 100 elementů.
* Textová hodnota elementu pole nemůže být delší než 10 000 znaků včetně mezer.
* Celý text zahrnutý v požadavku nemůže být delší než 50 000 znaků včetně mezer.

## <a name="response-body"></a>Text odpovědi

Úspěšná odpověď je pole JSON se jeden výsledek pro každý řetězec vstupního pole. Výsledný objekt zahrnuje následující vlastnosti:

  * `language`: Kód zjištěný jazyk.

  * `score`: Hodnoty typu float označující důvěru ve výsledky. Je skóre mezi 0 a 1 a nízké skóre označuje s nízkou spolehlivostí.

  * `isTranslationSupported`: Logická hodnota, která má hodnotu true, pokud se zjištěný jazyk je jedním z jazyků pro překlady textů nepodporuje.

  * `isTransliterationSupported`: Logická hodnota, která má hodnotu true, pokud se zjištěný jazyk je jedním z jazyků pro transkripci nepodporuje.
  
  * `alternatives`: Pole jazyků je to možné. Každý prvek pole je jiný objekt se stejnými vlastnostmi výše uvedených: `language`, `score`, `isTranslationSupported` a `isTransliterationSupported`.

Je například odpověď JSON:

```json
[
  {
    "language": "de",
    "score": 0.92,
    "isTranslationSupported": true,
    "isTransliterationSupported": false,
    "alternatives": [
      {
        "language": "pt",
        "score": 0.23,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      },
      {
        "language": "sk",
        "score": 0.23,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      }
    ]
  }
]
```

## <a name="response-headers"></a>Hlavičky odpovědi

<table width="100%">
  <th width="20%">Hlavičky</th>
  <th>Popis</th>
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
    <td>400</td>
    <td>Jeden z parametrů dotazu je chybí nebo není platný. Opravte parametry požadavku než to zkusíte znovu.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Žádost nešlo ověřit. Zkontrolujte, zda jsou pověření zadaná a je platný.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>Požadavek není autorizovaný. Najdete podrobnosti o chybové zprávě. To často určuje, že se využilo všechny bezplatné překlady zkušebního předplatného k dispozici.</td>
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

Pokud dojde k chybě, vrátí požadavek také chybová odpověď JSON. Kód chyby je, chybu zařadit 6místným číselným číslo kombinování stavový kód HTTP 3 číslice následované číslem 3 číslice na další. Běžné kódy chyb můžete najít na [stránky referenční dokumentace rozhraní Translator Text API v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors). 

## <a name="examples"></a>Příklady

Následující příklad ukazuje, jak načíst jazyky podporované pro překlad textu.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/detect?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'What language is this text written in?'}]"
```

---
