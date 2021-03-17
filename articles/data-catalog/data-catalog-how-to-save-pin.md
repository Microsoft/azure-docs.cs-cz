---
title: Uložení hledání a připnutí datových assetů v Azure Data Catalog
description: Článek zvýrazňování možností v Azure Data Catalog pro ukládání zdrojů dat a datových assetů pro pozdější použití.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 2d0baed53441893ec294784f0f8092fe89a347c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86523702"
---
# <a name="save-searches-and-pin-data-assets-in-azure-data-catalog"></a>Uložení hledání a připnutí datových assetů v Azure Data Catalog
## <a name="introduction"></a>Úvod
Azure Data Catalog poskytuje možnosti pro zjišťování zdrojů dat. Katalog můžete rychle vyhledat a vyfiltrovat, abyste našli zdroje dat a pochopili zamýšlený účel, což usnadňuje vyhledání správných dat pro danou úlohu.

Ale co dělat v případě, že potřebujete pravidelně pracovat se stejnými daty? A co když vy a jiní uživatelé pravidelně přispějete k vašim znalostem do stejných zdrojů dat v katalogu? V těchto situacích může být opakované vystavení stejných hledání neefektivní. Tady může pomáhat uložené hledání a připnuté datové prostředky.

## <a name="saved-searches"></a>Uložená hledání
Uložené hledání ve Data Catalog je opakovaně použitelná definice vyhledávání podle uživatele. Můžete definovat hledání, včetně hledaných podmínek, značek a dalších filtrů, a pak je uložit. Uloženou definici hledání můžete znovu spustit později a vrátit všechny datové prostředky, které odpovídají kritériím vyhledávání.

### <a name="create-a-saved-search"></a>Vytvoření uloženého hledání
Pokud chcete vytvořit uložené hledání, udělejte toto:
1. Na portálu Azure Data Catalog v **aktuálním okně hledání** klikněte na **Uložit**. 

    ![Aktuální nastavení vyhledávání – odkaz Uložit](./media/data-catalog-how-to-save-pin/01-save-option.png) 

2. Zadejte kritéria hledání, která chcete znovu použít, a potom klikněte na **Uložit**.

    ![Aktuální nastavení vyhledávání – uložený název hledání](./media/data-catalog-how-to-save-pin/02-name.png)

3. Po zobrazení výzvy zadejte název uloženého hledání. Vyberte název, který je smysluplný a popisuje datové prostředky, které bude hledání vracet.

### <a name="manage-saved-searches"></a>Spravovat uložená hledání
Po uložení jednoho nebo více hledání se zobrazí možnost **uložená** hledání pod **aktuálním vyhledávacím** polem. Po rozbalení seznamu se zobrazí všechna uložená hledání.

 ![Data Catalog – seznam uložených hledání](./media/data-catalog-how-to-save-pin/03-list.png)

Proveďte jednu z následujících akcí:

* Chcete-li spustit hledání, vyberte uložené hledání v seznamu.

* Chcete-li zobrazit seznam možností správy uloženého hledání, klikněte na šipku dolů vedle názvu hledání.

    ![Možnosti správy uložených hledání](./media/data-catalog-how-to-save-pin/04-managing.png)

* Pokud chcete zadat nový název pro uložené hledání, vyberte **Přejmenovat**. Definice hledání se nemění.

* Pokud chcete uložené hledání odebrat ze seznamu, vyberte **Odstranit**a potvrďte odstranění.

* Pokud chcete uložené hledání označit jako výchozí hledání, vyberte **Uložit jako výchozí**. Pokud provedete hledání "prázdného" na domovské stránce Azure Data Catalog, spustí se výchozí hledání. Kromě toho se v horní části seznamu **uložených hledání** zobrazí hledání, které je označeno jako výchozí hledání.

### <a name="organizational-saved-searches"></a>Hledání uložených v organizaci
Všichni uživatelé ve vaší organizaci můžou ukládat hledání vlastního použití. Správci Data Catalog můžou také ukládat hledání všech uživatelů v rámci organizace. Když správci uloží hledání, zobrazí se jim **v rámci možnosti společnosti sdílená složka** . Když vyberete tuto možnost, nasdílí se uložené hledání pro všechny uživatele v organizaci.

 ![Data Catalog – uložená hledání v organizaci](./media/data-catalog-how-to-save-pin/08-organizational-saved-search.png)

## <a name="pinned-data-assets"></a>Připnuté datové prostředky
Pomocí uložených hledání můžete vyhledávací definice Uložit a znovu použít. Datové assety, které jsou vraceny hledáními, se můžou v průběhu času v průběhu změny v katalogu měnit. Když přidáváte datové prostředky, můžete explicitně identifikovat konkrétní datové assety, které jim usnadní přístup, aniž byste museli používat hledání.

Připnutí datového assetu je jednoduché. Chcete-li přidat datový Asset do připnutého seznamu, stačí kliknout na ikonu **připnutí** . Ikona se zobrazí v rohu dlaždice Asset v zobrazení dlaždice a ve sloupci nejvíce vlevo v zobrazení seznamu na portálu Azure Data Catalog.

![Data Catalog – ikona pinu (data-Asset)](./media/data-catalog-how-to-save-pin/05-pinning.png)

Odpnutí datového assetu je stejně jasné. Jednoduše klikněte na ikonu **odepnout** a přepněte nastavení vybraného assetu.

![Data Catalog – ikona odepnout datový Asset – ikona](./media/data-catalog-how-to-save-pin/06-unpinning.png)

## <a name="the-my-assets-section"></a>Oddíl moje Assety
Na domovské stránce portálu Data Catalog se nachází oddíl **Moje assety** , ve kterém se zobrazují prostředky důležité pro aktuálního uživatele. Tato část obsahuje připnuté prostředky i uložená hledání.

![Část moje aktiva na domovské stránce](./media/data-catalog-how-to-save-pin/07-my-assets.png)

## <a name="summary"></a>Shrnutí
Azure Data Catalog poskytuje funkce, které usnadňují zjišťování zdrojů dat, které potřebujete, takže vy a ostatní členové organizace můžete strávit méně času hledáním dat a více času s tím, jak s ní pracujete. Uložená hledání a připnuté datové prostředky se vytvářejí na těchto základních funkcích, aby uživatelé mohli snadno identifikovat zdroje dat, se kterými pracují opakovaně.
