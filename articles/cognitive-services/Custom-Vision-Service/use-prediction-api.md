---
title: 'Příklad: Použití koncového bodu předpovědi pro programové testování imagí pomocí třídění – Custom Vision'
titlesuffix: Azure Cognitive Services
description: Zjistěte, jak použít rozhraní API k programovému testování obrázků pomocí klasifikátoru služby Custom Vision Service.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: article
ms.date: 03/26/2019
ms.author: anroth
ms.openlocfilehash: 715fa526c83608c9922315e3a0d89b67b31e0d16
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2019
ms.locfileid: "58472717"
---
#  <a name="use-your-model-with-the-prediction-api"></a>Váš Model pomocí prediktivního rozhraní API

Po vytrénování modelu můžete programově testovat obrázky jejich odesláním do rozhraní API pro předpovědi.

> [!NOTE]
> Tento dokument ukazuje použití jazyka C# k odeslání obrázku do rozhraní API pro předpovědi. Další informace a příklady použití rozhraní API najdete v tématu pojednávajícím o [referencích k rozhraní API pro předpovědi](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15).

## <a name="publish-your-trained-iteration"></a>Publikování trénovaného iterace

Na [webové stránce služby Custom Vision](https://customvision.ai) vyberte svůj projekt a pak vyberte kartu __Výkon__.

Odeslání Image do rozhraní API pro předpověď, musíte nejdřív publikovat vaši iteraci pro predikci, což lze provést tak, že vyberete __publikovat__ a zadáte název publikované iterace. To vám umožní modelu dostupná pro Prediction API Custom Vision Azure prostředku. 

![Na kartě výkonu se zobrazí s červeným obdélníkem tlačítko Publikovat.](./media/use-prediction-api/unpublished-iteration.png)

Jakmile modelu bylo úspěšně publikováno, zobrazí se vám "Publikováno" Popisek se zobrazí vedle vašeho iterace v levém bočním panelu, jakož i název publikované iterace v popisu iterace.

![Na kartě výkonu se zobrazí s red rámeček okolo publikováno label a název publikované iterace.](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>Získání adresy URL a klíče předpovědi

Po publikování modelu, můžete načíst informace o použití rozhraní API pro předpověď tak, že vyberete __předpovědi URL__. Tím se otevře dialogové okno podobný tomu vidíte níže spolu s informacemi o pomocí rozhraní API pro předpověď, včetně __předpovědi URL__ a __předpovědi klíč__.

![Na kartě výkonu se zobrazí s red obdélník tlačítko předpovědi adresy URL.](./media/use-prediction-api/published-iteration-prediction-url.png)

![Na kartě výkonu se zobrazí s červenou obdélník hodnota předpovědi URL souboru obrázku a hodnotu klíče předpovědi.](./media/use-prediction-api/prediction-api-info.png)

> [!TIP]
> Vaše __předpovědi klíč__ najdete také v [webu Azure Portal](https://portal.azure.com) stránky pro vlastní prostředek vizi Azure přidružené k projektu, v části __klíče__. 

Z tohoto dialogového okna zkopírujte následující informace pro použití v aplikaci:

* __Adresa URL předpovědi__ pro používání __soubor bitové kopie__.
* __Predikce klíč__ hodnotu.

## <a name="create-the-application"></a>Vytvoření aplikace

1. V sadě Visual Studio vytvořte novou konzolovou aplikaci v jazyce C#.

1. Použijte následující kód jako obsah souboru __Program.cs__.

    > [!IMPORTANT]
    > Změňte následující informace:
    >
    > * Nastavte __obor názvů__ na název vašeho projektu.
    > * Nastavte __předpovědi klíč__ hodnoty, který jste získali dříve v řádku, který začíná `client.DefaultRequestHeaders.Add("Prediction-Key",`.
    > * Nastavte __předpovědi URL__ hodnoty, který jste získali dříve v řádku, který začíná `string url =`.

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
                client.DefaultRequestHeaders.Add("Prediction-Key", "3b9dde6d1ae1453a86bfeb1d945300f2");

                // Prediction URL - replace this example URL with your valid Prediction URL.
                string url = "https://southcentralus.api.cognitive.microsoft.com/customvision/v3.0/Prediction/8622c779-471c-4b6e-842c-67a11deffd7b/classify/iterations/Cats%20vs.%20Dogs%20-%20Published%20Iteration%203/image";

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

## <a name="use-the-application"></a>Použití aplikace

Při spuštění aplikace, bude zadejte cestu k souboru image v konzole. Odeslání image Predikcí rozhraní API a predikované výsledky jsou vráceny jako dokument JSON. Následující kód JSON je příklad odpovědi.

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

[Export modelu pro mobilní použití](export-your-model.md)

[Začínáme s .NET SDK](csharp-tutorial.md)

[Začínáme se sadami SDK pro Python](python-tutorial.md)

[Začínáme s Java SDK](java-tutorial.md)

[Začínáme se sadami SDK pro Node](node-tutorial.md)

[Začínáme s Go SDK](go-tutorial.md)
