---
title: Bing Image nahrát přehledů (zdrojový kód) | Microsoft Docs
description: Zdrojový kód pro konzolovou aplikaci, která používá rozhraní API služby Bing Image Search přehledné bitové kopie a nahrajte image.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 12/07/2017
ms.author: v-gedod
ms.openlocfilehash: c2505e695ff3d8970e34a0a825510053712ea120
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342433"
---
# <a name="tutorial-bing-image-upload-for-insights"></a>Kurz: Bitové kopie Bingu nahrát přehledů

Toto je úplný zdrojový kód [Bing Image nahrát přehledů](tutorial-image-post.md) kurz, který používá koncový bod hledání bitové kopie s `POST` požadavku. Pokud chcete spustit aplikaci, zkopírujte zdrojový kód do konzolovou aplikaci C#. Sestavte a spusťte v ladicím programu sady Visual Studio.

```
using System;
using System.Collections.Generic;
using System.Text;
using System.Net;

namespace ConsoleAppPost
{
    class Program
    {
        // Replace the accessKey string value with your valid access key.
        const string accessKey = "enter key here";

        // The endpoint URI.  
        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/details";

        // The image to upload. Replace with your file and path.
        const string imageFile = "your-image-file.jpg";

        // Used to return image results including relevant headers
        struct SearchResult
        {
            public String jsonResult;
            public Dictionary<String, String> relevantHeaders;
        }

        static void Main()
        {
            Console.OutputEncoding = System.Text.Encoding.UTF8;

            if (accessKey.Length == 32)
            {
                //Console.WriteLine("Searching images for: " + searchTerm);

                SearchResult result = BingImageSearch(uriBase);

                Console.WriteLine("\nRelevant HTTP Headers:\n");
                foreach (var header in result.relevantHeaders)
                    Console.WriteLine(header.Key + ": " + header.Value);

                Console.WriteLine("\nJSON Response:\n");
                Console.WriteLine(JsonPrettyPrint(result.jsonResult));
            }
            else
            {
                Console.WriteLine("Invalid Bing Search API subscription key!");
                Console.WriteLine("Please paste yours into the source code.");
            }

            Console.Write("\nPress Enter to exit ");
            Console.ReadLine();
        }

        /// <summary>
        /// Performs a Bing Image search and return the results as a SearchResult.
        /// </summary>
        static SearchResult BingImageSearch(string uriStr)
        {
            WebClient client = new WebClient();
            client.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
            client.Headers["ContentType"] = "multipart/form-data"; 

            byte[] resp = client.UploadFile(uriBase + "?modules=All", imageFile);
            var json = System.Text.Encoding.Default.GetString(resp);

            // Create result object for return
            var searchResult = new SearchResult()
            {
                jsonResult = json,
                relevantHeaders = new Dictionary<String, String>()
            };
            
            return searchResult;
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