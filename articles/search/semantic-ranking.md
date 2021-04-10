---
title: Sémantické hodnocení
titleSuffix: Azure Cognitive Search
description: Přečtěte si, jak algoritmus sémantického hodnocení funguje v Azure Kognitivní hledání.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: bf311eb2b2d0ff7a9c17380d2e384bc05c6f05f3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105562031"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Sémantické hodnocení v Azure Kognitivní hledání

> [!IMPORTANT]
> Funkce sémantického vyhledávání jsou ve verzi Public Preview dostupné prostřednictvím REST API a portálu ve verzi Preview. Funkce ve verzi Preview se nabízejí tak, jak jsou, v části s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)a nemají zaručenou stejnou implementaci při obecné dostupnosti. Tyto funkce jsou Fakturovatelné. Další informace najdete v tématu [dostupnost a ceny](semantic-search-overview.md#availability-and-pricing).

Sémantické hodnocení je rozšíření kanálu provádění dotazů, které vylepšuje přesnost tím, že přeřadí horní shody počáteční sady výsledků. Sémantické hodnocení je zajištěno pomocí rozsáhlých sítí založených na transformátorech, které jsou výukou pro zachycení sémantického významu termínů dotazu, na rozdíl od jazykového porovnání klíčových slov. Na rozdíl od [výchozího algoritmu řazení podobnosti](index-ranking-similarity.md)používá sémantický klasifikátor kontext a význam slov k určení relevance.

Sémantické hodnocení je náročné na prostředky i čas. Aby bylo možné dokončit zpracování v rámci očekávané latence operace dotazu, jsou vstupy sémantického klasifikátoru konsolidovány a snižovány tak, aby bylo možné dokončit základní souhrn a kroky přeřazení co nejrychleji.

## <a name="pre-processing"></a>Předběžné zpracování

Před vyhodnocením relevance musí být obsah zmenšen na spravovatelný počet vstupů, které je možné efektivně zvládnout sémantickým hodnocením.

1. Za prvé začíná snížení obsahu počáteční sadou výsledků vrácenou výchozím [algoritmem hodnocení podobnosti](index-ranking-similarity.md) použitým pro hledání klíčových slov. U každého konkrétního dotazu může být výsledkem několik dokumentů, a to až do maximálního limitu 1 000. Vzhledem k tomu, že zpracování velkého počtu shod by mohlo trvat příliš dlouho, bude pouze prvních 50 pokrok až po sémantické hodnocení.

   Bez ohledu na počet dokumentů, ať už jedna nebo 50, vytvoří počáteční sada výsledků první iteraci dokumentu corpus pro sémantické hodnocení.

1. V dalším corpus se obsah každého pole v "searchFields" extrahuje a spojí do dlouhého řetězce.

1. Po konsolidaci řetězců se všechny řetězce, které jsou příliš dlouhé, oříznou, aby celková délka splňovala vstupní požadavky kroku Shrnutí.

   Toto oříznutí cvičení je důvod, proč je důležité umístit Stručná pole jako první do "searchFields", aby se zajistilo jejich zahrnutí do řetězce. Pokud máte velmi velké dokumenty s textovými poli, vše po překročení maximálního limitu se ignoruje.

Každý dokument je nyní reprezentován jedním dlouhým řetězcem.

> [!NOTE]
> Řetězec se skládá z tokenů, nikoli znaků nebo slov. Tokenizace je určena v rámci přiřazení analyzátoru v polích, která lze prohledávat. Pokud používáte specializovaný analyzátor, jako je například nGram nebo EdgeNGram, můžete chtít toto pole vyloučit z searchFields. Pro přehledy o tom, jak jsou řetězce v tokenech, můžete zkontrolovat výstup tokenu analyzátoru pomocí [REST API analyzátoru testů](/rest/api/searchservice/test-analyzer).

## <a name="extraction"></a>Extrakce

Po zmenšení řetězce je teď možné předat omezené vstupy prostřednictvím strojového porozumění a modelů reprezentace jazyka, abyste zjistili, které věty a fráze nejlépe shrnují dokument v souvislosti s dotazem. Tato fáze extrahuje obsah z řetězce, který se přesune vpřed na sémantický rozsah.

Vstupy do sumarizace jsou dlouhé řetězce získané pro každý dokument ve fázi přípravy. Z každého řetězce model Shrnutí nalezne pasáž, která je nejvíce reprezentativní. Tato pasáž také představuje [sémantický titulek](semantic-how-to-query-request.md) pro dokument. Každý titulek je k dispozici ve verzi s prostým textem a zvýrazněnou verzí a je často méně než 200 slov na jeden dokument.

Pokud jste zadali parametr odpovědi, bude vrácena i [sémantická odpověď](semantic-answers.md) , pokud byl dotaz považován za otázku a v případě, že se pasáž nachází v dlouhém řetězci, který pravděpodobně poskytne odpověď na otázku.

## <a name="semantic-ranking"></a>Sémantické hodnocení

1. Popisky jsou vyhodnocovány pro koncepční a sémantickou relevanci relativně k poskytnutému dotazu.

   Následující diagram znázorňuje, co znamená "sémantická závažnost". Vezměte v úvahu pojem "kapitálový", který by se mohl používat v kontextu finančních, zákonných, geografických nebo gramatických. Pokud dotaz obsahuje termíny ze stejného vektorového prostoru (například "Velká" a "investice"), dokument, který zahrnuje i tokeny ve stejném clusteru, bude mít větší skóre než ta, která ne.

   :::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="Vektorová reprezentace pro kontext" border="true":::

1. @search.rerankerScoreJe přiřazen ke každému dokumentu na základě sémantické relevance titulku.

1. Po určení skóre všech dokumentů jsou uvedeny v sestupném pořadí podle skóre a zahrnuté v datové části odpovědi na dotaz. Datová část obsahuje odpovědi, prostý text a zvýrazněné titulky a všechna pole, která jste označili jako získatelné nebo zadaná v klauzuli SELECT.

## <a name="next-steps"></a>Další kroky

Sémantické hodnocení se nabízí na úrovních Standard, v určitých oblastech. Další informace o dostupnosti a registraci najdete v tématu [dostupnost a ceny](semantic-search-overview.md#availability-and-pricing). Nový typ dotazu povoluje struktury a reakce na sémantické vyhledávání. Začněte tím, [že vytvoříte sémantický dotaz](semantic-how-to-query-request.md).

Případně si přečtěte následující články o výchozím hodnocení. Sémantické hodnocení závisí na pořadí podobnosti, které vrací počáteční výsledky. Znalost provádění dotazů a hodnocení vám poskytne širokou znalost toho, jak celý proces funguje.

+ [Fulltextové vyhledávání v Azure Kognitivní hledání](search-lucene-query-architecture.md)
+ [Podobnost a bodování v Azure Kognitivní hledání](index-similarity-and-scoring.md)
+ [Analyzátory pro zpracování textu v Azure Kognitivní hledání](search-analyzers.md)