---
title: Postup zjišťování zdrojů dat v Azure Data Catalog
description: V tomto článku se naučíte, jak zjistit registrované datové assety pomocí Azure Data Catalog, včetně vyhledávání a filtrování a používání možností zvýrazňování Azure Data Catalog na portálu.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 4eb689b17df8236a00b5914912d1927804944f25
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87081198"
---
# <a name="how-to-discover-data-sources-in-azure-data-catalog"></a>Postup zjišťování zdrojů dat v Azure Data Catalog

## <a name="introduction"></a>Úvod

Azure Data Catalog je plně spravovaná cloudová služba, která slouží jako systém registrace a zjišťování pro podnikové zdroje dat. Jinými slovy Data Catalog pomáhá lidem zjišťovat, pochopit a používat zdroje dat. Pomáhá organizacím získat větší hodnotu z jejich stávajících dat. Po zaregistrování zdroje dat ve službě Data Catalog jsou jeho metadata indexována službou, takže můžete snadno vyhledávat data, která potřebujete.

## <a name="searching-and-filtering"></a>Hledání a filtrování

Zjišťování v Data Catalog používá dva primární mechanismy: hledání a filtrování.

Vyhledávání je koncipováno tak, aby bylo jak intuitivní, tak výkonné. Ve výchozím nastavení se vyhledává shoda hledaných výrazů s libovolnou vlastností v katalogu včetně poznámek přidaných uživatelem.

Filtrování je koncipováno jako doplněk k vyhledávání. Můžete vybrat konkrétní vlastnosti, jako jsou například odborníci, typ zdroje dat, typ objektu a značky. Můžete zobrazit pouze vyhovující datové assety a omezit výsledky hledání na vyhovující prostředky.

Pomocí kombinace hledání a filtrování můžete rychle přejít ke zdrojům dat, které byly zaregistrovány v Data Catalog, abyste zjistili, jaké zdroje dat potřebujete.

## <a name="search-syntax"></a>Syntaxe hledání

I když je výchozí volné vyhledávání jednoduché a intuitivní, můžete také použít syntaxi hledání Data Catalog pro lepší kontrolu nad výsledky hledání. Data Catalog Search podporuje následující techniky:

| Technika | Použití | Příklad |
| --- | --- | --- |
| Základní hledání |Základní vyhledávání, které používá jeden nebo více hledaných výrazů. Výsledkem jsou všechny prostředky, které odpovídají libovolné vlastnosti s jedním nebo více zadanými podmínkami. |`sales data` |
| Rozsah vlastností |Vrátí pouze zdroje dat, ve kterých je hledaný termín shodný se zadanou vlastností. |`name:finance` |
| Logické operátory |Rozrozšiřte nebo zužte hledání pomocí logických operací. |`finance NOT corporate` |
| Seskupení s kulatými závorkami |Použijte kulaté závorky k seskupení částí dotazu k dosažení logické izolace, zejména ve spojení s logickými operátory. |`name:finance AND (tags:Q1 OR tags:Q2)` |
| Relační operátory |Použijte porovnání jiné než rovnost pro vlastnosti, které mají číselné a datové datové typy. |`modifiedTime > "11/05/2014"` |

Další informace o Data Catalog hledání naleznete v článku [Azure Data Catalog](/rest/api/datacatalog/#search-syntax-reference) .

## <a name="hit-highlighting"></a>Zvýrazňování položek

Při zobrazení výsledků hledání se zvýrazní všechny zobrazené vlastnosti, které odpovídají zadaným hledaným podmínkám (například název datového assetu, popis a značky), aby bylo snazší zjistit, proč daný datový Asset vrátil konkrétní hledání.

> [!NOTE]
> Zvýrazňování přístupů vypnete tak, že na Data Catalogovém portálu použijete přepínač **zvýraznění** .

Při zobrazení výsledků hledání nemusí být vždy zřejmé, proč se datový Asset zahrnuje, a to i s povoleným zvýrazňováním přístupů. Vzhledem k tomu, že všechny vlastnosti jsou prohledávány ve výchozím nastavení, může být datový Asset vrácen z důvodu shody s vlastností na úrovni sloupce. A vzhledem k tomu, že více uživatelů může opatřit zaregistrovanými datovými prostředky pomocí vlastních značek a popisů, nezobrazí se v seznamu výsledků hledání všechna metadata.

Ve výchozím zobrazení dlaždice Každá dlaždice zobrazená ve výsledcích hledání zahrnuje ikonu **zobrazení hledaného termínu** , která odpovídá ikoně, takže můžete rychle zobrazit počet shod a jejich umístění a přejít na ně, pokud chcete.

 ![Zvýrazňování přístupů a shody hledání na Azure Data Catalogovém portálu](./media/data-catalog-how-to-discover/search-matches.png)

## <a name="summary"></a>Shrnutí

Vzhledem k tomu, že registrace zdroje dat s Data Catalog kopíruje strukturální a popisné metadata ze zdroje dat do služby katalogu, bude zdroj dat snazší zjistit a pochopit. Po zaregistrování zdroje dat ho můžete zjistit pomocí filtrování a hledání v rámci Data Catalogového portálu.

## <a name="next-steps"></a>Další kroky

* Podrobné informace o tom, jak zjistit zdroje dat, najdete v tématu [Začínáme s Azure Data Catalog](data-catalog-get-started.md).
