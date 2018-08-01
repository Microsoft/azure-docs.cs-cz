---
title: Použití Azure rozhraní Video Indexer API | Dokumentace Microsoftu
description: Tento článek ukazuje, jak začít používat rozhraní Video Indexer API.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 07/25/2018
ms.author: juliako
ms.openlocfilehash: 82416c7c653438fcd8b8f4a4ead7591bad0ac022
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2018
ms.locfileid: "39391516"
---
# <a name="use-azure-video-indexer-api"></a>Použití Azure rozhraní Video Indexer API

Video Indexer konsoliduje různých technologií audio a video umělé inteligence (AI) nabízené microsoftem v jedné integrované služby, což usnadňuje vývoj. Rozhraní API slouží k povolení, který vývojářům zaměřit se na používání technologie AI média bez starostí o škálování, globální přístup, dostupnost a spolehlivost cloudovou platformu. Rozhraní API můžete nahrát soubory, dostávat podrobné poznatky z videí, získání adres URL insight a player widgetů k vložení do vaší aplikace a další úlohy.

Při vytváření účtu Video Indexer, můžete vybrat účet bezplatné zkušební verze (ve kterém získáte určitý počet minut zdarma indexování) nebo placenou variantu (Pokud nejste omezení kvóty). S bezplatnou zkušební verzi Video Indexer poskytuje až 600 minut zdarma indexování uživatelům webu a až 2 400 minut zdarma indexování uživatelům rozhraní API. S placenou variantu, vytvoříte účet služby Video Indexer, který je [připojené k vašemu předplatnému Azure a účet služby Azure Media Services](connect-to-azure.md). Platíte za minut indexovat, stejně jako účet Media související poplatky. 

