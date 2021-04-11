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
ms.date: 03/04/2021
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 3cc9051190bd314ac93e3de2689a6aa0ec2b6235
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106108077"
---
# <a name="upload-and-index-your-videos"></a>Nahrání videí na server a jejich indexování  

Po nahrání videa Video Indexer (volitelně) zakóduje video (popsané v článku). Při vytváření účtu Video Indexeru můžete zvolit účet bezplatné zkušební verze (ve kterém získáte určitý počet minut indexování zdarma) nebo placenou variantu (ve které nejste omezení kvótou). V bezplatné zkušební verzi Video Indexer poskytuje až 600 minut bezplatného indexování pro uživatele webu a až 2400 minut bezplatného indexování pro uživatele rozhraní API. S placenou variantou vytvoříte účet Video Indexeru, který je [spojený s vaším předplatným Azure a účtem služby Azure Media Services](connect-to-azure.md). Platíte za minuty, další informace najdete v tématu [Media Services ceny](https://azure.microsoft.com/pricing/details/media-services/).

Při nahrávání videí pomocí rozhraní API Video Indexeru máte následující možnosti nahrání: 

* Nahrání videa na server z adresy URL (upřednostňovaná možnost)
* Odeslat videosoubor jako pole bajtů v textu žádosti
* Pokud chcete použít existující aktivum služby Azure Media Services, zadejte [ID aktiva](../latest/assets-concept.md) (tato možnost se podporuje pouze u placených účtů).

V tomto článku se dozvíte, jak nahrát a indexovat videa pomocí těchto možností:

* [Web Video Indexeru](#upload-and-index-a-video-using-the-video-indexer-website) 
* [Rozhraní API Video Indexeru](#upload-and-index-with-api)

## <a name="supported-file-formats-for-video-indexer"></a>Podporované formáty souborů pro Video Indexer

Seznam formátů souborů, které můžete použít s Video Indexer, najdete v článku o [formátech vstupního kontejneru/souboru](../latest/encode-media-encoder-standard-formats-reference.md) .

## <a name="video-files-storage"></a>Úložiště souborů videí

- S placeným účtem Video Indexer vytvoříte Video Indexer účet, který je připojený k vašemu předplatnému Azure a účtu Azure Media Services. Další informace najdete v tématu [Vytvoření účtu video indexer připojeného k Azure](connect-to-azure.md).
- Videosoubory se ukládají v Azure Storage pomocí Azure Media Services. Nejedná se o žádné časové omezení.
- Videosoubory a zvukové soubory můžete vždycky odstranit, stejně jako veškerá metadata a přehledy z nich extrahovat Video Indexer. Po odstranění souboru z Video Indexer se soubor a jeho metadata a přehledy trvale odeberou z Video Indexer. Pokud jste ale ve službě Azure Storage implementovali vlastní řešení zálohování, zůstane soubor ve službě Azure Storage.
- Průniku videa je identické, bez ohledu na to, jestli je nahrávání hotové, Video Indexer web nebo pomocí rozhraní API pro nahrání.
   
## <a name="upload-and-index-a-video-using-the-video-indexer-website"></a>Nahrávání a indexování videa pomocí Video Indexer webu

> [!NOTE]
> Název videa nesmí být delší než 80 znaků.

1. Přihlaste se k webu [Video Indexer](https://www.videoindexer.ai/).
1. Pokud chcete nahrát video na server, stiskněte tlačítko nebo odkaz **Upload** (Nahrát).

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/video-indexer-upload.png" alt-text="Nahrát":::
1. Když se vaše video nahraje na server, začne ho Video Indexer indexovat a analyzovat.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/progress.png" alt-text="Průběh nahrávání":::
1. Po dokončení analýzy Video Indexer obdržíte e-mail s odkazem na vaše video a stručný popis toho, co bylo ve videu nalezeno. Například: lidé, témata, OCR.

## <a name="upload-and-index-with-api"></a>Nahrání a indexování pomocí rozhraní API

Pomocí rozhraní API pro [nahrání videa](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) můžete nahrávat a indexovat videa na základě adresy URL. Následující ukázka kódu obsahuje komentovaný kód, který ukazuje, jak nahrát pole bajtů. 

### <a name="configurations-and-params"></a>Konfigurace a parametry

Tato část popisuje některé volitelné parametry a kdy je vhodné je nastavit. Nejaktuálnější informace o parametrech najdete v tématu [nahrání](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) rozhraní API pro video.

#### <a name="externalid"></a>externalID 

Tento parametr umožňuje určit ID, které bude s videem spojené. ID můžete použít na integraci externího systému pro správu video obsahu. Videa, které se nacházejí na portálu Video Indexer, je možné pomocí zadaného externího ID vyhledávat.

#### <a name="callbackurl"></a>callbackUrl

[!INCLUDE [callback url](./includes/callback-url.md)]

##### <a name="other-considerations"></a>Další důležité informace

- Video Indexer vrátí všechny existující parametry zadané v původní adrese URL.
- Zadaná adresa URL musí být kódovaná.

#### <a name="indexingpreset"></a>indexingPreset

Tento parametr použijte k definování svazku AI, který chcete použít na svém zvukovém nebo videosouborovém souboru. Tento parametr se používá ke konfiguraci indexovacího procesu. Můžete určit tyto hodnoty:

- `AudioOnly` – Indexujte a extrahujte přehledy jenom pomocí zvuku (ignoruje se video).
- `VideoOnly` – Indexujte a extrahujte přehledy jenom pomocí videa (ignoruje zvuk).
- `Default` – Indexujte a extrahujte přehledy pomocí zvukového souboru i videa.
- `DefaultWithNoiseReduction` – Indexujte a extrahujte přehledy ze zvukového i obrazového zařízení a při použití algoritmu redukce hluku ve zvukovém streamu.

    `DefaultWithNoiseReduction`Hodnota je nyní namapována na výchozí předvolbu (zastaralé).
- `BasicAudio` – Indexujte a extrahujte přehledy jenom pomocí zvuku (ignoruje video), včetně jenom základních funkcí zvuku (přepis, překlad, formátování titulků a titulků).
 - `AdvancedAudio` – Indexujte a extrahujte přehledy jenom pomocí zvuku (ignoruje video), včetně pokročilých zvukových funkcí (detekce zvukové události), a to spolu se standardní analýzou zvuku.

> [!NOTE]
> Video Indexer pokrývá až dvě stopy zvuku. Pokud je v souboru více zvukových stop, budou považovány za jednu stopu.<br/>
Pokud chcete tyto stopy indexovat samostatně, budete si muset extrahovat relevantní zvukový soubor a indexovat ho jako `AudioOnly` .

Cena závisí na vybrané možnosti indexování. Další informace najdete v tématu [Media Services ceny](https://azure.microsoft.com/pricing/details/media-services/).

#### <a name="priority"></a>upřednostněn

Videa se indexují Video Indexer podle jejich priority. Pro určení priority indexu použijte parametr **priority** . Platné jsou následující hodnoty: **Nízká**, **normální** (výchozí) a **Vysoká**.

Parametr **priority** se podporuje jenom pro placené účty.

#### <a name="streamingpreset"></a>streamingPreset

Po nahrání videa na server ho Video Indexer volitelně zakóduje. Pak bude pokračovat k indexování a analýze videa. Když Video Indexer dokončí analýzu, dostanete oznámení s ID videa.  

Při použití API [Upload video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) (Nahrát video) nebo [Re-Index Video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) (Znovu indexovat video) je jedním z volitelných parametrů `streamingPreset`. Pokud nastavíte `streamingPreset` na `Default`, `SingleBitrate` nebo `AdaptiveBitrate`, aktivuje se proces kódování. Po dokončení úloh indexování a kódování se video publikuje, takže ho můžete také streamovat. Koncový bod streamování, ze kterého chcete video streamovat, musí být ve stavu **Spuštěno**.

V případě SingleBitrate se náklady na kodér budou vztahovat na výstup. Pokud je výška videa větší nebo rovna 720, Video Indexer ji kódovat jako 1280 × 720. Jinak jako 640x468.
Výchozím nastavením je [kódování zohledňující obsah](../latest/encode-content-aware-concept.md).

Pokud chcete spustit úlohy indexování a kódování, [účet Azure Media Services připojený k vašemu účtu Video Indexer](connect-to-azure.md) vyžaduje rezervované jednotky. Další informace najdete v článku o [škálování zpracování médií](../previous/media-services-scale-media-processing-overview.md). Protože jsou tyto úlohy náročné na výpočetní prostředky, důrazně doporučujeme typ jednotky S3. Počet rezervovaných jednotek určuje maximální počet úloh, které můžou běžet paralelně. Základní doporučení je 10 rezervovaných jednotek S3. 

Pokud chcete video jenom indexovat, ale ne kódovat, nastavte `streamingPreset` na `NoStreaming`.

#### <a name="videourl"></a>videoUrl

Adresa URL video/zvukového souboru k indexování. Adresa URL musí odkazovat na soubor média (stránky HTML nejsou podporované). Soubor se dá chránit přístupovým tokenem poskytnutým jako součást identifikátoru URI a koncový bod poskytující soubor musí být zabezpečený pomocí protokolu TLS 1.2 nebo vyššího. Adresa URL musí být zakódovaná. 

Pokud parametr `videoUrl` není zadaný, Video Indexer očekává, že soubor předáte jako obsah těla multipart/form.

### <a name="code-sample"></a>Ukázka kódu

Následující fragment kódu v jazyce C# předvádí společné použití všech rozhraní API Video Indexeru.

**Pokyny pro spuštění následující ukázky kódu**

Po zkopírování tohoto kódu na vývojovou platformu budete muset zadat dva parametry: API Management ověřovací klíč a adresu URL videa.

* Klíč rozhraní API – klíč rozhraní API je klíč předplatného služby API Management, který vám umožní získat přístupový token, aby mohl provádět operace s účtem Video Indexer. 

    Pokud chcete získat klíč rozhraní API, Projděte si tento tok:

    * Přejděte na adresu https://api-portal.videoindexer.ai/.
    * Přihlásit
    * Přejít na   ->    ->  **předplatné autorizační autorizace** na produkty
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

### <a name="common-errors"></a>Běžné chyby

Operace Upload může vrátit kódy stavu uvedené v následující tabulce.

|Stavový kód|ErrorType (v textu odpovědi)|Description|
|---|---|---|
|409|VIDEO_INDEXING_IN_PROGRESS|V daném účtu už probíhá zpracování stejného videa.|
|400|VIDEO_ALREADY_FAILED|V daném účtu se méně než před 2 hodinami nepodařilo zpracovat stejné video. Klienti rozhraní API by měli před dalším nahráním videa vyčkat minimálně 2 hodiny.|
|429||Zkušební účty se povolují 5 nahrání za minutu. Placené účty jsou povolené 50 nahrávání za minutu.|

## <a name="uploading-considerations-and-limitations"></a>Důležité informace o nahrávání a omezení
 
- Název videa nesmí být delší než 80 znaků.
- Při nahrávání videa podle zadané adresy URL (upřednostňovaná možnost) musí být koncový bod zabezpečený pomocí protokolu TLS 1.2 (nebo novějšího).
- Velikost nahrávaného souboru u možnosti s využitím adresy URL je omezená na 30 GB.
- Délka adresy URL požadavku je omezená na 6 144 znaků a délka adresy URL řetězce dotazu je omezená na 4 096 znaků.
- Velikost nahrávaného souboru u možnosti s využitím pole bajtů je omezená na 2 GB.
- Časový limit u možnosti s využitím pole bajtů vyprší za 30 minut.
- Adresa URL poskytnutá `videoURL` parametrem musí být zakódovaná.
- Pro indexování aktiv služby Media Services platí stejná omezení jako pro indexování z adresy URL.
- Ve Video Indexeru platí pro jednotlivé soubory limit maximální doby trvání 4 hodiny.
- Adresa URL musí být přístupná (například se musí jednat o veřejnou adresu URL). 

    Pokud se jedná o privátní adresu URL, v požadavku je potřeba zadat přístupový token.
- Adresa URL musí odkazovat na platný mediální soubor, nikoli na webovou stránku, jako je například odkaz na `www.youtube.com` stránku.
- V placeném účtu můžete nahrát až 50 filmů za minutu a ve zkušebním účtu můžete nahrát až 5 filmů za minutu.

> [!Tip]
> Doporučujeme používat rozhraní .NET Framework verze 4.6.2 nebo novější, protože starší rozhraní .NET Framework nemají ve výchozím nastavení protokol TLS 1.2.
>
> Pokud potřebujete použít starší rozhraní .NET Framework, před voláním rozhraní REST API přidejte do kódu jeden řádek:  <br/> System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls12;

## <a name="next-steps"></a>Další kroky

[Projděte si výstup Azure Video Indexer vytvořený pomocí rozhraní API](video-indexer-output-json-v2.md)
