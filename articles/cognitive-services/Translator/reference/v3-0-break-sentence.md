---
title: Translator Text API BreakSentence – metoda
titlesuffix: Azure Cognitive Services
description: Použijte metodu Translator Text API BreakSentence.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: v-jansko
ms.openlocfilehash: 8aa726d8a00e76c1b4311140a433e6c7e476dc50
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884898"
---
# <a name="translator-text-api-30-breaksentence"></a>Translator Text API 3.0: BreakSentence

Určuje umístění hranice větu v část textu.

## <a name="request-url"></a>Adresa URL požadavku

Odeslat `POST` požadavek na:

```HTTP
https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0
```

## <a name="request-parameters"></a>Parametry žádosti

Žádosti jsou parametry předané v řetězci dotazu:

<table width="100%">
  <th width="20%">Parametr dotazu</th>
  <th>Popis</th>
  <tr>
    <td>verze API-version</td>
    <td>*Povinný parametr dotazu*.<br/>Verze rozhraní API požadovaná klientem. Hodnota musí být `3.0`.</td>
  </tr>
  <tr>
    <td>language</td>
    <td>*Parametr dotazu volitelné*.<br/>Značka jazyka, identifikace jazyka vstupního textu. Pokud není zadán kód, použijí se automatické rozpoznávání jazyka.</td>
  </tr>
  <tr>
    <td>skript</td>
    <td>*Parametr dotazu volitelné*.<br/>Značka skriptu identifikace skript, které ve vstupním textu. Pokud skript není zadán, předpokládá se výchozí skript jazyka.</td>
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

Text požadavku je pole JSON. Každý prvek pole je objekt JSON s řetězcovou vlastnost s názvem `Text`. Hranice věty se počítají pro hodnotu vlastnosti `Text` vlastnost. Ukázkový text žádosti s jednu část textu vypadá tímto způsobem:

```json
[
    { "Text": "How are you? I am fine. What did you do today?" }
]
```

Platí následující omezení:

* Pole může mít maximálně 100 elementů.
* Textová hodnota elementu pole nemůže být delší než 10 000 znaků včetně mezer.
* Celý text zahrnutý v požadavku nemůže být delší než 50 000 znaků včetně mezer.
* Pokud `language` je zadán parametr dotazu, pak všechny prvky pole musí být ve stejném jazyce. V opačném případě automatické rozpoznávání jazyka se aplikuje na každý prvek pole nezávisle na sobě.

## <a name="response-body"></a>Text odpovědi

Úspěšná odpověď je pole JSON se jeden výsledek pro každý řetězec vstupního pole. Výsledný objekt zahrnuje následující vlastnosti:

  * `sentLen`: Pole celých čísel reprezentujících délky věty v textu elementu. Délka pole je počet věty a hodnoty jsou délka jednotlivé věty. 

  * `detectedLanguage`: Objekt popisující zjištěný jazyk prostřednictvím následující vlastnosti:

     * `language`: Kód zjištěný jazyk.

     * `score`: Hodnoty typu float označující důvěru ve výsledky. Je skóre mezi 0 a 1 a nízké skóre označuje s nízkou spolehlivostí.
     
    Všimněte si, `detectedLanguage` vlastnost je k dispozici v objektu výsledků pouze pokud se požaduje automatické rozpoznávání jazyka.

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

Následující příklad ukazuje, jak získat hranice větu v jedné větě. Služba automaticky zjistí jazyk věty.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'How are you? I am fine. What did you do today?'}]"
```

---

