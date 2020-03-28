---
title: Co je rozhraní API Bingu pro vyhledávání videí?
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak vyhledávat videa na webu pomocí rozhraní API pro vyhledávání videí Bingu.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: 8377f0f5d586212c94bb763598b6e7a9e391073c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75382714"
---
# <a name="what-is-the-bing-video-search-api"></a>Co je rozhraní API Bingu pro vyhledávání videí?

Rozhraní API pro vyhledávání videa Bingu usnadňuje přidávání funkcí vyhledávání videa do vašich služeb a aplikací. Odesláním vyhledávacích dotazů uživatelů pomocí rozhraní API můžete získat a zobrazit relevantní a vysoce kvalitní videa podobná [bingu Video](https://www.bing.com/video). Toto rozhraní API použijte pro výsledky hledání, které obsahují pouze videa. Rozhraní [API pro vyhledávání na webu Bing](../bing-web-search/search-the-web.md) může vracet další typy webového obsahu, včetně webových stránek, videí, zpráv a obrázků.

## <a name="bing-video-search-api-features"></a>Funkce rozhraní API pro vyhledávání videí bingu

| Funkce                                                                                                                                                                                 | Popis                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Navrhování hledaných termínů v reálném čase](concepts/sending-requests.md#suggest-search-terms-with-the-bing-autosuggest-api) | Pomocí [rozhraní API pro automatické návrhy Bingu](../bing-autosuggest/get-suggested-search-terms.md) můžete vylepšit prostředí své aplikace tak, aby se při psaní zobrazovaly návrhy hledaných termínů. |
| [Filtrování a omezení výsledků videa](concepts/get-videos.md#filtering-videos)                      | Filtrujte videa vrácená úpravou parametrů dotazu.                                                                                                       |
| [Ořezávání, změna velikosti a zobrazování miniatur](../bing-web-search/resize-and-crop-thumbnails.md)                                                | Upravte a zobrazte náhledy miniatur videí vrácených rozhraním API pro vyhledávání videí Bingu.                                                                                      |
| [Získání videí, která jsou v kurzu](trending-videos.md) | Hledejte populární videa z celého světa.                                                                                                          |
| [Získání přehledů videí](video-insights.md) | Přizpůsobte si vyhledávání populárních videí z celého světa.                                                                                                          |

## <a name="workflow"></a>Pracovní postup

Bing Video Search API je RESTful webové služby, takže je snadné volat z libovolného programovacího jazyka, který může provádět požadavky HTTP a analyzovat JSON. Tuto službu můžete využívat pomocí rozhraní [REST API](csharp.md) nebo sady [SDK](video-search-sdk-quickstart.md).

1. Vytvořte [účet rozhraní API služby Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s přístupem k rozhraním API pro vyhledávání Bingu. Pokud nemáte předplatné Azure, můžete si zdarma [vytvořit účet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).
2. Odešlete do rozhraní API požadavek s platným vyhledávacím dotazem.
3. Zpracujte odpověď rozhraní API parsováním vrácené zprávy JSON.


## <a name="next-steps"></a>Další kroky

[Interaktivní ukázka rozhraní](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/) API pro vyhledávání videí bingu ukazuje, jak můžete přizpůsobit vyhledávací dotaz a vyhledat videa na webu.

Jakmile budete připraveni volat rozhraní API, vytvořte si [účet rozhraní API služby Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). Pokud nemáte předplatné Azure, můžete si zdarma [vytvořit účet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

Pomocí [rychlého startu](csharp.md) můžete rychle začít s prvním požadavkem na rozhraní API.

## <a name="see-also"></a>Viz také

* Referenční stránka [rozhraní API pro vyhledávání videa Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference) obsahuje seznam koncových bodů, záhlaví a parametrů dotazu, které se používají k vyžádání výsledků hledání.

* [Požadavky Bingu na zobrazení a použití](./useanddisplayrequirements.md) určují přijatelné způsoby použití obsahu a informací získaných prostřednictvím rozhraní API pro vyhledávání Bingu.

* Navštivte [stránku centra rozhraní API pro vyhledávání Binga a](../bing-web-search/search-the-web.md) prozkoumejte další dostupná rozhraní API.