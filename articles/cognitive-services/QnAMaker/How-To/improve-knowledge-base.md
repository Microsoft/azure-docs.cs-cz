---
title: Zlepšete znalostní bázi - QnA Maker
description: Zlepšete kvalitu své znalostní báze s aktivním učením. Zkontrolujte, přijměte nebo zamítněte, přidejte bez odebrání nebo změny stávajících otázek.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 7fafc23eaf21099ebb974da226d07c351fa19699
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756750"
---
# <a name="accept-active-learning-suggested-questions-in-the-knowledge-base"></a>Přijetí aktivních otázek navržených učením ve znalostní bázi


<a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>

Funkce Active Learning po schválení návrhu změní znalostní bázi nebo vyhledávací službu a uloží a táčí. Pokud návrh schválíte, bude přidán jako alternativní otázka.

## <a name="turn-on-active-learning"></a>Aktivace aktivního učení

Chcete-li zobrazit navrhované otázky, musíte [zapnout aktivní učení](use-active-learning.md) pro zdroj QnA Maker.

## <a name="view-suggested-questions"></a>Zobrazit navrhované otázky

1. Chcete-li zobrazit navrhované otázky, vyberte na stránce **Upravit** znalostní bázi **možnost Zobrazit možnosti**a pak vyberte **Zobrazit návrhy aktivních učení**.

    [![V části Úpravy na portálu vyberte Zobrazit návrhy, abyste viděli nové alternativy otázek aktivního učení.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. Umožňuje filtrovat znalostní bázi pomocí párů otázek a odpovědí tak, aby zobrazovala pouze návrhy výběrem **možnosti Filtrovat podle návrhů**.

    [![Pomocí přepínače Filtr podle návrhů zobrazíte pouze alternativy doporučených otázek pro aktivní učení.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. Každá dvojice QnA navrhuje nové alternativy otázek `✔` se zaškrtnutím , `x` přijmout otázku nebo odmítnout návrhy. Zaškrtnutím políčka přidejte otázku.

    [![Vyberte nebo odmítněte alternativy doporučených otázek aktivního učení zaškrtnutím zelené značky zaškrtnutí nebo červené značky odstranění.](../media/improve-knowledge-base/accept-active-learning-suggestions-small.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    _Všechny návrhy_ můžete přidat nebo odstranit tak, že v kontextovém panelu nástrojů vyberete **Přidat vše** nebo **Odmítnout vše.**

1. Chcete-li uložit změny do znalostní báze, vyberte možnost **Uložit a trénovat.**

1. Vyberte **Publikovat,** chcete-li povolit, aby změny byly k dispozici v [rozhraní API GenerateAnswer .](metadata-generateanswer-usage.md#generateanswer-request-configuration)

    Když jsou seskupeny 5 nebo více podobných dotazů, každých 30 minut, QnA Maker navrhuje alternativní otázky, které můžete přijmout nebo odmítnout.


<a name="#score-proximity-between-knowledge-base-questions"></a>

### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>Architektonický tok pro použití generateAnswer a trénování API z robota

Robot nebo jiná klientská aplikace by měla používat následující architektonický tok používat aktivní učení:

* Bot [získá odpověď ze znalostní báze](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers) pomocí `top` rozhraní GENERATEAnswer API, pomocí vlastnosti získat řadu odpovědí.
* Bot určuje explicitní zpětnou vazbu:
    * Pomocí vlastní [obchodní logiky](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user)odfiltrujte nízké skóre.
    * V bot nebo klient-aplikace, zobrazit seznam možných odpovědí na uživatele a získat uživatele vybrané odpovědi.
* Bot [odešle vybranou odpověď zpět qnA makeru](#bot-framework-sample-code) pomocí [Train API](#train-api).


### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>Použití nejvyšší vlastnosti v požadavku Generovat odpověď k získání několika odpovídajících odpovědí

Při odesílání otázky QnA Maker pro odpověď, `top` vlastnost těla JSON nastaví počet odpovědí vrátit.

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>Použití vlastnosti score spolu s obchodní logikou k získání seznamu odpovědí pro zobrazení uživatele

Když klientská aplikace (například chatovací robot) obdrží odpověď, jsou vráceny první 3 otázky. Pomocí `score` vlastnosti můžete analyzovat blízkost mezi skóre. Tento rozsah přiblížení je určen vlastní obchodní logikou.

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

## <a name="client-application-follow-up-when-questions-have-similar-scores"></a>Zpracování klientské aplikace v případě, že otázky mají podobné skóre

Klientská aplikace zobrazí otázky s možností pro uživatele vybrat _jednu otázku,_ která nejvíce představuje jejich záměr.

Jakmile uživatel vybere jednu z existujících otázek, klientská aplikace odešle volbu uživatele jako zpětnou vazbu pomocí rozhraní API train api služby QnA Maker. Tato zpětná vazba dokončí aktivní smyčku zpětné vazby učení.

## <a name="train-api"></a>Rozhraní Train API

Aktivní zpětná vazba učení se odesílá do QnA Makeru s požadavkem Train API POST. Podpis rozhraní API je:

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|Vlastnost požadavku HTTP|Name (Název)|Typ|Účel|
|--|--|--|--|
|Parametr trasy adresy URL|ID znalostní báze|řetězec|Identifikátor GUID pro vaši znalostní bázi.|
|Vlastní subdoména|Název prostředku QnAMaker|řetězec|Název prostředku se používá jako vlastní subdoména pro váš QnA Maker. Tato možnost je k dispozici na stránce Nastavení po publikování znalostní báze. Je uveden jako `host`.|
|Hlavička|Typ obsahu|řetězec|Typ média těla odeslaného do rozhraní API. Výchozí hodnota je:`application/json`|
|Hlavička|Autorizace|řetězec|Klíč koncového bodu (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxxxxxxxxx).|
|Tělo příspěvku|Objekt JSON|JSON|Zpětná vazba školení|

Tělo JSON má několik nastavení:

|Vlastnost těla JSON|Typ|Účel|
|--|--|--|--|
|`feedbackRecords`|pole|Seznam zpětné vazby.|
|`userId`|řetězec|ID uživatele osoby, která navrhované otázky přijímá. Formát ID uživatele je na vás. E-mailová adresa může být například platným ID uživatele ve vaší architektuře. Nepovinný parametr.|
|`userQuestion`|řetězec|Přesný text dotazu uživatele. Povinná hodnota.|
|`qnaID`|číslo|ID otázky, nalezené v [generateanswer odpověď](metadata-generateanswer-usage.md#generateanswer-response-properties). |

Příklad JSON tělo vypadá takto:

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

Úspěšná odpověď vrátí stav 204 a žádné tělo odezvy JSON.

### <a name="batch-many-feedback-records-into-a-single-call"></a>Dávka mnoha záznamů zpětné vazby do jednoho hovoru

V aplikaci na straně klienta, jako je například bot, můžete uložit data a `feedbackRecords` potom odeslat mnoho záznamů v jednom těle JSON v poli.

Příklad JSON tělo vypadá takto:

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

## <a name="bot-framework-sample-code"></a>Ukázkový kód rozhraní bot

Váš kód architektury robota musí volat Train API, pokud dotaz uživatele by měl být použit pro aktivní učení. Existují dva kusy kódu k napsání:

* Určení, zda má být dotaz použit pro aktivní učení
* Odeslání dotazu zpět do rozhraní API pro aktivní výuku služby QnA Maker

V [ukázce Azure Botu](https://aka.ms/activelearningsamplebot)byly naprogramovány obě tyto aktivity.

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>Příklad kódu Jazyka C# pro rozhraní TRAIN API s rozhraním Bot Framework 4.x

Následující kód ukazuje, jak odesílat informace zpět do QnA Maker s train API.

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

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>Příklad kódu Node.js pro rozhraní TRAIN API s rozhraním Bot Framework 4.x

Následující kód ukazuje, jak odesílat informace zpět do QnA Maker s train API.

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

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>Aktivní učení je uloženo v exportované znalostní bázi

Když je vaše aplikace povolena aktivní výuka `SuggestedQuestions` a exportujete aplikaci, sloupec v souboru tsv zachová aktivní data učení.

Sloupec `SuggestedQuestions` je objekt JSON informací implicitní, `autosuggested`a `usersuggested` explicitní, zpětná vazba. Příkladem tohoto objektu JSON pro otázku `help` odeslanou jedním uživatelem je:

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

Pomocí rozhraní API pro změny stahování můžete také zkontrolovat tyto změny pomocí sady REST nebo libovolné sady SDK založené na jazyce:
* [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fc)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.alterationsextensions.getasync?view=azure-dotnet)


Při opětovném importu této aplikace aktivní učení pokračuje ve shromažďování informací a doporučujeme návrhy pro vaši znalostní bázi.



## <a name="best-practices"></a>Osvědčené postupy

Doporučené postupy při používání aktivního učení naleznete v [tématu Doporučené postupy](../Concepts/best-practices.md#active-learning).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Použití metadat s rozhraním GenerateAnswer API](metadata-generateanswer-usage.md)