Tento článek popisuje, jak vývojáři mohou využít výhod [Video Indexer API](https://api-portal.videoindexer.ai/). Podrobnější přehled služby Video Indexer, najdete v tématu [přehled](video-indexer-overview.md) článku.

## <a name="subscribe-to-the-api"></a>Přihlaste se k rozhraní API odběru

1. Přihlásit se.

    Jak začít s vývojem pomocí funkce Video Indexer, musíte první přihlásit k [Video Indexer](https://api-portal.videoindexer.ai/) portálu. 
    
    ![Registrace](./media/video-indexer-use-apis/video-indexer-api01.png)

    > [!Important]
    > * Je nutné použít stejný zprostředkovatel, který jste použili při registraci pro Video Indexer.
    > * Osobní Google a Microsoft (outlook nebo živé) účty jde použít jenom pro účty. Účty připojené k Azure vyžadují služby Azure AD.
    > * Může existovat pouze jeden aktivní účet na e-mailu. Pokud se uživatel pokusí přihlásit pomocí user@gmail.com pro LinkedIn a potom s user@gmail.com pro Google pozdější zobrazí chybovou stránku, informacemi o tom uživatel již existuje.


2. Přihlásit k odběru.

    Vyberte [produkty](https://api-portal.videoindexer.ai/products) kartu. Potom vyberte autorizace a odběru. 
    
    ![Registrace](./media/video-indexer-use-apis/video-indexer-api02.png)

    > [!NOTE]
    > Noví uživatelé automaticky přihlášeni k autorizaci.
    
    Po přihlášení k odběru, budete moct zobrazit vaše předplatné a primárním a sekundárním klíčem. Klíče by měly být chráněné. Klíče by měly být používány pouze váš kód serveru. Jejich by neměl být k dispozici na na straně klienta (.js, HTML atd.).

    ![Registrace](./media/video-indexer-use-apis/video-indexer-api03.png)

## <a name="obtain-access-token-using-the-authorization-api"></a>Získání přístupového tokenu pomocí rozhraní API pro autorizaci

Jakmile odebíráte autorizace rozhraní API, bude moct získat přístupové tokeny. Tyto tokeny přístupu se používají k ověřování s využitím rozhraní API operace. 

Každé volání rozhraní API operace by měly být přidruženy s přístupovým tokenem, odpovídající rozsah autorizace volání.

- Uživatelů level – tokeny uživatele úroveň přístupu vám umožní provádět operace na **uživatele** úroveň. Třeba získáte přidružené účty.
- Úroveň účtu – tokenů úroveň přístupu účtu vám umožní provádět operace na **účet** úroveň nebo **videa** úroveň. Například nahrání videa, vypsat všechna videa, můžete získat nové poznatky z videí, atd.
- Video úroveň – tokeny přístupu na úrovni videa umožňují provádět operace s konkrétní **videa**. Třeba získat nové poznatky z videí, stáhněte si titulky a získejte widgetů, atd. 

Můžete řídit, jestli tyto tokeny jsou jen pro čtení nebo povolit úpravy zadáním **allowEdit = true/false**.

Pro většinu scénářů na serveru, budete pravděpodobně používat stejné **účet** token zahrnuje obě **účet** operací a **videa** operace. Ale pokud máte v úmyslu provést na straně klienta volání do modulu Video Indexer (například z JavaScriptu), je vhodné používat **videa** přístupový token, čímž zabráníte klientům přístup k na celý účet. Který je také důvodem, že vložení VideoIndexer klientský kód do vašeho klienta (například pomocí **získat přehledy widgetu** nebo **získat widgetu Player**) je nutné zadat **video**přístupový token.

Zájmu usnadnění můžete použít **autorizace** rozhraní API > **GetAccounts** k získání tokenu nejprve vašich účtů bez získání uživatele. Můžete také požádat účet s platným tokeny, můžete přeskočit další volání pro získání tokenu účtu.

Přístupové tokeny vyprší za 1 hodinu. Ujistěte se, že váš přístupový token je platný před použitím operace rozhraní API. Pokud vyprší platnost, volání rozhraní API autorizace znovu k získání nového tokenu přístupu.
 
Jste připraveni pustit do integrace s rozhraním API. Najít [podrobný popis každé Video Indexer REST API](http://api-portal.videoindexer.ai/).

## <a name="location"></a>Umístění

Všechny operace rozhraní API vyžaduje parametr umístění, která označuje oblast, na který by měl směrovat volání a který účet vytvořil.

Použít s hodnotami popsanými v následující tabulce. **Param value** je hodnota, pokud předáte pomocí rozhraní API.

|**Název**|**Hodnota parametru**|**Popis**|
|---|---|---|
|Zkušební verze|Zkušební verze|Používá pro účty.|
|USA – západ|westus2|Používá se pro oblasti Azure USA – západ 2.|
|Severní Evropa |northeurope|Používá se pro oblast Evropa – sever Azure.|
|Východní Asie|eastasia|Používá se pro oblast Azure východní Asie.|

## <a name="account-id"></a>ID účtu 

Parametr ID účtu se vyžaduje u všech provozních volání rozhraní API. ID účtu je identifikátor GUID, který lze získat v jednom z následujících způsobů:

* Použití portálu pro Video Indexer k získání ID účtu:

    1. Přihlaste se k [videoindexer](https://www.videoindexer.ai/).
    2. Přejděte **nastavení** stránky.
    3. Zkopírujte ID účtu.

        ![ID účtu](./media/video-indexer-use-apis/account-id.png)

* Použití rozhraní API prostřednictvím kódu programu získat ID účtu.

    Použití [získat účty](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Accounts?) rozhraní API.
    
    > [!TIP]
    > Můžete generovat přístupové tokeny pro účty tak, že definujete `generateAccessTokens=true`.
    
* Získejte ID účtu z adresy URL pro přehrávač stránky ve vašem účtu.

    Při sledování videa ID se zobrazí po `accounts` oddílu a před `videos` oddílu.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>Doporučení

Tato část uvádí několik doporučení, při použití rozhraní Video Indexer API.

- Pokud se chystáte nahrát video, doporučujeme umístit soubor do umístění veřejné síti (třeba Onedrivu). Získejte odkaz na video a zadejte adresu URL jako param nahrávání souboru. 

    Adresy URL poskytnuté do modulu Video Indexer musí odkazovat na soubor média (zvuku nebo videa). Některé z odkazů generovaných OneDrive jsou určené pro stránku HTML, který obsahuje soubor. Jednoduché ověřování pro adresu URL může být vložením do prohlížeče – Pokud se tento soubor spustí stahování, je pravděpodobně dobrý adresy URL. Pokud v prohlížeči je vykreslování některé vizualizace, není pravděpodobně odkaz na soubor, ale stránku HTML.
    
- Při volání rozhraní API, která získá nové poznatky z videí pro zadaný videu získáte podrobný výstup JSON jako obsah odpovědi. [Zobrazit podrobnosti o vrácený JSON v tomto tématu](video-indexer-output-json.md).

## <a name="code-sample"></a>Ukázka kódu

Následující fragment kódu jazyka C# ukazuje využití všechna rozhraní Video Indexer API společně.

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

[Zkontrolujte podrobnosti ve výstupu JSON](video-indexer-output-json.md).

## <a name="see-also"></a>Další informace najdete v tématech

[Video Indexer s přehledem](video-indexer-overview.md)
