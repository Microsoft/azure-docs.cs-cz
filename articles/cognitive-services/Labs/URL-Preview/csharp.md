---
title: 'Rychlý start: Náhled adresy URL projektu,C#'
titlesuffix: Azure Cognitive Services
description: Začněte používat funkci Project URL Preview za použití jazyka C#.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: quickstart
ms.date: 03/16/2018
ms.author: rosh
ms.openlocfilehash: 88429aa10ef847800d0d1be60261c94934233016
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55227790"
---
# <a name="quickstart-url-preview-query-in-c"></a>Rychlý start: Náhled adresy URL dotazu vC#

Následující příklady v jazyce C# vytvoří náhled adresy URL pro web SwiftKey: https://swiftkey.com/en.

## <a name="prerequisites"></a>Požadavky

Ke spuštění tohoto kódu ve Windows budete potřebovat [Visual Studio 2017](https://www.visualstudio.com/downloads/). (Bude stačit bezplatná verze Community Edition.)

Získání přístupového klíče k bezplatné zkušební verzi služby [Cognitive Services Labs](https://aka.ms/answersearchsubscription)

## <a name="code-scenario"></a>Scénář kódu

Následující kód v jazyce C# vytvoří náhled adresy URL pro web SwiftKey: https://swiftkey.com/en. 

Implementuje se v následujících krocích:
1. Deklarujte proměnné, které určují koncový bod a adresu URL dotazu k náhledu.  
2. Vytvořte požadavek.
3. Přidejte hlavičku *Ocp-Apim-Subscription-Key*. 
4. Asynchronně spusťte webový požadavek. 
5. Přečtěte si odpověď.
6. Zobrazte hlavičky a výsledky JSON v konzole.

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

Spusťte aplikaci:

1. Vytvořte nové konzolové řešení v sadě Visual Studio.
2. Nahraďte `Program.cs` uvedeným kódem.
3. Hodnotu `YOUR-ACCESS-KEY` nahraďte platným přístupovým klíčem pro vaše předplatné.
4. Spusťte program.

## <a name="next-steps"></a>Další postup
- [Rychlý start pro Javu](java-quickstart.md)
- [Rychlý start pro JavaScript](javascript.md)
- [Rychlý start pro Node](node-quickstart.md)
- [Rychlý start pro Python](python-quickstart.md)
