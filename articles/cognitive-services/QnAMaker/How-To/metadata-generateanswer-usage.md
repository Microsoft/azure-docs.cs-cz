---
title: Metadata s GenerateAnswer API – QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker umožňuje přidat metadata ve formě párů klíč/hodnota k vašim dvojicím dotazů a odpovědí. Výsledky můžete filtrovat podle uživatelských dotazů a ukládat Další informace, které se dají použít v následných konverzacích.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 18b70d60ade7cd40f7ed51aa7c219c8c046abfc3
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584736"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>Získat odpověď s GenerateAnswer API a metadaty

Pokud chcete získat předpokládanou odpověď na otázku uživatele, použijte rozhraní GenerateAnswer API. Když publikujete znalostní bázi, uvidíte informace o tom, jak toto rozhraní API používat na stránce **publikovat** . Můžete také nakonfigurovat rozhraní API pro filtrování odpovědí na základě značek metadat a otestovat znalostní bázi z koncového bodu s parametrem řetězce dotazu test.

QnA Maker umožňuje přidávat metadata ve formě párů klíč-hodnota na vaše páry otázek a odpovědí. Tyto informace pak můžete použít k filtrování výsledků do uživatelských dotazů a k ukládání dalších informací, které je možné použít v následných konverzacích. Další informace najdete v tématu [znalostní báze](../index.yml).

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Uložení otázek a odpovědí s entitou QnA

Je důležité porozumět tomu, jak QnA Maker ukládá data otázky a odpovědi. Následující ilustrace znázorňuje QnA entitu:

