---
title: Definování vlastních návrhů pro automatické návrhy – Vlastní vyhledávání Bingu
titleSuffix: Azure Cognitive Services
description: Vlastní Automatický návrh vrátí seznam řetězců navrhovaných vyhledávacích dotazů, které jsou relevantní pro vaše možnosti vyhledávání.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: maheshb
ms.openlocfilehash: 5b2b8871d868e827532f23c7ef4f14fb00afb5bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "74072809"
---
# <a name="configure-your-custom-autosuggest-experience"></a>Konfigurace vlastního prostředí pro automatické návrhy

Vlastní Automatický návrh vrátí seznam řetězců navrhovaných vyhledávacích dotazů, které jsou relevantní pro vaše možnosti vyhledávání. Navrhované řetězce dotazu jsou založeny na částečném řetězci dotazu, který uživatel poskytuje ve vyhledávacím poli. Seznam bude obsahovat maximálně 10 návrhů. 

Určíte, jestli se mají vracet jenom vlastní návrhy, nebo jestli se mají zahrnout také návrhy Bingu. Pokud zahrnete návrhy Bingu, vlastní návrhy se zobrazí před návrhy Bingu. Pokud poskytnete dostatek relevantních návrhů, je možné, že vrácený seznam návrhů nebude zahrnovat návrhy Bingu. Návrhy Bingu jsou vždycky v kontextu vaší instance vlastního vyhledávání. 

Chcete-li konfigurovat návrhy vyhledávacích dotazů pro vaši instanci, klikněte na kartu automatický **Návrh** .  

> [!NOTE]
> Pokud chcete tuto funkci používat, musíte se přihlásit k odběru vlastního vyhledávání na příslušné úrovni (viz [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)).

Může trvat až 24 hodin, než se návrhy projeví v koncovém bodě obsluhujícím (rozhraní API nebo hostované uživatelské rozhraní).

## <a name="enable-bing-suggestions"></a>Povolit návrhy Bingu

Chcete-li povolit návrhy Bingu, přepněte na pozici posuvník **automatických návrhů Bingu** . Posuvník se nastaví na modrou.

## <a name="add-your-own-suggestions"></a>Přidání vlastních návrhů

Chcete-li přidat vlastní návrhy řetězce dotazu, přidejte je do seznamu v části **uživatelsky definované návrhy**. Po přidání návrhu do seznamu stiskněte klávesu ENTER nebo klikněte na **+** ikonu. Návrh můžete zadat v libovolném jazyce. Můžete přidat maximálně 5 000 návrhů řetězce dotazu.

## <a name="upload-suggestions"></a>Nahrávání návrhů

Jako možnost můžete nahrát seznam návrhů ze souboru. Soubor musí na každém řádku obsahovat jeden řetězec vyhledávacího dotazu. Pokud chcete nahrát soubor, klikněte na ikonu nahrát a vyberte soubor, který se má nahrát. Služba extrahuje návrhy ze souboru a přidá je do seznamu.

## <a name="remove-suggestions"></a>Odebrat návrhy

Chcete-li odebrat návrh řetězce dotazu, klikněte na ikonu odebrat vedle návrhu, který chcete odebrat.

## <a name="block-suggestions"></a>Zablokovat návrhy

Pokud zahrnete návrhy Bingu, můžete přidat seznam řetězců vyhledávacích dotazů, které nechcete, aby Bing vracel. Pokud chcete přidat blokované řetězce dotazů, klikněte na **Zobrazit blokované návrhy**. Přidejte řetězec dotazu do seznamu a stiskněte klávesu ENTER nebo klikněte na **+** ikonu. Můžete přidat maximálně 50 zablokovaných řetězců dotazů.



[!INCLUDE [publish or revert](./includes/publish-revert.md)]

>[!NOTE]  
>Změny konfigurace vlastních automatických návrhů může trvat až 24 hodin.


## <a name="enabling-autosuggest-in-hosted-ui"></a>Povolení automatické návrhy v hostovaném uživatelském rozhraní

Pokud chcete pro hostované uživatelské rozhraní povolit návrhy řetězce dotazu, klikněte na **hostované uživatelské rozhraní**. Přejděte dolů do části **Další konfigurační** oddíl. V části **vyhledávání na webu**vyberte možnost **zapnuto** pro možnost **Povolit automatický návrh**. Chcete-li povolit Automatický návrh, je nutné vybrat rozložení, které obsahuje vyhledávací pole.


## <a name="calling-the-autosuggest-api"></a>Volání rozhraní API pro automatické návrhy

Chcete-li získat navržené řetězce dotazu pomocí rozhraní API pro vlastní vyhledávání Bingu, odešlete `GET` požadavek do následujícího koncového bodu.

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

Každý návrh zahrnuje `displayText` pole a `query` . `displayText`Pole obsahuje navrhovaný řetězec dotazu, který použijete k naplnění rozevíracího seznamu vyhledávacího pole.

Pokud uživatel vybere navrhovaný řetězec dotazu z rozevíracího seznamu, použijte řetězec dotazu v `query` poli při volání [rozhraní API pro vlastní vyhledávání Bingu](overview.md).


## <a name="next-steps"></a>Další kroky

- [Získání vlastních návrhů](./get-custom-suggestions.md)
- [Hledání vlastní instance](./search-your-custom-view.md)
- [Konfigurace a využívání vlastního hostovaného uživatelského rozhraní](./hosted-ui.md)
