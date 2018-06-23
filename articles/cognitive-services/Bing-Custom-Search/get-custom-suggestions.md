---
title: 'Hledání vlastní Bing: Získat vlastní pro automatické návrhy návrhy | Microsoft Docs'
description: Popisuje, jak načíst vlastní návrhy automatických návrhů
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.technology: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 73059038018602f7aa76377bf7c98d4658576576
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342857"
---
# <a name="get-custom-suggestions"></a>Umožňuje získat návrhy vlastní
Před odesláním dotazy vyhledávání vlastní Bing, volání API pro automatické návrhy vlastní zkontrolujte návrhy termínů a zajištění lepších možností vyhledávání. Rozhraní API pro automatické návrhy vlastní vrátí seznam hodnot navrhované dotazy založené na dotazu částečný řetězec, který obsahuje uživatele. Před návrhy, které generuje automatických návrhů se zobrazí všechny relevantní vlastního dotazu podmínky, které zadáte. Další informace najdete v tématu [definovat vlastní vyhledávání návrhy](define-custom-suggestions.md).

## <a name="endpoint"></a>Koncový bod
Získat návrhy dotazy pomocí rozhraní API služby Bing vlastní Search, odeslání `GET` požadavek na následující koncový bod.

```
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions 
```

## <a name="response-json"></a>Odpověď JSON
Odpověď obsahuje seznam SearchAction objektů, které obsahují podmínky navrhované dotazu.

```
        {  
            "displayText" : "sailing lessons seattle",  
            "query" : "sailing lessons seattle",  
            "searchKind" : "CustomSearch"  
        },  
```

Zahrnuje každého návrhu `displayText` a `query` pole. `displayText` Pole obsahuje navrhované dotaz, který můžete použít k naplnění rozevíracího seznamu vyhledávacího pole.

Pokud uživatel vybere navrhované dotazu z rozevíracího seznamu, použijte termín dotazu v `query` pole při volání metody [rozhraní API služby Bing vlastní Search](overview.md).

## <a name="throttling-requests"></a>Omezování požadavků

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Další postup

- [Volání vlastní hledání](./search-your-custom-view.md)
- [Konfigurace prostředí hostované uživatelského rozhraní](./hosted-ui.md)