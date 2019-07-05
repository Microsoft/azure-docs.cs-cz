---
title: Znalostní báze – QnA Maker
titleSuffix: Azure Cognitive Services
description: Znalostní báze QnA Maker se skládá ze sady páry otázek a odpovědí (QnA) a volitelná metadata spojená s každou dvojici QnA.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/25/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 7e40af9b2362ee52a1d00f29cdc112d3c2b9a842
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565851"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>Co je znalostní bázi nástroje QnA Maker?

Znalostní báze QnA Maker se skládá ze sady páry otázek a odpovědí (QnA) a volitelná metadata spojená s každou dvojici QnA.

## <a name="key-knowledge-base-concepts"></a>Koncepty klíče znalostní báze

* **Dotazy** -dotaz obsahuje text, který nejlépe představuje uživatelský dotaz. 
* **Odpovědi** – odpověď je tato odpověď, která je vrácena, pokud uživatelský dotaz je nalezena shoda s přidružené otázku.  
* **Metadata** – Metadata jsou značky přiřazeny k pár QnA a jsou reprezentovány jako páry klíč hodnota. Metadata značky se používají k filtrování QnA páry a omezit tak skupinu, přes který dotaz se provádí porovnávání.

Jeden QnA, reprezentovaný číselné ID QnA má několik variant otázky (alternativní dotazy), že všechny mapovat na jednu odpověď. Kromě toho každý takový pár může mít více polí metadat s ním spojená: jeden klíč a jednu hodnotu.

![Nástroj QnA Maker znalostních bází](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>Formát obsahu znalostní báze

Při ingestování formátovaný obsah do znalostní báze, nástroj QnA Maker se pokusí převést obsah na markdown. Čtení [to](https://aka.ms/qnamaker-docs-markdown-support) blogu o markdownu formáty používaném většina klientů konverzace.

Pole metadat se skládá z dvojice klíč hodnota oddělené dvojtečkou **(produktu: Shredder)** . Klíče a hodnoty musí být prostého textu. Klíč metadat nesmí obsahovat žádné mezery. Metadata podporuje pouze jednu hodnotu pro klíč.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Jak QnA Maker zpracovává uživatelské dotaz pro výběr nejlepší odpověď

Trénovaného a [publikované](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) QnA Maker znalostní báze obdrží dotaz uživatele, robota nebo jiné klientské aplikace na [GenerateAnswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage). Následující obrázek znázorňuje proces, při přijetí uživatelský dotaz.

![Proces hodnocení pro uživatelský dotaz](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

Proces je vysvětleno v následující tabulce:

|Krok|Účel|
|--|--|
|1|Klientská aplikace odešle dotaz na uživatele [GenerateAnswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage).|
|2|Nástroj QnA Maker předzpracování uživatelský dotaz se rozpoznávání jazyka, spellers a moduly pro dělení na slova.|
|3|Tato předzpracování se používá ke změně uživatelský dotaz pro nejlepší výsledky hledání.|
|4|Tento upravený dotaz odeslán do indexu Azure Search, přijetí `top` počet výsledků. Pokud správnou odpověď není v těchto výsledky, zvyšte hodnotu `top` mírně. Obecně hodnotu 10 pro `top` funguje v 90 % z dotazů.|
|5|Nástroj QnA Maker se týká pokročilé snadné k určení správnost počet získaných výsledků vyhledávání Azure pro uživatelský dotaz. |
|6|Klasifikátor trénovaného modelu používá funkci skóre, z kroku 5, chcete-li seřadit výsledky Azure Search.|
|7|Nové výsledky se vrátí do klientské aplikace v seřazený pořadí.|
|||

Funkce používá zahrnují ale nejsou omezeny na úrovni slovo sémantiku, Termín úrovně důležitosti souhrnu a hloubkové zjištěná sémantickým modelům určíte podobnosti a relevance mezi dva textové řetězce.


## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Životní cyklus vývoje znalostní báze](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Další informace najdete v tématech

[Přehled služby QnA Maker](../Overview/overview.md)
