---
title: 'Rychlý start: Získání podporovaných jazyků – Translator Text API, C#'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu získáte seznam jazyků podporovaných pro překlad, transkripci a vyhledávání ve slovníku a příklady s použitím služby Translator Text API s C#.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/15/2018
ms.author: nolachar
ms.openlocfilehash: 967e978518debb5aef071807b1482d6f225c062f
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46367708"
---
# <a name="quickstart-get-supported-languages-with-c35"></a>Rychlý start: Získání podporovaných jazyků s C&#35;

V tomto rychlém startu získáte seznam jazyků podporovaných pro překlad, transkripci a vyhledávání ve slovníku a příklady s použitím služby Translator Text API.

Zdrojový kód k této ukázce je dostupný na [GitHubu](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-C-Sharp).

## <a name="prerequisites"></a>Požadavky

Ke spuštění tohoto kódu ve Windows budete potřebovat [Visual Studio 2017](https://www.visualstudio.com/downloads/). (Bude stačit bezplatná verze Community Edition.)

Abyste mohli použít službu Translator Text API, budete potřebovat klíč předplatného. Přečtěte si, [jak se zaregistrovat ve službě Translator Text API](translator-text-how-to-signup.md).

## <a name="languages-request"></a>Žádost Languages

Pomocí následujícího kódu získáte seznam jazyků podporovaných pro překlad, transkripci a vyhledávání ve slovníku a příklady pomocí metody [Languages](./reference/v3-0-languages.md).

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
        static string path = "/languages?api-version=3.0";

        // NOTE: Replace this example key with a valid subscription key.
        static string key = "ENTER KEY HERE";

        async static void GetLanguages()
        {
            HttpClient client = new HttpClient();
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);
            var uri = host + path;
            var response = await client.GetAsync(uri);
            var result = await response.Content.ReadAsStringAsync();
            var json = JsonConvert.SerializeObject(JsonConvert.DeserializeObject(result), Formatting.Indented);
            // Note: If writing to the console, set this.
            // Console.OutputEncoding = UnicodeEncoding.UTF8;
            System.IO.File.WriteAllBytes("output.txt", Encoding.UTF8.GetBytes(json));
        }

        static void Main(string[] args)
        {
            GetLanguages();
            Console.ReadLine();
        }
    }
}
```

## <a name="languages-response"></a>Odpověď metody Languages

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu:

```json
{
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    },
...
  },
  "transliteration": {
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "scripts": [
        {
          "code": "Arab",
          "name": "Arabic",
          "nativeName": "العربية",
          "dir": "rtl",
          "toScripts": [
            {
              "code": "Latn",
              "name": "Latin",
              "nativeName": "اللاتينية",
              "dir": "ltr"
            }
          ]
        },
        {
          "code": "Latn",
          "name": "Latin",
          "nativeName": "اللاتينية",
          "dir": "ltr",
          "toScripts": [
            {
              "code": "Arab",
              "name": "Arabic",
              "nativeName": "العربية",
              "dir": "rtl"
            }
          ]
        }
      ]
    },
...
  },
  "dictionary": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
...
  }
}
```

## <a name="next-steps"></a>Další kroky

Prozkoumejte vzorový kód pro tento rychlý start a další, včetně překladu a transkripce, a také další vzorové projekty Translator Text na GitHubu.

> [!div class="nextstepaction"]
> [Prozkoumejte příklady C# na GitHubu](https://aka.ms/TranslatorGitHub?type=&language=c%23)
