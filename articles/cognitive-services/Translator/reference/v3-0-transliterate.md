---
title: Metoda přepisu překladatelů
titleSuffix: Azure Cognitive Services
description: Převod textu v jednom jazyce z jednoho skriptu na jiný skript s metodou překladatelů pro přepis.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: lajanuar
ms.openlocfilehash: 0d5a7f8df0c722ca52780ba254e9af9608f26b54
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98895420"
---
# <a name="translator-30-transliterate"></a>Překladatel 3,0: přepis

Převede text v jednom jazyce z jednoho skriptu na jiný skript.

## <a name="request-url"></a>Adresa URL požadavku

Odeslat `POST` požadavek na:

```HTTP
https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0
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
  <tr>
    <td>language</td>
    <td>*Povinný parametr*.<br/>Určuje jazyk textu, který má být převeden z jednoho skriptu na jiný. Možné jazyky jsou uvedené v oboru, který je `transliteration` získaný dotazem na službu pro [podporované jazyky](./v3-0-languages.md).</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td>*Povinný parametr*.<br/>Určuje skript používaný vstupním textem. Vyhledat [podporované jazyky](./v3-0-languages.md) pomocí `transliteration` oboru, aby bylo možné najít vstupní skripty dostupné pro vybraný jazyk.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td>*Povinný parametr*.<br/>Určuje výstupní skript. Vyhledejte [podporované jazyky](./v3-0-languages.md) pomocí `transliteration` oboru, abyste našli výstupní skripty, které jsou dostupné pro vybranou kombinaci vstupního jazyka a vstupního skriptu.</td>
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

Tělo požadavku je pole JSON. Každý prvek pole je objekt JSON s vlastností řetězce s názvem `Text` , který představuje řetězec, který má být převeden.

```json
[
    {"Text":"こんにちは"},
    {"Text":"さようなら"}
]
```

Platí následující omezení:

* Pole může mít maximálně 10 prvků.
* Textová hodnota prvku pole nesmí být delší než 1 000 znaků včetně mezer.
* Celý text zahrnutý v požadavku nesmí být delší než 5 000 znaků včetně mezer.

## <a name="response-body"></a>Text odpovědi

Úspěšná odpověď je pole JSON s jedním výsledkem pro každý prvek ve vstupním poli. Objekt výsledku obsahuje následující vlastnosti:

  * `text`: Řetězec, který je výsledkem převodu vstupního řetězce do výstupního skriptu.
  
  * `script`: Řetězec určující skript používaný ve výstupu.

Příklad odpovědi JSON:

```json
[
    {"text":"konnnichiha","script":"Latn"},
    {"text":"sayounara","script":"Latn"}
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

Následující příklad ukazuje, jak převést dva japonské řetězce na římské japonské.

Datová část JSON pro požadavek v tomto příkladu:

```json
[{"text":"こんにちは","script":"jpan"},{"text":"さようなら","script":"jpan"}]
```

Pokud používáte kudrlinkou v okně příkazového řádku, které nepodporuje znaky Unicode, použijte následující datovou část JSON a uložte ji do souboru s názvem `request.txt` . Nezapomeňte soubor uložit s `UTF-8` kódováním.

```
curl -X POST "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=Jpan&toScript=Latn" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d @request.txt
```