---
title: Registrace zdrojů dat ve službě Azure Data Catalog
description: Tento článek popisuje postup registrace zdrojů dat ve službě Azure Data Catalog, včetně polí metadata extrahovaná během registrace.
services: data-catalog
author: steelanddata
ms.author: maroche
ms.assetid: bab89906-186f-4d35-9ffd-61b1d903905d
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 4688b58b40df110a33f9310226db9a6412f43054
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43053387"
---
# <a name="register-data-sources-in-azure-data-catalog"></a>Registrace zdrojů dat ve službě Azure Data Catalog
## <a name="introduction"></a>Úvod
Azure Data Catalog je plně spravovaná Cloudová služba, která slouží jako systém registrace a systém zjišťování pro podnikové zdroje dat. Jinými slovy katalogu Data Catalog umožňuje uživatelům zjišťovat, pochopit a používat zdroje dat a pomáhá organizacím vytěžit více z jejich existující data. Prvním krokem při vytváření zdroje dat objevitelný prostřednictvím katalogu Data Catalog je zaregistrovat tento zdroj dat.

## <a name="register-data-sources"></a>Registrace zdrojů dat
Registrace je proces extrahování metadat ze zdroje dat a kopírování dat do služby Data Catalog. Data zůstanou uložena tam, kde v současnosti jsou, a zůstávají pod kontrolou správců a zásad aktuálního systému.

K registraci zdroje dat, postupujte takto:
1. Na portálu Azure Data Catalog spusťte nástroj registrace zdroje dat katalogu Data Catalog. 
2. Přihlaste se pomocí svého pracovního nebo školního účtu pomocí stejné přihlašovací údaje Azure Active Directory, které používáte k přihlášení k portálu.
3. Vyberte zdroj dat, který chcete zaregistrovat.

Další podrobné informace najdete v článku [Začínáme s Azure Data Catalog](data-catalog-get-started.md) kurzu.

Po zaregistrování zdroje dat, v katalogu sleduje jeho umístění a indexuje jeho metadata. Uživatelům můžete hledat, procházet a zjistit zdroje dat a pak použít její umístění k němu připojit pomocí aplikace nebo nástroj podle vlastní volby.

## <a name="supported-data-sources"></a>Podporované zdroje dat
Seznam aktuálně podporovaných zdrojů dat naleznete v tématu [Data Catalog DSR](data-catalog-dsr.md).

## <a name="structural-metadata"></a>Strukturální metadata
Při registraci zdroje dat, nástroje pro registraci extrahuje informace o struktuře objekty, které jste vybrali. Tyto informace se označuje jako strukturální metadata.

Pro všechny objekty tento strukturální metadata obsahují umístění objektu tak, aby uživatelé, kteří je zjistili data tyto informace slouží k připojení k objektu v nástrojích klienta podle vlastní volby. Další strukturální metadata obsahují název objektu a typ a zadejte název atributu/sloupce a data.

## <a name="descriptive-metadata"></a>Popisná metadata
Nástroj registrace zdroje dat kromě základní strukturální metadata extrahovaná ze zdroje dat, extrahuje popisnými metadaty. Pro SQL Server Analysis Services a SQL Server Reporting Services tato metadata je převzata z popis vlastností vystavovaných třídami těchto služeb. Pro SQL Server, hodnoty poskytnuté pomocí ms\_je extrahován rozšířená vlastnost Popis. Nástroje pro registraci zdroje dat pro Oracle Database, extrahuje sloupce KOMENTÁŘE z všechny\_kartu\_zobrazení KOMENTÁŘŮ.

Kromě popisných metadat, které je extrahován ze zdroje dat můžete uživatele zadat popisná metadata pomocí registračního nástroje zdroje dat. Uživatelé můžou přidávat značky a identifikují odborníků pro objekty, které jsou registrovány. Tato popisná metadata je zkopírován do služby Data Catalog spolu s strukturální metadata.

## <a name="include-previews"></a>Zahrnout verze Preview
Ve výchozím nastavení je pouze metadata extrahovaná ze zdroje dat a zkopírovat do služby Data Catalog, ale princip, který zdroj dat je často snazší zobrazíte ukázková data, která obsahuje.

Pomocí nástroje pro registraci zdroje dat katalogu Data Catalog, můžete zahrnout snímek náhledu dat v jednotlivých tabulek a zobrazení, které je zaregistrovaný. Pokud budete chtít zahrnout náhledy během registrace, nástroje pro registraci zahrnuje až 20 záznamů z každé tabulky a zobrazení. Tento snímek pak zkopíruje do katalogu spolu s strukturální a popisných metadat.

> [!NOTE]
> Širokých tabulek s velkým počtem sloupců může mít méně než 20 záznamů zahrnutých ve své verzi preview.
>
>

## <a name="include-data-profiles"></a>Zahrnout profilů dat
Stejně jako, včetně náhledů může poskytnout cenné kontext pro uživatele, kteří vyhledejte zdroje dat ve službě Data Catalog, včetně dat profilu můžete usnadňují porozumět zdrojům zjištěná data.

Pomocí nástroje pro registraci zdroje dat katalogu Data Catalog, můžete zahrnout profil dat pro jednotlivé tabulky a zobrazení, které je zaregistrovaný. Pokud budete chtít zahrnout profil dat během registrace, nástroje pro registraci obsahuje souhrnné statistiky o data v každé tabulky a zobrazení, včetně:

* Počet řádků a množství dat v objektu.
* Datum poslední aktualizace dat a schématu objektu.
* Počet záznamů null a různé hodnoty pro sloupce.
* Minimum, maximum, průměr a směrodatné odchylky hodnot pro sloupce.

Tyto statistické údaje jsou poté zkopírován do katalogu spolu s strukturální a popisných metadat.

> [!NOTE]
> Text a datum sloupce nezahrnují statistiku průměr nebo směrodatná odchylka v jejich data profilu.
>
>

## <a name="update-registrations"></a>Aktualizace registrace
Registrace zdroje dat díky zjistitelné ve službě Data Catalog při použití metadat a volitelné preview extrahovat během registrace. Pokud zdroj dat je potřeba aktualizovat v katalogu (například pokud schéma objektu se změnilo, tabulky původně vyloučené by měly být zahrnuty nebo chcete aktualizovat data, která je součástí verze Preview), nástroj registrace zdroje dat můžete znovu spustit.

Opakovanou registrací zdroje dat už zaregistrovaný rozhraní provádí operaci sloučení "upsert": existující objekty jsou aktualizovány a vytvoření nových objektů. Veškerá metadata, která poskytuje uživatelům prostřednictvím portálu pro Data Catalog se zachovají.

## <a name="summary"></a>Souhrn
Protože kopíruje popisný a strukturální metadata ze zdroje dat ve službě katalogu, registrace zdroje dat ve službě Data Catalog je snadnější data zjišťování a pochopení. Po registraci zdroje dat, opatřit poznámkami a spravovat jej zjistit pomocí portálu pro Data Catalog.

## <a name="next-steps"></a>Další postup
Další informace o registraci zdrojů dat, najdete v článku [Začínáme s Azure Data Catalog](data-catalog-get-started.md) kurzu.
