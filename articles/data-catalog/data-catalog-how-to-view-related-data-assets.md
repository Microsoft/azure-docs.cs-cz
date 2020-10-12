---
title: Postup zobrazení souvisejících datových assetů v Azure Data Catalog
description: Tento článek vysvětluje, jak zobrazit související datové prostředky vybraného datového assetu v Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: fb5352b4cc84801296d9e621053672328b02692a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86523362"
---
# <a name="how-to-view-related-data-assets-in-azure-data-catalog"></a>Jak zobrazit související datové prostředky v Azure Data Catalog?
Azure Data Catalog umožňuje zobrazit datové prostředky související s vybraným datovým Assetem a mezi nimi zobrazit vztahy. 

## <a name="supported-data-sources"></a>Podporované zdroje dat 
Když zaregistrujete datové assety z následujících zdrojů dat, Azure Data Catalog automaticky zaregistruje metadata o vztazích mezi vybranými datovými prostředky. 

- SQL Server
- Azure SQL Database
- MySQL
- Oracle

> [!NOTE]
> Aby bylo možné Data Catalog importovat vztah mezi dvěma datovými assety, je nutné současně zaregistrovat prostředky. Pokud jste jeden z nich přidali samostatně, přidejte ho znovu a druhý datový prostředek pro import vztahů mezi nimi.

## <a name="view-related-data-assets"></a>Zobrazení souvisejících datových prostředků
Chcete-li zobrazit datové prostředky, které souvisejí s vybranou datovou sadou, použijte kartu **relace** , jak je znázorněno na následujícím obrázku: 

![Azure Data Catalog – zobrazení souvisejících datových assetů](media/data-catalog-how-to-view-related-data-assets/relationships-tab.png)

V tomto příkladu jsou k dispozici dvě relace pro vybraný **ProductSubcategory** datový Asset: 

- Sloupec ProductSubcategoryID v tabulce produktů má relaci cizího klíče se sloupcem ProductSubcategoryID vybrané tabulky ProductSubcategory. 
- Sloupec ProductCategoryID tabulky ProductSubCategory má relaci cizího klíče se sloupcem ProductCategoryID vybrané tabulky ProductCategory.

> [!NOTE]
> Všimněte si směru šipky ve stromovém zobrazení vztahů.  

Chcete-li zobrazit více podrobností, jako je plně kvalifikovaný název sloupce, přesuňte ukazatel myši na a zobrazí se místní nabídka podobná následujícímu obrázku: 

![Místní nabídka Azure Data Catalog-vztahu](media/data-catalog-how-to-view-related-data-assets/relationship-popup.png)

Chcete-li zahrnout relace mezi prostředky, které již byly registrovány, znovu zaregistrujte tyto prostředky.

## <a name="next-steps"></a>Další kroky
- [Jak spravovat datové assety](data-catalog-how-to-manage.md)
