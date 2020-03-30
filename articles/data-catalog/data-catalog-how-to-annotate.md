---
title: Jak oznamovat zdroje dat v Azure Katalogu dat
description: Článek s postupem, který zvýrazňuje, jak oznamovat datové prostředky v Katalogu dat Azure, včetně popisných názvů, značek, popisů a odborníků.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: c1e022591ce1aee073330055744fbd78d97c0b1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68950262"
---
# <a name="how-to-annotate-data-sources-in-azure-data-catalog"></a>Jak oznamovat zdroje dat v Azure Katalogu dat

## <a name="introduction"></a>Úvod

**Microsoft Azure Data Catalog** je plně spravovaná cloudová služba, která slouží jako systém registrace a zjišťování pro zdroje podnikových dat. Jinými slovy, katalog dat je především o pomoci lidem objevovat, pochopit a používat zdroje dat a pomoci organizacím získat větší hodnotu z jejich stávajících dat. Když je zdroj dat registrován v katalogu dat, jeho metadata jsou zkopírována a indexována službou, ale příběh tím nekončí. Katalog dat umožňuje uživatelům poskytovat vlastní popisná metadata , která doplňují metadata extrahovaná ze zdroje dat a usnadňují srozumitelnější zdroj dat pro více lidí.

## <a name="annotation-and-crowdsourcing"></a>Anotace a crowdsourcing
Každý má svůj názor. A tohle je dobrá věc.
Katalog dat uznává, že různí uživatelé mají různé pohledy na zdroje podnikových dat a že každý z těchto pohledů může být cenný. Představte si následující scénář:

* Správce systému zná smlouvu o úrovni služeb pro servery nebo služby, které jsou hostitelem zdroje dat.
* Správce databáze zná plán zálohování pro každou databázi a povolené eTL zpracování oken.
* Vlastník systému zná proces, kdy mohou uživatelé požádat o přístup ke zdroji dat.
* Správce dat ví, jak se prostředky a atributy ve zdroji dat mapují na podnikový datový model.
* Analytik ví, jak jsou data používána v kontextu obchodních procesů, které podporují.

Každá z těchto perspektiv je cenná a katalog dat používá crowdsourcingový přístup k metadatům, který umožňuje, aby každá z nich byla zachycena a použita k poskytnutí úplného obrazu o registrovaných zdrojích dat. Pomocí portálu katalogu dat může každý uživatel přidávat a upravovat vlastní poznámky a zároveň zobrazovat poznámky poskytované jinými uživateli.

## <a name="different-types-of-annotations"></a>Různé typy poznámk
Katalog dat podporuje následující typy anotací:

| Poznámka | Poznámky |
| --- | --- |
| Popisný název |Popisné názvy mohou být poskytnuty na úrovni datového prostředku, aby byly datové prostředky snadněji srozumitelné. Popisné názvy jsou nejužitečnější, pokud je název základního objektu záhadný, zkrácený nebo jinak nesmysluplný pro uživatele. |
| Popis |Popisy mohou být dodány na datových aktiv a atribut / sloupec úrovně. Popisy jsou volné krátké textové poznámky, které popisují pohled uživatele na datový prostředek nebo jeho použití. |
| Značky (uživatelské značky) |Značky mohou být dodány na datových aktiv a atribut / sloupec úrovně. Uživatelské značky jsou uživatelem definované popisky, které lze použít ke kategorizaci datových prostředků nebo atributů. |
| Tagy (slovníčkové štítky) |Značky mohou být dodány na datových aktiv a atribut / sloupec úrovně. Značky glosáře jsou centrálně definované termíny glosáře, které lze použít ke kategorizaci datových datových prostředků nebo atributů pomocí běžné obchodní taxonomie. Další informace naleznete v článku [Jak nastavit obchodní glosář řízeným přidáváním značek](data-catalog-how-to-business-glossary.md) |
| Odborníků |Odborníci mohou být dodány na úrovni datových aktiv. Odborníci identifikují uživatele nebo skupiny s odbornými pohledy na data a mohou sloužit jako kontaktní místa pro uživatele, kteří zjišťují registrované zdroje dat a mají otázky, které nejsou zodpovězeny stávajícími anotacemi. |
| Vyžádání přístup |Informace o přístupu k požadavku mohou být poskytnuty na úrovni datového prostředku. Tyto informace jsou určeny uživatelům, kteří zjistí zdroj dat, ke kterému ještě nemají oprávnění k přístupu. Uživatelé mohou zadat e-mailovou adresu uživatele nebo skupiny, která uděluje přístup, adresu URL procesu nebo nástroje, ke kterému uživatelé potřebují získat přístup, nebo mohou zadat samotný proces jako text. |
| Dokumentace |Dokumentaci lze dodat na úrovni datového prostředku. Dokumentace k datovým zdrojům jsou informace ve formátovaném textu, které mohou obsahovat odkazy a obrázky a které mohou poskytovat jakékoli informace, které nejsou přenášeny prostřednictvím popisů a značek. |

## <a name="annotating-multiple-assets"></a>Anotace více datových zdrojů
Při výběru více datových datových prostředků na portálu katalogu dat mohou uživatelé oznamovat všechny vybrané datové zdroje v jedné operaci. Poznámky se budou vztahovat na všechny vybrané datové zdroje, což usnadňuje výběr a poskytování konzistentního popisu a sad značek a odborníků pro související datové datové prostředky.

> [!NOTE]
> Značky a odborníci mohou být také poskytnuty při registraci datových prostředků pomocí nástroje pro registraci zdroje dat v katalogu dat.
>
>

Při výběru více tabulek a zobrazení se na portálu katalogu dat zobrazí pouze sloupce, které mají společné všechny vybrané datové prostředky. To umožňuje uživatelům zadat značky a popisy pro všechny sloupce se stejným názvem pro všechny vybrané datové zdroje.

## <a name="annotations-and-discovery"></a>Poznámky a objevy
Stejně jako metadata extrahovaná ze zdroje dat během registrace je přidán a indexování katalogu dat, uživateli zadaná metadata je také indexována. To znamená, že nejen že poznámky usnadňují uživatelům pochopit data, která zjišťují, ale také usnadňují uživatelům zjišťování datových prostředků s anotací pomocí termínů, které jim dávají smysl.

## <a name="summary"></a>Souhrn
Registrace zdroje dat pomocí katalogu dat umožňuje zjistitelné data zkopírováním strukturálních a popisných metadat ze zdroje dat do služby Katalog. Po registraci zdroje dat mohou uživatelé poskytnout poznámky, které usnadňují zjišťování a pochopení z portálu katalogu dat.

## <a name="see-also"></a>Viz také
* [Začínáme s kurzem Katalogu dat Azure,](data-catalog-get-started.md) kde nazemte zdroje dat s podrobnými odpověďmi.
