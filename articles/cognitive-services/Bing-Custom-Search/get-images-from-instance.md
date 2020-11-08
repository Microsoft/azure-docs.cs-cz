---
title: Získat obrázky z vlastního zobrazení – Vlastní vyhledávání Bingu
titleSuffix: Azure Cognitive Services
description: Základní přehled o použití Vlastní vyhledávání Bingu k získání imagí z vlastního zobrazení webu.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: c29e83f7851bc9be1688f3828373942c0e31f4ef
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2020
ms.locfileid: "94367461"
---
# <a name="get-images-from-your-custom-view"></a>Získat obrázky z vlastního zobrazení

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](https://aka.ms/cogsvcs/bingmove)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](https://aka.ms/cogsvcs/bingmigration).

Vlastní vyhledávání obrázků Bing umožňuje rozšířit vlastní možnosti hledání pomocí imagí. Podobně jako webové výsledky podporuje vlastní vyhledávání i vyhledávání obrázků v seznamu webů vaší instance. Image můžete získat pomocí rozhraní API pro vlastní vyhledávání obrázků Bingu nebo pomocí hostované funkce uživatelského rozhraní. Použití funkce hostovaného uživatelského rozhraní se snadno používá a doporučuje se k tomu, abyste mohli začít pracovat v krátkém pořadí.  Informace o konfiguraci hostovaného uživatelského rozhraní pro zahrnutí imagí najdete v tématu [Konfigurace prostředí hostovaného uživatelského rozhraní](hosted-ui.md).

Pokud chcete mít větší kontrolu nad zobrazením výsledků hledání, můžete použít rozhraní API pro vyhledávání vlastních imagí Bingu. Vzhledem k tomu, že volání rozhraní API je podobné volání rozhraní API Bingu pro vyhledávání obrázků, [vyhledávání obrázků Bingu](../Bing-Image-Search/overview.md) pro příklady volání rozhraní API. Než to uděláte, Seznamte se s [referenčním obsahem rozhraní API pro vyhledávání vlastních imagí](/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference) . Hlavní rozdíly jsou podporované parametry dotazu (musíte zahrnout parametr dotazu customConfig) a koncový bod, do kterého odesíláte požadavky.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->