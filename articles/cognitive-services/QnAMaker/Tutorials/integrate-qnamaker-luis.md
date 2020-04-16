---
title: LUIS a QnAMaker - integrace botů
titleSuffix: Azure Cognitive Services
description: Jak se vaše znalostní báze QnA Maker zvětšuje, je obtížné ji udržovat jako jednu monolitickou sadu. Rozdělte znalostní bázi na menší logické bloky.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 09/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b0d28c77966668f919cdf1265f8cc63b4931d5fd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402721"
---
# <a name="use-a-bot-with-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>Distribuce znalostní báze pomocí robota s QnA Makerem a LUIS
Jak se vaše znalostní báze QnA Maker zvětšuje, je obtížné ji udržovat jako jednu monolitickou sadu. Rozdělte znalostní bázi na menší logické bloky.

I když je jednoduché vytvořit více znalostních bází v QnA Makeru, budete potřebovat určitou logiku pro směrování příchozí otázky do příslušné znalostní báze. Můžete to provést pomocí LUIS.

Tento článek používá bot framework v3 SDK. Pokud máte zájem o bot framework u4 SDK verze těchto informací, najdete v tématu [použití více modelů LUIS a QnA](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csharp).

## <a name="architecture"></a>Architektura

![Grafika znázorňující architekturu QnA Makeru s jazykovým porozuměním](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

Předchozí obrázek ukazuje, že QnA Maker nejprve získá záměr příchozí otázku z modelu LUIS. QnA Maker pak použije tento záměr k směrování otázky do správné znalostní báze QnA Maker.

## <a name="create-a-luis-app"></a>Vytvoření aplikace LUIS

1. Přihlaste se k portálu [LUIS.](https://www.luis.ai/)
1. [Vytvořte aplikaci](https://docs.microsoft.com/azure/cognitive-services/luis/create-new-app).
1. [Přidejte záměr](https://docs.microsoft.com/azure/cognitive-services/luis/add-intents) pro každou znalostní bázi QnA Maker. Příklad projevy by měly odpovídat otázky ve znalostních bází QnA Maker.
1. [Trénování aplikace LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) a [publikování aplikace LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/publishapp).
1. V části **Spravovat** poznamenejte si ID aplikace LUIS, klíč koncového bodu LUIS a [vlastní název domény](../../cognitive-services-custom-subdomains.md). Tyto hodnoty budete potřebovat později.

## <a name="create-qna-maker-knowledge-bases"></a>Vytvoření znalostních bází QnA Maker

1. Přihlaste se ke [qna makeru](https://qnamaker.ai).
1. [Vytvořte](https://www.qnamaker.ai/Create) znalostní bázi pro každý záměr v aplikaci LUIS.
1. Otestujte a publikujte znalostní báze. Při publikování každého z nich si poznamenejte ID, název prostředku (vlastní subdoménu před _.azurewebsites.net/qnamaker_) a klíč koncového bodu autorizace. Tyto hodnoty budete potřebovat později.

    Tento článek předpokládá, že všechny znalostní báze jsou vytvořeny ve stejném předplatném Azure QnA Maker.

    ![Snímek obrazovky s požadavkem HTTP qnA makeru](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

## <a name="web-app-bot"></a>Robot webové aplikace

1. [Vytvořte "Základní" bot se službou Azure Bot Service](https://docs.microsoft.com/azure/bot-service/bot-service-quickstart?view=azure-bot-service-4.0), která automaticky zahrnuje aplikaci LUIS. Vyberte programovací jazyk C#.

1. Po vytvoření robota webové aplikace vyberte na webu Azure Portal robota webové aplikace.
1. V navigaci robota webových aplikací vyberte **Nastavení aplikace**. Potom přejděte dolů k části **Nastavení aplikace** v dostupných nastaveních.
1. Změňte **LuisAppId** na hodnotu aplikace LUIS vytvořené v předchozí části. Potom vyberte **Uložit**.


## <a name="change-the-code-in-the-basicluisdialogcs-file"></a>Změna kódu v souboru BasicLuisDialog.cs
1. V části **Správa botů** navigace robota webové aplikace na webu Azure Portal vyberte **Build**.
2. Vyberte **Otevřít online editor kódu**. Otevře se nová karta prohlížeče v prostředí pro úpravy online.
3. V části **WWWROOT** vyberte adresář **Dialogs** a otevřete **BasicLuisDialog.cs**.
4. Přidejte závislosti na začátek **BasicLuisDialog.cs** souboru:

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

5. Přidejte následující třídy pro rekonstrukci odpovědi QnA Maker:

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


6. Přidejte následující třídu, abyste vytvořili požadavek HTTP do služby QnA Maker. Všimněte si, že hodnota hlavičky `EndpointKey` **autorizace** obsahuje slovo , s mezerou za slovem. Výsledek JSON je deserializován do předchozích tříd a je vrácena první odpověď.

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


7. Upravte `BasicLuisDialog` třídu. Každý záměr SLUŽBY LUIS by měl mít metodu **dekorovoci LuisIntent**. Parametr dekorace je skutečný název záměru LUIS. Název metody, který je dekorován _by měl_ být luis záměr název pro čitelnost a udržovatelnost, ale nemusí být stejné v návrhu nebo runtime.

    ```csharp
    [Serializable]
    public class BasicLuisDialog : LuisDialog<object>
    {
        // LUIS Settings
        static string LUIS_appId = "<LUIS APP ID>";
        static string LUIS_apiKey = "<LUIS API KEY>";
        static string LUIS_hostRegion = "westus.api.cognitive.microsoft.com";

        // QnA Maker global settings
        // assumes all knowledge bases are created with same Azure service
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


## <a name="build-the-bot"></a>Sestavte bot
1. V editoru kódu klepněte pravým tlačítkem myši na **soubor build.cmd**a vyberte **příkaz Spustit z konzoly**.

    ![Snímek obrazovky s možností Spustit z konzoly v editoru kódu](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. Zobrazení kódu je nahrazeno terminálovým oknem, které zobrazuje průběh a výsledky sestavení.

    ![Snímek obrazovky se stavbou konzoly](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>Otestujte bot
Na webu Azure Portal vyberte **Testovat ve webovém chatu** a otestujte robota. Zadejte zprávy z různých záměrů získat odpověď z odpovídající znalostní báze.

![Snímek obrazovky testu webového chatu](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Integrace znalostní báze s agentem ve virtuálních agentech power](integrate-with-power-virtual-assistant-fallback-topic.md)
