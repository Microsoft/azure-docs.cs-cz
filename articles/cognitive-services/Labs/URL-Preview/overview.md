---
title: Co je náhled adresy URL projektu? -Microsoft kognitivní služby | Microsoft Docs
description: Úvod do verze Preview adresa URL projektu.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/16/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 6b486e0ab4092bef4fe829a5f166311a572a2900
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343233"
---
# <a name="what-is-project-url-preview"></a>Co je náhled adresy URL projektu?
Koncový bod adresy URL Preview přebírá parametr adresy URL dotazu a vrátí odpověď JSON s názvem cílový prostředek, stručný popis a odkaz na obrázek zobrazíte v náhledu. Odpověď obsahuje také [isFamilyFriendly](url-preview-reference.md#query-parameters) příznak označující, zda adresa URL obsahuje neplatný obsah pro dospělé, nelegální či jiných. 

Chcete-li získat adresu URL náhledu výsledků, odešlete požadavek GET a zahrnout *Ocp-Apim-Subscription-Key* hlavička s platný token:  
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

Rozhraní API URL Preview podporuje stručný popis webové prostředky. Vývojáři ji použít k vytváření bohatě preview prostředí.  Uživatelé mohou sdílet nebo bookmark webové stránky, zprávy, blogy, fóra, atd. Toto rozhraní API můžete použít také pro obsahu přerušování.    

Aplikací pomocí adresy URL Preview odesílají webové požadavky na koncový bod s dotazem přiřazené k adrese URL si verzi preview.  Odpověď JSON obsahuje informace o verzi preview: název, popis prostředku, *familyFriendly* příznak a odkazy, které poskytují přístup k reprezentativní bitové kopie a k dokončení prostředek online. 

## <a name="terms-of-use"></a>Podmínky použití
Použijte pouze data z verze Preview adresa URL projektu můžete zobrazit náhled fragmenty a miniatury s hypertextovým odkazem na jejich zdrojových lokalit ve sdílení na sociálních médií, chatovací robota nebo podobné nabídky URL iniciované koncovým uživatelem. D nelze zkopírovat, uložit, nebo žádná data, které jste získali od Preview adresa URL projektu do mezipaměti. Případném dalším sdílení dodržovat všechny žádosti zakázat verze Preview, které se mohou zobrazit od vlastníků webu nebo obsahu.

Můžete nebo třetích stran vaším jménem nemusí používat, zachovat, ukládat, mezipaměti, sdílet, nebo distribuovat všechna data z rozhraní API Preview adresu URL pro testování, vývoj, školení, distribuci nebo zpřístupnění jakoukoli službu, jiných společností než Microsoft nebo funkci. 

## <a name="throttling-requests"></a>Omezování požadavků

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Další postup
- [Rychlý start C#](csharp.md)
- [Rychlý start Java](java-quickstart.md)
- [Rychlý start JavaScript](javascript.md)
- [Rychlé spuštění uzlu](node-quickstart.md)
- [Rychlý start Python](python-quickstart.md)
