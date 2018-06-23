---
title: Překladač Microsoft Text rozhraní API Transliterate metoda | Microsoft Docs
description: Použijte metodu Transliterate Microsoft překladač Text rozhraní API.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: fdd6fa9236f0c02685198b6de3228c444993dad6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343560"
---
# <a name="text-api-30-transliterate"></a>Text API 3.0: Transliterate

Převede text v jednom jazyce z jednoho skriptu na jiném skriptu.

## <a name="request-url"></a>Adresa URL požadavku

Odesílání `POST` požadavek na:

```HTTP
https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0
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
    <td>jazyk</td>
    <td>*Požadovaný parametr*.<br/>Určuje jazyk textu převést z jednoho skriptu. Možné jazyky jsou uvedeny v `transliteration` oboru získat dotazováním služby pro jeho [podporované jazyky](.\v3-0-languages.md).</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td>*Požadovaný parametr*.<br/>Určuje skript používá vstupního textu. Vyhledávání [podporované jazyky](.\v3-0-languages.md) pomocí `transliteration` oboru, najít vstupní skripty, které jsou dostupné pro vybraný jazyk.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td>*Požadovaný parametr*.<br/>Určuje výstupní skript. Vyhledávání [podporované jazyky](.\v3-0-languages.md) pomocí `transliteration` oboru, najít výstup skripty, které jsou k dispozici pro vybrané kombinace jazyka a vstupní skript.</td>
  </tr>
</table> 

Hlavičky požadavku zahrnují:

<table width="100%">
  <th width="20%">Záhlaví</th>
  <th>Popis</th>
  <tr>
    <td>_Jedním autorizačním_<br/>_Záhlaví_</td>
    <td>*Hlavička požadavku požadované*.<br/>V tématu [dostupné možnosti pro ověřování](./v3-0-reference.md#authentication).</td>
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
    <td>X ClientTraceId</td>
    <td>*Volitelné*.<br/>GUID klientem generovaná k jednoznačné identifikaci požadavku. Poznámka: tuto hlavičku je možné vynechat, pokud zahrnete ID trasování v řetězci dotazu pomocí parametru dotazu s názvem `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Tělo požadavku

Text žádosti je pole JSON. Každý element pole je objekt JSON s řetězec vlastnost s názvem `Text`, která představuje řetězec k převedení.

```json
[
    {"Text":"こんにちは"},
    {"Text":"さようなら"}
]
```

Platí následující omezení:

* Pole může obsahovat maximálně 10 elementy.
* Hodnota textového elementu pole nesmí překročit 1 000 znaků včetně mezer.
* Celý text zahrnuté v požadavku nesmí překročit 5 000 znaků včetně mezer.

## <a name="response-body"></a>Text odpovědi

Úspěšná odpověď je pole JSON s jeden výsledek pro každý prvek ve vstupní pole. Výsledný objekt zahrnuje následující vlastnosti:

  * `text`: Řetězec, který je výsledkem převod vstupní řetězec do výstupní skript.
  
  * `script`: Řetězec určující skriptu použít ve výstupu.

Je například odpověď JSON:

```json
[
    {"text":"konnnichiha","script":"Latn"},
    {"text":"sayounara","script":"Latn"}
]
```

## <a name="response-headers"></a>Hlavičky odpovědi

<table width="100%">
  <th width="20%">Záhlaví</th>
  <th>Popis</th>
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
    <td>400</td>
    <td>Jeden z parametrů dotazu nebyl nalezen nebo není platný. Před opakováním opravte parametry žádosti.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Žádost nešlo ověřit. Zkontrolujte, zda jsou přihlašovací údaje zadané a platné.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>Požadavek není ověřen. Zkontrolujte podrobnosti chybové zprávy. Často to znamená, že všechny volné překlady součástí zkušební předplatné vyčerpání.</td>
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

Následující příklad ukazuje, jak převést dvě japonské řetězce na Romanized japonské.

# <a name="curltabcurl"></a>[curl](#tab/curl)

Datová část JSON pro požadavek v tomto příkladu:

```
[{"text":"こんにちは","script":"jpan"},{"text":"さようなら","script":"jpan"}]
```

Pokud používáte cUrl v okně příkazového řádku, který nepodporuje znaky Unicode, proveďte následující datové části JSON a uložte ho do souboru s názvem `request.txt`. Uložte soubor s `UTF-8` kódování.

```
curl -X POST "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=Jpan&toScript=Latn" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d @request.txt
```

---
