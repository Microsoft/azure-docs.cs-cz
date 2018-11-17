---
title: 'Rychlý start: Analýza obrázku – SDK, C# – Počítačové zpracování obrazu'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu analyzujete obrázek pomocí klientské knihovny C# systému Windows počítačového zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 09/14/2018
ms.author: pafarley
ms.openlocfilehash: 7de7a87c73c2dabddf2268a6a0bc2992d6017e4d
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2018
ms.locfileid: "51853927"
---
# <a name="quickstart-analyze-an-image-using-the-computer-vision-sdk-and-c"></a>Rychlý start: Analýza obrázku s použitím sady SDK pro počítačové zpracování obrazu a C#

V tomto rychlém startu analyzujete místní a vzdálený obrázek za účelem extrakce vizuálních prvků pomocí klientské knihovny Windows počítačového zpracování obrazu.

## <a name="prerequisites"></a>Požadavky

* Abyste mohli počítačové zpracování obrazu použít, potřebujete klíč předplatného. Přečtěte si, [jak získat klíče předplatného](../Vision-API-How-to-Topics/HowToSubscribe.md).
* Libovolná edice sady [Visual Studio 2015 nebo 2017](https://www.visualstudio.com/downloads/).
* Balíček NuGet klientské knihovny [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision). Stažení tohoto balíčku není nutné. Pokyny k instalaci jsou uvedené dál.

## <a name="analyzeimageasync-method"></a>Metoda AnalyzeImageAsync

> [!TIP]
> Získejte z [GitHubu](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/ComputerVision) nejnovější verzi kódu jako řešení sady Visual Studio.

Metody `AnalyzeImageAsync` a `AnalyzeImageInStreamAsync` využívají [rozhraní API pro analýzu obrázků](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) pro vzdálené a místní obrázky, v uvedeném pořadí. Tyto metody můžete použít k extrakci vizuálních funkcí na základě obsahu obrázku a rozhodnutí, které funkce se mají vrátit, včetně:

* Podrobného seznamu značek souvisejících s obsahem obrázku
* Popisu obsahu obrázku v celé větě
* Souřadnic, pohlaví a věku veškerých obličejů na obrázku
* Typu obrázku (klipart nebo perokresba)
* Převládající barvy, doplňkové barvy a toho, jestli je obrázek černobílý
* Kategorie definované v této [taxonomii](../Category-Taxonomy.md)
* Je obsah obrázku určený pro dospělé nebo se jedná o sexuálně sugestivní obsah?

Pokud chcete spustit ukázku, postupujte takto:

1. V sadě Visual Studio vytvořte novou konzolovou aplikaci Visual C#.
1. Nainstalujte balíček NuGet klientské knihovny počítačového zpracování obrazu.
    1. V nabídce klikněte na **Nástroje** vyberte **Správce balíčků NuGet** a potom **Spravovat balíčky NuGet pro řešení**.
    1. Klikněte na kartu **Procházet** a do pole **Hledat** zadejte Microsoft.Azure.CognitiveServices.Vision.ComputerVision.
    1. Jakmile se zobrazí, vyberte **Microsoft.Azure.CognitiveServices.Vision.ComputerVision**, potom klikněte na zaškrtávací políčko vedle názvu vašeho projektu a na **Nainstalovat**.
1. `Program.cs` nahraďte následujícím kódem.
1. Místo `<Subscription Key>` použijte platný klíč předplatného.
1. V případě potřeby změňte `computerVision.Endpoint` na oblast Azure spojenou s klíči vašeho předplatného.
1. `<LocalImage>` nahraďte cestou a názvem souboru místního obrázku.
1. Volitelně můžete `remoteImageUrl` nastavit na jiný obrázek.
1. Spusťte program.

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
            "http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg";

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
            Console.WriteLine(analysis.Description.Captions[0].Text + "\n");
        }
    }
}
```

## <a name="analyzeimageasync-response"></a>Odpověď metody AnalyzeImageAsync

Úspěšná odpověď zobrazí nejrelevantnější titulek každého obrázku.

Příklad nezpracovaného výstupu JSON najdete v [rychlých startech pro rozhraní API a analýzu místního obrázku s C#](../QuickStarts/CSharp-analyze.md#examine-the-response).

```
http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg
a large waterfall over a rocky cliff
```

## <a name="next-steps"></a>Další postup

Prozkoumejte rozhraní API pro počítačové zpracování obrazu používané pro analýzu obrázku, zjišťování celebrit a památek, vytvoření miniatury a extrahování tištěného a ručně psaného textu.

> [!div class="nextstepaction"]
> [Prozkoumat rozhraní API pro počítačové zpracování obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
