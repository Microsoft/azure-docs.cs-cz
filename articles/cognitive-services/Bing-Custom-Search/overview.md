---
title: Co je Bing vlastní hledání? | Dokumenty Microsoft
description: Poskytuje přehled Bing vlastní vyhledávání
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/29/2017
ms.author: v-brapel
ms.openlocfilehash: 7cd61fc63d0d7734b842ed222c67c6753da9a418
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342864"
---
# <a name="what-is-bing-custom-search"></a>Co je Bing vlastní hledání?

Bing vlastní vyhledávání můžete vytvořit prostředí šité na míru vyhledávání pro témata, která se zajímáte o. Například pokud web, který poskytuje možnosti vyhledávání, které vlastníte, můžete domén, webů a webových stránek, které hledá Bing. Výsledky hledání podle obsahu zajímají místo nutnosti se uživatelům zobrazí stránku prostřednictvím výsledky hledání, které mají důležité obsah.

Pokud chcete vytvořit vlastní zobrazení webu, použijte funkci vyhledávání vlastní Bing [portál](https://customsearch.ai). Na portálu umožňuje vytvářet vlastní hledání instance, který určuje domén, webů a webové stránky, které chcete Bing k vyhledání a weby, které nechcete, aby ji k prohledávání. Kromě určení adresy URL obsahu, který víte o, můžete taky použít portál vyhledání relevantního obsahu, který chcete přidat.

Na portálu umožňuje taky připnout konkrétní webové stránky do horní části výsledek hledání, pokud uživatel zadá konkrétní hledaný termín. 

Po definování instance, můžete integrovat vlastní vyhledávání do vašeho webu, aplikace na ploše nebo mobilní aplikace při volání rozhraní API služby vlastní Search. Pokud máte na webu nebo aplikaci, můžete je nechat rozhraní hostované vykreslení rozhraní vyhledávání za vás.

Následující obrázek znázorňuje jednoduchost integrace vlastní vyhledávání.

![Obrázek alt](./media/bcs-overview.png "jak Bing vlastní vyhledávání funguje.")

## <a name="customize-search-suggestions"></a>Přizpůsobení návrhy vyhledávání

Pokud odebíráte vlastní vyhledávání na příslušné úrovni (najdete v článku [ceny stránky](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), můžete přizpůsobit návrhy vyhledávání v prostředí vlastní vyhledávání. Rozhraní API pro automatické návrhy vlastní vrátí seznam hodnot navrhované dotazy založené na dotazu částečný řetězec, který obsahuje uživatele. S vlastní pro automatické návrhy poskytnete návrhy vlastní vyhledávání, které jsou relevantní pro možnosti vyhledávání. Je určit, zda vrátit pouze vlastní návrhy nebo obsahují návrhy Bing. Pokud Bing návrhy jsou zahrnuty, zobrazí se vlastní návrhy před návrhy, které poskytuje služby Bing. Návrhy Bing jsou omezeny na kontext vaší instance vlastní vyhledávání.

## <a name="next-steps"></a>Další postup

Abyste mohli rychle začít, najdete v části [vytvoření vaší první instance Bing vlastní vyhledávání](quick-start.md).

Podrobnosti o dostupných možností pro přizpůsobení instance hledání najdete v tématu [definovat vlastní hledání instance](define-your-custom-view.md).

Seznamte se s [rozhraní API pro vyhledávání vlastní](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference) odkaz. Odkaz obsahuje seznam koncových bodů, hlavičky a parametry dotazu, které byste použili k žádosti o výsledky hledání. Zahrnuje také definice objektů odpovědi.

Naučte se přizpůsobovat návrhy, najdete v tématu [definovat vlastní vyhledávání návrhy](define-custom-suggestions.md).

Nezapomeňte si přečíst [požadavky zobrazení a použití Bing](./use-and-display-requirements.md) tak, že nemáte žádné z pravidel o používání výsledky hledání rozdělit.