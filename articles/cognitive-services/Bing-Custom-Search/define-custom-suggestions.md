---
title: Definování vlastních automatických návrhů návrhy – vlastní vyhledávání Bingu
titlesuffix: Azure Cognitive Services
description: Popisuje, jak nakonfigurovat vlastní automatické návrhy vlastní návrhy
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 09/28/2017
ms.author: maheshb
ms.openlocfilehash: 6d7ee4ef1a84b644a7075e24c0760ea46b407754
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49465538"
---
# <a name="configure-your-custom-autosuggest-experience"></a>Konfigurace prostředí pro funkci vlastních automatických návrhů

Funkci vlastních automatických návrhů vrátí seznam hodnot navrhovaný řetězce dotazu, které jsou relevantní pro vaše prostředí hledání. Řetězce navrhovaných dotazů jsou založeny na řetězec částečné dotazu, který uživatel zadá do vyhledávacího pole. Seznam bude obsahovat maximálně 10 návrhů. 

Můžete nastavit, zda vrátit pouze vlastní návrhy nebo také obsahují návrhy Bingu. Pokud zahrnete návrhy Bingu, předcházet vlastních návrhů pro návrhy Bingu. Pokud zadáte dostatek relevantní návrhy, je možné, že vrácený seznam návrhů nezahrnuje návrhy Bingu. Návrhy Bingu se vždycky nacházejí v rámci vaší instance vlastního vyhledávání. 

Chcete-li konfigurovat návrhy hledání dotazu pro vaši instanci, klikněte na tlačítko **pro automatické návrhy** kartu.  

> [!NOTE]
> Pro tuto funkci použít, se musíte přihlásit k vlastní vyhledávání na příslušné úrovni (viz [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)).

Může trvat až 24 hodin pro návrhy, aby se změny projevovaly v koncovém bodě obsluhující (rozhraní API nebo hostované uživatelského rozhraní).

## <a name="enable-bing-suggestions"></a>Povolit návrhy Bingu

Povolit návrhy Bingu, přepnout **Bing pro automatické návrhy** posuvník do polohy zapnuto. Jezdec stane modrá.

## <a name="add-your-own-suggestions"></a>Přidat vlastní návrhy

Chcete-li přidat vlastní návrhy řetězec dotazu, přidejte do seznamu v části **uživatelem definované návrhy**. Po přidání návrh v seznamu, stiskněte klávesu enter nebo klikněte na tlačítko **+** ikonu. Návrh můžete zadat v libovolném jazyce. Můžete přidat až 5 000 návrhy řetězec dotazu.

## <a name="upload-suggestions"></a>Odeslat návrhy

Volitelně můžete nahrát seznam návrhů ze souboru. Soubor musí obsahovat jeden hledaný řetězec dotazu na řádek. Pokud chcete nahrát soubor, klikněte na ikonu nahrávání a vyberte soubor k nahrání. Služba extrahuje návrhy ze souboru a přidá je do seznamu.

## <a name="remove-suggestions"></a>Odeberte návrhy

Chcete-li odebrat návrh řetězec dotazu, klikněte na odebrat ikonu vedle návrh, který chcete odebrat.

## <a name="block-suggestions"></a>Blokovat návrhy

Pokud zahrnete návrhy Bingu, můžete přidat seznam nechcete, aby Bing k vrácení řetězce dotazu hledání. Pro přidání řetězce dotazu blokované, klikněte na tlačítko **zobrazit zablokuje návrhy**. Přidat řetězec dotazu do seznamu a stisknutím klávesy enter nebo klikněte na tlačítko **+** ikonu. Můžete přidat až 50 řetězce dotazu blokované.



[!INCLUDE[publish or revert](./includes/publish-revert.md)]

>[!NOTE]  
>Může trvat až 24 hodin vlastních automatických návrhů změny konfigurace projevily.


## <a name="enabling-autosuggest-in-hosted-ui"></a>Povolení pro automatické návrhy v Uživatelském prostředí

Povolit návrhy řetězec dotazu pro vaše prostředí uživatelské rozhraní, klikněte na tlačítko **hostované uživatelského rozhraní**. Přejděte dolů k položce **další konfiguraci** oddílu. V části **vyhledávání na webu**vyberte **na** pro **povolit pro automatické návrhy**. Pokud chcete povolit automatické návrhy, musíte vybrat rozložení, která obsahuje vyhledávací pole.


## <a name="calling-the-autosuggest-api"></a>Volání rozhraní API pro automatické návrhy

Potřebujete řetězce navrhovaných dotazů pomocí rozhraní API pro vlastní vyhledávání Bingu, pošlete `GET` požadavek na následující koncový bod.

```
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions 
```

Odpověď obsahuje seznam `SearchAction` objekty, které obsahují řetězce navrhovaných dotazů.

```
        {  
            "displayText" : "sailing lessons seattle",  
            "query" : "sailing lessons seattle",  
            "searchKind" : "CustomSearch"  
        },  
```

Obsahuje každou návrh `displayText` a `query` pole. `displayText` Pole obsahuje řetězec navrhované dotazu, který můžete použít k naplnění rozevíracího seznamu vyhledávacího pole.

Pokud si uživatel vybere z rozevíracího seznamu řetězec navrhované dotazu, použijte řetězec dotazu v `query` pole při volání [API pro vlastní vyhledávání Bingu](overview.md).


## <a name="next-steps"></a>Další postup

- [Získat vlastní návrhy](./get-custom-suggestions.md)
- [Hledat vlastní instance](./search-your-custom-view.md)
- [Konfigurovat a používat vlastní prostředí uživatelského rozhraní](./hosted-ui.md)