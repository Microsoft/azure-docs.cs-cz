---
title: Otázky, které jsou navržené aktivními kurzy – QnA Maker
description: Vylepšete kvalitu znalostní báze s aktivním učením. Zkontrolujte, přijměte nebo odmítněte, přidejte bez odebrání nebo změny existujících otázek.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 04/06/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 7d7dc8564ee35bcd8bfd92d996d07e1d0c365806
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776489"
---
# <a name="accept-active-learning-suggested-questions-in-the-knowledge-base"></a>Přijetí dotazů na aktivní učení ve znalostní bázi Knowledge Base


<a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>

Aktivní učení mění znalostní bázi nebo Search Service, když schválíte návrh a pak ho uložíte a naučíte. Pokud schválíte návrh, přidá se jako alternativní otázka.

## <a name="turn-on-active-learning"></a>Aktivace aktivního učení

Chcete-li zobrazit navrhované otázky, je nutné [zapnout službu Active Learning](use-active-learning.md) pro váš QnA maker prostředek.

## <a name="view-suggested-questions"></a>Zobrazit navrhované otázky

1. Chcete-li zobrazit navrhované otázky, na stránce **Upravit** znalostní bázi vyberte možnost **Zobrazit možnosti**a pak vyberte možnost **Zobrazit návrhy aktivního učení**.

    [![V části Upravit na portálu vyberte možnost Zobrazit návrhy, aby se zobrazily nové alternativy otázek aktivního učení.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. Vyfiltrujte znalostní bázi dvojice otázek a odpovědí, abyste zobrazili jenom návrhy, a to tak, že vyberete **filtrovat podle návrhů**.

    [![Pomocí přepínače filtrovat podle návrhů můžete zobrazit jenom navrhované alternativní otázky pro aktivní učení.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. Každý pár QnA navrhuje nové alternativy dotazů s zaškrtnutím, `✔` k přijetí otázky nebo `x` k zamítnutí návrhů. Kliknutím na značku zaškrtnutí přidejte otázku.

    [![Zaškrtněte nebo zrušte výběr alternativních otázek pro aktivní učení, a to tak, že vyberete zelenou značku zaškrtnutí nebo červenou značku DELETE.](../media/improve-knowledge-base/accept-active-learning-suggestions-small.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    _Všechny návrhy_ můžete přidat nebo odstranit výběrem možnosti **Přidat vše** nebo **Odmítnout vše** na kontextovém panelu nástrojů.

1. Kliknutím na **Uložit a výuka** uložte změny ve znalostní bázi.

1. Vyberte **publikovat** , pokud chcete, aby byly změny dostupné z [rozhraní GenerateAnswer API](metadata-generateanswer-usage.md#generateanswer-request-configuration).

    V případě, že je 5 nebo více podobných dotazů clusterovaných, QnA Maker každých 30 minut návrhy alternativních otázek, které můžete přijmout nebo odmítnout.


<a name="#score-proximity-between-knowledge-base-questions"></a>

### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>Postup architektury pro používání rozhraní API pro GenerateAnswer a vlak z robota

Robot nebo jiná klientská aplikace by se měli pomocí tohoto toku architektury použít k používání aktivního učení:

* Robot [získá odpověď ze znalostní báze](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers) s rozhraním API GenerateAnswer a pomocí `top` vlastnosti získá několik odpovědí.
* Robot určí explicitní zpětnou vazbu:
    * Pomocí vlastní [obchodní logiky](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user)vyfiltrujte nízká skóre.
    * V robotu nebo klientské aplikaci můžete zobrazit seznam možných odpovědí na uživatele a získat zvolenou odpověď uživatele.
* Robot [pošle vybranou odpověď zpátky do QnA maker](#bot-framework-sample-code) s využitím [rozhraní API pro vlaky](#train-api).


### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>Použijte vlastnost Top v žádosti GenerateAnswer k získání několika vyhovujících odpovědí.

Při odesílání otázky QnA Maker pro odpověď `top` nastaví vlastnost těla zprávy JSON počet odpovědí, které se mají vrátit.

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>K získání seznamu odpovědí pro zobrazení uživatele použijte vlastnost skóre spolu s obchodní logikou.

Když klientská aplikace (například robota v programu chat) obdrží odpověď, vrátí se 3 Nejčastější dotazy. Vlastnost slouží `score` k analýze blízkosti výsledků. Tento rozsah blízkosti je určený vaší vlastní obchodní logikou.

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ *+ ' Wi-Fi Direct is displayed in the Status bar.",
            "score": 74.21,
            "id": 607,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Wi-Fi - Connections"
            ],
            "answer": "**Wi-Fi**\n\nWi-Fi is a term used for certain types of Wireless Local Area Networks (WLAN). Wi-Fi communication requires access to a wireless Access Point (AP).",
            "score": 74.15,
            "id": 599,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Turn Wi-Fi On or Off"
            ],
            "answer": "**Turn Wi-Fi On or Off**\n\nTurning Wi-Fi on makes your device able to discover and connect to compatible in-range wireless APs.  \n\n1.  From a Home screen, tap ::: Apps > e Settings .\n2.  Tap Connections > Wi-Fi , and then tap On/Off to turn Wi-Fi on or off.",
            "score": 69.99,
            "id": 600,
            "source": "Bugbash KB.pdf",
            "metadata": []
        }
    ]
}
```

## <a name="client-application-follow-up-when-questions-have-similar-scores"></a>Sledování klientských aplikací, když mají dotazy podobné skóre

Klientská aplikace zobrazí otázky s možností, jak uživateli vybrat _jedinou otázku_ , která nejvíc představuje svůj záměr.

Jakmile uživatel vybere jednu z existujících otázek, klientská aplikace pošle výběr uživatele jako zpětnou vazbu pomocí QnA Makerho rozhraní API pro vlaky. Tato zpětná vazba dokončí smyčku aktivního výukového zpětné vazby.

## <a name="train-api"></a>Rozhraní Train API

Zpětná vazba k aktivnímu učení se pošle QnA Maker s žádostí POST API pro vlak. Podpis rozhraní API:

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|Vlastnost požadavku HTTP|Název|Typ|Účel|
|--|--|--|--|
|Parametr trasy adresy URL|ID znalostní báze|řetězec|Identifikátor GUID znalostní báze|
|Vlastní subdoména|Název prostředku Qnamakerem|řetězec|Název prostředku se používá jako vlastní subdoména pro váš QnA Maker. Tato možnost je k dispozici na stránce nastavení po publikování znalostní báze. Je uveden jako `host` .|
|Záhlaví|Typ obsahu|řetězec|Typ média těla odesílaného do rozhraní API Výchozí hodnota je: `application/json`|
|Záhlaví|Autorizace|řetězec|Klíč koncového bodu (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Tělo příspěvku|Objekt JSON|JSON|Váš názor na školení|

Tělo JSON má několik nastavení:

|Vlastnost těla zprávy JSON|Typ|Účel|
|--|--|--|--|
|`feedbackRecords`|array|Seznam zpětné vazby|
|`userId`|řetězec|ID uživatele osoby, která přijímá navrhované otázky. Formát ID uživatele je až vám. Například e-mailová adresa může být platným ID uživatele ve vaší architektuře. Nepovinný parametr.|
|`userQuestion`|řetězec|Přesný text dotazu uživatele. Povinná hodnota.|
|`qnaID`|číslo|ID otázky nalezené v [odpovědi GenerateAnswer](metadata-generateanswer-usage.md#generateanswer-response-properties) |

Ukázkový text JSON vypadá takto:

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "<question-text>",
            "qnaId": 1
        }
    ]
}
```

Úspěšná odpověď vrátí stav 204 a text odpovědi JSON.

### <a name="batch-many-feedback-records-into-a-single-call"></a>Batch mnoho záznamů zpětné vazby do jednoho volání

V aplikaci na straně klienta, jako je robot, můžete ukládat data a potom do pole poslat mnoho záznamů v jednom těle JSON `feedbackRecords` .

Ukázkový text JSON vypadá takto:

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "How do I ...",
            "qnaId": 1
        },
        {
            "userId": "2",
            "userQuestion": "Where is ...",
            "qnaId": 40
        },
        {
            "userId": "3",
            "userQuestion": "When do I ...",
            "qnaId": 33
        }
    ]
}
```



<a name="active-learning-is-saved-in-the-exported-apps-tsv-file"></a>

## <a name="bot-framework-sample-code"></a>Vzorový kód pro bot Framework

Pokud se má dotaz uživatele použít pro aktivní učení, váš kód rozhraní bot Framework musí volat rozhraní API pro analýzu. Existují dva části kódu pro zápis:

* Určení, jestli se má dotaz použít pro aktivní učení
* Poslat dotaz zpátky do QnA Maker API pro školení pro aktivní učení

V [ukázce Azure bot](https://github.com/microsoft/BotBuilder-Samples)byly obě tyto aktivity naprogramované.

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>Příklad kódu jazyka C# pro rozhraní API pro učení s bot Framework 4. x

Následující kód ilustruje, jak odeslat informace zpět do QnA Maker pomocí rozhraní API pro vlaky.

```csharp
public class FeedbackRecords
{
    // <summary>
    /// List of feedback records
    /// </summary>
    [JsonProperty("feedbackRecords")]
    public FeedbackRecord[] Records { get; set; }
}

