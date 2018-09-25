---
title: Co je Vlastní vyhledávání Bingu? | Dokumenty Microsoft
description: Poskytuje základní přehled o vlastní vyhledávání Bingu
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/29/2017
ms.author: v-brapel
ms.openlocfilehash: b6f50844d6571cca6d63c1db7a85863e3d22d411
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948073"
---
# <a name="what-is-bing-custom-search"></a>Co je Vlastní vyhledávání Bingu?

Vlastní vyhledávání Bingu umožňuje vytvořit míru vyhledávací prostředí pro témata, které vás zajímají. Například pokud web, který poskytuje možnosti vyhledávání, které vlastníte, můžete domén, webů a webových stránek, který vyhledává Bing. Výsledky hledání přizpůsobená pro obsah, o kterou vám jde namísto toho, aby se uživatelům zobrazí na stránce přes výsledky hledání, které mají irelevantní obsah.

Chcete-li vytvořit vlastní zobrazení webu, použijte vlastní vyhledávání Bingu [portál](https://customsearch.ai). Na portálu umožňuje vytvořit instanci vlastního vyhledávání, která určuje domén, webů a webové stránky, které chcete, aby Bingu pro vyhledávání a weby, které nechcete, aby ji k prohledávání. Kromě zadání adresy URL, o kterém víte o obsahu, můžete také můžete na portálu najít relevantní obsah, který chcete přidat.

Na portálu můžete taky připnout konkrétní webové stránky k hornímu okraji výsledek hledání, pokud uživatel zadá konkrétní hledaný termín. 

Po definování instance, můžete integrovat vlastní vyhledávání do vašich webů, aplikací klasické pracovní plochy nebo mobilních aplikací voláním rozhraní API pro vlastní vyhledávání. Pokud máte na webu nebo aplikace, můžete nechat hostované uživatelského rozhraní vykreslení rozhraní vyhledávání za vás.

Následující obrázek ukazuje jednoduchost integrace vlastního hledání.

![Obrázek alt](./media/bcs-overview.png "funguje jak vlastní vyhledávání Bingu.")

## <a name="adding-custom-search-box-suggestions"></a>Přidání vlastní vyhledávací pole návrhy

Můžete vylepšit prostředí pro vlastní vyhledávání s návrhy vlastní vyhledávací pole. Tato funkce umožňuje poskytovat návrhy vlastní hledání, které jsou relevantní pro vaše prostředí hledání. Jako zadaného uživatelem do vyhledávacího pole rozevírací seznam obsahuje navrhované dotazu řetězce na základě řetězce dotazu částečné uživatele. Můžete zadat, jestli se má vrátit pouze své vlastní návrhy nebo také obsahují návrhy Bingu. [Přečtěte si další informace](define-custom-suggestions.md).

## <a name="adding-custom-image-search-experience"></a>Přidání vyhledávací funkce vlastní image

Můžete vylepšit prostředí pro vlastní vyhledávání s obrázky. Podobně jako webové výsledky hledání obrázků ve vaší instanci seznamu webů, podporuje vlastní vyhledávání. [Přečtěte si další informace](get-images-from-instance.md).

## <a name="adding-custom-video-search-experience"></a>Přidání prostředí pro vlastní vyhledávání videí

Vlastní vyhledávání s využitím videí, lze rozšířit. Podobně jako webové výsledky, podporuje vlastní vyhledávání, vyhledávání videí ve vaší instanci seznamu webů. [Přečtěte si další informace](get-videos-from-instance.md).

## <a name="sharing-your-custom-search-instance-with-others"></a>Sdílení s jinými uživateli vaší instance vlastního vyhledávání

Můžete snadno povolit spolupráci, úpravu a testování vaší instance při sdílení se členy vašeho týmu. [Přečtěte si další informace](share-your-custom-search.md).

## <a name="next-steps"></a>Další postup

Abyste mohli rychle začít, najdete v článku [vytvořit první instanci vlastního vyhledávání Bingu](quick-start.md).

Podrobnosti o přizpůsobení vaší instance hledání najdete v tématu [definovat instanci vlastního vyhledávání](define-your-custom-view.md).

Seznamte se s referenční obsah pro každou z koncových bodů vlastního hledání. Odkaz obsahuje koncové body, záhlaví a parametry dotazu, které můžete využít k vyžádání výsledky hledání. Obsahují také definice objektů odpovědi.

- [Rozhraní API pro vlastní vyhledávání](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference)
- [Rozhraní API pro vlastní Image](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-images-api-v7-reference)
- [Vlastní Vidoe rozhraní API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-videos-api-v7-reference)
- [Vlastní rozhraní API pro automatické návrhy](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-autosuggest-api-v7-reference)


Nezapomeňte si přečíst [požadavky Bingu na zobrazení a použití](./use-and-display-requirements.md), abyste neporušili žádná pravidla používání výsledků hledání.