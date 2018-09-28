---
title: Správa datových prostředků ve službě Azure Data Catalog
description: V článku se dozvíte, jak řídit viditelnost a vlastnictví datových assetů, které jsou registrované ve službě Azure Data Catalog.
services: data-catalog
author: markingmyname
ms.author: maghan
ms.assetid: 623f5ed4-8da7-48f5-943a-448d0b7cba69
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 46af78dae6d5c1607f56d36732adce5e677e0c65
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47407634"
---
# <a name="manage-data-assets-in-azure-data-catalog"></a>Správa datových prostředků ve službě Azure Data Catalog
## <a name="introduction"></a>Úvod
Azure Data Catalog je určen pro zdroj dat zjišťování, takže můžete snadno vyhledat a porozumět zdrojům dat, které potřebujete k provádění analýzy a rozhodování. Při vám a dalším uživatelům můžete najít a pochopit nejširší řadu dostupných zdrojů dat, tyto funkce zjišťování provést největší dopad. S těmito prvky v úvahu je výchozí chování katalogu Data Catalog pro všechny registrované datové zdroje bude viditelné a všichni uživatelé katalogu zjistitelné.

Data Catalog neposkytuje přístup k samotným datům. Vlastník zdroje dat se řídí přístup k datům. Pomocí katalogu Data Catalog můžete zjistit zdroje dat a zobrazit metadata, která je související se zdroji zaregistrovanými v katalogu.

Můžou nastat situace, ale pokud být zdroje dat by měl pouze viditelné pro konkrétní uživatele, nebo členy určitých skupin. V takových scénářích uživatelé převzít vlastnictví registrovaných datových assetů v katalogu a následně řídit viditelnost assetů, které vlastní.

> [!NOTE]
> Funkce popsané v tomto článku je k dispozici pouze v nástroje Azure Data Catalog Standard Edition. Edice Free neposkytuje funkce pro vlastnictví a omezení viditelnosti datovému assetu.
>
>

## <a name="manage-ownership-of-data-assets"></a>Správa vlastnictví datových prostředků
Ve výchozím nastavení nevlastněný datových assetů, které jsou registrované ve službě Data Catalog. Každý uživatel s oprávněním pro přístup ke katalogu lze můžete zjišťovat a anotovat tyto prostředky. Uživatelé mohou převzít vlastnictví nevlastněný datové prostředky a pak omezit viditelnost assetů, které vlastní.

Když k datovému assetu ve službě Data Catalog je vlastní, pouze uživatelé, kteří mají oprávnění vlastníky můžete zjistit assetu a zobrazit jeho metadata, a pouze vlastníci můžete odstranit i asset z katalogu.

> [!NOTE]
> Ve službě Data Catalog vlastnictví ovlivňuje pouze metadata, která je uložena v katalogu. Vlastnictví neuděluje žádná oprávnění na podkladový zdroj dat.
>
>

### <a name="take-ownership"></a>Převzít vlastnictví
Uživatelé můžou převzít vlastnictví datových prostředků tak, že vyberete **převzít vlastnictví** možnost portálu Data Catalog. Žádná zvláštní oprávnění nejsou vyžadována převzít vlastnictví assetu nevlastněný data. Každý uživatel může převzít vlastnictví assetu nevlastněný data.

### <a name="add-owners-and-co-owners"></a>Přidat vlastníky a spoluvlastníků
Pokud už ho vlastní datový prostředek, nemůže ostatním uživatelům jednoduše převzít vlastnictví. Je třeba přidat jako spoluvlastníky pomocí stávající vlastník. Žádné vlastníka můžete přidat další uživatele nebo skupiny zabezpečení jako spoluvlastníky.

> [!NOTE]
> Je vhodné mít aspoň dva jednotlivce vlastníků pro všechny vlastněné datovému assetu.
>
>

### <a name="remove-owners"></a>Odebrat vlastníky
Stejně jako jakékoli vlastník majetku můžete přidat spoluvlastníky, můžete odebrat všechny vlastník majetku jakékoli spoluvlastníka.

Vlastníka prostředku, který odebere sebe jako vlastníka již nemůže spravovat assetu. Pokud neexistují žádné spoluvlastníků, vlastník majetku odebere sebe jako vlastníka prostředku se vrátí do nevlastněný stavu.

## <a name="control-visibility"></a>Přehled ovládacího prvku
Vlastníky datového prostředku můžete řídit viditelnost datových prostředků, které vlastní. Chcete-li omezit viditelnost jako výchozí, ve kterém všichni uživatelé katalogu Data Catalog může zjištění a zobrazení datovému assetu, vlastník majetku můžete přepínat viditelnost nastavení z **Everyone** k **vlastníci a tito uživatelé** v vlastnosti pro prostředek. Vlastníci poté můžete přidat konkrétního uživatele a skupiny zabezpečení.

> [!NOTE]
> Kdykoli je to možné, měla být přiřazena oprávnění vlastnictví a viditelnosti prostředku na skupiny zabezpečení a ne pro jednotlivé uživatele.
>
>

## <a name="catalog-administrators"></a>Správci katalogu
Správci služby Data Catalog jsou implicitně spoluvlastníky nad všemi prostředky v katalogu. Vlastníkům prostředků nejde odebrat viditelnost před správci a správci můžou spravovat vlastnictví a viditelnost pro všechny datové assety v katalogu.

## <a name="summary"></a>Souhrn
Data Catalog crowdsourcingový model na metadata a data zjišťování prostředků umožňuje všem uživatelům katalogu přispívat a zjišťování. Standardní edice Data Catalog je určen pro vlastnictví a správu omezit viditelnost a použití konkrétní datové assety.
