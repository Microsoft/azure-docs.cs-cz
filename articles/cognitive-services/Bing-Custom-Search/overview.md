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
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: e70d4d83fcd9641fa86f013688b5a3a6e3652919
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338397"
---
# <a name="what-is-the-bing-custom-search-api"></a>Co je rozhraní API pro vlastní vyhledávání Bingu?

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Rozhraní API pro vlastní vyhledávání Bingu vám umožní vytvářet uživatelsky přizpůsobené vyhledávací prostředí bez reklamy pro témata, o kterých se zajímáte. Můžete určit domény a webové stránky, které bude Bing Hledat, a také připnout, zvýšit nebo snížit konkrétní obsah, abyste mohli vytvořit vlastní zobrazení webu a pomáhat uživatelům rychle najít relevantní výsledky hledání. 

## <a name="features"></a>Funkce

|Funkce  |Popis  |
|---------|---------|
|[Vlastní návrhy hledání v reálném čase](define-custom-suggestions.md)     | Poskytněte návrhy hledání, které se dají jako typ uživatele zobrazovat jako rozevírací seznam.       | 
|[Prostředí pro vlastní vyhledávání obrázků](get-images-from-instance.md)     | Umožněte uživatelům vyhledávat image z domén a webů zadaných ve vaší instanci vlastního vyhledávání.        |        
|[Prostředí pro vlastní vyhledávání videí](get-videos-from-instance.md)     | Umožněte uživatelům hledat videa z domén a webů zadaných ve vaší instanci vlastního vyhledávání.        |    
|[Sdílení instance vlastního vyhledávání](share-your-custom-search.md)     | Spoluupravujte a otestujte vaši instanci hledání tak, že ji sdílíte se členy týmu.        | 
|[Konfigurace uživatelského rozhraní pro aplikace a weby](hosted-ui.md)     | Poskytuje hostované uživatelské rozhraní, které můžete snadno integrovat do webových stránek a webových aplikací jako fragment kódu JavaScriptu.        | 
## <a name="workflow"></a>Pracovní postup

Přizpůsobenou instanci hledání můžete vytvořit pomocí [portálu vlastní vyhledávání Bingu](https://customsearch.ai). Portál umožňuje vytvořit vlastní instanci hledání, která určuje domény, weby a webové stránky, které má Bing Hledat, spolu s těmi, které nechcete hledat. Portál můžete také použít pro: náhled vyhledávacího prostředí, upravit pořadí hledání, které poskytuje rozhraní API, a volitelně nakonfigurovat prohledávatelný uživatelský rozhraní, které bude vykresleno na vašich webech a v aplikacích.

Po vytvoření instance vyhledávání ji můžete integrovat (a volitelně také pomocí uživatelského rozhraní) do svého webu nebo aplikace voláním rozhraní API pro vlastní vyhledávání Bingu:

![Obrázek znázorňující, že se můžete připojit ke službě Bing vlastní vyhledávání prostřednictvím rozhraní API](media/BCS-Overview.png "Jak Vlastní vyhledávání Bingu funguje.")


## <a name="next-steps"></a>Další kroky

Pokud chcete rychle začít, přečtěte si téma [Vytvoření první instance Vlastního vyhledávání Bingu](quick-start.md).

Podrobnosti o přizpůsobení instance vyhledávání najdete v tématu [Definice instance vlastního vyhledávání](define-your-custom-view.md).

Nezapomeňte si přečíst [požadavky na použití a zobrazení Bingu](../bing-web-search/use-display-requirements.md) pro používání výsledků hledání ve vašich službách a aplikacích.

Navštivte [stránku vyhledávání Bingu centrum rozhraní API](../bing-web-search/overview.md) a Prozkoumejte další dostupná rozhraní API.

Seznamte se s referenčním obsahem pro jednotlivé koncové body vlastního vyhledávání. Tyto referenční informace obsahují koncové body, hlavičky a parametry dotazů, které můžete použít při odesílání požadavků na výsledky hledání. Obsahují také definice objektů odpovědi.

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

- [Rozhraní API pro vlastní vyhledávání](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
- [Rozhraní API pro vlastní vyhledávání obrázků Bingu](/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference)
- [Rozhraní API pro vlastní vyhledávání videí Bingu](/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference)
- [Rozhraní API pro vlastní automatické návrhy Bingu](/rest/api/cognitiveservices-bingsearch/bing-custom-autosuggest-api-v7-reference)