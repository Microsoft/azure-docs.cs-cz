---
title: Jak zobrazit související datové prostředky v Katalogu dat Azure
description: Tento článek vysvětluje, jak zobrazit související datové prostředky vybraného datového prostředku v katalogu dat Azure.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 212ba647e6eb44e800a589928620f56fba65107c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68737016"
---
# <a name="how-to-view-related-data-assets-in-azure-data-catalog"></a>Jak zobrazit související datové prostředky v Katalogu dat Azure?
Azure Data Catalog umožňuje zobrazit datové prostředky související s vybraným datovým prostředkem a zobrazit vztahy mezi nimi. 

## <a name="supported-data-sources"></a>Podporované zdroje dat 
Když zaregistrujete datové prostředky z následujících zdrojů dat, Azure Data Catalog automaticky zaregistruje metadata o vztazích spojení mezi vybranými datovými prostředky. 

- SQL Server
- Azure SQL Database
- MySQL
- Oracle

> [!NOTE]
> Aby katalog dat importoval vztah mezi dvěma datovými prostředky, musíte registrovat oba datové prostředky současně. Pokud jste jeden z nich přidali samostatně, přidejte jej znovu a další datový datový zdroj, abyste mezi nimi importovali vztah.

## <a name="view-related-data-assets"></a>Zobrazení souvisejících datových prostředků
Chcete-li zobrazit datové prostředky související s vybranou datovou sadou, použijte kartu **Relace** tak, jak je znázorněno na následujícím obrázku: 

![Katalog dat Azure – zobrazení souvisejících datových prostředků](media/data-catalog-how-to-view-related-data-assets/relationships-tab.png)

V tomto příkladu existují dva vztahy pro vybraný datový datový **majetek ProductSubcategory:** 

- ProductSubcategoryID sloupec Product Table má vztah cizího klíče se sloupcem ProductSubcategoryID vybrané tabulky ProductSubcategory. 
- Sloupec ProductCategoryID tabulky ProductSubCategory má vztah cizího klíče se sloupcem ProductCategoryID vybrané tabulky ProductCategory.

> [!NOTE]
> Všimněte si směru šipky v zobrazení stromu vztahů.  

Chcete-li zobrazit další podrobnosti, jako je například plně kvalifikovaný název sloupce, přesuňte ukazatel myši a zobrazí se vyskakovací okno podobné následujícímu obrázku: 

![Katalog dat Azure – vyskakovací okno vztahu](media/data-catalog-how-to-view-related-data-assets/relationship-popup.png)

Chcete-li zahrnout vztahy mezi aktivy, které již byly zaregistrovány, znovu zaregistrujte tyto prostředky.

## <a name="next-steps"></a>Další kroky
- [Jak spravovat datové assety](data-catalog-how-to-manage.md)