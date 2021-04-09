---
title: Dotaz na znalostní bázi Knowledge Base – QnA Maker
description: Je nutné publikovat znalostní bázi. Po publikování se znalostní báze dotazuje na koncový bod předpovědi prostředí runtime pomocí rozhraní generateAnswer API.
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: c723d1446c90290929bc8cad066b4744e284f3f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103008667"
---
# <a name="query-the-knowledge-base-for-answers"></a>Dotazování znalostní báze o odpovědích

Je nutné publikovat znalostní bázi. Po publikování se znalostní báze dotazuje na koncový bod předpovědi prostředí runtime pomocí rozhraní generateAnswer API. Dotaz obsahuje text otázky a další nastavení, která vám pomůžou QnA Maker vybrat nejlepší možnou shodu s odpovědí.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Jak QnA Maker zpracovává dotaz uživatele, aby mohl vybrat nejlepší odpověď

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)

Vyškolená a [publikovaná](../quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) QnA maker znalostní báze obdrží dotaz na uživatele z robota nebo jiné klientské aplikace v [rozhraní API pro GenerateAnswer](../how-to/metadata-generateanswer-usage.md). Následující diagram znázorňuje proces, když je přijat dotaz uživatele.

![Proces modelu hodnocení pro dotaz na uživatele](../media/qnamaker-concepts-knowledgebase/ranker-v1.png)

### <a name="ranker-process"></a>Proces hodnocení

Tento proces je vysvětlen v následující tabulce.

|Krok|Účel|
|--|--|
|1|Klientská aplikace pošle dotaz uživatele do [rozhraní GenerateAnswer API](../how-to/metadata-generateanswer-usage.md).|
|2|QnA Maker předzpracovává dotazy uživatelů pomocí rozpoznávání jazyka, pravopisu a dělení slov.|
|3|Tento předzpracování se provádí pro změnu dotazu uživatele na nejlepší výsledky hledání.|
|4|Tento změněný dotaz se odešle do indexu služby Azure Kognitivní hledání, který přijímá `top` počet výsledků. Pokud v těchto výsledcích není správná odpověď, zvyšte hodnotu `top` mírně. Obecně platí, že hodnota 10 pro `top` funguje v 90% dotazů. Filtry Azure Search [neukončí slova](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/STOPWORDS.md) v tomto kroku.|
|5|QnA Maker používá syntaktickou a sémanticky založenou featurization k určení podobnosti mezi dotazem uživatele a načtenými QnA výsledky.|
|6|Model klasifikátoru poučený počítačem používá k určení výsledků spolehlivosti a nového pořadí řazení různé funkce z kroku 5.|
|7|Nové výsledky se vrátí do klientské aplikace v pořadí podle pořadí.|
|||

Použité funkce zahrnují, ale nejsou omezené na sémantiku na úrovni aplikace, důležitost na úrovni termínu v Corpus a hloubkované sémantické modely, které určují podobnost a relevanci mezi dvěma textovými řetězci.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/v2)

Vyškolená a [publikovaná](../quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) QnA maker znalostní báze obdrží dotaz na uživatele z robota nebo jiné klientské aplikace v [rozhraní API pro GenerateAnswer](../how-to/metadata-generateanswer-usage.md). Následující diagram znázorňuje proces, když je přijat dotaz uživatele.

![Proces modelu hodnocení pro dotaz uživatele ve verzi Preview](../media/qnamaker-concepts-knowledgebase/ranker-v2.png)

### <a name="ranker-process"></a>Proces hodnocení

Tento proces je vysvětlen v následující tabulce.

|Krok|Účel|
|--|--|
|1|Klientská aplikace pošle dotaz uživatele do [rozhraní GenerateAnswer API](../how-to/metadata-generateanswer-usage.md).|
|2|QnA Maker předzpracovává dotazy uživatelů pomocí rozpoznávání jazyka, pravopisu a dělení slov.|
|3|Tento předzpracování se provádí pro změnu dotazu uživatele na nejlepší výsledky hledání.|
|4|Tento změněný dotaz se odešle do indexu služby Azure Kognitivní hledání, který přijímá `top` počet výsledků. Pokud v těchto výsledcích není správná odpověď, zvyšte hodnotu `top` mírně. Obecně platí, že hodnota 10 pro `top` funguje v 90% dotazů. Filtry Azure Search [neukončí slova](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/STOPWORDS.md) v tomto kroku.|
|5|QnA Maker využívá model založený na špičkovém transformátoru k určení podobnosti mezi dotazem uživatele a výsledky kandidátů QnA získané z Azure Kognitivní hledání. Model založený na transformátoru je obsáhlý model pro vícejazyčnou výuku, který pracuje vodorovně pro všechny jazyky, aby bylo možné určit hodnocení spolehlivosti a nové pořadí řazení.|
|6|Nové výsledky se vrátí do klientské aplikace v pořadí podle pořadí.|
|||

Klasifikátor funguje na všech alternativních otázkách a odpovědích a hledá nejlépe odpovídající páry QnA pro dotaz na uživatele. Uživatelé mají flexibilitu v tom, že je možné nastavit, že se má seřadit pouze hodnocení. 

---

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
Odpověď řídíte nastavením vlastností, jako jsou [scoreThreshold](./confidence-score.md#choose-a-score-threshold), [Top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)a [strictFilters](../how-to/query-knowledge-base-with-metadata.md).

Použijte [kontext konverzace](../how-to/query-knowledge-base-with-metadata.md) s [funkcemi vícenásobného zapínání](../how-to/multiturn-conversation.md) , aby konverzace mohla Upřesnit otázky a odpovědi a najít správnou a konečnou odpověď.

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
> [Hodnocení spolehlivosti](./confidence-score.md)
