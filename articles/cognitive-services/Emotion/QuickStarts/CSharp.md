---
title: 'Rychlý start: Na tváří v obrázku – rozhraní API pro rozpoznávání Emocí, rozpoznávání emocíC#'
titlesuffix: Azure Cognitive Services
description: Získejte informace a vzorový kód, které vám pomůžou rychle začít používat rozhraní API pro rozpoznávání emocí pomocí jazyka C#.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: quickstart
ms.date: 11/02/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: 827617a15f8fe6ffda795f7b26ecc50b9f0cf74b
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55883353"
---
# <a name="quickstart-build-an-app-to-recognize-emotions-on-faces-in-an-image"></a>Rychlý start: Vytvářejte aplikace pro rozpoznávání emocí na tváří v obrázku.

> [!IMPORTANT]
> Rozhraní API pro rozpoznávání emocí se přestane používat 15. února 2019. Funkce rozpoznávání emocí je teď obecně dostupná v rámci [rozhraní API pro rozpoznávání tváře](https://docs.microsoft.com/azure/cognitive-services/face/).

Tento článek obsahuje informace a vzorový kód, které vám pomůžou rychle začít používat [metodu Recognize rozhraní API pro rozpoznávání emocí](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) pomocí jazyka C#. Můžete ji použít k rozpoznávání emocí vyjadřovaných jedním člověkem nebo několika lidmi na obrázku.

## <a name="prerequisites"></a>Požadavky
* Získejte sadu [Windows SDK rozhraní API pro rozpoznávání emocí](https://www.nuget.org/packages/Microsoft.ProjectOxford.Emotion/) ve službách Cognitive Services.
* Získejte [klíč svého bezplatného předplatného](https://azure.microsoft.com/try/cognitive-services/).

## <a name="emotion-recognition-c-example-request"></a>Příklad požadavku na rozpoznávání emocí v jazyce C#

V sadě Visual Studio vytvořte nové konzolové řešení a pak nahraďte obsah souboru Program.cs následujícím kódem. Hodnotu `string uri` změňte na oblast, kde jste získali klíče předplatného. Hodnotu **Ocp-Apim-Subscription-Key** nahraďte platným klíčem předplatného. Pokud chcete zjistit klíč předplatného, přejděte na Azure Portal. V levém navigačním podokně v části **Klíče** přejděte ke svému prostředku rozhraní API pro rozpoznávání emocí. Podobně můžete získat odpovídající identifikátor URI pro připojení, a to na panelu **Přehled** vašeho prostředku v části **Koncový bod**.

![Klíče prostředku rozhraní API](../../media/emotion-api/keys.png)

Ke zpracování odpovědi na váš požadavek můžete použít knihovnu jako `Newtonsoft.Json`. Tímto způsobem můžete zpracovat řetězec JSON jako řadu spravovatelných objektů označovaných jako tokeny. Pokud chcete tuto knihovnu přidat do svého balíčku, klikněte pravým tlačítkem na projekt v Průzkumníku řešení a vyberte **Spravovat balíčky NuGet**. Pak vyhledejte **Newtonsoft**. První výsledek by měl být **Newtonsoft.Json**. Vyberte **Install** (Nainstalovat). Teď můžete na tuto knihovnu odkazovat ve své aplikaci.

![Instalace balíčku Newtonsoft.Json](../../media/emotion-api/newtonsoft-nuget.png)

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

## <a name="recognize-emotions-sample-response"></a>Ukázka odpovědi rozpoznávání emocí
Úspěšné volání vrátí pole položek tváří s přidruženým skóre rozpoznávání emocí. Tyto položky jsou seřazené podle velikosti obdélníku tváře v sestupném pořadí. Prázdná odpověď znamená, že se nezjistily žádné tváře. Položka rozpoznávání emocí obsahuje následující pole:

* faceRectangle: Obdélník umístění tvář na obrázku
* skóre: Pro každou tvář na obrázku stanoví skóre pro rozpoznávání emocí

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
