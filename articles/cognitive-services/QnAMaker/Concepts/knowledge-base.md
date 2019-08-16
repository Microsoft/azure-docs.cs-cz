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
ms.date: 08/15/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 8cd63913c0e96d496aa617369601c1dd121b4b46
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69542845"
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

## <a name="http-request-and-response-with-endpoint"></a>Požadavek HTTP a odpověď s koncovým bodem
Když publikujete znalostní bázi, služba vytvoří **koncový bod** http založený na REST, který bude možné integrovat do vaší aplikace, obvykle robota chatu. 

### <a name="the-user-query-request-to-generate-an-answer"></a>Požadavek na dotaz na uživatele pro vygenerování odpovědi

**Uživatelský dotaz** je otázka, kterou koncový uživatel požádá o znalostní bázi Knowledge Base, jako je `How do I add a collaborator to my app?`například. Dotaz je často ve formátu přirozeného jazyka nebo některá klíčová slova, která reprezentují otázku, jako `help with collaborators`je například. Dotaz se pošle na vaše znalosti z **požadavku** http v klientské aplikaci.

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

Odpověď řídíte nastavením vlastností, jako jsou [scoreThreshold](./confidence-score.md#choose-a-score-threshold), [Top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)a [stringFilters](../how-to/metadata-generateanswer-usage.md#filter-results-with-strictfilters-for-metadata-tags).

Používejte [obsah konverzací](../how-to/metadata-generateanswer-usage.md#use-question-and-answer-results-to-keep-conversation-context) s [funkcemi vícenásobného](../how-to/multiturn-conversation.md) zapínání, aby konverzace mohla Upřesnit otázky a odpovědi a najít správnou a konečnou odpověď.

### <a name="the-response-from-a-call-to-generate-answer"></a>Odpověď ze volání pro vygenerování odpovědi

**Odpověď** http je odpověď získaná ze znalostní báze na základě nejlepší shody pro daný dotaz uživatele. Odpověď obsahuje odpověď a skóre předpovědi. Pokud jste si vyžádali více než jednu otázku nejvyšší úrovně `top` , získáte s vlastností více než jednu horní otázku, z nichž každá má skóre. 

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

### <a name="test-and-production-knowledge-base"></a>Testovací a provozní znalostní báze
Znalostní báze je úložiště otázky a odpovědi vytvořen, udržovat a použít nástroj QnA Maker. Každá úroveň QnA Maker lze použít pro více znalostní báze.

Znalostní báze má dva stavy - testů a publikovat. 

**Testovací znalostní báze** je verze, kterou je upravována, ukládána a testována pro přesnost a úplnost reakcí. Změny provedené ve znalostní bázi test neovlivňují koncový uživatel aplikace/chatovací robot. Testovací znalostní báze se označuje jako `test` požadavek HTTP. 

**Publikovaná znalostní báze** je verze, která se používá ve robotu nebo aplikaci chatu. Akce publikování znalostní báze převádí obsah znalostní báze Test na publikovanou verzi znalostní báze. Protože znalostní báze publikované verzi, která aplikace používá prostřednictvím koncového bodu, by měl zajistit, že obsah je správná a dobře otestovaný věnovat pozornost. Publikovaná znalostní báze je známá jako `prod` v požadavku HTTP. 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Životní cyklus vývoje znalostní báze](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Další informace najdete v tématech

[Přehled služby QnA Maker](../Overview/overview.md)

Vytvoření a úprava znalostní báze pomocí: 
* [REST API](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [Sada .NET SDK](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

Generovat odpověď s: 
* [REST API](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [Sada .NET SDK](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
