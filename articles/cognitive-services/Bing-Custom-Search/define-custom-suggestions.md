---
title: Definování vlastních návrhů automatických návrhů – vlastní vyhledávání Bingu
titleSuffix: Azure Cognitive Services
description: Vlastní autosuggest vrátí seznam navržených řetězců vyhledávacích dotazů, které jsou relevantní pro vaše vyhledávání.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: maheshb
ms.openlocfilehash: 5b2b8871d868e827532f23c7ef4f14fb00afb5bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74072809"
---
# <a name="configure-your-custom-autosuggest-experience"></a>Konfigurace vlastního automatického návrhu

Vlastní autosuggest vrátí seznam navržených řetězců vyhledávacích dotazů, které jsou relevantní pro vaše vyhledávání. Navrhované řetězce dotazu jsou založeny na částečném řetězci dotazu, který uživatel zadá do vyhledávacího pole. Seznam bude obsahovat maximálně 10 návrhů. 

Určíte, zda chcete vrátit pouze vlastní návrhy nebo zahrnout také návrhy Bingu. Pokud zahrnete návrhy bingu, zobrazí se před návrhy Bingu vlastní návrhy. Pokud poskytnete dostatek relevantních návrhů, je možné, že vrácený seznam návrhů nebude obsahovat návrhy bingu. Návrhy Bingu jsou vždy v kontextu vaší instance vlastního hledání. 

Chcete-li nakonfigurovat návrhy vyhledávacích dotazů pro vaši instanci, klikněte na kartu **Automatické návrhy.**  

> [!NOTE]
> Chcete-li tuto funkci používat, musíte se přihlásit k odběru vlastního vyhledávání na příslušné úrovni (viz [ceny).](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)

Může trvat až 24 hodin, než se návrhy projeví v koncovém bodu sloužícího (rozhraní API nebo hostované hojné umělá vi).

## <a name="enable-bing-suggestions"></a>Povolení návrhů Bingu

Chcete-li povolit návrhy Bingu, přepněte jezdec **Automatické návrhy Bingu** do polohy Zapnuto. Jezdec zmodrá.

## <a name="add-your-own-suggestions"></a>Přidání vlastních návrhů

Chcete-li přidat vlastní návrhy řetězců dotazu, přidejte je do seznamu v části **Návrhy definované uživatelem**. Po přidání návrhu do seznamu stiskněte klávesu **+** Enter nebo klikněte na ikonu. Návrh můžete zadat v libovolném jazyce. Můžete přidat maximálně 5 000 návrhů řetězců dotazu.

## <a name="upload-suggestions"></a>Nahrát návrhy

Jako možnost můžete nahrát seznam návrhů ze souboru. Soubor musí obsahovat jeden řetězec vyhledávacího dotazu na řádek. Chcete-li soubor nahrát, klepněte na ikonu nahrávání a vyberte soubor, který chcete nahrát. Služba extrahuje návrhy ze souboru a přidá je do seznamu.

## <a name="remove-suggestions"></a>Odebrání návrhů

Chcete-li odebrat návrh řetězce dotazu, klepněte na ikonu odebrat vedle návrhu, který chcete odebrat.

## <a name="block-suggestions"></a>Blokovat návrhy

Pokud zahrnete návrhy Bingu, můžete přidat seznam řetězců vyhledávacích dotazů, které nechcete, aby bing vracel. Chcete-li přidat blokované řetězce dotazů, klepněte na tlačítko **Zobrazit blokované návrhy**. Přidejte řetězec dotazu do seznamu a stiskněte **+** klávesu Enter nebo klikněte na ikonu. Můžete přidat maximálně 50 blokovaných řetězců dotazu.



[!INCLUDE [publish or revert](./includes/publish-revert.md)]

>[!NOTE]  
>Může trvat až 24 hodin, než se změny konfigurace vlastní autosuggest projeví.


## <a name="enabling-autosuggest-in-hosted-ui"></a>Povolení automatického návrhu v hostovaném unovém uzly

Pokud chcete povolit návrhy řetězců dotazů pro hostované uI, klikněte na **Hostované uI**. Posuňte se dolů do části **Další konfigurace.** V části **Hledání na webu**vyberte Možnost **Zapnuto** pro **povolení automatického návrhu**. Chcete-li povolit automatické návrhy, musíte vybrat rozložení, které obsahuje vyhledávací pole.


## <a name="calling-the-autosuggest-api"></a>Volání rozhraní API automatického návrhu

Chcete-li získat navrhované řetězce dotazů pomocí rozhraní `GET` API pro vlastní vyhledávání Bingu, odešlete požadavek na následující koncový bod.

```
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions 
```

Odpověď obsahuje seznam `SearchAction` objektů, které obsahují navrhované řetězce dotazu.

```
        {  
            "displayText" : "sailing lessons seattle",  
            "query" : "sailing lessons seattle",  
            "searchKind" : "CustomSearch"  
        },  
```

Každý návrh `displayText` obsahuje `query` pole a. Toto `displayText` pole obsahuje navrhovaný řetězec dotazu, který používáte k naplnění rozevíracího seznamu vyhledávacího pole.

Pokud uživatel vybere navrhovaný řetězec dotazu z rozevíracího seznamu, `query` použijte řetězec dotazu v poli při volání [rozhraní API pro vlastní vyhledávání Bingu](overview.md).


## <a name="next-steps"></a>Další kroky

- [Získání vlastních návrhů](./get-custom-suggestions.md)
- [Hledání vlastní instance](./search-your-custom-view.md)
- [Konfigurace a využití vlastního hostovaného rozhraní](./hosted-ui.md)
