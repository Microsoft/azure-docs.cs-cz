---
title: Rychlý start C# pro náhled adresy URL projektu - kognitivní služby Microsoft | Microsoft Docs
description: Začněte používat Preview adresa URL projektu v kognitivní služby společnosti Microsoft na platformě Azure.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/16/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 17d44bd0c23d0a1e67da5a0e91248700d3166c1a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343246"
---
# <a name="url-preview-query-in-c"></a>Adresa URL Preview dotazu v jazyce C#

Následující příklad jazyka C# vytvoří Náhled adresy Url pro web SwiftKey: https://swiftkey.com/en.

## <a name="prerequisites"></a>Požadavky

Budete potřebovat [Visual Studio 2017](https://www.visualstudio.com/downloads/) pro spuštění tohoto kódu v systému Windows. (Bezplatná edice Community bude fungovat.)

Získat přístupový klíč pro bezplatné zkušební verze [kognitivní Labs služby](https://aka.ms/answersearchsubscription)

## <a name="code-scenario"></a>Scénář kódu

Následující kód C# vytvoří náhled adresu URL webu SwiftKey: https://swiftkey.com/en. 

Jsou implementované v následujících krocích:
1. Deklarujte proměnné zadat koncový bod a adresu URL dotazu si verzi preview.  
2. Vytvoření požadavku.
3. Přidat *Ocp-Apim-Subscription-Key* záhlaví. 
4. Asynchronně spusťte webový požadavek. 
5. Přečtěte si odpovědi.
6. Tisk záhlaví a výsledky JSON do konzoly.

**Zdrojový kód**

```
using System;
using System.IO;
using System.Net;
using System.Text;

namespace UrlPrevCshp
{
    class Program
    {
        static void Main(string[] args)
        {
            String uriBase = "https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search";
            String searchQuery = "https://swiftkey.com/en"; 
            var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(searchQuery);

            // Do the Web request and get response
            WebRequest request = HttpWebRequest.Create(uriQuery);
            request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR-SUBSCRIPTION-KEY";
            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            Console.WriteLine("\nHTTP Headers:\n");
            foreach (String header in response.Headers)
            {
                if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
                    Console.WriteLine(header + ": " + response.Headers[header]);
            }

            Console.WriteLine(JsonPrettyPrint(json));
            

            Console.ReadKey();
        }


        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            char last = ' ';
            int offset = 0;
            int indentLength = 2;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\\':
                        if (quote && last != '\\') ignore = true;
                        break;
                }

                if (quote)
                {
                    sb.Append(ch);
                    if (last == '\\' && ignore) ignore = false;
                }
                else
                {
                    switch (ch)
                    {
                        case '{':
                        case '[':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', ++offset * indentLength));
                            break;
                        case '}':
                        case ']':
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', --offset * indentLength));
                            sb.Append(ch);
                            break;
                        case ',':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', offset * indentLength));
                            break;
                        case ':':
                            sb.Append(ch);
                            sb.Append(' ');
                            break;
                        default:
                            if (quote || ch != ' ') sb.Append(ch);
                            break;
                    }
                }
                last = ch;
            }

            return sb.ToString().Trim();
        }

    }
}

```
## <a name="running-the-application"></a>Spouštění aplikace.

Ke spuštění aplikace:

1. Vytvořte nové řešení konzoly v sadě Visual Studio.
2. Nahraďte `Program.cs` s poskytnutý kód.
3. Nahraďte `YOUR-ACCESS-KEY` hodnotu s platnou přístupový klíč pro vaše předplatné.
4. Spusťte program.

## <a name="next-steps"></a>Další postup
- [Rychlý start Java](java-quickstart.md)
- [Rychlý start JavaScript](javascript.md)
- [Rychlé spuštění uzlu](node-quickstart.md)
- [Rychlý start Python](python-quickstart.md)