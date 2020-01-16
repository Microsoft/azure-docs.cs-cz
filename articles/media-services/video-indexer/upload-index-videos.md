---
title: Nahrání videa na server a jeho indexování pomocí Video Indexeru
titleSuffix: Azure Media Services
description: Toto téma ukazuje, jak nahrát video na server a indexovat ho pomocí Video Indexeru s využitím rozhraní API.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: juliako
ms.openlocfilehash: c4c39dc53e492fd295cf30a7b7d75c933ebc912f
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75972630"
---
# <a name="upload-and-index-your-videos"></a>Nahrání videí na server a jejich indexování  

Při nahrávání videí pomocí rozhraní Video Indexer API máte následující možnosti nahrávání: 

* Nahrání videa na server z adresy URL (upřednostňovaná možnost)
* Odeslat videosoubor jako pole bajtů v textu žádosti
* Použijte existující Asset Azure Media Services tím, že zadáte [ID assetu](https://docs.microsoft.com/azure/media-services/latest/assets-concept) (podporované jenom u placených účtů).

Tento článek ukazuje, jak používat API [Upload video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) (Nahrát video) k nahrání videí na server a jejich indexování na základě adresy URL. Vzorový kód v článku obsahuje okomentovaný kód, který ukazuje, jak nahrát pole bajtů. <br/>Článek se také zabývá některými parametry, které můžete v API nastavit, abyste proces a výstup API změnili.

Po nahrání videa Video Indexer případně zakódovat video (popsané v článku). Při vytváření účtu Video Indexeru můžete zvolit účet bezplatné zkušební verze (ve kterém získáte určitý počet minut indexování zdarma) nebo placenou variantu (ve které nejste omezení kvótou). V bezplatné zkušební verzi Video Indexer poskytuje až 600 minut bezplatného indexování pro uživatele webu a až 2400 minut bezplatného indexování pro uživatele rozhraní API. Pomocí placené možnosti vytvoříte účet Video Indexer, který je [připojený k vašemu předplatnému Azure a účet Azure Media Services](connect-to-azure.md). Platíte za indexované minuty a také poplatky související s účtem Media. 

## <a name="uploading-considerations-and-limitations"></a>Odesílání důležitých informací a omezení
 
- Název videa nesmí být delší než 80 znaků.
- Při nahrávání videa na základě adresy URL (upřednostňované) musí být koncový bod zabezpečený pomocí TLS 1,2 (nebo vyšší).
- Velikost nahrávání s možností URL je omezená na 30 GB.
- Délka adresy URL žádosti je omezená na 6144 znaků, kde délka adresy URL řetězce dotazu je omezená na 4096 znaků.
- Velikost nahrávání s možností bajtového pole je omezená na 2 GB.
- Časový limit možnosti bajtového pole vyprší po 30 minutách.
- Adresa URL poskytnutá parametrem `videoURL` musí být zakódována.
- Indexace Media Services prostředků má stejné omezení jako indexování z adresy URL.
- Video Indexer má pro jeden soubor maximální dobu trvání 4 hodiny.
- Můžete nahrávat až 60 filmů za minutu.

> [!Tip]
> Doporučujeme používat rozhraní .NET Framework verze 4.6.2 nebo novější, protože starší rozhraní .NET Framework nemají ve výchozím nastavení protokol TLS 1.2.
>
> Pokud potřebujete použít starší rozhraní .NET Framework, před voláním rozhraní REST API přidejte do kódu jeden řádek:  <br/> System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls12;

## <a name="configurations-and-params"></a>Konfigurace a parametry

Tato část popisuje některé volitelné parametry a kdy je vhodné je nastavit.

### <a name="externalid"></a>externalID 

Tento parametr umožňuje určit ID, které bude s videem spojené. ID můžete použít na integraci externího systému pro správu video obsahu. Videa, které se nacházejí na portálu Video Indexer, je možné pomocí zadaného externího ID vyhledávat.

### <a name="callbackurl"></a>callbackUrl

Adresa URL, která se používá k upozornění na zákazníka (pomocí žádosti POST) o následujících událostech:

- Změna stavu indexování: 
    - Vlastnosti:    
    
        |Name (Název)|Popis|
        |---|---|
        |id|ID videa|
        |state|Stav videa|  
    - Příklad: https:\//test.com/notifyme?projectName=MyProject&id=1234abcd&state=Processed
- Osoba identifikovaná ve videu:
  - Vlastnosti
    
      |Name (Název)|Popis|
      |---|---|
      |id| ID videa|
      |faceId|ID obličeje, které se zobrazí v indexu videa|
      |knownPersonId|Jedinečné ID osoby v rámci modelu obličeje|
      |personName|Jméno osoby|
        
    - Příklad: https:\//test.com/notifyme?projectName=MyProject&id=1234abcd&faceid=12&knownPersonId=CCA84350-89B7-4262-861C-3CAC796542A5&personName=Inigo_Montoya 

#### <a name="notes"></a>Poznámky

- Video Indexer vrátí všechny existující parametry zadané v původní adrese URL.
- Zadaná adresa URL musí být kódovaná.

### <a name="indexingpreset"></a>indexingPreset

Tento parametr použijte, pokud nezpracované nebo externí záznamy obsahují šum na pozadí. Tento parametr se používá ke konfiguraci indexovacího procesu. Můžete určit tyto hodnoty:

- `AudioOnly` – indexovat a extrahovat přehledy s využitím jenom zvuku (video se ignoruje)
- `VideoOnly` – Indexujte a extrahujte přehledy jenom pomocí videa (ignoruje zvuk).
- `Default` – indexovat a extrahovat přehledy s využitím zvuku i videa
- `DefaultWithNoiseReduction` – indexovat a extrahovat přehledy ze zvuku i videa při použití algoritmů snížení šumu na zvukový datový proud

Cena závisí na vybrané možnosti indexování.  

### <a name="priority"></a>priorita

Videa se indexují Video Indexer podle jejich priority. Pro určení priority indexu použijte parametr **priority** . Platné jsou následující hodnoty: **Nízká**, **normální** (výchozí) a **Vysoká**.

Parametr **priority** se podporuje jenom pro placené účty.

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

### <a name="instructions-for-running-this-code-sample"></a>Pokyny pro spuštění této ukázky kódu

Po zkopírování tohoto kódu na vývojovou platformu budete muset zadat dva parametry: API Management ověřovací klíč a adresu URL videa.

* Klíč rozhraní API – klíč rozhraní API je klíč předplatného služby API Management, který vám umožní získat přístupový token, aby mohl provádět operace s účtem Video Indexer. 

    Pokud chcete získat klíč rozhraní API, Projděte si tento tok:

    * Přejděte na https://api-portal.videoindexer.ai/
    * Přihlášení
    * Přejít na **produkty** -> **authorization** -> **autorizační předplatné**
    * Zkopírování **primárního klíče**
* Adresa URL videa – adresa URL videosouboru nebo zvukového souboru, který se má indexovat. Adresa URL musí odkazovat na soubor média (stránky HTML nejsou podporované). Soubor se dá chránit přístupovým tokenem poskytnutým jako součást identifikátoru URI a koncový bod poskytující soubor musí být zabezpečený pomocí protokolu TLS 1.2 nebo vyššího. Adresa URL musí být zakódovaná.

Výsledek úspěšného spuštění ukázky kódu bude zahrnovat adresu URL widgetu Insight a adresu URL widgetu přehrávače, která vám umožní zkoumat v uvedeném pořadí přehledy a videa. 


```csharp
public async Task Sample()
{
    var apiUrl = "https://api.videoindexer.ai";
    var apiKey = "..."; // replace with API key taken from https://aka.ms/viapi

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
    
    // take the relevant account, here we simply take the first, 
    // you can also get the account via accounts.First(account => account.Id == <GUID>);
    var accountInfo = accounts.First();

    // we will use the access token from here on, no need for the apim key
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // upload a video
    var content = new MultipartFormDataContent();
    Console.WriteLine("Uploading...");
    // get the video from URL
    var videoUrl = "VIDEO_URL"; // replace with the video URL

    // as an alternative to specifying video URL, you can upload a file.
    // remove the videoUrl parameter from the query params below and add the following lines:
    //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
    //byte[] buffer =new byte[video.Length];
    //video.Read(buffer, 0, buffer.Length);
    //content.Add(new ByteArrayContent(buffer));

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

    // get the video ID from the upload result
    string videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
    Console.WriteLine("Uploaded");
    Console.WriteLine("Video ID:");
    Console.WriteLine(videoId);

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

        Console.WriteLine("");
        Console.WriteLine("State:");
        Console.WriteLine(processingState);

        // job is finished
        if (processingState != "Uploaded" && processingState != "Processing")
        {
            Console.WriteLine("");
            Console.WriteLine("Full JSON:");
            Console.WriteLine(videoGetIndexResult);
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
    Console.WriteLine("");
    Console.WriteLine("Search:");
    Console.WriteLine(searchResult);

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
    Console.WriteLine("Insights Widget url:");
    Console.WriteLine(insightsWidgetLink);

    // get player widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
        });
    var playerWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/PlayerWidget?{queryParams}");
    var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
     Console.WriteLine("");
     Console.WriteLine("Player Widget url:");
     Console.WriteLine(playerWidgetLink);
     Console.WriteLine("\nPress Enter to exit...");
     String line = Console.ReadLine();
     if (line == "enter")
     {
         System.Environment.Exit(0);
     }

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
## <a name="common-errors"></a>Běžné chyby

Operace Upload může vrátit kódy stavu uvedené v následující tabulce.

|Kód stavu|ErrorType (v textu odpovědi)|Popis|
|---|---|---|
|409|VIDEO_INDEXING_IN_PROGRESS|V daném účtu už probíhá zpracování stejného videa.|
|400|VIDEO_ALREADY_FAILED|V daném účtu se méně než před 2 hodinami nepodařilo zpracovat stejné video. Klienti rozhraní API by měli před dalším nahráním videa vyčkat minimálně 2 hodiny.|

## <a name="next-steps"></a>Další kroky

[Projděte si výstup Azure Video Indexer vytvořený pomocí rozhraní API](video-indexer-output-json-v2.md)
