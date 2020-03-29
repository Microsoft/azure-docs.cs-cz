---
title: Jak používat zdroje dat profilování dat v Katalogu dat Azure
description: Článek s postupem, který zvýrazňuje, jak zahrnout datové profily na úrovni tabulky a sloupce při registraci zdrojů dat v katalogu dat Azure a jak používat profily dat k pochopení zdrojů dat.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 04ac6c2bf0137289221a4ae6ed58d5a71ad21739
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68950227"
---
# <a name="how-to-data-profile-data-sources-in-azure-data-catalog"></a>Jak datové zdroje profilu v Katalogu dat Azure

## <a name="introduction"></a>Úvod

**Microsoft Azure Data Catalog** je plně spravovaná cloudová služba, která slouží jako systém registrace a zjišťování pro zdroje podnikových dat. Jinými slovy, **Azure Data Catalog** je především o pomoci lidem objevovat, pochopit a používat zdroje dat a pomáhá organizacím získat větší hodnotu z jejich stávajících dat. Když je zdroj dat registrovaný v **Katalogu dat Azure**, jeho metadata se zkopírují a indexují službou, ale příběh tím nekončí.

Funkce **profilování dat** **katalogu dat Azure** zkoumá data z podporovaných zdrojů dat ve vašem katalogu a shromažďuje statistiky a informace o těchto datech. Je snadné zahrnout profil datových prostředků. Při registraci datového prostředku zvolte **Zahrnout profil dat** v nástroji pro registraci zdroje dat.

## <a name="what-is-data-profiling"></a>Co je profilování dat

Profilování dat zkoumá data v registrovaném zdroji dat a shromažďuje statistiky a informace o těchto datech. Během zjišťování zdroje dat vám tyto statistiky mohou pomoci určit vhodnost dat k vyřešení jejich obchodního problému.

<!-- In [How to discover data sources](data-catalog-how-to-discover.md), you learn about **Azure Data Catalog's** extensive search capabilities including searching for data assets that have a profile. See [How to include a data profile when registering a data source](#howto). -->

Profilování dat podporují následující zdroje dat:

* Tabulky a zobrazení SQL Serveru (včetně Azure SQL DB a Azure SQL Data Warehouse)
* Tabulky a zobrazení oracle
* Teradata tabulky a zobrazení
* Tabulky Hive

Zahrnutí datových profilů při registraci datových prostředků pomáhá uživatelům odpovídat na otázky týkající se zdrojů dat, včetně:

* Může být použit k vyřešení mého obchodního problému?
* Odpovídají údaje určitým normám nebo vzorům?
* Jaké jsou některé anomálie zdroje dat?
* Jaké jsou možné problémy s integrací těchto dat do mé aplikace?

> [!NOTE]
> Můžete také přidat dokumentaci k datovému zdroji, která popisuje, jak by mohla být data integrována do aplikace. Viz [Jak dokumentovat zdroje dat](data-catalog-how-to-documentation.md).
>

<a name="howto"/>

## <a name="how-to-include-a-data-profile-when-registering-a-data-source"></a>Jak zahrnout datový profil při registraci zdroje dat

Je snadné zahrnout profil zdroje dat. Když zaregistrujete zdroj dat, v panelu **Objekty, které mají být registrovány** v nástroji pro registraci zdroje dat, zvolte **Zahrnout profil dat**.

![Zaškrtávací políčko Zahrnout datový profil](media/data-catalog-data-profile/data-catalog-register-profile.png)

Další informace o registraci zdrojů dat najdete v [tématu Jak zaregistrovat zdroje dat](data-catalog-how-to-register.md) a [začít s Azure Data Catalog](data-catalog-get-started.md).

## <a name="filtering-on-data-assets-that-include-data-profiles"></a>Filtrování datových prostředků, které obsahují datové profily

Chcete-li zjistit datové prostředky, které `has:tableDataProfiles` obsahují `has:columnsDataProfiles` datový profil, můžete zahrnout nebo jako jeden z hledaných výrazů.

> [!NOTE]
> Výběr **zahrnout profil dat** do registračního nástroje zdroje dat zahrnuje informace o profilu na úrovni tabulky i sloupce. Rozhraní API katalogu dat však umožňuje registraci datových prostředků pouze s jednou sadou informací o profilu.
>

## <a name="viewing-data-profile-information"></a>Zobrazení informací o datovém profilu

Jakmile najdete vhodný zdroj dat s profilem, můžete zobrazit podrobnosti o profilu dat. Chcete-li zobrazit datový profil, vyberte datový datový zdroj a v okně Portál katalogu dat zvolte **Datový profil.**

![Karta Profil dat](media/data-catalog-data-profile/data-catalog-view.png)

Datový profil v **Katalogu dat Azure** zobrazuje informace o tabulkách a sloupech, včetně:

### <a name="object-data-profile"></a>Profil dat objektu

* Počet řádků
* Velikost tabulky
* Kdy byl objekt naposledy aktualizován

### <a name="column-data-profile"></a>Profil dat sloupce

* Datový typ sloupce
* Počet odlišných hodnot
* Počet řádků s hodnotami NULL
* Minimální, maximální, průměrná a směrodatná odchylka pro hodnoty sloupců

## <a name="summary"></a>Souhrn

Profilování dat poskytuje statistiky a informace o registrovaných datových prostředcích, které vám pomohou určit vhodnost dat k řešení obchodních problémů. Spolu s anotací a dokumentováním zdrojů dat mohou profily dat poskytnout uživatelům hlubší pochopení vašich dat.

## <a name="see-also"></a>Viz také

* [Postup registrace zdrojů dat](data-catalog-how-to-register.md)
* [Začínáme s Azure Data Catalogem](data-catalog-get-started.md)
