---
title: 'Bing vlastní vyhledávání: Vlastní zobrazení hledání | Microsoft Docs'
description: Popisuje, jak hledat vlastní zobrazení webu
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 75f6c8d299c7eed901dda0631fca74b040f72e30
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342854"
---
# <a name="call-your-custom-search"></a>Volání vlastní hledání
Před provedením první volání rozhraní API pro vyhledávání vlastní získat výsledky hledání instance, které je potřeba získat předplatné klíč kognitivní služby. Získat klíč rozhraní API pro vyhledávání vlastní, najdete v části [zkuste kognitivní služby](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).

> [!NOTE]
> Existující Bing vlastní vyhledávání zákazníci, kteří mají klíč preview zřízený, nebo před 15 říjen 2017 bude moci používat jejich klíče, dokud 2017 30. listopadu, nebo dokud budou mít vyčerpá maximální počet dotazů, které jsou povoleny. Pak se musí migrovat na verzi všeobecně dostupná v Azure.

## <a name="try-it-out"></a>Vyzkoušet
Po dokončení konfigurace prostředí vlastní vyhledávání, můžete otestovat konfiguraci z portálu pro vlastní vyhledávání. Přihlaste se k [vlastní vyhledávání](https://customsearch.ai)vlastní hledání instance a klikněte **produkční** kartě. **Koncové body** se zobrazí karta. Vaše předplatné určí, které koncové body jsou k dispozici akci, najdete v článku [ceny stránky](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/). Chcete-li otestovat koncový bod, vyberte z rozevíracího seznamu a nastavte související možnosti konfigurace. 

Dále jsou dostupné možnosti.

- **Dotaz**: hledaný termín pro vyhledávání. K dispozici je pouze pro webové, Image a automatických návrhů koncové body.
- **Vlastní konfigurace ID**: ID konfigurace zvolené instance vlastní vyhledávání. Toto pole je jen pro čtení.
- **Trhu**: na trhu, odkud pocházejí výsledky. K dispozici je pouze pro webové, Image a hostované uživatelského rozhraní koncové body.
- **Klíč předplatného**: klíč předplatného se otestovat s. Může z rozevíracího seznamu vyberte klíč, nebo ji zadat ručně.
- **Bezpečné vyhledávání**: filtr použít k filtrování webových stránek pro obsah pro dospělé. K dispozici je pouze pro webové, Image a hostované uživatelského rozhraní koncové body.
- **Počet**: počet výsledků vyhledávání do v odpovědi vrátit. K dispozici je pouze pro webové a bitové kopie koncové body.
- **Posun**: počet výsledků vyhledávání do v odpovědi vrátit. K dispozici je pouze pro webové a bitové kopie koncové body.

Po zadání všech požadovaných možností pro webové, Image nebo automatických návrhů, klikněte na tlačítko **volání** zobrazíte odpověď JSON v pravém podokně. 

Pokud vyberete koncový bod hostované uživatelského rozhraní, můžete otestovat možnosti vyhledávání v pravém podokně.

## <a name="next-steps"></a>Další postup
- [Volání vlastní zobrazení pomocí C#](./call-endpoint-csharp.md)
- [Volání vlastní zobrazení s Javou](./call-endpoint-java.md)
- [Vaše vlastní zobrazení s NodeJs volání](./call-endpoint-nodejs.md)
- [Volání vlastní zobrazení s Pythonem](./call-endpoint-python.md)