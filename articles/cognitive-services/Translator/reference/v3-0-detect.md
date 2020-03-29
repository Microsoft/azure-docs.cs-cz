---
title: Metoda detekce textového rozhraní překladače
titleSuffix: Azure Cognitive Services
description: Identifikujte jazyk části textu pomocí metody Detekce překladače překladačů Azure Cognitive Services.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: 370f3b14c12fc05f181d6497b7069bbf1cf3c9cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837296"
---
# <a name="translator-text-api-30-detect"></a>Překladač Text API 3.0: Detekce

Identifikuje jazyk části textu.

## <a name="request-url"></a>Adresa URL požadavku

Odeslání `POST` žádosti na:

```HTTP
https://api.cognitive.microsofttranslator.com/detect?api-version=3.0
```

## <a name="request-parameters"></a>Parametry požadavku

Parametry požadavku předané v řetězci dotazu jsou:

<table width="100%">
  <th width="20%">Parametr dotazu</th>
  <th>Popis</th>
  <tr>
    <td>verze-api</td>
    <td>*Povinný parametr*.<br/>Verze rozhraní API požadované klientem. Hodnota musí `3.0`být .</td>
  </tr>
</table> 

Mezi hlavičky požadavku patří:

<table width="100%">
  <th width="20%">Hlavičky</th>
  <th>Popis</th>
  <tr>
    <td>Autentizační hlavička (záhlaví)</td>
    <td><em>Hlavička požadavku .</em><br/>Viz <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">dostupné možnosti ověřování</a>.</td>
  </tr>
  <tr>
    <td>Typ obsahu</td>
    <td>*Hlavička požadavku .*<br/>Určuje typ obsahu datové části. Možné hodnoty `application/json`jsou: .</td>
  </tr>
  <tr>
    <td>Délka obsahu</td>
    <td>*Hlavička požadavku .*<br/>Délka těla požadavku.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Nepovinné*.<br/>Identifikátor GUID generovaný klientem k jednoznačné identifikaci požadavku. Všimněte si, že můžete vynechat toto záhlaví, pokud zahrnete `ClientTraceId`ID trasování v řetězci dotazu pomocí parametru dotazu s názvem .</td>
  </tr>
</table> 

## <a name="request-body"></a>Text požadavku

Tělo požadavku je pole JSON. Každý prvek pole je objekt JSON `Text`s vlastností string s názvem . Detekce jazyka se použije `Text` na hodnotu vlastnosti. Tělo požadavku vzorku vypadá takto:

```json
[
    { "Text": "Ich würde wirklich gern Ihr Auto um den Block fahren ein paar Mal." }
]
```

Platí následující omezení:

* Pole může mít maximálně 100 prvků.
* Textová hodnota prvku pole nesmí přesáhnout 10 000 znaků včetně mezer.
* Celý text obsažený v požadavku nesmí přesáhnout 50 000 znaků včetně mezer.

## <a name="response-body"></a>Text odpovědi

Úspěšná odpověď je pole JSON s jedním výsledkem pro každý řetězec ve vstupním poli. Výsledný objekt obsahuje následující vlastnosti:

  * `language`: Kód detekovaného jazyka.

  * `score`: Hodnota plovoucího signálu označující důvěru ve výsledek. Skóre je mezi nulou a jedním a nízké skóre označuje nízkou důvěru.

  * `isTranslationSupported`: Logická hodnota, která je pravdivá, pokud je zjištěný jazyk jedním z jazyků podporovaných pro překlad textu.

  * `isTransliterationSupported`: Logická hodnota, která je true, pokud je zjištěný jazyk jedním z jazyků podporovaných pro přepis.
  
  * `alternatives`: Řada dalších možných jazyků. Každý prvek pole je jiný objekt se stejnými `score` `isTranslationSupported` vlastnostmi uvedenými výše: `isTransliterationSupported` `language`, , a .

Příklad odpovědi JSON je:

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
    <td>400</td>
    <td>Jeden z parametrů dotazu chybí nebo je neplatný. Před opakováním opravte parametry požadavku.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Požadavek nelze ověřit. Zkontrolujte, zda jsou zadána a platná pověření.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>Požadavek není autorizován. Zkontrolujte chybovou zprávu podrobností. To často znamená, že všechny bezplatné překlady poskytované zkušební předplatné byly vypovězeny.</td>
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
curl -X POST "https://api.cognitive.microsofttranslator.com/detect?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'What language is this text written in?'}]"
```
