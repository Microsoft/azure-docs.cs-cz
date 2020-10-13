---
title: Postup přidání poznámek ke zdrojům dat v Azure Data Catalog
description: Postup popisuje, jak opatřit poznámkami datové assety v Azure Data Catalog, včetně popisných názvů, značek, popisů a expertů.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 61a91ca8a51886c28beee77853d1ae67911e00e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87081232"
---
# <a name="how-to-annotate-data-sources-in-azure-data-catalog"></a>Postup přidání poznámek ke zdrojům dat v Azure Data Catalog

## <a name="introduction"></a>Úvod

**Microsoft Azure Data Catalog** je plně spravovaná cloudová služba, která slouží jako systém registrace a systém zjišťování pro podnikové zdroje dat. Jinými slovy Data Catalog je vše, co je potřeba k tomu, aby lidé mohli zjišťovat, pochopit a používat zdroje dat a pomáhat organizacím získat větší hodnotu z jejich stávajících dat. Pokud je zdroj dat zaregistrován ve Data Catalog, jeho metadata jsou zkopírována a indexována službou, ale tento scénář nekončí. Data Catalog umožňuje uživatelům poskytnout jejich vlastní popisné metadata, jako jsou popisy a značky – k doplnění metadat extrahovaných ze zdroje dat a k tomu, aby byl zdroj dat lépe srozumitelný pro více lidí.

## <a name="annotation-and-crowdsourcing"></a>Anotace a crowdsourcingový
Každý má stanovisko. A to je dobrá věc.
Data Catalog rozpozná, že různí uživatelé mají různé perspektivy pro podnikové zdroje dat a že každé z těchto perspektiv může být cenné. Představte si následující scénář:

* Správce systému ví smlouvu o úrovni služeb pro servery nebo služby, které hostují daný zdroj dat.
* Správce databáze zná plán zálohování pro každou databázi a povolená okna zpracování ETL.
* Vlastník systému ví, jak proces, aby si uživatelé vyžádali přístup ke zdroji dat.
* Data Steward ví, jak jsou prostředky a atributy ve zdroji dat mapovány do podnikového datového modelu.
* Analytik ví, jak se data používají v kontextu obchodních procesů, které podporují.

Každé z těchto perspektiv je cenné a Data Catalog používá přístup crowdsourcingový k metadatům, které umožňují zachytit každý z nich a použít k poskytnutí kompletního přehledu registrovaných zdrojů dat. Pomocí Data Catalogového portálu mohou jednotliví uživatelé přidávat a upravovat svoje vlastní anotace a zobrazovat poznámky poskytované ostatními uživateli.

## <a name="different-types-of-annotations"></a>Různé typy poznámek
Data Catalog podporuje následující typy poznámek:

| Poznámka | Poznámky |
| --- | --- |
| Popisný název |Popisné názvy lze zadat na úrovni datového assetu, aby bylo možné snadněji pochopit datové prostředky. Popisné názvy jsou nejužitečnější, pokud je název podkladového objektu nešifrovaný, zkrácený nebo jinak nesmysluplný pro uživatele. |
| Description |Popisy lze zadat na úrovni assetů dat a atributů nebo sloupců. Popisy jsou krátké textové poznámky s volným tvarem, které popisují perspektivu uživatele na datovém assetu nebo jeho použití. |
| Značky (uživatelské značky) |Značky lze zadat na úrovni assetů dat a atributů nebo sloupců. Uživatelské značky jsou uživatelsky definované popisky, které lze použít k kategorizaci datových prostředků nebo atributů. |
| Značky (Glosář glosáře) |Značky lze zadat na úrovni assetů dat a atributů nebo sloupců. Značky glosáře jsou centrálně definované výrazy glosáře, které lze použít ke kategorizaci datových assetů nebo atributů pomocí běžné obchodní taxonomie. Další informace naleznete v článku [Jak nastavit obchodní glosář řízeným přidáváním značek](data-catalog-how-to-business-glossary.md) |
| Odborníky |Odborníky mohou být dodány na úrovni datového assetu. Odborníci identifikují uživatele nebo skupiny pomocí odborných perspektiv na data a můžou sloužit jako kontaktní body pro uživatele, kteří zjišťují registrované zdroje dat a mají otázky, na které neodpoví stávající poznámky. |
| Vyžádání přístup |Žádosti o přístup k informacím lze zadat na úrovni datového assetu. Tyto informace jsou pro uživatele, kteří zjišťují zdroj dat, ke kterému ještě nemají oprávnění k přístupu. Uživatelé mohou zadat e-mailovou adresu uživatele nebo skupiny, kteří udělí přístup, adresu URL procesu nebo nástroje, které uživatelé potřebují k získání přístupu, nebo mohou zadat samotný proces jako text. |
| Dokumentace |Dokumentaci lze dodávat na úrovni datového assetu. Dokumentace k assetům je bohatá textová informace, která může obsahovat odkazy a obrázky, které mohou poskytnout jakékoli informace, které nejsou předány popisy a značkami. |

## <a name="annotating-multiple-assets"></a>Přidávání poznámek k několika prostředkům
Při výběru více datových assetů na portálu Data Catalog můžou uživatelé přidávat poznámky k vybraným prostředkům v rámci jedné operace. Poznámky budou platit pro všechny vybrané prostředky, což usnadňuje výběr a poskytování konzistentního popisu a sad značek a odborníků pro související datové prostředky.

> [!NOTE]
> Značky a odborníky je také možné poskytnout při registraci datových assetů pomocí nástroje Data Catalog pro registraci zdroje dat.
>
>

Při výběru více tabulek a zobrazení budou na portálu Data Catalog zobrazeny pouze sloupce, které jsou všechny vybrané datové prostředky společné. To umožňuje uživatelům poskytnout značky a popisy pro všechny sloupce se stejným názvem pro všechny vybrané prostředky.

## <a name="annotations-and-discovery"></a>Poznámky a zjišťování
Stejně jako metadata extrahovaná ze zdroje dat během registrace se přidají do indexu hledání Data Catalog, uživatelsky zadaná metadata se také indexují. To znamená, že nejenom anotace usnadňují uživatelům pochopení dat, která zjišťují, poznámky také usnadňují uživatelům vyhledávání datových assetů s poznámkami, a to pomocí podmínek, které jim dávají smysl.

## <a name="summary"></a>Shrnutí
Registrace zdroje dat pomocí Data Catalog zajišťuje, aby byla data zjistitelná zkopírováním strukturních a popisných metadat ze zdroje dat do služby katalogu. Po zaregistrování zdroje dat můžou uživatelé poskytnout poznámky, které usnadňují zjišťování a pochopení v rámci portálu Data Catalog.

## <a name="see-also"></a>Viz také
* [Začínáme s](data-catalog-get-started.md) kurzem pro Azure Data Catalog, kde najdete podrobné informace o tom, jak přidávat poznámky ke zdrojům dat.
