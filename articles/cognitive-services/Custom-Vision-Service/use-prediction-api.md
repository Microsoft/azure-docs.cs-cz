---
title: Použití koncového bodu předpovědi pro programové testování imagí s klasifikátorem – Custom Vision
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak použít rozhraní API k programovému testování obrázků pomocí klasifikátoru služby Custom Vision Service.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 7f1939536e033d2cf964dd2f4ee562e4ee20061b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88934748"
---
# <a name="use-your-model-with-the-prediction-api"></a>Použití modelu s prediktivním rozhraním API

Po vytvoření výukového modelu můžete image testovat programově jejich odesláním do koncového bodu rozhraní API předpovědi.

> [!NOTE]
> Tento dokument ukazuje použití jazyka C# k odeslání obrázku do rozhraní API pro předpovědi. Další informace a příklady najdete v referenčních informacích k [rozhraní API předpovědi](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15).

## <a name="publish-your-trained-iteration"></a>Publikování vyškolené iterace

Na [webové stránce služby Custom Vision](https://customvision.ai) vyberte svůj projekt a pak vyberte kartu __Výkon__.

Chcete-li odesílat obrázky do prediktivního rozhraní API, budete nejprve muset publikovat iteraci pro předpověď, což lze provést výběrem možnosti __publikovat__ a zadáním názvu publikované iterace. Díky tomu bude model dostupný pro předpovědi rozhraní API Custom Vision prostředku Azure.

![Karta výkon se zobrazí s červeným obdélníkem obklopujícím tlačítko publikovat.](./media/use-prediction-api/unpublished-iteration.png)

Po úspěšném publikování modelu se vedle vaší iterace na levém bočním panelu zobrazí popisek Publikováno a jeho název se zobrazí v popisu iterace.

![Zobrazí se karta výkon s červeným obdélníkem kolem publikovaného popisku a názvem publikované iterace.](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>Získání adresy URL a klíče předpovědi

Po publikování modelu můžete požadované informace načíst výběrem možnosti __Adresa URL předpovědi__. Tím se otevře dialogové okno s informacemi pro použití prediktivního rozhraní API, včetně __předpovědi URL__ a __předpovědi-Key__.

![Karta výkon se zobrazí s červeným obdélníkem obklopujícím tlačítko adresy URL předpovědi.](./media/use-prediction-api/published-iteration-prediction-url.png)

![Karta výkon se zobrazí s červeným obdélníkem, který obklopuje hodnotu adresy URL předpovědi pro použití souboru obrázku a hodnoty Prediction-Key.](./media/use-prediction-api/prediction-api-info.png)


V tomto průvodci použijete místní bitovou kopii, takže zkopírujte adresu URL v části **Pokud máte soubor s obrázkem** do dočasného umístění. Zkopírujte také odpovídající hodnotu __pro předpověď a klíč__ .

## <a name="create-the-application"></a>Vytvoření aplikace

1. V aplikaci Visual Studio vytvořte novou konzolovou aplikaci v jazyce C#.

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
   * Zástupný text nahraďte `<Your prediction key>` hodnotou klíče, kterou jste získali dříve.
   * Nahraďte zástupný symbol `<Your prediction URL>` adresou URL, kterou jste získali dříve.

## <a name="run-the-application"></a>Spuštění aplikace

Při spuštění aplikace se zobrazí výzva k zadání cesty k souboru obrázku v konzole nástroje. Obrázek se pak odešle do prediktivního rozhraní API a výsledky předpovědi se vrátí jako řetězec ve formátu JSON. Následuje příklad odpovědi.

```json
{
    "id":"7796df8e-acbc-45fc-90b4-1b0c81b73639",
    "project":"8622c779-471c-4b6e-842c-67a11deffd7b",
    "iteration":"59ec199d-f3fb-443a-b708-4bca79e1b7f7",
    "created":"2019-03-20T16:47:31.322Z",
    "predictions":[
        {"tagId":"d9cb3fa5-1ff3-4e98-8d47-2ef42d7fb373","tagName":"cat", "probability":1.0},
        {"tagId":"9a8d63fb-b6ed-4462-bcff-77ff72084d99","tagName":"dog", "probability":0.1087869}
    ]
}
```

## <a name="next-steps"></a>Další kroky

V této příručce jste zjistili, jak odesílat obrázky do vlastního klasifikátoru a detektoru imagí a přijímat odpovědi programově pomocí sady C# SDK. Dále se naučíte, jak dokončit ucelené scénáře pomocí jazyka C# nebo začít používat jinou jazykovou sadu SDK.

* [Rychlý Start: Custom Vision SDK](quickstarts/image-classification.md)
