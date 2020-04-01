---
title: Metadata s rozhraním API generateanswer – QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker umožňuje přidat metadata ve formě párů klíč/hodnota do sad otázek a odpovědí. Výsledky můžete filtrovat do uživatelských dotazů a uložit další informace, které lze použít v následných konverzacích.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: diberry
ms.openlocfilehash: 9beb6dbbba1c5855b8bfa97fc02f50aa59225d78
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474849"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>Získání odpovědi pomocí rozhraní API generateanswer a metadat

Chcete-li získat předpovídanou odpověď na otázku uživatele, použijte rozhraní API generateanswer. Při publikování znalostní báze, můžete zobrazit informace o tom, jak používat toto rozhraní API na stránce **Publikovat.** Rozhraní API můžete také nakonfigurovat tak, aby filtrovalo odpovědi na základě značek metadat, a otestovat znalostní bázi z koncového bodu pomocí parametru řetězce testovacího dotazu.

QnA Maker umožňuje přidávat metadata ve formě párů klíčů a hodnot do sad otázek a odpovědí. Tyto informace pak můžete použít k filtrování výsledků do uživatelských dotazů a k uložení dalších informací, které lze použít v následných konverzacích. Další informace naleznete v [tématu Knowledge Base](../Concepts/knowledge-base.md).

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Ukládání otázek a odpovědí pomocí entity QnA

Je důležité pochopit, jak QnA Maker ukládá data otázek a odpovědí. Následující obrázek znázorňuje entitu QnA:

