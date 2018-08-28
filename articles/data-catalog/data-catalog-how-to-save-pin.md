---
title: Uložení hledání a PIN kód datové assety ve službě Azure Data Catalog
description: Článek zvýraznění funkcí ve službě Azure Data Catalog pro ukládání zdroje dat a datové prostředky pro pozdější použití.
services: data-catalog
author: steelanddata
ms.author: maroche
ms.assetid: 6bd00a81-820d-4b7c-91fa-ab09e575474c
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 786d65eaf667ae8ae9dc2c91d3113f5057a98a27
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43053464"
---
# <a name="save-searches-and-pin-data-assets-in-azure-data-catalog"></a>Uložení hledání a PIN kód datové assety ve službě Azure Data Catalog
## <a name="introduction"></a>Úvod
Azure Data Catalog poskytuje možnosti pro zjišťování zdrojů dat. Můžete rychle vyhledávat a filtrovat katalogu pro vyhledání zdroje dat a pochopení jejich zamýšlený účel, což usnadňuje hledání ta správná data pro aktuální úlohu.

Ale co když je potřeba pravidelně pracovat se stejnými daty? A co když vy a ostatní uživatelé pravidelně přispívat vaše znalosti do stejného zdroje dat v katalogu? V těchto situacích by bylo nutné opakovaně vystavovat stejné hledání může být neefektivní. To je, kde může pomoct uložené výsledky hledání a připojených datových assetů.

## <a name="saved-searches"></a>Uložené výsledky hledání
Uložené výsledky hledání ve službě Data Catalog je opakovaně použitelné, definice vyhledávání na uživatele. Můžete definovat hledání včetně hledaného, značky a další filtry a pak ho uložte. Uložené výsledky hledání definici později vrátit všechny datové assety, které odpovídají jeho kritéria vyhledávání můžete spustit znovu.

### <a name="create-a-saved-search"></a>Vytvořit uložené výsledky hledání
Pokud chcete vytvořit uložené výsledky hledání, postupujte takto:
1. Na portálu Azure Data Catalog ve **aktuální hledání** okna, klikněte na tlačítko **Uložit**. 

    ![Uložení odkazu na aktuální hledání nastavení](./media/data-catalog-how-to-save-pin/01-save-option.png) 

2. Zadejte kritéria hledání, které chcete znovu použít a potom klikněte na tlačítko **Uložit**.

    ![Aktuální nastavení vyhledávání uložený název hledání](./media/data-catalog-how-to-save-pin/02-name.png)

3. Když se zobrazí výzva, zadejte název pro uložené hledání. Vyberte název, který má smysl a, který popisuje datové assety, které budou vráceny hledáním.

### <a name="manage-saved-searches"></a>Spravovat uložená hledání
Po uložení jednoho nebo více vyhledávání, **uložená hledání** možnost se zobrazí pod **aktuální hledání** pole. Po rozbalení seznamu se zobrazují všechny uložené výsledky hledání.

 ![Seznam uložených hledání](./media/data-catalog-how-to-save-pin/03-list.png)

Proveďte jednu z následujících akcí:

* Ke spuštění hledání v seznamu vyberte uložené výsledky hledání.

* Chcete-li zobrazit seznam možností správy pro uložené výsledky hledání, klikněte na šipku dolů vedle názvu vyhledávání.

    ![Možnosti pro správu uložená hledání](./media/data-catalog-how-to-save-pin/04-managing.png)

* Pokud chcete zadat nový název pro uložené výsledky hledání, vyberte **přejmenovat**. Definice vyhledávání se nezmění.

* Chcete-li odstranit uložené výsledky hledání v seznamu, vyberte **odstranit**a pak potvrďte odstranění.

* Chcete-li označit uložené výsledky hledání jako výchozí hledání, vyberte **uložit jako výchozí**. Pokud provádíte "prázdné" vyhledávání z domovské stránky Azure Data Catalog, je proveden výchozí hledání. Kromě toho hledání, který je označen jako výchozí hledání se zobrazí v horní části **uložená hledání** seznamu.

### <a name="organizational-saved-searches"></a>Organizační uložená hledání
Všechny uživatele ve vaší organizaci můžete uložit hledání pro vlastní použití. Správci služby Data Catalog můžete také uložit hledání pro všechny uživatele v rámci organizace. Když správci uložit hledání, že máte na výběr **sdílené složky v rámci společnosti** možnost. Výběrem této možnosti složky uložené hledání pro všechny uživatele v organizaci.

 ![Organizační uložená hledání](./media/data-catalog-how-to-save-pin/08-organizational-saved-search.png)

## <a name="pinned-data-assets"></a>Připojené datové assety
S uložená hledání můžete uložit a opakovaně použít vyhledávání definic. Datové assety, které jsou vráceny pomocí vyhledávání může mění v čase jako obsah změny katalogu. Při Připnutí datových assetů, můžete explicitně identifikovat konkrétní datové prostředky pro snadnější přístup bez nutnosti použít vyhledávání.

Připnutí k datovému assetu je jednoduché. Pokud chcete přidat do seznamu připojených datovému assetu, stačí kliknout **pin** ikonu. Ikona se zobrazí v horním rohu dlaždice prostředku v zobrazení tile a ve sloupci nejvíce vlevo v zobrazení seznamu na portálu Azure Data Catalog.

![Ikona Připnutí datovému assetu](./media/data-catalog-how-to-save-pin/05-pinning.png)

Odepnutí k datovému assetu je stejně jednoduché. Stačí kliknout **Odepnout** ikonu pro přepnutí nastavení pro vybraný prostředek.

![Ikona Odepnout datovému assetu](./media/data-catalog-how-to-save-pin/06-unpinning.png)

## <a name="the-my-assets-section"></a>V části Moje prostředky
Domovské stránky portálu katalogu Data Catalog zahrnuje **Moje prostředky** oddíl, který zobrazuje prostředky, které vás zajímají aktuálního uživatele. Tato část obsahuje oba připnuté prostředky a uložená hledání.

![V části Moje prostředky na domovské stránce](./media/data-catalog-how-to-save-pin/07-my-assets.png)

## <a name="summary"></a>Souhrn
Azure Data Catalog poskytuje možnosti, které usnadňují zjišťování zdrojů dat, co potřebujete, abyste vy a ostatní členové organizace může strávit míň času hledáním dat a víc času práci s ní. Uložená hledání a připnout data, která prostředků sestavení na tyto základní funkce, tak uživatelé mohli snadno identifikovat zdroje dat, které při práci s opakovaně.
