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
ms.date: 11/29/2019
ms.author: aahi
ms.openlocfilehash: 4e08596e8cf71bbb0e88abdc51f5d8e69972464d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74665251"
---
# <a name="what-is-bing-local-business-search"></a>Co je vyhledávání v místní firmě Bingu?
Rozhraní API pro vyhledávání místních firem Bing u je služba RESTful, která umožňuje aplikacím vyhledávat informace o místních firmách na základě vyhledávacích dotazů. Například `q=<business-name> in Redmond, Washington`, `q=Italian restaurants near me`nebo . 

## <a name="features"></a>Funkce
| Funkce | Popis |  
| -- | -- | 
| [Vyhledání místních firem a lokalit](quickstarts/local-quickstart.md) | Rozhraní API pro vyhledávání místních společností Bing získává lokalizované výsledky z dotazu. Výsledky zahrnují adresu URL webových stránek firmy a zobrazovaný text, telefonní číslo a zeměpisnou polohu, včetně: GPS souřadnic, města, adresy |  
| [Filtrování místních výsledků pomocí zeměpisných hranic](specify-geographic-search.md) | Přidáním souřadnic jako parametrů hledání omezíte výsledky na určitou zeměpisnou oblast určenou kruhovou oblastí nebo čtvercovým ohraničovacím rámečkem. | 
| [Filtrování výsledků místní firmy podle kategorie](local-categories.md) | Vyhledejte výsledky místní firmy podle kategorie. Tato možnost používá reverzní IP umístění nebo GPS souřadnice volajícího vrátit lokalizované výsledky v různých kategoriích podnikání.|

## <a name="workflow"></a>Pracovní postup
Volání rozhraní API pro vyhledávání místních společností Bingu z libovolného programovacího jazyka, který může provádět požadavky HTTP a analyzovat odpovědi JSON. Tato služba je přístupná pomocí rozhraní REST API.
 
1. Vytvořte [účet rozhraní API služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s přístupem k rozhraním API pro vyhledávání Bingu. Pokud nemáte předplatné Azure, můžete si [vytvořit bezplatný účet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).   
2. Adresa URL kóduje hledané termíny pro parametr dotazu. `q=""` Příkladem je `q=nearby+restaurant` nebo `q=nearby%20restaurant`. V případě potřeby nastavte také stránkování. 
3. Odeslání [požadavku do rozhraní API pro vyhledávání místních společností Bingu](quickstarts/local-quickstart.md) 
4. Analyzovat odpověď JSON 

> [!NOTE]
> V současné době vyhledávání místních podniků: 
> * Podporuje pouze `en-US` trh. 
> * Nepodporuje automatické návrhy Bingu. 

## <a name="next-steps"></a>Další kroky
- [Dotaz a odpověď](local-search-query-response.md)
- [Rychlý start hledání místní firmy](quickstarts/local-quickstart.md)
- [Referenční informace k rozhraní API pro místní obchodní vyhledávání](local-search-reference.md)
- [Požadavky na použití a zobrazení](use-display-requirements.md)
