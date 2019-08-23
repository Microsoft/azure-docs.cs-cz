---
title: Co je rozhraní API Bingu pro místní obchodní vyhledávání?
titleSuffix: Azure Cognitive Services
description: Rozhraní API Bingu pro místní obchodní vyhledávání je služba RESTful, která vašim aplikacím umožňuje vyhledávat informace o místních podnicích a sídlech, a to na základě vyhledávacích dotazů.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: overview
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 8ee5dbe546b78557dde70868d01ec09ae33bee07
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906325"
---
# <a name="what-is-bing-local-business-search"></a>Co je vyhledávání v místním podnikání Bingu?
Rozhraní API pro místní vyhledávání Bingu je služba RESTful, která umožňuje vašim aplikacím najít informace o místních firmách na základě vyhledávacích dotazů. Například `q=<business-name> in Redmond, Washington`, nebo `q=Italian restaurants near me`. 

## <a name="features"></a>Funkce
| Funkce | Popis |  
| -- | -- | 
| [Najít místní firmy a umístění](quickstarts/local-quickstart.md) | Rozhraní API pro místní vyhledávání Bingu získá lokalizované výsledky dotazu. Výsledky zahrnují adresu URL webu firmy a zobrazený text, telefonní číslo a zeměpisnou polohu, včetně: Souřadnice GPS, město, adresa na ulici |  
| [Filtrování místních výsledků s geografickými hranicemi](specify-geographic-search.md) | Přidáním souřadnic jako parametrů hledání můžete výsledky omezit na konkrétní geografickou oblast určenou kruhovou oblastí nebo čtvercovým ohraničovacím rámečkem. | 
| [Filtrovat výsledky místních obchodních výsledků podle kategorií](local-categories.md) | Hledání místních obchodních výsledků podle kategorií Tato možnost používá k vrácení lokalizovaných výsledků do různých kategorií firmy reverzní umístění IP nebo souřadnice GPS volajícího.|

## <a name="workflow"></a>Pracovní postup
Volejte rozhraní API pro místní vyhledávání Bingu z libovolného programovacího jazyka, který může vytvářet požadavky HTTP a analyzovat odpovědi JSON. Tato služba je přístupná pomocí REST API.
 
1. Vytvořte [účet Cognitive Services rozhraní API](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s přístupem k rozhraní API pro vyhledávání Bingu. Pokud nemáte předplatné Azure, můžete si [vytvořit bezplatný účet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).   
2. Adresa URL pro `q=""` parametr dotazu zakóduje hledané výrazy. Například `q=nearby+restaurant` nebo `q=nearby%20restaurant`. Nastavte také stránkování, pokud je to potřeba. 
3. Odeslat [žádost do rozhraní API služby Bing pro hledání v místních obchodech](quickstarts/local-quickstart.md) 
4. Analyzovat odpověď JSON 

> [!NOTE]
> Místní vyhledávání v `en-US` současné době podporuje jenom trh. 
> [!NOTE]
> Místní vyhledávání v současné době nepodporuje automatické návrhy. 

## <a name="next-steps"></a>Další postup
- [Dotaz a odpověď](local-search-query-response.md)
- [Rychlý start hledání místních obchodních obchodů](quickstarts/local-quickstart.md)
- [Referenční informace k rozhraní API pro místní vyhledávání v obchodu](local-search-reference.md)
- [Požadavky na použití a zobrazení](use-display-requirements.md)
