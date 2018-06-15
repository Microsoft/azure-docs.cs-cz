---
title: Použití Azure videa Indexer rozhraní API | Microsoft Docs
description: Tento článek ukazuje, jak začít používat rozhraní API Indexer Video.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 06/04/2018
ms.author: juliako
ms.openlocfilehash: d378934a0c085910475c366f4bdb538f09efc12b
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35346024"
---
# <a name="use-azure-video-indexer-api"></a>Použití Azure videa Indexer rozhraní API

Video Indexer slučuje různých technologií audio a video umělé intelligence (AI) společností Microsoft nabízeno ve integrované služby, vývoj jednodušší. Rozhraní API jsou navrženy pro povolení, které vývojářům zaměřit se na použití technologie AI média bez obav o škálování, globální dosáhnout, dostupnost a spolehlivost Cloudová platforma. Rozhraní API můžete použít k odeslání svých souborů, přehledné podrobné videa, získání adres URL widgetů přehledy, hráče a aby bylo možné vložit do vaší aplikace a další úkoly.

> [!Note]
> Denní limit nahrávání 100 souborů je bezplatná zkušební verze. Můžete zkontrolovat chybovou zprávu vrácenou podrobnosti. Všimněte si, že se může změnit denního limitu.

Tento článek ukazuje, jak mohou vývojáři využít výhod [Video Indexer API](https://api-portal.videoindexer.ai/). Podrobnější přehled služby Video Indexer najdete v tématu [přehled](video-indexer-overview.md) článku.

## <a name="subscribe-to-the-api"></a>Přihlášení k odběru do rozhraní API

1. Přihlásit se.

    Pokud chcete začít vyvíjet s Video Indexer, musíte první přihlásit k [Video Indexer](https://api-portal.videoindexer.ai/) portálu. 
    
    ![Registrace](./media/video-indexer-use-apis/video-indexer-api01.png)

    > [!Important]
    > 1. Je nutné použít stejný zprostředkovatel, který jste použili při registraci k Video Indexer.
    > 2. Než se můžete přihlásit uživatele Azure AD z domény, musí správce domény AAD povolit registraci této domény [zde](https://api-portal.videoindexer.ai/aadadminconsent).
    > 3. Osobní Google a Microsoft (outlook nebo live) účty lze použít pouze pro zkušební účty. Účty připojení k Azure vyžadují AAD.

2. Přihlášení k odběru.

    Vyberte [produkty](https://api-portal.videoindexer.ai/products) kartě. Pak vyberte autorizace a přihlášení k odběru. 
    
    ![Registrace](./media/video-indexer-use-apis/video-indexer-api02.png)
    
    Po přihlášení k odběru, nebudete moci zobrazit vaše předplatné a primární a sekundární klíče. Klíče by měly být chráněné. Klíče má být používána pouze kódu serveru. Jejich by neměl být k dispozici na straně klienta (.js, HTML atd.).

    ![Registrace](./media/video-indexer-use-apis/video-indexer-api03.png)

## <a name="obtain-access-token-using-the-authorization-api"></a>Získat přístupový token pomocí rozhraní API autorizace

Po přihlášení k odběru rozhraní API autorizace, nebudete moct získat přístupové tokeny. Tyto tokeny přístupu slouží k ověřování na základě operace rozhraní API. 

Každé volání rozhraní API operace by měly být přidružené přístupový token, odpovídající rozsah autorizace volání.

- Úroveň uživatele – tokeny přístupu na úrovni uživatele umožňují provádět v nich operace **uživatele** úroveň. Například získáte přidružené účty.
- Účet úroveň – účet úrovně přístupové tokeny umožňují provádět operace na **účet** úroveň nebo **video** úroveň. Například nahrát video, seznam všech videa, získat video Statistika atd.
- Video úroveň – video úrovně přístupové tokeny umožňují provádět operace na určitém **video**. Například přehledné videa, stáhnout titulků, získat pomůcky atd. 

Můžete určit, zda tyto tokeny jsou jen pro čtení nebo umožňují úpravy zadáním **allowEdit = true nebo false**.

Pro většinu scénářů serveru na server, budete pravděpodobně používat stejné **účet** token zahrnuje obě **účet** operace a **video** operace. Ale pokud máte v úmyslu provést na straně klienta volání Video Indexer (např. z JavaScriptu), je vhodnější použít **video** přístupový token, čímž zabráníte klientům v získání přístupu ke celý účet. Který je také důvodem, který po vložení kódu klienta VideoIndexer do vašeho klienta (například pomocí **získat přehledy pomůcky** nebo **získat pomůcky Player**) je nutné zadat **video**přístupový token.

Chcete-li zájmu usnadnění můžete použít **autorizace** rozhraní API > **GetAccounts** se získat token nejprve vaše účty bez získání uživatele. Můžete také požádat získat účty s platné tokeny, umožňuje přejděte další volání s cílem získat token účtu.

Přístupové tokeny vyprší po 1 hodina. Zajistěte, aby že váš přístupový token je platný před použitím rozhraní API operace. Pokud vyprší, volání rozhraní API autorizace znovu a získejte nový přístupový token.
 
Jste připravení začít s integrací s rozhraním API. Najít [podrobný popis každé rozhraní API REST Indexer Video](http://api-portal.videoindexer.ai/).

## <a name="location"></a>Umístění

Všechny operace rozhraní API vyžadovat umístění parametr, který označuje oblast, na který by měl směrovat volání a který byl vytvořen účet.

Použít hodnoty popsané v následující tabulce. **Param hodnota** při předáte hodnotu používá rozhraní API.

|**Název**|**Hodnota parametru**|**Popis**|
|---|---|---|
|Zkušební verze|Zkušební verze|Použít pro zkušebními účty.|
|Západní USA|westus2|Používá se pro oblast Azure západní USA 2.|
|Severní Evropa |northeurope|Používá se pro oblast Severní Evropa Azure.|
|Východní Asie|eastasia|Používá se pro oblast Azure východní Asie.|

## <a name="account-id"></a>ID účtu 

V všechny provozní volání rozhraní API je vyžadován parametr ID účtu. ID účtu je identifikátor GUID, který lze získat v jednom z následujících způsobů:

* Použijte Video Indexer portál k získání ID účtu:

    1. Přihlaste se k [videoindexer](https://www.videoindexer.ai/).
    2. Vyhledejte **nastavení** stránky.
    3. Zkopírujte ID účtu.

        ![ID účtu](./media/video-indexer-use-apis/account-id.png)

* Použít rozhraní API prostřednictvím kódu programu získat ID účtu.

    Použití [získat účty](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Accounts?) rozhraní API.
    
    > [!TIP]
    > Můžete vygenerovat tokeny přístupu pro účty tak, že definujete `generateAccessTokens=true`.
    
* Získání ID účtu z adresy URL stránky player ve vašem účtu.

    Když jste přehrát video, ID se zobrazí po `accounts` části a před `videos` oddílu.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>Doporučení

Tato část uvádí některá doporučení, jaké při použití Video Indexer API.

- Pokud máte v úmyslu nahrát video, doporučujeme umístit soubor do umístění některé veřejné síti (třeba OneDrive). Získejte odkaz na video a zadejte adresu URL jako param souboru nahrávání. 

    Adresy URL poskytnuté na Video Indexer musí odkazovat na soubor média (zvuk nebo video). Některé z odkazů generované OneDrive jsou pro stránku HTML, který obsahuje soubor. Snadno ověření pro adresu URL by se vložit do prohlížeče – Pokud se soubor spouští stahování, pravděpodobně je dobré adresa URL. Pokud v prohlížeči je vykreslování některé vizualizace, není pravděpodobně odkaz na soubor, ale stránku HTML.
    
- Při volání rozhraní API, který získá video statistiky pro zadaný video zobrazí podrobný výstup JSON jako obsahu odpovědi. [Zobrazit podrobnosti o vrácený JSON v tomto tématu](video-indexer-output-json.md).

## <a name="code-sample"></a>Ukázka kódu

Následující fragment kódu jazyka C# ukazuje použití všechny rozhraní API Indexer Video společně.

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

## <a name="next-steps"></a>Další postup

[Zkontrolujte podrobnosti o výstup JSON](video-indexer-output-json.md).

## <a name="see-also"></a>Další informace najdete v tématech

[Video s přehledem indexeru](video-indexer-overview.md)
