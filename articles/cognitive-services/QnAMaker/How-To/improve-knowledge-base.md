---
title: Vylepšení znalostní báze Knowledge Base – QnA Maker
titleSuffix: Azure Cognitive Services
description: Aktivní učení vám umožní vylepšit kvalitu znalostní báze tím, že navrhuje alternativní otázky na základě počtu uživatelů, a to na pár otázek a odpovědí. Tyto návrhy si můžete prohlédnout, buď je přidáte do existujících otázek, nebo je odmítnete. Vaše znalostní báze se nemění automaticky. Aby se změny projevily, musíte přijmout návrhy. Tyto návrhy přidávají otázky, ale nemění ani neodstraňují stávající otázky.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: diberry
ms.openlocfilehash: ab4447c8c07f8e8315c0258cc3254e5272ab7582
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71272439"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>Využití aktivního učení k vylepšení vaší znalostní báze

Aktivní učení vám umožní vylepšit kvalitu znalostní báze tím, že navrhuje alternativní otázky na základě počtu uživatelů, a to na pár otázek a odpovědí. Tyto návrhy si můžete prohlédnout, buď je přidáte do existujících otázek, nebo je odmítnete. 

Vaše znalostní báze se nemění automaticky. Aby se změny projevily, musíte přijmout návrhy. Tyto návrhy přidávají otázky, ale nemění ani neodstraňují stávající otázky.

## <a name="what-is-active-learning"></a>Co je aktivní učení?

QnA Maker se učí nové variace otázek s implicitní a explicitní zpětnou vazbou.
 
