---
title: Vysvětlení funkcí vyhledávání v Azure dosah (Preview)
description: Tento článek vysvětluje, jak Azure dosah umožňuje zjišťování dat prostřednictvím funkcí vyhledávání.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: af8ec9e0aac38240c7da92edd614892ff65712e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96552698"
---
# <a name="understand-search-features-in-azure-purview"></a>Vysvětlení funkcí vyhledávání v Azure dosah

Tento článek poskytuje přehled možností vyhledávání v Azure dosah. Hledání je základní funkcí platformy dosah, která slouží ke zjišťování dat a používání funkcí zásad správného řízení v organizaci.

## <a name="search"></a>Hledat

Dosah vyhledávání používá spravovaný index vyhledávání. Po zaregistrování zdroje dat ve službě dosah je jeho metadata indexována službou vyhledávání, aby bylo možné snadné zjišťování. Index poskytuje možnosti pro vyhledávání a dokončuje požadavky na hledání prostřednictvím dotazů na miliony assetů metadat. Hledání vám pomůže zjistit, pochopit a používat data a získat z nich co nejvíc hodnot.

Vyhledávací prostředí v dosah je třemi fázemi:

1. V poli hledání se zobrazuje historie obsahující nedávno použitá klíčová slova a prostředky.
1. Když začnete psát stisknutí kláves, hledání navrhne porovnávací klíčová slova a prostředky. 
1. Zobrazí se stránka výsledků hledání s prostředky, které odpovídají zadanému klíčovému slovu.

## <a name="reduce-the-time-to-discover-data"></a>Zkrátit čas na zjištění dat

Zjišťování dat je prvním krokem pro úlohy analýzy dat nebo zásad správného řízení dat pro příjemce dat. Zjišťování dat může být časově náročné, protože možná nevíte, kde najít požadovaná data. I po nalezení dat můžete mít pochybnosti o tom, jestli je nebo nemůžete důvěřovat datům a jak na nich pořídit závislosti. 

Cílem hledání ve službě Azure dosah je urychlení procesu zjišťování dat tím, že poskytuje gesta, která umožňují rychle najít data, která jsou v otázkách.

## <a name="recent-search-and-suggestions"></a>Poslední hledání a návrhy

V mnoha případech možná pracujete na několika projektech současně. Aby bylo snazší obnovit předchozí projekty, dosah Search nabízí možnost Zobrazit nedávná klíčová slova a návrhy hledání. Historii nedávných hledání můžete také spravovat tak, že v rozevíracím seznamu vyhledávacího pole vyberete **Zobrazit vše** .

## <a name="filters"></a>Filtry

Filtry (označované také jako *omezující vlastnosti*) jsou navržené k doplnění hledání. Filtry se zobrazují na stránce výsledků hledání. Filtry na stránce výsledků hledání zahrnují klasifikaci, popisek citlivosti, zdroj dat a vlastníky. Uživatelé mohou vybrat konkrétní hodnoty ve filtru, aby viděli pouze vyhovující datové prostředky, a omezit výsledek hledání na vyhovující prostředky.

## <a name="hit-highlighting"></a>Zvýrazňování položek

Porovnávací klíčová slova na stránce výsledků hledání jsou zvýrazněna, což usnadňuje identifikaci důvodů, proč byl datový Asset vrácen hledáním. Shoda klíčového slova se může vyskytovat ve více polích, jako je název datového assetu, popis a vlastník.

Nemusí se jednat o zjevné důvody, proč je datový Asset zahrnutý ve vyhledávání, a to i v případě, že je zapnuté zvýrazňování přístupů. Ve výchozím nastavení se prohledávají všechny vlastnosti. Proto může být datový Asset vrácen z důvodu shody s vlastností na úrovni sloupce. A vzhledem k tomu, že k datovým assetům může opatřit více uživatelů pomocí vlastních klasifikací a popisů, ne všechna metadata se zobrazí v seznamu výsledků hledání.

## <a name="sort"></a>Seřadit

Uživatelé mají dvě možnosti řazení výsledků hledání. Mohou třídit podle názvu assetu nebo podle výchozí relevance vyhledávání.

## <a name="search-relevance"></a>Hledat podle relevance

Relevance je výchozím pořadím řazení na stránce výsledků hledání. Relevance hledání najde dokumenty, které obsahují klíčové slovo Search (některé nebo všechny). Prostředky, které obsahují mnoho instancí klíčového slova hledání, jsou seřazené výše. Navíc se vlastní mechanizmus hodnocení neustále ladí, aby se zlepšila závažnost vyhledávání.

## <a name="next-steps"></a>Další kroky

* [Rychlý Start: vytvoření účtu Azure dosah v Azure Portal](create-catalog-portal.md)
* [Rychlý Start: vytvoření účtu Azure dosah pomocí Azure PowerShell/Azure CLI](create-catalog-powershell.md)
* [Rychlý Start: použití dosah studia](use-purview-studio.md)
