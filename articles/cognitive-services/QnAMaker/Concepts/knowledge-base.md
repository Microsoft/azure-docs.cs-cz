---
title: Znalostní báze – QnA Maker
titleSuffix: Azure Cognitive Services
description: Znalostní báze QnA Maker se skládá ze sady páry otázek a odpovědí (QnA) a volitelná metadata spojená s každou dvojici QnA.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 022b16669791b9b9cce066b3dd17c70b33569cc0
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955239"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>Co je znalostní bázi nástroje QnA Maker?

Znalostní báze QnA Maker se skládá ze sady páry otázek a odpovědí (QnA) a volitelná metadata spojená s každou dvojici QnA.

## <a name="key-knowledge-base-concepts"></a>Koncepty klíče znalostní báze

* **Dotazy** -dotaz obsahuje text, který nejlépe představuje uživatelský dotaz. 
* **Odpovědi** – odpověď je tato odpověď, která je vrácena, pokud uživatelský dotaz je nalezena shoda s přidružené otázku.  
* **Metadata** – Metadata jsou značky přiřazeny k pár QnA a jsou reprezentovány jako páry klíč hodnota. Značky metadat slouží k filtrování párů QnA a omezení sady, u které se provádí párování dotazů.

Jeden QnA, reprezentovaný číselné ID QnA má několik variant otázky (alternativní dotazy), že všechny mapovat na jednu odpověď. Ke každému takové dvojici navíc může být přidruženo více polí metadat: jeden klíč a jedna hodnota.

![Nástroj QnA Maker znalostních bází](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>Formát obsahu znalostní báze

Při ingestování formátovaný obsah do znalostní báze, nástroj QnA Maker se pokusí převést obsah na markdown. Čtení [to](https://aka.ms/qnamaker-docs-markdown-support) blogu o markdownu formáty používaném většina klientů konverzace.

Pole metadat se skládá z dvojice klíč hodnota oddělené dvojtečkou **(produktu: Shredder)** . Klíče a hodnoty musí být prostého textu. Klíč metadat nesmí obsahovat žádné mezery. Metadata podporují pouze jednu hodnotu na klíč.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Jak QnA Maker zpracovává dotaz uživatele, aby mohl vybrat nejlepší odpověď

Vyškolená [](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) a publikovaná QnA maker znalostní báze obdrží dotaz na uživatele z robota nebo jiné klientské aplikace v [rozhraní API pro GenerateAnswer](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage). Následující diagram znázorňuje proces, když je přijat dotaz uživatele.

![Proces řazení pro dotaz uživatele](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

Postup je vysvětlen v následující tabulce:

|Krok|Účel|
|--|--|
|1|Klientská aplikace pošle dotaz uživatele do [rozhraní GenerateAnswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage).|
|2|QNA maker předzpracování dotazu uživatele pomocí rozpoznávání jazyka, pravopisu a dělení slov.|
|3|K tomuto předběžnému zpracování se provádí Změna dotazu uživatele pro dosažení nejlepších výsledků hledání.|
|4|Tento změněný dotaz se odešle do Azure Search indexu a přijímá `top` počet výsledků. Pokud v těchto výsledcích není správná odpověď, zvyšte hodnotu `top` mírně. Obecně platí, že hodnota 10 `top` pro funguje v 90% dotazů.|
|5|QnA Maker použije rozšířená featurization k určení správnosti načtených Azure Searchch výsledků pro dotaz na uživatele. |
|6|Vyškolený model hodnocení používá skóre funkcí z kroku 5 k zařazení výsledků Azure Search.|
|7|Nové výsledky se vrátí do klientské aplikace v pořadí podle pořadí.|
|||

Používané funkce zahrnují, ale nejsou omezeny na sémantiku na úrovni aplikace Word, důležitost na úrovni podmínky v Corpus a hloubkované sémantické modely, které určují podobnost a relevanci mezi dvěma textovými řetězci.


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Životní cyklus vývoje znalostní báze](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Další informace najdete v tématech

[Přehled služby QnA Maker](../Overview/overview.md)
