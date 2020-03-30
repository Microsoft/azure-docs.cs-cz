---
title: Správa datových prostředků v katalogu dat Azure
description: Článek upozorňuje, jak řídit viditelnost a vlastnictví datových prostředků registrovaných v katalogu dat Azure.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 9905ed72ec54304bbdb0f7ee607cbb013fc645bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68736342"
---
# <a name="manage-data-assets-in-azure-data-catalog"></a>Správa datových prostředků v katalogu dat Azure
## <a name="introduction"></a>Úvod
Azure Data Catalog je navržený pro zjišťování zdrojů dat, takže můžete snadno zjistit a pochopit zdroje dat, které potřebujete k provádění analýzy a rozhodování. Tyto funkce zjišťování mají největší vliv, když vy a ostatní uživatelé můžete najít a pochopit nejširší škálu dostupných zdrojů dat. S ohledem na tyto prvky je výchozí chování katalogu dat pro všechny registrované zdroje dat, které mají být viditelné a zjistitelné všemi uživateli katalogu.

Katalog dat neposkytuje přístup k samotným datům. Přístup k datům je řízen vlastníkem zdroje dat. Pomocí katalogu dat můžete zjistit zdroje dat a zobrazit metadata, která se vztahují ke zdrojům registrovaným v katalogu.

Mohou však nastat situace, kdy by zdroje dat měly být viditelné pouze pro určité uživatele nebo členy určitých skupin. V takových scénářích mohou uživatelé převzít vlastnictví registrovaných datových prostředků v rámci katalogu a potom řídit viditelnost prostředků, které vlastní.

> [!NOTE]
> Funkce popsané v tomto článku je k dispozici pouze ve standardní edici katalogu dat Azure. Free Edition neposkytuje možnosti pro vlastnictví a omezení viditelnosti datových prostředků.
>
>

## <a name="manage-ownership-of-data-assets"></a>Správa vlastnictví datových prostředků
Ve výchozím nastavení jsou datové prostředky registrované v katalogu dat nevlastněné. Každý uživatel s oprávněním k přístupu ke katalogu může tyto datové zdroje zjistit a oznamovat pomocí oznamujících. Uživatelé mohou převzít vlastnictví nevlastněných datových prostředků a pak omezit viditelnost prostředků, které vlastní.

Pokud je datový prostředek v katalogu dat vlastněn, mohou ho zjišťovat a zobrazovat pouze uživatelé, kteří jsou autorizováni vlastníky, a zobrazit jeho metadata pouze vlastníci a pouze vlastníci mohou datový zdroj z katalogu odstranit.

> [!NOTE]
> Vlastnictví v katalogu dat ovlivňuje pouze metadata, která jsou uložena v katalogu. Vlastnictví neuděluje žádná oprávnění pro základní zdroj dat.
>
>

### <a name="take-ownership"></a>Přebírat vlastnictví
Uživatelé mohou převzít vlastnictví datových prostředků výběrem **možnosti Převzít vlastnictví** na portálu katalogu dat. K převzetí vlastnictví nevlastněného datového prostředku nejsou vyžadována žádná zvláštní oprávnění. Každý uživatel může převzít vlastnictví nevlastněného datového prostředku.

### <a name="add-owners-and-co-owners"></a>Přidání vlastníků a spoluvlastníků
Pokud je datový datový zdroj již vlastněn, ostatní uživatelé nemohou jednoduše převzít vlastnictví. Musí být přidány jako spoluvlastníci stávajícím vlastníkem. Každý vlastník může přidat další uživatele nebo skupiny zabezpečení jako spoluvlastníky.

> [!NOTE]
> Je osvědčeným postupem mít alespoň dvě osoby jako vlastníky pro jakýkoli vlastněný datový prostředek.
>
>

### <a name="remove-owners"></a>Odebrat vlastníky
Stejně jako každý vlastník majetku může přidat spoluvlastníky, může každý vlastník majetku odebrat jakéhokoli spoluvlastníka.

Vlastník majetku, který se sám odebere jako vlastník, již nemůže majetek spravovat. Pokud se vlastník majetku odebere jako vlastník a neexistují žádní další spoluvlastníci, vrátí se do nevlastněného stavu.

## <a name="control-visibility"></a>Viditelnost ovládání
Vlastníci datových prostředků mohou řídit viditelnost datových prostředků, které vlastní. Chcete-li omezit viditelnost jako výchozí, kde všichni uživatelé katalogu dat mohou zjistit a zobrazit datový prostředek, vlastník datového prostředku může přepnout nastavení viditelnosti od **Everyone** to **Owners & Tito uživatelé** ve vlastnostech datového zdroje. Vlastníci pak mohou přidat konkrétní uživatele a skupiny zabezpečení.

> [!NOTE]
> Kdykoli je to možné, měla by být oprávnění k vlastnictví a viditelnosti prostředků přiřazena skupinám zabezpečení, a nikoli jednotlivým uživatelům.
>
>

## <a name="catalog-administrators"></a>Správci katalogu
Správci katalogu dat jsou implicitně spoluvlastníky všech datových zdrojů v katalogu. Vlastníci prostředků nemohou odebrat viditelnost správcům a správci mohou spravovat vlastnictví a viditelnost pro všechny datové prostředky v katalogu.

## <a name="summary"></a>Souhrn
Model crowdsourcingu katalogu dat pro metadata a zjišťování datových prostředků umožňuje všem uživatelům katalogu přispívat a zjišťovat. Standardní edice katalogu dat je určena pro vlastnictví a správu, aby se omezila viditelnost a použití konkrétních datových prostředků.
