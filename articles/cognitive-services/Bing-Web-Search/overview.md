---
title: Co je rozhraní API Bingu pro vyhledávání na webu?
titleSuffix: Azure Cognitive Services
description: Rozhraní API Bingu pro vyhledávání na webu je služba RESTful, která poskytuje okamžité odpovědi na dotazy na hledání na webu. Nakonfigurujte výsledky tak, aby zahrnovaly webové stránky, obrázky, videa, novinky a další. Výsledky jsou ve formátu JSON a vycházejí z relevance hledání a vašich předplatných rozhraní API Bingu pro vyhledávání na webu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: overview
ms.date: 03/31/2020
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: e312265705161dd1cfa23856c9f6dfb71997ec82
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381095"
---
# <a name="what-is-the-bing-web-search-api"></a>Co je rozhraní API Bingu pro vyhledávání na webu?

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](https://aka.ms/cogsvcs/bingmove)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](https://aka.ms/cogsvcs/bingmigration).

Rozhraní API Bingu pro vyhledávání na webu je služba RESTful, která poskytuje okamžité odpovědi na dotazy uživatelů. Výsledky hledání je možné snadno nakonfigurovat tak, aby zahrnovaly webové stránky, obrázky, videa, zprávy, překlady a další. Vyhledávání na webu Bingu poskytuje výsledky jako JSON na základě relevance vyhledávání a vašich Vyhledávání na webu Bingu předplatných.

Toto rozhraní API je ideální pro aplikace, které potřebují přístup k veškerému obsahu relevantnímu pro vyhledávací dotaz uživatele. Pokud vytváříte aplikaci, která vyžaduje pouze konkrétní typ výsledku, zvažte použití [rozhraní API Bingu pro vyhledávání obrázků](../Bing-Image-Search/overview.md), [rozhraní API Bingu pro vyhledávání videí](../bing-video-search/overview.md) nebo [rozhraní API Bingu pro vyhledávání zpráv](../Bing-News-Search/search-the-web.md). Úplný seznam rozhraní API pro vyhledávání Bingu najdete v tématu [Rozhraní API služeb Cognitive Services](../index.yml).

Chcete se podívat, jak to funguje? Vyzkoušejte naši [ukázku rozhraní API Bingu pro vyhledávání na webu](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/).

## <a name="features"></a>Funkce  

Vyhledávání na webu Bingu vám nedává přístup jenom k okamžitým odpovědím. Poskytuje také další funkce a funkce, které umožňují přizpůsobit výsledky hledání pro uživatele.

| Funkce | Popis |
|---------|-------------|
| [Navrhování hledaných termínů v reálném čase](../bing-autosuggest/get-suggested-search-terms.md) | Pomocí rozhraní API pro automatické návrhy Bingu můžete vylepšit prostředí své aplikace tak, aby se při psaní zobrazovaly návrhy hledaných termínů. |
| [Filtrování a omezování výsledků podle typu obsahu](filter-answers.md) | Výsledky hledání můžete přizpůsobit a upřesnit pomocí filtrů a parametrů dotazu pro webové stránky, obrázky, videa, bezpečné hledání a dalších. |
| [Zvýrazňování shod pro znaky Unicode](hit-highlighting.md) | Před zobrazením výsledků hledání uživatelům v nich můžete pomocí zvýrazňování shod identifikovat a odebrat nežádoucí znaky Unicode. |
| [Lokalizace výsledků hledání podle země, oblasti nebo trhu](./language-support.md) | Rozhraní API Bingu pro vyhledávání na webu podporuje více než 30 zemí nebo oblastí. Pomocí této funkce můžete upřesnit výsledky hledání pro konkrétní zemi, oblast nebo trh. |
| [Analýza metrik hledání s využitím Statistiky Bingu](bing-web-stats.md) | Statistika Bingu je placené předplatné, které poskytuje analýzu objemu volání, nejčastějších řetězců dotazu, geografické distribuce a dalších. |

## <a name="workflow"></a>Pracovní postup

Rozhraní API Bingu pro vyhledávání na webu se snadno volá z jakéhokoli programovacího jazyka, který dokáže provádět požadavky HTTP a parsovat odpovědi JSON. Služba je přístupná pomocí [REST API](quickstarts/python.md) nebo [klientských knihoven vyhledávání na webu Bingu](./quickstarts/client-libraries.md).

1. [Vytvořte prostředek Azure](../cognitive-services-apis-create-account.md) pro rozhraní API pro vyhledávání Bingu. Pokud nemáte předplatné Azure, můžete si [vytvořit bezplatný účet](https://azure.microsoft.com/free/cognitive-services/).  
2. Odešlete [požadavek na rozhraní API Bingu pro vyhledávání na webu](quickstarts/python.md).
3. Parsujte odpověď JSON.

## <a name="next-steps"></a>Další kroky

* Podle našich [rychlých startů pro Python](./quickstarts/client-libraries.md?pivots=programming-language-python) poprvé zavolejte rozhraní API Bingu pro vyhledávání na webu.  
* [Vytvořte jednostránkovou webovou aplikaci](tutorial-bing-web-search-single-page-app.md).
* Prostudujte si [referenční dokumentaci k rozhraní API Bingu pro vyhledávání na webu verze 7](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference).  
* Přečtěte si další informace o [požadavcích na zobrazení a použití](./use-display-requirements.md) rozhraní API Bingu pro vyhledávání na webu.