---
title: 'Rychlý start: Extrahujte rukou psaný text - SDKC#'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu extrahujete text z obrázku pomocí klientské knihovny počítačového zpracování obrazu systému Windows.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 09/27/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ca11c25f5e2dc793bc3c8a4ee10b4a89139ce1f3
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55879220"
---
# <a name="quickstart-extract-text-using-the-computer-vision-sdk-and-c"></a>Rychlý start: Extrahovat text pomocí sady SDK služby pro zpracování obrazu počítač aC#

V tomto rychlém startu extrahujete rukou psaný nebo tištěný text z obrázku pomocí klientské knihovny počítačového zpracování obrazu systému Windows.

## <a name="prerequisites"></a>Požadavky

* Abyste mohli počítačové zpracování obrazu použít, potřebujete klíč předplatného. Přečtěte si, [jak získat klíče předplatného](../Vision-API-How-to-Topics/HowToSubscribe.md).
* Libovolná edice sady [Visual Studio 2015 nebo 2017](https://www.visualstudio.com/downloads/).
* Balíček NuGet klientské knihovny [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision). Stažení tohoto balíčku není nutné. Pokyny k instalaci jsou uvedené dál.

## <a name="recognizetextasync-method"></a>Metoda RecognizeTextAsync

> [!TIP]
> Získejte z [GitHubu](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/ComputerVision) nejnovější verzi kódu jako řešení sady Visual Studio.

Metody `RecognizeTextAsync` a `RecognizeTextInStreamAsync` využívají [rozhraní API pro rozpoznávání textu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) pro vzdálené a místní obrázky, v uvedeném pořadí. Metoda `GetTextOperationResultAsync` používá [rozhraní API pro získání výsledků operace rozpoznávání textu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201).  Pomocí těchto metod můžete rozpoznat text v obrázku a extrahovat rozpoznané znaky do datového proudu se strojově využitelnými znaky.

Pokud chcete spustit ukázku, postupujte takto:

1. V sadě Visual Studio vytvořte novou konzolovou aplikaci Visual C#.
1. Nainstalujte balíček NuGet klientské knihovny počítačového zpracování obrazu.
    1. V nabídce klikněte na **Nástroje** vyberte **Správce balíčků NuGet** a potom **Spravovat balíčky NuGet pro řešení**.
    1. Klikněte na kartu **Procházet** a do pole **Hledat** zadejte Microsoft.Azure.CognitiveServices.Vision.ComputerVision.
    1. Jakmile se zobrazí, vyberte **Microsoft.Azure.CognitiveServices.Vision.ComputerVision**, potom klikněte na zaškrtávací políčko vedle názvu vašeho projektu a na **Nainstalovat**.
1. `Program.cs` nahraďte následujícím kódem.
1. Místo `<Subscription Key>` použijte platný klíč předplatného.
1. V případě potřeby změňte `computerVision.Endpoint` na oblast Azure spojenou s klíči vašeho předplatného.
1. Můžete nastavit `textRecognitionMode` na `TextRecognitionMode.Printed`.
1. `<LocalImage>` nahraďte cestou a názvem souboru místního obrázku.
1. Volitelně můžete `remoteImageUrl` nastavit na jiný obrázek.
1. Spusťte program.

```csharp
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;

using System;
using System.IO;
using System.Threading.Tasks;

namespace ExtractText
{
    class Program
    {
        // subscriptionKey = "0123456789abcdef0123456789ABCDEF"
        private const string subscriptionKey = "<SubscriptionKey>";

        // For printed text, change to TextRecognitionMode.Printed
        private const TextRecognitionMode textRecognitionMode =
            TextRecognitionMode.Handwritten;

        // localImagePath = @"C:\Documents\LocalImage.jpg"
        private const string localImagePath = @"<LocalImage>";

        private const string remoteImageUrl =
            "https://upload.wikimedia.org/wikipedia/commons/thumb/d/dd/" +
            "Cursive_Writing_on_Notebook_paper.jpg/" +
            "800px-Cursive_Writing_on_Notebook_paper.jpg";

        private const int numberOfCharsInOperationId = 36;

        static void Main(string[] args)
        {
            ComputerVisionClient computerVision = new ComputerVisionClient(
                new ApiKeyServiceClientCredentials(subscriptionKey),
                new System.Net.Http.DelegatingHandler[] { });

            // You must use the same region as you used to get your subscription
            // keys. For example, if you got your subscription keys from westus,
            // replace "westcentralus" with "westus".
            //
            // Free trial subscription keys are generated in the "westus"
            // region. If you use a free trial subscription key, you shouldn't
            // need to change the region.

            // Specify the Azure region
            computerVision.Endpoint = "https://westcentralus.api.cognitive.microsoft.com";

            Console.WriteLine("Images being analyzed ...");
            var t1 = ExtractRemoteTextAsync(computerVision, remoteImageUrl);
            var t2 = ExtractLocalTextAsync(computerVision, localImagePath);

            Task.WhenAll(t1, t2).Wait(5000);
            Console.WriteLine("Press ENTER to exit");
            Console.ReadLine();
        }

        // Recognize text from a remote image
        private static async Task ExtractRemoteTextAsync(
            ComputerVisionClient computerVision, string imageUrl)
        {
            if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
            {
                Console.WriteLine(
                    "\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
                return;
            }

            // Start the async process to recognize the text
            RecognizeTextHeaders textHeaders =
                await computerVision.RecognizeTextAsync(
                    imageUrl, textRecognitionMode);

            await GetTextAsync(computerVision, textHeaders.OperationLocation);
        }

        // Recognize text from a local image
        private static async Task ExtractLocalTextAsync(
            ComputerVisionClient computerVision, string imagePath)
        {
            if (!File.Exists(imagePath))
            {
                Console.WriteLine(
                    "\nUnable to open or read localImagePath:\n{0} \n", imagePath);
                return;
            }

            using (Stream imageStream = File.OpenRead(imagePath))
            {
                // Start the async process to recognize the text
                RecognizeTextInStreamHeaders textHeaders =
                    await computerVision.RecognizeTextInStreamAsync(
                        imageStream, textRecognitionMode);

                await GetTextAsync(computerVision, textHeaders.OperationLocation);
            }
        }

        // Retrieve the recognized text
        private static async Task GetTextAsync(
            ComputerVisionClient computerVision, string operationLocation)
        {
            // Retrieve the URI where the recognized text will be
            // stored from the Operation-Location header
            string operationId = operationLocation.Substring(
                operationLocation.Length - numberOfCharsInOperationId);

            Console.WriteLine("\nCalling GetHandwritingRecognitionOperationResultAsync()");
            TextOperationResult result =
                await computerVision.GetTextOperationResultAsync(operationId);

            // Wait for the operation to complete
            int i = 0;
            int maxRetries = 10;
            while ((result.Status == TextOperationStatusCodes.Running ||
                    result.Status == TextOperationStatusCodes.NotStarted) && i++ < maxRetries)
            {
                Console.WriteLine(
                    "Server status: {0}, waiting {1} seconds...", result.Status, i);
                await Task.Delay(1000);

                result = await computerVision.GetTextOperationResultAsync(operationId);
            }

            // Display the results
            Console.WriteLine();
            var lines = result.RecognitionResult.Lines;
            foreach (Line line in lines)
            {
                Console.WriteLine(line.Text);
            }
            Console.WriteLine();
        }
    }
}
```

## <a name="recognizetextasync-response"></a>Odpověď metody RecognizeTextAsync

Úspěšná odpověď zobrazí řádky rozpoznaného textu jednotlivých obrázků.

Zobrazit [rychlý start: Extrahujte rukou psaný text - REST, C# ](../QuickStarts/CSharp-hand-text.md#examine-the-response) příklad nezpracovaném formátu JSON výstupu.

```cmd
Calling GetHandwritingRecognitionOperationResultAsync()

Calling GetHandwritingRecognitionOperationResultAsync()
Server status: Running, waiting 1 seconds...
Server status: Running, waiting 1 seconds...

dog
The quick brown fox jumps over the lazy
Pack my box with five dozen liquor jugs
```

## <a name="next-steps"></a>Další postup

Prozkoumejte rozhraní API pro počítačové zpracování obrazu používané pro analýzu obrázku, zjišťování celebrit a památek, vytvoření miniatury a extrahování tištěného a ručně psaného textu.

> [!div class="nextstepaction"]
> [Prozkoumat rozhraní API pro počítačové zpracování obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
