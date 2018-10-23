---
title: Co je Vlastní vyhledávání Bingu?
titlesuffix: Azure Cognitive Services
description: Poskytuje základní přehled Vlastního vyhledávání Bingu.
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: overview
ms.date: 09/29/2017
ms.author: v-brapel
ms.openlocfilehash: f2946918f0c1a7a516788989042825e8f49d7b0b
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2018
ms.locfileid: "49318720"
---
# <a name="what-is-bing-custom-search"></a>Co je Vlastní vyhledávání Bingu?

Vlastní vyhledávání Bingu umožňuje vytvářet přizpůsobená vyhledávací prostředí pro témata, o která máte zájem. Pokud například vlastníte web, který poskytuje možnosti vyhledávání, můžete určit domény, weby a webové stránky, které Bing prohledává. Uživatelům se budou zobrazovat výsledky hledání přizpůsobené obsahu, který je zajímá, a nebudou tak muset procházet výsledky hledání s irelevantním obsahem.

Pokud chcete vytvořit vlastní zobrazení webu, použijte [portál](https://customsearch.ai) Vlastní vyhledávání Bingu. Na portálu můžete vytvořit instanci vlastního vyhledávání, která určuje požadované domény, weby a webové stránky, které má Bing prohledávat, a weby, které prohledávat nemá. Kromě toho, že můžete zadat adresy URL obsahu, o kterém víte, můžete pomocí portálu také vyhledat relevantní obsah, který byste mohli chtít přidat.

Portál také umožňuje připnout konkrétní webovou stránku na začátek výsledků hledání v případě, že uživatel zadá konkrétní hledaný termín. 

Po definování instance můžete vlastní vyhledávání integrovat do webu, desktopové aplikace nebo mobilní aplikace voláním rozhraní API pro vlastní vyhledávání. Pokud máte webovou stránku nebo aplikaci, můžete nechat hostované uživatelské rozhraní vykreslit vyhledávací rozhraní za vás.

Následující obrázek ukazuje, jak snadná je integrace vlastního vyhledávání.

![Alternativní text k obrázku](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/cognitive-services/Bing-Custom-Search/media/BCS-Overview.png "Jak funguje Vlastní vyhledávání Bingu")

## <a name="adding-custom-search-box-suggestions"></a>Přidání vlastních návrhů hledání do vyhledávacího pole

Vlastní vyhledávací prostředí můžete obohatit o vlastní návrhy hledání ve vyhledávacím poli. Tato funkce umožňuje poskytovat vlastní návrhy hledání, které jsou relevantní pro vaše vyhledávací prostředí. Uživateli se při psaní do vyhledávacího pole zobrazí rozevírací seznam obsahující navrhované řetězce dotazu na základě jeho částečného řetězce dotazu. Můžete určit, jestli se mají vracet pouze vlastní návrhy, nebo jestli se mají zahrnout i návrhy Bingu. [Další informace](define-custom-suggestions.md).

## <a name="adding-custom-image-search-experience"></a>Přidání vlastního vyhledávání obrázků

Vlastní vyhledávací prostředí můžete obohatit o obrázky. Podobně jako webové výsledky podporuje vlastní vyhledávání i vyhledávání obrázků v seznamu webů vaší instance. [Další informace](get-images-from-instance.md).

## <a name="adding-custom-video-search-experience"></a>Přidání vlastního vyhledávání videí

Vlastní vyhledávací prostředí můžete obohatit o videa. Podobně jako webové výsledky podporuje vlastní vyhledávání i vyhledávání videí v seznamu webů vaší instance. [Další informace](get-videos-from-instance.md).

## <a name="sharing-your-custom-search-instance-with-others"></a>Sdílení instance vlastního vyhledávání s ostatními

Sdílením instance s členy vašeho týmu můžete snadno umožnit úpravy a testování založené na spolupráci. [Další informace](share-your-custom-search.md).

## <a name="next-steps"></a>Další kroky

Pokud chcete rychle začít, přečtěte si téma [Vytvoření první instance Vlastního vyhledávání Bingu](quick-start.md).

Podrobnosti o přizpůsobení instance vyhledávání najdete v tématu [Definice instance vlastního vyhledávání](define-your-custom-view.md).

Seznamte se s referenčním obsahem pro jednotlivé koncové body vlastního vyhledávání. Tyto referenční informace obsahují koncové body, hlavičky a parametry dotazů, které můžete použít při odesílání požadavků na výsledky hledání. Obsahují také definice objektů odpovědi.

- [Rozhraní API pro vlastní vyhledávání Bingu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference)
- [Rozhraní API pro vlastní vyhledávání obrázků Bingu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-images-api-v7-reference)
- [Rozhraní API pro vlastní vyhledávání videí Bingu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-videos-api-v7-reference)
- [Rozhraní API pro vlastní automatické návrhy Bingu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-autosuggest-api-v7-reference)


Nezapomeňte si přečíst [požadavky Bingu na zobrazení a použití](./use-and-display-requirements.md), abyste neporušili žádná pravidla používání výsledků hledání.
