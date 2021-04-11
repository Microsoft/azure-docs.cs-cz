---
title: Registrace zdrojů dat v Azure Data Catalog
description: Tento článek popisuje, jak registrovat zdroje dat v Azure Data Catalog včetně polí metadat extrahovaných během registrace.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: fe132c5f80e138ef47db758ad42c04b8e854ca00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674797"
---
# <a name="register-data-sources-in-azure-data-catalog"></a>Registrace zdrojů dat v Azure Data Catalog

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>Úvod
Azure Data Catalog je plně spravovaná cloudová služba, která slouží jako systém registrace a zjišťování pro podnikové zdroje dat. Jinými slovy Data Catalog pomáhá lidem zjišťovat, pochopit a používat zdroje dat a pomáhá organizacím získat větší hodnotu z jejich stávajících dat. Prvním krokem k provedení zjistitelnosti zdroje dat prostřednictvím Data Catalog je registrace zdroje dat.

## <a name="register-data-sources"></a>Registrace zdrojů dat
Registrace je proces extrakce metadat ze zdroje dat a zkopírování těchto dat do služby Data Catalog. Data zůstanou uložena tam, kde v současnosti jsou, a zůstávají pod kontrolou správců a zásad aktuálního systému.

Chcete-li zaregistrovat zdroj dat, postupujte následovně:
1. Na portálu Azure Data Catalog spusťte nástroj pro registraci zdroje dat Data Catalog. 
2. Přihlaste se přes svůj pracovní nebo školní účet. se stejnými přihlašovacími údaji Azure Active Directory, které používáte k přihlášení na portál.
3. Vyberte zdroj dat, který chcete zaregistrovat.

Další podrobné informace najdete v kurzu [Začínáme s Azure Data Catalog](data-catalog-get-started.md) .

Po zaregistrování zdroje dat katalog sleduje jeho umístění a indexuje jeho metadata. Uživatelé mohou hledat, Procházet a zjišťovat zdroj dat a potom použít jeho umístění k připojení pomocí aplikace nebo nástroje podle svého výběru.

## <a name="supported-data-sources"></a>Podporované zdroje dat
Seznam aktuálně podporovaných zdrojů dat najdete v tématu [Data Catalog DSR](data-catalog-dsr.md).

## <a name="structural-metadata"></a>Strukturovaná metadata
Při registraci zdroje dat extrahuje nástroj registrace informace o struktuře objektů, které vyberete. Tyto informace se označují jako strukturovaná metadata.

Pro všechny objekty tato strukturální metadata obsahují umístění objektu, aby uživatelé, kteří data zjišťují, mohli tyto informace použít pro připojení k objektu v nástrojích klienta podle svého výběru. Další strukturální metadata obsahují název a typ objektu a název a název sloupce nebo sloupce a datový typ.

## <a name="descriptive-metadata"></a>Popisná metadata
Kromě základních strukturálních metadat, která jsou extrahována ze zdroje dat, nástroj pro registraci zdroje dat extrahuje popisné metadata. Pro Služba Analysis Services serveru SQL a SQL Server Reporting Services se tato metadata odebírají z vlastností popisu zveřejněných těmito službami. Pro SQL Server jsou extrahovány hodnoty zadané pomocí \_ Rozšířené vlastnosti Popis MS. Pro Oracle Database Nástroj pro registraci zdroje dat extrahuje sloupec komentáře ze \_ zobrazení komentáře na kartě všechny \_ .

Kromě popisných metadat, která jsou extrahována ze zdroje dat, mohou uživatelé zadat popisná metadata pomocí nástroje pro registraci zdroje dat. Uživatelé můžou přidávat značky a můžou identifikovat odborníky na registrované objekty. Všechna tato popisné metadata se zkopírují do služby Data Catalog spolu se strukturálními metadaty.

## <a name="include-previews"></a>Zahrnout náhledy
Ve výchozím nastavení se ze zdrojů dat extrahují jenom metadata a zkopírují se do služby Data Catalog, ale porozumění zdroji dat se často usnadňuje, když si můžete prohlédnout ukázku dat, která obsahuje.

Když použijete nástroj pro registraci zdroje dat Data Catalog, můžete zahrnout náhled snímku dat do každé tabulky a zobrazení, které je zaregistrované. Pokud se rozhodnete zahrnout náhledy během registrace, nástroj pro registraci zahrnuje až 20 záznamů z každé tabulky a zobrazení. Tento snímek se pak zkopíruje do katalogu společně se strukturálními a popisnými metadaty.

> [!NOTE]
> Široké tabulky s velkým počtem sloupců můžou mít ve verzi Preview méně než 20 záznamů.
>
>

## <a name="include-data-profiles"></a>Zahrnout profily dat
Stejně jako zahrnutí verzí Preview může poskytovat hodnotný kontext pro uživatele, kteří hledají zdroje dat v Data Catalog, včetně datového profilu, může usnadnit pochopení zjištěných zdrojů dat.

Když použijete nástroj pro registraci zdroje dat Data Catalog, můžete zahrnout profil dat pro každou tabulku a zobrazení, které je zaregistrované. Pokud se rozhodnete zahrnout profil dat během registrace, nástroj pro registraci zahrnuje agregované statistiky o datech v jednotlivých tabulkách a zobrazeních, včetně těchto:

* Počet řádků a velikost dat v objektu.
* Datum poslední aktualizace dat a schématu objektu.
* Počet záznamů s hodnotou null a jedinečné hodnoty pro sloupce.
* Minimální, maximální, průměrné a standardní hodnoty odchylky pro sloupce.

Tyto statistiky se pak zkopírují do katalogu společně se strukturálními a popisnými metadaty.

> [!NOTE]
> Sloupce text a datum nezahrnují statistiku průměrných nebo standardních odchylek ve svém datovém profilu.
>
>

## <a name="update-registrations"></a>Aktualizace registrací
Registrace zdroje dat umožňuje zjistitelnost v Data Catalog při použití metadat a volitelné verze Preview, která byla extrahována během registrace. Pokud je potřeba aktualizovat zdroj dat v katalogu (například pokud se změnilo schéma objektu, měly by být zahrnuté tabulky, které se původně vyloučily, nebo chcete aktualizovat data, která jsou obsažená v náhledech), můžete nástroj pro registraci zdroje dat spustit znovu.

Když se znovu zaregistrujete už registrovaný zdroj dat, provede se sloučení operace Upsert: stávající objekty se aktualizují a vytvoří se nové objekty. Všechna metadata, která poskytuje uživatelé prostřednictvím portálu Data Catalog, jsou zachována.

## <a name="summary"></a>Souhrn
Vzhledem k tomu, že kopíruje strukturální a popisné metadata ze zdroje dat do služby katalogu, registruje zdroj dat v Data Catalog usnadňuje zjišťování a pochopení dat. Po zaregistrování zdroje dat ho můžete opatřit poznámkami, spravovat a zjišťovat pomocí portálu Data Catalog.

## <a name="next-steps"></a>Další kroky
Další informace o registraci zdrojů dat najdete v kurzu [Začínáme s Azure Data Catalog](data-catalog-get-started.md) .
