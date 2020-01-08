---
title: Co je rozhraní API Bingu pro vyhledávání videí?
titleSuffix: Azure Cognitive Services
description: Naučte se hledat videa napříč webem pomocí rozhraní API Bingu pro vyhledávání videí.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: 8377f0f5d586212c94bb763598b6e7a9e391073c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75382714"
---
# <a name="what-is-the-bing-video-search-api"></a>Co je rozhraní API Bingu pro vyhledávání videí?

Rozhraní API Bingu pro vyhledávání videí usnadňuje přidávání funkcí pro vyhledávání videí pro vaše služby a aplikace. Když odesíláte vyhledávací dotazy uživatelů pomocí rozhraní API, můžete získat a zobrazit relevantní a vysoce kvalitní videa podobná [videu Bingu](https://www.bing.com/video). Toto rozhraní API se používá pro výsledky hledání, které obsahují jenom videa. [Rozhraní API Bingu pro vyhledávání na webu](../bing-web-search/search-the-web.md) mohou vracet další typy webového obsahu, včetně webových stránek, videí, zpráv a obrázků.

## <a name="bing-video-search-api-features"></a>rozhraní API Bingu pro vyhledávání videí funkce

| Funkce                                                                                                                                                                                 | Popis                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Navrhování hledaných termínů v reálném čase](concepts/sending-requests.md#suggest-search-terms-with-the-bing-autosuggest-api) | Pomocí [rozhraní API pro automatické návrhy Bingu](../bing-autosuggest/get-suggested-search-terms.md) můžete vylepšit prostředí své aplikace tak, aby se při psaní zobrazovaly návrhy hledaných termínů. |
| [Filtrování a omezení výsledků videa](concepts/get-videos.md#filtering-videos)                      | Filtrování videí vrácených úpravou parametrů dotazu.                                                                                                       |
| [Ořezávání, změna velikosti a zobrazování miniatur](../bing-web-search/resize-and-crop-thumbnails.md)                                                | Upraví a zobrazí náhledy miniatur pro videa vrácená rozhraní API Bingu pro vyhledávání videí.                                                                                      |
| [Získat videa o trendech](trending-videos.md) | Vyhledejte videa o trendech z celého světa.                                                                                                          |
| [Získat video přehledy](video-insights.md) | Umožňuje přizpůsobit hledání trendů z celého světa.                                                                                                          |

## <a name="workflow"></a>Pracovní postupy

Rozhraní API Bingu pro vyhledávání videí je webová služba RESTful, která usnadňuje volání ze všech programovacích jazyků, které mohou provádět požadavky HTTP a analyzovat JSON. Tuto službu můžete využívat pomocí rozhraní [REST API](csharp.md) nebo sady [SDK](video-search-sdk-quickstart.md).

1. Vytvořte [účet rozhraní API služby Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s přístupem k rozhraním API pro vyhledávání Bingu. Pokud nemáte předplatné Azure, můžete si zdarma [vytvořit účet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).
2. Odešle požadavek do rozhraní API s platným vyhledávacím dotazem.
3. Zpracujte odpověď rozhraní API parsováním vrácené zprávy JSON.


## <a name="next-steps"></a>Další kroky

Rozhraní API Bingu pro vyhledávání videí [interaktivní ukázka](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/) ukazuje, jak můžete upravit vyhledávací dotaz a vyhledat videa na webu.

Jakmile budete připraveni volat rozhraní API, vytvořte si [účet rozhraní API služby Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). Pokud nemáte předplatné Azure, můžete si zdarma [vytvořit účet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

Pomocí [rychlého](csharp.md) startu můžete rychle začít s první žádostí o rozhraní API.

## <a name="see-also"></a>Další informace najdete v tématech

* Odkaz na [rozhraní API Bingu pro vyhledávání videí v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference) obsahuje seznam koncových bodů, hlaviček a parametrů dotazu použitých k vyžádání výsledků hledání.

* [Požadavky Bingu na zobrazení a použití](./useanddisplayrequirements.md) určují přijatelné způsoby použití obsahu a informací získaných prostřednictvím rozhraní API pro vyhledávání Bingu.

* Navštivte [stránku vyhledávání Bingu centrum rozhraní API](../bing-web-search/search-the-web.md) a Prozkoumejte další dostupná rozhraní API.