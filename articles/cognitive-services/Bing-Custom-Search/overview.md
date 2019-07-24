---
title: Co je rozhraní API pro vlastní vyhledávání Bingu?
titleSuffix: Azure Cognitive Services
description: Rozhraní API pro vlastní vyhledávání Bingu vám umožní vytvářet prostředí s přizpůsobeným vyhledáváním pro témata, o kterých se zajímáte.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: overview
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: 4b0b0d91af15912e1c64761351ba33acfd3e2725
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405152"
---
# <a name="what-is-the-bing-custom-search-api"></a>Co je rozhraní API pro vlastní vyhledávání Bingu?

Rozhraní API pro vlastní vyhledávání Bingu vám umožní vytvářet uživatelsky přizpůsobené vyhledávací prostředí bez reklamy pro témata, o kterých se zajímáte. Můžete určit domény a webové stránky, které bude Bing Hledat, a také připnout, zvýšit nebo snížit konkrétní obsah, abyste mohli vytvořit vlastní zobrazení webu a pomáhat uživatelům rychle najít relevantní výsledky hledání. 

## <a name="features"></a>Funkce

|Funkce  |Popis  |
|---------|---------|
|[Vlastní návrhy hledání v reálném čase](define-custom-suggestions.md)     | Poskytněte návrhy hledání, které se dají jako typ uživatele zobrazovat jako rozevírací seznam.       | 
|[Prostředí pro vlastní vyhledávání obrázků](get-images-from-instance.md)     | Umožněte uživatelům vyhledávat image z domén a webů zadaných ve vaší instanci vlastního vyhledávání.        |        
|[Prostředí pro vlastní vyhledávání videí](get-videos-from-instance.md)     | Umožněte uživatelům hledat videa z domén a webů zadaných ve vaší instanci vlastního vyhledávání.        |    
|[Sdílení vlastní instance hledání](share-your-custom-search.md)     | Spoluupravujte a otestujte vaši instanci hledání tak, že ji sdílíte se členy týmu.        | 
|[Konfigurace uživatelského rozhraní pro aplikace a weby](hosted-ui.md)     | Spoluupravujte a otestujte vaši instanci hledání tak, že ji sdílíte se členy týmu.        | 
## <a name="workflow"></a>Pracovní postup

Přizpůsobenou instanci hledání můžete vytvořit pomocí [portálu vlastní vyhledávání Bingu](https://customsearch.ai). Portál umožňuje vytvořit vlastní instanci hledání, která určuje domény, weby a webové stránky, které má Bing Hledat, spolu s těmi, které nechcete hledat. Portál můžete také použít pro: náhled vyhledávacího prostředí, upravit pořadí hledání, které poskytuje rozhraní API, a volitelně nakonfigurovat prohledávatelný uživatelský rozhraní, které bude vykresleno na vašich webech a v aplikacích.

Po vytvoření instance vyhledávání ji můžete integrovat (a volitelně také pomocí uživatelského rozhraní) do svého webu nebo aplikace voláním rozhraní API pro vlastní vyhledávání Bingu:

![Obrázek znázorňující, že se můžete připojit ke službě Bing vlastní vyhledávání prostřednictvím rozhraní API](media/BCS-Overview.png "Jak vlastní vyhledávání Bingu funguje.")


## <a name="next-steps"></a>Další postup

Pokud chcete rychle začít, přečtěte si téma [Vytvoření první instance Vlastního vyhledávání Bingu](quick-start.md).

Podrobnosti o přizpůsobení instance vyhledávání najdete v tématu [Definice instance vlastního vyhledávání](define-your-custom-view.md).

Nezapomeňte si přečíst [požadavky na použití a zobrazení Bingu](./use-and-display-requirements.md) pro používání výsledků hledání ve vašich službách a aplikacích.

Seznamte se s referenčním obsahem pro jednotlivé koncové body vlastního vyhledávání. Tyto referenční informace obsahují koncové body, hlavičky a parametry dotazů, které můžete použít při odesílání požadavků na výsledky hledání. Obsahují také definice objektů odpovědi.

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

- [Rozhraní API pro vlastní vyhledávání Bingu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
- [Rozhraní API pro vlastní vyhledávání obrázků Bingu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference)
- [Rozhraní API pro vlastní vyhledávání videí Bingu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference)
- [Rozhraní API pro vlastní automatické návrhy Bingu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-autosuggest-api-v7-reference)

