---
title: Rychlý start C# pro služby Azure kognitivní, analýza textu rozhraní API | Microsoft Docs
description: Get informace a ukázky kódu můžete rychle začít používat rozhraní API Analytics Text v kognitivní služby společnosti Microsoft na platformě Azure.
services: cognitive-services
documentationcenter: ''
author: luiscabrer
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 09/20/2017
ms.author: ashmaka
ms.openlocfilehash: f46c5179fc245d84a72e038fe3870d2e6c990550
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "35343914"
---
# <a name="quickstart-for-text-analytics-api-with-c"></a>Rychlý start pro Analýza textu rozhraní API pomocí jazyka C# 
<a name="HOLTop"></a>

Tento článek ukazuje, jak zjišťovat jazyk, analyzovat postojích a extrahovat klíče frází pomocí [rozhraní API Analytics Text](//go.microsoft.com/fwlink/?LinkID=759711) pomocí C#. Kód byla zapsána do pracovat na .net Core aplikace s minimálním odkazy na externí knihovny, takže je může také spustit v systému Linux nebo systému MacOS.

Odkazovat [definice rozhraní API](//go.microsoft.com/fwlink/?LinkID=759346) pro technická dokumentace k rozhraní API.

## <a name="prerequisites"></a>Požadavky

Musíte mít [kognitivní rozhraní API služby účet](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s **Text Analytics API**. Můžete použít **úroveň free pro 5 000 transakce za měsíc** dokončete tento rychlý start.

Je také nutné mít [koncový bod a přístupový klíč](../How-tos/text-analytics-how-to-access-key.md) který byl vygenerován pro vás při přihlašování nahoru. 


## <a name="install-the-nuget-sdk-package"></a>Nainstalujte balíček Nuget sady SDK
1. Vytvořte nové řešení konzoly v sadě Visual Studio.
1. Klikněte pravým tlačítkem na řešení a klikněte na tlačítko **spravovat balíčky NuGet pro řešení**
1. Označit **zahrnout předprodejní verze** zaškrtávací políčko.
1. Vyberte **Procházet** kartě a vyhledejte **Microsoft.Azure.CognitiveServices.Language**
1. Vyberte balíček Nuget a nainstalujte ho.

> [!Tip]
>  Když může zavolat [koncové body HTTP](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) přímo z C#, Microsoft.Azure.CognitiveServices.Language SDK je mnohem jednodušší volání služby bez nutnosti starat o serializaci a deserializaci JSON.
>
> Několik užitečné odkazy:
> - [Stránka Nuget sady SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language)
> - [Kód SDK ](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Language/Language)


## <a name="call-the-text-analytics-api-using-the-sdk"></a>Volání rozhraní API Analýza textu pomocí sady SDK
1. Nahraďte kód níže uvedenou Program.cs. Tento program předvádí možnosti rozhraní API Analytics Text v částech 3 (jazyk extrakce, extrakce klíč frázi a postojích analýzy).
1. Nahraďte `Ocp-Apim-Subscription-Key` hodnota hlavičky se přístupový klíč platný pro vaše předplatné.
1. Nahraďte umístění v `client.AzureRegion` (aktuálně `AzureRegions.Westus`) pro danou oblast registraci aplikace.
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
        /// </summary>
        class ApiKeyServiceClientCredentials : ServiceClientCredentials
        {
            public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
            {
                request.Headers.Add("Ocp-Apim-Subscription-Key", "ENTER KEY HERE");
                return base.ProcessHttpRequestAsync(request, cancellationToken);
            }
        }

        static void Main(string[] args)
        {

            // Create a client.
            ITextAnalyticsAPI client = new TextAnalyticsAPI(new ApiKeyServiceClientCredentials());
            client.AzureRegion = AzureRegions.Westus;

            Console.OutputEncoding = System.Text.Encoding.UTF8;

            // Extracting language
            Console.WriteLine("===== LANGUAGE EXTRACTION ======");

            var result =  client.DetectLanguageAsync(new BatchInput(
                    new List<Input>()
                        {
                          new Input("1", "This is a document written in English."),
                          new Input("2", "Este es un document escrito en Español."),
                          new Input("3", "这是一个用中文写的文件")
                    })).Result;

            // Printing language results.
            foreach (var document in result.Documents)
            {
                Console.WriteLine("Document ID: {0} , Language: {1}", document.Id, document.DetectedLanguages[0].Name);
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
                Console.WriteLine("Document ID: {0} ", document.Id);

                Console.WriteLine("\t Key phrases:");

                foreach (string keyphrase in document.KeyPhrases)
                {
                    Console.WriteLine("\t\t" + keyphrase);
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
                Console.WriteLine("Document ID: {0} , Sentiment Score: {1:0.00}", document.Id, document.Score);
            }
        }
    }
}
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Analýza textu pomocí Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Další informace najdete v tématech 

 [Přehled analýzy textu](../overview.md)  
 [Nejčastější dotazy (FAQ)](../text-analytics-resource-faq.md)

