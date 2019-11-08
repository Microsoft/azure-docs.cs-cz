---
title: Znalostní báze Knowledge Base – QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker znalostní báze se skládá ze sady párů otázek a odpovědí (QnA) a volitelných metadat přidružených ke každému páru QnA.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 355556e98300ecad6aa3141f0f4ab14b834cd91e
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73794900"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>Co je znalostní báze QnA Maker Knowledge Base?

QnA Maker znalostní báze se skládá ze sady párů otázek a odpovědí (QnA) a volitelných metadat přidružených ke každému páru QnA.

## <a name="key-knowledge-base-concepts"></a>Klíčové pojmy znalostní báze Knowledge Base

* **Otázky**: otázka obsahuje text, který nejlépe představuje dotaz uživatele. 
* **Odpovědi**: odpověď je odpověď, která se vrátí, když se dotaz uživatele shoduje s přidruženou otázkou.  
* **Metadata**: metadata jsou značky přidružené k páru QnA a jsou reprezentovány jako páry klíč-hodnota. Značky metadat slouží k filtrování párů QnA a omezení sady, u které se provádí párování dotazů.

Jedna QnA reprezentovaná číselným ID QnA má několik variant dotazu (alternativní otázky), které jsou všechny mapovány na jednu odpověď. Ke každému takové dvojici navíc může být přidruženo více polí metadat: jedna klávesa a jedna hodnota.

![QnA Maker základy znalostí](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>Formát obsahu znalostní báze

Když do znalostní báze ingestujte bohatě bohatý obsah, QnA Maker se pokusí převést obsah na Markdownu. Přečtěte si [Tento blog](https://aka.ms/qnamaker-docs-markdown-support) , kde najdete další informace o formátech Markdownu, které jsou srozumitelné pro většinu klientů chatu.

Pole metadat se skládají z párů klíč-hodnota oddělených dvojtečkou, jako je například produkt: Shredder. Klíč i hodnota musí být pouze text. Klíč metadat nesmí obsahovat žádné mezery. Metadata podporují pouze jednu hodnotu na klíč.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Jak QnA Maker zpracovává dotaz uživatele, aby mohl vybrat nejlepší odpověď

Vyškolená a [publikovaná](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) QnA maker znalostní báze obdrží dotaz na uživatele z robota nebo jiné klientské aplikace v [rozhraní API pro GenerateAnswer](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage). Následující diagram znázorňuje proces, když je přijat dotaz uživatele.

![Proces řazení pro dotaz uživatele](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

### <a name="ranker-process"></a>Proces hodnocení

Tento proces je vysvětlen v následující tabulce.

|Krok|Účel|
|--|--|
|1\. místo|Klientská aplikace pošle dotaz uživatele do [rozhraní GenerateAnswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage).|
|2|QnA Maker předzpracovává dotazy uživatelů pomocí rozpoznávání jazyka, pravopisu a dělení slov.|
|3|Tento předzpracování se provádí pro změnu dotazu uživatele na nejlepší výsledky hledání.|
|4|Tento změněný dotaz se odešle do indexu služby Azure Kognitivní hledání, který obdrží `top` počet výsledků. Pokud se v těchto výsledcích nejedná o správnou odpověď, zvyšte hodnotu `top` mírně. Obecně platí, že hodnota 10 pro `top` funguje v 90% dotazů.|
|5|QnA Maker použije rozšířená featurization k určení správnosti načtených výsledků hledání pro dotaz uživatele. |
|6|Vyškolený model hodnocení používá skóre funkcí z kroku 5 k zařazení výsledků Kognitivní hledání Azure.|
|7|Nové výsledky se vrátí do klientské aplikace v pořadí podle pořadí.|
|||

Použité funkce zahrnují, ale nejsou omezené na sémantiku na úrovni aplikace, důležitost na úrovni termínu v Corpus a hloubkované sémantické modely, které určují podobnost a relevanci mezi dvěma textovými řetězci.

## <a name="http-request-and-response-with-endpoint"></a>Požadavek HTTP a odpověď s koncovým bodem
Když publikujete znalostní bázi, služba vytvoří koncový bod HTTP založený na REST, který bude možné integrovat do vaší aplikace, obvykle robota chatu. 

### <a name="the-user-query-request-to-generate-an-answer"></a>Požadavek na dotaz na uživatele pro vygenerování odpovědi

Uživatelský dotaz je otázka, kterou koncový uživatel požaduje ve znalostní bázi, například `How do I add a collaborator to my app?`. Dotaz je často ve formátu přirozeného jazyka nebo některá klíčová slova, která reprezentují otázku, například `help with collaborators`. Dotaz se odešle do znalostní báze z požadavku HTTP v klientské aplikaci.

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

Odpověď řídíte nastavením vlastností, jako jsou [scoreThreshold](./confidence-score.md#choose-a-score-threshold), [Top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)a [strictFilters](../how-to/metadata-generateanswer-usage.md#filter-results-with-strictfilters-for-metadata-tags).

Použijte [kontext konverzace](../how-to/metadata-generateanswer-usage.md#use-question-and-answer-results-to-keep-conversation-context) s [funkcemi vícenásobného zapínání](../how-to/multiturn-conversation.md) , aby konverzace mohla Upřesnit otázky a odpovědi a najít správnou a konečnou odpověď.

### <a name="the-response-from-a-call-to-generate-an-answer"></a>Odpověď ze volání pro vygenerování odpovědi

Odpověď HTTP je odpověď získaná ze znalostní báze na základě nejlepší shody pro daný dotaz uživatele. Odpověď obsahuje odpověď a skóre předpovědi. Pokud jste si vyžádali více než jednu horní odpověď s vlastností `top`, získáte více než jednu otázku nejvyšší odpovědi, z nichž každá má skóre. 

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
Znalostní báze je úložiště otázek a odpovědí, které se vytvářejí, udržují a používají prostřednictvím QnA Maker. Každou vrstvu QnA Maker lze použít pro více znalostní báze.

Znalostní báze má dva stavy: *test* a *Publikováno*.

*Test Knowledge Base* je verze, která je upravována, ukládána a testována pro přesnost a úplnost reakcí. Změny provedené ve znalostní bázi test se netýkají koncového uživatele vaší aplikace nebo robota chatu. Testovací znalostní báze se v požadavku HTTP označuje jako `test`. 

*Publikovaná znalostní báze* je verze, která se používá v robotu nebo v aplikaci chatu. Akce publikování znalostní báze znamená obsah testovací znalostní báze v publikované verzi znalostní báze. Vzhledem k tomu, že publikovaná znalostní báze je verze, kterou aplikace používá prostřednictvím koncového bodu, ujistěte se, že je obsah správný a dobře testovaný. Publikovaná znalostní báze se označuje jako `prod` v požadavku HTTP.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Životní cyklus vývoje znalostní báze](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Další informace najdete v tématech

[Přehled služby QnA Maker](../Overview/overview.md)

Vytvořte a upravte znalostní bázi pomocí: 
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

Generovat odpověď s: 
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
