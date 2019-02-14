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
ms.date: 02/06/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: fa1e2e6ac6e85c431a759d8eb1c22923e86e40d4
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56237310"
---
# <a name="what-is-the-bing-image-search-api"></a>Co je rozhraní API Bingu pro vyhledávání obrázků?

Rozhraní API Bingu pro vyhledávání obrázků můžete pomocí možnosti vyhledávání obrázků Bingu ve vaší aplikaci. Odesláním vyhledávacích dotazů na rozhraní API můžete získat vysoce kvalitní imagí podobný [bing.com/images](https://www.bing.com/images).

Rozhraní API Bingu pro vyhledávání obrázků poskytuje výsledky hledání jen pro image, můžete kombinovat nebo používat další dostupné [rozhraní API Bingu pro vyhledávání](../bing-web-search/bing-api-comparison.md) najít mnoho typů obsahu na webu.

## <a name="bing-image-search-features"></a>Funkce rozhraní API Bingu pro vyhledávání obrázků

| Funkce                                                                                                                                                                                 | Popis                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Navrhování hledaných termínů v reálném čase](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) | Pomocí [rozhraní API pro automatické návrhy Bingu](../bing-autosuggest/get-suggested-search-terms.md) můžete vylepšit prostředí své aplikace tak, aby se při psaní zobrazovaly návrhy hledaných termínů. |
| [Filtrování a omezování výsledků hledání obrázků](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images)                       | Obrázky, které Bing vrací, můžete filtrovat úpravou parametrů dotazu.                                                                                                       |
| [Ořezávání, změna velikosti a zobrazování miniatur](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/resize-and-crop-thumbnails)                                                | Pro obrázky vrácené rozhraním API Bingu pro vyhledávání obrázků můžete upravovat a zobrazovat náhledy miniatur.                                                                                      |
| [Otáčení a rozšiřování vyhledávacích dotazů uživatelů](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries)               | Možnosti vyhledávání můžete rozšířit tím, že pro dotazy zahrnete a zobrazíte hledané termíny navrhované Bingem.                                                                    |
| [Získání obrázků, které jsou v kurzu](https://review.docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images)                                                                     | Přizpůsobte si vyhledávání obrázků z celého světa, které jsou právě v kurzu.                                                                                                          |

## <a name="workflow"></a>Pracovní postup

Rozhraní API Bingu pro vyhledávání obrázků je webová služba RESTful a díky tomu se snadno volá z jakéhokoli programovacího jazyka, který dokáže provádět požadavky HTTP a parsovat JSON. Tuto službu můžete využívat pomocí rozhraní [REST API](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp?) nebo sady [SDK](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart).

1. Vytvořte [účet rozhraní API služby Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s přístupem k rozhraním API pro vyhledávání Bingu. Pokud nemáte předplatné Azure, můžete si zdarma [vytvořit účet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).
2. Odešlete do rozhraní API požadavek s platným [vyhledávacím dotazem](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries).
3. Zpracujte odpověď rozhraní API parsováním vrácené zprávy JSON.

## <a name="next-steps"></a>Další postup

Nejprve vyzkoušejte [interaktivní ukázku](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) rozhraní API Bingu pro vyhledávání obrázků.
Tato ukázka znázorňuje, jak můžete rychle přizpůsobit vyhledávací dotaz a procházet obrázky na webu.

Jakmile budete připraveni k volání rozhraní API, vytvořit [účtu služeb Cognitive services API](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). Pokud nemáte předplatné Azure, můžete si zdarma [vytvořit účet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

Pokud chcete rychle začít s prvním požadavkem na rozhraní API, můžete si přečíst informace o následujících tématech:

* [Odesílání vyhledávacích dotazů do Bingu](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp) s využitím rozhraní REST API
* [Vyžádání a filtrování](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart) obrázků vrácených Bingem pomocí sady SDK

## <a name="see-also"></a>Další informace najdete v tématech

* [Podrobnosti o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) pro rozhraní API pro vyhledávání Bingu. 

* [API Bingu pro vyhledávání obrázků v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) odkaz část obsahuje informace o koncových bodů rozhraní API, záhlaví, odpovědi rozhraní API a parametry dotazu.

* [Požadavky Bingu na zobrazení a použití](./useanddisplayrequirements.md) určují přijatelné způsoby použití obsahu a informací získaných prostřednictvím rozhraní API pro vyhledávání Bingu.

* [Získávání imagí z webu pomocí rozhraní API Bingu pro vyhledávání obrázků](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images) článek popisuje, jak vyhledat a získat Image z webu.

* [Odesílání a práci s vyhledávací dotazy](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) článek popisuje, jak vytvořit, přizpůsobit a otáčení vyhledávací dotazy.
