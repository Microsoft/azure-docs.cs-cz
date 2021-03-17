---
title: Správa datových assetů v Azure Data Catalog
description: Tento článek popisuje, jak ovládat viditelnost a vlastnictví datových assetů registrovaných v Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 9d035417b613c81487c493bde90cdcc461f04412
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017283"
---
# <a name="manage-data-assets-in-azure-data-catalog"></a>Správa datových assetů v Azure Data Catalog
## <a name="introduction"></a>Úvod
Azure Data Catalog je navržená pro zjišťování zdrojů dat, abyste mohli snadno zjistit a pochopit zdroje dat, které potřebujete k analýze, a rozhodování. Tyto možnosti zjišťování mají největší dopad, když vy a jiní uživatelé můžete najít a pochopit nejširší škálu dostupných zdrojů dat. U těchto prvků je výchozím chováním Data Catalog, aby všechny registrované zdroje dat byly viditelné a zjistitelné všemi uživateli katalogu.

Data Catalog vám nedává přístup k samotným datům. Přístup k datům je řízen vlastníkem zdroje dat. Pomocí Data Catalog můžete zjistit zdroje dat a zobrazit metadata související se zdroji registrovanými v katalogu.

Mohou však nastat situace, kdy mají být zdroje dat viditelné pouze pro určité uživatele nebo členy určitých skupin. V takových scénářích mohou uživatelé převzít vlastnictví registrovaných datových assetů v katalogu a pak řídit viditelnost prostředků, které vlastní.

> [!NOTE]
> Funkce popsané v tomto článku jsou k dispozici pouze v edici Standard Azure Data Catalog. Edice Free neposkytuje funkce pro vlastnictví a omezení viditelnosti datových assetů.
>
>

## <a name="manage-ownership-of-data-assets"></a>Správa vlastnictví datových assetů
Ve výchozím nastavení nejsou datové prostředky registrované v Data Catalog nevlastní. Každý uživatel s oprávněním pro přístup ke katalogu může tyto prostředky vyhledat a opatřit poznámkami. Uživatelé mohou převzít vlastnictví nevlastněných datových assetů a potom omezit viditelnost prostředků, které vlastní.

Když je ve vlastnictví datového assetu Data Catalog, můžou assety zjistit a zobrazit jeho metadata a jenom vlastníci mohou odstranit Asset z katalogu.

> [!NOTE]
> Vlastnictví v Data Catalog ovlivní pouze metadata, která jsou uložena v katalogu. Vlastnictví neuděluje žádná oprávnění k základnímu zdroji dat.
>
>

### <a name="take-ownership"></a>Přebírat vlastnictví
Uživatelé mohou převzít vlastnictví datových assetů výběrem možnosti **převzít vlastnictví** na portálu Data Catalog. K převzetí vlastnictví nevlastněného datového assetu se nevyžadují žádná zvláštní oprávnění. Každý uživatel může převzít vlastnictví nevlastněného datového prostředku.

### <a name="add-owners-and-co-owners"></a>Přidat vlastníky a spoluvlastníky
Pokud je již datový prostředek vlastněn, ostatní uživatelé nemohou jednoduše převzít vlastnictví. Musí být přidáni jako spoluvlastníci stávající vlastník. Každý vlastník může jako spoluvlastníky přidat další uživatele nebo skupiny zabezpečení.

> [!NOTE]
> Osvědčeným postupem je mít alespoň dvě jednotlivce jako vlastníci pro jakýkoliv vlastněný datový Asset.
>
>

### <a name="remove-owners"></a>Odebrat vlastníky
Stejně jako vlastník prostředku může přidat spoluvlastníci, může kterýkoli vlastník prostředku odebrat všechny spoluvlastníky.

Vlastník assetu, který se odebere sám jako vlastník, už nemůže spravovat Asset. Pokud vlastník prostředku odebere sám sebe jako vlastníka a neexistují žádní spoluvlastníci, Asset se vrátí do stavu nevlastnictví.

## <a name="control-visibility"></a>Viditelnost ovládacího prvku
Vlastníci assetů dat můžou řídit viditelnost datových prostředků, které vlastní. Chcete-li omezit viditelnost jako výchozí, kde všichni Data Catalog uživatelé mohou vyhledat a zobrazit datový Asset, může vlastník prostředků přepnout nastavení viditelnosti ze **všech** na **vlastníky & tito uživatelé** ve vlastnostech daného prostředku. Vlastníci potom můžou přidat konkrétní uživatele a skupiny zabezpečení.

> [!NOTE]
> Kdykoli je to možné, oprávnění k vlastnictví prostředků a viditelnosti by se měla přiřadit skupinám zabezpečení, nikoli jednotlivým uživatelům.
>
>

## <a name="catalog-administrators"></a>Správci katalogu
Správci Data Catalog jsou implicitně spoluvlastníci všech assetů v katalogu. Vlastníci prostředků nemohou odebrat viditelnost správců a správci mohou spravovat vlastnictví a viditelnost všech datových assetů v katalogu.

## <a name="summary"></a>Souhrn
Model Data Catalog crowdsourcingový pro metadata a zjišťování datových assetů umožňuje všem uživatelům katalogu přispívat a zjišťovat. Edice Standard Data Catalog je navržená k vlastnictví a správě, aby omezila viditelnost a používání konkrétních datových assetů.
