---
title: Co je rozhraní API Bingu pro vyhledávání obrázků?
titleSuffix: Azure Cognitive Services
description: Rozhraní API Bingu pro vyhledávání obrázků umožňuje využívat kognitivní možnosti vyhledávání obrázků Bingu ve vlastní aplikaci. Prostřednictvím odesílání vyhledávacích dotazů uživatelů pomocí rozhraní API můžete získat a zobrazit relevantní a vysoce kvalitní obrázky podobně jako v Obrázcích Bingu.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 1446AD8B-A685-4F5F-B4AA-74C8E9A40BE9
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: eb657c16f6f3ff67f4379134f3aa478f10d8ef94
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "85603532"
---
# <a name="what-is-the-bing-image-search-api"></a>Co je rozhraní API Bingu pro vyhledávání obrázků?

Rozhraní API Bingu pro vyhledávání obrázků umožňuje používat ve vaší aplikaci funkce pro vyhledávání obrázků Bingu. Odesláním vyhledávacích dotazů do rozhraní API můžete získat vysoce kvalitní obrázky podobné [Bing.com/images](https://www.bing.com/images).

I když rozhraní API Bingu pro vyhledávání obrázků poskytuje výsledky hledání jen pro image, můžete zkombinovat nebo použít jiné dostupné [rozhraní API pro vyhledávání Bingu](../bing-web-search/bing-api-comparison.md) k vyhledání mnoha typů obsahu na webu.

## <a name="bing-image-search-features"></a>Funkce rozhraní API Bingu pro vyhledávání obrázků

| Funkce                                                                                                                                                                                 | Popis                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Navrhování hledaných termínů v reálném čase](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) | Pomocí [rozhraní API pro automatické návrhy Bingu](../bing-autosuggest/get-suggested-search-terms.md) můžete vylepšit prostředí své aplikace tak, aby se při psaní zobrazovaly návrhy hledaných termínů. |
| [Filtrování a omezování výsledků hledání obrázků](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images)                       | Obrázky, které Bing vrací, můžete filtrovat úpravou parametrů dotazu.                                                                                                       |
| [Ořezávání, změna velikosti a zobrazování miniatur](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/resize-and-crop-thumbnails)                                                | Pro obrázky vrácené rozhraním API Bingu pro vyhledávání obrázků můžete upravovat a zobrazovat náhledy miniatur.                                                                                      |
| [Otáčení a rozšiřování vyhledávacích dotazů uživatelů](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries)               | Možnosti vyhledávání můžete rozšířit tím, že pro dotazy zahrnete a zobrazíte hledané termíny navrhované Bingem.                                                                    |
| [Získání obrázků, které jsou v kurzu](trending-images.md)                                                                     | Přizpůsobte si vyhledávání obrázků z celého světa, které jsou právě v kurzu.                                                                                                          |

## <a name="workflow"></a>Pracovní postup

Rozhraní API Bingu pro vyhledávání obrázků je webová služba RESTful a díky tomu se snadno volá z jakéhokoli programovacího jazyka, který dokáže provádět požadavky HTTP a parsovat JSON. Tuto službu můžete využívat pomocí rozhraní [REST API](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp?) nebo sady [SDK](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart).

1. Vytvořte [účet rozhraní API služby Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s přístupem k rozhraním API pro vyhledávání Bingu. Pokud nemáte předplatné Azure, můžete si zdarma [vytvořit účet](https://azure.microsoft.com/free/cognitive-services/).
2. Odešlete do rozhraní API požadavek s platným [vyhledávacím dotazem](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries).
3. Zpracujte odpověď rozhraní API parsováním vrácené zprávy JSON.

## <a name="next-steps"></a>Další kroky

Nejprve vyzkoušejte [interaktivní ukázku](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) rozhraní API Bingu pro vyhledávání obrázků.
Tato ukázka znázorňuje, jak můžete rychle přizpůsobit vyhledávací dotaz a procházet obrázky na webu.

Pokud chcete rychle začít s prvním požadavkem na rozhraní API, můžete si přečíst informace o následujících tématech:

* [Odesílání vyhledávacích dotazů do Bingu](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp) s využitím rozhraní REST API
* [Vyžádání a filtrování](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart) obrázků vrácených Bingem pomocí sady SDK

## <a name="see-also"></a>Viz také

* [Podrobnosti o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) pro rozhraní API pro vyhledávání Bingu. 

* Část [rozhraní API Bingu pro vyhledávání obrázků](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) Reference k V7 obsahuje informace o koncových bodech, hlavičkách, ODPOVĚDÍCH rozhraní API a parametrech dotazů rozhraní API.

* [Požadavky Bingu na zobrazení a použití](./useanddisplayrequirements.md) určují přijatelné způsoby použití obsahu a informací získaných prostřednictvím rozhraní API pro vyhledávání Bingu.

* [Získávání imagí z webu pomocí rozhraní API Bingu pro vyhledávání obrázků](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images) článku popisuje, jak vyhledávat a získávat obrázky z webu.

* Článek [odeslání a práce s dotazy pro hledání](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) popisuje, jak vytvářet, přizpůsobovat a pivotovat vyhledávací dotazy.

* Navštivte [stránku vyhledávání Bingu centrum rozhraní API](../bing-web-search/search-the-web.md) a Prozkoumejte další dostupná rozhraní API.
