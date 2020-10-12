---
title: Nastavení obchodního glosáře v Azure Data Catalog
description: Postup popisuje obchodní Glosář v Azure Data Catalog pro definování a použití společného slovníku podnikání k označení registrovaných datových assetů.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: f91728435b885f3b4d9415bc81e18fdaaea0148f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86537744"
---
# <a name="set-up-the-business-glossary-for-governed-tagging"></a>Nastavení podnikového glosáře pro řízení označování

## <a name="introduction"></a>Úvod

Azure Data Catalog povoluje zjišťování zdrojů dat, takže můžete snadno zjistit a pochopit zdroje dat, které potřebujete k analýze, a rozhodování. Tyto možnosti mají největší dopad, když můžete najít a pochopit nejširší škálu dostupných zdrojů dat.

Jedna funkce Data Catalog, která propaguje větší porozumění údajům o assetech, je označení. Pomocí značek můžete přidružit klíčová slova k assetu nebo sloupci, což usnadňuje zjišťování prostředků prostřednictvím vyhledávání nebo procházení. Označování vám také pomůže snadněji pochopit kontext a účel assetu.

Označování ale může někdy způsobit problémy vlastními. Mezi příklady problémů, které mohou značky zavádět, patří:

* Použití zkratek u některých assetů a rozbalených textů v jiných. Tato nekonzistence brání zjišťování prostředků, i když záměr byl označit prostředky stejnou značkou.
* Potenciální variace v závislosti na kontextu. Například značka s názvem *tržby* ze sady zákaznických dat může znamenat Tržby podle zákazníka, ale stejná značka na datové sadě čtvrtletních prodejů může znamenat čtvrtletní tržby za firmu.  

Aby bylo možné tyto a podobné výzvy vyřešit, Data Catalog zahrnuje obchodní Glosář.

Pomocí Data Catalog obchodního glosáře může organizace dokumentovat klíčové obchodní účely a jejich definice k vytvoření společného obchodního slovníku. Tento zásad správného řízení umožňuje konzistenci využití dat v rámci organizace. Po definování termínu v obchodním glosáři je možné ho přiřadit k datovému assetu v katalogu. Tento přístup, který *řídí označování*, je stejný přístup jako označování.

## <a name="glossary-availability-and-privileges"></a>Dostupnost a oprávnění glosáře

Obchodní Glosář je k dispozici pouze v edici Standard Azure Data Catalog. Bezplatná edice Data Catalog nezahrnuje Glosář a neposkytuje možnosti pro řízení označování.

K obchodnímu glosáři získáte přístup pomocí možnosti **Glosář** v navigační nabídce portálu Data Catalog.  

![Data Catalog – přístup k obchodnímu glosáři](./media/data-catalog-how-to-business-glossary/01-portal-menu.png)

Data Catalog správci a členové role Správci glosáře můžou vytvořit, upravit a odstranit glosářové výrazy v obchodním glosáři. Všichni Data Catalog uživatelé mohou zobrazit definice termínů a assetů značek s termíny glosáře.

![Data Catalog – přidat nový termín glosáře](./media/data-catalog-how-to-business-glossary/02-new-term.png)

## <a name="creating-glossary-terms"></a>Vytváření termínů glosáře

Data Catalog správci a glosáře můžou vytvořit Glosář pojmů kliknutím na tlačítko **nový termín** . Každý pojem glosáře obsahuje následující pole:

* Definice podniku pro termín
* Popis, který zachycuje zamýšlené použití nebo obchodní pravidla pro daný prostředek nebo sloupec.
* Seznam zúčastněných stran, které znají nejvíc pojmu
* Nadřazený termín, který definuje hierarchii, ve které je výraz uspořádaný

## <a name="glossary-term-hierarchies"></a>Glosář pojmů

Pomocí Data Catalog obchodního glosáře může organizace popsat svůj obchodní slovník jako hierarchii podmínek a může vytvořit klasifikaci termínů, které lépe představují svou obchodní taxonomii.

Termín musí být v dané úrovni hierarchie jedinečný. Duplicitní názvy nejsou povoleny. Neexistuje žádné omezení počtu úrovní v hierarchii, ale hierarchie je často jednodušší pochopit, když existují tři úrovně nebo méně.

Použití hierarchií v obchodním glosáři je volitelné. Když necháte pole nadřazeného výrazu prázdné pro glosářové termíny, vytvoří se v glosáři plochý (nehierarchicky) seznam podmínek.  

## <a name="tagging-assets-with-glossary-terms"></a>Označování prostředků pomocí pojmů Glosář

Po definování podmínek glosáře v rámci katalogu jsou možnosti tagování prostředků optimalizované pro hledání glosáře, když uživatel zadá značku. Portál Data Catalog zobrazí seznam vyhovujících termínů glosáře, ze kterých si můžete vybrat. Pokud uživatel vybere termín glosáře ze seznamu, výraz se přidá do assetu jako značka (označuje se také jako značka glosáře). Uživatel může také zvolit vytvoření nové značky zadáním termínu, který není v glosáři (označuje se také jako značka uživatele).

![Datový Asset označený jedním uživatelským tagem a dvěma značkami glosáře](./media/data-catalog-how-to-business-glossary/03-tagged-asset.png)

> [!NOTE]
> Uživatelské značky jsou jediný typ značky podporovaný v bezplatné verzi Data Catalog.

### <a name="hover-behavior-on-tags"></a>Chování při najetí myší na značky

Na portálu Data Catalog jsou dva typy značek vizuálně jedinečné a představují různá chování při najetí myší. Když najedete myší na značku uživatele, uvidíte text značky a uživatele nebo uživatele, kteří značku přidali. Když najedete myší na tag glosáře, zobrazí se také definice pojmu Glosář a odkaz pro otevření obchodního glosáře, který zobrazí úplnou definici podmínky.

### <a name="search-filters-for-tags"></a>Vyhledávací filtry pro značky

Značky glosáře a uživatelské značky jsou prohledávatelné a můžete je použít jako filtry ve vyhledávání.

## <a name="summary"></a>Shrnutí

Používáním podnikového glosáře v Azure Data Catalog a se správou označení, které umožňuje, můžete identifikovat, spravovat a vyhledávat datové assety konzistentním způsobem. Obchodní Glosář může propagovat učení podnikového slovníku podle členů organizace. Glosář také podporuje zachytávání smysluplných metadat, což zjednodušuje zjišťování a porozumění prostředků.

## <a name="next-steps"></a>Další kroky

* [Dokumentace REST API pro operace glosáře pro firmy](/rest/api/datacatalog/data-catalog-glossary)
