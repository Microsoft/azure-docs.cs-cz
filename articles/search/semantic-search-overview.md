---
title: Sémantické vyhledávání
titleSuffix: Azure Cognitive Search
description: Přečtěte si, jak Kognitivní hledání používá sémantické vyhledávací modely pro rozsáhlou výuku z Bingu, aby byly výsledky hledání intuitivnějšíější.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: e9cbb7daf61397064bd79f30d851d96fdf63f5a0
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102203228"
---
# <a name="semantic-search-in-azure-cognitive-search"></a>Sémantické vyhledávání v Azure Kognitivní hledání

> [!IMPORTANT]
> Funkce sémantického vyhledávání jsou ve verzi Public Preview dostupné jenom v rámci verze Preview REST API. Funkce ve verzi Preview se nabízejí tak, jak jsou, v části s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Sémantické vyhledávání je kolekce funkcí souvisejících s dotazy, které podporují vysoce kvalitní a přirozené možnosti dotazů. Mezi funkce patří sémantické přeřazení výsledků hledání a také popisky a generování odpovědí se sémantickým zvýrazňováním. Horní 50 výsledků vrácených z [fulltextového vyhledávacího stroje](search-lucene-query-architecture.md) se přeřadí do pořadí, aby bylo možné najít nejrelevantnější shody.

Základní technologie využívá investice z Bingu a Microsoft Research a je integrovaná do infrastruktury Kognitivní hledání. Pokud ho chcete použít, budete potřebovat malé úpravy žádosti o vyhledávání, ale nevyžaduje se žádná další konfigurace ani změna indexu.

Mezi funkce veřejné verze Preview patří:

+ Model sémantického hodnocení, který vyhodnotí výsledky na základě kontextu nebo sémantického významu termínů vyhledávacího dotazu
+ Sémantické titulky, které zvýrazňují relevantní pasáže
+ Sémantické odpovědi na dotaz, které se také formulují z výsledků
+ Kontrola pravopisu, která opraví překlepy před tím, než se vyhledají výrazy dotazu pro vyhledávací modul

## <a name="semantic-search-architecture"></a>Architektura sémantického vyhledávání

Komponenty sémantického hledání jsou vrstveny nad existujícím kanálem spuštění dotazu. Korekce pravopisu (nezobrazuje se v diagramu) zvyšuje počet odvolání opravou překlepů v jednotlivých výrazech dotazu. Po dokončení všech analýz a analýz vyhledávací modul načte dokumenty, které odpovídají dotazu, a vyhodnotí je pomocí [výchozího algoritmu bodování](index-similarity-and-scoring.md#similarity-ranking-algorithms), ať už BM25 nebo Classic, podle toho, kdy se služba vytvořila. V této fázi jsou také aplikovány profily vyhodnocování. 

Po přijetí prvních 50 shod se v [algoritmu sémantického hodnocení](semantic-how-to-query-response.md) znovu vyhodnocuje corpus dokumentu. Výsledky mohou zahrnovat více než 50 shod, ale pouze první 50 bude zařazeno. Pro účely hodnocení používá algoritmus strojové učení i přenosový program, aby se dokumenty znovu vyhodnocovat na základě toho, jak dobře se každý z nich shoduje s záměrem dotazu.

Chcete-li vytvořit titulky a odpovědi, používá modely reprezentace jazyka. V rámci titulku používá algoritmus jazykový model vyvinutý bingem k extrakci titulků s nejzajímavějšími výsledky dotazu. Pokud vyhledávací dotaz je otázka a jsou požadovány odpovědi, podobný jazykový model identifikuje text pasáže, který nejlépe odpovídá otázce vyjádřené vyhledávacím dotazem.

:::image type="content" source="media/semantic-search-overview/semantic-query-architecture.png" alt-text="Sémantické komponenty v kanálu dotazů" border="true":::

## <a name="availability-and-pricing"></a>Dostupnost a ceny

Sémantické hodnocení je dostupné prostřednictvím [registrace registrace](https://aka.ms/SemanticSearchPreviewSignup), a to u vyhledávacích služeb vytvořených na úrovni Standard (S1, S2, S3), které se nacházejí v jedné z těchto oblastí: Střed USA – sever, Západní USA, Západní USA 2, Východní USA 2, Severní Evropa, západní Evropa. Existující vyhledávací služba na S1 nebo vyšší v uvedených oblastech je pro verzi Preview platná (není nutné vytvářet novou službu).

Oprava pravopisu je k dispozici ve stejných oblastech, ale nemá žádná omezení na úrovni a nevyžaduje žádné požadavky na registraci. 

V období od 1. dubna se v rámci verze Preview spustí Oprava pravopisu a sémantické hodnocení bez poplatků. Od 1. dubna se výpočetní náklady na spuštění této funkce stanou fakturovatelnými událostmi. Očekávané náklady jsou přibližně USD $500 měsíčně za dotazy 250 000. Podrobné informace o nákladech, které jsou popsány na [stránce s cenami kognitivní hledání](https://azure.microsoft.com/pricing/details/search/) , najdete v části [odhad a Správa nákladů](search-sku-manage-costs.md).

## <a name="next-steps"></a>Další kroky

Nový typ dotazu povoluje hodnocení relevance a struktury odpovědí sémantického hledání. Pokud chcete začít, [vytvořte sémantický dotaz](semantic-how-to-query-request.md) . Případně můžete zobrazit související informace v některém z následujících článků.

+ [Přidat kontrolu pravopisu pro dotaz na výrazy](speller-how-to-add.md)
+ [Sémantické hodnocení a reakce](semantic-how-to-query-response.md)