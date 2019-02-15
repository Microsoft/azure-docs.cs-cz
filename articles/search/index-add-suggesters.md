---
title: Přidat moduly pro návrhy do indexu Azure Search
description: Povolí pole pro akce našeptávání dotazů, kde navrhované dotazy se skládají z text z pole v indexu Azure Search.
ms.date: 02/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 7128e4d3b0675775dc713451ef672b28a4991499
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56269922"
---
# <a name="add-suggesters-to-an-azure-search-index"></a>Přidat moduly pro návrhy do indexu Azure Search

A **modulu pro návrhy** je konstrukce Azure Search podporuje "vyhledávání jako vám – typ" [návrhy](https://docs.microsoft.com/rest/api/searchservice/suggestions) funkce a [(preview) funkce automatického dokončování](search-autocomplete-tutorial.md) funkce. Než bude možné volat rozhraní API pro návrhy, je nutné definovat **modulu pro návrhy** v indexu umožňující návrhy na konkrétních polí.

I když **modulu pro návrhy** má několik vlastností, je primárně kolekce pole, pro které chcete povolit [návrhy API](https://docs.microsoft.com/rest/api/searchservice/suggestions). Cestovní aplikaci může být například vhodné typeahead hledání na cíle, města a atrakce. Všechny tři pole by jako takové, přejděte v kolekci polí.

Může mít pouze jeden **modulu pro návrhy** prostředků pro každý index (konkrétně jeden **modulu pro návrhy** v **moduly pro návrhy** kolekce).

## <a name="creating-a-suggester"></a>Vytváření modulu pro návrhy 

Můžete vytvořit **modulu pro návrhy** kdykoli, ale dopad na indexu se liší v závislosti na pole. 

+ Nová pole přidat do modulu pro návrhy v rámci stejné aktualizace jsou nejmenší dopad, v tom, že žádné znovuvytvoření indexu je povinný.
+ Existující pole přidána do modulu pro návrhy, ale mění definici pole, proto je nutné úplné opětovné sestavení indexu.

 **Moduly pro návrhy** fungují lépe, když se používá pro návrh konkrétní dokumenty namísto podmínky dojde ke ztrátě nebo fráze. Nejlepší kandidát pole jsou názvy, názvy a jiné relativně krátké fráze, identifikují položky. Méně účinné jsou opakované pole, jako je například kategorie a značky, nebo velmi dlouhý pole, jako je pole Popis a v komentářích.  

Po vytvoření modulu pro návrhy, přidejte [návrhy API](https://docs.microsoft.com/rest/api/searchservice/suggestions) v logice dotaz, vyvolat funkci.  

Vlastnosti, které definují **modulu pro návrhy** patří následující:  

|Vlastnost|Popis|  
|--------------|-----------------|  
|`name`|Název **modulu pro návrhy**. Použijte název **modulu pro návrhy** při volání [návrhy &#40;rozhraní REST API služby Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/suggestions).|  
|`searchMode`|Strategie použitá k vyhledání kandidátských frází. Jediný momentálně podporovaný režim je `analyzingInfixMatching`, který provádí flexibilní porovnávání frází na začátku nebo uprostřed vět.|  
|`sourceFields`|Seznam jednoho nebo více polí, které jsou zdrojem obsahu pro návrhy. Pouze pole typu `Edm.String` a `Collection(Edm.String)` může být zdroje pro návrhy. Lze použít pouze pole, které nemají vlastní analyzátor jazyka nastavit. |  

## <a name="suggester-example"></a>Příklad modulu pro návrhy  
 A **modulu pro návrhy** je součástí definice indexu. Pouze jeden **modulu pro návrhy** může existovat v **moduly pro návrhy** kolekce v aktuální verzi, společně s **pole** kolekce a **scoringProfiles**.  

```  
{  
  "name": "hotels",  
  "fields": [  
     . . .   
   ],  
  "suggesters": [  
    {  
    "name": "sg",  
    "searchMode": "analyzingInfixMatching",  
    "sourceFields": ["hotelName", "category"]  
    }  
  ],  
  "scoringProfiles": [  
     . . .   
  ]  
}  

```  

## <a name="see-also"></a>Další informace najdete v tématech  
 [Vytvořit Index &#40;rozhraní REST API služby Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)   
 [Aktualizovat Index &#40;rozhraní REST API služby Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/update-index)   
 [Návrhy &#40;rozhraní REST API služby Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/suggestions)   
 [Index operace &#40;rozhraní REST API služby Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/index-operations)   
 [Rozhraní REST služby Azure Search](https://docs.microsoft.com/rest/api/searchservice/)   
 [Služba Azure Search .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
