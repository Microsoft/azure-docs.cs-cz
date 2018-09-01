---
title: Použít službě Custom Vision Service předpovědi koncový bod - služeb Azure Cognitive Services | Dokumentace Microsoftu
description: Další informace o použití rozhraní API prostřednictvím kódu programu testování obrázků s klasifikátoru Custom Vision Service.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: d7f9b90db06811e16cd0cd6ad2b32a27912cfee5
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2018
ms.locfileid: "43341789"
---
# <a name="use-the-prediction-endpoint-to-test-images-programmatically-with-a-custom-vision-service-classifier"></a>Použití koncového bodu předpovědi k otestování Image programově pomocí služby Custom Vision Service třídění

Po tréninku modelu, můžete otestovat imagí prostřednictvím kódu programu, odešlete jim Predikcí rozhraní API. 

> [!NOTE]
> Tento dokument ukazuje použití jazyka C# k odeslání image do rozhraní API pro předpověď. Další informace a příklady použití rozhraní API najdete v tématu [reference k rozhraní API pro předpověď](https://go.microsoft.com/fwlink/?linkid=865445).

## <a name="get-the-url-and-prediction-key"></a>Získání adresy URL a predikcí klíče

Z [vizi vlastní webovou stránku](https://customvision.ai), vyberte svůj projekt a pak vyberte __výkonu__ kartu. K zobrazení informací o použití rozhraní API pro předpověď, včetně __předpovědi klíč__vyberte __předpovědi URL__. Pro projekty, které jsou připojené k prostředku Azure vaše __předpovědi klíč__ najdete také v [webu Azure Portal](https://portal.azure.com) stránky pro přidružený prostředek Azure v rámci __klíče__. Zkopírujte následující informace pro použití v aplikaci:

* __Adresa URL__ pro používání __soubor bitové kopie__.
* __Predikce klíč__ hodnotu.

> [!TIP]
> Pokud máte více iterací, můžete řídit, která se používá tak, že nastavíte jako výchozí. Vyberte tak iterace z __iterací__ a potom vyberte __nastavit jako výchozí__ v horní části stránky.

![Na kartě výkonu se zobrazí s červenou obdélník, adresa URL předpovědi.](./media/use-prediction-api/prediction-url.png)

## <a name="create-the-application"></a>Vytvoření aplikace

1. Ze sady Visual Studio vytvořte novou aplikaci konzoly C#.

2. Použijte následující kód jako obsah metody __Program.cs__ souboru.

    > [!IMPORTANT]
    > Změňte následující informace:
    >
    > * Nastavte __obor názvů__ na název vašeho projektu.
    > * Nastavte __předpovědi klíč__ hodnoty, které jste získali dříve v řádku, který začíná `client.DefaultRequestHeaders.Add("Prediction-Key",`.
    > * Nastavte __URL__ hodnoty, které jste získali dříve v řádku, který začíná `string url =`.

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace CSPredictionSample
    {
        static class Program
        {
            static void Main()
            {
                Console.Write("Enter image file path: ");
                string imageFilePath = Console.ReadLine();

                MakePredictionRequest(imageFilePath).Wait();

                Console.WriteLine("\n\n\nHit ENTER to exit...");
                Console.ReadLine();
            }

            static byte[] GetImageAsByteArray(string imageFilePath)
            {
                FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }

            static async Task MakePredictionRequest(string imageFilePath)
            {
                var client = new HttpClient();

                // Request headers - replace this example key with your valid subscription key.
                client.DefaultRequestHeaders.Add("Prediction-Key", "13hc77781f7e4b19b5fcdd72a8df7156");

                // Prediction URL - replace this example URL with your valid prediction URL.
                string url = "http://southcentralus.api.cognitive.microsoft.com/customvision/v1.0/prediction/d16e136c-5b0b-4b84-9341-6a3fff8fa7fe/image?iterationId=f4e573f6-9843-46db-8018-b01d034fd0f2";

                HttpResponseMessage response;

                // Request body. Try this sample with a locally stored image.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                using (var content = new ByteArrayContent(byteData))
                {
                    content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
                    response = await client.PostAsync(url, content);
                    Console.WriteLine(await response.Content.ReadAsStringAsync());
                }
            }
        }
    }
    ```

## <a name="use-the-application"></a>Použití aplikace

Při spuštění aplikace, zadejte cestu k souboru obrázku. Odeslání image do rozhraní API a výsledky se vrátí jako dokument JSON. Následující kód JSON je příklad odpovědi

```json
{
    "Id":"3f76364c-b8ae-4818-a2b2-2794cfbe377a",
    "Project":"2277aca4-7aff-4742-8afb-3682e251c913",
    "Iteration":"84105bfe-73b5-4fcc-addb-756c0de17df2",
    "Created":"2018-05-03T14:15:22.5659829Z",
    "Predictions":[
        {"TagId":"35ac2ad0-e3ef-4e60-b81f-052a1057a1ca","Tag":"dog","Probability":0.102716163},
        {"TagId":"28e1a872-3776-434c-8cf0-b612dd1a953c","Tag":"cat","Probability":0.02037274}
    ]
}
```

## <a name="next-steps"></a>Další postup

[Export modelu pro mobilní použití](export-your-model.md)
