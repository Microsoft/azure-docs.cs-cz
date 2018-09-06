---
title: Integrace nástroje QnA Maker a LUIS – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: podrobný návod k integraci nástroje QnA Maker a LUIS
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: 18eae69867dc9774f63b11c762b22df4595bdce6
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43781743"
---
# <a name="integrate-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>Integrace nástroje QnA Maker a LUIS distribuovat znalostní báze
Jak QnA Maker znalostní báze případu dostatečného růstu, bude obtížné spravovat jako jeden monolitické nastavit a je potřeba rozdělit do menších logické bloků ve znalostní bázi.

I když je jednoduché vytvářet více znalostních bází v nástroje QnA Maker, budete potřebovat nějaké logiky směrovat příchozí dotaz do odpovídající znalostní báze. Můžete to provést s využitím služby LUIS.

## <a name="architecture"></a>Architektura

![Architektura služby luis QnA Maker](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

Ve výše popsaném scénáři QnA Maker nejprve získá záměr příchozí dotaz z modelu LUIS a použije ji směrovat do znalostní báze správný nástroj QnA Maker.

## <a name="prerequisites"></a>Požadavky
- Přihlaste se k [LUIS](https://www.luis.ai/) portál a [vytvořit aplikaci](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/create-new-app).
- [Přidání záměrů](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/add-intents) podle váš scénář.
- [Trénování](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-how-to-train) a [publikovat](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/publishapp) aplikace LUIS.
- Přihlaste se k [QnA Maker](https://qnamaker.ai) a [vytvořit](https://www.qnamaker.ai/Create) znalostních bází v závislosti na vašem scénáři.
- Otestujeme a publikujeme a znalostních bází.

## <a name="qna-maker--luis-bot"></a>Nástroj QnA Maker + LUIS Bot
1. První vytváření robotů webové aplikace pomocí šablony služby LUIS, ho propojit s aplikaci LUIS, kterou jste vytvořili výše a úprava příkazů. Podrobný postup najdete [tady](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-csharp-tutorial-build-bot-framework-sample).

2. Přidáte závislosti do horní části souboru s další závislosti:

    ```
    using RestSharp;
    using System.Collections.Generic;
    using Newtonsoft.Json;
    ```
3. Přidat následující třídy pro volání služby QnA Maker:

    ```
        /// <summary>
        /// QnAMakerService is a wrapper over the QnA Maker REST endpoint
        /// </summary>
        [Serializable]
        public class QnAMakerService
        {
            private string qnaServiceHostName;
            private string knowledgeBaseId;
            private string endpointKey;
    
            /// <summary>
            /// Initialize a particular endpoint with it's details
            /// </summary>
            /// <param name="hostName">Hostname of the endpoint</param>
            /// <param name="kbId">Knowledge base ID</param>
            /// <param name="ek">Endpoint Key</param>
            public QnAMakerService(string hostName, string kbId, string ek)
            {
                qnaServiceHostName = hostName;
                knowledgeBaseId = kbId;
                endpointKey = ek;
            }
    
            /// <summary>
            /// Call the QnA Maker endpoint and get a response
            /// </summary>
            /// <param name="query">User question</param>
            /// <returns></returns>
            public string GetAnswer(string query)
            {
                var client = new RestClient( qnaServiceHostName + "/qnamaker/knowledgebases/" + knowledgeBaseId + "/generateAnswer");
                var request = new RestRequest(Method.POST);
                request.AddHeader("authorization", "EndpointKey " + endpointKey);
                request.AddHeader("content-type", "application/json");
                request.AddParameter("application/json", "{\"question\": \"" + query + "\"}", ParameterType.RequestBody);
                IRestResponse response = client.Execute(request);
    
                // Deserialize the response JSON
                QnAAnswer answer = JsonConvert.DeserializeObject<QnAAnswer>(response.Content);
    
                // Return the answer if present
                if (answer.answers.Count > 0)
                    return answer.answers[0].answer;
                else
                    return "No good match found.";
            }
        }
    
        /* START - QnA Maker Response Class */
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
        /* END - QnA Maker Response Class */
    ```

3. Přejděte na https://qnamaker.ai -> Moje znalostních bází -> Zobrazit kód odpovídající znalostní báze. Získáte následující informace:

    ![Požadavek QnA Maker HTTP](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

4. Vytvoření instance třídy QnAMakerService pro každou znalostních bází:
    ```
            // Instantiate the knowledge bases
            public QnAMakerService hrQnAService = new QnAMakerService("https://hrkb.azurewebsites.net", "<HR knowledge base id>", "<HR endpoint key>");
            public QnAMakerService payrollQnAService = new QnAMakerService("https://payrollkb.azurewebsites.net", "<Payroll knowledge base id>", "<Payroll endpoint key>");
            public QnAMakerService financeQnAService = new QnAMakerService("https://financekb.azurewebsites.net", "<Finance knowledge base id>", "<Finance endpoint key>");
    ```

5. Znalostní báze odpovídající volání pro záměr.
    ```
            // HR Intent
            [LuisIntent("HR")]
            public async Task CancelIntent(IDialogContext context, LuisResult result)
            {
                // Ask the HR knowledge base
                await context.PostAsync(hrQnAService.GetAnswer(result.Query));
            }
    
            // Payroll intent
            [LuisIntent("Payroll")]
            public async Task GreetingIntent(IDialogContext context, LuisResult result)
            {
                // Ask the payroll knowledge base
                await context.PostAsync(payrollQnAService.GetAnswer(result.Query));
            }
    
            // Finance intent
            [LuisIntent("Finance")]
            public async Task HelpIntent(IDialogContext context, LuisResult result)
            {
                // Ask the finance knowledge base
                await context.PostAsync(financeQnAService.GetAnswer(result.Query));
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
