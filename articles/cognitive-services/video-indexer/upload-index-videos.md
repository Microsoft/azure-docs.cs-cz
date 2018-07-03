---
title: Nahrání a indexovat vaše videa pomocí Azure Video Indexer | Dokumentace Microsoftu
description: Toto téma ukazuje, jak pomocí rozhraní API můžete nahrát a indexovat vaše videa pomocí Azure Video Indexer
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 05/30/2018
ms.author: juliako
ms.openlocfilehash: df3ebdcb07980c297204d6d2959cac6a759b34e2
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2018
ms.locfileid: "37347394"
---
# <a name="upload-and-index-your-videos"></a>Nahrání a indexovat vaše videa  

Tento článek popisuje, jak používat [nahrát video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) rozhraní API k nahrání a indexovat vaše videa pomocí Azure Video Indexer. Také popisuje některé z parametrů, můžete nastavit na rozhraní API Chcete-li změnit představu o procesu a výstup tohoto rozhraní API.

## <a name="configurations-and-params"></a>Konfigurace a parametry

Tato část popisuje některé volitelné parametry a kdy je vhodné je nastavit.

### <a name="externalid"></a>externalID 

Tento parametr umožňuje určit ID, které mají být asociována s videem. ID můžete použít pro externí systémové integrace "Video obsahu Management" (VCM). Videa, které se nacházejí na portálu pro Video Indexer lze prohledávat pomocí zadané externí ID.

### <a name="indexingpreset"></a>indexingPreset

Tento parametr použijte, pokud raw nebo externí záznamy obsahují šum na pozadí. Tento parametr se používá ke konfiguraci indexovacím procesem. Můžete zadat následující hodnoty:

- `Default` – Index a extrahovat přehledy s využitím audio a video
- `AudioOnly` – Index a extrahovat přehledy s využitím zvuku pouze (ignoruje video)
- `DefaultWithNoiseReduction` – Index a extrakce poznatků z zvuku a videa, při použití algoritmů snížení šumu na zvukový datový proud

Cena závisí na vybrané možnosti indexování.  

### <a name="streamingpereset"></a>streamingPereset

Jakmile je Nahraná videa, Video Indexer, volitelně kóduje videa. Pak pokračuje a indexování a analýza videa. Když Video Indexer probíhá analýza, zobrazí se oznámení s ID videa.  

Při použití [nahrát video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) nebo [znovu Indexujte videa](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) rozhraní API, volitelné parametry je `streamingPreset`. Pokud nastavíte `streamingPereset` k `Default`, `SingleBitrate`, nebo `AdaptiveBitrate`, se aktivuje procesu kódování. Po dokončení indexování a úloh kódování, bude video publikované, můžete také streamování videa. Koncového bodu streamování ze kterého chcete Streamovat videa musí být v **systémem** stavu.

Chcete-li spustit indexování a úloh kódování [připojení účtu Azure Media Services pro váš účet služby Video Indexer](connect-to-azure.md), vyžaduje rezervovaných jednotek. Další informace najdete v tématu [škálování zpracování médií](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview). Protože jde o úlohy náročné na výpočetní prostředky, je důrazně doporučujeme typ jednotky S3. Počet jednotek ru určuje maximální počet úloh, které můžou běžet paralelně. Základní doporučení je 10 jednotek ru S3. 

Pokud chcete pouze indexovat vaše video, ale ne kódování, nastavte `streamingPereset`k `NoStreaming`.

## <a name="code-sample"></a>Ukázka kódu

Následující fragment kódu jazyka C# ukazuje využití všechna rozhraní Video Indexer API společně.

