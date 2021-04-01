---
title: 'Postupy: procházení Data Catalog'
description: Tento článek poskytuje přehled o tom, jak procházet Data Catalog Azure dosah na základě typu assetu.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/30/2020
ms.openlocfilehash: dd2fd4fbc9d8aecd9821d668dd02bd4b68659cf6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97695061"
---
# <a name="browse-the-azure-purview-data-catalog"></a>Procházet Azure dosah Data Catalog

Tento článek popisuje, jak zjišťovat data ve službě Azure dosah Data Catalog pomocí hierarchického názvového prostoru zdroje dat.

## <a name="browse-experience"></a>Možnosti procházení

Příjemce dat může vyhledat data pomocí známého hierarchického oboru názvů pro každý ze zdrojů dat pomocí zobrazení Průzkumníka. Jakmile je zdroj dat zaregistrován a prohledáván, mapa dat extrahuje informace o struktuře (hierarchický obor názvů) zdroje dat. Tyto informace slouží k sestavování prostředí procházení pro zjišťování dat.

Můžete například snadno najít datovou sadu s názvem *DateDimension* ve složce s názvem *Dimensions* v Azure Data Lake Storage Gen 2. Můžete použít možnosti procházet podle typu Assetu a přejít na účet úložiště ADLS Gen 2 a pak přejít na službu > kontejnerem > složky, abyste dosáhli konkrétní složky *Dimensions* a pak viděli tabulku *DateDimension* .

Nativní možnosti procházení s hierarchickým oborem názvů jsou k dispozici pro každý z odpovídajících zdrojů dat.

## <a name="browse-the-data-catalog-by-asset-type"></a>Procházet Data Catalog podle typu assetu

1. Datové assety můžete procházet výběrem možnosti **Procházet podle typu assetu** na domovské stránce.

    :::image type="content" source="media/how-to-browse-catalog/studio-home-page.png" alt-text="Domovská stránka dosah" border="true":::

1. Na stránce **Procházet typy assetů** jsou dlaždice rozdělené podle zdrojů dat. Chcete-li dále prozkoumat prostředky v každém zdroji dat, vyberte odpovídající dlaždici.

    :::image type="content" source="media/how-to-browse-catalog/browse-asset-types.jpg" alt-text="Stránka procházení typů prostředků" border="true":::

1. Na další stránce jsou uvedeny prostředky nejvyšší úrovně pod vaším zvoleným datovým typem. Vyberte jeden z prostředků, abyste mohli dále prozkoumat jeho obsah.

    :::image type="content" source="media/how-to-browse-catalog/asset-type-specific-browse.jpg" alt-text="Stránka pro procházení specifická pro typ prostředku Zobrazený příklad je Azure Storage účet." border="true":::

1. Otevře se zobrazení Průzkumníka. Spusťte procházení výběrem assetu na levém panelu. Podřízené prostředky budou uvedeny na pravém panelu stránky.

    :::image type="content" source="media/how-to-browse-catalog/explorer-view.jpg" alt-text="Zobrazení Průzkumníka" border="true":::

1. Chcete-li zobrazit podrobnosti o prostředku, vyberte tlačítko název nebo tři tečky na pravé straně.

    :::image type="content" source="media/how-to-browse-catalog/view-asset-detail-click-ellipses.jpg" alt-text="Kliknutím na tlačítko se třemi tečkami zobrazíte stránku s podrobnostmi o prostředcích" border="true":::

## <a name="view-related-data-assets"></a>Zobrazení souvisejících datových prostředků

Po zobrazení stránky s podrobnostmi o assetu můžete také zobrazit další související datové prostředky. Můžete například přejít do nadřazené složky *DateDimension* a zobrazit složku *Dimensions* nebo dokonce přejít do kontejneru a zobrazit tak prostředky v konkrétní hierarchii.

1. Na stránce s podrobnostmi o aktivech vyberte kartu **související** a zobrazte další související prostředky.

    :::image type="content" source="media/how-to-browse-catalog/launch-related-tab.jpg" alt-text="Spustit související kartu" border="true":::

1. Na levé straně aktuálního prostředku bude uvedena úplná hierarchie.

    :::image type="content" source="media/how-to-browse-catalog/hierarchical-structure.jpg" alt-text="Hierarchická struktura" border="true":::

1. Vyberte libovolnou úroveň v hierarchii, aby se v rámci této úrovně vybraly okamžité prostředky.

    :::image type="content" source="media/how-to-browse-catalog/select-different-hierarchy.jpg" alt-text="Vybrat jinou hierarchii" border="true":::

## <a name="next-steps"></a>Další kroky

- [Jak vytvářet, importovat a exportovat terminologie glosáře](how-to-create-import-export-glossary.md)
- [Správa termínových šablon pro obchodní Glosář](how-to-manage-term-templates.md)
