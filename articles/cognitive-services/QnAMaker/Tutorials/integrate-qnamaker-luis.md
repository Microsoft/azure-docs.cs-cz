---
title: Integrace LUIS a Qnamakerem – bot
titleSuffix: Azure Cognitive Services
description: Vzhledem k tomu, že váš QnA Maker znalostní báze roste, je obtížné ji udržovat jako jednu monolitické sadu a je potřeba rozdělit znalostní bázi do menších logických bloků dat.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 09/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 7e1ea234bde96ce84259841bbc592bf6373bc639
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802797"
---
# <a name="use-bot-with-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>Použití robota s QnA Maker a LUIS k distribuci znalostní báze
Vzhledem k tomu, že váš QnA Maker znalostní báze roste, je obtížné ji udržovat jako jednu monolitické sadu a je potřeba rozdělit znalostní bázi do menších logických bloků dat.

I když je jednoduché vytvořit v QnA Maker více znalostní báze, budete potřebovat určitou logiku pro směrování příchozí otázky do příslušné znalostní báze. To můžete provést pomocí LUIS.

Tento článek používá sadu robot Framework V3 SDK. Pokud vás zajímá verze sady bot Framework v4 SDK této informace, podívejte se prosím na tento [článek o rozhraní bot Framework](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csharp).

## <a name="architecture"></a>Architektura