* [Implicitní zpětná vazba](#how-qna-makers-implicit-feedback-works) – seznámení s tím, že má uživatelská otázka více odpovědí s výsledky, které jsou velmi blízko a považuje se za zpětnou vazbu. K tomu nemusíte nic dělat.
* [Explicitní názory](#how-you-give-explicit-feedback-with-the-train-api) – Pokud se ve znalostní bázi Knowledge Base vrátí více odpovědí s malým kolísáním skóre, klientská aplikace se zeptá, který dotaz je na správnou otázku. Explicitní zpětná vazba uživatele se pošle QnA Maker s využitím [rozhraní API pro vlaky](#train-api). 

Obě metody poskytují pořadí s podobnými dotazy, které jsou v clusteru.

## <a name="how-active-learning-works"></a>Jak funguje aktivní učení

Aktivní učení se aktivuje na základě skóre nejčastějších odpovědí vrácených QnA Maker. Pokud rozdíly v skóre leží v malém rozsahu, pak se dotaz považuje za možný návrh (jako alternativní otázka) pro každý z možných QnA párů. Jakmile přijmete navrhovanou otázku pro určitý pár QnA, je odmítnut pro ostatní páry. Po přijetí návrhů musíte pamatovat na uložení a výuku.

Aktivní učení nabízí nejlepší možné návrhy v případech, kdy koncové body získávají přijatelné množství a různé dotazy na používání. Když je 5 nebo více podobných dotazů clusterovaných, každých 30 minut QnA Maker navrhuje dotazy založené na uživateli v Návrháři znalostní báze, aby je bylo možné přijmout nebo odmítnout. Všechny návrhy jsou seskupené podle podobnosti a na základě frekvence konkrétních dotazů koncovými uživateli se zobrazují nejdůležitější návrhy na alternativní otázky.

Po navržení otázek na portálu QnA Maker musíte tyto návrhy projít a přijmout nebo odmítnout. Není k dispozici rozhraní API pro správu návrhů.

## <a name="how-qna-makers-implicit-feedback-works"></a>Způsob fungování implicitní zpětné vazby QnA Maker

Implicitní zpětná vazba QnA Maker používá algoritmus k určení blízkosti skóre a následně k vytváření návrhů na aktivní učení. Algoritmus k určení blízkosti není jednoduchý výpočet. Rozsahy v následujícím příkladu nejsou určeny k pevnému, ale měly by být použity jako vodítko pro pochopení dopadu pouze na algoritmus.

Když je skóre otázky vysoce důvěrná, například 80%, rozsah skóre, která jsou považována za aktivní, je v širokém rozsahu přibližně do 10%. Vzhledem k poklesu spolehlivosti, jako je 40%, se rozsah hodnocení zmenší i přibližně do 4%. 

## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>Způsob poskytnutí explicitní zpětné vazby pomocí rozhraní API pro vlaky

Je důležité, aby QnA Maker získat explicitní zpětnou vazbu o tom, které odpovědi odpověděly na nejlepší odpověď. Způsob, jakým je nejlepší odpověď určena, je až na vás a může zahrnovat:

* Zpětnou vazbu od uživatele vyberte jednu z odpovědí.
* Obchodní logika, například určení přijatelného rozsahu skóre.  
* Kombinace zpětné vazby uživatelů a obchodní logiky.

## <a name="upgrade-your-runtime-version-to-use-active-learning"></a>Upgrade verze modulu runtime pro použití aktivního učení

Aktivní učení je podporované v modulu runtime verze 4.4.0 a vyšší. Pokud byla znalostní báze vytvořena v dřívější verzi, [upgradujte modul runtime](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates) , aby tuto funkci používal. 

## <a name="turn-on-active-learning-to-see-suggestions"></a>Pokud chcete zobrazit návrhy, zapněte aktivní učení.

Aktivní učení je ve výchozím nastavení vypnuté. Zapněte ho, aby se zobrazily navržené otázky. Po zapnutí aktivního učení je potřeba odeslat informace z klientské aplikace do QnA Maker. Další informace najdete v tématu [architektonický tok pro použití GenerateAnswer a rozhraní API pro učení z robota](#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot).

1. Vyberte **publikovat** a publikujte znalostní bázi. Aktivní výukové dotazy se shromažďují jenom z koncového bodu předpovědi rozhraní GenerateAnswer API. Dotazy do podokna test na portálu QnA Maker neovlivňují aktivní učení.

1. Pokud chcete zapnout aktivní učení na portálu QnA Maker, přečtěte si v pravém horním rohu, vyberte své **jméno**a pak přejít na [**nastavení služby**](https://www.qnamaker.ai/UserSettings).  

    ![Na stránce nastavení služby zapněte možnosti pro navrhované otázky pro aktivní učení. V pravé horní nabídce vyberte své uživatelské jméno a pak vyberte nastavení služby.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Vyhledejte službu QnA Maker a potom přepněte **aktivní učení**. 

    [![Na stránce nastavení služby přepněte na funkci aktivní učení. Pokud nemůžete funkci zapnout, možná budete muset upgradovat službu.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > Přesná verze na předchozím obrázku se zobrazí jenom jako příklad. Vaše verze se může lišit. 

    Po povolení **aktivního učení** znalostní báze v pravidelných intervalech navrhuje nové otázky na základě dotazů odeslaných uživatelem. Můžete zakázat **aktivní učení** tím, že znovu přepnete nastavení.

## <a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>Přijmout aktivní návrh učení ve znalostní bázi

Aktivní učení mění znalostní bázi nebo Search Service, když schválíte návrh a pak ho uložíte a naučíte. Pokud schválíte návrh, přidá se jako alternativní otázka.

1. Chcete-li zobrazit navrhované otázky, na stránce **Upravit** znalostní bázi vyberte možnost **Zobrazit možnosti**a pak vyberte možnost **Zobrazit návrhy aktivního učení**. 

    [![V části Upravit na portálu vyberte možnost Zobrazit návrhy, aby se zobrazily nové alternativy otázek aktivního učení.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. Vyfiltrujte znalostní bázi dvojice otázek a odpovědí, abyste zobrazili jenom návrhy, a to tak, že vyberete **filtrovat podle návrhů**.

    [![Pomocí přepínače filtrovat podle návrhů můžete zobrazit jenom navrhované alternativní otázky pro aktivní učení.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. Každý pár QnA navrhuje nové alternativy dotazů s zaškrtnutím, `✔` k přijetí otázky `x` nebo k zamítnutí návrhů. Kliknutím na značku zaškrtnutí přidejte otázku. 

    [![Zaškrtněte nebo zrušte výběr alternativních otázek pro aktivní učení, a to tak, že vyberete zelenou značku zaškrtnutí nebo červenou značku DELETE.](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

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

Při odesílání otázky QnA maker pro odpověď `top` nastaví vlastnost těla zprávy JSON počet odpovědí, které se mají vrátit. 

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>K získání seznamu odpovědí pro zobrazení uživatele použijte vlastnost skóre spolu s obchodní logikou.

Když klientská aplikace (například robota v programu chat) obdrží odpověď, vrátí se 3 Nejčastější dotazy. `score` Vlastnost slouží k analýze blízkosti výsledků. Tento rozsah blízkosti je určený vaší vlastní obchodní logikou. 

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ •+ ' Wi-Fi Direct is displayed in the Status bar.",
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

|Vlastnost požadavku HTTP|Name|type|Účel|
|--|--|--|--|
|Parametr trasy adresy URL|ID znalostní báze|řetězec|Identifikátor GUID znalostní báze|
|Host subdomain|Název prostředku Qnamakerem|řetězec|Název hostitele vašeho QnA Maker ve vašem předplatném Azure. Tato možnost je k dispozici na stránce nastavení po publikování znalostní báze. |
|Záhlaví|Typ obsahu|řetězec|Typ média těla odesílaného do rozhraní API Výchozí hodnota je:`application/json`|
|Záhlaví|Authorization|řetězec|Klíč koncového bodu (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Tělo příspěvku|Objekt JSON|JSON|Váš názor na školení|

Tělo JSON má několik nastavení:

|Vlastnost těla zprávy JSON|type|Účel|
|--|--|--|--|
|`feedbackRecords`|array|Seznam zpětné vazby|
|`userId`|řetězec|ID uživatele osoby, která přijímá navrhované otázky. Formát ID uživatele je až vám. Například e-mailová adresa může být platným ID uživatele ve vaší architektuře. Volitelný parametr.|
|`userQuestion`|řetězec|Přesný text dotazu uživatele. Povinný parametr.|
|`qnaID`|number|ID otázky nalezené v [odpovědi GenerateAnswer](metadata-generateanswer-usage.md#generateanswer-response-properties) |

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

V aplikaci na straně klienta, jako je robot, můžete ukládat data a potom do `feedbackRecords` pole poslat mnoho záznamů v jednom těle JSON. 

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

V [ukázce Azure bot](https://aka.ms/activelearningsamplebot)byly obě tyto aktivity naprogramované. 

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>Příklad C# kódu pro rozhraní API pro učení s robot Framework 4. x

Následující kód ilustruje, jak odeslat informace zpět do QnA Maker pomocí rozhraní API pro vlaky. Tato [kompletní ukázka kódu](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-activelearning/csharp_dotnetcore) je k dispozici na GitHubu.

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
/// <param name="host">Endpoint host of the runtime</param>
/// <param name="FeedbackRecords">Feedback records train API</param>
/// <param name="kbId">Knowledgebase Id</param>
/// <param name="key">Endpoint key</param>
/// <param name="cancellationToken"> Cancellation token</param>
public async static void CallTrain(string host, FeedbackRecords feedbackRecords, string kbId, string key, CancellationToken cancellationToken)
{
    var uri = host + "/knowledgebases/" + kbId + "/train/";

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

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>Příklad kódu Node. js pro rozhraní API pro učení s bot Framework 4. x 

Následující kód ilustruje, jak odeslat informace zpět do QnA Maker pomocí rozhraní API pro vlaky. Tato [kompletní ukázka kódu](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs) je k dispozici na GitHubu.

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

Sloupec je objekt JSON s informacemi o implicitní, `autosuggested`a explicitní `usersuggested` zpětné vazbě. `SuggestedQuestions` Příkladem tohoto objektu JSON pro jednu otázku `help` odeslanou uživatelem je:

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

Můžete také použít rozhraní API pro stažení změn ke kontrole těchto změn pomocí REST nebo jakékoli sady SDK založené na jazyku:
* [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fc)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.alterationsextensions.getasync?view=azure-dotnet)


Když znovu naimportujete tuto aplikaci, aktivní učení nadále shromažďuje informace a doporučuje návrhy vaší znalostní báze. 



## <a name="best-practices"></a>Osvědčené postupy

Osvědčené postupy při používání aktivního učení najdete v tématu [osvědčené postupy](../Concepts/best-practices.md#active-learning).

## <a name="next-steps"></a>Další kroky
 
> [!div class="nextstepaction"]
> [Použití metadat s rozhraním API GenerateAnswer](metadata-generateanswer-usage.md)
