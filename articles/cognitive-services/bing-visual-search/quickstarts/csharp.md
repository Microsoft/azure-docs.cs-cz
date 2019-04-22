---
title: 'Rychlý start: Získejte přehledy obrázků pomocí API REST pro vizuální vyhledávání Bingu aC#'
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak k nahrání obrázku do Visual API Bingu pro vyhledávání a získávat přehledy o něm.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 3/28/2019
ms.author: scottwhi
ms.openlocfilehash: d2f5e87bd6c6780e8504abe1753e90eca5db763a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "58880402"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-c"></a>Rychlý start: Přehledné znázornění image pomocí rozhraní REST API vizuální vyhledávání Bingu aC#

Tento rychlý start ukazuje, jak k nahrání obrázku do API vizuální vyhledávání Bingu a chcete-li zobrazit přehledy, které vrátí.

## <a name="prerequisites"></a>Požadavky

* Libovolná edice sady [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* [Json.NET framework](https://www.newtonsoft.com/json), která je dostupná jako balíček NuGet.
* Pokud používáte Linux nebo MacOS, můžete spustit tuto aplikaci pomocí [Mono](https://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Vytvoření a inicializace projektu

1. V sadě Visual Studio vytvořte nové řešení konzoly s názvem BingSearchApisQuickStart. Přidejte následující obory názvů do souboru hlavní kódu:

    ```csharp
    using System;
    using System.Text;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    ```

2. Přidejte proměnné pro váš klíč předplatného, koncový bod a cestu k bitové kopii, kterou chcete nahrát:

    ```csharp
        const string accessKey = "<my_subscription_key>";
        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
        static string imagePath = @"<path_to_image>";
    ```

3. Vytvořit metodu s názvem `GetImageFileName()` k získání cesty pro vaši image:
    
    ```csharp
    static string GetImageFileName(string path)
            {
                return new FileInfo(path).Name;
            }
    ```

4. Vytvořte metodu k získání binárních dat na obrázku:

    ```csharp
    static byte[] GetImageBinary(string path)
    {
        return File.ReadAllBytes(path);
    }
    ```

## <a name="build-the-form-data"></a>Data formuláře sestavení

Odešlete místní image, vytvoříte nejprve data formuláře odeslat k rozhraní API. Musí zahrnovat data formuláře `Content-Disposition` záhlaví, jeho `name` parametr musí být nastaven na "image" a `filename` parametr lze nastavit na libovolný řetězec. Obsah ve formátu obsahovat binární data bitové kopie. Maximální velikost, kterou můžete nahrát je 1 MB.

    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

1. Přidáte hranici řetězce pro formátování data formuláře POST. Hranice řetězce určují znaků zahájení, ukončení a znakem nového řádku pro data:

    ```csharp
    // Boundary strings for form data in body of POST.
    const string CRLF = "\r\n";
    static string BoundaryTemplate = "batch_{0}";
    static string StartBoundaryTemplate = "--{0}";
    static string EndBoundaryTemplate = "--{0}--";
    ```

2. K přidání parametrů do data formuláře použijte následující proměnné:

    ```csharp
    const string CONTENT_TYPE_HEADER_PARAMS = "multipart/form-data; boundary={0}";
    const string POST_BODY_DISPOSITION_HEADER = "Content-Disposition: form-data; name=\"image\"; filename=\"{0}\"" + CRLF +CRLF;
    ```

3. Vytvoření funkce s názvem `BuildFormDataStart()` vytvořit úvodní formulář data s využitím hranice řetězce a cesta k bitové kopii:
    
    ```csharp
        static string BuildFormDataStart(string boundary, string filename)
        {
            var startBoundary = string.Format(StartBoundaryTemplate, boundary);

            var requestBody = startBoundary + CRLF;
            requestBody += string.Format(POST_BODY_DISPOSITION_HEADER, filename);

            return requestBody;
        }
    ```

4. Vytvoření funkce s názvem `BuildFormDataEnd()` vytvořit koncové formulář dat pomocí řetězce hranic:
    
    ```csharp
        static string BuildFormDataEnd(string boundary)
        {
            return CRLF + CRLF + string.Format(EndBoundaryTemplate, boundary) + CRLF;
        }
    ```

## <a name="call-the-bing-visual-search-api"></a>Volání rozhraní API vizuální vyhledávání Bingu

1. Vytvoření funkce k volání koncového bodu pro vizuální vyhledávání Bingu a vrátí odpověď JSON. Funkce přebírá počáteční a koncové formulář dat, bajtové pole obsahující data bitové kopie a `contentType` hodnotu.

2. Použití `WebRequest` pro ukládání identifikátorů URI, hodnota contentType a záhlaví.  

3. Použití `request.GetRequestStream()` zapisovat data formuláře a obrázek a potom získat odpověď. Funkce by měl vypadat přibližně následující:
        
    ```csharp
        static string BingImageSearch(string startFormData, string endFormData, byte[] image, string contentTypeValue)
        {
            WebRequest request = HttpWebRequest.Create(uriBase);
            request.ContentType = contentTypeValue;
            request.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
            request.Method = "POST";

            // Writes the boundary and Content-Disposition header, then writes
            // the image binary, and finishes by writing the closing boundary.
            using (Stream requestStream = request.GetRequestStream())
            {
                StreamWriter writer = new StreamWriter(requestStream);
                writer.Write(startFormData);
                writer.Flush();
                requestStream.Write(image, 0, image.Length);
                writer.Write(endFormData);
                writer.Flush();
                writer.Close();
            }

            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            return json;
        }
    ```

## <a name="create-the-main-method"></a>Vytvořit metodu Main

1. V `Main` metoda vaší aplikace, získejte název souboru a binární data bitové kopie:

    ```csharp
    var filename = GetImageFileName(imagePath);
    var imageBinary = GetImageBinary(imagePath);
    ```

2. Nastavte text příspěvku formátování hranice. Poté zavolejte `startFormData()` a `endFormData` vytvořit data formuláře:

    ```csharp
    // Set up POST body.
    var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
    var startFormData = BuildFormDataStart(boundary, filename);
    var endFormData = BuildFormDataEnd(boundary);
    ```

3. Vytvořte `ContentType` hodnoty formátování `CONTENT_TYPE_HEADER_PARAMS` a hranice data formuláře:

    ```csharp
    var contentTypeHdrValue = string.Format(CONTENT_TYPE_HEADER_PARAMS, boundary);
    ```

4. Získejte odpovědi rozhraní API pomocí volání `BingImageSearch()` a tisku odpověď:

    ```csharp
    var json = BingImageSearch(startFormData, endFormData, imageBinary, contentTypeHdrValue);
    Console.WriteLine(json);
    Console.WriteLine("enter any key to continue");
    Console.readKey();
    ```

## <a name="using-httpclient"></a>Jak použít HTTPClient

Pokud používáte `HttpClient`, můžete použít `MultipartFormDataContent` třídy pro vytváření dat formuláře. Stačí použijte následující části kódu k nahrazení odpovídajících metod v předchozím příkladu.

Nahradit `Main` metoda s tímto kódem:

```csharp
        static void Main()
        {
            try
            {
                Console.OutputEncoding = System.Text.Encoding.UTF8;

                if (accessKey.Length == 32)
                {
                    if (IsImagePathSet(imagePath))
                    {
                        var filename = GetImageFileName(imagePath);
                        Console.WriteLine("Getting image insights for image: " + filename);
                        var imageBinary = GetImageBinary(imagePath);

                        var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
                        var json = BingImageSearch(imageBinary, boundary, uriBase, accessKey);

                        Console.WriteLine("\nJSON Response:\n");
                        Console.WriteLine(JsonPrettyPrint(json));
                    }
                }
                else
                {
                    Console.WriteLine("Invalid Bing Visual Search API subscription key!");
                    Console.WriteLine("Please paste yours into the source code.");
                }

                Console.Write("\nPress Enter to exit ");
                Console.ReadLine();
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
            }
        }
```

Nahradit `BingImageSearch` metoda s tímto kódem:

```csharp
        /// <summary>
        /// Calls the Bing visual search endpoint and returns the JSON response.
        /// </summary>
        static string BingImageSearch(byte[] image, string boundary, string uri, string subscriptionKey)
        {
            var requestMessage = new HttpRequestMessage(HttpMethod.Post, uri);
            requestMessage.Headers.Add("Ocp-Apim-Subscription-Key", accessKey);

            var content = new MultipartFormDataContent(boundary);
            content.Add(new ByteArrayContent(image), "image", "myimage");
            requestMessage.Content = content;

            var httpClient = new HttpClient();

            Task<HttpResponseMessage> httpRequest = httpClient.SendAsync(requestMessage, HttpCompletionOption.ResponseContentRead, CancellationToken.None);
            HttpResponseMessage httpResponse = httpRequest.Result;
            HttpStatusCode statusCode = httpResponse.StatusCode;
            HttpContent responseContent = httpResponse.Content;

            string json = null;

            if (responseContent != null)
            {
                Task<String> stringContentsTask = responseContent.ReadAsStringAsync();
                json = stringContentsTask.Result;
            }

            return json;
        }
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvořit pro vizuální vyhledávání jednostránkovou webovou aplikaci](../tutorial-bing-visual-search-single-page-app.md)
