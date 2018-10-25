---
title: Co je Project URL Preview?
titlesuffix: Azure Cognitive Services
description: Úvod k Project URL Preview.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: url-preview
ms.topic: overview
ms.date: 03/16/2018
ms.author: rosh
ms.openlocfilehash: e3791c897722c9bbf50f58c126d7e4e573b2096f
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49465810"
---
# <a name="what-is-project-url-preview"></a>Co je Project URL Preview?
Koncový bod rozhraní URL Preview přebírá parametr dotazu v podobě adresy URL a vrací odpověď ve formátu JSON s názvem cílového prostředku, stručným popisem a odkazem na obrázek, který se zobrazí v náhledu. Odpověď obsahuje také příznak [isFamilyFriendly](url-preview-reference.md#query-parameters), který označuje, zda se na adrese URL nachází obsah pro dospělé, obsah porušující autorská práva nebo jiný nelegální obsah. 

Výsledky náhledu adresy URL získáte tak, že pošlete požadavek GET, do kterého zahrnete hlavičku *Ocp-Apim-Subscription-Key* s platným tokenem:  
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=https://swiftkey.com

```
Odpověď: 
````
HTTP Headers:
BingAPIs-TraceId: 3CC74C94769440C0851D9DF0869FCE7F
BingAPIs-SessionId: 52219085A6364692958C9C83983A0DBA
X-MSEdge-ClientID: 13D44DC2DE946B4B0F25460CDF036AD6
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 3CC74C94769440C0851D9DF0869FCE7F Ref B: CO1EDGE0315 Ref C: 2018-04-11T18:47:40Z
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile typing",
  "url": "https:\/\/swiftkey.com\/en",
  "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https:\/\/swiftkey.com\/images\/og\/default.jpg"
  }
}

````
## <a name="scenarios"></a>Scénáře 

Rozhraní API URL Preview podporuje stručné popisy webových prostředků. Vývojářům to umožňuje vytvářet podrobné náhledy.  Uživatelé můžou sdílet nebo si ukládat do záložek webové stránky, zprávy, blogy, fóra atd. Toto rozhraní API také můžete použít k moderování obsahu.    

Aplikace pomocí rozhraní URL Preview odesílají do koncového bodu webové požadavky, které obsahují dotaz přiřazený k adrese URL, jejíž náhled chtějí získat.  Odpověď ve formátu JSON obsahuje informace náhledu: název, popis prostředku, příznak *familyFriendly* a odkazy, které poskytují přístup k reprezentativnímu obrázku a úplnému prostředku online. 

## <a name="terms-of-use"></a>Podmínky použití
Při sdílení adres URL iniciovaných koncovým uživatelem na sociálních sítích, v chatbotech nebo podobných službách používejte k zobrazení fragmentů náhledu a náhledových obrázků odkazujících na zdrojové stránky pouze data z rozhraní Project URL Preview. Nekopírujte a neukládejte (ani do mezipaměti) žádná data, která z rozhraní Project URL Preview obdržíte. Respektujte všechny případné požadavky na zákaz náhledů, které obdržíte od webových stránek nebo vlastníků obsahu.

Vy ani třetí strana jednající vaším jménem nesmí používat, uchovávat, ukládat, ukládat do mezipaměti, sdílet ani distribuovat jakákoliv data z rozhraní API URL Preview pro účely testování, vývoje, školení, distribuce ani zpřístupnění služeb nebo funkcí jiných společností než Microsoft. 

## <a name="throttling-requests"></a>Omezování požadavků

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Další kroky
- [Rychlý start pro jazyk C#](csharp.md)
- [Rychlý start pro Javu](java-quickstart.md)
- [Rychlý start pro JavaScript](javascript.md)
- [Rychlý start pro Node](node-quickstart.md)
- [Rychlý start pro Python](python-quickstart.md)
