---
title: Získat předpovědi s voláním REST vC#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: 4cbec342bc20de35c0c62284e4e1fe1ae8b8e8a4
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2020
ms.locfileid: "76966869"
---
## <a name="prerequisites"></a>Požadavky

* [.NET Core V 2.2 +](https://dotnet.microsoft.com/download)
* [Visual Studio Code](https://code.visualstudio.com/)
* ID veřejné aplikace: `df67dcdb-c37d-46af-88e1-8b97951ca1c2`

## <a name="create-luis-runtime-key-for-predictions"></a>Vytvoření klíče LUIS runtime pro předpovědi

1. Přihlaste se k [Azure Portal](https://portal.azure.com)
1. Klikněte na [vytvořit **Language Understanding** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Zadejte všechna požadovaná nastavení pro klíč za běhu:

    |Nastavení|Hodnota|
    |--|--|
    |Name (Název)|Požadovaný název (2-64 znaků)|
    |Předplatné|Vyberte odpovídající předplatné.|
    |Umístění|Výběr libovolného okolí a dostupného umístění|
    |Cenová úroveň|`F0` – minimální cenová úroveň|
    |Skupina prostředků|Vyberte dostupnou skupinu prostředků.|

1. Klikněte na **vytvořit** a počkejte na vytvoření prostředku. Po vytvoření přejděte na stránku prostředků.
1. Shromážděte nakonfigurované `endpoint` a `key`.

## <a name="get-intent-programmatically"></a>Získání záměru prostřednictvím kódu programu

Použijte C# (.NET Core) k dotazování [koncového bodu předpovědi](https://aka.ms/luis-apim-v3-prediction) a získejte výsledek předpovědi.

1. Vytvořte novou konzolovou aplikaci, která C# cílí na jazyk, s názvem projektu a složky `predict-with-rest`.

    ```console
    dotnet new console -lang C# -n predict-with-rest
    ```

1. Přejděte do adresáře `predict-with-rest`, který jste právě vytvořili, a nainstalujte požadované závislosti pomocí těchto příkazů:

    ```console
    cd predict-with-rest
    dotnet add package System.Net.Http
    ```

1. Otevřete `Program.cs` v oblíbeném integrovaném vývojovém prostředí (IDE) nebo editoru. Pak `Program.cs` přepsat následujícím kódem:

   ```csharp
    using System;
    using System.Net.Http;
    using System.Web;

    namespace predict_with_rest
    {
        class Program
        {
            static void Main(string[] args)
            {
                // YOUR-KEY: 32 character key
                var key = "YOUR-KEY";

                // YOUR-ENDPOINT: example is your-resource-name.api.cognitive.microsoft.com
                var endpoint = "YOUR-ENDPOINT";

                // //public sample app
                var appId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2";

                var utterance = "turn on all lights";

                MakeRequest(key, endpoint, appId, utterance);

                Console.WriteLine("Hit ENTER to exit...");
                Console.ReadLine();
            }
            static async void MakeRequest(string key, string endpoint, string appId, string utterance)
            {
                var client = new HttpClient();
                var queryString = HttpUtility.ParseQueryString(string.Empty);

                // The request header contains your subscription key
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

                // The "q" parameter contains the utterance to send to LUIS
                queryString["query"] = utterance;

                // These optional request parameters are set to their default values
                queryString["verbose"] = "true";
                queryString["show-all-intents"] = "true";
                queryString["staging"] = "false";
                queryString["timezoneOffset"] = "0";

                var endpointUri = String.Format("https://{0}/luis/prediction/v3.0/apps/{1}/slots/production/predict?query={2}", endpoint, appId, queryString);

                var response = await client.GetAsync(endpointUri);

                var strResponseContent = await response.Content.ReadAsStringAsync();

                // Display the JSON result from LUIS
                Console.WriteLine(strResponseContent.ToString());
            }
        }
    }

   ```

1. Hodnoty `YOUR-KEY` a `YOUR-ENDPOINT` nahraďte vlastním klíčem předpovědi a koncovým bodem.

    |Informace|Účel|
    |--|--|
    |`YOUR-KEY`|Klíč předpovědi znaků 32.|
    |`YOUR-ENDPOINT`| Koncový bod adresy URL předpovědi Například, `replace-with-your-resource-name.api.cognitive.microsoft.com`.|

1. Sestavte konzolovou aplikaci pomocí tohoto příkazu:

    ```console
    dotnet build
    ```

1. Spusťte konzolovou aplikaci. Výstup konzoly zobrazí stejný kód JSON, který jste viděli dříve v okně prohlížeče.

    ```console
    dotnet run
    ```

1. Zkontrolujte odpověď předpovědi, která se vrátí jako JSON:

    ```console
    Hit ENTER to exit...
    {'query': 'turn on all lights', 'prediction': {'topIntent': 'HomeAutomation.TurnOn', 'intents': {'HomeAutomation.TurnOn': {'score': 0.5375382}, 'None': {'score': 0.08687421}, 'HomeAutomation.TurnOff': {'score': 0.0207554}}, 'entities': {'HomeAutomation.Operation': ['on'], '$instance': {'HomeAutomation.Operation': [{'type': 'HomeAutomation.Operation', 'text': 'on', 'startIndex': 5, 'length': 2, 'score': 0.724984169, 'modelTypeId': -1, 'modelType': 'Unknown', 'recognitionSources': ['model']}]}}}}
    ```

    Odpověď JSON naformátovaná pro čitelnost:

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "None": {
                    "score": 0.08687421
                },
                "HomeAutomation.TurnOff": {
                    "score": 0.0207554
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ],
                "$instance": {
                    "HomeAutomation.Operation": [
                        {
                            "type": "HomeAutomation.Operation",
                            "text": "on",
                            "startIndex": 5,
                            "length": 2,
                            "score": 0.724984169,
                            "modelTypeId": -1,
                            "modelType": "Unknown",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s tímto rychlým startem hotovi, odstraňte soubor ze systému souborů.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidat projevy a vlak](../get-started-get-model-rest-apis.md)