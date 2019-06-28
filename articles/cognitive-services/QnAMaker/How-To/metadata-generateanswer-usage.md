---
title: Metadata s GenerateAnswer API – QnA Maker
titleSuffix: Azure Cognitive Services
description: Nástroj QnA Maker umožňuje přidat metadata ve formě dvojice klíč/hodnota do sad otázek a odpovědí. Můžete filtrovat výsledky na dotazy uživatelů a ukládání dalších informací, lze použít v následných konverzace.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/27/2019
ms.author: diberry
ms.openlocfilehash: 99c076d7f26638833b568935e766cf319d21945e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443474"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>Získejte odpovědi pomocí rozhraní GenerateAnswer API a metadat

Předpokládaná odpověď na otázku uživatele, použijte rozhraní API GenerateAnswer. Při publikování znalostní báze se zobrazí informace o tom, jak pomocí tohoto rozhraní API na **publikovat** stránky. Můžete také konfigurovat rozhraní API filtrování odpovědi na základě metadat značek a testování ve znalostní bázi z koncového bodu pomocí parametru řetězce dotazu testu.

Nástroj QnA Maker umožňuje přidat metadata ve formě dvojice klíče a hodnoty do sad otázek a odpovědí. Pak můžete tyto informace pro filtrování výsledků na dotazy uživatelů a k ukládání dalších informací, lze použít v následných konverzace. Další informace najdete v tématu [znalostní báze](../Concepts/knowledge-base.md).

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Otázky a odpovědi s entitou QnA Store

Je důležité pochopit, jak QnA Maker ukládá data otázek a odpovědí. Následující obrázek znázorňuje QnA entity:

![Obrázek QnA entity](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Každá entita QnA má jedinečný a trvalý ID. Můžete provést aktualizace na konkrétní entitu QnA ID.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Získání odpovědí předpovědi s rozhraním API GenerateAnswer

Můžete použít [GenerateAnswer API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) v váš bot nebo aplikaci k dotazování znalostní báze se dotaz uživatele, nastaví se získat nejlepší shodu z otázek a odpovědí.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Publikování na získání GenerateAnswer koncového bodu 

Po publikování znalostní báze, buď z [portál QnA Maker](https://www.qnamaker.ai), nebo pomocí [API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish), můžete získat podrobné GenerateAnswer koncového bodu.

Získat podrobnosti o vašich koncových bodů:
1. Přihlaste se k webu [https://www.qnamaker.ai](https://www.qnamaker.ai).
1. V **Moje znalostních bází**vyberte **zobrazit kód** pro znalostní báze.
    ![Snímek obrazovky Moje znalostních bází](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. Získáte podrobnosti o vašich GenerateAnswer koncového bodu.

    ![Snímek obrazovky se podrobnosti o koncovém bodu](../media/qnamaker-how-to-metadata-usage/view-code.png)

Můžete také získat podrobnosti o vašem koncového bodu z **nastavení** kartu znalostní báze.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>Konfigurační požadavek GenerateAnswer

Volání GenerateAnswer pomocí požadavku HTTP POST. Ukázkový kód, který ukazuje, jak volat GenerateAnswer, najdete v článku [rychlých startů](../quickstarts/csharp.md). 

Požadavek POST používá:

* Vyžaduje [parametry identifikátoru URI](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* Vyžaduje [vlastnosti hlavička](https://docs.microsoft.com/azure/cognitive-services/qnamaker/quickstarts/get-answer-from-knowledge-base-nodejs#add-a-post-request-to-send-question-and-get-an-answer), `Authorization`, pro zabezpečení
* Vyžaduje [základní vlastnosti](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto). 

Adresa URL GenerateAnswer má následující formát: 

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

Nezapomeňte nastavit vlastnost záhlaví HTTP `Authorization` s hodnotou řetězce `EndpointKey ` s koncový znak mezery pak klíč koncového bodu, který najdete na **nastavení** stránky.

Příklad text JSON vypadá takto:

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

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>Vlastnosti GenerateAnswer odpovědi

[Odpovědi](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful_query) je objekt JSON, včetně všech informace, které potřebujete k zobrazení odpovědi a dalších zapnout v konverzaci, pokud je k dispozici.

```json
{
    "answers": [
        {
            "score": 28.54820341616869,
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

## <a name="use-qna-maker-with-a-bot-in-c"></a>Použijte nástroj QnA Maker s využitím botu vC#

Rozhraní bot framework poskytuje přístup k vlastnostem QnA Maker:

```csharp
using Microsoft.Bot.Builder.AI.QnA;
var metadata = new Microsoft.Bot.Builder.AI.QnA.Metadata();
var qnaOptions = new QnAMakerOptions();

qnaOptions.Top = Constants.DefaultTop;
qnaOptions.ScoreThreshold = 0.3F;
var response = await _services.QnAServices[QnAMakerKey].GetAnswersAsync(turnContext, qnaOptions);
```

Robot podpory má [příklad](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-support/csharp_dotnetcore/Service/SupportBotService.cs#L418) s tímto kódem.

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>Použijte nástroj QnA Maker s využitím botu v Node.js

Rozhraní bot framework poskytuje přístup k vlastnostem QnA Maker:

```javascript
const { QnAMaker } = require('botbuilder-ai');
this.qnaMaker = new QnAMaker(endpoint);

// Default QnAMakerOptions
var qnaMakerOptions = {
    ScoreThreshold: 0.03,
    Top: 3
};
var qnaResults = await this.qnaMaker.getAnswers(stepContext.context, qnaMakerOptions);
```

Robot podpory má [příklad](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs/Helpers/dialogHelper.js#L36) s tímto kódem.

<a name="metadata-example"></a>

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Filtrovat podle vlastní metadatové značky odpovědi pomocí metadat

Přidání metadat umožňuje filtrovat podle značky těchto metadat odpovědi. Přidat sloupec metadat z **možnosti zobrazení** nabídky. Přidat metadata do znalostní báze výběrem metadata **+** ikona pro přidání páru metadat. Tento pár se skládá z jednoho klíče a jedna hodnota.

![Snímek obrazovky přidání metadat](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtrování výsledků s strictFilters pro značky metadat

Zvažte otázku uživatele "Když se tento hotelu zavřít?", kde je implicitní záměr pro restaurace "Paradise."

Protože výsledky jsou vyžadována pouze pro restaurace "Paradise", můžete nastavit filtr ve volání GenerateAnswer metadat ""restaurace název. Následující příklad ukazuje toto:

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

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Použití výsledky otázky a odpovědi k zajištění kontextu konverzace

Odpověď GenerateAnswer obsahuje odpovídající informace metadat odpovídající sadu otázek a odpovědí. Tyto informace můžete použít v klientské aplikaci k ukládání kontextu předchozí konverzace pro použití v pozdější konverzace. 

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

## <a name="match-questions-only-by-text"></a>Odpovídají pouze dotazy podle textu

Nástroj QnA Maker se ve výchozím nastavení, prohledá otázek a odpovědí. Pokud chcete prohledávat pouze dotazy, generovat odpověď, použijte `RankerType=QuestionOnly` v textu POST GenerateAnswer požadavku.

Můžete prohledávat publikované kb pomocí `isTest=false`, nebo v kb test pomocí `isTest=true`.

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}
```

## <a name="next-steps"></a>Další postup

**Publikovat** stránka také obsahuje informace pro generování odpovědi pomocí [Postman](../Quickstarts/get-answer-from-kb-using-postman.md) a [cURL](../Quickstarts/get-answer-from-kb-using-curl.md). 

> [!div class="nextstepaction"]
> [Vytvoření znalostní báze](./create-knowledge-base.md)
