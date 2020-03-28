---
title: Co je rozhraní API pro vlastní vyhledávání Bingu?
titleSuffix: Azure Cognitive Services
description: Rozhraní API pro vlastní vyhledávání Bingu umožňuje vytvářet přizpůsobené možnosti vyhledávání pro témata, která vás zajímají.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: 12c255f0e4ed11ce8acbd762530604218f8bc12b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75448725"
---
# <a name="what-is-the-bing-custom-search-api"></a>Co je rozhraní API pro vlastní vyhledávání Bingu?

Rozhraní API pro vlastní vyhledávání Bingu umožňuje vytvářet přizpůsobené vyhledávací prostředí bez reklam pro témata, která vás zajímají. Můžete určit domény a webové stránky, které má Bing prohledávat, a také připnout, propagovat nebo snížit úroveň konkrétního obsahu, abyste vytvořili vlastní zobrazení webu a pomohli uživatelům rychle najít relevantní výsledky vyhledávání. 

## <a name="features"></a>Funkce

|Funkce  |Popis  |
|---------|---------|
|[Vlastní návrhy hledání v reálném čase](define-custom-suggestions.md)     | Poskytněte návrhy hledání, které lze zobrazit jako rozevírací seznam podle typu uživatelů.       | 
|[Vlastní možnosti vyhledávání obrázků](get-images-from-instance.md)     | Umožněte uživatelům vyhledávat obrázky z domén a webů určených ve vlastní instanci vyhledávání.        |        
|[Vlastní možnosti vyhledávání videí](get-videos-from-instance.md)     | Umožněte uživatelům vyhledávat videa z domén a webů určených ve vlastní instanci vyhledávání.        |    
|[Sdílení instance vlastního vyhledávání](share-your-custom-search.md)     | Společně upravujte a otestujte svou instanci vyhledávání sdílením se členy týmu.        | 
|[Konfigurace nového rozhraní pro aplikace a weby](hosted-ui.md)     | Společně upravujte a otestujte svou instanci vyhledávání sdílením se členy týmu.        | 
## <a name="workflow"></a>Pracovní postup

Přizpůsobenou instanci vyhledávání můžete vytvořit pomocí [portálu vlastního vyhledávání Bing .](https://customsearch.ai) Portál umožňuje vytvořit vlastní instanci vyhledávání, která určuje domény, weby a webové stránky, které má Bing prohledávat, spolu s těmi, které nechcete, aby prohledávat. Portál můžete také použít k: zobrazení náhledu prostředí vyhledávání, úpravě pořadí vyhledávání, které rozhraní API poskytuje, a volitelně nakonfigurovat uživatelské rozhraní s možností vyhledávání, které se vykresluje na vašich webech a v aplikacích.

Po vytvoření instance vyhledávání ji můžete integrovat (a volitelně uživatelské rozhraní) do svého webu nebo aplikace voláním rozhraní API pro vlastní vyhledávání Bingu:

![Obrázek, který ukazuje, že se můžete připojit k vlastnímu vyhledávání Bingu prostřednictvím rozhraní API](media/BCS-Overview.png "Jak funguje vlastní vyhledávání Bingu.")


## <a name="next-steps"></a>Další kroky

Pokud chcete rychle začít, přečtěte si téma [Vytvoření první instance Vlastního vyhledávání Bingu](quick-start.md).

Podrobnosti o přizpůsobení instance vyhledávání najdete v tématu [Definice instance vlastního vyhledávání](define-your-custom-view.md).

Nezapomeňte si přečíst [požadavky na používání služby Bing a zobrazení](./use-and-display-requirements.md) pro použití výsledků hledání ve vašich službách a aplikacích.

Navštivte [stránku centra rozhraní API pro vyhledávání Binga a](../bing-web-search/search-the-web.md) prozkoumejte další dostupná rozhraní API.

Seznamte se s referenčním obsahem pro jednotlivé koncové body vlastního vyhledávání. Tyto referenční informace obsahují koncové body, hlavičky a parametry dotazů, které můžete použít při odesílání požadavků na výsledky hledání. Obsahují také definice objektů odpovědi.

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

- [Rozhraní API pro vlastní vyhledávání Bingu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
- [Rozhraní API pro vlastní vyhledávání obrázků Bingu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference)
- [Rozhraní API pro vlastní vyhledávání videí Bingu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference)
- [Rozhraní API pro vlastní automatické návrhy Bingu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-autosuggest-api-v7-reference)

