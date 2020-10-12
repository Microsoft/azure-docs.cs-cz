---
title: Metoda BreakSentence překladatele
titleSuffix: Azure Cognitive Services
description: Metoda BreakSentence překladatele určuje umístění hranic věty v části textu.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 08/06/2020
ms.author: swmachan
ms.openlocfilehash: 340b0ed02821fb98f271539ac39e0ccad8581082
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87903999"
---
# <a name="translator-30-breaksentence"></a>Překladatel 3,0: BreakSentence

Určuje umístění vět v části textu.

## <a name="request-url"></a>Adresa URL požadavku

Odeslat `POST` požadavek na:

```HTTP
https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0
```

## <a name="request-parameters"></a>Parametry žádosti

Parametry žádosti předané řetězci dotazu jsou:

| Parametr dotazu | Description |
| -------| ----------- |
| verze-api <img width=200/>   | **Požadovaný parametr dotazu**.<br/>Verze rozhraní API, kterou klient požaduje. Hodnota musí být `3.0` . |
| language | **Volitelný parametr dotazu**.<br/>Značka jazyka identifikující jazyk vstupního textu. Pokud není zadán kód, použije se automatické rozpoznávání jazyka. |
| script    | **Volitelný parametr dotazu**.<br/>Značka skriptu identifikující skript používaný vstupním textem Pokud není zadán skript, předpokládá se výchozí skript jazyka.  | 

Hlavičky požadavku zahrnují:

| Hlavičky | Description |
| ------- | ----------- |
| Ověřovací hlavičky (y) <img width=200/>  | **Požadovaná hlavička žádosti**<br/>Podívejte se <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">na dostupné možnosti ověřování</a>. |
| Typ obsahu | **Požadovaná hlavička žádosti**<br/>Určuje typ obsahu datové části. Možné hodnoty jsou: `application/json` . |
| Délka obsahu    | **Požadovaná hlavička žádosti**<br/>Délka textu žádosti  | 
| X – ClientTraceId   | **Volitelné**.<br/>Identifikátor GUID generovaný klientem pro jednoznačnou identifikaci požadavku. Všimněte si, že tuto hlavičku můžete vynechat, pokud zahrnete ID trasování do řetězce dotazu pomocí parametru dotazu s názvem `ClientTraceId` .  | 

## <a name="request-body"></a>Text požadavku

Tělo požadavku je pole JSON. Každý prvek pole je objekt JSON s vlastností řetězce s názvem `Text` . Pro hodnotu vlastnosti jsou vypočítány hranice věty `Text` . Vzorový text žádosti s jedním textem vypadá nějak takto:

```json
[
    { "Text": "How are you? I am fine. What did you do today?" }
]
```

Platí následující omezení:

* Pole může mít maximálně 100 prvků.
* Textová hodnota prvku pole nesmí být delší než 50 000 znaků včetně mezer.
* Celý text zahrnutý v požadavku nesmí být delší než 50 000 znaků včetně mezer.
* `language`Je-li zadán parametr dotazu, všechny prvky pole musí být ve stejném jazyce. V opačném případě se automatické zjišťování jazyka aplikuje na každý prvek pole nezávisle.

## <a name="response-body"></a>Text odpovědi

Úspěšná odpověď je pole JSON s jedním výsledkem pro každý řetězec ve vstupním poli. Objekt výsledku obsahuje následující vlastnosti:

  * `sentLen`: Pole celých čísel představující délky vět v textovém elementu. Délka pole je počet vět a hodnoty jsou délkou jednotlivých vět. 

  * `detectedLanguage`: Objekt popisující zjištěný jazyk prostřednictvím následujících vlastností:

     * `language`: Kód zjištěného jazyka.

     * `score`: Hodnota typu float označující důvěru ve výsledku. Skóre je v rozsahu 0 až 1 a nízké skóre indikuje nízkou důvěru.
     
    Všimněte si, že `detectedLanguage` vlastnost je přítomna pouze v objektu výsledek, pokud je požadováno automatické zjišťování jazyka.

Příklad odpovědi JSON:

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

Pokud dojde k chybě, požadavek vrátí také odpověď na chybu JSON. Kód chyby je číslo na 6 číslic, ve kterém se kombinují stavový kód HTTP s kódem, za nímž následuje 3 číslice a další kategorizace chyby. Běžné kódy chyb najdete na [referenční stránce překladatele V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors). 

## <a name="examples"></a>Příklady

Následující příklad ukazuje, jak získat hranice věty pro jednu větu. Služba automaticky detekuje jazyk této věty.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'How are you? I am fine. What did you do today?'}]"
```

