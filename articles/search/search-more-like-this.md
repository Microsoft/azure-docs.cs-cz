---
title: moreLikeThis ve službě Azure Search (preview) – Azure Search
description: Předběžná dokumentace pro funkci moreLikeThis (preview) v rozhraní REST API Azure Search.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 10/27/2016
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 1b91fbbcc025456b48ac8fcfcb3f286ede893541
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53314485"
---
# <a name="morelikethis-in-azure-search-preview"></a>moreLikeThis ve službě Azure Search (preview)

`moreLikeThis=[key]` je parametr dotazu v [rozhraní API pro vyhledávání](https://docs.microsoft.com/rest/api/searchservice/search-documents). Zadáním `moreLikeThis` parametr do vyhledávacího dotazu, můžete najít dokumenty, které jsou podobné pro dokument určený pomocí klíče dokumentu. Po provedení požadavku na hledání s `moreLikeThis`, dotazu je vytvořen s hledané termíny extrahují z daného dokumentu, které popisují nejlepší tohoto dokumentu. Generování dotazu se pak použije k odeslání požadavku hledání. Ve výchozím nastavení, obsah všech `searchable` pole jsou považovány za, není-li `searchFields` parametr se používá k omezení pole. `moreLikeThis` Parametr nelze použít s parametrem vyhledávání `search=[string]`.

## <a name="examples"></a>Příklady 

Níže je příklad dotazu moreLikeThis. Tento dotaz najde dokumentů, jejichž popis pole jsou nejvíc podobný pole zdrojový dokument určený `moreLikeThis` parametru.

```  
Get /indexes/hotels/docs?moreLikeThis=1002&searchFields=description&api-version=2016-09-01-Preview
```  

```  
POST /indexes/hotels/docs/search?api-version=2016-09-01-Preview
    {  
      "moreLikeThis": "1002",  
      "searchFields": "description"  
    }  
```  

## <a name="feature-availability"></a>Dostupnost funkcí

MoreLikeThis funkce je aktuálně ve verzi preview a podporuje jenom ve verzi preview api Version, `2015-02-28-Preview` a `2016-09-01-Preview`. Vzhledem k tomu, že v požadavku je zadaná verze rozhraní API, je možné kombinovat všeobecně dostupná (GA) a rozhraní API verze preview ve stejné aplikaci. Však mohou změnit, které rozhraní API se nevztahuje žádná smlouva SLA a funkce ve verzi preview, proto nedoporučujeme použití v aplikacích v produkčním prostředí.