---
title: Zlepšení znalostní báze – QnA Maker
titleSuffix: Azure Cognitive Services
description: Můžete aktivně učit a zlepšovat tak kvalitu znalostní báze Navrhněte alternativní dotazy založené na příspěvky uživatele, na vaše otázky a odpovědi spárování. Buď je přidáte do stávající dotazy nebo odmítat je zkontrolujete tyto návrhy. Znalostní báze se nezmění automaticky. Je nutné přijmout návrhy pro všechny změny se projeví. Tyto návrhy přidat dotazy, ale nechcete změnit nebo odebrat existující dotazy.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/06/2019
ms.author: diberry
ms.openlocfilehash: f8d2f6d9fce6a249a782f959ac7672ac8e123fbc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075164"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>Aktivní studium slouží ke zlepšení znalostní báze

Můžete aktivně učit a zlepšovat tak kvalitu znalostní báze Navrhněte alternativní dotazy založené na příspěvky uživatele, na vaše otázky a odpovědi spárování. Buď je přidáte do stávající dotazy nebo odmítat je zkontrolujete tyto návrhy. 

Znalostní báze se nezmění automaticky. Aby změny projevily je nutné přijmout návrhy. Tyto návrhy přidat dotazy, ale nechcete změnit nebo odebrat existující dotazy.

## <a name="what-is-active-learning"></a>Co je aktivní učení?

Nástroj QnA Maker se učí novou otázku variace implicitní a explicitní odeslání zpětné vazby.
 
