---
title: Registrace zdrojů dat v katalogu dat Azure
description: Tento článek upozorňuje, jak zaregistrovat zdroje dat v Katalogu dat Azure, včetně polí metadat extrahovaných během registrace.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 0c5fdac7df41fec3a6206dbd78af74b7f1b58c7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68736327"
---
# <a name="register-data-sources-in-azure-data-catalog"></a>Registrace zdrojů dat v katalogu dat Azure
## <a name="introduction"></a>Úvod
Azure Data Catalog je plně spravovaná cloudová služba, která slouží jako systém registrace a zjišťování pro zdroje podnikových dat. Jinými slovy, katalog dat pomáhá lidem zjišťovat, chápat a používat zdroje dat a pomáhá organizacím získat větší hodnotu ze svých stávajících dat. Prvním krokem k tomu, aby byl zdroj dat zjistitelný prostřednictvím katalogu dat, je registrace tohoto zdroje dat.

## <a name="register-data-sources"></a>Registrace zdrojů dat
Registrace je proces extrahování metadat ze zdroje dat a kopírování těchto dat do služby katalogu dat. Data zůstanou uložena tam, kde v současnosti jsou, a zůstávají pod kontrolou správců a zásad aktuálního systému.

Chcete-li zaregistrovat zdroj dat, postupujte takto:
1. Na portálu Katalogu dat Azure spusťte nástroj pro registraci zdroje dat v katalogu dat. 
2. Přihlaste se pomocí pracovního nebo školního účtu pomocí stejných přihlašovacích údajů služby Azure Active Directory, které používáte k přihlášení k portálu.
3. Vyberte zdroj dat, který chcete zaregistrovat.

Další podrobné informace najdete v tématu [Začínáme s](data-catalog-get-started.md) azure katalog dat kurzu.

Po registraci zdroje dat katalog sleduje jeho umístění a indexuje jeho metadata. Uživatelé mohou prohledávat, procházet a zjišťovat zdroj dat a potom použít jeho umístění k jeho připojení pomocí aplikace nebo nástroje podle vlastního výběru.

## <a name="supported-data-sources"></a>Podporované zdroje dat
Seznam aktuálně podporovaných zdrojů dat naleznete v [tématu Data Catalog DSR](data-catalog-dsr.md).

## <a name="structural-metadata"></a>Strukturální metadata
Při registraci zdroje dat nástroj pro registraci extrahuje informace o struktuře vybraných objektů. Tyto informace se označují jako strukturální metadata.

Pro všechny objekty tato strukturální metadata zahrnuje umístění objektu, takže uživatelé, kteří zjišťují data, mohou tyto informace použít k připojení k objektu v klientských nástrojích podle vlastního výběru. Další strukturální metadata zahrnují název a typ objektu a název atributu/sloupce a datový typ.

## <a name="descriptive-metadata"></a>Popisná metadata
Kromě základních strukturálních metadat, která jsou extrahována ze zdroje dat, nástroj pro registraci zdroje dat extrahuje popisná metadata. Pro SQL Server Analysis Services a SQL Server Reporting Services tato metadata je převzata z Description vlastnosti vystavené tyto služby. Pro SQL Server jsou extrahovány\_hodnoty poskytované pomocí rozšířené vlastnosti ms description. V databázi Oracle Database extrahuje nástroj pro registraci zdroje dat sloupec KOMENTÁŘE ze zobrazení KOMENTÁŘE VŠECHNY\_TAB.\_

Kromě popisných metadat extrahovaných ze zdroje dat mohou uživatelé zadávat popisná metadata pomocí nástroje pro registraci zdroje dat. Uživatelé mohou přidávat značky a mohou identifikovat odborníky pro registrované objekty. Všechna tato popisná metadata se zkopírují do služby Katalog dat spolu se strukturálními metadaty.

## <a name="include-previews"></a>Zahrnout náhledy
Ve výchozím nastavení jsou ze zdrojů dat extrahována pouze metadata a zkopírována do služby Katalog dat, ale pochopení zdroje dat je často usnadněno, když můžete zobrazit ukázku dat, která obsahuje.

Pomocí nástroje registrace zdroje dat katalogu dat můžete zahrnout náhled snímku dat v každé tabulce a zobrazení, které je registrováno. Pokud se rozhodnete zahrnout náhledy během registrace, registrační nástroj obsahuje až 20 záznamů z každé tabulky a zobrazení. Tento snímek je pak zkopírován do katalogu spolu se strukturálními a popisnými metadaty.

> [!NOTE]
> Široké tabulky s velkým počtem sloupců mohou mít v náhledu méně než 20 záznamů.
>
>

## <a name="include-data-profiles"></a>Zahrnout datové profily
Stejně jako zahrnutí náhledů může poskytnout cenný kontext pro uživatele, kteří vyhledávají zdroje dat v katalogu dat, včetně datového profilu může usnadnit pochopení zjištěných zdrojů dat.

Pomocí nástroje registrace zdroje dat katalogu dat můžete zahrnout profil dat pro každou tabulku a zobrazení, které je registrováno. Pokud se rozhodnete zahrnout datový profil během registrace, registrační nástroj obsahuje souhrnné statistiky o datech v každé tabulce a zobrazení, včetně:

* Počet řádků a velikost dat v objektu.
* Datum poslední aktualizace dat a schématu objektu.
* Počet nulových záznamů a odlišné hodnoty pro sloupce.
* Minimální, maximální, průměrné a směrodatné odchylky pro sloupce.

Tyto statistiky jsou pak zkopírovány do katalogu spolu se strukturálními a popisnými metadaty.

> [!NOTE]
> Sloupce textu a data nezahrnují ve svém datovém profilu statistiky průměrné nebo směrodatné odchylky.
>
>

## <a name="update-registrations"></a>Aktualizovat registrace
Registrace zdroje dat umožňuje zjistitelné v katalogu dat při použití metadat a volitelný náhled extrahované během registrace. Pokud je třeba aktualizovat zdroj dat v katalogu (například pokud došlo ke změně schématu objektu, měly by být zahrnuty původně vyloučené tabulky nebo chcete aktualizovat data, která jsou zahrnuta v náhledech), lze nástroj pro registraci zdroje dat znovu spustit.

Opětovná registrace již registrovaného zdroje dat provede operaci sloučení "upsert": existující objekty jsou aktualizovány a jsou vytvořeny nové objekty. Všechna metadata poskytnutá uživateli prostřednictvím portálu katalogu dat jsou zachována.

## <a name="summary"></a>Souhrn
Vzhledem k tomu, že kopíruje strukturální a popisná metadata ze zdroje dat do služby katalogu, registrace zdroje dat v katalogu dat usnadňuje zjišťování a pochopení dat. Po registraci zdroje dat jej můžete oznamovat, spravovat a zjišťovat pomocí portálu katalogu dat.

## <a name="next-steps"></a>Další kroky
Další informace o registraci zdrojů dat najdete v tématu [Začínáme s](data-catalog-get-started.md) Azure Katalog dat kurzu.
