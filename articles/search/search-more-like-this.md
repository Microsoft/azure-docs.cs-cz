---
title: moreLikeThis ve službě Azure Search (preview) | Dokumentace Microsoftu
description: Předběžná dokumentace pro funkci moreLikeThis (preview) v rozhraní REST API Azure Search.
author: mhko
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 10/27/2016
ms.author: nateko
ms.openlocfilehash: d8b7dd754700a5b8cc781a0b13bd1b3ffecb2806
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49468360"
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