---
title: Použití rozhraní API Video Indexeru
titleSuffix: Azure Media Services
description: Tento článek popisuje, jak začít s rozhraním API Azure Media Services Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/13/2020
ms.author: juliako
ms.openlocfilehash: 82bdb177cf4d9c400d1b13ba7178658089950557
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314339"
---
# <a name="tutorial-use-the-video-indexer-api"></a>Kurz: Používání rozhraní API Video Indexeru

Video Indexer konsoliduje různé technologie umělé inteligence zvuku a videa (AI), které společnost Microsoft nabízí, do jedné integrované služby, což usnadňuje vývoj. Rozhraní API jsou navržena tak, aby vývojářům umožnila soustředit se na využívání technologií Media AI bez obav z rozsahu, globálního dosahu, dostupnosti a spolehlivosti cloudových platforem. Pomocí rozhraní API můžete nahrát soubory, získat podrobné přehledy videa, získat adresy URL vložitelných přehledů a widgetů přehrávače a další.

Při vytváření účtu Video Indexer si můžete vybrat bezplatný zkušební účet (kde získáte určitý počet volných minut indexování) nebo placenou možnost (kde nejste omezeni kvótou). S bezplatnou zkušební verzi, Video Indexer poskytuje až 600 minut zdarma indexování pro uživatele webových stránek a až 2400 minut zdarma indexování pro uživatele ROZHRANÍ API. S placenou možností si vytvoříte účet Video Indexer, který je [připojený k vašemu předplatnému Azure a účtu Azure Media Services](connect-to-azure.md). Platíte za indexované minuty a také poplatky související s účtem Azure Media Services.

