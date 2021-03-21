---
title: Sémantické hodnocení
titleSuffix: Azure Cognitive Search
description: Přečtěte si, jak algoritmus sémantického hodnocení funguje v Azure Kognitivní hledání.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: bb65a53f1ba6e97a39bd0c0170c5c41da38aee8b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "104720504"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Sémantické hodnocení v Azure Kognitivní hledání

> [!IMPORTANT]
> Funkce sémantického vyhledávání jsou ve verzi Public Preview dostupné jenom v rámci verze Preview REST API. Funkce ve verzi Preview se nabízejí tak, jak jsou, v části s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)a nemají zaručenou stejnou implementaci při obecné dostupnosti. Tyto funkce jsou Fakturovatelné. Další informace najdete v tématu [dostupnost a ceny](semantic-search-overview.md#availability-and-pricing).

Sémantické hodnocení je rozšířením kanálu spuštění dotazu, který vylepšuje přesnost a vrácení zpět změnou pořadí horních shod počáteční sady výsledků. Sémantické hodnocení je zajištěné špičkovými modely pro čtení počítačů, školení pro dotazy vyjádřené v přirozeném jazyce na rozdíl od jazykového porovnání klíčových slov. Na rozdíl od [výchozího algoritmu řazení podobnosti](index-ranking-similarity.md)používá sémantický klasifikátor kontext a význam slov k určení relevance.

Sémantické hodnocení je náročné na prostředky i čas. Aby bylo možné dokončit zpracování v rámci očekávané latence operace dotazu, jsou vstupy konsolidovány a zjednodušeny, takže Shrnutí a analýza lze dokončit co nejrychleji.

## <a name="preparation-for-semantic-ranking"></a>Příprava pro sémantické hodnocení

Před vyhodnocením relevance musí být obsah zmenšen na množství parametrů, které je možné efektivně zvládnout sémantickým hodnocením. Snížení obsahu zahrnuje následující posloupnost kroků.

1. Snížení obsahu začíná pomocí počátečních výsledků vrácených výchozím [algoritmem řazení podobnosti](index-ranking-similarity.md) používaného pro hledání klíčových slov. Výsledky hledání můžou zahrnovat až 1 000 shod, ale sémantické hodnocení zpracuje jenom prvních 50. 

   V závislosti na tom, kolik shod bylo nalezeno, mohou být počáteční výsledky mnohem menší než 50. Bez ohledu na počet dokumentů je počáteční sada výsledků dokument corpus pro sémantické hodnocení.

1. V rámci dokumentu corpus je obsah každého pole v "searchFields" extrahován a sloučen do dlouhého řetězce.

1. Všechny řetězce, které jsou příliš dlouhé, jsou oříznuty, aby celková délka splňovala vstupní požadavky modelu sumarizace. Toto oříznutí cvičení je důvod, proč je důležité umístit Stručná pole jako první do "searchFields", aby se zajistilo jejich zahrnutí do řetězce. Pokud máte velmi velké dokumenty s textovými poli, vše po překročení maximálního limitu se ignoruje.

Každý dokument je nyní reprezentován jedním dlouhým řetězcem.

> [!NOTE]
> Vstupy parametrů pro modely jsou tokeny bez znaků nebo slov. Tokenizace je určena v rámci přiřazení analyzátoru v polích, která lze prohledávat. Pro přehledy o tom, jak jsou řetězce v tokenech, můžete zkontrolovat výstup tokenu analyzátoru pomocí [REST API analyzátoru testů](/rest/api/searchservice/test-analyzer).
>
> V této verzi Preview dlouhé řetězce můžou mít velikost maximálně 8 000 tokenů. Pokud se při hledání nepovede doručovat očekávanou odpověď hluboko v rámci dokumentu, získáte informace o tom, proč je oříznutí obsahu užitečné. 

## <a name="summarization"></a>Souhrn

Po zmenšení řetězce je teď možné předat parametry prostřednictvím porozumění strojového porozumění a reprezentace jazyka, abyste zjistili, které věty a fráze nejlépe shrnují model v souvislosti s dotazem.

Vstupy do sumarizace jsou dlouhé řetězce z přípravné fáze. Z tohoto vstupu model Shrnutí vyhodnocuje obsah a hledá nejdůležitější pasáže.

Výstupem je [sémantický titulek](semantic-how-to-query-request.md), v prostém textu a s nejzajímavější. Popisek je menší než dlouhý řetězec, obvykle méně než 200 slov na dokument, a je považována za největšího zástupce dokumentu. 

Pokud jste zadali parametr odpovědi, bude vrácena i [sémantická odpověď](semantic-answers.md) , pokud byl dotaz považován za otázku a pokud se pasáž nachází v dlouhém řetězci, který vypadá jako plausible odpověď na otázku.

## <a name="scoring-and-ranking"></a>Bodování a hodnocení

V tuto chvíli teď máte titulky pro každý dokument. Popisky jsou vyhodnocovány pro relevanci dotazu.

1. Bodování je určeno vyhodnocením každého titulu pro koncepční a sémantickou relevanci vzhledem k poskytnutému dotazu.

   Následující diagram znázorňuje, co znamená "sémantická závažnost". Vezměte v úvahu pojem "kapitálový", který by se mohl používat v kontextu finančních, zákonných, geografických nebo gramatických. Pokud dotaz obsahuje termíny ze stejného vektorového prostoru (například "Velká" a "investice"), dokument, který zahrnuje i tokeny ve stejném clusteru, bude mít větší skóre než ta, která ne.

   :::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="Vektorová reprezentace pro kontext" border="true":::

1. Výstup této fáze je @search.rerankerScore přiřazen ke každému dokumentu. Po určení skóre všech dokumentů jsou uvedeny v sestupném pořadí a zahrnuty do datové části odpovědi na dotaz. Datová část obsahuje odpovědi, prostý text a zvýrazněné titulky a všechna pole, která jste označili jako získatelné nebo zadaná v klauzuli SELECT.

## <a name="next-steps"></a>Další kroky

Sémantické hodnocení se nabízí na úrovních Standard, v určitých oblastech. Další informace o dostupných a zaregistrování najdete v tématu [dostupnost a ceny](semantic-search-overview.md#availability-and-pricing). Nový typ dotazu povoluje hodnocení relevance a struktury odpovědí sémantického hledání. Začněte tím, [že vytvoříte sémantický dotaz](semantic-how-to-query-request.md).

Případně si přečtěte následující články o výchozím hodnocení. Sémantické hodnocení závisí na pořadí podobnosti, které vrací počáteční výsledky. Znalost provádění dotazů a hodnocení vám poskytne širokou znalost toho, jak celý proces funguje.

+ [Fulltextové vyhledávání v Azure Kognitivní hledání](search-lucene-query-architecture.md)
+ [Podobnost a bodování v Azure Kognitivní hledání](index-similarity-and-scoring.md)
+ [Analyzátory pro zpracování textu v Azure Kognitivní hledání](search-analyzers.md)