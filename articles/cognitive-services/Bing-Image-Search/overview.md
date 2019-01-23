---
title: Co je API pro vyhledávání obrázků Bingu?
titleSuffix: Azure Cognitive Services
description: Rozhraní API Bingu pro vyhledávání obrázků umožňuje využívat kognitivní možnosti vyhledávání obrázků Bingu ve vlastní aplikaci. Prostřednictvím odesílání vyhledávacích dotazů uživatelů pomocí rozhraní API můžete získat a zobrazit relevantní a vysoce kvalitní obrázky podobně jako v Obrázcích Bingu.
services: cognitive-services
author: aahill
manager: cgronlun
ms.assetid: 1446AD8B-A685-4F5F-B4AA-74C8E9A40BE9
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: overview
ms.date: 10/11/2017
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 71e8e363a02e08a099d9051c7f851e11bbd9b80b
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54467277"
---
# <a name="what-is-the-bing-image-search-api"></a>Co je API pro vyhledávání obrázků Bingu?

Rozhraní API Bingu pro vyhledávání obrázků umožňuje využívat kognitivní možnosti vyhledávání obrázků Bingu ve vlastní aplikaci. Prostřednictvím odesílání vyhledávacích dotazů uživatelů pomocí rozhraní API můžete získat a zobrazit relevantní a vysoce kvalitní obrázky podobně jako v [Obrázcích Bingu](https://www.bing.com/images).

Mějte na paměti, že rozhraní API Bingu pro vyhledávání obrázků poskytuje pouze výsledky hledání obrázků. Pro ostatní typy webového obsahu můžete použít [rozhraní API Bingu pro vyhledávání na webu](../bing-web-search/search-the-web.md), [rozhraní API Bingu pro vyhledávání videí](https://docs.microsoft.com/azure/cognitive-services/Bing-Video-Search) a [rozhraní API Bingu pro vyhledávání zpráv](https://review.docs.microsoft.com/azure/cognitive-services/bing-news-search).

## <a name="bing-image-search-features"></a>Funkce rozhraní API Bingu pro vyhledávání obrázků

Přestože rozhraní API Bingu pro vyhledávání obrázků primárně vyhledává a vrací relevantní obrázky na základě vyhledávacího dotazu, poskytuje tato služba také několik dalších funkcí inteligentního a úzce zaměřeného načítání obrázků na webu.


| Funkce                                                                                                                                                                                 | Popis                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Navrhování hledaných termínů v reálném čase](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) | Pomocí [rozhraní API pro automatické návrhy Bingu](../bing-autosuggest/get-suggested-search-terms.md) můžete vylepšit prostředí své aplikace tak, aby se při psaní zobrazovaly návrhy hledaných termínů. |
| [Filtrování a omezování výsledků hledání obrázků](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images#filtering-images)                       | Obrázky, které Bing vrací, můžete filtrovat úpravou parametrů dotazu.                                                                                                       |
| [Ořezávání, změna velikosti a zobrazování miniatur](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/resize-and-crop-thumbnails)                                                | Pro obrázky vrácené rozhraním API Bingu pro vyhledávání obrázků můžete upravovat a zobrazovat náhledy miniatur.                                                                                      |
| [Otáčení a rozšiřování vyhledávacích dotazů uživatelů](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries#pivoting-the-query)               | Možnosti vyhledávání můžete rozšířit tím, že pro dotazy zahrnete a zobrazíte hledané termíny navrhované Bingem.                                                                    |
| [Získání obrázků, které jsou v kurzu](https://review.docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images)                                                                     | Přizpůsobte si vyhledávání obrázků z celého světa, které jsou právě v kurzu.                                                                                                          |

## <a name="workflow"></a>Pracovní postup

Rozhraní API Bingu pro vyhledávání obrázků je webová služba RESTful a díky tomu se snadno volá z jakéhokoli programovacího jazyka, který dokáže provádět požadavky HTTP a parsovat JSON. Tuto službu můžete využívat pomocí rozhraní [REST API](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp?) nebo sady [SDK](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart).

1. Vytvořte [účet rozhraní API služby Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s přístupem k rozhraním API pro vyhledávání Bingu. Pokud nemáte předplatné Azure, můžete si zdarma [vytvořit účet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).
2. Odešlete do rozhraní API požadavek s platným [vyhledávacím dotazem](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries).
3. Zpracujte odpověď rozhraní API parsováním vrácené zprávy JSON.

## <a name="next-steps"></a>Další postup

Nejprve vyzkoušejte [interaktivní ukázku](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) rozhraní API Bingu pro vyhledávání obrázků.
Tato ukázka znázorňuje, jak můžete rychle přizpůsobit vyhledávací dotaz a procházet obrázky na webu.

Jakmile budete připraveni volat rozhraní API, vytvořte si [účet rozhraní API služby Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). Pokud nemáte předplatné Azure, můžete si zdarma [vytvořit účet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

Pokud chcete rychle začít s prvním požadavkem na rozhraní API, můžete si přečíst informace o následujících tématech:

* [Odesílání vyhledávacích dotazů do Bingu](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp) s využitím rozhraní REST API
* [Vyžádání a filtrování](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart) obrázků vrácených Bingem pomocí sady SDK

## <a name="see-also"></a>Další informace najdete v tématech

* V části s referenčními informacemi k [rozhraní API Bingu pro vyhledávání obrázků verze 7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) najdete definice a informace o koncových bodech, hlavičkách, odpovědích rozhraní API a parametrech dotazu, které můžete použít k odesílání požadavků na výsledky hledání obrázků.

* [Požadavky Bingu na zobrazení a použití](./useanddisplayrequirements.md) určují přijatelné způsoby použití obsahu a informací získaných prostřednictvím rozhraní API pro vyhledávání Bingu.

* Téma [Získávání obrázků z webu s využitím rozhraní API Bingu pro vyhledávání obrázků](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images) popisuje, jak vyhledat a získat obrázky z webu.

* Téma [Odesílání a práce s vyhledávacími dotazy](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) popisuje, jak vytvořit, přizpůsobit a otočit vyhledávací dotazy.
