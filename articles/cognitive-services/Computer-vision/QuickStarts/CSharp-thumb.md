---
title: 'Rychlý Start: vygenerování miniatur – RESTC#'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu vygenerujete miniaturu z obrázku pomocí rozhraní API pro počítačové zpracování obrazu C#.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 889ce093e09a7e7d0fa3e81f3a819139ee225800
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176886"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-c"></a>Rychlý Start: vygenerujte miniaturu pomocí Počítačové zpracování obrazu REST API aC#

V tomto rychlém startu vygenerujete miniaturu z obrázku pomocí REST API Počítačové zpracování obrazu. Pomocí metody [získat miniaturu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) můžete vygenerovat miniaturu obrázku. Zadejte výšku a šířku, která se může lišit od poměru stran vstupní bitové kopie. Počítačové zpracování obrazu používá inteligentní ořezávání k inteligentně identifikaci oblasti zájmu a generování souřadnic oříznutí na základě této oblasti.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

- Musíte mít [Visual Studio 2015](https://visualstudio.microsoft.com/downloads/) nebo novější.
- Pro Počítačové zpracování obrazu musíte mít klíč předplatného. Bezplatný zkušební klíč si můžete [vyzkoušet Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Případně postupujte podle pokynů v části [Vytvoření účtu Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pro přihlášení k odběru počítačové zpracování obrazu a získání klíče. Pak [vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro řetězec klíčového a koncového bodu služby s názvem `COMPUTER_VISION_SUBSCRIPTION_KEY` a `COMPUTER_VISION_ENDPOINT` v uvedeném pořadí.

## <a name="create-and-run-the-sample-application"></a>Vytvoření a spuštění ukázkové aplikace

Chcete-li vytvořit ukázku v aplikaci Visual Studio, proveďte následující kroky:

1. Vytvořte nové řešení sady Visual Studio v aplikaci Visual Studio pomocí šablony aplikace C# Visual Console.
1. Nainstalujte balíček NuGet Newtonsoft. JSON.
    1. V nabídce klikněte na **nástroje**, vyberte **Správce balíčků NuGet**a pak **spravujte balíčky NuGet pro řešení**.
    1. Klikněte na kartu **Procházet** a do **vyhledávacího** pole zadejte "Newtonsoft. JSON".
    1. Po zobrazení vyberte **Newtonsoft. JSON** a potom klikněte na zaškrtávací políčko vedle názvu projektu a **nainstalujte**.
1. Spusťte program.
1. Na příkazovém řádku zadejte cestu k místní imagi.

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
        // Add your Computer Vision subscription key and endpoint to your environment variables.
        static string subscriptionKey = Environment.GetEnvironmentVariable("COMPUTER_VISION_SUBSCRIPTION_KEY");

        static string endpoint = Environment.GetEnvironmentVariable("COMPUTER_VISION_ENDPOINT");
        
        // the GenerateThumbnail method endpoint
        const string uriBase = endpoint + "vision/v2.1/generateThumbnail";

        static void Main()
        {
            // Get the path and filename to process from the user.
            Console.WriteLine("Thumbnail:");
            Console.Write(
                "Enter the path to the image you wish to use to create a thumbnail image: ");
            string imageFilePath = Console.ReadLine();

            if (File.Exists(imageFilePath))
            {
                // Call the REST API method.
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
                // The width and height parameters specify a thumbnail that's 
                // 200 pixels wide and 150 pixels high.
                // The smartCropping parameter is set to true, to enable smart cropping.
                string requestParameters = "width=200&height=150&smartCropping=true";

                // Assemble the URI for the REST API method.
                string uri = uriBase + "?" + requestParameters;

                HttpResponseMessage response;

                // Read the contents of the specified local image
                // into a byte array.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                // Add the byte array as an octet stream to the request body.
                using (ByteArrayContent content = new ByteArrayContent(byteData))
                {
                    // This example uses the "application/octet-stream" content type.
                    // The other content types you can use are "application/json"
                    // and "multipart/form-data".
                    content.Headers.ContentType =
                        new MediaTypeHeaderValue("application/octet-stream");

                    // Asynchronously call the REST API method.
                    response = await client.PostAsync(uri, content);
                }

                // Check the HTTP status code of the response. If successful, display
                // display the response and save the thumbnail.
                if (response.IsSuccessStatusCode)
                {
                    // Display the response data.
                    Console.WriteLine("\nResponse:\n{0}", response);

                    // Get the image data for the thumbnail from the response.
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
            // Open a read-only file stream for the specified file.
            using (FileStream fileStream =
                new FileStream(imageFilePath, FileMode.Open, FileAccess.Read))
            {
                // Read the file's contents into a byte array.
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
}
```

## <a name="examine-the-response"></a>Projděte si odpověď.

Úspěšná odpověď se vrátí jako binární data, která představují data obrázku pro miniaturu. Pokud je požadavek úspěšný, Miniatura se uloží do stejné složky jako místní image s použitím původního názvu s příponou "_thumb". Pokud požadavek neuspěje, odpověď obsahuje kód chyby a zprávu, která vám pomůže zjistit, co se nepovedlo.

Ukázková aplikace zobrazuje úspěšnou odpověď v okně konzoly, podobně jako v následujícím příkladu:

```console
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

Prozkoumejte základní aplikaci pro Windows, která používá Počítačové zpracování obrazu k provedení optického rozpoznávání znaků (OCR); vytvořit miniatury s inteligentním oříznutím; Navíc ke zjištění, kategorizaci, označení a popisu vizuálních funkcí, včetně obličeje, v obrázku. Pokud chcete rychle experimentovat s rozhraními API Počítačové zpracování obrazu, zkuste [otevřít konzolu pro testování rozhraní API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Kurz pro&#35; rozhraní API pro počítačové zpracování obrazu C](../Tutorials/CSharpTutorial.md)
