---
title: 'Kurz: Používání rozhraní API Video Indexeru'
titlesuffix: Azure Cognitive Services
description: Tento článek ukazuje, jak začít používat rozhraní API Video Indexeru.
services: cognitive services
author: juliako
manager: cgronlun
ms.service: cognitive-services
ms.component: video-indexer
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: juliako
ms.openlocfilehash: 7bd7ed1b2d2f437ef57598c42ca12ce8bfb174a1
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "45985561"
---
# <a name="tutorial-use-the-video-indexer-api"></a>Kurz: Používání rozhraní API Video Indexeru

> [!Note]
> API Video Indexeru ve verzi 1 se přestalo používat 1. srpna 2018. Teď byste měli používat rozhraní API Video Indexeru ve verzi 2. <br/>Pokyny k vývoji pomocí rozhraní API Video Indexeru ve verzi 2 najdete [tady](https://api-portal.videoindexer.ai/). 

Video Indexer slučuje různé technologie umělé inteligence (AI) pro zvuk a video nabízené Microsoftem do jedné integrované služby a tím usnadňuje vývoj. Rozhraní API umožňují vývojářům zaměřit se na používání technologií AI pro média, aniž by se museli starat o škálování, globální dosah, dostupnost a spolehlivost cloudové platformy. Rozhraní API můžete využívat k nahrávání souborů, získání podrobných přehledů z videí, získání adres URL widgetů přehledů a přehrávače k vložení do vaší aplikace i k dalším úlohám.

Při vytváření účtu Video Indexeru můžete zvolit účet bezplatné zkušební verze (ve kterém získáte určitý počet minut indexování zdarma) nebo placenou variantu (ve které nejste omezení kvótou). V bezplatné zkušební verzi Video Indexer poskytuje až 600 minut bezplatného indexování pro uživatele webu a až 2400 minut bezplatného indexování pro uživatele rozhraní API. S placenou variantou vytvoříte účet Video Indexeru, který je [spojený s vaším předplatným Azure a účtem Azure Media Services](connect-to-azure.md). Platíte za indexované minuty a také poplatky související s účtem Azure Media Services. 

Tento článek popisuje, jak můžou vývojáři využít výhod rozhraní [API Video Indexeru](https://api-portal.videoindexer.ai/).

## <a name="subscribe-to-the-api"></a>Přihlášení k odběru rozhraní API

1. Přihlaste se k portálu [Video Indexer Developer Portal](https://api-portal.videoindexer.ai/).
    
    ![Přihlášení](./media/video-indexer-use-apis/video-indexer-api01.png)

    > [!Important]
    > * Musíte použít stejného zprostředkovatele, jakého jste použili při registraci k Video Indexeru.
    > * Osobní účty Google a Microsoft (Outlook nebo Live) jde použít jenom pro zkušební verze účtu. Účty připojené k Azure vyžadují Azure AD.
    > * Pro každý e-mail může existovat jenom jeden aktivní účet. Pokud se uživatel pokusí přihlásit pomocí adresy user@gmail.com pro LinkedIn a pak pomocí adresy user@gmail.com pro Google, ve druhém případě se zobrazí chybová stránka s informací, že uživatel už existuje.

2. Přihlaste se k odběru.

    Vyberte kartu [Products](https://api-portal.videoindexer.ai/products) (Produkty). Pak vyberte Authorization (Autorizace) a Subscribe (Přihlásit se k odběru). 
    
    ![Registrace](./media/video-indexer-use-apis/video-indexer-api02.png)

    > [!NOTE]
    > Noví uživatelé jsou k odběru API Authorization automaticky přihlášení.
    
    Po přihlášení k odběru budete moct zobrazit svůj odběr a primární a sekundární klíč. Klíče by měly být chráněné. Klíče by měl používat jenom kód vašeho serveru. Neměly by být k dispozici na straně klienta (.js, html apod.).

    ![Registrace](./media/video-indexer-use-apis/video-indexer-api03.png)

## <a name="obtain-access-token-using-the-authorization-api"></a>Získání přístupového tokenu pomocí rozhraní API Authorization

Po přihlášení k odběru API Authorization budete moct získat přístupové tokeny. Tyto přístupové tokeny se používají k ověřování v API Operations. 

Každé volání do API Operations by mělo být přidružené k přístupovému tokenu s odpovídajícím oborem autorizace volání.

- Úroveň uživatele – přístupové tokeny na úrovni uživatele vám umožní provádět operace na úrovni **uživatele**. Třeba získat přidružené účty.
- Úroveň účtu – přístupové tokeny na úrovni účtu vám umožní provádět operace na úrovni **účtu** nebo na úrovni **videa**. Například nahrát video, vypsat všechna videa, získat přehledy z videa apod.
- Úroveň videa – přístupové tokeny na úrovni videa vám umožní provádět operace s konkrétním **videem**. Třeba získat nové přehledy z videa, stáhnout titulky, získat widgety apod. 

Zadáním **allowEdit=true/false** můžete řídit, jestli jsou tyto tokeny jen pro čtení, nebo povolují úpravy.

Pro většinu scénářů mezi servery budete pravděpodobně používat stejný token **účtu**, protože zahrnuje operace **účtu** i **videa**. Pokud ale máte v úmyslu provádět volání do Video Indexeru na straně klienta (třeba z JavaScriptu), je vhodné používat přístupový token **videa**, abyste klientům zabránili v přístupu k celému účtu. To je také důvodem, proč při vkládání klientského kódu Video Indexeru do vašeho klienta (třeba pomocí API **Get Insights Widget** nebo **Get Player Widget**) musíte poskytnout přístupový token **videa**.

Kvůli usnadnění můžete pomocí **Autorization** API > **GetAccounts** získat účty bez předchozího získání tokenu uživatele. Můžete také požádat o získání účtů s platnými tokeny. To vám umožní přeskočit další volání pro získání tokenu účtu.

Platnost přístupových tokenů vyprší za 1 hodinu. Před použitím API Operations se ujistěte, že je váš přístupový token platný. Pokud platnost vyprší, znovu volejte API Authorization, abyste získali nový přístupový token.
 
Jste připraveni pustit se do integrace pomocí rozhraní API. Najděte si [podrobný popis každého REST API Video Indexeru](http://api-portal.videoindexer.ai/).

## <a name="location"></a>Umístění

Všechna API operací vyžadují parametr Location (Poloha), který označuje oblast, do které by se mělo volání směrovat a ve kterém byl vytvořen účet.

Používají se hodnoty popsané v následující tabulce. **Hodnota parametru** je hodnota, kterou předáte při použití rozhraní API.

|**Název**|**Hodnota parametru**|**Popis**|
|---|---|---|
|Zkušební verze|trial|Používá se pro zkušební účty.|
|USA – západ|westus2|Používá se pro oblasti Azure USA – západ 2.|
|Severní Evropa |northeurope|Používá se pro oblasti Azure Severní Evropa.|
|Východní Asie|eastasia|Používá se pro oblasti Azure Východní Asie.|

## <a name="account-id"></a>Account ID 

Parametr Account ID (ID účtu) se vyžaduje u všech volání API operací. ID účtu je globálně jedinečný identifikátor, který se dá získat jedním z těchto způsobů:

* K získání ID účtu použijte **web Video Indexer**:

    1. Přejděte na web [Video Indexer](https://www.videoindexer.ai/) a přihlaste se.
    2. Přejděte na stránku **Settings** (Nastavení).
    3. Zkopírujte ID účtu.

        ![Account ID](./media/video-indexer-use-apis/account-id.png)

* K získání ID účtu prostřednictvím programu použijte **Video Indexer Developer Portal**.

    Použijte API [Get Accounts](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Accounts?) (Získat účty).
    
    > [!TIP]
    > Přístupové tokeny pro účty můžete vygenerovat tak, že definujete `generateAccessTokens=true`.
    
* Získejte ID účtu z adresy URL stránky přehrávače ve vašem účtu.

    Při sledování videa se ID zobrazuje za částí `accounts` a před částí `videos`.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>Doporučení

Tato část uvádí několik doporučení pro používání rozhraní API Video Indexeru.

- Pokud se chystáte nahrát video na server, doporučujeme umístit soubor do veřejného síťového umístění (jako je OneDrive). Získejte odkaz na video a zadejte adresu URL jako parametr souboru k nahrání. 

    Adresa URL poskytnutá Video Indexeru musí odkazovat na soubor média (zvukový nebo video). Některé z odkazů vygenerovaných OneDrivem vedou na pro stránku HTML, která soubor obsahuje. Adresu URL můžete jednoduše ověřit vložením do prohlížeče – pokud se soubor začne stahovat, je adresa URL pravděpodobně správná. Pokud prohlížeč zobrazuje vizuální obsah, pravděpodobně se nejedná o odkaz na soubor, ale stránku HTML.
    
- Při volání rozhraní API, které získává přehledy ze zadaného videa získáte jako obsah odpovědi podrobný výstup JSON. [Podrobnosti o vráceném JSON najdete v tomto tématu](video-indexer-output-json.md).

## <a name="code-sample"></a>Ukázka kódu

Následující fragment kódu v jazyce C# předvádí společné použití všech rozhraní API Video Indexeru.

```csharp
var apiUrl = "https://api.videoindexer.ai";
var accountId = "..."; 
var location = "westus2";
var apiKey = "..."; 

System.Net.ServicePointManager.SecurityProtocol = System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

// create the http client
var handler = new HttpClientHandler(); 
handler.AllowAutoRedirect = false; 
var client = new HttpClient(handler);
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey); 

// obtain account access token
var accountAccessTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/AccessToken?allowEdit=true").Result;
var accountAccessToken = accountAccessTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// upload a video
var content = new MultipartFormDataContent();
Debug.WriteLine("Uploading...");
// get the video from URL
var videoUrl = "VIDEO_URL"; // replace with the video URL

// as an alternative to specifying video URL, you can upload a file.
// remove the videoUrl parameter from the query string below and add the following lines:
  //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
  //byte[] buffer =newbyte[video.Length];
  //video.Read(buffer, 0, buffer.Length);
  //content.Add(newByteArrayContent(buffer));

var uploadRequestResult = client.PostAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos?accessToken={accountAccessToken}&name=some_name&description=some_description&privacy=private&partition=some_partition&videoUrl={videoUrl}", content).Result;
var uploadResult = uploadRequestResult.Content.ReadAsStringAsync().Result;

// get the video id from the upload result
var videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
Debug.WriteLine("Uploaded");
Debug.WriteLine("Video ID: " + videoId);           

// obtain video access token            
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
var videoTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/Videos/{videoId}/AccessToken?allowEdit=true").Result;
var videoAccessToken = videoTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// wait for the video index to finish
while (true)
{
  Thread.Sleep(10000);

  var videoGetIndexRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/Index?accessToken={videoAccessToken}&language=English").Result;
  var videoGetIndexResult = videoGetIndexRequestResult.Content.ReadAsStringAsync().Result;

  var processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

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
var searchRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/Search?accessToken={accountAccessToken}&id={videoId}").Result;
var searchResult = searchRequestResult.Content.ReadAsStringAsync().Result;
Debug.WriteLine("");
Debug.WriteLine("Search:");
Debug.WriteLine(searchResult);

// get insights widget url
var insightsWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/InsightsWidget?accessToken={videoAccessToken}&widgetType=Keywords&allowEdit=true").Result;
var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
Debug.WriteLine("Insights Widget url:");
Debug.WriteLine(insightsWidgetLink);

// get player widget url
var playerWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/PlayerWidget?accessToken={videoAccessToken}").Result;
var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
Debug.WriteLine("");
Debug.WriteLine("Player Widget url:");
Debug.WriteLine(playerWidgetLink);

```

## <a name="next-steps"></a>Další kroky

[Prozkoumejte podrobnosti výstupního JSON](video-indexer-output-json.md).

[Přehled Video Indexeru](video-indexer-overview.md)
