---
title: Postup přidání poznámek ke zdrojům dat ve službě Azure Data Catalog
description: Článek zvýraznění jak opatřit poznámkami datové assety ve službě Azure Data Catalog, včetně popisné názvy, značky, popisy a odborníky.
services: data-catalog
author: steelanddata
ms.author: maroche
ms.assetid: 5a7e6bb2-863c-4eca-b614-1c814920d9ed
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: a6751a2cbb2a12d0d0ffe1eb0707ad4e8bc45ed8
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43053467"
---
# <a name="how-to-annotate-data-sources"></a>Postup přidání poznámek ke zdrojům dat
## <a name="introduction"></a>Úvod
**Microsoft Azure Data Catalog** je plně spravovaná Cloudová služba, která slouží jako systém registrace a systém zjišťování pro podnikové zdroje dat. Jinými slovy katalogu Data Catalog se točí kolem pomáháme lidem zjišťovat, pochopit a pomocí zdroje dat a pomáhá organizacím vytěžit více z jejich existující data. Když zdroj dat je zaregistrován ve službě Data Catalog, se zkopíruje a službou indexována, jeho metadata, ale scénář nekončí existuje. Data Catalog umožňuje uživatelům zadat svá vlastní popisná metadat – například popisy a značky – abyste doplnili metadata extrahovaná ze zdroje dat a aby byl srozumitelnější zdroj dat pro více lidí.

## <a name="annotation-and-crowdsourcing"></a>Poznámky a crowdsourcingu
Všichni mají k vyjádření. A to je dobrá věc.
Data Catalog rozpozná, že různí uživatelé mají různých perspektiv na podnikové zdroje dat. a že každý z těchto perspektiv může být důležité. Vezměte v úvahu následující scénář:

* Správce systému ví smlouvu o úrovni služeb pro servery nebo služby, které hostují zdroj dat.
* Správce databáze ví plán zálohování pro všechny databáze a systému windows povolené zpracování ETL.
* Vlastníka systému ví, uživatelé můžou žádat o přístup ke zdroji dat procesu.
* Společnosti steward data ví, jak namapovat prostředky a atributy ve zdroji dat do datového modelu enterprise.
* Analytik ví, jak se používají data v rámci obchodní procesy, které mu podporuje.

Každá z těchto perspektiv je vhodné a Data Catalog používá crowdsourcingový přístup k metadatům, která umožňuje každému z nich zachytit a poskytne ucelený přehled o registrovaných datových zdrojů. Pomocí portálu pro katalog dat, každý uživatel přidávat a upravovat své vlastní poznámky, nebudou moct zobrazit poznámky k dispozici jinými uživateli.

## <a name="different-types-of-annotations"></a>Různé druhy poznámky
Data Catalog podporuje následující typy poznámky:

| Poznámka | Poznámky |
| --- | --- |
| Popisný název |Na úrovni prostředku data, aby datových assetů srozumitelnější můžete zadat popisné názvy. Popisné názvy jsou nejužitečnější, když základní název objektu je nejasné, zkrácený nebo jinak nemá význam pro uživatele. |
| Popis |Popisy, lze je zadat v datovém assetu a atribut / sloupce úrovně. Popisy jsou krátké formě volného textu poznámky, které popisují uživatele Perspektiva na datový prostředek nebo jeho použití. |
| Značky (uživatele značky) |Značky lze je zadat v datovém assetu a atribut / sloupce úrovně. Značky uživatele jsou uživatelem definované popisky, které slouží ke kategorizaci datové assety nebo atributy. |
| Značky (Glosář značky) |Značky lze je zadat v datovém assetu a atribut / sloupce úrovně. Glosář značky jsou centrálně glosáře podmínky, které slouží ke kategorizaci datové assety nebo atributů pomocí běžné obchodní taxonomii. Další informace naleznete v článku [Jak nastavit obchodní glosář řízeným přidáváním značek](data-catalog-how-to-business-glossary.md) |
| Odborníků |Lze je zadat odborníků na úrovni datový asset. Odborníci identifikovat uživatele nebo skupiny s odborným pohledem na data a může sloužit jako body kontaktu pro uživatele, kteří zjišťovat registrované datové zdroje a máte otázky, na které nenajdete odpovědi v existujících poznámek. |
| Vyžádat si přístup |Lze je zadat žádost o přístup k informacím na úrovni datový asset. Tyto informace jsou pro uživatele, kteří se objeví zdroj dat, která ještě nemají oprávnění k přístupu. Uživatele můžete zadat e-mailovou adresu uživatele nebo skupiny, který uděluje přístup, adresa URL procesu nebo nástroj, který uživatelé potřebují k získání přístupu, nebo můžete zadat samotný proces jako text. |
| Dokumentace |Dokumentace ke službě, lze je zadat na úrovni datový asset. Dokumentace ke službě Asset je informace formátovaný text, který může obsahovat odkazy a obrázky a které poskytují všechny informace není předávají prostřednictvím značky a popisy. |

## <a name="annotating-multiple-assets"></a>Zadávání poznámek k více assetů
Při výběru více datových assetů v katalogu Data Catalog portál, můžete uživatele opatřit poznámkami všechny vybrané prostředky v rámci jedné operace. Poznámky platí pro všechny vybrané prostředky, což usnadňuje výběr a poskytnout konzistentní popis a sady značek a odborníky souvisejících datových prostředků.

> [!NOTE]
> Značky a odborníky lze také zadat při registraci datových assetů pomocí katalogu Data Catalog dat zdroje nástroj pro registraci.
>
>

Při výběru více tabulek nebo zobrazení, pouze sloupce, že všechny vybrané data, která mají společné prostředky zobrazí se v portál služby Data Catalog. To umožňuje uživatelům zadat značky a popisy pro všechny sloupce se stejným názvem pro všechny vybrané prostředky.

## <a name="annotations-and-discovery"></a>Poznámky a zjišťování
Stejně jako metadata extrahovaná přímo ze zdroje dat během registrace je přidat do indexu vyhledávání katalogu Data Catalog, uživatelem zadané metadata jsou také indexována. To znamená, že nejen poznámky usnadnit uživatelům pochopit data, která zjistí, poznámky také usnadňují uživatelům s poznámkami datové assety zjistit tak, že pomocí termínů, které dávají smysl k nim.

## <a name="summary"></a>Souhrn
Registrace zdroje dat pomocí služby Data Catalog umožňuje tato data zjistitelné zkopírováním popisný a strukturální metadata ze zdroje dat ve službě katalogu. Jakmile byl zaregistrován zdroji dat, mohou uživatelé zadat poznámky a zjednodušují zjišťování a pochopení z v rámci portálu katalogu Data Catalog.

## <a name="see-also"></a>Další informace najdete v tématech
* [Začínáme s Azure Data Catalog](data-catalog-get-started.md) kurz pro podrobné informace o přidání poznámek ke zdrojům dat.
