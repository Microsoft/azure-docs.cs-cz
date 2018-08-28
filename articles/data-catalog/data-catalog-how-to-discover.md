---
title: Zjišťování zdrojů dat ve službě Azure Data Catalog
description: V tomto článku se dozvíte, jak zjistit registrované datové assety pomocí služby Azure Data Catalog, včetně vyhledávání a filtrování a pomocí přístupů zvýraznění funkcí portálu Azure Data Catalog.
services: data-catalog
author: steelanddata
ms.author: maroche
ms.assetid: f72ae3a3-6573-4710-89a7-f13555e1968c
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: f8e722073db967752747511e47921aec3c4a281a
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43053406"
---
# <a name="how-to-discover-data-sources-in-azure-data-catalog"></a>Zjišťování zdrojů dat ve službě Azure Data Catalog
## <a name="introduction"></a>Úvod
Azure Data Catalog je plně spravovaná Cloudová služba, která slouží jako systém registrace a systém zjišťování pro podnikové zdroje dat. Jinými slovy katalogu Data Catalog umožňuje uživatelům zjišťovat, pochopit a používat zdroje dat a pomáhá organizacím vytěžit více z jejich existující data. Po registraci zdroje dat pomocí služby Data Catalog jeho metadata jsou indexována službou, takže můžete snadno vyhledat potřebná data zjišťování.

## <a name="searching-and-filtering"></a>Vyhledávání a filtrování
Zjišťování ve službě Data Catalog používá dva primární mechanismy: vyhledávání a filtrování.

Vyhledávání je koncipováno tak, aby bylo jak intuitivní, tak výkonné. Ve výchozím nastavení se vyhledává shoda hledaných výrazů s libovolnou vlastností v katalogu včetně poznámek přidaných uživatelem.

Filtrování je koncipováno jako doplněk k vyhledávání. Můžete vybrat konkrétní charakteristiky, jako je například odborníky, typ zdroje dat, typ objektu a značky. Můžete zobrazit pouze datové assety odpovídající a omezit výsledky vyhledávání na odpovídající prostředky.

Pomocí kombinace vyhledávání a filtrování vám umožní rychle procházet zdroje dat, které byly registrovány ve službě Data Catalog ke zjišťování zdrojů dat, které potřebujete.

## <a name="search-syntax"></a>Syntaxe služby Search
Přestože výchozí textové vyhledávání je jednoduché a intuitivní, můžete také použít syntaxe hledání v katalogu Data Catalog pro větší kontrolu nad výsledky hledání. Vyhledávání ve službě data Catalog podporuje následujících postupů:

| Technika | Použití | Příklad: |
| --- | --- | --- |
| Základní vyhledávání |Základní vyhledávání, která používá jeden či více hledaných výrazů. Výsledky se zobrazí veškeré assety, které odpovídají jakékoli vlastnosti s jednou nebo více zadaných výrazů. |`sales data` |
| Zkoumání vlastností |Vrátí pouze ty zdroje dat, kde se hledaný termín shoduje se zadanou vlastností. |`name:finance` |
| Logické operátory |Rozšíří nebo zúží vyhledávání pomocí logických operací. |`finance NOT corporate` |
| Seskupování pomocí závorek |Použití závorek k seskupení části dotazu k dosažení logické izolace, zejména ve spojení s logickými operátory. |`name:finance AND (tags:Q1 OR tags:Q2)` |
| Operátory porovnání |Použijte porovnávání jiné než rovnost pro vlastnosti, které mají číselné a číselné datové typy. |`modifiedTime > "11/05/2014"` |

Další informace o vyhledávání ve službě Data Catalog, najdete v článku [Azure Data Catalog](https://msdn.microsoft.com/library/azure/mt267594.aspx) článku.

## <a name="hit-highlighting"></a>Zvýrazňování položek
Při zobrazení výsledků hledání, všechny zobrazené vlastnosti, které odpovídají zadaným hledaným výrazům (jako je například název datového assetu, popis a značky) jsou zvýrazněny k němu snadněji určit, proč se daný datový prostředek vrátil zadaným hledáním.

> [!NOTE]
> Chcete-li vypnout zvýrazňování shod, použijte **zvýrazněte** přepnout na portálu pro Data Catalog.
>
>

Při zobrazení výsledků hledání, nemusí být vždy zřejmé důvod, proč k datovému assetu je součástí, dokonce i zvýrazňování shod povolena. Vzhledem k tomu, že ve výchozím nastavení jsou prohledány všechny vlastnosti, může být vrácena k datovému assetu z důvodu shodu na úrovni sloupců vlastností. A protože více uživatelů může opatřit poznámkami registrovaných datových assetů pomocí vlastní značky a popisy, ne všechna metadata může zobrazit v seznamu výsledků hledání.

Ve výchozím zobrazení vedle sebe, obsahuje každou dlaždici se zobrazí ve výsledcích hledání **zobrazení hledaný termín shoduje** ikonu, tak, že můžete rychle zobrazit počet shod a jejich umístění a můžete přejít k nim, chcete-li.

 ![Zvýrazňování a hledání odpovídá na portálu Azure Data Catalog](./media/data-catalog-how-to-discover/search-matches.png)

## <a name="summary"></a>Souhrn
Protože registrace zdroje dat pomocí služby Data Catalog zkopíruje strukturální a popisná metadata ze zdroje dat ve službě katalogu, je jednodušší zdroj dat zjišťování a pochopení. Po zaregistrování zdroje dat, můžete jej zjistit pomocí filtrování a hledání z v rámci portálu katalogu Data Catalog.

## <a name="next-steps"></a>Další postup
* Podrobné informace o tom, jak zjistit zdroje dat, naleznete v tématu [Začínáme s Azure Data Catalog](data-catalog-get-started.md).
