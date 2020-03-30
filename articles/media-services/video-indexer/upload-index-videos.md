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
ms.date: 02/18/2020
ms.author: juliako
ms.openlocfilehash: 245eabdf4d77682c87062c2581239a554112d748
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77468758"
---
# <a name="upload-and-index-your-videos"></a>Nahrání videí na server a jejich indexování  

Při nahrávání videí pomocí rozhraní API pro indexer videa máte následující možnosti nahrávání: 

* Nahrání videa na server z adresy URL (upřednostňovaná možnost)
* odeslat video soubor jako bajtové pole v těle požadavku,
* Stávající prostředek Azure Media Services použijte poskytnutím [ID prostředku](https://docs.microsoft.com/azure/media-services/latest/assets-concept) (podporovaného jenom na placených účtech).

Po nahrání videa video indexer (volitelně) video zakóduje (popisem článku). Při vytváření účtu Video Indexeru můžete zvolit účet bezplatné zkušební verze (ve kterém získáte určitý počet minut indexování zdarma) nebo placenou variantu (ve které nejste omezení kvótou). V bezplatné zkušební verzi Video Indexer poskytuje až 600 minut bezplatného indexování pro uživatele webu a až 2400 minut bezplatného indexování pro uživatele rozhraní API. S placenou možností si vytvoříte účet Video Indexer, který je [připojený k vašemu předplatnému Azure a účtu Azure Media Services](connect-to-azure.md). Platíte za indexované minuty a také poplatky související s účtem Media. 

Článek ukazuje, jak nahrát a indexovat videa pomocí těchto možností:

* [Web videoindexeru](#website) 
* [Api video indexeru](#apis)

## <a name="uploading-considerations-and-limitations"></a>Nahrání aspekty a omezení
 
- Název videa nesmí být větší než 80 znaků.
- Při nahrávání videa na základě adresy URL (preferované) musí být koncový bod zabezpečen pomocí TLS 1.2 (nebo vyšší).
- Velikost nahrávání s možností URL je omezena na 30 GB.
- Délka adresy URL požadavku je omezena na 6144 znaků, kde je délka adresy URL řetězce dotazu omezena na 4096 znaků .
- Velikost nahrávání s možností bajtového pole je omezena na 2 GB.
- Možnost bajtového pole vyčnívá po 30 minutách.
- Adresa URL uvedená `videoURL` v param musí být zakódována.
- Indexování prostředků Media Services má stejné omezení jako indexování z adresy URL.
- Video Indexer má maximální dobu trvání limit 4 hodiny pro jeden soubor.
- Adresa URL musí být přístupná (například veřejná adresa URL). 

    Pokud se jedná o privátní adresu URL, musí být v požadavku uveden přístupový token.
- Adresa URL musí odkazovat na platný mediální soubor a nikoli na `www.youtube.com` webovou stránku, například na odkaz na stránku.
- Na placeném účtu můžete nahrát až 50 filmů za minutu a ve zkušebním účtu až 5 filmů za minutu.

> [!Tip]
> Doporučujeme používat rozhraní .NET Framework verze 4.6.2 nebo novější, protože starší rozhraní .NET Framework nemají ve výchozím nastavení protokol TLS 1.2.
>
> Pokud potřebujete použít starší rozhraní .NET Framework, před voláním rozhraní REST API přidejte do kódu jeden řádek:  <br/> System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls12;

## <a name="supported-file-formats-for-video-indexer"></a>Podporované formáty souborů pro video indexer

Seznam formátů souborů, které lze použít s indexerem videa, naleznete v článku [o formátech vstupních kontejnerů nebo souborů.](../latest/media-encoder-standard-formats.md#input-containerfile-formats)

## <a name="upload-and-index-a-video-using-the-video-indexer-website"></a><a id="website"/>Nahrání a indexování videa pomocí webu Video Indexer

> [!NOTE]
> Název videa nesmí být větší než 80 znaků.

1. Přihlaste se k webu [Video Indexer](https://www.videoindexer.ai/).
2. Pokud chcete nahrát video na server, stiskněte tlačítko nebo odkaz **Upload** (Nahrát).

    ![Odeslat](./media/video-indexer-get-started/video-indexer-upload.png)

    Když se vaše video nahraje na server, začne ho Video Indexer indexovat a analyzovat.

    ![Nahráno](./media/video-indexer-get-started/video-indexer-uploaded.png) 

    Když Video Indexer dokončí analýzu, zobrazí se oznámení s odkazem na vaše video a krátkým popisem, co se ve videu našlo. Například: lidé, témata, OCR.

## <a name="upload-and-index-with-api"></a><a id="apis"/>Nahrávání a indexování pomocí rozhraní API

Pomocí rozhraní [API pro nahrávání videa](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) nahrajte a indexujte videa na základě adresy URL. Ukázka kódu, která následuje, obsahuje kód komentovaný, který ukazuje, jak nahrát bajtové pole. 

### <a name="configurations-and-params"></a>Konfigurace a parametry

Tato část popisuje některé volitelné parametry a kdy je vhodné je nastavit.

#### <a name="externalid"></a>externalID 

Tento parametr umožňuje určit ID, které bude s videem spojené. ID můžete použít na integraci externího systému pro správu video obsahu. Videa, které se nacházejí na portálu Video Indexer, je možné pomocí zadaného externího ID vyhledávat.

#### <a name="callbackurl"></a>callbackUrl

Adresa URL, která se používá k upozornění zákazníka (pomocí požadavku POST) na následující události:

- Změna stavu indexování: 
    - Vlastnosti:    
    
        |Name (Název)|Popis|
        |---|---|
        |id|ID videa|
        |state|Stav videa|  
    - Příklad: https:\//test.com/notifyme?projectName=MyProject&id=1234abcd&state=Zpracováno
- Osoba identifikovaná ve videu:
  - Vlastnosti
    
      |Name (Název)|Popis|
      |---|---|
      |id| ID videa|
      |faceId|ID obličeje, které se zobrazí v indexu videa|
      |knownPersonId|ID osoby, které je jedinečné v modelu obličeje|
      |personJméno|Jméno osoby|
        
    - Příklad: https:\//test.com/notifyme?projectName=MyProject&id=1234abcd&faceid=12&známýPersonId=CCA84350-89B7-4262-861C-3CAC796542A5&personName=Inigo_Montoya 

##### <a name="notes"></a>Poznámky

- Video Indexer vrátí všechny existující parametry uvedené v původní adrese URL.
- Zadaná adresa URL musí být zakódována.

#### <a name="indexingpreset"></a>indexingPreset

Tento parametr použijte, pokud nezpracované nebo externí záznamy obsahují šum na pozadí. Tento parametr se používá ke konfiguraci indexovacího procesu. Můžete určit tyto hodnoty:

- `AudioOnly` – indexovat a extrahovat přehledy s využitím jenom zvuku (video se ignoruje)
- `VideoOnly`- Index a extrahovat poznatky pomocí videa pouze (ignorování zvuku)
- `Default` – indexovat a extrahovat přehledy s využitím zvuku i videa
- `DefaultWithNoiseReduction` – indexovat a extrahovat přehledy ze zvuku i videa při použití algoritmů snížení šumu na zvukový datový proud

> [!NOTE]
> Video Indexer zakrývá až dvě stopy zvuku. Pokud je v souboru více zvukových stop, budou považovány za jednu stopu.<br/>
Pokud chcete indexovat stopy samostatně, budete muset extrahovat příslušný `AudioOnly`zvukový soubor a indexovat jej jako .

Cena závisí na vybrané možnosti indexování.  

#### <a name="priority"></a>Prioritou

Videa jsou indexována video indexerem podle jejich priority. K určení priority indexu použijte parametr **priority.** Následující hodnoty jsou platné: **Nízká**, **Normální** (výchozí) a **Vysoká**.

**Parametr priority** je podporován pouze pro placené účty.

#### <a name="streamingpreset"></a>streamingPreset

Po nahrání videa na server ho Video Indexer volitelně zakóduje. Pak bude pokračovat k indexování a analýze videa. Když Video Indexer dokončí analýzu, dostanete oznámení s ID videa.  

Při použití API [Upload video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) (Nahrát video) nebo [Re-Index Video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) (Znovu indexovat video) je jedním z volitelných parametrů `streamingPreset`. Pokud nastavíte `streamingPreset` na `Default`, `SingleBitrate` nebo `AdaptiveBitrate`, aktivuje se proces kódování. Po dokončení úloh indexování a kódování se video publikuje, takže ho můžete také streamovat. Koncový bod streamování, ze kterého chcete video streamovat, musí být ve stavu **Spuštěno**.

Pokud chcete spustit úlohy indexování a kódování, [účet Azure Media Services připojený k vašemu účtu Video Indexer](connect-to-azure.md) vyžaduje rezervované jednotky. Další informace najdete v článku o [škálování zpracování médií](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview). Protože jsou tyto úlohy náročné na výpočetní prostředky, důrazně doporučujeme typ jednotky S3. Počet rezervovaných jednotek určuje maximální počet úloh, které můžou běžet paralelně. Základní doporučení je 10 rezervovaných jednotek S3. 

Pokud chcete video jenom indexovat, ale ne kódovat, nastavte `streamingPreset` na `NoStreaming`.

#### <a name="videourl"></a>videoUrl

Adresa URL video/zvukového souboru k indexování. Adresa URL musí odkazovat na soubor média (stránky HTML nejsou podporované). Soubor se dá chránit přístupovým tokenem poskytnutým jako součást identifikátoru URI a koncový bod poskytující soubor musí být zabezpečený pomocí protokolu TLS 1.2 nebo vyššího. Adresa URL musí být zakódovaná. 

Pokud parametr `videoUrl` není zadaný, Video Indexer očekává, že soubor předáte jako obsah těla multipart/form.

### <a name="code-sample"></a>Ukázka kódu

Následující fragment kódu v jazyce C# předvádí společné použití všech rozhraní API Video Indexeru.

#### <a name="instructions-for-running-this-code-sample"></a>Pokyny pro spuštění této ukázky kódu

Po zkopírování tohoto kódu do vývojové platformy budete muset zadat dva parametry: ověřovací klíč api management a adresu URL videa.

* Klíč rozhraní API – klíč rozhraní API je váš osobní klíč předplatného pro správu rozhraní API, který vám umožní získat přístupový token za účelem provádění operací s účtem Video Indexer. 

    Chcete-li získat klíč rozhraní API, projděte si tento tok:

    * Přejít nahttps://api-portal.videoindexer.ai/
    * Přihlásit
    * Přejít **Products** -> na předplatné**autorizace autorizace** -> **Authorization subscription** produktů
    * Kopírování **primárního klíče**
* Adresa URL videa – Adresa URL video/audio souboru, který má být indexován. Adresa URL musí odkazovat na soubor média (stránky HTML nejsou podporované). Soubor se dá chránit přístupovým tokenem poskytnutým jako součást identifikátoru URI a koncový bod poskytující soubor musí být zabezpečený pomocí protokolu TLS 1.2 nebo vyššího. Adresa URL musí být zakódovaná.

Výsledek úspěšného spuštění ukázky kódu bude obsahovat adresu URL widgetu přehledu a adresu URL widgetu přehrávače, která vám umožní prozkoumat poznatky a nahrané video. 


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

|Kód stavu|ErrorType (v textu odpovědi)|Popis|
|---|---|---|
|409|VIDEO_INDEXING_IN_PROGRESS|V daném účtu už probíhá zpracování stejného videa.|
|400|VIDEO_ALREADY_FAILED|V daném účtu se méně než před 2 hodinami nepodařilo zpracovat stejné video. Klienti rozhraní API by měli před dalším nahráním videa vyčkat minimálně 2 hodiny.|
|429||Zkušební účty jsou povoleny 5 nahrávání za minutu. Placené účty jsou povoleny 50 nahrávání za minutu.|

## <a name="next-steps"></a>Další kroky

[Prozkoumejte výstup Azure Video Indexer vytvořený rozhraním API](video-indexer-output-json-v2.md)
