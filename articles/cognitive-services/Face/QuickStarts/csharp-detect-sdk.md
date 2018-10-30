---
title: 'Rychlý start: Rozpoznávání tváří na obrázku pomocí sady .NET SDK a jazyka C#'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu budete pomocí klientské knihovny služby Rozpoznávání tváře systému Windows s C# rozpoznávat tváře z obrázku ve službě Cognitive Services.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 09/14/2018
ms.author: pafarley
ms.openlocfilehash: a4b0b8b277ed6bc6e2bc3c7549d1e67d5f18c615
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49954959"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-net-sdk-with-c"></a>Rychlý start: Rozpoznávání tváří na obrázku pomocí sady .NET SDK a jazyka C#

V tomto rychlém startu budete pomocí klientské knihovny služby Rozpoznávání tváře systému Windows rozpoznávat lidské tváře na obrázku.

## <a name="prerequisites"></a>Požadavky

* Ke spuštění této ukázky budete potřebovat klíč předplatného. Klíče bezplatného zkušebního předplatného můžete získat v tématu [Zkuste služby Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api).
* Libovolná edice sady [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* Balíček NuGet klientské knihovny [Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview). Stažení tohoto balíčku není nutné. Pokyny k instalaci jsou uvedené dál.

## <a name="detectwithurlasync-method"></a>Metoda DetectWithUrlAsync

> [!TIP]
> Získejte z [GitHubu](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/Face) nejnovější verzi kódu jako řešení sady Visual Studio.

Metody `DetectWithUrlAsync` a `DetectWithStreamAsync` využívají [Face - Detect API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) pro vzdálené a místní obrázky (v uvedeném pořadí). Pomocí těchto metod rozpoznejte tváře na obrázku a vraťte atributy tváře, včetně:

* Face ID: jedinečná hodnota ID používaná v několika scénářích rozhraní API pro rozpoznávání tváře
* Obdélník tváře: umístění tváře na obrázku vlevo a nahoře a šířka a výška obličeje
* Rysy: soustava 27 bodů obličejových rysů odkazující na důležité polohy součástí obličeje.
* Atributy obličeje včetně věku, pohlaví, intenzity úsměvu, pozice hlavy a vousů.

Pokud chcete spustit ukázku, postupujte takto:

1. V sadě Visual Studio vytvořte novou konzolovou aplikaci Visual C#.
1. Nainstalujte balíček NuGet klientské knihovny služby Rozpoznávání tváře.
    1. V horní nabídce klikněte na **Nástroje** vyberte **Správce balíčků NuGet** a potom **Spravovat balíčky NuGet pro řešení**.
    1. Klikněte na kartu **Procházet** a potom vyberte **Zahrnout předběžnou verzi**.
    1. V poli **Vyhledat** zadejte Microsoft.Azure.CognitiveServices.Vision.Face.
    1. Vyberte **Microsoft.Azure.CognitiveServices.Vision.Face** (jakmile se zobrazí), potom klikněte na zaškrtávací políčko vedle názvu vašeho projektu a klikněte na **Nainstalovat**.
1. Soubor *Program.cs* nahraďte následujícím kódem.
1. Místo `<Subscription Key>` použijte platný klíč předplatného.
1. V případě potřeby změňte `faceEndpoint` na oblast Azure spojenou s klíči vašeho předplatného.
1. Volitelně můžete nahradit <`LocalImage>` cestou a názvem souboru místního obrázku (pokud není nastaveno, bude se ignorovat).
1. Volitelně můžete `remoteImageUrl` nastavit na jiný obrázek.
1. Spusťte program.

```csharp
using Microsoft.Azure.CognitiveServices.Vision.Face;
using Microsoft.Azure.CognitiveServices.Vision.Face.Models;

using System;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;

namespace DetectFace
{
    class Program
    {
        // subscriptionKey = "0123456789abcdef0123456789ABCDEF"
        private const string subscriptionKey = "<SubscriptionKey>";

        // You must use the same region as you used to get your subscription
        // keys. For example, if you got your subscription keys from westus,
        // replace "westcentralus" with "westus".
        //
        // Free trial subscription keys are generated in the westcentralus
        // region. If you use a free trial subscription key, you shouldn't
        // need to change the region.
        // Specify the Azure region
        private const string faceEndpoint =
            "https://westcentralus.api.cognitive.microsoft.com";

        // localImagePath = @"C:\Documents\LocalImage.jpg"
        private const string localImagePath = @"<LocalImage>";

        private const string remoteImageUrl =
            "https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg";

        private static readonly FaceAttributeType[] faceAttributes =
            { FaceAttributeType.Age, FaceAttributeType.Gender };

        static void Main(string[] args)
        {
            FaceClient faceClient = new FaceClient(
                new ApiKeyServiceClientCredentials(subscriptionKey),
                new System.Net.Http.DelegatingHandler[] { });
            faceClient.Endpoint = faceEndpoint;

            Console.WriteLine("Faces being detected ...");
            var t1 = DetectRemoteAsync(faceClient, remoteImageUrl);
            var t2 = DetectLocalAsync(faceClient, localImagePath);

            Task.WhenAll(t1, t2).Wait(5000);
            Console.WriteLine("Press any key to exit");
            Console.ReadLine();
        }

        // Detect faces in a remote image
        private static async Task DetectRemoteAsync(
            FaceClient faceClient, string imageUrl)
        {
            if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
            {
                Console.WriteLine("\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
                return;
            }

            try
            {
                IList<DetectedFace> faceList =
                    await faceClient.Face.DetectWithUrlAsync(
                        imageUrl, true, false, faceAttributes);

                DisplayAttributes(GetFaceAttributes(faceList, imageUrl), imageUrl);
            }
            catch (APIErrorException e)
            {
                Console.WriteLine(imageUrl + ": " + e.Message);
            }
        }

        // Detect faces in a local image
        private static async Task DetectLocalAsync(FaceClient faceClient, string imagePath)
        {
            if (!File.Exists(imagePath))
            {
                Console.WriteLine(
                    "\nUnable to open or read localImagePath:\n{0} \n", imagePath);
                return;
            }

            try
            {
                using (Stream imageStream = File.OpenRead(imagePath))
                {
                    IList<DetectedFace> faceList =
                            await faceClient.Face.DetectWithStreamAsync(
                                imageStream, true, false, faceAttributes);
                    DisplayAttributes(
                        GetFaceAttributes(faceList, imagePath), imagePath);
                }
            }
            catch (APIErrorException e)
            {
                Console.WriteLine(imagePath + ": " + e.Message);
            }
        }

        private static string GetFaceAttributes(
            IList<DetectedFace> faceList, string imagePath)
        {
            string attributes = string.Empty;

            foreach (DetectedFace face in faceList)
            {
                double? age = face.FaceAttributes.Age;
                string gender = face.FaceAttributes.Gender.ToString();
                attributes += gender + " " + age + "   ";
            }

            return attributes;
        }

        // Display the face attributes
        private static void DisplayAttributes(string attributes, string imageUri)
        {
            Console.WriteLine(imageUri);
            Console.WriteLine(attributes + "\n");
        }
    }
}
```

### <a name="detectwithurlasync-response"></a>Odpověď DetectWithUrlAsync

Úspěšná odpověď zobrazí pohlaví a věk pro každou tvář na obrázku.

Příklad nezpracovaného výstupu JSON najdete v [rychlých startech pro rozhraní API a rozpoznávání tváří na obrázku pomocí C#](CSharp.md).

```
https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg
Male 37   Female 56
```

## <a name="next-steps"></a>Další kroky

Naučte se vytvářet aplikace WPF pro Windows, která využívá službu pro rozpoznávání tváře k rozpoznání tváří na obrázku. Tato aplikace nakreslí rámeček kolem jednotlivých tváří a zobrazí popis tváře na stavovém řádku.

> [!div class="nextstepaction"]
> [Kurz: Vytvoření aplikace WPF k detekci a orámování tváří na obrázku](../Tutorials/FaceAPIinCSharpTutorial.md)
