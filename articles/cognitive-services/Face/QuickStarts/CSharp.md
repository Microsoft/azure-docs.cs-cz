---
title: 'Rychlý start: Rozpoznávání tváří v obrázku pomocí rozhraní Azure REST API aC#'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu použijete Azure REST API pro rozpoznávání tváře s C# k rozpoznávání tváří v obrázku.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 11/09/2018
ms.author: pafarley
ms.openlocfilehash: 587005fa183c11a24ea2083ee28c8faca616b4a4
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55866640"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-c"></a>Rychlý start: Rozpoznávání tváří v obrázku pomocí rozhraní REST API pro rozpoznávání tváře aC#

V tomto rychlém startu použijete Azure REST API pro rozpoznávání tváře s C# k detekci lidských tváří v obrázku.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

## <a name="prerequisites"></a>Požadavky

- Klíč rozhraní API pro rozpoznávání tváře předplatného. Můžete získat bezplatné předplatné zkušební verze klíče z [zkuste služby Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Nebo, postupujte podle pokynů v [vytvoření účtu služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) k odběru služby API pro rozpoznávání tváře a získejte klíč.
- Libovolná edice sady [Visual Studio 2015 nebo 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Vytvoření projektu sady Visual Studio

1. V sadě Visual Studio vytvořte nový **Konzolová aplikace (.NET Framework)** projektu a pojmenujte ho **FaceDetection**. 
1. Pokud vaše řešení obsahuje i jiné projekty, vyberte tento projekt jako jediný spouštěný projekt.

## <a name="add-face-detection-code"></a>Přidejte kód pro rozpoznávání tváře detekce

Otevřete nový projekt *Program.cs* souboru. Zde přidejte kód potřebný k načtení bitové kopie a rozpoznávání tváří.

### <a name="include-namespaces"></a>Zahrnutí oborů názvů

Na začátek souboru *Program.cs* přidejte následující příkazy `using`.

```csharp
using System;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
```

### <a name="add-essential-fields"></a>Přidat nezbytné pole

Do třídy **Program** přidejte následující pole. Tato data Určuje, jak se připojit ke službě pro rozpoznávání tváře a kde se stáhnout vstupní data. Budete muset aktualizovat `subscriptionKey` pole s hodnotou váš klíč předplatného a může být nutné změnit `uriBase` řetězec tak, aby obsahoval oblasti správný identifikátor (najdete v článku [dokumenty k rozhraní API pro rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) seznam všech oblastí Koncové body).


```csharp
// Replace <Subscription Key> with your valid subscription key.
const string subscriptionKey = "<Subscription Key>";

// NOTE: You must use the same region in your REST call as you used to
// obtain your subscription keys. For example, if you obtained your
// subscription keys from westus, replace "westcentralus" in the URL
// below with "westus".
//
// Free trial subscription keys are generated in the "westus" region.
// If you use a free trial subscription key, you shouldn't need to change
// this region.
const string uriBase =
    "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect";
```

### <a name="receive-image-input"></a>Vstup bitové kopie

Přidejte následující kód, který **hlavní** metodu **Program** třídy. Tato výzva zapisuje do konzoly s dotazem, aby uživatel zadal adresu URL obrázku. Potom volá jinou metodu **MakeAnalysisRequest**, zpracování obrázků v tomto umístění.

```csharp
// Get the path and filename to process from the user.
Console.WriteLine("Detect faces:");
Console.Write(
    "Enter the path to an image with faces that you wish to analyze: ");
string imageFilePath = Console.ReadLine();

if (File.Exists(imageFilePath))
{
    try
    {
        MakeAnalysisRequest(imageFilePath);
        Console.WriteLine("\nWait a moment for the results to appear.\n");
    }
    catch (Exception e)
    {
        Console.WriteLine("\n" + e.Message + "\nPress Enter to exit...\n");
    }
}
else
{
    Console.WriteLine("\nInvalid file path.\nPress Enter to exit...\n");
}
Console.ReadLine();
```

### <a name="call-the-face-detection-rest-api"></a>Volání rozhraní REST API pro rozpoznávání tváře

Do třídy **Program** přidejte následující metodu. Sestaví volání REST API pro rozpoznávání tváře k detekci informací o rozpoznávání tváře ve bitovou vzdálenou ( `requestParameters` řetězec Určuje, které pro rozpoznávání tváře atributy k načtení). Pak zapíše výstupní data na řetězec formátu JSON.

Pomocné metody budou definovat v následujících krocích.

```csharp
// Gets the analysis of the specified image by using the Face REST API.
static async void MakeAnalysisRequest(string imageFilePath)
{
    HttpClient client = new HttpClient();

    // Request headers.
    client.DefaultRequestHeaders.Add(
        "Ocp-Apim-Subscription-Key", subscriptionKey);

    // Request parameters. A third optional parameter is "details".
    string requestParameters = "returnFaceId=true&returnFaceLandmarks=false" +
        "&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses," +
        "emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";

    // Assemble the URI for the REST API Call.
    string uri = uriBase + "?" + requestParameters;

    HttpResponseMessage response;

    // Request body. Posts a locally stored JPEG image.
    byte[] byteData = GetImageAsByteArray(imageFilePath);

    using (ByteArrayContent content = new ByteArrayContent(byteData))
    {
        // This example uses content type "application/octet-stream".
        // The other content types you can use are "application/json"
        // and "multipart/form-data".
        content.Headers.ContentType =
            new MediaTypeHeaderValue("application/octet-stream");

        // Execute the REST API call.
        response = await client.PostAsync(uri, content);

        // Get the JSON response.
        string contentString = await response.Content.ReadAsStringAsync();

        // Display the JSON response.
        Console.WriteLine("\nResponse:\n");
        Console.WriteLine(JsonPrettyPrint(contentString));
        Console.WriteLine("\nPress Enter to exit...");
    }
}
```

### <a name="process-the-input-image-data"></a>Zpracování dat vstupního obrázku

Do třídy **Program** přidejte následující metodu. To převede obrázek ze zadané adresy URL do bajtového pole.

```csharp
// Returns the contents of the specified file as a byte array.
static byte[] GetImageAsByteArray(string imageFilePath)
{
    using (FileStream fileStream =
        new FileStream(imageFilePath, FileMode.Open, FileAccess.Read))
    {
        BinaryReader binaryReader = new BinaryReader(fileStream);
        return binaryReader.ReadBytes((int)fileStream.Length);
    }
}
```

### <a name="parse-the-json-response"></a>Analyzovat odpověď JSON

Do třídy **Program** přidejte následující metodu. Formát JSON vstupu, aby byly snadněji čitelné. Vaše aplikace zapíše data tento řetězec do konzoly.

```csharp
// Formats the given JSON string by adding line breaks and indents.
static string JsonPrettyPrint(string json)
{
    if (string.IsNullOrEmpty(json))
        return string.Empty;

    json = json.Replace(Environment.NewLine, "").Replace("\t", "");

    StringBuilder sb = new StringBuilder();
    bool quote = false;
    bool ignore = false;
    int offset = 0;
    int indentLength = 3;

    foreach (char ch in json)
    {
        switch (ch)
        {
            case '"':
                if (!ignore) quote = !quote;
                break;
            case '\'':
                if (quote) ignore = !ignore;
                break;
        }

        if (quote)
            sb.Append(ch);
        else
        {
            switch (ch)
            {
                case '{':
                case '[':
                    sb.Append(ch);
                    sb.Append(Environment.NewLine);
                    sb.Append(new string(' ', ++offset * indentLength));
                    break;
                case '}':
                case ']':
                    sb.Append(Environment.NewLine);
                    sb.Append(new string(' ', --offset * indentLength));
                    sb.Append(ch);
                    break;
                case ',':
                    sb.Append(ch);
                    sb.Append(Environment.NewLine);
                    sb.Append(new string(' ', offset * indentLength));
                    break;
                case ':':
                    sb.Append(ch);
                    sb.Append(' ');
                    break;
                default:
                    if (ch != ' ') sb.Append(ch);
                    break;
            }
        }
    }

    return sb.ToString().Trim();
}
```

## <a name="run-the-app"></a>Spuštění aplikace

Úspěšná odpověď se zobrazí data pro rozpoznávání tváře ve snadno čitelném formátu JSON. Příklad:

```json
[
   {
      "faceId": "f7eda569-4603-44b4-8add-cd73c6dec644",
      "faceRectangle": {
         "top": 131,
         "left": 177,
         "width": 162,
         "height": 162
      },
      "faceAttributes": {
         "smile": 0.0,
         "headPose": {
            "pitch": 0.0,
            "roll": 0.1,
            "yaw": -32.9
         },
         "gender": "female",
         "age": 22.9,
         "facialHair": {
            "moustache": 0.0,
            "beard": 0.0,
            "sideburns": 0.0
         },
         "glasses": "NoGlasses",
         "emotion": {
            "anger": 0.0,
            "contempt": 0.0,
            "disgust": 0.0,
            "fear": 0.0,
            "happiness": 0.0,
            "neutral": 0.986,
            "sadness": 0.009,
            "surprise": 0.005
         },
         "blur": {
            "blurLevel": "low",
            "value": 0.06
         },
         "exposure": {
            "exposureLevel": "goodExposure",
            "value": 0.67
         },
         "noise": {
            "noiseLevel": "low",
            "value": 0.0
         },
         "makeup": {
            "eyeMakeup": true,
            "lipMakeup": true
         },
         "accessories": [

         ],
         "occlusion": {
            "foreheadOccluded": false,
            "eyeOccluded": false,
            "mouthOccluded": false
         },
         "hair": {
            "bald": 0.0,
            "invisible": false,
            "hairColor": [
               {
                  "color": "brown",
                  "confidence": 1.0
               },
               {
                  "color": "black",
                  "confidence": 0.87
               },
               {
                  "color": "other",
                  "confidence": 0.51
               },
               {
                  "color": "blond",
                  "confidence": 0.08
               },
               {
                  "color": "red",
                  "confidence": 0.08
               },
               {
                  "color": "gray",
                  "confidence": 0.02
               }
            ]
         }
      }
   }
]
```

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili jednoduchou konzolovou aplikaci .NET, využívající volání REST pomocí rozhraní API pro rozpoznávání tváře Azure rozpoznávání tváří v obrázku a vrátíte se jejich atributy. Referenční dokumentace rozhraní API pro rozpoznávání tváře získat další informace o podporovaných scénářích dále prozkoumejte.

> [!div class="nextstepaction"]
> [Rozhraní API pro rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