![Ilustrace entity QnA](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Každá entita QnA má jedinečné a trvalé ID. Pomocí ID můžete provádět aktualizace konkrétní entity QnA.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Získání odpovědi předpovědi pomocí rozhraní GenerateAnswer API

[Rozhraní GenerateAnswer API](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) ve své robotě nebo aplikaci můžete použít k dotazování znalostní báze s uživatelskou otázkou, abyste získali nejlepší shodu s páry otázka a odpověď.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Publikování pro získání koncového bodu GenerateAnswer

Po publikování znalostní báze z [QnA Makerového portálu](https://www.qnamaker.ai)nebo pomocí [rozhraní API](/rest/api/cognitiveservices/qnamaker/knowledgebase/publish)můžete získat podrobnosti o koncovém bodu GenerateAnswer.

Jak získat podrobnosti o koncovém bodu:
1. Přihlaste se k webu [https://www.qnamaker.ai](https://www.qnamaker.ai).
1. V **seznamu Moje znalostní** báze vyberte možnost **Zobrazit kód** pro znalostní bázi.
    ![Snímek obrazovky se základy naší znalostní báze](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. Získejte podrobnosti o GenerateAnswer koncového bodu.

    # <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)

    ![Snímek obrazovky s podrobnostmi koncového bodu](../media/qnamaker-how-to-metadata-usage/view-code.png)

    # <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/v2)

    ![Snímek obrazovky se spravovanými podrobnostmi koncového bodu](../media/qnamaker-how-to-metadata-usage/view-code-managed.png)

    ---

Podrobnosti o koncových bodech můžete také získat z karty **Nastavení** ve znalostní bázi.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>Konfigurace žádosti GenerateAnswer

GenerateAnswer zavoláte s požadavkem HTTP POST. Vzorový kód, který ukazuje, jak volat GenerateAnswer, naleznete v tématu [rychlé starty](../quickstarts/quickstart-sdk.md#generate-an-answer-from-the-knowledge-base).

Požadavek POST používá:

* Požadované [parametry identifikátoru URI](/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* Požadovaná vlastnost hlavičky, `Authorization` pro zabezpečení
* Požadované [vlastnosti těla](/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto).

Adresa URL GenerateAnswer má následující formát:

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

Nezapomeňte nastavit vlastnost hlaviček protokolu HTTP `Authorization` s hodnotou řetězce `EndpointKey` s koncovým místem, na které se nachází klíč koncového bodu na stránce **Nastavení** .

Ukázkový text JSON vypadá takto:

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 30,
    "rankerType": "" // values: QuestionOnly
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

Přečtěte si další informace o [rankerType](../concepts/best-practices.md#choosing-ranker-type).

Předchozí kód JSON požádal pouze o odpovědi, které jsou na 30% nebo vyšší než prahové skóre.

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>Vlastnosti odpovědi GenerateAnswer

[Odpověď](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query) je objekt JSON, včetně všech informací, které potřebujete k zobrazení odpovědi, a další Zapnutí konverzace, pokud je k dispozici.

```json
{
    "answers": [
        {
            "score": 38.54820341616869,
            "Id": 20,
            "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
            "source": "Custom Editorial",
            "questions": [
                "How can I integrate LUIS with QnA Maker?"
            ],
            "metadata": [
                {
                    "name": "category",
                    "value": "api"
                }
            ]
        }
    ]
}
```

Předchozí znak JSON odpověděl s odpovědí s skóre 38,5%.

## <a name="use-qna-maker-with-a-bot-in-c"></a>Použití QnA Maker s robotem v jazyce C #

Rozhraní bot Framework poskytuje přístup k vlastnostem QnA Maker pomocí [rozhraní Getanswer API](/dotnet/api/microsoft.bot.builder.ai.qna.qnamaker.getanswersasync#Microsoft_Bot_Builder_AI_QnA_QnAMaker_GetAnswersAsync_Microsoft_Bot_Builder_ITurnContext_Microsoft_Bot_Builder_AI_QnA_QnAMakerOptions_System_Collections_Generic_Dictionary_System_String_System_String__System_Collections_Generic_Dictionary_System_String_System_Double__):

```csharp
using Microsoft.Bot.Builder.AI.QnA;
var metadata = new Microsoft.Bot.Builder.AI.QnA.Metadata();
var qnaOptions = new QnAMakerOptions();

metadata.Name = Constants.MetadataName.Intent;
metadata.Value = topIntent;
qnaOptions.StrictFilters = new Microsoft.Bot.Builder.AI.QnA.Metadata[] { metadata };
qnaOptions.Top = Constants.DefaultTop;
qnaOptions.ScoreThreshold = 0.3F;
var response = await _services.QnAServices[QnAMakerKey].GetAnswersAsync(turnContext, qnaOptions);
```

Předchozí kód JSON požádal pouze o odpovědi, které jsou na 30% nebo vyšší než prahové skóre.

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>Použití QnA Maker s robotem v Node.js

Rozhraní bot Framework poskytuje přístup k vlastnostem QnA Maker pomocí [rozhraní Getanswer API](/javascript/api/botbuilder-ai/qnamaker?preserve-view=true&view=botbuilder-ts-latest#generateanswer-string---undefined--number--number-):

```javascript
const { QnAMaker } = require('botbuilder-ai');
this.qnaMaker = new QnAMaker(endpoint);

// Default QnAMakerOptions
var qnaMakerOptions = {
    ScoreThreshold: 0.30,
    Top: 3
};
var qnaResults = await this.qnaMaker.getAnswers(stepContext.context, qnaMakerOptions);
```

Předchozí kód JSON požádal pouze o odpovědi, které jsou na 30% nebo vyšší než prahové skóre.

<a name="metadata-example"></a>

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Filtrování odpovědí podle vlastních značek metadat pomocí metadat

Přidání metadat umožňuje filtrovat odpovědi pomocí těchto značek metadat. Přidejte sloupec metadata z nabídky **Možnosti zobrazení** . Přidejte metadata do znalostní báze tak, že vyberete **+** ikonu metadat a přidáte dvojici metadat. Tento pár se skládá z jednoho klíče a jedné hodnoty.

![Snímek obrazovky s přidáním metadat](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtrovat výsledky pomocí strictFilters pro značky metadat

Vezměte v úvahu otázku uživatele "kdy se tento hotel blíží?", kde je záměr předpokládaný pro restauraci "Paradise".

Vzhledem k tomu, že se výsledky vyžadují jenom pro restaurace "Paradise", můžete nastavit filtr ve volání GenerateAnswer v metadatech "název restaurace". Následující příklad ukazuje:

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [ { "name": "restaurant", "value": "paradise"}]
}
```

### <a name="logical-and-by-default"></a>Logický operátor AND ve výchozím nastavení

Chcete-li v dotazu zkombinovat několik filtrů metadat, přidejte do pole vlastnosti další filtry metadat `strictFilters` . Ve výchozím nastavení jsou hodnoty logicky kombinovány (a). Logická kombinace vyžaduje, aby všechny filtry odpovídaly dvojicím QnA, aby se dvojice vracela v odpovědi.

To je ekvivalentní použití `strictFiltersCompoundOperationType` vlastnosti s hodnotou `AND` .

### <a name="logical-or-using-strictfilterscompoundoperationtype-property"></a>Logická nebo použití vlastnosti strictFiltersCompoundOperationType

Pokud se zkombinujete s několika filtry metadat a chcete použít jenom jeden nebo několik filtrů, které odpovídají, použijte `strictFiltersCompoundOperationType` vlastnost s hodnotou `OR` .

Vaše znalostní báze umožňuje vracet odpovědi, pokud libovolný filtr odpovídá, ale nevrátí odpovědi, které nemají metadata.

```json
{
    "question": "When do facilities in this hotel close?",
    "top": 1,
    "strictFilters": [
      { "name": "type","value": "restaurant"},
      { "name": "type", "value": "bar"},
      { "name": "type", "value": "poolbar"}
    ],
    "strictFiltersCompoundOperationType": "OR"
}
```

### <a name="metadata-examples-in-quickstarts"></a>Příklady metadat v rychlých startech

Další informace o metadatech v rychlém startu portálu QnA Maker pro metadata:
* [Vytváření – přidání metadat do páru QnA](../quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)
* [Prediktivní dotazování – filtrování odpovědí podle metadat](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md)

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Použití výsledků otázek a odpovědí k zachování kontextu konverzace

Odpověď na GenerateAnswer obsahuje odpovídající informace o metadatech spárované dvojice otázek a odpovědí. Tyto informace můžete v klientské aplikaci použít k uložení kontextu předchozí konverzace pro použití v pozdějších konverzacích.

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

## <a name="match-questions-only-by-text"></a>Odpovídá jenom na otázky, podle textu

Ve výchozím nastavení QnA Maker vyhledává dotazy a odpovědi. Pokud chcete vygenerovat odpověď pouze v rámci otázek, použijte `RankerType=QuestionOnly` v těle žádosti GenerateAnswer v části post.

Můžete prohledat publikované znalostní báze KB, pomocí `isTest=false` nebo v testu kB pomocí `isTest=true` .

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}
```

## <a name="common-http-errors"></a>Běžné chyby protokolu HTTP

|Kód|Vysvětlení|
|:--|--|
|2xx|Success|
|400|Parametry požadavku jsou nesprávné, což znamená, že požadované parametry chybí, jsou poškozené nebo jsou moc velké.|
|400|Tělo žádosti není správné, což znamená, že JSON chybí, má špatný nebo je moc velký.|
|401|Neplatný klíč|
|403|Zakázáno – nemáte správná oprávnění.|
|404|KB neexistuje|
|410|Toto rozhraní API je zastaralé a už není dostupné.|

## <a name="next-steps"></a>Další kroky

Stránka **publikování** také poskytuje informace pro [vygenerování odpovědi](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md) pomocí metody post nebo kudrlinkou.

> [!div class="nextstepaction"]
> [Získání analýz pro znalostní bázi](../how-to/get-analytics-knowledge-base.md)
> [!div class="nextstepaction"]
> [Hodnocení spolehlivosti](../Concepts/confidence-score.md)
