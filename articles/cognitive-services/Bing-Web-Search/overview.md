---
title: Co je Vyhledávání na webu Bingu?
titleSuffix: Azure Cognitive Services
description: Rozhraní API Bingu pro vyhledávání na webu je služba RESTful, která poskytuje okamžité odpovědi na dotazy uživatelů. Výsledky hledání je možné snadno nakonfigurovat tak, aby zahrnovaly webové stránky, obrázky, videa, zprávy, překlady a další. Výsledky jsou ve formátu JSON a vycházejí z relevance hledání a vašich předplatných rozhraní API Bingu pro vyhledávání na webu.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: overview
ms.date: 08/14/2018
ms.author: erhopf
ms.openlocfilehash: 8482afae2889ccf2153e1864e4a65b635b6ab4c7
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46125234"
---
# <a name="what-is-bing-web-search"></a>Co je Vyhledávání na webu Bingu?

Rozhraní API Bingu pro vyhledávání na webu je služba RESTful, která poskytuje okamžité odpovědi na dotazy uživatelů. Výsledky hledání je možné snadno nakonfigurovat tak, aby zahrnovaly webové stránky, obrázky, videa, zprávy, překlady a další. Výsledky jsou ve formátu JSON a vycházejí z relevance hledání a vašich předplatných rozhraní API Bingu pro vyhledávání na webu.

Toto rozhraní API je ideální pro aplikace, které potřebují přístup k veškerému obsahu relevantnímu pro vyhledávací dotaz uživatele. Pokud vytváříte aplikaci, která vyžaduje pouze konkrétní typ výsledku, zvažte použití [rozhraní API Bingu pro vyhledávání obrázků](../Bing-Image-Search/overview.md), [rozhraní API Bingu pro vyhledávání videí](../Bing-Video-Search/search-the-web.md) nebo [rozhraní API Bingu pro vyhledávání zpráv](../Bing-News-Search/search-the-web.md). Úplný seznam rozhraní API pro vyhledávání Bingu najdete v tématu [Rozhraní API služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services#cognitive-services-apis).

Chcete se podívat, jak to funguje? Vyzkoušejte naši [ukázku rozhraní API Bingu pro vyhledávání na webu](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/).

## <a name="features"></a>Funkce  

Kromě okamžitých odpovědí poskytuje rozhraní API Bingu pro vyhledávání na webu další funkce, které umožňují přizpůsobit výsledky hledání pro uživatele.

| Funkce | Popis |
|---------|-------------|
| [Navrhování hledaných termínů v reálném čase](../bing-autosuggest/get-suggested-search-terms.md) | Pomocí rozhraní API pro automatické návrhy Bingu můžete vylepšit prostředí své aplikace tak, aby se při psaní zobrazovaly návrhy hledaných termínů. |
| [Filtrování a omezování výsledků podle typu obsahu](filter-answers.md) | Výsledky hledání můžete přizpůsobit a upřesnit pomocí filtrů a parametrů dotazu pro webové stránky, obrázky, videa, bezpečné hledání a dalších. |
| [Zvýrazňování shod pro znaky Unicode](hit-highlighting.md) | Před zobrazením výsledků hledání uživatelům v nich můžete pomocí zvýrazňování shod identifikovat a odebrat nežádoucí znaky Unicode. |
| [Lokalizace výsledků hledání podle země, oblasti nebo trhu](supported-countries-markets.md) | Rozhraní API Bingu pro vyhledávání na webu podporuje více než 30 zemí nebo oblastí. Pomocí této funkce můžete upřesnit výsledky hledání pro konkrétní zemi, oblast nebo trh. |
| [Analýza metrik hledání s využitím Statistiky Bingu](bing-web-stats.md) | Statistika Bingu je placené předplatné, které poskytuje analýzu objemu volání, nejčastějších řetězců dotazu, geografické distribuce a dalších. |

## <a name="workflow"></a>Pracovní postup

Rozhraní API Bingu pro vyhledávání na webu se snadno volá z jakéhokoli programovacího jazyka, který dokáže provádět požadavky HTTP a parsovat odpovědi JSON. Služba je přístupná prostřednictvím rozhraní [REST API](quickstarts/python.md) nebo sad [SDK pro rozhraní API Bingu pro vyhledávání na webu](web-sdk-python-quickstart.md).  

1. Vytvořte [účet rozhraní API služby Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s přístupem k rozhraním API pro vyhledávání Bingu. Pokud nemáte předplatné Azure, můžete si [vytvořit bezplatný účet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).  
2. Odešlete [požadavek na rozhraní API Bingu pro vyhledávání na webu](quickstarts/python.md).
3. Parsujte odpověď JSON.

## <a name="next-steps"></a>Další kroky

* Podle našich [rychlých startů pro Python](quickstarts/python.md) poprvé zavolejte rozhraní API Bingu pro vyhledávání na webu.  
* [Vytvořte jednostránkovou webovou aplikaci](tutorial-bing-web-search-single-page-app.md).
* Prostudujte si [referenční dokumentaci k rozhraní API Bingu pro vyhledávání na webu verze 7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference).  
* Přečtěte si další informace o [požadavcích na zobrazení a použití](UseAndDisplayRequirements.md) rozhraní API Bingu pro vyhledávání na webu.  
