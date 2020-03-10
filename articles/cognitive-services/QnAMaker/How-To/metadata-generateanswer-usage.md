---
title: Metadata s GenerateAnswer API – QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker umožňuje přidat metadata ve formě párů klíč/hodnota k sadám dotazů a odpovědí. Výsledky můžete filtrovat podle uživatelských dotazů a ukládat Další informace, které se dají použít v následných konverzacích.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/22/2019
ms.author: diberry
ms.openlocfilehash: 6a8cbabfd4e47c50d2c2e6f4a23c50a931e645a0
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78389303"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>Získat odpověď s GenerateAnswer API a metadaty

Pokud chcete získat předpokládanou odpověď na otázku uživatele, použijte rozhraní GenerateAnswer API. Když publikujete znalostní bázi, uvidíte informace o tom, jak toto rozhraní API používat na stránce **publikovat** . Můžete také nakonfigurovat rozhraní API pro filtrování odpovědí na základě značek metadat a otestovat znalostní bázi z koncového bodu s parametrem řetězce dotazu test.

QnA Maker umožňuje přidat metadata ve formě párů klíč-hodnota do sady otázek a odpovědí. Tyto informace pak můžete použít k filtrování výsledků do uživatelských dotazů a k ukládání dalších informací, které je možné použít v následných konverzacích. Další informace najdete v tématu [znalostní báze](../Concepts/knowledge-base.md).

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Uložení otázek a odpovědí s entitou QnA

Je důležité porozumět tomu, jak QnA Maker ukládá data otázky a odpovědi. Následující ilustrace znázorňuje QnA entitu:

![Ilustrace entity QnA](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Každá entita QnA má jedinečné a trvalé ID. Pomocí ID můžete provádět aktualizace konkrétní entity QnA.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Získání odpovědi předpovědi pomocí rozhraní GenerateAnswer API

[Rozhraní GenerateAnswer API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) ve své robotě nebo aplikaci můžete použít k dotazování znalostní báze s uživatelskou otázkou, abyste získali nejlepší shodu od sad otázek a odpovědí.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Publikování pro získání koncového bodu GenerateAnswer

Po publikování znalostní báze z [QnA Makerového portálu](https://www.qnamaker.ai)nebo pomocí [rozhraní API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish)můžete získat podrobnosti o koncovém bodu GenerateAnswer.

Jak získat podrobnosti o koncovém bodu:
1. Přihlaste se k webu [https://www.qnamaker.ai](https://www.qnamaker.ai).
1. V **seznamu Moje znalostní**báze vyberte možnost **Zobrazit kód** pro znalostní bázi.
    ![snímek obrazovky se základy znalostní báze](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. Získejte podrobnosti o GenerateAnswer koncového bodu.

    ![Snímek obrazovky s podrobnostmi koncového bodu](../media/qnamaker-how-to-metadata-usage/view-code.png)

Podrobnosti o koncových bodech můžete také získat z karty **Nastavení** ve znalostní bázi.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>Konfigurace žádosti GenerateAnswer

GenerateAnswer zavoláte s požadavkem HTTP POST. Vzorový kód, který ukazuje, jak volat GenerateAnswer, naleznete v tématu [rychlé starty](../quickstarts/quickstart-sdk.md#generate-an-answer-from-the-knowledge-base).

Požadavek POST používá:

* Požadované [parametry identifikátoru URI](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* Požadovaná vlastnost hlavičky, `Authorization`, pro zabezpečení
* Požadované [vlastnosti těla](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto).

Adresa URL GenerateAnswer má následující formát:

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

Nezapomeňte nastavit vlastnost hlaviček protokolu HTTP `Authorization` hodnotou `EndpointKey` řetězce s koncovým místem, kde se na stránce **Nastavení** nachází klíč koncového bodu.

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

[Odpověď](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query) je objekt JSON, včetně všech informací, které potřebujete k zobrazení odpovědi, a další Zapnutí konverzace, pokud je k dispozici.

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

## <a name="use-qna-maker-with-a-bot-in-c"></a>Použití QnA Maker s robotem vC#

Rozhraní bot Framework poskytuje přístup k vlastnostem QnA Maker pomocí [rozhraní Getanswer API](https://docs.microsoft.com/dotnet/api/microsoft.bot.builder.ai.qna.qnamaker.getanswersasync?view=botbuilder-dotnet-stable#Microsoft_Bot_Builder_AI_QnA_QnAMaker_GetAnswersAsync_Microsoft_Bot_Builder_ITurnContext_Microsoft_Bot_Builder_AI_QnA_QnAMakerOptions_System_Collections_Generic_Dictionary_System_String_System_String__System_Collections_Generic_Dictionary_System_String_System_Double__):

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

Robot podpory obsahuje [příklad](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-support/csharp_dotnetcore/Service/SupportBotService.cs#L418) s tímto kódem.

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>Použití QnA Maker s robotem v Node. js

Rozhraní bot Framework poskytuje přístup k vlastnostem QnA Maker pomocí [rozhraní Getanswer API](https://docs.microsoft.com/javascript/api/botbuilder-ai/qnamaker?view=botbuilder-ts-latest#generateanswer-string---undefined--number--number-):

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

Robot podpory obsahuje [příklad](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs/Helpers/dialogHelper.js#L36) s tímto kódem.

<a name="metadata-example"></a>

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Filtrování odpovědí podle vlastních značek metadat pomocí metadat

Přidání metadat umožňuje filtrovat odpovědi pomocí těchto značek metadat. Přidejte sloupec metadata z nabídky **Možnosti zobrazení** . Přidejte metadata do znalostní báze tak, že vyberete ikonu **+** metadat a přidáte dvojici metadat. Tento pár se skládá z jednoho klíče a jedné hodnoty.

![Snímek obrazovky s přidáním metadat](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtrovat výsledky pomocí strictFilters pro značky metadat

Vezměte v úvahu otázku uživatele "kdy se tento hotel blíží?", kde je záměr předpokládaný pro restauraci "Paradise".

Vzhledem k tomu, že se výsledky vyžadují jenom pro restaurace "Paradise", můžete nastavit filtr ve volání GenerateAnswer v metadatech "název restaurace". Následující příklad ukazuje:

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

Odpověď na GenerateAnswer obsahuje odpovídající informace metadat pro sadu odpovědí a otázku, které odpovídají. Tyto informace můžete v klientské aplikaci použít k uložení kontextu předchozí konverzace pro použití v pozdějších konverzacích.

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

Ve výchozím nastavení QnA Maker vyhledává dotazy a odpovědi. Chcete-li vyhledávat pouze otázky, vygenerujte odpověď, použijte `RankerType=QuestionOnly` v těle žádosti GenerateAnswer.

Můžete prohledat publikované znalostní báze KB, pomocí `isTest=false`nebo v testu kB pomocí `isTest=true`.

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
|2xx|Úspěch|
|400|Parametry žádosti jsou nesprávné, což znamená, že požadované parametry jsou chybí, je poškozený nebo je příliš velký|
|400|tělo žádosti je nesprávná, což znamená, že je ve formátu JSON chybí, je poškozený nebo je příliš velký|
|401|Neplatný klíč|
|403|Je zakázané - nemáte správná oprávnění|
|404|KB neexistuje|
|410|Toto rozhraní API je zastaralé a už není dostupné.|

## <a name="next-steps"></a>Další kroky

Stránka **publikování** také poskytuje informace pro [vygenerování odpovědi](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md) pomocí metody post nebo kudrlinkou.

> [!div class="nextstepaction"]
> [Vytvoření robota znalostní báze](../tutorials/integrate-qnamaker-luis.md)
