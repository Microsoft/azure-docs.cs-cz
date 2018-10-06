---
title: Získejte videa z vašeho vlastního zobrazení – vlastní vyhledávání Bingu
titlesuffix: Azure Cognitive Services
description: Základní přehled o použití pro vlastní vyhledávání Bingu k získání videa z vašeho vlastního zobrazení webu.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: 5394f86e8a5d167830cb4061800f4cbf9a521713
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816402"
---
# <a name="get-videos-from-your-custom-view"></a>Získejte videa z vašeho vlastního zobrazení

Vlastní vyhledávání videí Bingu umožňuje rozšířit vaše vlastní vyhledávací prostředí s využitím videí. Podobně jako webové výsledky, podporuje vlastní vyhledávání, vyhledávání videí ve vaší instanci seznamu webů. Můžete získat videí pomocí rozhraní API Bingu pro vyhledávání videí pro vlastní nebo prostřednictvím uživatelského rozhraní hostované funkce. Pomocí funkcí hostované uživatelského rozhraní se snadno používá a doporučuje se pro vaše prostředí hledání zprovoznění v krátkém pořadí. Informace o konfiguraci vašeho uživatelského rozhraní hostované zahrnout videa najdete v tématu [hostované uživatelské rozhraní konfigurace](hosted-ui.md).

Pokud chcete mít větší kontrolu nad zobrazením výsledků hledání, můžete použít rozhraní API Bingu pro vyhledávání videí pro vlastní. Vzhledem k tomu, že volání rozhraní API je podobný volání API pro vyhledávání videí Bingu checkout [Bingu pro vyhledávání videí](../Bing-Video-Search/search-the-web.md) příklady volání rozhraní API. Ale předtím, než to uděláte, seznamte se s [referenční dokumentace rozhraní API pro vyhledávání videí vlastní](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-videos-api-v7-reference) obsah. Hlavní rozdíly jsou podporované parametry dotazu (musí obsahovat parametr dotazu customConfig) a odesílat požadavky na koncový bod.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->