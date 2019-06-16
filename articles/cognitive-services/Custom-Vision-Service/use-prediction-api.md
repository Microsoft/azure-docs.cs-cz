---
title: Použití koncového bodu předpovědi pro programové testování imagí pomocí třídění – Custom Vision
titlesuffix: Azure Cognitive Services
description: Zjistěte, jak použít rozhraní API k programovému testování obrázků pomocí klasifikátoru služby Custom Vision Service.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: article
ms.date: 04/02/2019
ms.author: anroth
ms.openlocfilehash: 1ee6edbf49bbcd2014afcf29ed3b737168a3b5bc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60816753"
---
# <a name="use-your-model-with-the-prediction-api"></a>Váš Model pomocí prediktivního rozhraní API

Po jste trénování modelu, můžete otestovat imagí prostřednictvím kódu programu, odešlete je do koncového bodu Prediction API.

> [!NOTE]
> Tento dokument ukazuje použití jazyka C# k odeslání obrázku do rozhraní API pro předpovědi. Další informace a příklady najdete v tématu [reference k rozhraní API pro předpověď](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15).

## <a name="publish-your-trained-iteration"></a>Publikování trénovaného iterace

Na [webové stránce služby Custom Vision](https://customvision.ai) vyberte svůj projekt a pak vyberte kartu __Výkon__.

Odeslání Image do rozhraní API pro předpověď, musíte nejdřív publikovat vaši iteraci pro predikci, což lze provést tak, že vyberete __publikovat__ a zadáte název publikované iterace. Díky tomu budou váš model přístupné Prediction API Custom Vision Azure prostředku.

![Na kartě výkonu se zobrazí s červeným obdélníkem tlačítko Publikovat.](./media/use-prediction-api/unpublished-iteration.png)

Po úspěšném publikování modelu se zobrazí vedle vašeho iterace na levém bočním panelu se zobrazí popisek "Publikováno" a jejich název se zobrazí v popisu iterace.

![Na kartě výkonu se zobrazí s red rámeček okolo publikováno label a název publikované iterace.](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>Získání adresy URL a klíče předpovědi

Po publikování modelu, můžete načíst požadované informace tak, že vyberete __předpovědi URL__. Tím se otevře dialogové okno s informacemi o používání rozhraní API Predikcí, včetně __předpovědi URL__ a __předpovědi klíč__.

![Na kartě výkonu se zobrazí s red obdélník tlačítko předpovědi adresy URL.](./media/use-prediction-api/published-iteration-prediction-url.png)

![Na kartě výkonu se zobrazí s červenou obdélník hodnota předpovědi URL souboru obrázku a hodnotu klíče předpovědi.](./media/use-prediction-api/prediction-api-info.png)

> [!TIP]
> Vaše __předpovědi klíč__ najdete také v [webu Azure portal](https://portal.azure.com) stránky pro vlastní prostředek Azure pro zpracování obrazu spojené s projektem, v části __klíče__ okno.

V této příručce, použijte místní image, takže zkopírujte adresu URL v části **Pokud máte soubor obrázku** do dočasného umístění. Zkopírujte odpovídající __předpovědi klíč__ hodnotu.

## <a name="create-the-application"></a>Vytvoření aplikace

1. V sadě Visual Studio vytvořte nový C# konzolové aplikace.

1. Použijte následující kód jako obsah souboru __Program.cs__.

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace CVSPredictionSample
    {
        public static class Program
        {
            public static void Main()
            {
                Console.Write("Enter image file path: ");
                string imageFilePath = Console.ReadLine();

                MakePredictionRequest(imageFilePath).Wait();

                Console.WriteLine("\n\nHit ENTER to exit...");
                Console.ReadLine();
            }

            public static async Task MakePredictionRequest(string imageFilePath)
            {
                var client = new HttpClient();

                // Request headers - replace this example key with your valid Prediction-Key.
                client.DefaultRequestHeaders.Add("Prediction-Key", "<Your prediction key>");

                // Prediction URL - replace this example URL with your valid Prediction URL.
                string url = "<Your prediction URL>";

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

            private static byte[] GetImageAsByteArray(string imageFilePath)
            {
                FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
    ```

1. Změňte následující informace:
   * Nastavte `namespace` pole na název vašeho projektu.
   * Nahraďte zástupný text `<Your prediction key>` s hodnotou klíče, který jste získali dříve.
   * Nahraďte zástupný text `<Your prediction URL>` s adresou URL, který jste získali dříve.

## <a name="run-the-application"></a>Spuštění aplikace

Při spuštění aplikace, zobrazí se výzva k zadání cesty k souboru obrázku v konzole. Na obrázku je potom odeslán Predikcí rozhraní API a predikované výsledky jsou vráceny jako řetězec ve formátu JSON. Následuje příklad odpovědi.

```json
{
    "Id":"7796df8e-acbc-45fc-90b4-1b0c81b73639",
    "Project":"8622c779-471c-4b6e-842c-67a11deffd7b",
    "Iteration":"59ec199d-f3fb-443a-b708-4bca79e1b7f7",
    "Created":"2019-03-20T16:47:31.322Z",
    "Predictions":[
        {"TagId":"d9cb3fa5-1ff3-4e98-8d47-2ef42d7fb373","TagName":"cat", "Probability":1.0},
        {"TagId":"9a8d63fb-b6ed-4462-bcff-77ff72084d99","TagName":"dog", "Probability":0.1087869}
    ]
}
```

## <a name="next-steps"></a>Další postup

V této příručce, jste zjistili, jak odeslat Image do vaší vlastní image třídění/detektor a přijetí odpovědi programově pomocí C# SDK. Dále se naučíte k dokončení scénáře začátku do konce se C#, nebo začněte používat jiný jazyk sady SDK.

* [Rychlý start: .NET SDK](csharp-tutorial.md)
* [Rychlé zprovoznění: Python SDK](python-tutorial.md)
* [Rychlé zprovoznění: Java SDK](java-tutorial.md)
* [Rychlé zprovoznění: Node SDK](node-tutorial.md)
* [Rychlé zprovoznění: Go SDK](go-tutorial.md)
