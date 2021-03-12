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
ms.openlocfilehash: 9d2100dbc2c5f24742a949778a1b7450bf303c5f
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2021
ms.locfileid: "103232201"
---
# <a name="get-an-answer-with-the-generateanswer-api"></a>Získání odpovědi pomocí rozhraní GenerateAnswer API

Pokud chcete získat předpokládanou odpověď na otázku uživatele, použijte rozhraní GenerateAnswer API. Když publikujete znalostní bázi, uvidíte informace o tom, jak toto rozhraní API používat na stránce **publikovat** . Můžete také nakonfigurovat rozhraní API pro filtrování odpovědí na základě značek metadat a otestovat znalostní bázi z koncového bodu s parametrem řetězce dotazu test.

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

## <a name="get-precise-answers-with-generateanswer-api"></a>Získat přesné odpovědi pomocí rozhraní GenerateAnswer API

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)

Nabízíme přesnou funkci odpovědi jenom s QnA Maker spravované verze.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/v2)

Uživatel může povolit [přesné odpovědi](../reference-precise-answering.md) při použití spravovaného prostředku QnA maker. Parametr answerSpanRequest se musí aktualizovat na stejný.

```json
{
    "question": "How long it takes to charge surface pro 4?",
    "top": 3,
    "answerSpanRequest": {
        "enable": true,
        "topAnswersWithSpan": 1
    }
}
```

Podobně se uživatelé můžou rozhodnout zakázat přesné odpovědi nastavením parametru answerSpanRequest.

```json
{
    "question": "How long it takes to charge surface pro 4?",
    "top": 3
}
```
### <a name="bot-settings"></a>Nastavení robota

Pokud chcete pro službu robota nakonfigurovat přesné nastavení odpovědí, přejděte k prostředku App Service za vás. Pak je nutné aktualizovat konfigurace přidáním následujícího nastavení.

- EnablePreciseAnswer
- DisplayPreciseAnswerOnly

|Konfigurace zobrazení|EnablePreciseAnswer|DisplayPreciseAnswerOnly|
|:--|--|--|
|Pouze přesné odpovědi|true|true|
|Pouze dlouhé odpovědi|false (nepravda)|false (nepravda)|
|Dlouhá i přesná odpověď|true|false (nepravda)|

---

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
