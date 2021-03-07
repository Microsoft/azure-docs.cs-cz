---
title: Sémantické vyhledávání
titleSuffix: Azure Cognitive Search
description: Přečtěte si, jak Kognitivní hledání používá sémantické vyhledávací modely pro rozsáhlou výuku z Bingu, aby byly výsledky hledání intuitivnějšíější.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/05/2021
ms.custom: references_regions
ms.openlocfilehash: 19b7f9bc19bec989e524dce7172037025e2fe4fd
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102432975"
---
# <a name="semantic-search-in-azure-cognitive-search"></a>Sémantické vyhledávání v Azure Kognitivní hledání

> [!IMPORTANT]
> Funkce sémantického vyhledávání jsou ve verzi Public Preview dostupné jenom v rámci verze Preview REST API. Funkce ve verzi Preview se nabízejí tak, jak jsou, v části s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Sémantické vyhledávání je kolekce funkcí souvisejících s dotazy, které podporují vysoce kvalitní a přirozené možnosti dotazů. Mezi funkce patří sémantické přeřazení výsledků hledání a také popisky a generování odpovědí se sémantickým zvýrazňováním. Horní 50 výsledků vrácených z [fulltextového vyhledávacího stroje](search-lucene-query-architecture.md) se přeřadí do pořadí, aby bylo možné najít nejrelevantnější shody.

Základní technologie je z Bingu a Microsoft Research a integrovaná do infrastruktury Kognitivní hledání. Další informace o sémantickém vyhledávání v oblasti výzkumu a AI pro zálohování najdete v tématu [jak AI z Bingu používá Azure kognitivní hledání (blog Microsoft Research)](https://www.microsoft.com/research/blog/the-science-behind-semantic-search-how-ai-from-bing-is-powering-azure-cognitive-search/).

Pokud chcete v dotazech používat sémantické vyhledávání, budete muset provést drobné změny v žádosti o vyhledávání, ale není potřeba žádná další konfigurace ani změna indexu.

Mezi funkce veřejné verze Preview patří:

+ Model sémantického hodnocení, který používá kontext nebo sémantický význam k výpočtu skóre relevance
+ Sémantické titulky, které shrnují klíčové pasáže z výsledku pro snadné prohledávání
+ Sémantické odpovědi na dotaz, pokud dotaz je otázka
+ Sémantická světla, která přinášejí fokus na klíčové fráze a výrazy
+ Kontrola pravopisu, která opraví překlepy před tím, než se vyhledají výrazy dotazu pro vyhledávací modul

## <a name="availability-and-pricing"></a>Dostupnost a ceny

Sémantické hodnocení je dostupné prostřednictvím [registrace registrace](https://aka.ms/SemanticSearchPreviewSignup), a to u vyhledávacích služeb vytvořených na úrovni Standard (S1, S2, S3), které se nacházejí v jedné z těchto oblastí: Střed USA – sever, Západní USA, Západní USA 2, Východní USA 2, Severní Evropa, západní Evropa. Oprava pravopisu je k dispozici ve stejných oblastech, ale nemá žádná omezení vrstvy. Máte-li existující službu, která splňuje kritéria pro úroveň a oblast, je vyžadována pouze registrace.

V období od 1. dubna se v rámci verze Preview spustí Oprava pravopisu a sémantické hodnocení bez poplatků. Od 1. dubna se výpočetní náklady na spuštění této funkce stanou fakturovatelnými událostmi. Očekávané náklady jsou přibližně USD $500 měsíčně za dotazy 250 000. Podrobné informace o nákladech, které jsou popsány na [stránce s cenami kognitivní hledání](https://azure.microsoft.com/pricing/details/search/) , najdete v části [odhad a Správa nákladů](search-sku-manage-costs.md).

## <a name="semantic-search-architecture"></a>Architektura sémantického vyhledávání

Komponenty sémantického hledání jsou vrstveny nad existujícím kanálem spuštění dotazu. Korekce pravopisu (nezobrazuje se v diagramu) zvyšuje počet odvolání opravou překlepů v jednotlivých výrazech dotazu. Po dokončení analýzy a analýzy načte vyhledávací modul dokumenty, které odpovídají dotazu, a vyhodnotí je pomocí [výchozího algoritmu bodování](index-similarity-and-scoring.md#similarity-ranking-algorithms), ať už BM25 nebo Classic, podle toho, kdy se služba vytvořila. V této fázi jsou také aplikovány profily vyhodnocování.

Po přijetí prvních 50 shod se [model sémantického hodnocení](semantic-how-to-query-response.md) znovu vyhodnotí jako corpus dokumentu. Výsledky mohou zahrnovat více než 50 shod, ale pouze první 50 bude zařazeno. Pro účely hodnocení používá model strojové učení i přenosové učení k opakovanému vyhodnocení dokumentů na základě toho, jak dobře se každý z nich shoduje s záměrem dotazu.

Pro vytváření popisků a odpovědí používá sémantické prohledávání jazykovou reprezentaci pro extrakci a zvýraznění klíčových pasáží, které nejlépe shrnují výsledek. Pokud je vyhledávací dotaz otázka a jsou požadovány odpovědi, bude odpověď obsahovat text pasáž, který nejlépe odpoví na otázku, jak je vyjádřen vyhledávacím dotazem.

:::image type="content" source="media/semantic-search-overview/semantic-query-architecture.png" alt-text="Sémantické komponenty v kanálu dotazů" border="true":::

## <a name="next-steps"></a>Další kroky

Nový typ dotazu povoluje hodnocení relevance a struktury odpovědí sémantického hledání.

Pokud chcete začít, [vytvořte sémantický dotaz](semantic-how-to-query-request.md) . Případně můžete zobrazit související informace v některém z následujících článků.

+ [Přidat kontrolu pravopisu pro dotaz na výrazy](speller-how-to-add.md)
+ [Sémantické hodnocení a reakce (odpovědi a titulky)](semantic-how-to-query-response.md)