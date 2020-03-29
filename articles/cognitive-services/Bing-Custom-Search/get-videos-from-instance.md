---
title: Získejte videa z vlastního zobrazení – Vlastní vyhledávání Bingu
titleSuffix: Azure Cognitive Services
description: Přehled vysoké úrovně o používání vlastního vyhledávání Bingu k získání videí z vlastního zobrazení webu.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: 222256036a59c7df302546bbf82648c4d524d43f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "68405094"
---
# <a name="get-videos-from-your-custom-view"></a>Získání videí z vlastního zobrazení

Vyhledávání vlastních videí Bingu umožňuje obohatit vlastní vyhledávání o videa. Podobně jako webové výsledky podporuje vlastní vyhledávání i vyhledávání videí v seznamu webů vaší instance. Videa můžete získat pomocí rozhraní API pro vyhledávání vlastních videí bingu nebo prostřednictvím funkce Hostovaného rozhraní. Použití funkce Hosted UI je jednoduché a doporučuje se pro zprovoznění vyhledávání v krátkém pořadí. Informace o konfiguraci hostovaného uživatelského rozhraní tak, aby zahrnovalo videa, naleznete v [tématu Konfigurace hostovaného uživatelského rozhraní](hosted-ui.md).

Pokud chcete mít větší kontrolu nad zobrazením výsledků hledání, můžete použít rozhraní API pro vyhledávání vlastních videí bingu. Vzhledem k tomu, že volání rozhraní API je podobné volání rozhraní API pro vyhledávání videa Bing, pozbytmům [bingu Video Search](../Bing-Video-Search/search-the-web.md) pro příklady volání rozhraní API. Než to však uděláte, seznamte se s referenčním obsahem [rozhraní API pro vlastní vyhledávání videí.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference) Hlavními rozdíly jsou podporované parametry dotazu (musíte zahrnout parametr dotazu customConfig) a koncový bod, na který odesíláte požadavky.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->
