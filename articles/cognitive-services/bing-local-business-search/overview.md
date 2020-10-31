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
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 6b715d4b801159b451957c30ee9ef7b406f3a2f4
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095369"
---
# <a name="what-is-bing-local-business-search"></a>Co je vyhledávání v místním podnikání Bingu?

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](https://aka.ms/cogsvcs/bingmove)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](https://aka.ms/cogsvcs/bingmigration).
Rozhraní API pro místní vyhledávání Bingu je služba RESTful, která umožňuje vašim aplikacím najít informace o místních firmách na základě vyhledávacích dotazů. Například `q=<business-name> in Redmond, Washington` , nebo `q=Italian restaurants near me` . 

## <a name="features"></a>Funkce
| Funkce | Popis |  
| -- | -- | 
| [Najít místní firmy a umístění](quickstarts/local-quickstart.md) | Rozhraní API pro místní vyhledávání Bingu získá lokalizované výsledky dotazu. Mezi výsledky patří adresa URL webu firmy a zobrazený text, telefonní číslo a zeměpisná poloha, včetně: souřadnice GPS, město, Adresa ulice |  
| [Filtrování místních výsledků s geografickými hranicemi](specify-geographic-search.md) | Přidáním souřadnic jako parametrů hledání můžete výsledky omezit na konkrétní geografickou oblast určenou kruhovou oblastí nebo čtvercovým ohraničovacím rámečkem. | 
| [Filtrovat výsledky místních obchodních výsledků podle kategorií](local-categories.md) | Hledání místních obchodních výsledků podle kategorií Tato možnost používá k vrácení lokalizovaných výsledků do různých kategorií firmy reverzní umístění IP nebo souřadnice GPS volajícího.|

## <a name="workflow"></a>Pracovní postup
Volejte rozhraní API pro místní vyhledávání Bingu z libovolného programovacího jazyka, který může vytvářet požadavky HTTP a analyzovat odpovědi JSON. Tato služba je přístupná pomocí REST API.
 
1. Vytvořte [účet Cognitive Services rozhraní API](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)  s přístupem k rozhraní API pro vyhledávání Bingu. Pokud nemáte předplatné Azure, můžete si [vytvořit bezplatný účet](https://azure.microsoft.com/free/cognitive-services/).   
2. Adresa URL pro parametr dotazu zakóduje hledané výrazy `q=""` . Příkladem je `q=nearby+restaurant` nebo `q=nearby%20restaurant`. Nastavte také stránkování, pokud je to potřeba. 
3. Odeslat [žádost do rozhraní API služby Bing pro hledání v místních obchodech](quickstarts/local-quickstart.md) 
4. Analyzovat odpověď JSON 

> [!NOTE]
> V současné době místní hledání firmy: 
> * Podporuje jenom `en-US` trh. 
> * Nepodporuje Automatické návrhy Bingu. 

## <a name="next-steps"></a>Další kroky
- [Dotaz a odpověď](local-search-query-response.md)
- [Rychlý start hledání místních obchodních obchodů](quickstarts/local-quickstart.md)
- [Referenční informace k rozhraní API pro místní obchodní vyhledávání](local-search-reference.md)
- [Požadavky na zobrazení a použití](use-display-requirements.md)
