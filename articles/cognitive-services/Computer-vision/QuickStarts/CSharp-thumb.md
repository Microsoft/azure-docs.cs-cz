---
title: Rychlý start k vytvoření miniatury obrázku pomocí rozhraní API pro počítačové zpracování obrazu s C# | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: V tomto rychlém startu vygenerujete ve službách Cognitive Services pomocí počítačového zpracování obrazu s C# miniaturu obrázku.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 0f5e3be75ce34d10c223e6a157a89fca12b9c3dc
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2018
ms.locfileid: "43769285"
---
# <a name="quickstart-generate-a-thumbnail---rest-c35"></a>Rychlý start: Vygenerování miniatury – REST, C&#35;

V tomto rychlém startu vygenerujete pomocí počítačového zpracování obrazu miniaturu obrázku.

## <a name="prerequisites"></a>Požadavky

Abyste mohli počítačové zpracování obrazu použít, potřebujete klíč předplatného. Přečtěte si, [jak klíče předplatného získat](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="get-thumbnail-request"></a>Žádost Get Thumbnail

Miniaturu obrázku můžete vygenerovat pomocí [metody Get Thumbnail](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb). Zadáte výšku a šířku, které se mohou od poměru stran vstupního obrázku lišit. Počítačové zpracování obrazu použije chytré oříznutí, které inteligentně identifikuje oblast zájmu a na základě této oblasti vygeneruje souřadnice oříznutí.

Pokud chcete spustit ukázku, postupujte takto:

1. V sadě Visual Studio vytvořte novou konzolovou aplikaci Visual C#.
1. Nainstalujte balíček NuGet Newtonsoft.Json.
    1. V nabídce klikněte na **Nástroje** vyberte **Správce balíčků NuGet** a potom **Spravovat balíčky NuGet pro řešení**.
    1. Klikněte na kartu **Procházet** a do pole **Hledat** zadejte Newtonsoft.Json.
    1. Když se zobrazí, vyberte **Newtonsoft.Json**. Pak klikněte na zaškrtávací políčko vedle názvu vašeho projektu a na **Nainstalovat**.
1. `Program.cs` nahraďte následujícím kódem.
1. Místo `<Subscription Key>` použijte platný klíč předplatného.
1. V případě potřeby změňte hodnotu `uriBase` na umístění, kde jste získali klíče předplatného.
1. Spusťte program.
1. Do příkazového řádku zadejte cestu k místnímu obrázku.

Miniatura se uloží do stejné složky jako místní obrázek, použije se původní název s příponou _thumb.

```csharp
using Newtonsoft.Json.Linq;
using System;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;

namespace CSHttpClientSample
{
    static class Program
    {
        // Replace <Subscription Key> with your valid subscription key.
        const string subscriptionKey = "<Subscription Key>";

        // You must use the same region in your REST call as you used to
        // get your subscription keys. For example, if you got your
        // subscription keys from westus, replace "westcentralus" in the URL
        // below with "westus".
        //
        // Free trial subscription keys are generated in the westcentralus region.
        // If you use a free trial subscription key, you shouldn't need to change
        // this region.
        const string uriBase =
            "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail";

        static void Main()
        {
            // Get the path and filename to process from the user.
            Console.WriteLine("Thumbnail:");
            Console.Write(
                "Enter the path to the image you wish to use to create a thumbnail image: ");
            string imageFilePath = Console.ReadLine();

            if (File.Exists(imageFilePath))
            {
                // Make the REST API call.
                Console.WriteLine("\nWait a moment for the results to appear.\n");
                MakeThumbNailRequest(imageFilePath).Wait();
            }
            else
            {
                Console.WriteLine("\nInvalid file path");
            }
            Console.WriteLine("\nPress Enter to exit...");
            Console.ReadLine();
        }

        /// <summary>
        /// Gets a thumbnail image from the specified image file by using
        /// the Computer Vision REST API.
        /// </summary>
        /// <param name="imageFilePath">The image file to use to create the thumbnail image.</param>
        static async Task MakeThumbNailRequest(string imageFilePath)
        {
            try
            {
                HttpClient client = new HttpClient();

                // Request headers.
                client.DefaultRequestHeaders.Add(
                    "Ocp-Apim-Subscription-Key", subscriptionKey);

                // Request parameters.
                string requestParameters = "width=200&height=150&smartCropping=true";

                // Assemble the URI for the REST API Call.
                string uri = uriBase + "?" + requestParameters;

                HttpResponseMessage response;

                // Request body.
                // Posts a locally stored JPEG image.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                using (ByteArrayContent content = new ByteArrayContent(byteData))
                {
                    // This example uses content type "application/octet-stream".
                    // The other content types you can use are "application/json"
                    // and "multipart/form-data".
                    content.Headers.ContentType =
                        new MediaTypeHeaderValue("application/octet-stream");

                    // Make the REST API call.
                    response = await client.PostAsync(uri, content);
                }

                if (response.IsSuccessStatusCode)
                {
                    // Display the response data.
                    Console.WriteLine("\nResponse:\n{0}", response);

                    // Get the image data.
                    byte[] thumbnailImageData =
                        await response.Content.ReadAsByteArrayAsync();

                    // Save the thumbnail to the same folder as the original image,
                    // using the original name with the suffix "_thumb".
                    // Note: This will overwrite an existing file of the same name.
                    string thumbnailFilePath =
                        imageFilePath.Insert(imageFilePath.Length - 4, "_thumb");
                    File.WriteAllBytes(thumbnailFilePath, thumbnailImageData);
                    Console.WriteLine("\nThumbnail written to: {0}", thumbnailFilePath);
                }
                else
                {
                    // Display the JSON error data.
                    string errorString = await response.Content.ReadAsStringAsync();
                    Console.WriteLine("\n\nResponse:\n{0}\n",
                        JToken.Parse(errorString).ToString());
                }
            }
            catch (Exception e)
            {
                Console.WriteLine("\n" + e.Message);
            }
        }

        /// <summary>
        /// Returns the contents of the specified file as a byte array.
        /// </summary>
        /// <param name="imageFilePath">The image file to read.</param>
        /// <returns>The byte array of the image data.</returns>
        static byte[] GetImageAsByteArray(string imageFilePath)
        {
            using (FileStream fileStream =
                new FileStream(imageFilePath, FileMode.Open, FileAccess.Read))
            {
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
}
```

## <a name="get-thumbnail-response"></a>Odpověď metody Get Thumbnail

Úspěšná odpověď obsahuje binární kód miniatury obrázku. Pokud požadavek selže, bude odpověď obsahovat chybový kód a zprávu, která vám pomůže určit, co se nepovedlo.

```text
Response:

StatusCode: 200, ReasonPhrase: 'OK', Version: 1.1, Content: System.Net.Http.StreamContent, Headers:
{
  Pragma: no-cache
  apim-request-id: 131eb5b4-5807-466d-9656-4c1ef0a64c9b
  Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
  x-content-type-options: nosniff
  Cache-Control: no-cache
  Date: Tue, 06 Jun 2017 20:54:07 GMT
  X-AspNet-Version: 4.0.30319
  X-Powered-By: ASP.NET
  Content-Length: 5800
  Content-Type: image/jpeg
  Expires: -1
}
```

## <a name="next-steps"></a>Další kroky

Prozkoumejte základní aplikaci Windows, která používá počítačové zpracování obrazu k optickému rozpoznávání znaků (OCR), vytvořte chytře ořezané miniatury a rozpoznávejte, kategorizujte, označujte a popisujte vizuální vlastnosti na obrázku včetně obličejů. Pokud chcete rychle vyzkoušet rozhraní API pro počítačové zpracování obrazu, vyzkoušejte [testovací konzolu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Kurz – rozhraní API pro počítačové zpracování obrazu s C&#35;](../Tutorials/CSharpTutorial.md)
