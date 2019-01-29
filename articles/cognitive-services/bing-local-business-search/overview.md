---
title: Co je rozhraní API Bingu pro místní obchodní vyhledávání? | Dokumenty Microsoft
titleSuffix: Azure Cognitive Services
description: Rozhraní API Bingu pro místní obchodní vyhledávání je služba RESTful, která vašim aplikacím umožňuje vyhledávat informace o místních podnicích a sídlech, a to na základě vyhledávacích dotazů.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: fd57c344c640974e9c73156902848fb1685861f2
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55163139"
---
# <a name="what-is-bing-local-business-search"></a>Co je místní firmy vyhledávání Bingu?
Místní firmy API pro vyhledávání Bingu je služba RESTful, která umožňuje aplikace vyhledejte informace o místních firmách na základě dotazů vyhledávání. Například `q=<business-name> in Redmond, Washington`, nebo `q=Italian restaurants near me`. 

## <a name="features"></a>Funkce
| Funkce | Popis |  
| -- | -- | 
| [Najít místní firmy a umístění](quickstarts/local-quickstart.md) | Místní firmy API pro vyhledávání Bingu získá lokalizovaný výsledků z dotazu. Výsledky uvést adresu URL pro web společnosti a zobrazení textu, telefonní číslo a zeměpisnou polohu, včetně: Souřadnice GPS, Město, adresu |  
| [Filtrovat výsledky místního s geografické hranice](specify-geographic-search.md) | Přidáte jako parametry hledání, chcete-li omezit rozsah výsledků na konkrétní geografické oblasti, zadaný kruhové oblasti nebo čtverec ohraničující rámeček. | 
| [Filtrovat podle kategorie místní obchodních výsledků](local-categories.md) | Místní obchodních výsledků hledání podle kategorie. Tato volba používá reverzní poloha IP adresy nebo souřadnice GPS volajícího vrací lokalizovaný výsledky v různých kategoriích podnikání.|

## <a name="workflow"></a>Pracovní postup
Volání API Bingu pro místní obchodní vyhledávání z libovolného programovacího jazyka, který může vytvářet požadavky HTTP a parsovat odpovědi JSON. Tato služba je přístupná pomocí rozhraní REST API.
 
1. Vytvoření [účet rozhraní API služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s přístupem k rozhraní API pro vyhledávání Bingu. Pokud nemáte předplatné Azure, můžete si [vytvořit bezplatný účet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).   
2. Podmínky hledání pro kódování URL `q=""` parametr dotazu. Například `q=nearby+restaurant` nebo `q=nearby%20restaurant`. Stránkování, nastavte v případě potřeby. 
3. Odeslat [požadavek místní firmy API pro vyhledávání Bingu](quickstarts/local-quickstart.md) 
4. Analyzovat odpověď JSON 

> [!NOTE]
> V současné době podporuje místní firmy hledání pouze `en-US` na trhu. 
> [!NOTE]
> Místní firmy vyhledávání v současné době nepodporuje pro automatické návrhy. 

## <a name="next-steps"></a>Další postup
- [Dotazy a odpovědi](local-search-query-response.md)
- [Rychlý start místní firmy vyhledávání](quickstarts/local-quickstart.md)
- [Místní referenční rozhraní API pro obchodní vyhledávání](local-search-reference.md)
- [Požadavky na použití a zobrazení](use-display-requirements.md)
