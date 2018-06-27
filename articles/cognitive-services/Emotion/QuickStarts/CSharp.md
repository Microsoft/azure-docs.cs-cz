---
title: Rozpoznávání emocí úrovně rozhraní API jazyka C# rychlý start | Microsoft Docs
description: Získáte informace a ukázku kódu můžete rychle začít s použitím rozhraní API pro rozpoznávání emocí úrovně pomocí C# v kognitivní služby.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 11/02/2017
ms.author: anroth
ms.openlocfilehash: 89735ae54395447e3cb421f45db3d6b99001ecd6
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37016561"
---
# <a name="emotion-api-c-quick-start"></a>Rozpoznávání emocí úrovně rozhraní API jazyka C# rychlý start

> [!IMPORTANT]
> Verze Preview rozhraní Video API byla ukončena 30. října 2017. Statistika snadno extrahovat z videa, vyzkoušet nový [Preview rozhraní API Indexer Video](https://azure.microsoft.com/services/cognitive-services/video-indexer/). Také vám pomůže ho vylepšit zjišťování mluvené slovo, řezy, znaků a emoce zjišťování obsahu činnosti, jako je například výsledky hledání. Další informace najdete v tématu [Video Indexer Preview](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview) Přehled.

Tento článek obsahuje informace a ukázku kódu můžete rychle začít s použitím [rozpoznávání emocí úrovně rozhraní API rozpoznat metoda](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) pomocí C#. Můžete ho rozpoznat emoce vyjádřená jeden nebo více osob v obraze. 

## <a name="prerequisites"></a>Požadavky
* Získá kognitivní služby [rozpoznávání emocí úrovně rozhraní API Windows SDK](https://www.nuget.org/packages/Microsoft.ProjectOxford.Emotion/).
* Získat zkušební verze [klíč předplatného](https://azure.microsoft.com/try/cognitive-services/).

## <a name="emotion-recognition-c-example-request"></a>Žádost o příklad rozpoznávání emocí úrovně rozpoznávání C#

Vytvořte nové řešení konzoly v sadě Visual Studio a potom můžete nahradit Program.cs následujícím kódem. Změna `string uri` používat oblasti, kde získat klíče pro předplatné. Nahraďte **Ocp-Apim-Subscription-Key** hodnotu s klíč platné předplatné. Chcete-li najít klíč předplatného, přejděte na portál Azure. V navigačním podokně na levé straně v části **klíče** tématu, přejděte na prostředek rozhraní API pro rozpoznávání emocí úrovně. Podobně můžete získat správné připojení URI v **přehled** panel pro vaše prostředků uvedené v části **koncový bod**.

![Klíče rozhraní API prostředků](../../media/emotion-api/keys.png)

Při zpracování odpovědi žádosti, použití knihovny jako `Newtonsoft.Json`. Tímto způsobem můžete zpracovat řetězec formátu JSON jako řadu spravovatelných objektů názvem tokeny. Chcete-li přidat tuto knihovnu do vašeho balíčku, klikněte pravým tlačítkem na projekt v Průzkumníku řešení a vyberte **spravovat balíčky Nuget**. Poté vyhledejte **Newtonsoft**. První výsledek by měl být **Newtonsoft.Json**. Vyberte **Install** (Nainstalovat). V této knihovně můžete odkazovat ve vaší aplikaci.

![Nainstalujte Newtonsoft.Json](../../media/emotion-api/newtonsoft-nuget.png)

```csharp
using System;
using System.IO;
using System.Net.Http.Headers;
using System.Net.Http;
using Newtonsoft.Json.Linq;

namespace CSHttpClientSample
{
    static class Program
    {
        static void Main()
        {
            Console.Write("Enter the path to a JPEG image file:");
            string imageFilePath = Console.ReadLine();

            MakeRequest(imageFilePath);

            Console.WriteLine("\n\n\nWait for the result below, then hit ENTER to exit...\n\n\n");
            Console.ReadLine(); // wait for ENTER to exit program
        }

        static byte[] GetImageAsByteArray(string imageFilePath)
        {
            FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
            BinaryReader binaryReader = new BinaryReader(fileStream);
            return binaryReader.ReadBytes((int)fileStream.Length);
        }

        static async void MakeRequest(string imageFilePath)
        {
            var client = new HttpClient();

            // Request headers - replace this example key with your valid key.
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "<your-subscription-key>"); // 

            // NOTE: You must use the same region in your REST call as you used to obtain your subscription keys.
            //   For example, if you obtained your subscription keys from westcentralus, replace "westus" in the 
            //   URI below with "westcentralus".
            string uri = "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize?";
            HttpResponseMessage response;
            string responseContent;

            // Request body. Try this sample with a locally stored JPEG image.
            byte[] byteData = GetImageAsByteArray(imageFilePath);

            using (var content = new ByteArrayContent(byteData))
            {
                // This example uses content type "application/octet-stream".
                // The other content types you can use are "application/json" and "multipart/form-data".
                content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
                response = await client.PostAsync(uri, content);
                responseContent = response.Content.ReadAsStringAsync().Result;
            }

            // A peek at the raw JSON response.
            Console.WriteLine(responseContent);

            // Processing the JSON into manageable objects.
            JToken rootToken = JArray.Parse(responseContent).First;

            // First token is always the faceRectangle identified by the API.
            JToken faceRectangleToken = rootToken.First;

            // Second token is all emotion scores.
            JToken scoresToken = rootToken.Last;

            // Show all face rectangle dimensions
            JEnumerable<JToken> faceRectangleSizeList = faceRectangleToken.First.Children();
            foreach (var size in faceRectangleSizeList) {
                Console.WriteLine(size);
            }

            // Show all scores
            JEnumerable<JToken> scoreList = scoresToken.First.Children();
            foreach (var score in scoreList) {
                Console.WriteLine(score);
            }
        }
    }
}
```

## <a name="recognize-emotions-sample-response"></a>Rozpoznat, že emoce ukázková odpověď
Úspěšné volání vrátí pole položek vzhled a jejich přidružené rozpoznávání emocí úrovně skóre. Že jsou seřazeny podle velikosti obdélníku řez v sestupném pořadí. Prázdnou odpověď označuje, že nebyly zjištěny žádné řezy. Položka rozpoznávání emocí úrovně obsahuje následující pole:

* faceRectangle: obdélníku umístění řez v bitové kopii
* skóre: rozpoznávání emocí úrovně skóre pro každý řez v bitové kopii 

```json
application/json 
[
  {
    "faceRectangle": {
      "left": 68,
      "top": 97,
      "width": 64,
      "height": 97
    },
    "scores": {
      "anger": 0.00300731952,
      "contempt": 5.14648448E-08,
      "disgust": 9.180124E-06,
      "fear": 0.0001912825,
      "happiness": 0.9875571,
      "neutral": 0.0009861537,
      "sadness": 1.889955E-05,
      "surprise": 0.008229999
    }
  }
]
