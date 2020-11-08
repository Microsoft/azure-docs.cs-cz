---
title: Použití rozhraní API pro kontrolu pravopisu Bingu
titleSuffix: Azure Cognitive Services
description: Přečtěte si o režimech Kontrola pravopisu Bingu, nastavení a dalších informacích, které se vztahují k rozhraní API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: a02d5217cb051516e11d17730f31869618a2cfb0
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369388"
---
# <a name="using-the-bing-spell-check-api"></a>Použití rozhraní API pro kontrolu pravopisu Bingu

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](https://aka.ms/cogsvcs/bingmove)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](https://aka.ms/cogsvcs/bingmigration).

V tomto článku se dozvíte, jak používat rozhraní API Bingu pro kontrolu pravopisu k provádění kontextové gramatiky a kontroly pravopisu. I když většina pravopisných kontrol spoléhá na sady pravidel založených na slovníku, nástroj pro kontrolu pravopisu Bingu využívá strojové učení a statistické překlady strojového překladu, aby poskytoval přesné a kontextové opravy. 

## <a name="spell-check-modes"></a>Režimy kontroly pravopisu

Rozhraní API podporuje dva režimy kontroly, `Proof` a `Spell`.  Příklady najdete [tady](https://azure.microsoft.com/services/cognitive-services/spell-check/).

### <a name="proof---for-documents"></a>Kontrola dokumentů 

Výchozí režim je `Proof`. Režim kontroly pravopisu `Proof` poskytuje během vytváření dokumentu nejkomplexnější kontroly, opravuje velká písmena, přidává základní interpunkci a další. Je ale k dispozici jenom pro trhy en-US (americká angličtina), es-ES (španělština) a pt-BR (portugalština). (Poznámka: Pro španělštinu a portugalštinu pouze v beta verzi.) Pro všechny ostatní trhy nastavte parametr režimu na Spell. 

> [!NOTE]
> Pokud délka textu dotazu překročí 4096, bude zkrácena na 4096 znaků a pak bude zpracována. 

### <a name="spell----for-web-searchesqueries"></a>Pravopis – vyhledávání na webu/dotazy

Režim `Spell` je agresivnější a vrací lepší výsledky hledání. Režim `Spell` najde nejvíc pravopisných chyb, ale nedokáže najít některé gramatické chyby, které režim `Proof` zachytí, například použití malých a velkých písmen nebo opakovaná slova.

> [!NOTE]
> * Maximální podporovaná délka dotazu je níže. Pokud dotaz překračuje maximální délku, dotaz a jeho výsledky se nezmění.
>    * 130 znaků pro následující kódy jazyků: EN, de, ES, FR, pl, PT, sv, ru, NL, NB, TR-tr, IT, zh, Ko. 
>    * 65 znaků pro všechny ostatní.
> * Režim pravopisu nepodporuje v dotazech hranaté závorky ( `[` a `]` ) a může způsobit nekonzistentní výsledky. Při použití režimu pravopisu je doporučujeme odebrat z vašich dotazů.

## <a name="market-setting"></a>Nastavení trhu

V žádosti by se měl zadat [kód na trhu](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#market-codes) s `mkt` parametrem dotazu. Rozhraní API bude jinak používat výchozí trh na základě IP adresy žádosti.


## <a name="http-post-and-get-support"></a>Podpora HTTP POST a GET

Rozhraní API podporuje metodu HTTP POST i HTTP GET. Kterou použijete, závisí na délce textu, který chcete kontrolovat. Pokud jsou řetězce vždy kratší než 1500 znaků, můžete použít metodu GET. Pokud ale chcete podporovat řetězce dlouhé až 10000 znaků, použijete metodu POST. Textový řetězec může obsahovat jakýkoli platný znak kódování UTF-8.

Následující příklad ukazuje požadavek POST na kontrolu pravopisu a gramatiky textového řetězce. Příklad pro úplnost obsahuje parametr dotazu [mode](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#mode) (může být vynechán, protože výchozí hodnota pro `mode` je Proof). Parametr dotazu [text](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#text) obsahuje řetězec, který má být zkontrolován.
  
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
  
Následující příklad ukazuje odpověď na předchozí požadavek. Odpověď obsahuje objekt [SpellCheck](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#spellcheck). 
  
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
  
Pole [flaggedTokens](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#flaggedtokens) obsahuje chyby pravopisu a gramatiky, které rozhraní API v řetězci [text](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#text) našlo. Pole `token` obsahuje slovo, které se má nahradit. K nalezení tokenu v řetězci `text` použijete offset se základem nula v poli `offset`. Potom nahradíte slovo na této pozici slovem v poli `suggestion`. 

Pokud pole `type` má hodnotu RepeatedToken, měli byste stále nahradit token obsahem pole `suggestion`, ale také bude pravděpodobně potřeba odebrat koncovou mezeru.

## <a name="throttling-requests"></a>Omezování požadavků

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Další kroky

- [Co je rozhraní API pro kontrolu pravopisu Bingu?](../overview.md)
- [Referenční informace k rozhraní API pro kontrolu pravopisu Bingu v7](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)