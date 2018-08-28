---
title: Jak nastavit obchodní Glosář řízená přidáváním značek ve službě Azure Data Catalog
description: Článek zvýraznění obchodní Glosář ve službě Azure Data Catalog pro definování a použití společný obchodní slovník ke značce registrovaných datových prostředků.
services: data-catalog
author: steelanddata
ms.author: maroche
ms.assetid: b3d63dbe-1ae7-499f-bc46-42124e950cd6
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: b4586133a6d3b7514d6b47e2e0c93b0e79988f09
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43053565"
---
# <a name="set-up-the-business-glossary-for-governed-tagging"></a>Nastavit obchodní Glosář pro řídí označování
## <a name="introduction"></a>Úvod
Azure Data Catalog umožňuje zjišťování zdrojů dat, takže můžete snadno vyhledat a porozumět zdrojům dat, které je nutné provádět analýzy a rozhodování. Tyto funkce při můžete vyhledat a porozumět nejširší řadu dostupných zdrojů dat, provést největší dopad.

Je označování jednu funkci katalog dat, která podporuje větší přehled o datové assety. Pomocí označování, můžete přiřadit klíčová slova k prostředku nebo sloupec, který zase usnadňuje zjišťování prostředků prostřednictvím vyhledávání a procházení. Označování také pomáhá další snadno pochopit kontextu a záměr majetku.

Ale označování může někdy způsobit problémy své vlastní. Mezi příklady problémů, které můžou představovat označování patří:

* Použití zkratky na některých prostředků a rozšířené text na ostatní. Tato nekonzistence omezuje zjišťování prostředků, i když bylo záměrem označit prostředky se stejnou značkou.
* Potenciální kolísání význam, v závislosti na kontextu. Například značku názvem *výnosy* na zákazníka datová sada může znamenat výnosy podle zákazníka, ale stejnou značku v datové sadě čtvrtletní prodejní může znamenat Čtvrtletní výnosy společnosti.  

K řešení těchto a dalších podobných výzev, katalogu Data Catalog zahrnuje obchodní Glosář.

Pomocí katalogu Data Catalog obchodní Glosář organizaci dokumentovat klíčové obchodní termíny a jejich definice vytvoření společný obchodní slovník. Tato zásady správného řízení umožňuje konzistence dat využití celé organizace. Po termín, který je definován v obchodní glosář, je možné přiřadit k datovému assetu v katalogu. Tento přístup *řídí označování*, je stejný přístup jako označování.

## <a name="glossary-availability-and-privileges"></a>Glosář dostupnost a oprávnění
Obchodní Glosář je k dispozici pouze v nástroje Azure Data Catalog Standard Edition. Bezplatná edice Data Catalog nezahrnuje Glosář a neposkytuje funkce pro řízení označování.

Obchodní Glosář prostřednictvím můžete přistupovat **Glosář** v navigační nabídce portál služby Data Catalog.  

![Přístup k obchodní Glosář](./media/data-catalog-how-to-business-glossary/01-portal-menu.png)

Správci služby Data Catalog a členové role správců glosáře můžete vytvořit, upravit a odstranit termíny glosáře v obchodní Glosář. Definice období a značky prostředků s termíny glosáře, mohou zobrazit všichni uživatelé katalogu Data Catalog.

![Přidat nový termín glosáře](./media/data-catalog-how-to-business-glossary/02-new-term.png)

## <a name="creating-glossary-terms"></a>Vytváření termíny glosáře
Data Catalog správci glosáře mohou vytvořit správci a termíny glosáře po kliknutí **nový termín** tlačítko. Každý termín glosáře obsahuje následující pole:

* Obchodní definici termínu
* Popis, který zachycuje zamýšlené použití nebo obchodní pravidla pro prostředek nebo sloupec
* Seznam účastníků, kteří znají nejvíce o termín
* Nadřazený termín, který definuje hierarchie, ve kterém je uspořádaný termín

## <a name="glossary-term-hierarchies"></a>Glosář termínů hierarchie
Pomocí katalogu Data Catalog obchodní Glosář organizace můžete popsat její obchodní slovník jako hierarchii podmínky a je možné vytvořit klasifikaci podmínky, která lépe představuje jeho obchodní taxonomii.

Termín, který musí být jedinečný na dané úrovni hierarchie. Duplicitní názvy nejsou povoleny. Neexistuje žádné omezení počtu zadaných úrovní v hierarchii, ale hierarchie se často srozumitelnější když existují tři úrovně nebo méně.

Použití hierarchie v obchodní Glosář je volitelné. Opuštění nadřazený termín pole prázdné pro termíny glosáře vytvoří (nehierarchických) plochý seznam termínů v glosáři.  

## <a name="tagging-assets-with-glossary-terms"></a>Označování prostředků s termíny glosáře
Po definování termíny glosáře v katalogu funkce označování prostředků je optimalizována pro hledání Glosář jako uživatel zadá značku. Portál služby Data Catalog zobrazí seznam odpovídajících termíny glosáře lze vybírat. Pokud si uživatel vybere ze seznamu termín glosáře, termín se přidá do assetu jako značku (také nazývané značka glosáře). Uživatel může také můžete vytvořit novou značku tak, že zadáte výraz, který není v glosáři (také nazývané značku uživatele).

![Datovému assetu označené značky s jedním uživatelem a dvě značky Glosář](./media/data-catalog-how-to-business-glossary/03-tagged-asset.png)

> [!NOTE]
> Značky uživatele jsou jediným typem značky, které jsou podporovány v bezplatné edice Data Catalog.
>
>

### <a name="hover-behavior-on-tags"></a>Chování při najetí myší na značky
Na portálu pro Data Catalog jsou dva typy značek chování vizuálně distinct a k dispozici jinou najetím myší. Když najedete myší značku uživatele, zobrazí se text značky a uživatel nebo uživatelé, kteří přidali značky. Když najedete myší značka glosáře, uvidíte také definice termín glosáře a odkaz k otevření obchodní glosář, chcete-li zobrazit úplnou definicí pojmu.

### <a name="search-filters-for-tags"></a>Vyhledávací filtry pro značky
Glosář značky a uživatelské značky jsou obě prohledávatelný a použít je jako filtry v hledání.

## <a name="summary"></a>Souhrn
Pomocí obchodní Glosář v Azure Data Catalog a řídí označování, které umožňuje identifikovat, spravovat a zjištění datových assetů konzistentním způsobem. Obchodní Glosář můžete zvýšit úroveň learning obchodní slovník členy organizace. Glosář podporuje také zaznamenání smysluplná metadata, který zjednodušuje zjišťování prostředků a pochopení.

## <a name="next-steps"></a>Další postup
* [Dokumentace k rozhraní REST API pro obchodní Glosář operace](https://msdn.microsoft.com/library/mt708855.aspx)