```csharp
public async Task Sample()
{
    var apiUrl = "https://api.videoindexer.ai";
    var location = "westus2";
    var apiKey = "...";

    System.Net.ServicePointManager.SecurityProtocol =
        System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

    // create the http client
    var handler = new HttpClientHandler();
    handler.AllowAutoRedirect = false;
    var client = new HttpClient(handler);
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);

    // obtain account information and access token
    string queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"generateAccessTokens", "true"},
            {"allowEdit", "true"},
        });
    HttpResponseMessage result = await client.GetAsync($"{apiUrl}/auth/trial/Accounts?{queryParams}");
    var json = await result.Content.ReadAsStringAsync();
    var accounts = JsonConvert.DeserializeObject<AccountContractSlim[]>(json);
    // take the relevant account, here we simply take the first
    var accountInfo = accounts.First();

    // we will use the access token from here on, no need for the apim key
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // upload a video
    var content = new MultipartFormDataContent();
    Debug.WriteLine("Uploading...");
    // get the video from URL
    var videoUrl = "VIDEO_URL"; // replace with the video URL

    // as an alternative to specifying video URL, you can upload a file.
    // remove the videoUrl parameter from the query params below and add the following lines:
    //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
    //byte[] buffer =newbyte[video.Length];
    //video.Read(buffer, 0, buffer.Length);
    //content.Add(newByteArrayContent(buffer));

    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"name", "video_name"},
            {"description", "video_description"},
            {"privacy", "private"},
            {"partition", "partition"},
            {"videoUrl", videoUrl},
        });
    var uploadRequestResult = await client.PostAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos?{queryParams}", content);
    var uploadResult = await uploadRequestResult.Content.ReadAsStringAsync();

    // get the video id from the upload result
    string videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
    Debug.WriteLine("Uploaded");
    Debug.WriteLine("Video ID:");
    Debug.WriteLine(videoId);

    // wait for the video index to finish
    while (true)
    {
        await Task.Delay(10000);

        queryParams = CreateQueryString(
            new Dictionary<string, string>()
            {
                {"accessToken", accountInfo.AccessToken},
                {"language", "English"},
            });

        var videoGetIndexRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/Index?{queryParams}");
        var videoGetIndexResult = await videoGetIndexRequestResult.Content.ReadAsStringAsync();

        string processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

        Debug.WriteLine("");
        Debug.WriteLine("State:");
        Debug.WriteLine(processingState);

        // job is finished
        if (processingState != "Uploaded" && processingState != "Processing")
        {
            Debug.WriteLine("");
            Debug.WriteLine("Full JSON:");
            Debug.WriteLine(videoGetIndexResult);
            break;
        }
    }

    // search for the video
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"id", videoId},
        });

    var searchRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/Search?{queryParams}");
    var searchResult = await searchRequestResult.Content.ReadAsStringAsync();
    Debug.WriteLine("");
    Debug.WriteLine("Search:");
    Debug.WriteLine(searchResult);

    // Generate video access token (used for get widget calls)
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
    var videoTokenRequestResult = await client.GetAsync($"{apiUrl}/auth/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/AccessToken?allowEdit=true");
    var videoAccessToken = (await videoTokenRequestResult.Content.ReadAsStringAsync()).Replace("\"", "");
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // get insights widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
            {"widgetType", "Keywords"},
            {"allowEdit", "true"},
        });
    var insightsWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/InsightsWidget?{queryParams}");
    var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
    Debug.WriteLine("Insights Widget url:");
    Debug.WriteLine(insightsWidgetLink);

    // get player widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
        });
    var playerWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/PlayerWidget?{queryParams}");
    var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
    Debug.WriteLine("");
    Debug.WriteLine("Player Widget url:");
    Debug.WriteLine(playerWidgetLink);
}

private string CreateQueryString(IDictionary<string, string> parameters)
{
    var queryParameters = HttpUtility.ParseQueryString(string.Empty);
    foreach (var parameter in parameters)
    {
        queryParameters[parameter.Key] = parameter.Value;
    }

    return queryParameters.ToString();
}

public class AccountContractSlim
{
    public Guid Id { get; set; }
    public string Name { get; set; }
    public string Location { get; set; }
    public string AccountType { get; set; }
    public string Url { get; set; }
    public string AccessToken { get; set; }
}
```



## <a name="next-steps"></a>Další postup

[Prozkoumejte Azure Video Indexer výstup vytvořený pomocí rozhraní API v2](video-indexer-output-json-v2.md)
