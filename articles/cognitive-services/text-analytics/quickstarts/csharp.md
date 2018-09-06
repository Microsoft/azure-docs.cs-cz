---
title: Rychlý start C# pro služby Cognitive Services Text Analytics API | Dokumentace Microsoftu
description: Získejte informace a ukázky kódu, které vám pomůžou rychle začít s využitím rozhraní Text Analytics API ve službě Microsoft Cognitive Services v Azure.
services: cognitive-services
documentationcenter: ''
author: luiscabrer
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 08/30/2018
ms.author: ashmaka
ms.openlocfilehash: 42a682898303b742a17b0a6d4d98c2b9fedf9003
ms.sourcegitcommit: 3d0295a939c07bf9f0b38ebd37ac8461af8d461f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2018
ms.locfileid: "43841690"
---
# <a name="quickstart-for-the-text-analytics-api-with-c"></a>Rychlý start pro rozhraní Text Analytics API s využitím C# 
<a name="HOLTop"></a>

V tomto článku se dozvíte, jak zjistit jazyk, analýza sentimentu a extrakce klíčových frází pomocí [rozhraní Text Analytics API](//go.microsoft.com/fwlink/?LinkID=759711) pomocí jazyka C#. Kód byl zapsán pro práci na aplikaci .NET Core, s minimálními odkazy na externí knihovny, takže je možné také spustit v systému Linux nebo MacOS.

Odkazovat [definice rozhraní API](//go.microsoft.com/fwlink/?LinkID=759346) technickou dokumentaci pro rozhraní API.

## <a name="prerequisites"></a>Požadavky

Musíte mít [účet rozhraní API služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pomocí rozhraní API pro analýzu textu. Můžete použít *bezplatná úroveň 5 000 transakcí za měsíc* k dokončení tohoto rychlého startu.

Je také nutné mít [koncový bod a přístupový klíč](../How-tos/text-analytics-how-to-access-key.md) , které byly vygenerovány pro vás během registrace. 


## <a name="install-the-nuget-sdk-package"></a>Nainstalovat balíček NuGet sady SDK
1. Vytvořte nové konzoly řešení v sadě Visual Studio.
1. Klikněte pravým tlačítkem na řešení a vyberte **spravovat balíčky NuGet pro řešení**.
1. Vyberte **zahrnout předprodejní verze** zaškrtávací políčko.
1. Vyberte **Procházet** kartu a vyhledejte **Microsoft.Azure.CognitiveServices.Language**.
1. Vyberte **Microsoft.Azure.CognitiveServices.Language.TextAnalytics** NuGet balíček a nainstalujte ho.

> [!Tip]
> I když můžete volat [koncových bodů HTTP](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) přímo z jazyka C# Microsoft.Azure.CognitiveServices.Language SDK mnohem snadněji k vyvolání služby bez nutnosti starat o serializaci a deserializaci JSON.
>
> Tady jsou užitečné odkazy:
> - [Stránka NuGet sady SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)
> - [Kód SDK](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Language/TextAnalytics)


## <a name="call-the-text-analytics-api-by-using-the-sdk"></a>Volání rozhraní API pro analýzu textu s použitím sady SDK
1. Soubor Program.cs nahraďte následujícím kódem. Tento program předvádí možnosti rozhraní API pro analýzu textu ve třech částech (jazyk extrakce, extrakce klíčových frází – to a analýza mínění).
1. Nahradit `Ocp-Apim-Subscription-Key` hodnota hlavičky přístupovým klíčem, který je platný pro vaše předplatné.
1. Nahraďte umístění v `Endpoint` ke koncovému bodu, který jste se přihlásili k. Najít koncový bod u prostředku Azure portal. Koncový bod se obvykle začíná řetězcem "https://[region].api.cognitive.microsoft.com." Zahrnout pouze název protokolu a hostitele.
1. Spuštění programu.

```csharp
using System;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
using System.Collections.Generic;
using Microsoft.Rest;
using System.Linq;
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
```

## <a name="detect-language"></a>Zjištění jazyka

Rozhraní API pro detekci jazyk zjistí jazyk textu dokumentu, pomocí [rozpoznat jazyk metoda](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7).

```csharp
            // Extracting language.
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
```

## <a name="extract-key-phrases"></a>Extrakce klíčových frází

Rozhraní API Key frázi extrakci extrahuje klíčových frází z textu dokumentu, pomocí [klíčových frází metoda](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6).

```csharp
            // Getting key phrases.
            Console.WriteLine("\n\n===== KEY-PHRASE EXTRACTION ======");

            KeyPhraseBatchResult result2 = client.KeyPhrasesAsync(new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("ja", "1", "猫は幸せ"),
                          new MultiLanguageInput("de", "2", "Fahrt nach Stuttgart und dann zum Hotel zu Fu."),
                          new MultiLanguageInput("en", "3", "My cat is stiff as a rock."),
                          new MultiLanguageInput("es", "4", "A mi me encanta el fútbol!")
                        })).Result;

            // Printing key phrases.
            foreach (var document in result2.Documents)
            {
                Console.WriteLine("Document ID: {0} ", document.Id);

                Console.WriteLine("\t Key phrases:");

                foreach (string keyphrase in document.KeyPhrases)
                {
                    Console.WriteLine("\t\t" + keyphrase);
                }
            }
```

## <a name="analyze-sentiment"></a>Analýza mínění

Rozhraní API pro analýzu mínění rozpozná mínění sadu textových záznamů pomocí [mínění metoda](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9).

```csharp
            // Analyzing sentiment.
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


            // Printing sentiment results.
            foreach (var document in result3.Documents)
            {
                Console.WriteLine("Document ID: {0} , Sentiment Score: {1:0.00}", document.Id, document.Score);
            }
```

## <a name="identify-linked-entities"></a>Identifikujte propojených entit

Rozhraní Entity Linking API identifikuje dobře známé entity v textu dokumentu, pomocí [propojování entit metoda](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634).

```csharp
            // Linking entities
            Console.WriteLine("\n\n===== ENTITY LINKING ======");

            EntitiesBatchResult result4 = client.EntitiesAsync(
                    new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                            new MultiLanguageInput("en", "0", "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."),
                            new MultiLanguageInput("en", "1", "The Seattle Seahawks won the Super Bowl in 2014."),
                        })).Result;

            // Printing entity results.
            foreach (var document in result4.Documents)
            {
                Console.WriteLine("Document ID: {0} , Entities: {1}", document.Id, String.Join(", ", document.Entities.Select(entity => entity.Name)));
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
 [Nejčastější dotazy](../text-analytics-resource-faq.md)
