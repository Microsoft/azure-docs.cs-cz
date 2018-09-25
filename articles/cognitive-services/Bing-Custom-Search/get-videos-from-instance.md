---
title: Získejte videa z vašeho vlastního zobrazení | Dokumentace Microsoftu
description: Základní přehled o použití pro vlastní vyhledávání Bingu k získání videa z vašeho vlastního zobrazení webu.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: 980081ae2f576aadd4ac31e29f0f77ddcb4e7f64
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978785"
---
# <a name="get-videos-from-your-custom-view"></a>Získejte videa z vašeho vlastního zobrazení

Vlastní vyhledávání videí Bingu umožňuje rozšířit vaše vlastní vyhledávací prostředí s využitím videí. Podobně jako webové výsledky, podporuje vlastní vyhledávání, vyhledávání videí ve vaší instanci seznamu webů. Můžete získat videí pomocí rozhraní API Bingu pro vyhledávání videí pro vlastní nebo prostřednictvím uživatelského rozhraní hostované funkce. Pomocí funkcí hostované uživatelského rozhraní se snadno používá a doporučuje se pro vaše prostředí hledání zprovoznění v krátkém pořadí. Informace o konfiguraci vašeho uživatelského rozhraní hostované zahrnout videa najdete v tématu [hostované uživatelské rozhraní konfigurace](hosted-ui.md).

Pokud chcete mít větší kontrolu nad zobrazením výsledků hledání, můžete použít rozhraní API Bingu pro vyhledávání videí pro vlastní. Vzhledem k tomu, že volání rozhraní API je podobný volání API pro vyhledávání videí Bingu checkout [Bingu pro vyhledávání videí](../Bing-Video-Search/search-the-web.md) příklady volání rozhraní API. Ale předtím, než to uděláte, seznamte se s [referenční dokumentace rozhraní API pro vyhledávání videí vlastní](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-videos-api-v7-reference) obsah. Hlavní rozdíly jsou podporované parametry dotazu (musí obsahovat parametr dotazu customConfig) a odesílat požadavky na koncový bod.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->