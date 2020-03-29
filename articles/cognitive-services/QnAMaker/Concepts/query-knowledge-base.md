---
title: Dotaz na znalostní bázi – QnA Maker
description: Znalostní báze musí být zveřejněna. Po publikování znalostní báze je dotazován na koncovém bodu prognózy za běhu pomocí generateAnswer API.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: cb777aa16fada50811cce1bbf49f28662c62b49b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220718"
---
# <a name="query-the-knowledge-base-for-answers"></a>Dotaz na znalostní bázi pro odpovědi

Znalostní báze musí být zveřejněna. Po publikování znalostní báze je dotazován na koncovém bodu prognózy za běhu pomocí generateAnswer API. Dotaz obsahuje text otázky a další nastavení, která pomáhají QnA Maker vybrat nejlepší možnou shodu s odpovědí.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Jak QnA Maker zpracovává uživatelský dotaz pro výběr nejlepší odpovědi

Trénovaná a [publikovaná](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) znalostní báze QnA Maker obdrží uživatelský dotaz od robota nebo jiné klientské aplikace v [rozhraní API GenerateAnswer](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage). Následující diagram znázorňuje proces při přijetí dotazu uživatele.

![Proces modelu hodnocení pro dotaz uživatele](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

### <a name="ranker-process"></a>Rankerproces

Proces je vysvětlen v následující tabulce.

|Krok|Účel|
|--|--|
|1|Klientská aplikace odešle uživatelský dotaz do [rozhraní GENERATEAnswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage).|
|2|QnA Maker předem zpracuje uživatelský dotaz pomocí detekce jazyka, pravopisů a modulů dělení na slova.|
|3|Toto předběžné zpracování je převzít změnit dotaz uživatele pro nejlepší výsledky hledání.|
|4|Tento změněný dotaz se odesílá do indexu azure `top` kognitivní vyhledávání, který obdrží počet výsledků. Pokud správná odpověď není v těchto výsledcích, mírně zvyšte hodnotu. `top` Obecně hodnota 10 pro `top` díla v 90 % dotazů.|
|5|QnA Maker používá syntaktické a sémantické založené featurization k určení podobnosti mezi uživatelský dotaz a načtené výsledky QnA.|
|6|Stroj-naučil ranker model používá různé funkce, od kroku 5, k určení skóre spolehlivosti a nové pořadí pořadí.|
|7|Nové výsledky jsou vráceny do klientské aplikace v seřazené pořadí.|
|||

Mezi použité funkce patří mimo jiné sémantiku na úrovni slov, důležitost na úrovni termínu v korpusu a podrobné naučené sémantické modely k určení podobnosti a relevance mezi dvěma textovými řetězci.

## <a name="http-request-and-response-with-endpoint"></a>Požadavek HTTP a odpověď s koncovým bodem
Při publikování znalostní báze, služba vytvoří koncový bod HTTP založené na rest, který lze integrovat do aplikace, obvykle chatovací robot.

### <a name="the-user-query-request-to-generate-an-answer"></a>Požadavek na dotaz uživatele za účelem generování odpovědi

Dotaz uživatele je otázka, kterou se koncový uživatel zeptá `How do I add a collaborator to my app?`na znalostní bázi, například . Dotaz je často ve formátu přirozeného jazyka nebo několik klíčových `help with collaborators`slov, které představují otázku, například . Dotaz je odeslán do znalostní báze z požadavku HTTP v klientské aplikaci.

```json
{
    "question": "How do I add a collaborator to my app?",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "QuestionType",
        "value": "Support"
    }],
    "userId": "sd53lsY="
}
```

Odpověď můžete řídit nastavením vlastností, jako je [například scoreThreshold](./confidence-score.md#choose-a-score-threshold), [top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)a [strictFilters](../how-to/metadata-generateanswer-usage.md#filter-results-with-strictfilters-for-metadata-tags).

Pomocí [kontextu konverzace](../how-to/metadata-generateanswer-usage.md#use-question-and-answer-results-to-keep-conversation-context) s [vícesměrovými funkcemi](../how-to/multiturn-conversation.md) udržujte konverzaci k upřesnění otázek a odpovědí a vyjděte správnou a konečnou odpověď.

### <a name="the-response-from-a-call-to-generate-an-answer"></a>Odpověď z volání generovat odpověď

Odpověď HTTP je odpověď načtená ze znalostní báze založená na nejlepší shodě pro daný uživatelský dotaz. Odpověď obsahuje odpověď a skóre předpovědi. Pokud jste požádali o více `top` než jednu horní odpověď s vlastností, dostanete více než jednu horní odpověď, z nichž každá má skóre.

```json
{
    "answers": [
        {
            "questions": [
                "How do I add a collaborator to my app?",
                "What access control is provided for the app?",
                "How do I find user management and security?"
            ],
            "answer": "Use the Azure portal to add a collaborator using Access Control (IAM)",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "QuestionType",
                    "value": "Support"
                },
                {
                    "name": "ToolDependency",
                    "value": "Azure Portal"
                }
            ]
        }
    ]
}
```


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Skóre spolehlivosti](./confidence-score.md)