![Ilustrace entity QnA](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Každá entita QnA má jedinečné a trvalé ID. ID můžete použít k aktualizaci konkrétní entity QnA.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Získat předpovědi odpovědí pomocí rozhraní API generateanswer

Pomocí [rozhraní API GenerateAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) ve vašem robotu nebo aplikaci můžete zadat dotaz na znalostní bázi s uživatelskou otázkou, abyste získali nejlepší shodu ze sad otázek a odpovědí.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Publikovat za účelem získání koncového bodu GenerateAnswer

Po publikování znalostní báze, buď z [portálu QnA Maker](https://www.qnamaker.ai), nebo pomocí [rozhraní API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish), můžete získat podrobnosti o koncovém bodu GenerateAnswer.

Získání podrobností o koncovém bodu:
1. Přihlaste [https://www.qnamaker.ai](https://www.qnamaker.ai)se do .
1. V **části Moje znalostní báze**vyberte možnost **Zobrazit kód** pro znalostní bázi.
    ![Snímek obrazovky s mými znalostními bázemi](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. Získejte podrobnosti o koncovém bodu GenerateAnswer.

    ![Snímek obrazovky s podrobnostmi o koncovém bodu](../media/qnamaker-how-to-metadata-usage/view-code.png)

Podrobnosti o koncovém bodu můžete také získat na kartě **Nastavení** znalostní báze.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>Generovat konfiguraci požadavku na odpověď

Volání GenerateAnswer s požadavkem HTTP POST. Ukázkový kód, který ukazuje, jak volat GenerateAnswer, naleznete v [tématu rychlé starty](../quickstarts/quickstart-sdk.md#generate-an-answer-from-the-knowledge-base).

Požadavek POST používá:

* Požadované [parametry identifikátoru URI](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* Požadovaná vlastnost `Authorization`hlavičky, , pro zabezpečení
* Požadované [vlastnosti těla](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto).

Adresa URL generateanswer má následující formát:

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

Nezapomeňte nastavit vlastnost hlavičky `Authorization` HTTP s hodnotou řetězce `EndpointKey` s koncovým prostorem, pak klíč koncového bodu nalezený na stránce **Nastavení.**

Příklad JSON tělo vypadá takto:

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

Další informace o [rankerType](../concepts/best-practices.md#choosing-ranker-type).

Předchozí JSON požadoval pouze odpovědi, které jsou na 30 % nebo nad prahovým skóre.

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>Vlastnosti odpovědi GenerateAnswer

[Odpověď](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query) je objekt JSON včetně všech informací, které potřebujete k zobrazení odpovědi a další otočení v konverzaci, pokud je k dispozici.

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

Předchozí JSON odpověděl s odpovědí se skóre 38,5%.

## <a name="use-qna-maker-with-a-bot-in-c"></a>Použití QnA Maker u robota v C #

Rozhraní bot poskytuje přístup k vlastnostem QnA Maker s [rozhraním getAnswer API](https://docs.microsoft.com/dotnet/api/microsoft.bot.builder.ai.qna.qnamaker.getanswersasync?view=botbuilder-dotnet-stable#Microsoft_Bot_Builder_AI_QnA_QnAMaker_GetAnswersAsync_Microsoft_Bot_Builder_ITurnContext_Microsoft_Bot_Builder_AI_QnA_QnAMakerOptions_System_Collections_Generic_Dictionary_System_String_System_String__System_Collections_Generic_Dictionary_System_String_System_Double__):

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

Předchozí JSON požadoval pouze odpovědi, které jsou na 30 % nebo nad prahovým skóre.

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>Použití QnA Makeru s robotem v souboru Node.js

Rozhraní bot poskytuje přístup k vlastnostem QnA Maker s [rozhraním getAnswer API](https://docs.microsoft.com/javascript/api/botbuilder-ai/qnamaker?view=botbuilder-ts-latest#generateanswer-string---undefined--number--number-):

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

Předchozí JSON požadoval pouze odpovědi, které jsou na 30 % nebo nad prahovým skóre.

<a name="metadata-example"></a>

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Použití metadat k filtrování odpovědí podle vlastních značek metadat

Přidání metadat umožňuje filtrovat odpovědi podle těchto značek metadat. Přidejte sloupec metadat z nabídky **Možnosti zobrazení.** Přidejte metadata do znalostní báze výběrem ikony metadat **+** a přidejte dvojici metadat. Tento pár se skládá z jednoho klíče a jedné hodnoty.

![Snímek obrazovky s přidáním metadat](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtrování výsledků pomocí strictFilters pro značky metadat

Zvažte uživatelskou otázku "Kdy se tento hotel zavře?", kde je záměr implikován pro restauraci "Paradise".

Vzhledem k tomu, že výsledky jsou vyžadovány pouze pro restauraci "Ráj", můžete nastavit filtr v generateAnswer volání na metadata "Název restaurace". Následující příklad ukazuje toto:

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [
      {
        "name": "restaurant",
        "value": "paradise"
      }]
}
```

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Použití výsledků otázek a odpovědí k zachování kontextu konverzace

Odpověď na GenerateAnswer obsahuje odpovídající metadata informace o odpovídající otázku a odpověď set. Tyto informace v klientské aplikaci můžete použít k uložení kontextu předchozí konverzace pro použití v pozdějších konverzacích.

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

## <a name="match-questions-only-by-text"></a>Shoda pouze s otázkami podle textu

Ve výchozím nastavení služba QnA Maker prohledává otázky a odpovědi. Pokud chcete prohledávat pouze otázky, chcete-li `RankerType=QuestionOnly` vygenerovat odpověď, použijte v textu POST požadavku GenerateAnswer.

Můžete prohledávat publikované kb, pomocí `isTest=false`, nebo `isTest=true`v testu kb pomocí .

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}
```

## <a name="common-http-errors"></a>Běžné chyby PROTOKOLU HTTP

|kód|Vysvětlení|
|:--|--|
|2xx|Úspěch|
|400|Parametry požadavku jsou nesprávné, což znamená, že požadované parametry chybí, jsou poškozeny nebo příliš velké|
|400|Tělo požadavku je nesprávné, což znamená, že json chybí, je poškozený nebo příliš velký|
|401|Neplatný klíč|
|403|Zakázáno - nemáte správná oprávnění|
|404|KB neexistuje|
|410|Toto rozhraní API je zastaralé a již není k dispozici.|

## <a name="next-steps"></a>Další kroky

Stránka **Publikovat** také poskytuje informace pro [generování odpovědi](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md) s Postman nebo cURL.

> [!div class="nextstepaction"]
> [Vytvoření robota znalostní báze](../tutorials/integrate-qnamaker-luis.md)
