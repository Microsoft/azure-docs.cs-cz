---
title: Publikování znalostní báze – rychlý start pro C# – Azure Cognitive Services | Microsoft Docs
description: Jak publikovat znalostní bázi pro službu QnA Maker v C#
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.technology: qna-maker
ms.topic: quickstart
ms.date: 06/18/2018
ms.author: nolachar
ms.openlocfilehash: 2517e7d70b3235c419faf98f452d2b5f43dfad88
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2018
ms.locfileid: "43768981"
---
# <a name="publish-a-knowledge-base-in-c"></a>Publikování znalostní báze v C#

Následující kód publikuje existující znalostní bázi pomocí metody [Publish](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe).

1. Ve svém oblíbeném prostředí IDE vytvořte nový projekt C#.
2. Přidejte níže uvedený kód.
3. Hodnotu `key` nahraďte přístupovým klíčem platným pro vaše předplatné.
4. Spusťte program.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace QnAMaker
{
    class Program
    {
        static string host = "https://westus.api.cognitive.microsoft.com";
        static string service = "/qnamaker/v4.0";
        static string method = "/knowledgebases/";

        // NOTE: Replace this with a valid subscription key.
        static string key = "ENTER KEY HERE";

        // NOTE: Replace this with a valid knowledge base ID.
        static string kb = "ENTER ID HERE";

        static string PrettyPrint(string s)
        {
            return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
        }

        async static Task<string> Post(string uri)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                if (response.IsSuccessStatusCode)
                {
                    return "{'result' : 'Success.'}";
                }
                else
                {
                    return await response.Content.ReadAsStringAsync();
                }
            }
        }

        async static void PublishKB()
        {
            var uri = host + service + method + kb;
            Console.WriteLine("Calling " + uri + ".");
            var response = await Post(uri);
            Console.WriteLine(PrettyPrint(response));
            Console.WriteLine("Press any key to continue.");
        }

        static void Main(string[] args)
        {
            PublishKB();
            Console.ReadLine();
        }
    }
}

```

## <a name="the-publish-a-knowledge-base-response"></a>Odpověď na publikování znalostní báze

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu: 

```json
{
  "result": "Success."
}
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Reference k rozhraní REST API služby QnA Maker (V4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)