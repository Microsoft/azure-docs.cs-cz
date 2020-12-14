---
title: Jak označit více pojmů Glosář na seznam vybraných prostředků
description: Naučte se hromadně upravovat prostředky v Azure dosah.
author: nayenama
ms.author: nayenama
ms.service: data-catalog
ms.subservice: data-catalog-gen2
ms.topic: conceptual
ms.date: 11/24/2020
ms.openlocfilehash: c42a6894c33993dc9aee5a9fdd10b1c3a3627320
ms.sourcegitcommit: 287c20509c4cf21d20eea4619bbef0746a5cd46e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2020
ms.locfileid: "97372108"
---
# <a name="how-to-bulk-edit-assets-to-tag-glossary-terms"></a>Jak hromadně upravovat prostředky a označovat pojem glosáře

Tento článek popisuje, jak označit více pojmů glosáře na seznam vybraných prostředků v rámci jedné akce.

### <a name="add-assets-to-view-selected-list-using-search"></a>Přidat prostředky pro zobrazení vybraného seznamu pomocí hledání

1. Vyhledejte datový Asset, který chcete přidat do seznamu pro hromadné úpravy.

2. Na stránce výsledek hledání najeďte myší na Asset, který chcete přidat, do **vybraného** seznamu hromadných úprav a zobrazte zaškrtávací políčko.

   :::image type="content" source="media/how-to-bulk-edit-assets/asset-checkbox.png" alt-text="Snímek obrazovky se zaškrtávacím políčkem.":::

3. Zaškrtněte políčko, které chcete přidat do vybraného seznamu pro **zobrazení** hromadných úprav. Po přidání se v dolní části stránky zobrazí ikona vybrané položky.

   :::image type="content" source="media/how-to-bulk-edit-assets/selected-list.png" alt-text="Snímek obrazovky se seznamem":::

4. Opakujte výše uvedené kroky a přidejte všechny datové assety do seznamu.

### <a name="add-assets-to-view-selected-list-from-asset-detail-page"></a>Přidat Assety k zobrazení vybraného seznamu na stránce s podrobnostmi o prostředku

1. Na stránce s podrobnostmi o aktivech zaškrtněte políčko v pravém horním rohu a přidejte Asset do **vybraného** seznamu hromadných úprav.

   :::image type="content" source="media/how-to-bulk-edit-assets/asset-list.png" alt-text="Snímek obrazovky assetu":::

### <a name="bulk-edit-assets-in-the-view-selected-list-to-add-replace-or-remove-glossary-terms"></a>Hromadné úpravy prostředků v seznamu Zobrazit vybrané, pokud chcete přidat, nahradit nebo odebrat výrazy glosáře.

1. V případě, že jste hotovi s identifikací všech datových assetů, které je třeba hromadně upravovat, vyberte možnost **Zobrazit vybraný** seznam na stránce výsledků hledání nebo na stránce s podrobnostmi o prostředcích.

:::image type="content" source="media/how-to-bulk-edit-assets/view-list.png" alt-text="Snímek obrazovky zobrazení":::

2. Zkontrolujte seznam a vyberte **Odebrat** , pokud chcete odebrat jakékoli výrazy.

:::image type="content" source="media/how-to-bulk-edit-assets/remove-list.png" alt-text="Snímek obrazovky s odebráním":::

3. Vyberte Hromadná úprava pro přidání, odebrání nebo nahrazení podmínek glosáře pro všechny vybrané prostředky.

4. Pokud chcete přidat pojem Glosář, vyberte operaci jako **Přidat**. Vyberte všechny výrazy glosáře, které chcete přidat do nové hodnoty. Po dokončení vyberte použít.
    - Operace přidání bude připojovat novou hodnotu k seznamu termínů glosáře již označených datovými prostředky.  
   
    :::image type="content" source="media/how-to-bulk-edit-assets/add-list.png" alt-text="Snímek obrazovky s přidáním":::

5. Chcete-li nahradit termín glosáře, vyberte operaci jako **nahraďte**. Vyberte všechny termíny glosáře, které chcete nahradit novou hodnotou. Po dokončení vyberte použít.
    - Operace nahrazení nahradí všechny výrazy glosáře pro vybrané datové prostředky s podmínkami vybranými v nové hodnotě.
   
6. Chcete-li odebrat výrazy glosáře, vyberte operaci jako **Remove**. Po dokončení vyberte použít.
    - Operace odebrat odebere všechny výrazy glosáře pro vybrané datové prostředky.
   
    :::image type="content" source="media/how-to-bulk-edit-assets/replace-list.png" alt-text="Snímek obrazovky s podmínkami odebrání":::

7. Po dokončení zavřete okno hromadných úprav tak, že vyberete **Zavřít** nebo **Odebrat vše a zavřít**. Při zavření nedojde k odebrání vybraných assetů, zatímco možnost Odebrat vše a zavřít odstraní všechny vybrané prostředky.
    :::image type="content" source="media/how-to-bulk-edit-assets/close-list.png" alt-text="Snímek obrazovky zavřít":::

   > [!Important]
   > Doporučený počet prostředků pro hromadnou úpravu je 15. Výběr více než 15 může způsobit problémy s výkonem.
   > **Vybrané pole zobrazení** bude viditelné pouze v případě, že je vybrán alespoň jeden prostředek.


Postup najdete v tomto [kurzu: vytváření a import podmínek glosáře](how-to-create-import-export-glossary.md) a další informace.
