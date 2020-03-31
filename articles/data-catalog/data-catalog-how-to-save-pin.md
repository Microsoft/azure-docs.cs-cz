---
title: Ukládání vyhledávání a připnutí datových prostředků v Katalogu dat Azure
description: Možnosti zvýraznění článku v Katalogu dat Azure pro ukládání zdrojů dat a datových prostředků pro pozdější použití.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: a8545939fd027989519e0f7a161f750ec676749a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68976814"
---
# <a name="save-searches-and-pin-data-assets-in-azure-data-catalog"></a>Ukládání vyhledávání a připnutí datových prostředků v Katalogu dat Azure
## <a name="introduction"></a>Úvod
Azure Data Catalog poskytuje funkce pro zjišťování zdrojů dat. Katalog můžete rychle vyhledat a filtrovat, abyste našli zdroje dat a porozuměli jejich zamýšlenému účelu, což usnadňuje nalezení správných dat pro práci.

Ale co když potřebujete pravidelně pracovat se stejnými daty? A co když vy a ostatní uživatelé pravidelně přispíváte svými znalostmi do stejných zdrojů dat v katalogu? V těchto situacích nutnost opakovaně vydávat stejná hledání může být neefektivní. To to je místo, kde uložené vyhledávání a připnuté datové prostředky mohou pomoci.

## <a name="saved-searches"></a>Uložená hledání
Uložené hledání v katalogu dat je opakovaně použitelná definice vyhledávání pro jednotlivé uživatele. Můžete definovat hledání, včetně vyhledávacích dotazů, značek a dalších filtrů, a pak je uložit. Uloženou definici hledání můžete později znovu spustit a vrátit tak všechny datové prostředky, které odpovídají jejím kritériím vyhledávání.

### <a name="create-a-saved-search"></a>Vytvoření uloženého hledání
Chcete-li vytvořit uložené hledání, postupujte takto:
1. Na portálu Katalog dat Azure klikněte v okně **Aktuální hledání** na **Uložit**. 

    ![Aktuální nastavení hledání Uložit odkaz](./media/data-catalog-how-to-save-pin/01-save-option.png) 

2. Zadejte kritéria hledání, která chcete znovu použít, a klepněte na tlačítko **Uložit**.

    ![Aktuální nastavení hledání uložené název hledání](./media/data-catalog-how-to-save-pin/02-name.png)

3. Po zobrazení výzvy zadejte název uloženého hledání. Vyberte název, který je smysluplný a který popisuje datové prostředky, které budou vráceny hledáním.

### <a name="manage-saved-searches"></a>Správa uložených hledání
Po uložení jednoho nebo více hledání se pod polem Aktuální **hledání** zobrazí možnost **Uložená hledání.** Po rozbalení seznamu se zobrazí všechna uložená hledání.

 ![Katalog dat - Seznam uložených vyhledávání](./media/data-catalog-how-to-save-pin/03-list.png)

Proveďte jednu z následujících akcí:

* Chcete-li provést hledání, vyberte uložené hledání v seznamu.

* Chcete-li zobrazit seznam možností správy uloženého hledání, klikněte na šipku dolů vedle názvu hledání.

    ![Možnosti správy uložených hledání](./media/data-catalog-how-to-save-pin/04-managing.png)

* Chcete-li zadat nový název uloženého hledání, vyberte **přejmenovat**. Definice hledání se nezmění.

* Pokud chcete uložené hledání ze seznamu odebrat, vyberte **Odstranit**a potvrzte odstranění.

* Chcete-li uložené hledání označit jako výchozí, vyberte **uložit jako výchozí**. Pokud provedete "prázdné" hledání z domovské stránky katalogu dat Azure, spustí se výchozí hledání. Kromě toho se v horní části seznamu **Uložených vyhledávání** zobrazí hledání označené jako výchozí hledání.

### <a name="organizational-saved-searches"></a>Organizační uložená hledání
Všichni uživatelé ve vaší organizaci mohou vyhledávat ukládat pro vlastní potřebu. Správci katalogu dat mohou také ukládat vyhledávání pro všechny uživatele v organizaci. Když správci uloží vyhledávání, zobrazí se jim **share v rámci možnosti společnosti.** Výběrem této možnosti sdílíte uložené hledání pro všechny uživatele v organizaci.

 ![Katalog dat - organizační uložená hledání](./media/data-catalog-how-to-save-pin/08-organizational-saved-search.png)

## <a name="pinned-data-assets"></a>Připnuté datové prostředky
Uložených hledání můžete ukládat a znovu používat definice vyhledávání. Datové prostředky, které jsou vráceny hledání může změnit v průběhu času jako obsah katalogu změnit. Když připnete datové prostředky, můžete explicitně identifikovat konkrétní datové datové prostředky, abyste jim usnadnili přístup bez nutnosti použití vyhledávání.

Připnutí datového prostředku je jednoduché. Chcete-li datový datový zdroj přidat do připnutého seznamu, stačí kliknout na ikonu **připnutí.** Ikona se zobrazí v rohu dlaždice datového zdroje v zobrazení dlaždic a ve sloupci zcela vlevo v zobrazení seznamu na portálu Katalog dat Azure.

![Katalog dat – ikona pinu datového prostředku](./media/data-catalog-how-to-save-pin/05-pinning.png)

Odepnutí datového prostředku je stejně jednoduché. Stačí kliknout na ikonu **odepnutí** a přepnout nastavení vybraného datového zdroje.

![Katalog dat – ikona odepnutí datového prostředku](./media/data-catalog-how-to-save-pin/06-unpinning.png)

## <a name="the-my-assets-section"></a>Sekce Můj majetek
Domovská stránka portálu Katalog dat obsahuje oddíl **Můj majetek,** který zobrazuje podklady, které zajímají aktuální ho uživatele. Tato část obsahuje připnuté datové zdroje i uložená hledání.

![Oddíl Můj majetek na domovské stránce](./media/data-catalog-how-to-save-pin/07-my-assets.png)

## <a name="summary"></a>Souhrn
Azure Data Catalog poskytuje funkce, které usnadňují zjišťování zdrojů dat, které potřebujete, takže vy a ostatní členové organizace můžete strávit méně času hledáním dat a více času s ním pracovat. Uložená vyhledávání a připnuté datové prostředky vycházejí z těchto základních funkcí, takže uživatelé mohou snadno identifikovat zdroje dat, se kterými opakovaně pracují.
