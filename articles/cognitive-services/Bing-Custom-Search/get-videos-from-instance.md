---
title: Získat videa z vlastního zobrazení – Vlastní vyhledávání Bingu
titleSuffix: Azure Cognitive Services
description: Základní přehled o používání Vlastní vyhledávání Bingu k získání videí z vlastního zobrazení webu.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: 4e2bb14519f8ed4e3e4a0e9ac8800957b30229a9
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338414"
---
# <a name="get-videos-from-your-custom-view"></a>Získat videa z vlastního zobrazení

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Vlastní vyhledávání videí Bing umožňuje rozšířit vlastní možnosti hledání pomocí videí. Podobně jako webové výsledky podporuje vlastní vyhledávání i vyhledávání videí v seznamu webů vaší instance. Videa můžete získat pomocí rozhraní API pro vyhledávání vlastních videí Bingu nebo pomocí hostované funkce uživatelského rozhraní. Použití funkce hostovaného uživatelského rozhraní se snadno používá a doporučuje se k tomu, abyste mohli začít pracovat v krátkém pořadí. Informace o konfiguraci hostovaného uživatelského rozhraní pro zahrnutí videí najdete v tématu [Konfigurace prostředí hostovaného uživatelského rozhraní](hosted-ui.md).

Pokud chcete mít větší kontrolu nad zobrazením výsledků hledání, můžete použít rozhraní API pro vyhledávání vlastních videí v Bingu. Vzhledem k tomu, že volání rozhraní API je podobné volání rozhraní API Bingu pro vyhledávání videí, [Vvyhledávání videí Bingu](../bing-video-search/overview.md) pro příklady volání rozhraní API. Než to uděláte, Seznamte se s [referenčním obsahem rozhraní API pro vyhledávání vlastních videí](/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference) . Hlavní rozdíly jsou podporované parametry dotazu (musíte zahrnout parametr dotazu customConfig) a koncový bod, do kterého odesíláte požadavky.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->