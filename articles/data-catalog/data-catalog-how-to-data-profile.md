---
title: Jak pracovat s daty profilování zdroje dat ve službě Azure Data Catalog
description: Článek zvýraznění zahrnují profily úrovni tabulky a sloupce dat, při registraci zdroje dat ve službě Azure Data Catalog a porozumět zdrojům dat pomocí data profilů.
services: data-catalog
author: markingmyname
ms.author: maghan
ms.assetid: 94a8274b-5c9c-4962-a4b1-2fed38a3d919
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: b40bbe7d1eb89f934d6ae07688e698f1949f8e76
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47410234"
---
# <a name="data-profile-data-sources"></a>Zdroje dat profilů dat
## <a name="introduction"></a>Úvod
**Microsoft Azure Data Catalog** je plně spravovaná Cloudová služba, která slouží jako systém registrace a systém zjišťování pro podnikové zdroje dat. Jinými slovy **Azure Data Catalog** se točí kolem lidé zjišťovat, pochopit a používat zdroje dat a pomáhá organizacím vytěžit více z jejich stávajících dat. Když je zdroj dat zaregistrován ve službě **Azure Data Catalog**, se zkopíruje a službou indexována, jeho metadata, ale scénář nekončí existuje.

**Data profilace** funkce **Azure Data Catalog** zkontroluje data z podporovaných zdrojů dat ve vašem katalogu a shromažďuje statistické údaje a informace o těchto datech. Je snadné zahrnout profil tvořeného těmito daty. Když si zaregistrujete k datovému assetu, zvolte **zahrnout profil dat** v nástroji pro registraci zdroje dat.

## <a name="what-is-data-profiling"></a>Co je dat profilace
Data profilování zkontroluje dat ve zdroji dat, které jsou registrovány a shromažďuje statistické údaje a informace o těchto datech. Během zjišťování zdrojů dat ve statistikách vám může pomoct určit vhodnost data k vyřešení obchodního problému.

<!-- In [How to discover data sources](data-catalog-how-to-discover.md), you learn about **Azure Data Catalog's** extensive search capabilities including searching for data assets that have a profile. See [How to include a data profile when registering a data source](#howto). -->

Následující zdroje dat podporují data profilování:

* Tabulky a zobrazení SQL serveru (včetně Azure SQL Database a Azure SQL Data Warehouse)
* Oracle tabulek a zobrazení
* Teradata tabulek a zobrazení
* Tabulek Hive

Včetně profily dat při registraci datových assetů uživatelům pomůžou odpovědět na otázky o zdrojích dat, včetně:

* Lze ji použít k vyřešení obchodního problému?
* Data v souladu s konkrétní standardy nebo vzorce?
* V čem spočívají anomálií zdroje dat?
* Jaké jsou možné problémy integrace těchto dat do mé aplikace?

> [!NOTE]
> Dokumentace ke službě můžete také přidat na prostředek, popisující, jak může integrovat data do aplikace. Zobrazit [postup dokumentování zdrojů dat](data-catalog-how-to-documentation.md).
>
>

<a name="howto"/>

## <a name="how-to-include-a-data-profile-when-registering-a-data-source"></a>Postup při registraci zdroje dat zahrnutí dat profilu
Je snadné zahrnout profil dat zdroje. Při registraci zdroje dat, v **objekty k registraci** panel Nástroj registrace zdroje dat, zvolte **zahrnout profil dat**.

![](media/data-catalog-data-profile/data-catalog-register-profile.png)

Další informace o tom, jak zaregistrovat zdroje dat, naleznete v tématu [postup registrace zdrojů dat](data-catalog-how-to-register.md) a [Začínáme s Azure Data Catalog](data-catalog-get-started.md).

## <a name="filtering-on-data-assets-that-include-data-profiles"></a>Filtrování datových assetů, které zahrnují data profily
Ke zjištění datových assetů, které zahrnují data profilu, můžete zahrnout `has:tableDataProfiles` nebo `has:columnsDataProfiles` jako jeden z hledané výrazy.

> [!NOTE]
> Výběr **zahrnout profil dat** ve zdroji dat obsahuje nástroj pro registraci tabulky a informace o profilu úrovni sloupce. Rozhraní API katalogu Data Catalog, ale umožňuje datových assetů, které mají být registrována pomocí pouze jednu sadu zahrnuty informace o profilu.
>
>

## <a name="viewing-data-profile-information"></a>Zobrazení informací o profilu dat
Jakmile najdete vhodné datovému zdroji prostřednictvím profilu, můžete zobrazit podrobnosti dat profilu. Chcete-li zobrazit profil dat, vyberte datový prostředek a zvolte **profil dat** v okně portálu katalogu Data Catalog.

![](media/data-catalog-data-profile/data-catalog-view.png)

Profil dat v **Azure Data Catalog** zobrazuje tabulky a sloupce profil informace včetně:

### <a name="object-data-profile"></a>Profil dat objektu
* Počet řádků
* Velikost tabulky
* Kdy byl objekt naposledy aktualizována

### <a name="column-data-profile"></a>Profil sloupce dat.
* Datový typ sloupce
* Počet jedinečných hodnot
* Počet řádků s hodnotami NULL
* Minimum, maximum, průměr a směrodatné odchylky hodnot sloupců

## <a name="summary"></a>Souhrn
Data profilování poskytuje statistické údaje a informace o registrovaných datových assetů, které vám pomohou určit vhodnost dat k řešení obchodních problémů. Zadávání poznámek a dokumentování zdrojů dat, data profily můžete uživatelům udělit lépe pochopit vaše data.

## <a name="see-also"></a>Viz také
* [Postup registrace zdrojů dat](data-catalog-how-to-register.md)
* [Začínáme s Azure Data Catalogem](data-catalog-get-started.md)
