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
ms.openlocfilehash: a008551ac6f149617feedd01e256b637f83e975d
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2021
ms.locfileid: "103234989"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Sémantické hodnocení v Azure Kognitivní hledání

> [!IMPORTANT]
> Funkce sémantického vyhledávání jsou ve verzi Public Preview dostupné jenom v rámci verze Preview REST API. Funkce ve verzi Preview se nabízejí tak, jak jsou, v části s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)a nemají zaručenou stejnou implementaci při obecné dostupnosti. Další informace najdete v tématu [dostupnost a ceny](semantic-search-overview.md#availability-and-pricing).

Sémantické hodnocení je rozšířením kanálu spuštění dotazu, který vylepšuje přesnost a vrácení zpět změnou pořadí horních shod počáteční sady výsledků. Sémantické hodnocení je zajištěné špičkovými modely pro čtení z uměleckého porozumění, které jsou výukou pro dotazy vyjádřené v přirozeném jazyce na rozdíl od jazykového porovnání klíčových slov. Na rozdíl od [výchozího algoritmu řazení podobnosti](index-ranking-similarity.md)používá sémantický klasifikátor kontext a význam slov k určení relevance.

## <a name="how-semantic-ranking-works"></a>Jak sémantické hodnocení funguje

Sémantické hodnocení je náročné na prostředky i čas. Aby bylo možné dokončit zpracování v rámci očekávané latence operace dotazu, model přebírá jako vstup pouze prvních 50 dokumentů vrácených z výchozího [algoritmu řazení podobnosti](index-ranking-similarity.md). Výsledky počátečního hodnocení můžou zahrnovat víc než 50 shod, ale jenom prvních 50 se přeřadí sémanticky. 

Pro sémantické hodnocení používá model porozumění strojovým účelům čtení i přenosu, aby bylo možné dokumenty znovu vyhodnotit na základě toho, jak dobře se každý z nich shoduje s záměrem dotazu.

1. U každého dokumentu sémantická klasifikace vyhodnocuje pole v parametru searchFields v daném pořadí a konsoliduje obsah do jednoho velkého řetězce.

1. Řetězec se pak ořízne tak, aby celková délka nepřesahoval 20 000 tokeny. Pokud máte velmi velké dokumenty, s polem obsahu nebo merged_content poli, které má mnoho stránek obsahu, budou použity pouze první tokeny 20 000.

1. Každý z dokumentů 50 je nyní reprezentován jedním dlouhým řetězcem, který je až 20 000 tokenů. Tento řetězec je odeslán do modelu sumarizace. Model sumarizace vytváří popisy (a odpovědi) pomocí porozumění strojovým pochopením k identifikaci pasáží, které se zobrazí pro shrnutí obsahu, nebo k zodpovězení otázky. Výstupem modelu sumarizace je další redukovaný řetězec, který má maximálně 128 tokenů.

1. Menší řetězec se zobrazí v titulku dokumentu a představuje nejrelevantnější pasáže nalezené ve větším řetězci. Sada titulů 50 (nebo méně) je pak seřazená podle relevance pořadí. 

Koncepční a sémantická relevance je založena na základě vektorové reprezentace a termínů clusterů. Vzhledem k tomu, že algoritmus podobnosti klíčových slov může v rámci dotazu poskytnout stejnou váhu, sémantický model byl vyškolen pro rozpoznání vzájemné závislosti a vztahů mezi slovy, která jsou jinak nesouvisející na povrchu. Výsledkem je, že pokud řetězec dotazu obsahuje ve stejném clusteru nějaké výrazy, bude mít dokument, který obsahuje obě hodnoty, pořadí vyšší než ten, který ne.

:::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="Vektorová reprezentace pro kontext" border="true":::

## <a name="next-steps"></a>Další kroky

Sémantické hodnocení se nabízí na úrovních Standard, v určitých oblastech. Další informace a registraci najdete v tématu [dostupnost a ceny](semantic-search-overview.md#availability-and-pricing).

Nový typ dotazu povoluje hodnocení relevance a struktury odpovědí sémantického hledání. Pokud chcete začít, [vytvořte sémantický dotaz](semantic-how-to-query-request.md) .

Případně si můžete projít některé z následujících článků a získat související informace.

+ [Přidat kontrolu pravopisu pro dotaz na výrazy](speller-how-to-add.md)
+ [Vrátí sémantickou odpověď.](semantic-answers.md)