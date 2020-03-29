---
title: Použití koncového bodu předpovědi k programovému testování obrazů pomocí klasifikátoru – vlastní vidění
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak použít rozhraní API k programovému testování obrázků pomocí klasifikátoru služby Custom Vision Service.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: anroth
ms.openlocfilehash: dcb12da680d70e1f0ce4cd763bee340bb3416c6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "76169955"
---
# <a name="use-your-model-with-the-prediction-api"></a>Použití modelu s rozhraním API pro předpověď

Po trénování modelu můžete testovat bitové kopie programově jejich odesláním do koncového bodu rozhraní API předpověď.

> [!NOTE]
> Tento dokument ukazuje použití jazyka C# k odeslání obrázku do rozhraní API pro předpovědi. Další informace a příklady naleznete v [odkazu rozhraní API předpověď](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15).

## <a name="publish-your-trained-iteration"></a>Publikování trénované iterace

Na [webové stránce služby Custom Vision](https://customvision.ai) vyberte svůj projekt a pak vyberte kartu __Výkon__.

Chcete-li odeslat bitové kopie do rozhraní API předpověď, budete muset nejprve publikovat iteraci pro předpověď, které lze provést výběrem __Publikovat__ a zadáním názvu pro publikovanou iteraci. Tím se váš model zpřístupní rozhraní API předpověď vašeho prostředku vlastní vize Azure.

![Zobrazí se karta výkon s červeným obdélníkem obklopujícím tlačítko Publikovat.](./media/use-prediction-api/unpublished-iteration.png)

Po úspěšném publikování modelu se vedle iterace v levém postranním panelu zobrazí popisek "Publikováno" a jeho název se zobrazí v popisu iterace.

![Zobrazí se karta výkonu s červeným obdélníkem obklopujícím popisek Published a názvem publikované iterace.](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>Získání adresy URL a klíče předpovědi

Po publikování modelu můžete načíst požadované informace výběrem __adresy URL předpovědi__. Tím se otevře dialogové okno s informacemi pro použití rozhraní API předpověď, včetně __předpověď URL__ a __předpověď-key__.

![Karta výkon se zobrazí s červeným obdélníkem obklopujícím tlačítko Adresy URL predikce.](./media/use-prediction-api/published-iteration-prediction-url.png)

![Karta výkonu je zobrazena s červeným obdélníkem obklopujícím hodnotu adresy URL předpověď pro použití souboru obrázku a hodnotu Predikční klíč.](./media/use-prediction-api/prediction-api-info.png)


V této příručce budete používat místní obrázek, takže zkopírujte adresu URL v části **Pokud máte soubor obrázku** do dočasného umístění. Zkopírujte také odpovídající hodnotu __Predikční klíč.__

## <a name="create-the-application"></a>Vytvoření aplikace

1. V sadě Visual Studio vytvořte novou konzolovou aplikaci Jazyka C#.

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
   * Nastavte `namespace` pole na název projektu.
   * Nahraďte `<Your prediction key>` zástupný symbol hodnotou klíče, kterou jste načetli dříve.
   * Nahraďte `<Your prediction URL>` zástupný symbol adresou URL, kterou jste načetli dříve.

## <a name="run-the-application"></a>Spuštění aplikace

Při spuštění aplikace budete vyzváni k zadání cesty k souboru obrázku v konzole. Bitová kopie je pak odeslána předpověď rozhraní API a výsledky předpověď jsou vráceny jako řetězec ve formátu JSON. Následuje příklad odpovědi.

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

## <a name="next-steps"></a>Další kroky

V této příručce jste se naučili, jak odeslat obrázky do vlastního klasifikátoru/detektoru obrázků a získat programovou odpověď pomocí sady C# SDK. Dále se dozvíte, jak dokončit scénáře začátku do konce pomocí jazyka C#, nebo začít používat jinou jazykovou sadu SDK.

* [Úvodní příručka: .NET SDK](csharp-tutorial.md)
* [Úvodní příručka: Python SDK](python-tutorial.md)
* [Úvodní příručka: Java SDK](java-tutorial.md)
* [Úvodní příručka: Sada SDK uzlu](node-tutorial.md)
* [Úvodní příručka: Přejít na sdk](go-tutorial.md)
