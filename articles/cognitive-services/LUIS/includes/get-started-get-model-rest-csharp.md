---
title: 'Získání modelu pomocí volání REST v jazyce C #'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/18/2020
ms.author: diberry
ms.openlocfilehash: b1bf3c0d7c902a048881b5fb75783744214b073e
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655509"
---
## <a name="prerequisites"></a>Požadavky

* Azure Language Understanding – vytváření znaků a vytváření kódu URL koncového bodu prostředku 32. Vytvořte pomocí [Azure Portal](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) nebo [Azure CLI](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli).
* Importujte aplikaci [Pizza](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json) z `Azure-Samples/cognitive-services-sample-data-files` úložiště GitHub.
* ID aplikace LUIS pro importovanou aplikaci Pizza ID aplikace je uvedené na řídicím panelu aplikace.
* ID verze aplikace, která přijímá promluvy.
* [.NET Core 3,1](https://dotnet.microsoft.com/download)
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>Soubor JSON s ukázkovými promluvami

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="change-model-programmatically"></a>Programové změny modelu

1. Vytvořte novou konzolovou aplikaci cílící na jazyk C# s názvem projektu a složky `csharp-model-with-rest` .

    ```console
    dotnet new console -lang C# -n csharp-model-with-rest
    ```

1. Přejděte do `csharp-model-with-rest` vytvořeného adresáře a nainstalujte požadované závislosti pomocí těchto příkazů:

    ```console
    cd csharp-model-with-rest
    dotnet add package System.Net.Http
    dotnet add package JsonFormatterPlus
    ```

1. Soubor Program.cs přepište následujícím kódem:

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using System.Linq;

    // 3rd party NuGet packages
    using JsonFormatterPlus;

    namespace AddUtterances
    {
        class Program
        {
            // YOUR-APP-ID: The App ID GUID found on the www.luis.ai Application Settings page.
            static string appID = "YOUR-APP-ID";

            // YOUR-AUTHORING-KEY: Your LUIS authoring key, 32 character value.
            static string authoringKey = "YOUR-AUTHORING-KEY";

            // YOUR-AUTHORING-ENDPOINT: Replace this endpoint with your authoring key endpoint.
            // For example, "https://your-resource-name.api.cognitive.microsoft.com/"
            static string endpoint = "YOUR-AUTHORING-ENDPOINT";

            // NOTE: Replace this your version number.
            static string appVersion = "0.1";

            static string host = String.Format("{0}luis/authoring/v3.0-preview/apps/{1}/versions/{2}/", endpoint, appID, appVersion);

            // GET request with authentication
            async static Task<HttpResponseMessage> SendGet(string uri)
            {
                using (var client = new HttpClient())
                using (var request = new HttpRequestMessage())
                {
                    request.Method = HttpMethod.Get;
                    request.RequestUri = new Uri(uri);
                    request.Headers.Add("Ocp-Apim-Subscription-Key", authoringKey);
                    return await client.SendAsync(request);
                }
            }

            // POST request with authentication
            async static Task<HttpResponseMessage> SendPost(string uri, string requestBody)
            {
                using (var client = new HttpClient())
                using (var request = new HttpRequestMessage())
                {
                    request.Method = HttpMethod.Post;
                    request.RequestUri = new Uri(uri);

                    if (!String.IsNullOrEmpty(requestBody))
                    {
                        request.Content = new StringContent(requestBody, Encoding.UTF8, "text/json");
                    }

                    request.Headers.Add("Ocp-Apim-Subscription-Key", authoringKey);
                    return await client.SendAsync(request);
                }
            }

            // Add utterances as string with POST request
            async static Task AddUtterances(string utterances)
            {
                string uri = host + "examples";

                var response = await SendPost(uri, utterances);
                var result = await response.Content.ReadAsStringAsync();
                Console.WriteLine("Added utterances.");
                Console.WriteLine(JsonFormatter.Format(result));
            }

            // Train app after adding utterances
            async static Task Train()
            {
                string uri = host  + "train";

                var response = await SendPost(uri, null);
                var result = await response.Content.ReadAsStringAsync();
                Console.WriteLine("Sent training request.");
                Console.WriteLine(JsonFormatter.Format(result));
            }

            // Check status of training
            async static Task Status()
            {
                var response = await SendGet(host  + "train");
                var result = await response.Content.ReadAsStringAsync();
                Console.WriteLine("Requested training status.");
                Console.WriteLine(JsonFormatter.Format(result));
            }

            // Add utterances, train, check status
            static void Main(string[] args)
            {
                string utterances = @"
                [
                    {
                        'text': 'order a pizza',
                        'intentName': 'ModifyOrder',
                        'entityLabels': [
                            {
                                'entityName': 'Order',
                                'startCharIndex': 6,
                                'endCharIndex': 12
                            }
                        ]
                    },
                    {
                        'text': 'order a large pepperoni pizza',
                        'intentName': 'ModifyOrder',
                        'entityLabels': [
                            {
                                'entityName': 'Order',
                                'startCharIndex': 6,
                                'endCharIndex': 28
                            },
                            {
                                'entityName': 'FullPizzaWithModifiers',
                                'startCharIndex': 6,
                                'endCharIndex': 28
                            },
                            {
                                'entityName': 'PizzaType',
                                'startCharIndex': 14,
                                'endCharIndex': 28
                            },
                            {
                                'entityName': 'Size',
                                'startCharIndex': 8,
                                'endCharIndex': 12
                            }
                        ]
                    },
                    {
                        'text': 'I want two large pepperoni pizzas on thin crust',
                        'intentName': 'ModifyOrder',
                        'entityLabels': [
                            {
                                'entityName': 'Order',
                                'startCharIndex': 7,
                                'endCharIndex': 46
                            },
                            {
                                'entityName': 'FullPizzaWithModifiers',
                                'startCharIndex': 7,
                                'endCharIndex': 46
                            },
                            {
                                'entityName': 'PizzaType',
                                'startCharIndex': 17,
                                'endCharIndex': 32
                            },
                            {
                                'entityName': 'Size',
                                'startCharIndex': 11,
                                'endCharIndex': 15
                            },
                            {
                                'entityName': 'Quantity',
                                'startCharIndex': 7,
                                'endCharIndex': 9
                            },
                            {
                                'entityName': 'Crust',
                                'startCharIndex': 37,
                                'endCharIndex': 46
                            }
                        ]
                    }
                ]
                ";

                AddUtterances(utterances).Wait();
                Train().Wait();
                Status().Wait();
            }
        }
    }
    ```

1. Nahraďte hodnoty začínající `YOUR-` vlastními hodnotami.

    |Informace|Účel|
    |--|--|
    |`YOUR-APP-ID`| Vaše ID aplikace LUIS |
    |`YOUR-AUTHORING-KEY`|Klíč pro vytváření znaků 32.|
    |`YOUR-AUTHORING-ENDPOINT`| Váš koncový bod adresy URL pro vytváření Například, `https://replace-with-your-resource-name.api.cognitive.microsoft.com/`. Název prostředku se nastaví při vytváření prostředku.|

    Přiřazené klíče a prostředky jsou zobrazené na portálu LUIS v části Správa na stránce **prostředky Azure** . ID aplikace je k dispozici ve stejné části pro správu na stránce **nastavení aplikace** .

1. Sestavte konzolovou aplikaci.

    ```console
    dotnet build
    ```

1. Spusťte konzolovou aplikaci. Výstup konzoly zobrazí stejný kód JSON, který jste viděli dříve v okně prohlížeče.

    ```console
    dotnet run
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s tímto rychlým startem hotovi, odstraňte složku projektu ze systému souborů.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Osvědčené postupy pro aplikaci](../luis-concept-best-practices.md)
