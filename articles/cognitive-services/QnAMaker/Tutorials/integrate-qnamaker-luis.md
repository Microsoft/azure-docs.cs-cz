---
title: Služba LUIS a QnA maker – integrace robota
titleSuffix: Azure Cognitive Services
description: Jak QnA Maker znalostní báze případu dostatečného růstu, bude obtížné spravovat jako jeden monolitické nastavit a je potřeba rozdělit do menších logické bloků ve znalostní bázi.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/28/2018
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: dd030eeefd731ae176e1fe7907d941fff56a9db1
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53080023"
---
# <a name="integrate-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>Integrace nástroje QnA Maker a LUIS distribuovat znalostní báze
Jak QnA Maker znalostní báze případu dostatečného růstu, bude obtížné spravovat jako jeden monolitické nastavit a je potřeba rozdělit do menších logické bloků ve znalostní bázi.

I když je jednoduché vytvářet více znalostních bází v nástroje QnA Maker, budete potřebovat nějaké logiky směrovat příchozí dotaz do odpovídající znalostní báze. Můžete to provést s využitím služby LUIS.

Tento článek používá sadu SDK v3 Bot Framework. Přečtěte si tento [Bot Framework článku](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csharp), pokud vás zajímá verze sady SDK rozhraní Bot Framework v4 těchto informací.

## <a name="architecture"></a>Architektura

![Architektura služby luis QnA Maker](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

Ve výše popsaném scénáři QnA Maker nejprve získá záměr příchozí dotaz z modelu LUIS a použije ji směrovat do znalostní báze správný nástroj QnA Maker.

## <a name="create-a-luis-app"></a>Vytvoření aplikace LUIS

1. Přihlaste se k [LUIS](https://www.luis.ai/) portálu.
1. [Vytvoření aplikace](https://docs.microsoft.com/azure/cognitive-services/luis/create-new-app).
1. [Přidat záměru](https://docs.microsoft.com/azure/cognitive-services/luis/add-intents) pro každý nástroj QnA Maker znalostní báze. Příklad projevy, musí odpovídat na otázky v znalostních bází QnA Maker.
1. [Trénování aplikace LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) a [publikovat aplikaci LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/publishapp) aplikace LUIS.
1. V **spravovat** části, poznamenejte si vaše LUIS ID aplikace, klíč koncového bodu služby LUIS a hostujte oblasti. Tyto hodnoty budete potřebovat později. 

## <a name="create-qna-maker-knowledge-bases"></a>Vytváření znalostních bází QnA Maker

1. Přihlaste se k [QnA Maker](https://qnamaker.ai).
1. [Vytvoření](https://www.qnamaker.ai/Create) znalostních bází pro každý záměr v aplikaci LUIS.
1. Otestujeme a publikujeme a znalostních bází. Když budete publikovat každý KB, poznamenejte si ID znalostní BÁZE, hostování (subdoména před _.azurewebsites.net/qnamaker_) a klíče koncového bodu autorizace. Tyto hodnoty budete potřebovat později. 

    Tento článek předpokládá, že že znalostní báze se vytvoří ve stejném předplatném Azure QnA Maker.

    ![Požadavek QnA Maker HTTP](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

## <a name="web-app-bot"></a>Web app Bot

1. [Vytvořte Web App bot](https://docs.microsoft.com/azure/cognitive-services/luis/luis-csharp-tutorial-build-bot-framework-sample) pomocí šablony služby LUIS. Vyberte 3.x sady SDK a programovací jazyk C#.

1. Po vytvoření použijete web app bot, na webu Azure Portal, vyberte použijete web app bot.
1. Vyberte **nastavení aplikace** v navigačním služby Web app bot, poté přejděte dolů k **nastavení aplikace** část dostupných nastaveních.
1. Změnit **LuisAppId** hodnotu LUIS aplikaci vytvořenou v předchozí části pak vyberte **Uložit**.


## <a name="change-code-in-basicluisdialogcs"></a>Změna kódu v BasicLuisDialog.cs
1. Z **Bot správu** části navigační web app bot na webu Azure Portal, vyberte **sestavení**.
2. Vyberte **otevřít online editor kódu**. Otevře se nová karta prohlížeče s online prostředí pro úpravy. 
3. V **WWWROOT** vyberte **dialogová okna** adresář a potom otevřete **BasicLuisDialog.cs**.
4. Přidat závislosti do horní části **BasicLuisDialog.cs** souboru:

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

5. Přidat následující třídy k deserializaci odpovědí nástroje QnA Maker:

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


6. Přidejte následující třídu tak, aby služba QnA Maker požadavek HTTP. Všimněte si, že **autorizace** hodnotu hlavičky obsahuje slovo, `EndpointKey` mezerou následující slovo. Výsledek JSON je deserializovat do tříd předchozí a vrátil první odpovědi.

    ```csharp
    [Serializable]
    public class QnAMakerService
    {
        private string qnaServiceHostName;
        private string knowledgeBaseId;
        private string endpointKey;

        public QnAMakerService(string hostName, string kbId, string endpointkey)
        {
            qnaServiceHostName = hostName;
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
            string uri = qnaServiceHostName + "/qnamaker/knowledgebases/" + knowledgeBaseId + "/generateAnswer";
            string questionJSON = @"{'question': '" + question + "'}";

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


7. Změna BasicLuisDialog třídy. Každá služba LUIS záměr by měl mít metody upravené pomocí **LuisIntent**. Parametr dekorativní je skutečný název služby LUIS záměru. Název metody, která je upravena _by měl_ LUIS záměru název pro lepší čitelnost a udržovatelnosti, ale nemusí být stejná na návrh nebo běhu.  

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
        static string qnamaker_endpointDomain = "my-qnamaker-s0-s";
        
        // QnA Maker Human Resources Knowledge base
        static string HR_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // QnA Maker Finance Knowledge base
        static string Finance_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // Instantiate the knowledge bases
        public QnAMakerService hrQnAService = new QnAMakerService("https://" + qnamaker_endpointDomain + ".azurewebsites.net", HR_kbID, qnamaker_endpointKey);
        public QnAMakerService financeQnAService = new QnAMakerService("https://" + qnamaker_endpointDomain + ".azurewebsites.net", Finance_kbID, qnamaker_endpointKey);

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


## <a name="build-the-bot"></a>Sestavit robota
1. V editoru kódu, klikněte pravým tlačítkem na `build.cmd` a vyberte **spustit z konzoly**.

    ![spustit z konzoly](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. Zobrazení kódu se nahradí okno terminálu znázorňující průběh a výsledky sestavení.

    ![sestavení konzoly](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>Testování robota
Na webu Azure Portal, vyberte **testování ve Web Chat** otestovat robota. Typ zprávy z různých záměrů pro získání odpovědi z odpovídající znalostní báze.

![Webový test chatu](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvořit plán obchodní kontinuity podnikových procesů pro nástroj QnA Maker](../How-To/business-continuity-plan.md)
