---
title: Dotaz na znalostní bázi Knowledge Base – QnA Maker
description: Je nutné publikovat znalostní bázi. Po publikování se znalostní báze dotazuje na koncový bod předpovědi prostředí runtime pomocí rozhraní generateAnswer API.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: e903714aab35de40c1179045505e1520c65b3ebc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776914"
---
# <a name="query-the-knowledge-base-for-answers"></a>Dotazování znalostní báze o odpovědích

Je nutné publikovat znalostní bázi. Po publikování se znalostní báze dotazuje na koncový bod předpovědi prostředí runtime pomocí rozhraní generateAnswer API. Dotaz obsahuje text otázky a další nastavení, která vám pomůžou QnA Maker vybrat nejlepší možnou shodu s odpovědí.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Jak QnA Maker zpracovává dotaz uživatele, aby mohl vybrat nejlepší odpověď

Vyškolená a [publikovaná](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) QnA maker znalostní báze obdrží dotaz na uživatele z robota nebo jiné klientské aplikace v [rozhraní API pro GenerateAnswer](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage). Následující diagram znázorňuje proces, když je přijat dotaz uživatele.

![Proces modelu hodnocení pro dotaz na uživatele](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

### <a name="ranker-process"></a>Proces hodnocení

Tento proces je vysvětlen v následující tabulce.

|Krok|Účel|
|--|--|
|1|Klientská aplikace pošle dotaz uživatele do [rozhraní GenerateAnswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage).|
|2|QnA Maker předzpracovává dotazy uživatelů pomocí rozpoznávání jazyka, pravopisu a dělení slov.|
|3|Tento předzpracování se provádí pro změnu dotazu uživatele na nejlepší výsledky hledání.|
|4|Tento změněný dotaz se odešle do indexu služby Azure Kognitivní hledání, který přijímá `top` počet výsledků. Pokud v těchto výsledcích není správná odpověď, zvyšte hodnotu `top` mírně. Obecně platí, že hodnota 10 pro `top` funguje v 90% dotazů.|
|5|QnA Maker používá syntaktickou a sémanticky založenou featurization k určení podobnosti mezi dotazem uživatele a načtenými QnA výsledky.|
|6|Model klasifikátoru poučený počítačem používá k určení výsledků spolehlivosti a nového pořadí řazení různé funkce z kroku 5.|
|7|Nové výsledky se vrátí do klientské aplikace v pořadí podle pořadí.|
|||

Použité funkce zahrnují, ale nejsou omezené na sémantiku na úrovni aplikace, důležitost na úrovni termínu v Corpus a hloubkované sémantické modely, které určují podobnost a relevanci mezi dvěma textovými řetězci.

## <a name="http-request-and-response-with-endpoint"></a>Požadavek HTTP a odpověď s koncovým bodem
Když publikujete znalostní bázi, služba vytvoří koncový bod HTTP založený na REST, který bude možné integrovat do vaší aplikace, obvykle robota chatu.

### <a name="the-user-query-request-to-generate-an-answer"></a>Požadavek na dotaz na uživatele pro vygenerování odpovědi

Uživatelský dotaz je otázka, kterou koncový uživatel požaduje ve znalostní bázi, například `How do I add a collaborator to my app?` . Dotaz je často ve formátu přirozeného jazyka nebo některá klíčová slova, která reprezentují otázku, jako je například `help with collaborators` . Dotaz se odešle do znalostní báze z požadavku HTTP v klientské aplikaci.

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

Odpověď řídíte nastavením vlastností, jako jsou [scoreThreshold](./confidence-score.md#choose-a-score-threshold), [Top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)a [strictFilters](../how-to/metadata-generateanswer-usage.md#filter-results-with-strictfilters-for-metadata-tags).

Použijte [kontext konverzace](../how-to/metadata-generateanswer-usage.md#use-question-and-answer-results-to-keep-conversation-context) s [funkcemi vícenásobného zapínání](../how-to/multiturn-conversation.md) , aby konverzace mohla Upřesnit otázky a odpovědi a najít správnou a konečnou odpověď.

### <a name="the-response-from-a-call-to-generate-an-answer"></a>Odpověď ze volání pro vygenerování odpovědi

Odpověď HTTP je odpověď získaná ze znalostní báze na základě nejlepší shody pro daný dotaz uživatele. Odpověď obsahuje odpověď a skóre předpovědi. Pokud jste si vyžádali více než jednu horní otázku s `top` vlastností, získáte více než jednu otázku nejvyšší odpovědi, z nichž každá má skóre.

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
