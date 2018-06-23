---
title: Použít vlastní vize předpovědi koncový bod služby - kognitivní služeb Azure | Microsoft Docs
description: Naučte se používat rozhraní API prostřednictvím kódu programu testování obrázků s třídění vaše vlastní vize služby.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 54f9d9fec1f40c167341dec6a8699b6a558419da
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343005"
---
# <a name="use-the-prediction-endpoint-to-test-images-programmatically-with-a-custom-vision-service-classifier"></a>Koncový bod předpovědi použít k testování obrázků programově pomocí služby vize vlastní třídění

Poté, co jste natrénování modelu, můžete otestovat bitové kopie prostřednictvím kódu programu jejich odesláním do rozhraní API předpovědi. 

> [!NOTE]
> Tento dokument ukazuje, jak pomocí jazyka C# Odeslat bitovou kopii do rozhraní API předpovědi. Další informace a příklady použití rozhraní API najdete v tématu [referenční dokumentace rozhraní API předpovědi](https://go.microsoft.com/fwlink/?linkid=865445).

## <a name="get-the-url-and-prediction-key"></a>Získání adresy URL a předpovědi klíče

Z [vize vlastní webové stránky](https://customvision.ai), vyberte projekt a pak vyberte __výkonu__ kartě. Chcete-li zobrazit informace o používání rozhraní API předpovědi, vyberte __předpovědi URL__. Zkopírujte následující informace pro použití v aplikaci:

* __Adresa URL__ pro použití __soubor bitové kopie__.
* __Klíč předpovědi__ hodnotu.

> [!TIP]
> Pokud máte více iterací, můžete řídit, která je používána nastavení jako výchozí. Vyberte iterace z __iterací__ části a pak vyberte __nastavit jako výchozí__ v horní části stránky.

![Na kartě výkonu se zobrazí s červenou obdélník, adresu URL předpovědi.](./media/use-prediction-api/prediction-url.png)

## <a name="create-the-application"></a>Vytvoření aplikace

1. Ze sady Visual Studio vytvořte novou aplikaci konzoly C#.

2. Použít následující kód jako text __Program.cs__ souboru.

    > [!IMPORTANT]
    > Změňte následující informace:
    >
    > * Nastavte __obor názvů__ na název projektu.
    > * Nastavte __předpovědi klíč__ přijaté dříve v řádku, který začíná hodnoty `client.DefaultRequestHeaders.Add("Prediction-Key",`.
    > * Nastavte __URL__ přijaté dříve v řádku, který začíná hodnoty `string url =`.

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

## <a name="use-the-application"></a>Pomocí aplikace

Při spuštění aplikace, zadejte cestu k souboru obrázku. Obrázek je odeslána do rozhraní API a budou vráceny výsledky jako dokument JSON. Následujícím kódu JSON je příkladem odpovědi

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