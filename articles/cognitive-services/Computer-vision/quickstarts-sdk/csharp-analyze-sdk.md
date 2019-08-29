---
title: 'Rychlý start: Analyzovat image – sada SDKC#'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu analyzujete obrázek pomocí klientské knihovny C# systému Windows počítačového zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 59641973a953d1db501f7b660b04df900bc55cb0
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141023"
---
# <a name="quickstart-analyze-an-image-using-the-computer-vision-sdk-and-c"></a>Rychlý start: Analýza obrázku pomocí sady Počítačové zpracování obrazu SDK aC#

V tomto rychlém startu budete analyzovat místní i vzdálenou image pro extrakci vizuálních funkcí pomocí klientské knihovny Počítačové zpracování obrazu pro C#. Pokud chcete, můžete si kód v této příručce stáhnout jako kompletní ukázkovou aplikaci z Cognitive Servicesho úložiště [Vision](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/ComputerVision) na GitHubu.

## <a name="prerequisites"></a>Požadavky

* Počítačové zpracování obrazu klíč předplatného. Můžete získat bezplatné předplatné zkušební verze klíče z [zkuste služby Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Případně postupujte podle pokynů v části [Vytvoření účtu Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pro přihlášení k odběru počítačové zpracování obrazu a získání klíče. Pak [vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro řetězec klíčového a koncového bodu služby s `COMPUTER_VISION_SUBSCRIPTION_KEY` názvem `COMPUTER_VISION_ENDPOINT`a v uvedeném pořadí.
* Libovolná edice sady [Visual Studio 2015 nebo 2017](https://www.visualstudio.com/downloads/).
* Balíček NuGet klientské knihovny [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision). Stažení tohoto balíčku není nutné. Pokyny k instalaci jsou uvedené dál.

## <a name="create-and-run-the-sample-application"></a>Vytvoření a spuštění ukázkové aplikace

Pokud chcete spustit ukázku, postupujte takto:

1. V sadě Visual Studio vytvořte novou konzolovou aplikaci Visual C#.
1. Nainstalujte balíček NuGet klientské knihovny počítačového zpracování obrazu.
    1. V nabídce klikněte na **Nástroje** vyberte **Správce balíčků NuGet** a potom **Spravovat balíčky NuGet pro řešení**.
    1. Klikněte na kartu **Procházet** a do pole **Hledat** zadejte Microsoft.Azure.CognitiveServices.Vision.ComputerVision.
    1. Jakmile se zobrazí, vyberte **Microsoft.Azure.CognitiveServices.Vision.ComputerVision**, potom klikněte na zaškrtávací políčko vedle názvu vašeho projektu a na **Nainstalovat**.
1. Obsah *program.cs* nahraďte následujícím kódem. Metody `AnalyzeImageAsync` a`AnalyzeImageInStreamAsync` zabalí [REST API analyzovat bitovou kopii](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) pro vzdálené a místní image v uvedeném pořadí.

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;

    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading.Tasks;

    namespace ImageAnalyze
    {
        class Program
        {
            // subscriptionKey = "0123456789abcdef0123456789ABCDEF"
            private const string subscriptionKey = "<SubscriptionKey>";

            // localImagePath = @"C:\Documents\LocalImage.jpg"
            private const string localImagePath = @"<LocalImage>";

            private const string remoteImageUrl =
                "https://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg";

            // Specify the features to return
            private static readonly List<VisualFeatureTypes> features =
                new List<VisualFeatureTypes>()
            {
                VisualFeatureTypes.Categories, VisualFeatureTypes.Description,
                VisualFeatureTypes.Faces, VisualFeatureTypes.ImageType,
                VisualFeatureTypes.Tags
            };

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
                var t1 = AnalyzeRemoteAsync(computerVision, remoteImageUrl);
                var t2 = AnalyzeLocalAsync(computerVision, localImagePath);

                Task.WhenAll(t1, t2).Wait(5000);
                Console.WriteLine("Press ENTER to exit");
                Console.ReadLine();
            }

            // Analyze a remote image
            private static async Task AnalyzeRemoteAsync(
                ComputerVisionClient computerVision, string imageUrl)
            {
                if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
                {
                    Console.WriteLine(
                        "\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
                    return;
                }

                ImageAnalysis analysis =
                    await computerVision.AnalyzeImageAsync(imageUrl, features);
                DisplayResults(analysis, imageUrl);
            }

            // Analyze a local image
            private static async Task AnalyzeLocalAsync(
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
                    ImageAnalysis analysis = await computerVision.AnalyzeImageInStreamAsync(
                        imageStream, features);
                    DisplayResults(analysis, imagePath);
                }
            }

            // Display the most relevant caption for the image
            private static void DisplayResults(ImageAnalysis analysis, string imageUri)
            {
                Console.WriteLine(imageUri);
                if (analysis.Description.Captions.Count != 0)
                {
                    Console.WriteLine(analysis.Description.Captions[0].Text + "\n");
                }
                else
                {
                    Console.WriteLine("No description generated.");
                }
            }
        }
    }
    ```

1. Místo `<Subscription Key>` použijte platný klíč předplatného.
1. V případě potřeby změňte `computerVision.Endpoint` na oblast Azure spojenou s klíči vašeho předplatného.
1. `<LocalImage>` nahraďte cestou a názvem souboru místního obrázku.
1. Volitelně můžete nastavit `remoteImageUrl` na jinou adresu URL obrázku.
1. Spusťte program.

## <a name="examine-the-response"></a>Prozkoumání odpovědi

Úspěšná odpověď zobrazí nejrelevantnější titulek každého obrázku. Můžete změnit `DisplayResults` metodu na výstup různých dat obrázku. Další informace najdete v tématu metoda [AnalyzeLocalAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions.analyzeimageinstreamasync?view=azure-dotnet) .

Viz [rychlé starty rozhraní API: Analyzovat místní image s C# ](../QuickStarts/CSharp-analyze.md#examine-the-response) příkladem nezpracovaného výstupu JSON.

```console
https://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg
a large waterfall over a rocky cliff
```

## <a name="next-steps"></a>Další postup

Prozkoumejte rozhraní API pro počítačové zpracování obrazu používané pro analýzu obrázku, zjišťování celebrit a památek, vytvoření miniatury a extrahování tištěného a ručně psaného textu.

> [!div class="nextstepaction"]
> [Prozkoumat rozhraní API pro počítačové zpracování obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
