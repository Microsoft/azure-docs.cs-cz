---
title: Metoda breaksentence rozhraní API překladače
titleSuffix: Azure Cognitive Services
description: Metoda Translator Text API BreakSentence identifikuje umístění hranic věty v části textu.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: swmachan
ms.openlocfilehash: 4c314148b8e1495a8b5a12c42d4989d13cdd6a08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "76548114"
---
# <a name="translator-text-api-30-breaksentence"></a>Překladač Text API 3.0: BreakSentence

Identifikuje umístění hranic věty v části textu.

## <a name="request-url"></a>Adresa URL požadavku

Odeslání `POST` žádosti na:

```HTTP
https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0
```

## <a name="request-parameters"></a>Parametry požadavku

Parametry požadavku předané v řetězci dotazu jsou:

| Parametr dotazu | Popis |
| -------| ----------- |
| verze-api <img width=200/>   | **Parametr povinného dotazu**.<br/>Verze rozhraní API požadované klientem. Hodnota musí `3.0`být . |
| language | **Volitelný parametr dotazu**.<br/>Jazyková značka identifikující jazyk vstupního textu. Pokud kód není zadán, bude použito automatické zjišťování jazyka. |
| .    | **Volitelný parametr dotazu**.<br/>Značka skriptu identifikující skript používaný vstupním textem. Pokud skript není zadán, bude předpokládán výchozí skript jazyka.  | 

Mezi hlavičky požadavku patří:

| Hlavičky | Popis |
| ------- | ----------- |
| Autentizační hlavička (záhlaví) <img width=200/>  | **Hlavička požadavku .**<br/>Viz <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">dostupné možnosti ověřování</a>. |
| Typ obsahu | **Hlavička požadavku .**<br/>Určuje typ obsahu datové části. Možné hodnoty `application/json`jsou: . |
| Délka obsahu    | **Hlavička požadavku .**<br/>Délka těla požadavku.  | 
| X-ClientTraceId   | **Nepovinné**.<br/>Identifikátor GUID generovaný klientem k jednoznačné identifikaci požadavku. Všimněte si, že můžete vynechat toto záhlaví, pokud zahrnete `ClientTraceId`ID trasování v řetězci dotazu pomocí parametru dotazu s názvem .  | 

## <a name="request-body"></a>Text požadavku

Tělo požadavku je pole JSON. Každý prvek pole je objekt JSON `Text`s vlastností string s názvem . Hranice věty jsou vypočítány `Text` pro hodnotu vlastnosti. Tělo ukázkové žádosti s jedním kusem textu vypadá takto:

```json
[
    { "Text": "How are you? I am fine. What did you do today?" }
]
```

Platí následující omezení:

* Pole může mít maximálně 100 prvků.
* Textová hodnota prvku pole nesmí přesáhnout 10 000 znaků včetně mezer.
* Celý text obsažený v požadavku nesmí přesáhnout 50 000 znaků včetně mezer.
* Pokud `language` je zadán parametr dotazu, musí být všechny prvky pole ve stejném jazyce. V opačném případě je automatické zjišťování jazyka použito pro každý prvek pole nezávisle.

## <a name="response-body"></a>Text odpovědi

Úspěšná odpověď je pole JSON s jedním výsledkem pro každý řetězec ve vstupním poli. Výsledný objekt obsahuje následující vlastnosti:

  * `sentLen`: Pole celá čísla představující délky vět v textovém prvku. Délka pole je počet vět a hodnoty jsou délka každé věty. 

  * `detectedLanguage`: Objekt popisující zjištěný jazyk prostřednictvím následujících vlastností:

     * `language`: Kód detekovaného jazyka.

     * `score`: Hodnota plovoucího signálu označující důvěru ve výsledek. Skóre je mezi nulou a jedním a nízké skóre označuje nízkou důvěru.
     
    Všimněte `detectedLanguage` si, že vlastnost je k dispozici pouze v objektu výsledek, pokud je požadováno automatické zjišťování jazyka.

Příklad odpovědi JSON je:

```json
[
  {
    "sentLen": [ 13, 11, 22 ]
    "detectedLanguage": {
      "language": "en",
      "score": 401
    },
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

Následující příklad ukazuje, jak získat hranice věty pro jednu větu. Jazyk věty je automaticky detekován službou.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'How are you? I am fine. What did you do today?'}]"
```

