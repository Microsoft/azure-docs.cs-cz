---
title: 'Rychlý start: Vygenerování miniatury – SDK, C# – Počítačové zpracování obrazu'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu vygenerujete miniaturu z obrázku pomocí klientské knihovny C# systému Windows počítačového zpracování obrazu.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 09/14/2018
ms.author: nolachar
ms.openlocfilehash: 8fdbcf5bfe4d4fe60a2858b34b38c01d66e75d99
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47054808"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-sdk-and-c"></a>Rychlý start: Vygenerování miniatury pomocí sady SDK počítačového zpracování obrazu a C#

V tomto rychlém startu vygenerujete miniaturu z obrázku pomocí klientské knihovny systému Windows počítačového zpracování obrazu.

Zdrojový kód k této ukázce je dostupný na [Githubu](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/ComputerVision).

## <a name="prerequisites"></a>Požadavky

* Abyste mohli počítačové zpracování obrazu použít, potřebujete klíč předplatného. Přečtěte si, [jak získat klíče předplatného](../Vision-API-How-to-Topics/HowToSubscribe.md).
* Libovolná edice sady [Visual Studio 2015 nebo 2017](https://www.visualstudio.com/downloads/).
* Balíček NuGet klientské knihovny [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision). Stažení tohoto balíčku není nutné. Pokyny k instalaci jsou uvedené dál.

## <a name="generatethumbnailasync-method"></a>Metoda GenerateThumbnailAsync

Metody `GenerateThumbnailAsync` a `GenerateThumbnailInStreamAsync` využívají [rozhraní API pro získání miniatur](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) pro vzdálené a místní obrázky, v uvedeném pořadí.  Tyto metody můžete použít k vygenerování miniatury obrázku. Zadáte výšku a šířku, které se mohou od poměru stran vstupního obrázku lišit. Počítačové zpracování obrazu použije chytré oříznutí, které inteligentně identifikuje oblast zájmu a na základě této oblasti vygeneruje souřadnice oříznutí.

Pokud chcete spustit ukázku, postupujte takto:

1. V sadě Visual Studio vytvořte novou konzolovou aplikaci Visual C#.
1. Nainstalujte balíček NuGet klientské knihovny počítačového zpracování obrazu.
    1. V nabídce klikněte na **Nástroje** vyberte **Správce balíčků NuGet** a potom **Spravovat balíčky NuGet pro řešení**.
    1. Klikněte na kartu **Procházet** a do pole **Hledat** zadejte Microsoft.Azure.CognitiveServices.Vision.ComputerVision.
    1. Jakmile se zobrazí, vyberte **Microsoft.Azure.CognitiveServices.Vision.ComputerVision**, potom klikněte na zaškrtávací políčko vedle názvu vašeho projektu a na **Nainstalovat**.
1. `Program.cs` nahraďte následujícím kódem.
1. Místo `<Subscription Key>` použijte platný klíč předplatného.
1. V případě potřeby změňte `computerVision.Endpoint` na oblast Azure spojenou s vašimi klíči předplatného.
1. Volitelně můžete nahradit `<LocalImage>` cestou a názvem místního obrázku (pokud není nastaveno, bude se ignorovat).
1. Volitelně můžete `remoteImageUrl` nastavit na jiný obrázek.
1. Volitelně můžete nastavit `writeThumbnailToDisk` na `true` a uložit tak miniaturu na disk.
1. Spusťte program.

```csharp
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;

using System;
using System.IO;
using System.Threading.Tasks;

namespace ImageThumbnail
{
    class Program
    {
        private const bool writeThumbnailToDisk = false;

        // subscriptionKey = "0123456789abcdef0123456789ABCDEF"
        private const string subscriptionKey = "<SubscriptionKey>";

        // localImagePath = @"C:\Documents\LocalImage.jpg"
        private const string localImagePath = @"<LocalImage>";

        private const string remoteImageUrl =
            "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg";

        private const int thumbnailWidth = 100;
        private const int thumbnailHeight = 100;

        static void Main(string[] args)
        {
            ComputerVisionClient computerVision = new ComputerVisionClient(
                new ApiKeyServiceClientCredentials(subscriptionKey),
                new System.Net.Http.DelegatingHandler[] { });

            // You must use the same region as you used to get your subscription
            // keys. For example, if you got your subscription keys from westus,
            // replace "westcentralus" with "westus".
            //
            // Free trial subscription keys are generated in the westcentralus
            // region. If you use a free trial subscription key, you shouldn't
            // need to change the region.

            // Specify the Azure region
            computerVision.Endpoint = "https://westcentralus.api.cognitive.microsoft.com";

            Console.WriteLine("Images being analyzed ...\n");
            var t1 = GetRemoteThumbnailAsync(computerVision, remoteImageUrl);
            var t2 = GetLocalThumbnailAsnc(computerVision, localImagePath);

            Task.WhenAll(t1, t2).Wait(5000);
            Console.WriteLine("Press ENTER to exit");
            Console.ReadLine();
        }

        // Create a thumbnail from a remote image
        private static async Task GetRemoteThumbnailAsync(
            ComputerVisionClient computerVision, string imageUrl)
        {
            if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
            {
                Console.WriteLine(
                    "\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
                return;
            }

            Stream thumbnail = await computerVision.GenerateThumbnailAsync(
                thumbnailWidth, thumbnailHeight, imageUrl, true);

            string path = Environment.CurrentDirectory;
            string imageName = imageUrl.Substring(imageUrl.LastIndexOf('/') + 1);
            string thumbnailFilePath =
                path + "\\" + imageName.Insert(imageName.Length - 4, "_thumb");

            // Save the thumbnail to the current working directory,
            // using the original name with the suffix "_thumb".
            SaveThumbnail(thumbnail, thumbnailFilePath);
        }

        // Create a thumbnail from a local image
        private static async Task GetLocalThumbnailAsnc(
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
                Stream thumbnail = await computerVision.GenerateThumbnailInStreamAsync(
                    thumbnailWidth, thumbnailHeight, imageStream, true);

                string thumbnailFilePath =
                    localImagePath.Insert(localImagePath.Length - 4, "_thumb");

                // Save the thumbnail to the same folder as the original image,
                // using the original name with the suffix "_thumb".
                SaveThumbnail(thumbnail, thumbnailFilePath);
            }
        }

        // Save the thumbnail locally.
        // NOTE: This will overwrite an existing file of the same name.
        private static void SaveThumbnail(Stream thumbnail, string thumbnailFilePath)
        {
            if (writeThumbnailToDisk)
            {
                using (Stream file = File.Create(thumbnailFilePath))
                {
                    thumbnail.CopyTo(file);
                }
            }
            Console.WriteLine("Thumbnail {0} written to: {1}\n",
                writeThumbnailToDisk ? "" : "NOT", thumbnailFilePath);
        }
    }
}
```

## <a name="generatethumbnailasync-response"></a>Odpověď metody GenerateThumbnailAsync

Úspěšná odpověď uloží miniaturu každého obrázku místně a zobrazí umístění této miniatury, například:

```cmd
Thumbnail written to: C:\Documents\LocalImage_thumb.jpg

Thumbnail written to: ...\bin\Debug\Bloodhound_Puppy_thumb.jpg
```

## <a name="next-steps"></a>Další kroky

Prozkoumejte rozhraní API pro počítačové zpracování obrazu používané pro analýzu obrázku, zjišťování celebrit a památek, vytvoření miniatury a extrahování tištěného a ručně psaného textu.

> [!div class="nextstepaction"]
> [Prozkoumat rozhraní API pro počítačové zpracování obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
