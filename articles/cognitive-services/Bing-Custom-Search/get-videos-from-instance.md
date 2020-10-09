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
ms.openlocfilehash: 222256036a59c7df302546bbf82648c4d524d43f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "68405094"
---
# <a name="get-videos-from-your-custom-view"></a>Získat videa z vlastního zobrazení

Vlastní vyhledávání videí Bing umožňuje rozšířit vlastní možnosti hledání pomocí videí. Podobně jako webové výsledky podporuje vlastní vyhledávání i vyhledávání videí v seznamu webů vaší instance. Videa můžete získat pomocí rozhraní API pro vyhledávání vlastních videí Bingu nebo pomocí hostované funkce uživatelského rozhraní. Použití funkce hostovaného uživatelského rozhraní se snadno používá a doporučuje se k tomu, abyste mohli začít pracovat v krátkém pořadí. Informace o konfiguraci hostovaného uživatelského rozhraní pro zahrnutí videí najdete v tématu [Konfigurace prostředí hostovaného uživatelského rozhraní](hosted-ui.md).

Pokud chcete mít větší kontrolu nad zobrazením výsledků hledání, můžete použít rozhraní API pro vyhledávání vlastních videí v Bingu. Vzhledem k tomu, že volání rozhraní API je podobné volání rozhraní API Bingu pro vyhledávání videí, [Vvyhledávání videí Bingu](../Bing-Video-Search/search-the-web.md) pro příklady volání rozhraní API. Než to uděláte, Seznamte se s [referenčním obsahem rozhraní API pro vyhledávání vlastních videí](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference) . Hlavní rozdíly jsou podporované parametry dotazu (musíte zahrnout parametr dotazu customConfig) a koncový bod, do kterého odesíláte požadavky.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->
