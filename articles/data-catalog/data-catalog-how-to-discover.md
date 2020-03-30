---
title: Jak zjistit zdroje dat v Katalogu dat Azure
description: Tento článek upozorňuje, jak zjistit registrované datové prostředky s Azure Data Catalog, včetně vyhledávání a filtrování a pomocí možností zvýraznění přístupů na portálu Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: b12cb94832a1ea977fb13f5f2271984dc8780cee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68736372"
---
# <a name="how-to-discover-data-sources-in-azure-data-catalog"></a>Jak zjistit zdroje dat v Katalogu dat Azure

## <a name="introduction"></a>Úvod

Azure Data Catalog je plně spravovaná cloudová služba, která slouží jako systém registrace a zjišťování pro zdroje podnikových dat. Jinými slovy, Katalog dat pomáhá lidem zjišťovat, chápat a používat zdroje dat. Pomáhá organizacím získat větší hodnotu ze svých stávajících dat. Po registraci zdroje dat v katalogu dat jsou jeho metadata indexována službou, takže můžete snadno vyhledávat a zjistit data, která potřebujete.

## <a name="searching-and-filtering"></a>Vyhledávání a filtrování

Zjišťování v katalogu dat používá dva primární mechanismy: vyhledávání a filtrování.

Vyhledávání je koncipováno tak, aby bylo jak intuitivní, tak výkonné. Ve výchozím nastavení se vyhledává shoda hledaných výrazů s libovolnou vlastností v katalogu včetně poznámek přidaných uživatelem.

Filtrování je koncipováno jako doplněk k vyhledávání. Můžete vybrat specifické charakteristiky, jako jsou odborníci, typ zdroje dat, typ objektu a značky. Můžete zobrazit pouze odpovídající datové prostředky a omezit výsledky hledání na odpovídající datové zdroje.

Pomocí kombinace vyhledávání a filtrování můžete rychle procházet zdroje dat, které byly zaregistrovány v katalogu dat, a zjistit potřebné zdroje dat.

## <a name="search-syntax"></a>Hledat syntaxi

Přestože je výchozí volné textové vyhledávání jednoduché a intuitivní, můžete také použít syntaxi vyhledávání v katalogu dat pro větší kontrolu nad výsledky hledání. Vyhledávání v katalogu dat podporuje následující techniky:

| Technika | Použití | Příklad |
| --- | --- | --- |
| Základní vyhledávání |Základní vyhledávání, které používá jeden nebo více hledaných výrazů. Výsledky jsou všechny prostředky, které odpovídají libovolné vlastnosti s jedním nebo více zadanými podmínkami. |`sales data` |
| Obor vlastností |Vrátí pouze zdroje dat, kde je hledaný termín spárován se zadanou vlastností. |`name:finance` |
| Logické operátory |Rozšířit nebo zúžit hledání pomocí logické operace. |`finance NOT corporate` |
| Seskupení s závorkou |Pomocí závorek seskupte části dotazu k dosažení logické izolace, zejména ve spojení s logickými operátory. |`name:finance AND (tags:Q1 OR tags:Q2)` |
| Operátory porovnání |Pro vlastnosti, které mají číselné a datové typy kalendářních dat, použijte jiná porovnání než rovnost. |`modifiedTime > "11/05/2014"` |

Další informace o hledání v katalogu dat najdete v článku [Katalog dat Azure.](/rest/api/datacatalog/#search-syntax-reference)

## <a name="hit-highlighting"></a>Zvýrazňování položek

Při zobrazení výsledků hledání jsou zvýrazněny všechny zobrazené vlastnosti, které odpovídají zadaným hledaným výrazům (například název datového prostředku, popis a značky), aby bylo snazší zjistit, proč byl daný datový zdroj vrácen daným vyhledáváním.

> [!NOTE]
> Chcete-li vypnout zvýraznění přístupů, použijte přepínač **Zvýraznění** na portálu Katalog dat.

Při zobrazení výsledků hledání nemusí být vždy zřejmé, proč je datový datový prostředek zahrnut, a to i při povoleném zvýraznění přístupů. Vzhledem k tomu, že všechny vlastnosti jsou prohledávány ve výchozím nastavení, datový datový prostředek může být vrácena z důvodu shody na úrovni sloupce vlastnost. A protože více uživatelů může opatřovat registrované datové prostředky vlastními značkami a popisy, ne všechna metadata se zobrazí v seznamu výsledků hledání.

Ve výchozím zobrazení dlaždic obsahuje každá dlaždice zobrazená ve výsledcích hledání ikonu **Zobrazit shody vyhledávacích dotazů,** takže můžete rychle zobrazit počet shod a jejich umístění a v případě, že chcete, přeskočíte na ně.

 ![Zvýraznění přístupů a hledání shody na portálu Katalog dat Azure](./media/data-catalog-how-to-discover/search-matches.png)

## <a name="summary"></a>Souhrn

Vzhledem k tomu, že registrace zdroje dat pomocí katalogu dat kopíruje strukturální a popisná metadata ze zdroje dat do služby katalogu, zdroj dat se snadněji zjišťuje a chápe. Po registraci zdroje dat jej můžete zjistit pomocí filtrování a vyhledávání z portálu katalogu dat.

## <a name="next-steps"></a>Další kroky

* Podrobné informace o tom, jak zjistit zdroje dat, najdete v tématu [Začínáme s Katalogem dat Azure](data-catalog-get-started.md).
