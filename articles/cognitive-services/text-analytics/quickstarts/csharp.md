---
title: 'Rychlý start: Pomocí C# k volání rozhraní Text Analytics API'
titleSuffix: Azure Cognitive Services
description: Získejte informace a vzorové kódy, které vám pomůžou rychle začít používat rozhraní API pro analýzu textu.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 01/02/2019
ms.author: assafi
ms.openlocfilehash: 5b6ae20445b74aa1713f9af5765684a9c01e2953
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55224305"
---
# <a name="quickstart-using-c-to-call-the-text-analytics-cognitive-service"></a>Rychlý start: Pomocí C# zavolat Text Analytics služby Cognitive Services
<a name="HOLTop"></a>

V tomto článku se dozvíte, jak zjistit jazyk, analýza sentimentu a extrakce klíčových frází pomocí [rozhraní Text Analytics API](//go.microsoft.com/fwlink/?LinkID=759711) s C#. Kód je napsaný tak, aby fungoval v aplikaci .NET Core, a obsahuje minimum odkazů na externí knihovny, takže ho můžete spustit i v systému Linux nebo MacOS.

Technickou dokumentaci pro tato rozhraní API najdete v [definicích rozhraní API](//go.microsoft.com/fwlink/?LinkID=759346).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Dále musíte mít [koncový bod a přístupový klíč](../How-tos/text-analytics-how-to-access-key.md) vygenerovaný během registrace.


## <a name="install-the-nuget-sdk-package"></a>Nainstalovat balíček NuGet sady SDK
1. Vytvořte nové konzolové řešení v sadě Visual Studio.
1. Klikněte na řešení pravým tlačítkem a pak klikněte na **Spravovat balíčky NuGet pro řešení**.
1. Zaškrtněte políčko **Zahrnout předběžné verze**.
1. Vyberte kartu **Procházet** a vyhledejte **Microsoft.Azure.CognitiveServices.Language.TextAnalytics**.
1. Vyberte balíček NuGet a nainstalujte ho.

> [!Tip]
>  [Koncové body HTTP](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) můžete volat přímo z kódu jazyka C#, ale sada SDK Microsoft.Azure.CognitiveServices.Language volání služby výrazně usnadňuje díky tomu, že se nemusíte starat o serializaci a deserializaci formátu JSON.
>
> Několik užitečných odkazů:
> - [Balíček NuGet sady SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)
> - [Kód sady SDK](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Language/TextAnalytics)


## <a name="call-the-text-analytics-api-using-the-sdk"></a>Volání rozhraní API pro analýzu textu s využitím sady SDK
1. Nahraďte soubor Program.cs níže uvedeným kódem. Tento program předvádí možnosti rozhraní API pro analýzu textu ve třech částech (jazyk extrakce, extrakce klíčových frází – to a analýza mínění).
1. Hodnotu hlavičky `Ocp-Apim-Subscription-Key` nahraďte přístupovým klíčem platným pro vaše předplatné.
1. Nahraďte umístění v proměnné `Endpoint` za koncový bod, který jste si zaregistrovali. Najít koncový bod u prostředku Azure portal. Koncový bod obvykle začíná řetězcem "https://[region].api.cognitive.microsoft.com" a pouze sem patří protokol a název hostitele.
1. Spusťte program.

```csharp
using System;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
using System.Collections.Generic;
using Microsoft.Rest;
using System.Net.Http;
using System.Threading;
using System.Threading.Tasks;

namespace ConsoleApp1
{
    class Program
    {
        /// <summary>
        /// Container for subscription credentials. Make sure to enter your valid key.
        private const string SubscriptionKey = ""; //Insert your Text Anaytics subscription key

        /// </summary>
        class ApiKeyServiceClientCredentials : ServiceClientCredentials
        {
            public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
            {
                request.Headers.Add("Ocp-Apim-Subscription-Key", SubscriptionKey);
                return base.ProcessHttpRequestAsync(request, cancellationToken);
            }
        }

        static void Main(string[] args)
        {

            // Create a client.
            ITextAnalyticsClient client = new TextAnalyticsClient(new ApiKeyServiceClientCredentials())
            {
                Endpoint = "https://westus.api.cognitive.microsoft.com"
            }; //Replace 'westus' with the correct region for your Text Analytics subscription

            Console.OutputEncoding = System.Text.Encoding.UTF8;

            // Extracting language
            Console.WriteLine("===== LANGUAGE EXTRACTION ======");

            var result = client.DetectLanguageAsync(new BatchInput(
                    new List<Input>()
                        {
                          new Input("1", "This is a document written in English."),
                          new Input("2", "Este es un document escrito en Español."),
                          new Input("3", "这是一个用中文写的文件")
                    })).Result;

            // Printing language results.
            foreach (var document in result.Documents)
            {
                Console.WriteLine($"Document ID: {document.Id} , Language: {document.DetectedLanguages[0].Name}");
            }

            // Getting key-phrases
            Console.WriteLine("\n\n===== KEY-PHRASE EXTRACTION ======");

            KeyPhraseBatchResult result2 = client.KeyPhrasesAsync(new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("ja", "1", "猫は幸せ"),
                          new MultiLanguageInput("de", "2", "Fahrt nach Stuttgart und dann zum Hotel zu Fu."),
                          new MultiLanguageInput("en", "3", "My cat is stiff as a rock."),
                          new MultiLanguageInput("es", "4", "A mi me encanta el fútbol!")
                        })).Result;

            // Printing keyphrases
            foreach (var document in result2.Documents)
            {
                Console.WriteLine($"Document ID: {document.Id} ");

                Console.WriteLine("\t Key phrases:");

                foreach (string keyphrase in document.KeyPhrases)
                {
                    Console.WriteLine($"\t\t{keyphrase}");
                }
            }

            // Extracting sentiment
            Console.WriteLine("\n\n===== SENTIMENT ANALYSIS ======");

            SentimentBatchResult result3 = client.SentimentAsync(
                    new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("en", "0", "I had the best day of my life."),
                          new MultiLanguageInput("en", "1", "This was a waste of my time. The speaker put me to sleep."),
                          new MultiLanguageInput("es", "2", "No tengo dinero ni nada que dar..."),
                          new MultiLanguageInput("it", "3", "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."),
                        })).Result;


            // Printing sentiment results
            foreach (var document in result3.Documents)
            {
                Console.WriteLine($"Document ID: {document.Id} , Sentiment Score: {document.Score:0.00}");
            }


            // Identify entities
            Console.WriteLine("\n\n===== ENTITIES ======");

            EntitiesBatchResultV2dot1 result4 = client.EntitiesAsync(
                    new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("en", "0", "The Great Depression began in 1929. By 1933, the GDP in America fell by 25%.")
                        })).Result;

            // Printing entities results
            foreach (var document in result4.Documents)
            {
                Console.WriteLine($"Document ID: {document.Id} ");

                Console.WriteLine("\t Entities:");

                foreach (EntityRecordV2dot1 entity in document.Entities)
                {
                    Console.WriteLine($"\t\t{entity.Name}\t\t{entity.WikipediaUrl}\t\t{entity.Type}\t\t{entity.SubType}");
                }
            }

            Console.ReadLine();
        }
    }
}
```

## <a name="application-output"></a>Výstup aplikace

Aplikace zobrazí následující informace:

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified


===== KEY-PHRASE EXTRACTION ======
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
Document ID: 3
         Key phrases:
                cat
                rock
Document ID: 4
         Key phrases:
                fútbol

===== SENTIMENT ANALYSIS ======
Document ID: 0 , Sentiment Score: 0.87
Document ID: 1 , Sentiment Score: 0.11
Document ID: 2 , Sentiment Score: 0.44
Document ID: 3 , Sentiment Score: 1.00
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Analýza textu s využitím Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Další informace najdete v tématech

 [Přehled analýzy textu](../overview.md) [– nejčastější dotazy (FAQ)](../text-analytics-resource-faq.md)

