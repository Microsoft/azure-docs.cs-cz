---
title: Rychlý start C# pro Azure Cognitive Services Text Analytics API | Dokumentace Microsoftu
description: Získat informace a ukázky kódu můžete rychle začít používat rozhraní API pro analýzu textu ve službě Microsoft Cognitive Services v Azure.
services: cognitive-services
documentationcenter: ''
author: luiscabrer
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 09/20/2017
ms.author: ashmaka
ms.openlocfilehash: 59e2254054f51a8d5f30e1b38dc5e6c23899c054
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284317"
---
# <a name="quickstart-for-text-analytics-api-with-c"></a>Rychlý start pro rozhraní Text Analytics API pomocí jazyka C# 
<a name="HOLTop"></a>

V tomto článku se dozvíte, jak zjistit jazyk, analýza sentimentu a extrakce klíčových frází pomocí [rozhraní Text Analytics API](//go.microsoft.com/fwlink/?LinkID=759711) pomocí jazyka C#. Kód byla zapsána do pracovat.Net Core aplikace s minimálními odkazy na externí knihovny, můžete ho také spustit v systému Linux nebo MacOS.

Odkazovat [definice rozhraní API](//go.microsoft.com/fwlink/?LinkID=759346) technickou dokumentaci pro rozhraní API.

## <a name="prerequisites"></a>Požadavky

Musíte mít [účet rozhraní API služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s **rozhraní Text Analytics API**. Můžete použít **bezplatná úroveň 5 000 transakcí za měsíc** k dokončení tohoto rychlého startu.

Je také nutné mít [koncový bod a přístupový klíč](../How-tos/text-analytics-how-to-access-key.md) , která byla vygenerována během přihlašování nahoru. 


## <a name="install-the-nuget-sdk-package"></a>Nainstalovat balíček Nuget sady SDK
1. Vytvořte nové konzoly řešení v sadě Visual Studio.
1. Klikněte pravým tlačítkem myši na řešení a klikněte na **spravovat balíčky NuGet pro řešení**
1. Mark **zahrnout předprodejní verze** zaškrtávací políčko.
1. Vyberte **Procházet** kartu a vyhledejte **Microsoft.Azure.CognitiveServices.Language**
1. Vyberte balíček Nuget a nainstalujte ho.

> [!Tip]
>  Přestože lze volat [koncových bodů HTTP](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) přímo z jazyka C# Microsoft.Azure.CognitiveServices.Language SDK mnohem snadněji k vyvolání služby bez nutnosti starat o serializaci a deserializaci JSON.
>
> Několik užitečných odkazů:
> - [Stránka Nuget sady SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)
> - [Kód SDK ](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Language/TextAnalytics)


## <a name="call-the-text-analytics-api-using-the-sdk"></a>Volání rozhraní API pro analýzu textu pomocí sady SDK
1. Nahraďte níže uvedený kód souboru Program.cs. Tento program předvádí možnosti rozhraní API pro analýzu textu ve 3 oddíly (jazyk extrakce, extrakce klíčových frází – to a analýza mínění).
1. Nahradit `Ocp-Apim-Subscription-Key` hodnota hlavičky se přístupový klíč platný pro vaše předplatné.
1. Nahraďte umístění v `Endpoint` ke koncovému bodu registrovanou službu. Koncový bod najdete na webu Azure Portal prostředků. Koncový bod obvykle začíná řetězcem "https://[region].api.cognitive.microsoft.com" a tady prosím obsahovat jenom protokol a název hostitele.
1. Spuštění programu.

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
            ITextAnalyticsClient client = new TextAnalyticsClient(new ApiKeyServiceClientCredentials())
            {
                Endpoint = "https://westus.api.cognitive.microsoft.com"
            };

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
> [Analýza textu s využitím Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Další informace najdete v tématech 

 [Přehled analýzy textu](../overview.md)  
 [Nejčastější dotazy (FAQ)](../text-analytics-resource-faq.md)

