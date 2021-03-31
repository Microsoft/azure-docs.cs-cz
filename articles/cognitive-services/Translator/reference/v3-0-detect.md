---
title: Metoda detekce překladatele
titleSuffix: Azure Cognitive Services
description: Identifikujte jazyk části textu pomocí metody zjišťování překladatele Azure Cognitive Services.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: lajanuar
ms.openlocfilehash: cb6660585b5f2b9ab56eaf863f1ec431e5e85109
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "98895522"
---
# <a name="translator-30-detect"></a>Překladatel 3,0: zjištění

Určuje jazyk části textu.

## <a name="request-url"></a>Adresa URL požadavku

Odeslat `POST` požadavek na:

```HTTP
https://api.cognitive.microsofttranslator.com/detect?api-version=3.0
```

## <a name="request-parameters"></a>Parametry žádosti

Parametry žádosti předané řetězci dotazu jsou:

<table width="100%">
  <th width="20%">Parametr dotazu</th>
  <th>Popis</th>
  <tr>
    <td>verze-api</td>
    <td>*Povinný parametr*.<br/>Verze rozhraní API, kterou klient požaduje. Hodnota musí být `3.0` .</td>
  </tr>
</table> 

Hlavičky požadavku zahrnují:

<table width="100%">
  <th width="20%">Hlavičky</th>
  <th>Description</th>
  <tr>
    <td>Ověřovací hlavičky (y)</td>
    <td><em>Požadovaná hlavička žádosti</em><br/>Podívejte se <a href="/azure/cognitive-services/translator/reference/v3-0-reference#authentication">na dostupné možnosti ověřování</a>.</td>
  </tr>
  <tr>
    <td>Typ obsahu</td>
    <td>*Požadovaná hlavička žádosti*<br/>Určuje typ obsahu datové části. Možné hodnoty jsou: `application/json` .</td>
  </tr>
  <tr>
    <td>Délka obsahu</td>
    <td>*Požadovaná hlavička žádosti*<br/>Délka textu žádosti</td>
  </tr>
  <tr>
    <td>X – ClientTraceId</td>
    <td>*Volitelné*.<br/>Identifikátor GUID generovaný klientem pro jednoznačnou identifikaci požadavku. Všimněte si, že tuto hlavičku můžete vynechat, pokud zahrnete ID trasování do řetězce dotazu pomocí parametru dotazu s názvem `ClientTraceId` .</td>
  </tr>
</table> 

## <a name="request-body"></a>Text požadavku

Tělo požadavku je pole JSON. Každý prvek pole je objekt JSON s vlastností řetězce s názvem `Text` . Rozpoznávání jazyka je použito pro hodnotu `Text` Vlastnosti. Automatické zjišťování jazyka funguje lépe s delším vstupním textem. Vzorový text žádosti vypadá nějak takto:

```json
[
    { "Text": "Ich würde wirklich gern Ihr Auto um den Block fahren ein paar Mal." }
]
```

Platí následující omezení:

* Pole může mít maximálně 100 prvků.
* Celý text zahrnutý v požadavku nesmí být delší než 50 000 znaků včetně mezer.

## <a name="response-body"></a>Text odpovědi

Úspěšná odpověď je pole JSON s jedním výsledkem pro každý řetězec ve vstupním poli. Objekt výsledku obsahuje následující vlastnosti:

  * `language`: Kód zjištěného jazyka.

  * `score`: Hodnota typu float označující důvěru ve výsledku. Skóre je v rozsahu 0 až 1 a nízké skóre indikuje nízkou důvěru.

  * `isTranslationSupported`: Logická hodnota, která má hodnotu true, pokud zjištěný jazyk je jedním z jazyků podporovaných pro překlad textu.

  * `isTransliterationSupported`: Logická hodnota, která má hodnotu true, pokud zjištěný jazyk je jedním z jazyků podporovaných pro účely přepočtu.
  
  * `alternatives`: Pole dalších možných jazyků. Každý prvek pole je další objekt se stejnými vlastnostmi, které jsou uvedeny výše `language` : `score` , `isTranslationSupported` a `isTransliterationSupported` .

Příklad odpovědi JSON:

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
  <th>Description</th>
  <tr>
    <td>X-RequestId</td>
    <td>Hodnota, kterou služba vygenerovala k identifikaci požadavku. Používá se pro účely řešení potíží.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Stavové kódy odpovědí

Níže jsou uvedené možné stavové kódy HTTP, které požadavek vrátí. 

<table width="100%">
  <th width="20%">Stavový kód</th>
  <th>Description</th>
  <tr>
    <td>200</td>
    <td>Úspěch.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Jeden z parametrů dotazu chybí nebo je neplatný. Před opakováním pokusu proveďte správné parametry žádosti.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Požadavek nebylo možné ověřit. Ověřte, jestli jsou přihlašovací údaje zadané a platné.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>Požadavek není autorizovaný. Podívejte se na podrobnosti chybové zprávy. To často znamená, že se využívaly všechny bezplatné překlady, které jsou součástí zkušebního předplatného.</td>
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
curl -X POST "https://api.cognitive.microsofttranslator.com/detect?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'What language is this text written in?'}]"
```