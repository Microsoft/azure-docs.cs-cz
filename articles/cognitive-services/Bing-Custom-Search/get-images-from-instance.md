---
title: Získat Image z vlastního zobrazení | Dokumentace Microsoftu
titleSuffix: Cognitive Services
description: Základní přehled o použití vlastní vyhledávání Bingu k získání bitové kopie z vašeho vlastního zobrazení webu.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: ba836bbafaf67238664862ee2afce7840a573e44
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46953885"
---
# <a name="get-images-from-your-custom-view"></a>Získat Image z vlastního zobrazení

Vlastní vyhledávání obrázků Bingu umožňuje vylepšit prostředí pro vlastní vyhledávání s obrázky. Podobně jako webové výsledky hledání obrázků ve vaší instanci seznamu webů, podporuje vlastní vyhledávání. Můžete získat pomocí rozhraní API Bingu pro vyhledávání bitové kopie pro vlastní Image nebo prostřednictvím uživatelského rozhraní hostované funkce. Pomocí funkcí hostované uživatelského rozhraní se snadno používá a doporučuje se pro vaše prostředí hledání zprovoznění v krátkém pořadí.  Informace o konfiguraci vašeho uživatelského rozhraní hostované zahrnout i obrázky, najdete v tématu [hostované uživatelské rozhraní konfigurace](hosted-ui.md).

Pokud chcete mít větší kontrolu nad zobrazením výsledků hledání, můžete použít rozhraní API Bingu pro vyhledávání bitové kopie pro vlastní. Vzhledem k tomu, že volání rozhraní API je podobný volání rozhraní API vyhledávání obrázků Bingu checkout [Bingu pro vyhledávání obrázků](../Bing-Image-Search/overview.md) příklady volání rozhraní API. Ale předtím, než to uděláte, seznamte se s [referenční dokumentace rozhraní API pro vyhledávání obrázků vlastní](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-images-api-v7-reference) obsah. Hlavní rozdíly jsou podporované parametry dotazu (musí obsahovat parametr dotazu customConfig) a odesílat požadavky na koncový bod.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->