/// <summary>
/// Active learning feedback record
/// </summary>
public class FeedbackRecord
{
    /// <summary>
    /// User id
    /// </summary>
    public string UserId { get; set; }

    /// <summary>
    /// User question
    /// </summary>
    public string UserQuestion { get; set; }

    /// <summary>
    /// QnA Id
    /// </summary>
    public int QnaId { get; set; }
}

/// <summary>
/// Method to call REST-based QnAMaker Train API for Active Learning
/// </summary>
/// <param name="endpoint">Endpoint URI of the runtime</param>
/// <param name="FeedbackRecords">Feedback records train API</param>
/// <param name="kbId">Knowledgebase Id</param>
/// <param name="key">Endpoint key</param>
/// <param name="cancellationToken"> Cancellation token</param>
public async static void CallTrain(string endpoint, FeedbackRecords feedbackRecords, string kbId, string key, CancellationToken cancellationToken)
{
    var uri = endpoint + "/knowledgebases/" + kbId + "/train/";

    using (var client = new HttpClient())
    {
        using (var request = new HttpRequestMessage())
        {
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(uri);
            request.Content = new StringContent(JsonConvert.SerializeObject(feedbackRecords), Encoding.UTF8, "application/json");
            request.Headers.Add("Authorization", "EndpointKey " + key);

            var response = await client.SendAsync(request, cancellationToken);
            await response.Content.ReadAsStringAsync();
        }
    }
}
```

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>Příklad Node.js kódu pro rozhraní API pro učení s bot Framework 4. x

Následující kód ilustruje, jak odeslat informace zpět do QnA Maker pomocí rozhraní API pro vlaky.

```javascript
async callTrain(stepContext){

    var trainResponses = stepContext.values[this.qnaData];
    var currentQuery = stepContext.values[this.currentQuery];

    if(trainResponses.length > 1){
        var reply = stepContext.context.activity.text;
        var qnaResults = trainResponses.filter(r => r.questions[0] == reply);

        if(qnaResults.length > 0){

            stepContext.values[this.qnaData] = qnaResults;

            var feedbackRecords = {
                FeedbackRecords:[
                    {
                        UserId:stepContext.context.activity.id,
                        UserQuestion: currentQuery,
                        QnaId: qnaResults[0].id
                    }
                ]
            };

            // Call Active Learning Train API
            this.activeLearningHelper.callTrain(this.qnaMaker.endpoint.host, feedbackRecords, this.qnaMaker.endpoint.knowledgeBaseId, this.qnaMaker.endpoint.endpointKey);

            return await stepContext.next(qnaResults);
        }
        else{

            return await stepContext.endDialog();
        }
    }

    return await stepContext.next(stepContext.result);
}
```

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>Aktivní učení se ukládá do exportované znalostní báze.

Když má vaše aplikace aktivní učení a exportujete aplikaci, `SuggestedQuestions` sloupec v souboru TSV uchová aktivní výuková data.

`SuggestedQuestions`Sloupec je objekt JSON s informacemi o implicitní, `autosuggested` a explicitní `usersuggested` zpětné vazbě. Příkladem tohoto objektu JSON pro jednu otázku odeslanou uživatelem `help` je:

```JSON
[
    {
        "clusterHead": "help",
        "totalAutoSuggestedCount": 1,
        "totalUserSuggestedCount": 0,
        "alternateQuestionList": [
            {
                "question": "help",
                "autoSuggestedCount": 1,
                "userSuggestedCount": 0
            }
        ]
    }
]
```

Když znovu naimportujete tuto aplikaci, aktivní učení nadále shromažďuje informace a doporučuje návrhy vaší znalostní báze.



## <a name="best-practices"></a>Osvědčené postupy

Osvědčené postupy při používání aktivního učení najdete v tématu [osvědčené postupy](../Concepts/best-practices.md#active-learning).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Použití metadat s rozhraním GenerateAnswer API](metadata-generateanswer-usage.md)
