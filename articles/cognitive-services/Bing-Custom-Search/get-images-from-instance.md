---
title: Získání obrázků z vlastního zobrazení – Vlastní vyhledávání Bingu
titleSuffix: Azure Cognitive Services
description: Přehled vysoké úrovně o používání vlastního vyhledávání Bingu k získání obrázků z vlastního zobrazení webu.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: 5fa71c62969e0f1ff7e98f374eca63efa051c041
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "66390334"
---
# <a name="get-images-from-your-custom-view"></a>Získání obrázků z vlastního zobrazení

Hledání vlastních obrázků bingem umožňuje obohatit vlastní vyhledávání o obrázky. Podobně jako webové výsledky podporuje vlastní vyhledávání i vyhledávání obrázků v seznamu webů vaší instance. Obrázky můžete získat pomocí rozhraní API pro vyhledávání vlastních obrázků bingu nebo prostřednictvím funkce Hosted UI. Použití funkce Hosted UI je jednoduché a doporučuje se pro zprovoznění vyhledávání v krátkém pořadí.  Informace o konfiguraci hostovaného uživatelského rozhraní tak, aby zahrnovalo obrázky, naleznete v [tématu Konfigurace hostovaného uživatelského rozhraní](hosted-ui.md).

Pokud chcete mít větší kontrolu nad zobrazením výsledků hledání, můžete použít rozhraní API pro vyhledávání vlastních obrázků bingu. Vzhledem k tomu, že volání rozhraní API rozhraní API pro vyhledávání obrázků bingu je podobné jako volání rozhraní API pro vyhledávání obrázků [bingem,](../Bing-Image-Search/overview.md) kde najdete příklady volající rozhraní API. Než to však uděláte, seznamte se s referenčním obsahem [rozhraní API pro vyhledávání vlastních obrázků.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference) Hlavními rozdíly jsou podporované parametry dotazu (musíte zahrnout parametr dotazu customConfig) a koncový bod, na který odesíláte požadavky.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->
