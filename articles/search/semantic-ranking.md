---
title: Sémantické hodnocení
titleSuffix: Azure Cognitive Search
description: Popisuje algoritmus sémantického hodnocení v Kognitivní hledání.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 01c4d6475ec23b8a55d91e18f49cab27760aa907
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604283"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Sémantické hodnocení v Azure Kognitivní hledání

> [!IMPORTANT]
> Funkce sémantického vyhledávání jsou ve verzi Public Preview dostupné jenom v rámci verze Preview REST API. Funkce ve verzi Preview se nabízejí tak, jak jsou, v části s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)a nemají zaručenou stejnou implementaci při obecné dostupnosti. Další informace najdete v tématu [dostupnost a ceny](semantic-search-overview.md#availability-and-pricing).

Sémantické hodnocení je rozšířením kanálu spuštění dotazu, který vylepšuje přesnost a vrácení zpět změnou pořadí horních shod počáteční sady výsledků. Sémantické hodnocení je zajištěné špičkovými modely pro čtení z uměleckého porozumění, které jsou výukou pro dotazy vyjádřené v přirozeném jazyce na rozdíl od jazykového porovnání klíčových slov. Na rozdíl od [výchozího algoritmu řazení podobnosti](index-ranking-similarity.md)používá sémantický klasifikátor kontext a význam slov k určení relevance.

## <a name="how-semantic-ranking-works"></a>Jak sémantické hodnocení funguje

Sémantické hodnocení je náročné na prostředky i čas. Aby bylo možné dokončit zpracování v rámci očekávané latence operace dotazu, model přebírá jako vstup pouze prvních 50 dokumentů vrácených z výchozího [algoritmu řazení podobnosti](index-ranking-similarity.md). Výsledky počátečního hodnocení můžou zahrnovat víc než 50 shod, ale jenom prvních 50 se přeřadí sémanticky. 

Pro sémantické hodnocení používá model porozumění strojovým účelům čtení i přenosu, aby bylo možné dokumenty znovu vyhodnotit na základě toho, jak dobře se každý z nich shoduje s záměrem dotazu.

### <a name="preparation-passage-extraction-phase"></a>Fáze přípravy (extrakce pasáže)

U každého dokumentu od počátečních výsledků existuje cvičení extrakce pasáže, které identifikuje klíčové pasáže. Toto je možnost cvičení, které snižuje obsah na množství, které lze zpracovat rychle.

1. Pro každý z dokumentů 50 se každé pole v parametru searchFields vyhodnocuje v po sobě jdoucích pořadí. Obsah z každého pole je konsolidován do jednoho dlouhého řetězce. 

1. Dlouhý řetězec se pak ořízne tak, aby celková délka nepřesahoval 8 000 tokeny. Z tohoto důvodu doporučujeme umístit Stručná pole jako první, aby byla vložena do řetězce. Pokud máte velmi velké dokumenty s poli s velkým objemem textu, cokoli po omezení tokenu se ignoruje.

1. Každý dokument je nyní reprezentován jedním dlouhým řetězcem, který je až 8 000 tokenů. Tyto řetězce jsou odesílány do modelu shrnutí, čímž se dále zkrátí řetězec. Model Shrnutí vyhodnocuje dlouhý řetězec pro klíčové věty nebo pasáže, které nejlépe shrnují dokument nebo odpovídají na otázku.

1. Výstupem této fáze je titulek (a volitelně odpověď). Popisek je nejvýše 128 tokenů na jeden dokument a je považován za nejvýraznějšího zástupce dokumentu.

### <a name="scoring-and-ranking-phases"></a>Fáze bodování a hodnocení

V této fázi jsou všechny titulky 50 vyhodnocovány k vyhodnocení relevance.

1. Bodování je určeno vyhodnocením každého titulu pro koncepční a sémantickou relevanci vzhledem k poskytnutému dotazu.

   Následující diagram znázorňuje, co znamená "sémantická závažnost". Vezměte v úvahu pojem "kapitálový", který by se mohl používat v kontextu finančních, zákonných, geografických nebo gramatických. Pokud dotaz obsahuje termíny ze stejného vektorového prostoru (například "Velká" a "investice"), dokument, který zahrnuje i tokeny ve stejném clusteru, bude mít větší skóre než ta, která ne.

   :::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="Vektorová reprezentace pro kontext" border="true":::

1. Výstup této fáze je @search.rerankerScore přiřazen ke každému dokumentu. Po určení skóre všech dokumentů jsou uvedeny v sestupném pořadí a zahrnuty do datové části odpovědi na dotaz.

## <a name="next-steps"></a>Další kroky

Sémantické hodnocení se nabízí na úrovních Standard, v určitých oblastech. Další informace a registraci najdete v tématu [dostupnost a ceny](semantic-search-overview.md#availability-and-pricing). Nový typ dotazu povoluje hodnocení relevance a struktury odpovědí sémantického hledání. Začněte tím, [že vytvoříte sémantický dotaz](semantic-how-to-query-request.md).

Případně si můžete projít některé z následujících článků a získat související informace.

+ [Přehled sémantického hledání](semantic-search-overview.md)
+ [Vrátí sémantickou odpověď.](semantic-answers.md)