---
title: Použití rozhraní API pro kontrolu pravopisu Bingu
titlesuffix: Azure Cognitive Services
description: Další informace o režimů pro kontrolu pravopisu Bingu, nastavení a další informace týkající se rozhraní API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: overview
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: 9544337ef1322e52cbdf123bb48d283485a8c7dd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60896134"
---
# <a name="using-the-bing-spell-check-api"></a>Použití rozhraní API pro kontrolu pravopisu Bingu

Další informace o použití rozhraní API Bingu pro kontrolu pravopisu zkontrolujte kontextové gramatiky a kontrola pravopisu pomocí tohoto článku. Zatímco většina pravopisu využívají sady pravidel na základě slovníku, kontrola pravopisu Bingu využívá strojové učení a statistické strojový překlad poskytnout přesné a kontextové opravy. 

## <a name="spell-check-modes"></a>Režimy kontroly pravopisu

Rozhraní API podporuje dva režimy kontroly, `Proof` a `Spell`.  Příklady najdete [tady](https://azure.microsoft.com/services/cognitive-services/spell-check/).

### <a name="proof---for-documents"></a>Testování – dokumenty 

Výchozí režim je `Proof`. Režim kontroly pravopisu `Proof` poskytuje během vytváření dokumentu nejkomplexnější kontroly, opravuje velká písmena, přidává základní interpunkci a další. Je ale k dispozici jenom pro trhy en-US (americká angličtina), es-ES (španělština) a pt-BR (portugalština). (Poznámka: Pro španělštinu a portugalštinu pouze v beta verzi.) Pro všechny ostatní trhy nastavte parametr režimu na Spell. 

> [!NOTE]
> Pokud délka textu dotazu je větší než 4096, bude zkrácen na 4 096 znaků, pak zpracovat. 

### <a name="spell----for-web-searchesqueries"></a>Kontroly pravopisu – pro webové vyhledávání a dotazy

Režim `Spell` je agresivnější a vrací lepší výsledky hledání. Režim `Spell` najde nejvíc pravopisných chyb, ale nedokáže najít některé gramatické chyby, které režim `Proof` zachytí, například použití malých a velkých písmen nebo opakovaná slova.

> [!NOTE]
> * Dotaz maximální podporovaná délka je menší než. Pokud dotaz překračuje maximální délku, dotaz a jeho výsledky se nezmění.
>    * 130 znaků pro následující kódy jazyků: en, de, es, fr, pl, pt, sv, ru, nl, nb, tr-tr, ho, zh, ko. 
>    * 65 znaků pro všechny ostatní.
> * Režim pravopisu nepodporuje hranaté závorky (`[` a `]`) v dotazech a může způsobit nekonzistentní výsledky. Doporučujeme, abyste je odeberete z vašich dotazů při použití režimu pravopisu.

## <a name="market-setting"></a>Nastavení trhu

A [uvedení na trh kód](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#market-codes) by měl být zadaný pomocí `mkt` parametr v žádosti o dotazu. Rozhraní API bude používat jinak na trhu výchozí na základě požadavku IP adresy.


## <a name="http-post-and-get-support"></a>Podpora HTTP POST a GET

Rozhraní API podporuje metodu HTTP POST i HTTP GET. Kterou použijete, závisí na délce textu, který chcete kontrolovat. Pokud jsou řetězce vždy kratší než 1500 znaků, můžete použít metodu GET. Pokud ale chcete podporovat řetězce dlouhé až 10000 znaků, použijete metodu POST. Textový řetězec může obsahovat jakýkoli platný znak kódování UTF-8.

Následující příklad ukazuje požadavek POST na kontrolu pravopisu a gramatiky textového řetězce. Příklad pro úplnost obsahuje parametr dotazu [mode](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#mode) (může být vynechán, protože výchozí hodnota pro `mode` je Proof). Parametr dotazu [text](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#text) obsahuje řetězec, který má být zkontrolován.
  
```  
POST https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?mode=proof&mkt=en-us HTTP/1.1  
Content-Type: application/x-www-form-urlencoded  
Content-Length: 47  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
 
text=when+its+your+turn+turn,+john,+come+runing  
``` 

Pokud používáte metodu HTTP GET, zahrnete parametr dotazu `text` do řetězce dotazu adresy URL.
  
Následující příklad ukazuje odpověď na předchozí požadavek. Odpověď obsahuje objekt [SpellCheck](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#spellcheck). 
  
```json
{  
    "_type" : "SpellCheck",  
    "flaggedTokens" : [{  
        "offset" : 5,  
        "token" : "its",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "it's",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 25,  
        "token" : "john",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "John",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 19,  
        "token" : "turn",  
        "type" : "RepeatedToken",  
        "suggestions" : [{  
            "suggestion" : "",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 35,  
        "token" : "runing",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "running",  
            "score" : 1  
        }]  
    }]  
}  
```  
  
Pole [flaggedTokens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#flaggedtokens) obsahuje chyby pravopisu a gramatiky, které rozhraní API v řetězci [text](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#text) našlo. Pole `token` obsahuje slovo, které se má nahradit. K nalezení tokenu v řetězci `text` použijete offset se základem nula v poli `offset`. Potom nahradíte slovo na této pozici slovem v poli `suggestion`. 

Pokud pole `type` má hodnotu RepeatedToken, měli byste stále nahradit token obsahem pole `suggestion`, ale také bude pravděpodobně potřeba odebrat koncovou mezeru.

## <a name="throttling-requests"></a>Omezování požadavků

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Další postup

- [Co je API kontrola pravopisu Bingu?](../overview.md)
- [Referenční informace k rozhraní API pro kontrolu pravopisu Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
