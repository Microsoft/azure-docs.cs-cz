---
title: Překladatel text API transgramrate metoda
titleSuffix: Azure Cognitive Services
description: Převést text v jednom jazyce z jednoho skriptu do jiného pomocí metody Translator Text API Transliterate.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: e6bb1541b2b668796b352bebc68d59b4ade143e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837284"
---
# <a name="translator-text-api-30-transliterate"></a>Překladatel Text API 3.0: Transliterate

Převede text v jednom jazyce z jednoho skriptu na jiný.

## <a name="request-url"></a>Adresa URL požadavku

Odeslání `POST` žádosti na:

```HTTP
https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0
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
  <tr>
    <td>language</td>
    <td>*Povinný parametr*.<br/>Určuje jazyk textu, který má být převeden z jednoho skriptu do druhého. Možné jazyky jsou `transliteration` uvedeny v oboru získaném dotazováním na službu pro podporované [jazyky](./v3-0-languages.md).</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td>*Povinný parametr*.<br/>Určuje skript používaný vstupním textem. Vyhledejte [podporované jazyky](./v3-0-languages.md) pomocí `transliteration` oboru a vyhledejte vstupní skripty dostupné pro vybraný jazyk.</td>
  </tr>
  <tr>
    <td>doscriptu</td>
    <td>*Povinný parametr*.<br/>Určuje výstupní skript. Vyhledejte [podporované jazyky](./v3-0-languages.md) pomocí `transliteration` oboru a vyhledejte výstupní skripty, které jsou k dispozici pro vybranou kombinaci vstupního jazyka a vstupního skriptu.</td>
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

Tělo požadavku je pole JSON. Každý prvek pole je objekt JSON `Text`s vlastností string s názvem , která představuje řetězec převést.

```json
[
    {"Text":"こんにちは"},
    {"Text":"さようなら"}
]
```

Platí následující omezení:

* Pole může mít maximálně 10 prvků.
* Textová hodnota prvku pole nesmí přesáhnout 1 000 znaků včetně mezer.
* Celý text obsažený v požadavku nesmí přesáhnout 5 000 znaků včetně mezer.

## <a name="response-body"></a>Text odpovědi

Úspěšná odpověď je pole JSON s jedním výsledkem pro každý prvek ve vstupním poli. Výsledný objekt obsahuje následující vlastnosti:

  * `text`: Řetězec, který je výsledkem převodu vstupního řetězce na výstupní skript.
  
  * `script`: Řetězec určující skript použitý ve výstupu.

Příklad odpovědi JSON je:

```json
[
    {"text":"konnnichiha","script":"Latn"},
    {"text":"sayounara","script":"Latn"}
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

Následující příklad ukazuje, jak převést dva japonské řetězce na romskou japonštinu.

Datová část JSON pro požadavek v tomto příkladu:

```json
[{"text":"こんにちは","script":"jpan"},{"text":"さようなら","script":"jpan"}]
```

Pokud používáte cURL v okně příkazového řádku, které nepodporuje znaky Unicode, vezměte následující datovou `request.txt`část JSON a uložte ji do souboru s názvem . Nezapomeňte soubor uložit s `UTF-8` kódováním.

```
curl -X POST "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=Jpan&toScript=Latn" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d @request.txt
```
