---
title: Postup zobrazení souvisejících datových prostředků ve službě Azure Data Catalog
description: Tento článek vysvětluje, jak zobrazení souvisejících datových prostředků vybraný datový asset ve službě Azure Data Catalog.
services: data-catalog
author: markingmyname
ms.author: maghan
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 156673bfac9bfa38772e4daca166e3431f81c09a
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47404997"
---
# <a name="how-to-view-related-data-assets-in-azure-data-catalog"></a>Postup zobrazení souvisejících datových prostředků ve službě Azure Data Catalog?
Azure Data Catalog vám umožní zobrazit datové prostředky související s vybraný datový asset a zobrazení vztahů mezi nimi. 

## <a name="supported-data-sources"></a>Podporované zdroje dat 
Když si zaregistrujete datové assety z následujících zdrojů dat, Azure Data Catalog zaregistruje automaticky metadata o relace typu join mezi vybraných datových prostředcích. 

- SQL Server
- Azure SQL Database
- MySQL
- Oracle

> [!NOTE]
> Pro službu Data Catalog pro import vztahu mezi dvěma datových assetů je nutné zaregistrovat oba prostředky ve stejnou dobu. Pokud jste měli jeden z nich přidali samostatně, přidejte ho znovu a datovému assetu pro import vztahu mezi nimi.

## <a name="view-related-data-assets"></a>Zobrazení souvisejících datových prostředků
Chcete-li zobrazit datové assety, které se vztahují k vybrané datové sadě, použijte **vztahy** kartu, jak je znázorněno na následujícím obrázku: 

![Azure Data Catalog – zobrazení souvisejících datových prostředků](media\data-catalog-how-to-view-related-data-assets\relationships-tab.png)

V tomto příkladu jsou dva vztahy pro vybranou **ProductSubcategory** datový prostředek: 

- ProductSubcategoryID sloupce do tabulky Product má vztah cizího klíče se sloupcem ProductSubcategoryID vybrané tabulky ProductSubcategory. 
- ProductCategoryID sloupec tabulky ProductSubCategory má vztah cizího klíče se sloupcem ProductCategoryID vybrané tabulky ProductCategory.

> [!NOTE]
> Všimněte si, že směr šipky ve stromovém zobrazení relací.  

Pokud chcete zobrazit další podrobnosti, jako je plně kvalifikovaný název sloupce, najeďte myší a zobrazí automaticky otevíraném okně podobně jako na následujícím obrázku: 

![Azure Data Catalog – automaticky otevírané okno relace](media\data-catalog-how-to-view-related-data-assets\relationship-popup.png)

Zahrnout vztahy mezi prostředky, které jsou již zaregistrovány, přeregistrujte tyto prostředky.

## <a name="next-steps"></a>Další postup
- [Jak spravovat datové prostředky](data-catalog-how-to-manage.md)