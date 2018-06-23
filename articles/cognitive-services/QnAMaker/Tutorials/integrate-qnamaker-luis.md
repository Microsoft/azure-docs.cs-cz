---
title: Integrovat QnA Maker a LEOŠ - kognitivní služby Microsoft | Microsoft Docs
titleSuffix: Azure
description: podrobný kurz k integraci QnA Maker a LEOŠ
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: 0a0eeb3815b793ed81f60b2b239bc459e5574788
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343274"
---
# <a name="integrate-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>Integrovat QnA Maker a LEOŠ distribuovat znalostní báze
S růstem znalostní báze QnA Maker velký, bude obtížné ji jako jediný monolitický a není potřeba rozdělit do menších logických bloků znalostní bázi knowledge base.

I když je přehledné v QnA Maker vytvořit více znalostních bází, budete potřebovat některé logiku směrovat příchozí dotaz na odpovídající znalostní bázi knowledge base. To provedete pomocí LEOŠ.

## <a name="architecture"></a>Architektura

![Architektura Leoš QnA Maker](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

Ve výše uvedené scénáře QnA Maker nejdřív získá záměr příchozí dotaz z modelu LEOŠ a potom použít ke směrování do správné QnA Maker znalostní báze.

## <a name="prerequisites"></a>Požadavky
- Přihlaste se k [LEOŠ](https://www.luis.ai/) portál a [vytvořit aplikaci](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/create-new-app).
- [Přidat tříd Intent](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/add-intents) podle váš scénář.
- [Train](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-how-to-train) a [publikování](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/publishapp) LEOŠ aplikace.
- Přihlaste se k [QnA Maker](https://qnamaker.ai) a [vytvářet]() základny podle váš scénář.
- [Test]() a [publikování]() znalostních bází.

## <a name="qna-maker--luis-bot"></a>QnA Maker + LEOŠ robota
1. Nejprve vytvořte robota webové aplikace pomocí šablony LEOŠ, propojit s LEOŠ aplikaci, kterou jste vytvořili výše a upravovat záměry. Podrobné kroky najdete v části [zde](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-csharp-tutorial-build-bot-framework-sample).

2. Přidejte závislosti do horní části souboru, s další závislosti:

    ```
    using RestSharp;
    using System.Collections.Generic;
    using Newtonsoft.Json;
    ```
3. Přidat níže třídu pro volání služby QnA Maker:

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

3. Přejděte na https://qnamaker.ai -> – Moje znalostních bází -> zobrazení kód, odpovídající znalostní báze. Získáte následující informace:

    ![Požadavek QnA Maker HTTP](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

4. Pro každou znalostních bází vytvořit instanci třídy QnAMakerService:
    ```
            // Instantiate the knowledge bases
            public QnAMakerService hrQnAService = new QnAMakerService("https://hrkb.azurewebsites.net", "<HR knowledge base id>", "<HR endpoint key>");
            public QnAMakerService payrollQnAService = new QnAMakerService("https://payrollkb.azurewebsites.net", "<Payroll knowledge base id>", "<Payroll endpoint key>");
            public QnAMakerService financeQnAService = new QnAMakerService("https://financekb.azurewebsites.net", "<Finance knowledge base id>", "<Finance endpoint key>");
    ```

5. Volání znalostní báze odpovídající pro záměr.
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

## <a name="build-the-bot"></a>Sestavení robota
1. V editoru kódu, klikněte pravým tlačítkem na `build.cmd` a vyberte **spustit z konzoly**.

    ![spustit z konzoly](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. Zobrazení kódu se nahradí okno terminálu zobrazující průběh a výsledky sestavení.

    ![konzole sestavení](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>Testovací robota
Na portálu Azure vyberte **testů ve webové Chat** k testování robota. Typ zprávy z různých tříd Intent se získat odpověď z odpovídající znalostní bázi knowledge base.

![test webu chatu](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření plánu kontinuity obchodních pro QnA Maker](../How-To/business-continuity-plan.md)
