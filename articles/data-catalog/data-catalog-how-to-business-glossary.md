---
title: Nastavení obchodního glosáře v Azure Data Catalog
description: Článek s postupem, který zvýrazňuje obchodní glosář v Katalogu dat Azure pro definování a používání společného obchodního slovníku k označení registrovaných datových prostředků.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 1065abecb1f0ef57eb13b1ec3f194f07ae01eaee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68976795"
---
# <a name="set-up-the-business-glossary-for-governed-tagging"></a>Nastavení obchodního glosáře pro řízené označování

## <a name="introduction"></a>Úvod

Azure Data Catalog umožňuje zjišťování zdrojů dat, takže můžete snadno zjistit a pochopit zdroje dat, které potřebujete k provádění analýzy a rozhodování. Tyto funkce mají největší vliv, když můžete najít a pochopit nejširší škálu dostupných zdrojů dat.

Jedna funkce katalogu dat, která podporuje lepší pochopení dat datových zdrojů, je označování. Pomocí značkování můžete klíčová slova přidružit k datovému zdroji nebo ke sloupci, což usnadňuje vyhledávání nebo procházení. Označování také pomáhá snadněji pochopit kontext a záměr datového zdroje.

Označování však může někdy způsobit vlastní problémy. Některé příklady problémů, které může označení zavést, jsou:

* Použití zkratek u některých datových zdrojů a rozšířený text u jiných. Tato nekonzistence brání zjišťování datových zdrojů, i když záměrem bylo označit datové zdroje stejnou značkou.
* Potenciální změny ve významu, v závislosti na kontextu. Například značka s názvem *Výnosy* ze sady dat zákazníka může znamenat výnosy podle zákazníka, ale stejná značka ve čtvrtletní datové sadě prodeje může znamenat čtvrtletní výnosy pro společnost.  

Katalog dat obsahuje obchodní glosář, který pomáhá řešit tyto a další podobné problémy.

Pomocí obchodního glosáře katalogu dat může organizace dokumentovat klíčové obchodní termíny a jejich definice a vytvořit tak společný obchodní slovník. Toto zásadsprávné řízení umožňuje konzistenci využití dat v celé organizaci. Po termín je definován v obchodním glosáři, může být přiřazen datový majetek v katalogu. Tento přístup, *řízené označování*, je stejný přístup jako označování.

## <a name="glossary-availability-and-privileges"></a>Dostupnost a oprávnění glosáře

Firemní glosář je k dispozici jenom ve standardní edici katalogu dat Azure. Bezplatná edice katalogu dat neobsahuje glosář a neposkytuje možnosti pro řízené označování.

K firemnímu glosáři se dostanete prostřednictvím **možnosti Glosář** v navigační nabídce portálu Katalog dat.  

![Katalog dat - Přístup k obchodnímu glosáři](./media/data-catalog-how-to-business-glossary/01-portal-menu.png)

Správci katalogu dat a členové role správců glosáře mohou vytvářet, upravovat a odstraňovat termíny glosáře v obchodním glosáři. Všichni uživatelé katalogu dat mohou zobrazit definice termínů a označit datové zdroje pomocí termínů glosáře.

![Katalog dat - Přidání nového termínu glosáře](./media/data-catalog-how-to-business-glossary/02-new-term.png)

## <a name="creating-glossary-terms"></a>Vytváření termínů glosáře

Správci katalogu dat a správci glosáře mohou vytvářet termíny glosáře klepnutím na tlačítko **Nový termín.** Každý termín glosáře obsahuje následující pole:

* Definice podniku pro pojem
* Popis, který zachycuje zamýšlená použití nebo obchodní pravidla pro majetek nebo sloupec
* Seznam zúčastněných stran, které vědí nejvíce o termínu
* Nadřazený termín, který definuje hierarchii, ve které je termín uspořádán

## <a name="glossary-term-hierarchies"></a>Hierarchie termínů glosáře

Pomocí obchodního glosáře katalogu dat může organizace popsat svůj obchodní slovník jako hierarchii termínů a může vytvořit klasifikaci termínů, která lépe reprezentuje její obchodní taxonomii.

Termín musí být jedinečný na dané úrovni hierarchie. Duplicitní názvy nejsou povoleny. Počet úrovní v hierarchii není nijak omezen, ale hierarchie je často srozumitelnější, pokud existují tři úrovně nebo méně.

Použití hierarchií v obchodním glosáři je volitelné. Ponechání pole nadřazeného termínu prázdného pro termíny glosáře vytvoří plochý (nehierarchický) seznam termínů v glosáři.  

## <a name="tagging-assets-with-glossary-terms"></a>Označování datových zdrojů pomocí termínů glosáře

Po definování termínů glosáře v katalogu je prostředí označování datových zdrojů optimalizováno tak, aby bylo prohledáno v glosáři, když uživatel zadá značku. Portál Katalog dat zobrazuje seznam odpovídajících termínů glosáře, ze kterých si můžete vybrat. Pokud uživatel vybere termín glosáře ze seznamu, termín se přidá do datového zdroje jako značka (také se nazývá slovníček značky). Uživatel může také vytvořit novou značku zadáním termínu, který není v glosáři (také nazývaný značka uživatele).

![Datový datový zdroj označený jednou značkou uživatele a dvěma značkami glosáře](./media/data-catalog-how-to-business-glossary/03-tagged-asset.png)

> [!NOTE]
> Uživatelské značky jsou jediným typem značky podporované v katalogu dat Free Edition.

### <a name="hover-behavior-on-tags"></a>Chování při najetí přes na značky

Na portálu katalogdat jsou tyto dva typy značek vizuálně odlišné a představují různé chování při hodu. Když najedete na značku uživatele, zobrazí se text značky a uživatel nebo uživatelé, kteří značku přidali. Když najedete na značku glosáře, zobrazí se také definice termínu glosáře a odkaz na otevření obchodního glosáře pro zobrazení úplné definice termínu.

### <a name="search-filters-for-tags"></a>Hledat filtry pro značky

Značky glosáře i značky uživatelů lze prohledávat a můžete je použít jako filtry při hledání.

## <a name="summary"></a>Souhrn

Pomocí obchodního glosáře v Katalogu dat Azure a řízené značkování, které umožňuje, můžete identifikovat, spravovat a zjišťovat datové prostředky konzistentním způsobem. Obchodní glosář může podporovat učení obchodního slovníku členy organizace. Glosář také podporuje zachytávání smysluplných metadat, což zjednodušuje zjišťování a pochopení datových zdrojů.

## <a name="next-steps"></a>Další kroky

* [Rest API dokumentace pro operace obchodního glosáře](/rest/api/datacatalog/data-catalog-glossary)