* [Implicitní zpětnou vazbu](#how-qna-makers-implicit-feedback-works) – klasifikátor znalost, kdy otázku uživatele obsahuje více odpovědi s skóre, která jsou velmi podobné a to považuje za zpětnou vazbu. Nemusíte dělat nic pro tuto akci.
* [Explicitní zpětnou vazbu](#how-you-give-explicit-feedback-with-the-train-api) – když je více odpovědi s malou kolísání skóre, které jsou vráceny z ve znalostní bázi, klientská aplikace žádá uživatele, které otázka je správné otázky. QnA Maker se posílá explicitní zpětnou vazbu uživatelů [trénování API](#train-api). 

Obě metody poskytují klasifikátor s podobné dotazy, které jsou v clusteru.

## <a name="how-active-learning-works"></a>Jak active learning funguje

Aktivní učení se aktivuje podle skóre nejčastější několik odpovědi vrácené QnA Maker. Pokud skóre rozdíly ležet v rozsahu malé, potom dotaz považuje pro všechny možné páry QnA návrh je to možné (jako alternativní dotaz). Když to potvrdíte navrhované otázky pro konkrétní dvojici QnA, byl odmítnut pro ostatní kombinace. Je potřeba si vzpomenout na Uložit a jejich trénování, po přijetí návrhy.

Aktivní učení nabízí nejlepší možný návrhů v případech, kde se zobrazuje koncové body přiměřené množství a různorodost dotazů na využití. Když jsou Clusterované 5 nebo další podobné dotazy, každých 30 minut, nástroj QnA Maker navrhuje dotazy založené na uživatelích do znalostní báze knowledge base návrháře přijmout nebo odmítnout. Všechny návrhy jsou Clusterované společně odpovědným a nejčastějších návrhů pro alternativní otázky se zobrazují na základě četnosti konkrétní dotazů koncovými uživateli.

Jakmile dotazy jsou navržené v portál QnA Maker, budete muset zkontrolovat a přijmout nebo odmítnout tyto návrhy. Není k dispozici rozhraní API pro správu návrhy.

## <a name="how-qna-makers-implicit-feedback-works"></a>Jak funguje nástroj QnA Maker implicitní zpětné vazby

Nástroj QnA Maker implicitní zpětná vazba používá algoritmus k určení skóre vzdálenost, ujistěte se, active learning návrhy. Algoritmů a určit blízkých výrazů není jednoduchý výpočet. Rozsahy v následujícím příkladu se nepředpokládá, že se pevná ale by měla sloužit jako vodítko k pochopení dopadu pouze algoritmus.

Při vysoce jistí, jako je například 80 % na otázku skóre jsou široké, přibližně v rámci 10 % rozsahu skóre, které jsou považovány za pro aktivní učení. Podle skóre spolehlivosti sníží, jako je například 40 % rozsahu skóre, které se také snižuje přibližně v rámci 4 %. 

## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>Jak udělit explicitní zpětné vazby pomocí rozhraní API pro trénování

Je důležité, že nástroj QnA Maker získá explicitní zpětnou vazbu o tom, které odpovědi byla nejlepší odpověď. Jak určit nejlepší odpověď je na vás a mohou zahrnovat:

* Zpětné vazby uživatelů, když vyberete některý z odpovědi.
* Obchodní logiku, jako je například určení přijatelný rozsah skóre.  
* Kombinace obou uživatelů zpětnou vazbu a obchodní logiky.

## <a name="upgrade-your-runtime-version-to-use-active-learning"></a>Upgrade verze modulu runtime pro použití aktivního učení

Aktivní učení se podporuje v modulu runtime verze 4.4.0 a vyšší. Pokud se znalostní báze vytvořil v dřívější verzi, [upgrade vašeho prostředí runtime](troubleshooting-runtime.md#how-to-get-latest-qnamaker-runtime-updates) pro použití této funkce. 

## <a name="turn-on-active-learning-to-see-suggestions"></a>Zapnout active learning návrhy

Aktivní učení je vypnuto ve výchozím nastavení. Zapněte navrhované dotazy. Po zapnutí active learning, budete muset odeslat informace z klientskou aplikaci nástroje QnA Maker. Další informace najdete v tématu [architektury toku pro používání rozhraní API pro trénování a GenerateAnswer z robota](#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot).

1. Vyberte **publikovat** publikovat znalostní báze. Aktivní učení dotazy jsou shromážděná z GenerateAnswer předpovědi koncový bod rozhraní API pouze. Dotazy na testovací podokno portálu QnA Maker nemají vliv na aktivní učení.

1. Chcete-li aktivní učení na platformě portálu QnA Maker, přejděte na pravém horním rohu vyberte váš **název**, přejděte na stránku [ **nastavení služby**](https://www.qnamaker.ai/UserSettings).  

    ![Zapněte aktivně učit alternativy navrhované otázky ze stránky nastavení služby. Vyberte své uživatelské jméno v pravé horní nabídce a potom vyberte nastavení služby.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Najít službu QnA Maker, a potom přepnout **aktivně učit**. 

    [![Na stránce nastavení služby zapněte funkci aktivně učit. Pokud nejste schopni Přepnout funkci, budete muset upgradovat vaší služby.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    Jednou **aktivně učit** je povoleno, znalostní báze navrhuje nové otázky v pravidelných intervalech podle uživatel odeslal dotazy. Můžete zakázat **aktivně učit** přepnutím nastavení znovu.

## <a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>Přijměte návrh aktivně učit znalostní báze knowledge base

1. Chcete-li zobrazit navržené otázky, na **upravit** znalostní báze stránce **možnosti zobrazení**a pak vyberte **zobrazovat návrhy aktivně učit**. 

    [![V části upravit na portálu vyberte Zobrazit návrhy, chcete-li zobrazit aktivně učit nový dotaz alternativy.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. Filtrovat ve znalostní bázi s páry otázek a odpovědí zobrazit pouze návrhy tak, že vyberete **filtrovat podle návrhy**.

    [![Chcete-li zobrazit pouze aktivně učit alternativy navrhované otázky, použijte filtr přepnutím návrhy.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. Každý pár QnA navrhuje novou otázku alternativy zaškrtnutí, `✔` , tak, aby přijímal otázku nebo `x` odmítnout návrhy. Vyberte zaškrtávací políčko, chcete-li přidat otázku. 

    [![Vyberte nebo odmítnout aktivně učit navrhované otázky alternativy tak, že vyberete red odstranění značky nebo zelená značka zaškrtnutí.](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    Můžete přidat nebo odstranit _všechny návrhy_ tak, že vyberete **přidejte všechny** nebo **odmítnout všechny** kontextové panelu nástrojů.

1. Vyberte **uložit a jejich trénování** a uložte změny do znalostní báze.

1. Vyberte **publikovat** k povolení změn, které chcete mít k dispozici na [GenerateAnswer API](metadata-generateanswer-usage.md#generateanswer-request-configuration).

    Když jsou Clusterované 5 nebo další podobné dotazy, každých 30 minut, QnA Maker navrhuje alternativní otázky můžete přijmout nebo odmítnout.


<a name="#score-proximity-between-knowledge-base-questions"></a>

### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>Architektury toku pro používání rozhraní API pro trénování a GenerateAnswer z robota

Robota nebo jiné klientské aplikace používejte následující architektury postup pro použití aktivního učení:

* Bot [získá odpověď ze znalostní báze](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers) s rozhraním API GenerateAnswer pomocí `top` vlastnost zobrazíte počet odpovědí.
* Bot určuje explicitní zpětnou vazbu:
    * Použít vlastní [vlastní obchodní logiky](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user), odfiltrovat nízké skóre.
    * V bot nebo klientskou aplikaci zobrazte seznam možných odpovědí pro uživatele a získat odpověď vybraného uživatele.
* Bot [odešle vybraný odpovědí zpět do nástroje QnA Maker](#bot-framework-sample-code) s [trénování API](#train-api).


### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>Vlastnost horní v požadavku GenerateAnswer několik odpovídajících odpovědi

Při odesílání dotaz do nástroje QnA Maker na odpověď `top` vlastnost datové části JSON nastaví dobu v odpovědi vrátit. 

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>Použijte vlastnost skóre spolu s obchodní logikou zobrazíte seznam odpovědi na Zobrazit uživatele

Když klientská aplikace (jako je chatovací robot) obdrží odpověď, jsou vráceny nejdůležitější otázky týkající se 3. Použití `score` vlastnost k analýze vzdálenost mezi skóre. Tento rozsah blízkosti se určuje podle vlastní obchodní logikou. 

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

## <a name="client-application-follow-up-when-questions-have-similar-scores"></a>Klient aplikace zpracování při otázky mají podobné skóre

Klientské aplikace zobrazí otázky pro uživatele, vyberte možnost _jeden dotaz_ , který nejlépe reprezentuje svůj záměr. 

Když uživatel vybere jednu z existujících otázek, klientská aplikace odešle jako zpětné vazby pomocí rozhraní API nástroje QnA Maker trénování podle volby uživatele. Tato zpětná vazba se dokončí active learning smyčku zpětné vazby. 

## <a name="train-api"></a>Rozhraní API trénování

Se požadavek POST trénovat na základě modelu rozhraní API nástroje QnA Maker zpětnou vazbu aktivně učit neposílají. Podpis rozhraní API je:

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|Vlastnost požadavku HTTP|Název|Type|Účel|
|--|--|--|--|
|Parametr trasa adresy URL|ID znalostní báze|string|Identifikátor GUID pro znalostní báze.|
|Host subdomain|Název prostředku QnA maker|string|Název hostitele pro váš nástroj QnA Maker ve vašem předplatném Azure. Toto je k dispozici na stránce nastavení po publikování znalostní báze. |
|Záhlaví|Content-Type|string|Typ média textu odeslaného do rozhraní API. Výchozí hodnota je: `application/json`|
|Záhlaví|Autorizace|string|Klíče vašeho koncového bodu (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Tělo POST|JSON – objekt|JSON|Zpětná vazba školení|

Text JSON má několik nastavení:

|Vlastnost text JSON|Type|Účel|
|--|--|--|--|
|`feedbackRecords`|pole|Seznam zpětnou vazbu.|
|`userId`|string|ID uživatele osoby, která přijímá navržené otázky. Formát ID uživatele je na vás. Například e-mailová adresa může být platné ID uživatele ve vaší architektuře. Volitelné.|
|`userQuestion`|string|Přesný text otázky. Povinná hodnota.|
|`qnaID`|číslo|ID otázku, součástí [GenerateAnswer odpovědi](metadata-generateanswer-usage.md#generateanswer-response-properties). |

Příklad text JSON vypadá takto:

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

Úspěšná odpověď vrátí stav 204 a bez těla odpovědi JSON. 

<a name="active-learning-is-saved-in-the-exported-apps-tsv-file"></a>

## <a name="bot-framework-sample-code"></a>Bot framework ukázkový kód

Váš bot framework kód potřebuje volat rozhraní API pro trénování, pokud se dotaz uživatele má používat pro aktivní učení. Existují dva druhy kódu k zápisu:

* Pokud dotaz by měl sloužící k aktivní učení
* Odesílat dotaz zpět do nástroje QnA Maker trénovat na základě modelu rozhraní API pro aktivní učení

V [ukázka Azure Bot](https://aka.ms/activelearningsamplebot), obě tyto činnosti naprogramovat. 

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>Příklad C# kód pro trénování API s využitím rozhraní Bot Framework 4.x

Následující kód ukazuje, jak odesílat informace nástroje QnA Maker pomocí rozhraní API pro trénování. To [kompletní ukázku kódu](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-activelearning/csharp_dotnetcore) je k dispozici na Githubu.

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

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>Ukázkový kód Node.js pro trénování API s využitím rozhraní Bot Framework 4.x 

Následující kód ukazuje, jak odesílat informace nástroje QnA Maker pomocí rozhraní API pro trénování. To [kompletní ukázku kódu](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs) je k dispozici na Githubu.

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

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>Aktivní učení se uloží exportované znalostní báze

Když se vaše aplikace může aktivně učit povolené a exportovat do aplikace `SuggestedQuestions` sloupec v souboru tsv uchovává data aktivně učit. 

`SuggestedQuestions` Sloupec je objekt JSON informací o implicitní, `autosuggested`a explicitní, `usersuggested` zpětnou vazbu. Příkladem tohoto objektu JSON pro jeden uživatel odeslal otázku `help` je:

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

Když tuto aplikaci znovu naimportujete, active learning i nadále shromažďovat informace a doporučení návrhy pro znalostní báze. 

## <a name="best-practices"></a>Osvědčené postupy

Osvědčené postupy při používání active learning, naleznete v tématu [osvědčené postupy](../Concepts/best-practices.md#active-learning).

## <a name="next-steps"></a>Další postup
 
> [!div class="nextstepaction"]
> [Pomocí rozhraní API GenerateAnswer metadat](metadata-generateanswer-usage.md)