![QnA Maker s architekturou Language Understanding](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

Ve výše uvedeném scénáři QnA Maker nejprve získá záměr příchozí otázky z modelu LUIS a pak ji použije ke směrování do správné QnA Maker znalostní báze.

## <a name="create-a-luis-app"></a>Vytvoření aplikace v LUIS

1. Přihlaste se k portálu [Luis](https://www.luis.ai/) .
1. [Vytvořte aplikaci](https://docs.microsoft.com/azure/cognitive-services/luis/create-new-app).
1. [Přidejte záměr](https://docs.microsoft.com/azure/cognitive-services/luis/add-intents) pro každou QnA maker znalostní bázi Knowledge Base. Vzorový projevy by měl odpovídat otázkám v QnA Maker znalostní báze.
1. [Školení aplikace Luis](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) a [publikování aplikace LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/publishapp) vaše aplikace Luis
1. V části **Správa** si poznamenejte ID aplikace Luis, klíč koncového bodu Luis a [název vlastní domény](../../cognitive-services-custom-subdomains.md). Tyto hodnoty budete potřebovat později. 

## <a name="create-qna-maker-knowledge-bases"></a>Vytváření QnA Maker báze znalostí

1. Přihlaste se k [QnA maker](https://qnamaker.ai).
1. Pro každý záměr v aplikaci LUIS [vytvořte](https://www.qnamaker.ai/Create) základy znalostní báze.
1. Testování a publikování znalostní báze. Při publikování jednotlivých KB si poznamenejte ID KB, název prostředku (vlastní subdoménu před _. azurewebsites.NET/qnamaker_) a klíč koncového bodu autorizace. Tyto hodnoty budete potřebovat později. 

    Tento článek předpokládá, že aktualizací KB jsou všechny vytvořené ve stejném předplatném Azure QnA Maker.

    ![QnA Maker požadavek HTTP](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

## <a name="web-app-bot"></a>Robot webové aplikace

1. [Vytvořte robot "základní" Web App](https://docs.microsoft.com/azure/bot-service/bot-service-quickstart?view=azure-bot-service-4.0) , který automaticky obsahuje aplikaci Luis. Vyberte C# programovací jazyk.

1. Po vytvoření bot webové aplikace v Azure Portal vyberte bot webové aplikace.
1. V navigaci služby webové aplikace bot vyberte **nastavení aplikace** a pak přejděte dolů k části **nastavení aplikace** v části dostupná nastavení.
1. Změňte **LuisAppId** na hodnotu aplikace Luis vytvořené v předchozí části a pak vyberte **Uložit**.


## <a name="change-code-in-basicluisdialogcs"></a>Změna kódu v BasicLuisDialog.cs
1. V části **Správa bot** v navigaci bot webové aplikace v Azure Portal vyberte **sestavení**.
2. Vyberte **otevřít online editor kódu**. Otevře se nová karta prohlížeče s online editačním prostředím. 
3. V části **WWWROOT** vyberte adresář **dialogy** a pak otevřete **BasicLuisDialog.cs**.
4. Přidejte závislosti na začátek souboru **BasicLuisDialog.cs** :

    ```csharp
    using System;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Bot.Builder.Luis;
    using Microsoft.Bot.Builder.Luis.Models;
    using Newtonsoft.Json;
    using System.Text;
    ```

5. Přidejte následující třídy k deserializaci QnA Maker odpovědi:

    ```csharp
    public class Metadata
    {
        public string name { get; set; }
        public string value { get; set; }
    }

    public class Answer
    {
        public IList<string> questions { get; set; }
        public string answer { get; set; }
        public double score { get; set; }
        public int id { get; set; }
        public string source { get; set; }
        public IList<object> keywords { get; set; }
        public IList<Metadata> metadata { get; set; }
    }

    public class QnAAnswer
    {
        public IList<Answer> answers { get; set; }
    }
    ```


6. Přidejte následující třídu, která provede požadavek HTTP na službu QnA Maker. Všimněte si, že hodnota **autorizační** hlavičky obsahuje slovo, `EndpointKey` mezerou za slovem. Výsledek JSON je deserializovaný na předchozí třídy a vrátí se první odpověď.

    ```csharp
    [Serializable]
    public class QnAMakerService
    {
        private string qnaServiceResourceName;
        private string knowledgeBaseId;
        private string endpointKey;

        public QnAMakerService(string resourceName, string kbId, string endpointkey)
        {
            qnaServiceResourceName = resourceName;
            knowledgeBaseId = kbId;
            endpointKey = endpointkey;

        }
        async Task<string> Post(string uri, string body)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(body, Encoding.UTF8, "application/json");
                request.Headers.Add("Authorization", "EndpointKey " + endpointKey);

                var response = await client.SendAsync(request);
                return  await response.Content.ReadAsStringAsync();
            }
        }
        public async Task<string> GetAnswer(string question)
        {
            string uri = qnaServiceResourceName + "/qnamaker/knowledgebases/" + knowledgeBaseId + "/generateAnswer";
            string questionJSON = "{\"question\": \"" + question.Replace("\"","'") +  "\"}";

            var response = await Post(uri, questionJSON);

            var answers = JsonConvert.DeserializeObject<QnAAnswer>(response);
            if (answers.answers.Count > 0)
            {
                return answers.answers[0].answer;
            }
            else
            {
                return "No good match found.";
            }
        }
    }
    ```


7. Upravte třídu BasicLuisDialog. Každý záměr LUIS by měl mít metodu dekorované pomocí **LuisIntent**. Parametr dekorace je skutečný název záměru LUIS. Název metody, který je upravený, _by měl_ být Luis názvem záměru pro čitelnost a udržovatelnost, ale nemusí být stejný v návrhu nebo v době běhu.  

    ```csharp
    [Serializable]
    public class BasicLuisDialog : LuisDialog<object>
    {
        // LUIS Settings
        static string LUIS_appId = "<LUIS APP ID>";
        static string LUIS_apiKey = "<LUIS API KEY>";
        static string LUIS_hostRegion = "westus.api.cognitive.microsoft.com";

        // QnA Maker global settings
        // assumes all KBs are created with same Azure service
        static string qnamaker_endpointKey = "<QnA Maker endpoint KEY>";
        static string qnamaker_resourceName = "my-qnamaker-s0-s";
        
        // QnA Maker Human Resources Knowledge base
        static string HR_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // QnA Maker Finance Knowledge base
        static string Finance_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // Instantiate the knowledge bases
        public QnAMakerService hrQnAService = new QnAMakerService("https://" + qnamaker_resourceName + ".azurewebsites.net", HR_kbID, qnamaker_endpointKey);
        public QnAMakerService financeQnAService = new QnAMakerService("https://" + qnamaker_resourceName + ".azurewebsites.net", Finance_kbID, qnamaker_endpointKey);

        public BasicLuisDialog() : base(new LuisService(new LuisModelAttribute(
            LUIS_appId,
            LUIS_apiKey,
            domain: LUIS_hostRegion)))
        {
        }

        [LuisIntent("None")]
        public async Task NoneIntent(IDialogContext context, LuisResult result)
        {
            HttpClient client = new HttpClient();
            await this.ShowLuisResult(context, result);
        }

        // HR Intent
        [LuisIntent("HR")]
        public async Task HumanResourcesIntent(IDialogContext context, LuisResult result)
        {
            // Ask the HR knowledge base
            var qnaMakerAnswer = await hrQnAService.GetAnswer(result.Query);
            await context.PostAsync($"{qnaMakerAnswer}");
            context.Wait(MessageReceived);
        }

        // Finance intent
        [LuisIntent("Finance")]
        public async Task FinanceIntent(IDialogContext context, LuisResult result)
        {
            // Ask the finance knowledge base
            var qnaMakerAnswer = await financeQnAService.GetAnswer(result.Query);
            await context.PostAsync($"{qnaMakerAnswer}");
            context.Wait(MessageReceived);
        }
        private async Task ShowLuisResult(IDialogContext context, LuisResult result)
        {
            await context.PostAsync($"You have reached {result.Intents[0].Intent}. You said: {result.Query}");
            context.Wait(MessageReceived);
        }
    }
    ```


## <a name="build-the-bot"></a>Sestavte robota
1. V editoru kódu klikněte pravým tlačítkem na `build.cmd` a vyberte **Spustit z konzoly**.

    ![spustit z konzoly](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. Zobrazení kódu je nahrazeno oknem terminálu zobrazujícím průběh a výsledky sestavení.

    ![sestavení konzoly](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>Test robota
V Azure Portal pro otestování robotu vyberte **test na webovém chatu** . Zadejte zprávy z různých záměrů, abyste získali odpověď od příslušné znalostní báze.

![Test webového chatu](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření plánu provozní kontinuity pro QnA Maker](../How-To/business-continuity-plan.md)
