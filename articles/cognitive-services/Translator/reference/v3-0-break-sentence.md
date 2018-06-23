---
title: V případě metody textová rozhraní API BreakSentence Microsoft překladač | Microsoft Docs
description: Použijte Microsoft překladač Text rozhraní API BreakSentence metodu.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 8ce6644d21b397ea0e7f2e71e3c3a5a96638eec5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343564"
---
# <a name="text-api-30-breaksentence"></a>Text API 3.0: BreakSentence

Určuje umístění větu hranice část textu.

## <a name="request-url"></a>Adresa URL požadavku

Odesílání `POST` požadavek na:

```HTTP
https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0
```

## <a name="request-parameters"></a>Parametry žádosti

Žádosti jsou parametry předané na řetězec dotazu:

<table width="100%">
  <th width="20%">Parametr dotazu</th>
  <th>Popis</th>
  <tr>
    <td>verze rozhraní API.</td>
    <td>*Požadovaný parametr dotazu*.<br/>Verze rozhraní API požadovaná klientem. Hodnota musí být `3.0`.</td>
  </tr>
  <tr>
    <td>jazyk</td>
    <td>*Parametr dotazu volitelné*.<br/>Značka jazyka identifikace jazyk ze vstupního textu. Pokud není zadán kód, budou použity automatické zjišťování jazyka.</td>
  </tr>
  <tr>
    <td>skript</td>
    <td>*Parametr dotazu volitelné*.<br/>Značky Script identifikace skript používá vstupního textu. Pokud skript není zadán, bude předpokládat výchozí skript jazyka.</td>
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

Text žádosti je pole JSON. Každý element pole je objekt JSON s řetězec vlastnost s názvem `Text`. Hranice větu se vypočítávají pro hodnotu `Text` vlastnost. Ukázkový obsah žádosti s jednu část textu vypadá jako je například:

```json
[
    { "Text": "How are you? I am fine. What did you do today?" }
]
```

Platí následující omezení:

* Pole může mít maximálně 100 elementy.
* Hodnota textového elementu pole nesmí překročit 10 000 znaků včetně mezer.
* Celý text zahrnuté v požadavku nesmí překročit 50 000 znaků včetně mezer.
* Pokud `language` je zadán parametr dotazu, pak všechny elementy pole musí být ve stejném jazyce. Jinak automatické rozpoznávání jazyka se použije pro každý element pole nezávisle.

## <a name="response-body"></a>Text odpovědi

Úspěšná odpověď je pole JSON s jeden výsledek pro každý řetězec v vstupního pole. Výsledný objekt zahrnuje následující vlastnosti:

  * `sentLen`: Pole celá čísla odpovídající délky vět v textu elementu. Délka pole je číslo vět a hodnoty jsou délka jednotlivé věty. 

  * `detectedLanguage`: Objekt popisující zjištěný jazyk prostřednictvím následujících vlastností:

     * `language`: Kód zjištěného jazyka.

     * `score`Hodnota: float označující spolehlivosti ve výsledku. Je skóre mezi 0 a 1 a nízké skóre označuje nízkou spolehlivosti.
     
    Všimněte si, že `detectedLanguage` vlastnost je přítomen v objektu výsledků, pouze pokud se požaduje automatické rozpoznávání jazyka.

Je například odpověď JSON:

```json
[
  {
    "sentenceLengths": [ 13, 11, 22 ]
    "detectedLanguage": {
      "language": "en",
      "score": 401
    },
  }
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

Následující příklad ukazuje, jak získat větu hranice pro jeden věty. Jazyk věty je automaticky zjišťován pomocí služby.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'How are you? I am fine. What did you do today?'}]"
```

---