Tento článek popisuje, jak můžou vývojáři využít výhod rozhraní [API Video Indexeru](https://api-portal.videoindexer.ai/).

## <a name="subscribe-to-the-api"></a>Přihlášení k odběru rozhraní API

1. Přihlaste se k portálu [Video Indexer Developer Portal](https://api-portal.videoindexer.ai/).
    
    ![Přihlášení k portálu pro vývojáře videoindexeru](./media/video-indexer-use-apis/video-indexer-api01.png)

   > [!Important]
   > * Musíte použít stejného zprostředkovatele, jakého jste použili při registraci k Video Indexeru.
   > * Osobní účty Google a Microsoft (Outlook/Live) lze použít pouze pro zkušební účty. Účty připojené k Azure vyžadují Azure AD.
   > * Na jeden e-mail může být pouze jeden aktivní účet. Pokud se uživatel pokusí user@gmail.com přihlásit pro LinkedIn a později pro user@gmail.com Google, ten se zobrazí chybová stránka s tím, že uživatel již existuje.

2. Přihlaste se k odběru.

    Vyberte kartu [Produkty.](https://api-portal.videoindexer.ai/products) Potom vyberte Autorizace a přihlaste se k odběru.
    
    ![Karta Produkty na portálu pro vývojáře videoindexeru](./media/video-indexer-use-apis/video-indexer-api02.png)

    > [!NOTE]
    > Noví uživatelé jsou k odběru API Authorization automaticky přihlášení.
    
    Jakmile se přihlásíte k odběru, uvidíte své předplatné a primární a sekundární klíče. Klíče by měly být chráněné. Klíče by měl používat jenom kód vašeho serveru. Neměly by být k dispozici na straně klienta (.js, .html a tak dále).

    ![Předplatné a klíče na portálu pro vývojáře videoindexeru](./media/video-indexer-use-apis/video-indexer-api03.png)

> [!TIP]
> Uživatelé Video Indexeru se můžou pomocí jednoho klíče předplatného připojit k několika účtům Video Indexeru. Tyto účty Video Indexeru můžete propojit s různými účty Media Services.

## <a name="obtain-access-token-using-the-authorization-api"></a>Získání přístupového tokenu pomocí rozhraní API Authorization

Jakmile se přihlásíte k odběru autorizačního rozhraní API, můžete získat přístupové tokeny. Tyto přístupové tokeny se používají k ověřování v API Operations.

Každé volání do API Operations by mělo být přidružené k přístupovému tokenu s odpovídajícím oborem autorizace volání.

- Úroveň uživatele: Přístupové tokeny na úrovni uživatele umožňují provádět operace na **úrovni uživatele.** Třeba získat přidružené účty.
- Úroveň účtu: Přístupové tokeny na úrovni účtu umožňují provádět operace na úrovni **účtu** nebo na úrovni **videa.** Například nahrajte video, uveďte všechna videa, získejte přehledy videa a tak dále.
- Úroveň videa: Přístupové tokeny na úrovni videa umožňují provádět operace s určitým **videem**. Můžete například získat přehledy o videích, stáhnout titulky, získat widgety a tak dále.

Můžete určit, zda tyto tokeny jsou jen pro čtení, nebo pokud umožňují úpravy zadáním **allowEdit=true/false**.

Pro většinu scénářů mezi servery budete pravděpodobně používat stejný token **účtu,** protože pokrývá operace **s účtem** i **operace s videem.** Pokud však plánujete volat na straně klienta video indexeru (například z JavaScriptu), měli byste použít token přístupu **k videu,** abyste zabránili klientům v přístupu k celému účtu. To je také důvod, proč při vkládání kódu klienta Video Indexer do klienta (například pomocí **widgetu Get Insights** nebo **Get Player Widget**) musíte poskytnout token přístupu k **videu.**

Kvůli usnadnění můžete pomocí **Autorization** API > **GetAccounts** získat účty bez předchozího získání tokenu uživatele. Můžete také požádat o získání účtů s platnými tokeny. To vám umožní přeskočit další volání pro získání tokenu účtu.

Platnost přístupových tokenů vyprší za 1 hodinu. Před použitím API Operations se ujistěte, že je váš přístupový token platný. Pokud vyprší jeho platnost, zavolejte rozhraní API autorizace znovu získat nový přístupový token.

Jste připraveni začít integrovat s rozhraním API. Najděte si [podrobný popis každého REST API Video Indexeru](https://api-portal.videoindexer.ai/).

## <a name="account-id"></a>Account ID

Parametr Account ID (ID účtu) se vyžaduje u všech volání API operací. ID účtu je globálně jedinečný identifikátor, který se dá získat jedním z těchto způsobů:

* K získání ID účtu použijte **web Video Indexer**:

    1. Přejděte na web [Video Indexer](https://www.videoindexer.ai/) a přihlaste se.
    2. Přejděte na stránku **Settings** (Nastavení).
    3. Zkopírujte ID účtu.

        ![Nastavení indexeru videa a ID účtu](./media/video-indexer-use-apis/account-id.png)

* K získání ID účtu prostřednictvím programu použijte **Video Indexer Developer Portal**.

    Použijte [rozhraní API účtu pro získání.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Account?)

    > [!TIP]
    > Přístupové tokeny pro účty můžete vygenerovat tak, že definujete `generateAccessTokens=true`.

* Získejte ID účtu z adresy URL stránky přehrávače ve vašem účtu.

    Při sledování videa se ID zobrazuje za částí `accounts` a před částí `videos`.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>Doporučení

Tato část uvádí několik doporučení pro používání rozhraní API Video Indexeru.

- Pokud plánujete nahrát video, doporučujeme soubor umístit do nějakého umístění ve veřejné síti (například na OneDrive). Získejte odkaz na video a zadejte adresu URL jako parametr souboru k nahrání.

    Adresa URL poskytnutá Video Indexeru musí odkazovat na soubor média (zvukový nebo video). Některé z odkazů vygenerovaných OneDrivem vedou na pro stránku HTML, která soubor obsahuje. Snadné ověření adresy URL je vložit do prohlížeče – pokud se soubor začne stahovat, je to pravděpodobně dobrá adresa URL. Pokud prohlížeč vykresluje nějakou vizualizaci, pravděpodobně se nejedná o odkaz na soubor, ale na stránku HTML.

- Při volání rozhraní API, které získává přehledy ze zadaného videa získáte jako obsah odpovědi podrobný výstup JSON. [Podrobnosti o vráceném JSON najdete v tomto tématu](video-indexer-output-json-v2.md).

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
  //byte[] buffer = new byte[video.Length];
  //video.Read(buffer, 0, buffer.Length);
  //content.Add(new ByteArrayContent(buffer));

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

## <a name="see-also"></a>Viz také

- [Přehled Video Indexeru](video-indexer-overview.md)
- [Oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)

## <a name="next-steps"></a>Další kroky

- [Prozkoumat podrobnosti o výstupu JSON](video-indexer-output-json-v2.md)
- Podívejte se na [ukázkový kód,](https://github.com/Azure-Samples/media-services-video-indexer) který ukazuje důležitý aspekt nahrávání a indexování videa. Následující kód wil vám dobrou představu o tom, jak používat naše rozhraní API pro základní funkce. Nezapomeňte si přečíst vtextu komentáře a všimněte si našich doporučení pro osvědčené postupy.

