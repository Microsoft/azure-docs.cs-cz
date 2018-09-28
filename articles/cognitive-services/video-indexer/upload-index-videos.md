---
title: 'Příklad: Nahrání videa na server a jeho indexování pomocí Video Indexeru'
titlesuffix: Azure Cognitive Services
description: Toto téma ukazuje, jak nahrát video na server a indexovat ho pomocí Video Indexeru s využitím rozhraní API.
services: cognitive services
author: juliako
manager: cgronlun
ms.service: cognitive-services
ms.component: video-indexer
ms.topic: sample
ms.date: 09/15/2018
ms.author: juliako
ms.openlocfilehash: e84411535b82b3e4861b529f490bdde0eb25fd42
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "45983881"
---
# <a name="example-upload-and-index-your-videos"></a>Příklad: Nahrání videí na server a jejich indexování  

Tento článek ukazuje, jak nahrát video na server pomocí Azure Video Indexeru. Rozhraní API Video Indexeru umožňuje dva způsoby nahrání na server: 

* Nahrání videa na server z adresy URL (upřednostňovaná možnost)
* Odeslání souboru videa jako pole bajtů v těle požadavku

Tento článek ukazuje, jak používat API [Upload video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) (Nahrát video) k nahrání videí na server a jejich indexování na základě adresy URL. Vzorový kód v článku obsahuje okomentovaný kód, který ukazuje, jak nahrát pole bajtů.  

Článek se také zabývá některými parametry, které můžete v API nastavit, abyste proces a výstup API změnili.

> [!Note]
> Při vytváření účtu Video Indexeru můžete zvolit účet bezplatné zkušební verze (ve kterém získáte určitý počet minut indexování zdarma) nebo placenou variantu (ve které nejste omezení kvótou). <br/>V bezplatné zkušební verzi Video Indexer poskytuje až 600 minut bezplatného indexování pro uživatele webu a až 2400 minut bezplatného indexování pro uživatele rozhraní API. S placenou variantou vytvoříte účet Video Indexeru, který je [spojený s vaším předplatným Azure a účtem Azure Media Services](connect-to-azure.md). Platíte za indexované minuty a také poplatky související s účtem Media. 

## <a name="uploading-considerations"></a>Aspekty nahrávání videí na server
    
- Při nahrávání videa na server podle zadané adresy URL (upřednostňovaná možnost) musí být koncový bod zabezpečený pomocí protokolu TLS 1.2 (nebo vyššího).
- Možnost bajtového pole je omezená na 2 GB a vyprší po 30 min.
- Adresa URL zadaná v parametru `videoURL` musí být zakódovaná.

## <a name="configurations-and-params"></a>Konfigurace a parametry

Tato část popisuje některé volitelné parametry a kdy je vhodné je nastavit.

### <a name="externalid"></a>externalID 

Tento parametr umožňuje určit ID, které bude s videem spojené. ID můžete použít na integraci externího systému pro správu video obsahu. Videa, které se nacházejí na portálu Video Indexer, je možné pomocí zadaného externího ID vyhledávat.

### <a name="indexingpreset"></a>indexingPreset

Tento parametr použijte, pokud nezpracované nebo externí záznamy obsahují šum na pozadí. Tento parametr se používá ke konfiguraci indexovacího procesu. Můžete určit tyto hodnoty:

- `Default` – indexovat a extrahovat přehledy s využitím zvuku i videa
- `AudioOnly` – indexovat a extrahovat přehledy s využitím jenom zvuku (video se ignoruje)
- `DefaultWithNoiseReduction` – indexovat a extrahovat přehledy ze zvuku i videa při použití algoritmů snížení šumu na zvukový datový proud

Cena závisí na vybrané možnosti indexování.  

### <a name="callbackurl"></a>callbackUrl

Adresa POST URL k upozornění , že se indexování dokončilo. Video Indexer k ní přidá dva parametry řetězce dotazu: id a stav. Pokud je například adresa URL zpětného volání https://test.com/notifyme?projectName=MyProject, zašle se oznámení s dalšími parametry na https://test.com/notifyme?projectName=MyProject&id=1234abcd&state=Processed.

Do adresy URL můžete před přidáním volání do Video Indexeru přidat také další parametry, které se zahrnou do zpětného volání. Později můžete v kódu analyzovat řetězec dotazu a získat zpět všechny zadané parametry v řetězci dotazu (data, která jste původně připojili k adrese URL a informace dodané Video Indexerem). Adresa URL musí být zakódovaná.

### <a name="streamingpreset"></a>streamingPreset

Po nahrání videa na server ho Video Indexer volitelně zakóduje. Pak bude pokračovat k indexování a analýze videa. Když Video Indexer dokončí analýzu, dostanete oznámení s ID videa.  

Při použití API [Upload video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) (Nahrát video) nebo [Re-Index Video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) (Znovu indexovat video) je jedním z volitelných parametrů `streamingPreset`. Pokud nastavíte `streamingPreset` na `Default`, `SingleBitrate` nebo `AdaptiveBitrate`, aktivuje se proces kódování. Po dokončení úloh indexování a kódování se video publikuje, takže ho můžete také streamovat. Koncový bod streamování, ze kterého chcete video streamovat, musí být ve stavu **Spuštěno**.

Pokud chcete spustit úlohy indexování a kódování, [účet Azure Media Services připojený k vašemu účtu Video Indexer](connect-to-azure.md) vyžaduje rezervované jednotky. Další informace najdete v článku o [škálování zpracování médií](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview). Protože jsou tyto úlohy náročné na výpočetní prostředky, důrazně doporučujeme typ jednotky S3. Počet rezervovaných jednotek určuje maximální počet úloh, které můžou běžet paralelně. Základní doporučení je 10 rezervovaných jednotek S3. 

Pokud chcete video jenom indexovat, ale ne kódovat, nastavte `streamingPreset` na `NoStreaming`.

### <a name="videourl"></a>videoUrl

Adresa URL video/zvukového souboru k indexování. Adresa URL musí odkazovat na soubor média (stránky HTML nejsou podporované). Soubor se dá chránit přístupovým tokenem poskytnutým jako součást identifikátoru URI a koncový bod poskytující soubor musí být zabezpečený pomocí protokolu TLS 1.2 nebo vyššího. Adresa URL musí být zakódovaná. 

Pokud parametr `videoUrl` není zadaný, Video Indexer očekává, že soubor předáte jako obsah těla multipart/form.

## <a name="code-sample"></a>Ukázka kódu

Následující fragment kódu v jazyce C# předvádí společné použití všech rozhraní API Video Indexeru.

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



## <a name="next-steps"></a>Další kroky

[Prozkoumejte výstup Azure Video Indexeru vytvořený pomocí rozhraní API v2](video-indexer-output-json-v2.md)
