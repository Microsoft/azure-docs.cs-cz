---
title: Jak používat zdroje dat profilování dat v Azure Data Catalog
description: Postup popisuje, jak zahrnout profily dat na úrovni tabulky a sloupců při registraci zdrojů dat v Azure Data Catalog a jak používat datové profily k pochopení zdrojů dat.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: b27ff631ce13d70d15a6fd2b6dd5ad5bccaf5450
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021907"
---
# <a name="how-to-data-profile-data-sources-in-azure-data-catalog"></a>Jak zdroje dat profilů dat v Azure Data Catalog

## <a name="introduction"></a>Úvod

**Microsoft Azure Data Catalog** je plně spravovaná cloudová služba, která slouží jako systém registrace a systém zjišťování pro podnikové zdroje dat. Jinými slovy **Azure Data Catalog** je vše, co je potřeba k tomu, aby lidé mohli zjišťovat, pochopit a používat zdroje dat a pomáhat organizacím získat větší hodnotu z jejich stávajících dat. Pokud je zdroj dat zaregistrován ve **Azure Data Catalog**, jeho metadata jsou zkopírována a indexována službou, ale tento scénář nekončí.

Funkce **profilace dat** **Azure Data Catalog** prověřuje data z podporovaných zdrojů dat ve vašem katalogu a shromažďuje statistické údaje a informace o těchto datech. Je snadné zahrnout profil datových assetů. Při registraci datového prostředku vyberte možnost **Zahrnout profil dat** do nástroje pro registraci zdroje dat.

## <a name="what-is-data-profiling"></a>Co je profilace dat

Profilace dat ověřuje data ve zdroji dat, který se zaregistruje, a shromažďuje statistické údaje a informace o těchto datech. Během zjišťování zdrojů dat vám tyto statistiky pomůžou určit vhodnost dat k vyřešení jejich obchodních problémů.

<!-- In [How to discover data sources](data-catalog-how-to-discover.md), you learn about **Azure Data Catalog's** extensive search capabilities including searching for data assets that have a profile. See [How to include a data profile when registering a data source](#howto). -->

Profilace dat podporují následující zdroje dat:

* SQL Server (včetně tabulek a zobrazení Azure SQL DB a Azure synapse Analytics)
* Tabulky a zobrazení Oracle
* Tabulky a zobrazení Teradata
* Tabulky Hive

Zahrnutí profilů dat při registraci datových assetů pomáhá uživatelům odpovídat na dotazy týkající se zdrojů dat, včetně:

* Dá se použít k vyřešení svého obchodního problému?
* Odpovídají data konkrétním standardům nebo vzorům?
* Jaké jsou některé anomálie zdroje dat?
* Co jsou možné problémy s integrací těchto dat do své aplikace?

> [!NOTE]
> Můžete také přidat dokumentaci k assetu, která popisuje, jak mohou být data integrována do aplikace. Podívejte [se, jak dokumentovat zdroje dat](data-catalog-how-to-documentation.md).
>

<a name="howto"></a>

## <a name="how-to-include-a-data-profile-when-registering-a-data-source"></a>Jak zahrnout datový profil při registraci zdroje dat

Je snadné zahrnout profil zdroje dat. Při registraci zdroje dat vyberte v panelu objekty, **které mají být registrovány** v nástroji pro registraci zdroje dat možnost **Zahrnout profil dat**.

![Zaškrtávací políčko Zahrnout datový profil](media/data-catalog-data-profile/data-catalog-register-profile.png)

Další informace o tom, jak registrovat zdroje dat, najdete v tématu [jak registrovat zdroje dat](data-catalog-how-to-register.md) a začít [s Azure Data Catalog](data-catalog-get-started.md).

## <a name="filtering-on-data-assets-that-include-data-profiles"></a>Filtrování datových assetů, které obsahují profily dat

Chcete-li zjistit datové assety, které obsahují datový profil, můžete zahrnout `has:tableDataProfiles` nebo `has:columnsDataProfiles` jako jeden z hledaných výrazů.

> [!NOTE]
> Výběr možnosti **Zahrnout profil dat** v nástroji pro registraci zdroje dat zahrnuje informace o profilu na úrovni tabulky i sloupce. Rozhraní Data Catalog API však umožňuje registraci datových assetů pouze s jednou sadou informací o profilu.
>

## <a name="viewing-data-profile-information"></a>Zobrazení informací o profilu dat

Jakmile najdete vhodný zdroj dat s profilem, můžete zobrazit podrobnosti o profilu dat. Chcete-li zobrazit datový profil, vyberte datový Asset a zvolte **datový profil** v okně data Catalogového portálu.

![Karta datový profil](media/data-catalog-data-profile/data-catalog-view.png)

Datový profil v **Azure Data Catalog** zobrazuje informace o profilu tabulky a sloupce, včetně:

### <a name="object-data-profile"></a>Datový profil objektu

* Počet řádků
* Velikost tabulky
* Čas poslední aktualizace objektu

### <a name="column-data-profile"></a>Datový profil sloupce

* Datový typ sloupce
* Počet jedinečných hodnot
* Počet řádků s hodnotami NULL
* Minimální, maximální, průměrná a směrodatná odchylka pro hodnoty sloupce

## <a name="summary"></a>Souhrn

Profilace dat poskytuje statistiku a informace o registrovaných datových prostředcích, které vám pomůžou určit vhodnost dat k řešení obchodních problémů. Spolu s přidáváním poznámek a dokumentací zdrojů dat můžou profily dat uživatelům získat hlubší přehled o vašich datech.

## <a name="see-also"></a>Viz také

* [Postup registrace zdrojů dat](data-catalog-how-to-register.md)
* [Začínáme s Azure Data Catalogem](data-catalog-get-started.md)
