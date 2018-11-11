---
title: Co je rozhraní API Bingu pro kontrolu pravopisu?
titlesuffix: Azure Cognitive Services
description: Rozhraní API Bingu pro kontrolu pravopisu využívá strojové učení a statistický strojový překlad pro kontextovou kontrolu pravopisu.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: overview
ms.date: 05/03/2018
ms.author: nolachar
ms.openlocfilehash: 48d9dee014d0759bd339c79811bb7b2fddecfe0b
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50214932"
---
# <a name="what-is-bing-spell-check-api"></a>Co je rozhraní API Bingu pro kontrolu pravopisu?

Rozhraní API Bingu pro kontrolu pravopisu umožňuje provést kontextovou kontrolu gramatiky a pravopisu.

Jaký je rozdíl mezi normální kontrolou pravopisu a kontrolou pravopisu Bingu třetí generace? Aktuální kontroly pravopisu se spoléhají na ověření pravopisu a gramatiky proti sadě slovníkových pravidel, které jsou periodicky aktualizovány a rozšiřovány. Jinými slovy je kontrola pravopisu silná pouze jako slovník, který ji podporuje, a redakční pracovníci, kteří podporují slovník. Tento typ kontroly pravopisu znáte z Microsoft Wordu a dalších textových editorů.

Naproti tomu Bing vyvinul webovou kontrolu pravopisu, která využívá strojové učení a statistický strojový překlad, aby dynamicky trénoval neustále se rozvíjející a vysoce kontextový algoritmus. Kontrola pravopisu je založená na rozsáhlém korpusu webových hledání a dokumentů.

Tato kontrola pravopisu zvládne jakýkoli scénář zpracování textu:

- Rozpoznává slang a neformální jazyk.
- Rozpoznává v kontextu běžné chyby v názvech.
- Opravuje nesprávné rozdělení slov pomocí jediného příznaku.
- Je schopná opravovat homonyma v kontextu a další obtížně odhalitelné chyby.
- Podporuje nové značky, termíny digitální zábavy a oblíbené výrazy od samého počátku.
- Slova, která zní stejně, ale liší se významem a pravopisem, například „see“ a „sea“.

## <a name="spell-check-modes"></a>Režimy kontroly pravopisu

Rozhraní API podporuje dva režimy kontroly, `Proof` a `Spell`.  Příklady najdete [tady](https://azure.microsoft.com/services/cognitive-services/spell-check/).
### <a name="proof---for-documents-scenario"></a>Proof – pro scénář s dokumenty
Výchozí režim je `Proof`. Režim kontroly pravopisu `Proof` poskytuje během vytváření dokumentu nejkomplexnější kontroly, opravuje velká písmena, přidává základní interpunkci a další. Je ale k dispozici jenom pro trhy en-US (americká angličtina), es-ES (španělština) a pt-BR (portugalština). (Poznámka: Pro španělštinu a portugalštinu pouze v beta verzi.) Pro všechny ostatní trhy nastavte parametr režimu na Spell. 
<br /><br/>**Poznámka:** Pokud délka textu dotazu přesáhne 4096 znaků, bude před zpracováním zkrácena na 4096 znaků. 
### <a name="spell----for-web-searchesqueries-scenario"></a>Spell –  pro scénář webových hledání a dotazů
Režim `Spell` je agresivnější a vrací lepší výsledky hledání. Režim `Spell` najde nejvíc pravopisných chyb, ale nedokáže najít některé gramatické chyby, které režim `Proof` zachytí, například použití malých a velkých písmen nebo opakovaná slova.
<br /></br>**Poznámka:** Maximální podporovaná délka dotazu je následující. Pokud dotaz překročí maximální délku, dotaz ani jeho výsledky se nezmění.
<ul><li>130 znaků pro kód jazyka en, de, es, fr, pl, pt, sv, ru, nl, nb, tr-tr, it, zh, ko </li>
<li>65 znaků pro ostatní</li></ul>

## <a name="market-setting"></a>Nastavení trhu
V parametru dotazu v adrese URL požadavku je nutné zadat trh, jinak kontrola pravopisu použije výchozí trh na základě IP adresy.


## <a name="post-vs-get"></a>POST vs. GET

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
  
```  
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

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Další kroky

Pokud chcete rychle začít s vaším prvním požadavkem, projděte si popis [vytvoření prvního požadavku](quickstarts/csharp.md).

Seznamte se s [referenčními informacemi k rozhraní API Bingu pro kontrolu pravopisu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference). Tyto referenční informace obsahují seznam koncových bodů, hlaviček a parametrů dotazů, které můžete použít při odesílání požadavků na výsledky hledání, a definice objektů odpovědi. 

Nezapomeňte si přečíst [požadavky Bingu na zobrazení a použití](./useanddisplayrequirements.md), abyste neporušili žádná pravidla používání výsledků.
