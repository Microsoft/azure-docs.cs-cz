---
title: 'Rychlý start: Získání délky vět, C# – Translator Text API'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu zjistíte délky vět v textu pomocí služby Translator Text API pomocí jazyka C#.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/15/2018
ms.author: erhopf
ms.openlocfilehash: c10a38164c71eaa4239072fe10973932ce8cce3b
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49645014"
---
# <a name="quickstart-get-sentence-lengths-with-the-translator-text-rest-api-c"></a>Rychlý start: Získání délky vět pomocí rozhraní REST API služby Translator Text (C#)

V tomto rychlém startu zjistíte délky vět v textu pomocí služby Translator Text API.

## <a name="prerequisites"></a>Požadavky

Ke spuštění tohoto kódu ve Windows budete potřebovat [Visual Studio 2017](https://www.visualstudio.com/downloads/). (Bude stačit bezplatná verze Community Edition.)

Abyste mohli použít službu Translator Text API, budete potřebovat klíč předplatného. Přečtěte si, [jak se zaregistrovat ve službě Translator Text API](translator-text-how-to-signup.md).

## <a name="breaksentence-request"></a>Žádost BreakSentence

> [!TIP]
> Získejte z [GitHubu](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-C-Sharp) nejnovější verzi kódu.

Následující kód rozdělí zdrojový text do vět pomocí metody [BreakSentence](./reference/v3-0-break-sentence.md).

1. Ve svém oblíbeném prostředí IDE vytvořte nový projekt C#.
2. Přidejte níže uvedený kód.
3. Hodnotu `key` nahraďte přístupovým klíčem platným pro vaše předplatné.
4. Spusťte program.

```csharp
using System;
using System.Net.Http;
using System.Text;
// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace TranslatorTextQuickStart
{
    class Program
    {
        static string host = "https://api.cognitive.microsofttranslator.com";
        static string path = "/breaksentence?api-version=3.0";

        static string uri = host + path;

        // NOTE: Replace this example key with a valid subscription key.
        static string key = "ENTER KEY HERE";

        static string text = "How are you? I am fine. What did you do today?";

        async static void Break()
        {
            System.Object[] body = new System.Object[] { new { Text = text } };
            var requestBody = JsonConvert.SerializeObject(body);

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();
                var result = JsonConvert.SerializeObject(JsonConvert.DeserializeObject(responseBody), Formatting.Indented);

                Console.OutputEncoding = UnicodeEncoding.UTF8;
                Console.WriteLine(result);
            }
        }

        static void Main(string[] args)
        {
            Break();
            Console.ReadLine();
        }
    }
}
```

## <a name="breaksentence-response"></a>Odpověď metody BreakSentence

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu:

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "sentLen": [
      13,
      11,
      22
    ]
  }
]
```

## <a name="next-steps"></a>Další kroky

Prozkoumejte vzorový kód pro tento rychlý start a další, včetně překladu a transkripce, a také další vzorové projekty Translator Text na GitHubu.

> [!div class="nextstepaction"]
> [Prozkoumejte příklady C# na GitHubu](https://aka.ms/TranslatorGitHub?type=&language=c%23)
