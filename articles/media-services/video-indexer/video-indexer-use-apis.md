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
ms.date: 01/07/2021
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: ac0b206a86edf3157141b56e0c2623a8429b0c7a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785518"
---
# <a name="tutorial-use-the-video-indexer-api"></a>Kurz: Používání rozhraní API Video Indexeru

Video Indexer slučuje různé technologie pro audio a video umělal Intelligence (AI), které Microsoft nabízí, do jedné integrované služby, což zjednodušuje vývoj. Rozhraní API jsou navržená tak, aby umožňovala vývojářům soustředit se na využívání technologií Media AI, aniž by se museli starat o škálování, globální dosah, dostupnost a spolehlivost cloudových platforem. Rozhraní API můžete použít k nahrání souborů, získání podrobných informací o videích, získání adres URL pro vložené widgety a pomůcky pro vkládání a další informace.

Při vytváření účtu Video Indexeru můžete zvolit účet bezplatné zkušební verze (ve kterém získáte určitý počet minut indexování zdarma) nebo placenou variantu (ve které nejste omezení kvótou). V bezplatné zkušební verzi Video Indexer poskytuje až 600 minut bezplatného indexování pro webové uživatele a až 2 400 minut bezplatného indexování pro uživatele rozhraní API. Pomocí placené možnosti vytvoříte účet Video Indexer, který je [připojený k vašemu předplatnému Azure a účet Azure Media Services](connect-to-azure.md). Platíte za minuty, další informace najdete v tématu [Media Services ceny](https://azure.microsoft.com/pricing/details/media-services/).

Tento článek popisuje, jak můžou vývojáři využít výhod rozhraní [API Video Indexeru](https://api-portal.videoindexer.ai/).

## <a name="subscribe-to-the-api"></a>Přihlášení k odběru rozhraní API

1. Přihlaste se k portálu [Video Indexer Developer Portal](https://api-portal.videoindexer.ai/).

    Přečtěte si poznámku k verzi týkající se [přihlašovacích informací](release-notes.md#october-2020).
    
     ![Přihlášení k Video Indexer portálu pro vývojáře](./media/video-indexer-use-apis/sign-in.png)

   > [!Important]
   > * Musíte použít stejného zprostředkovatele, jakého jste použili při registraci k Video Indexeru.
   > * Účty osobních účtů Google a Microsoft (Outlook/Live) se dají používat jenom pro zkušební účty. Účty připojené k Azure vyžadují Azure AD.
   > * V jednom e-mailu může být jenom jeden aktivní účet. Pokud se uživatel pokusí přihlásit pomocí user@gmail.com LinkedInu a novějšího s aplikací user@gmail.com pro Google, zobrazí se mu chybová stránka s oznámením, že uživatel už existuje.

2. Přihlaste se k odběru.

   Vyberte kartu [produkty](https://api-portal.videoindexer.ai/products) . Pak vyberte autorizace a přihlášení k odběru.
    
   ![Karta produkty na portálu pro vývojáře Video Indexer](./media/video-indexer-use-apis/authorization.png)

   > [!NOTE]
   > Noví uživatelé jsou k odběru API Authorization automaticky přihlášení.
    
   Po přihlášení k odběru můžete najít předplatné v části autorizace **produktů**  ->  . Na stránce předplatné najdete primární a sekundární klíče. Klíče by měly být chráněné. Klíče by měl používat jenom kód vašeho serveru. Neměly by být k dispozici na straně klienta (. js,. html atd.).

   ![Předplatné a klíče na portálu pro vývojáře Video Indexer](./media/video-indexer-use-apis/subscriptions.png)

> [!TIP]
> Uživatelé Video Indexeru se můžou pomocí jednoho klíče předplatného připojit k několika účtům Video Indexeru. Tyto účty Video Indexeru můžete propojit s různými účty Media Services.

## <a name="obtain-access-token-using-the-authorization-api"></a>Získání přístupového tokenu pomocí rozhraní API Authorization

Jakmile se přihlásíte k odběru autorizačního rozhraní API, můžete získat přístupové tokeny. Tyto přístupové tokeny se používají k ověřování v API Operations.

Každé volání do API Operations by mělo být přidružené k přístupovému tokenu s odpovídajícím oborem autorizace volání.

- Úroveň uživatele: tokeny přístupu na úrovni uživatele umožňují provádět operace na úrovni **uživatele** . Třeba získat přidružené účty.
- Úroveň účtu: přístupové tokeny na úrovni účtu umožňují provádět operace na úrovni **účtu** nebo na úrovni **videa** . Například nahrávání videa, vypsání všech videí, získání přehledů videí atd.
- Úroveň videa: přístupové tokeny na úrovni videa umožňují provádět operace na konkrétním **videu**. Můžete například získat přehled o videích, stahovat titulky, získávat widgety atd.

Úroveň oprávnění tokenů můžete řídit dvěma způsoby:

* Pro tokeny **účtu** můžete použít token pro **získání přístupu k účtu s** rozhraním API pro oprávnění a zadat typ **oprávnění (** /  / **MyAccessManager** / **Owner** přispěvatele).
* Pro všechny typy tokenů (včetně tokenů **účtu** ) můžete zadat **allowEdit = true/false**. **hodnota false** je ekvivalentem oprávnění **čtenáře** (jen pro čtení) a **hodnota true** je ekvivalentem oprávnění **přispěvatele** (pro čtení i zápis).

Pro většinu scénářů mezi servery budete pravděpodobně používat stejný token **účtu** , protože zahrnuje operace s **účty** a **video** operace. Pokud ale plánujete, že na straně klienta chcete Video Indexer (například z JavaScriptu), budete chtít použít token přístupu k **videu** , abyste klientům zabránili v získání přístupu k celému účtu. To je také důvod, proč při vkládání Video Indexer kódu klienta do klienta (například pomocí **widgetu Get Insights** nebo **Get widget Player**) musíte zadat přístupový token **videa** .

Kvůli usnadnění můžete pomocí **Autorization** API > **GetAccounts** získat účty bez předchozího získání tokenu uživatele. Můžete také požádat o získání účtů s platnými tokeny. To vám umožní přeskočit další volání pro získání tokenu účtu.

Platnost přístupových tokenů vyprší za 1 hodinu. Před použitím API Operations se ujistěte, že je váš přístupový token platný. V případě vypršení platnosti zavolejte autorizační rozhraní API, abyste získali nový přístupový token.

Jste připraveni zahájit integraci s rozhraním API. Najděte si [podrobný popis každého REST API Video Indexeru](https://api-portal.videoindexer.ai/).

## <a name="account-id"></a>Account ID

Parametr Account ID (ID účtu) se vyžaduje u všech volání API operací. ID účtu je globálně jedinečný identifikátor, který se dá získat jedním z těchto způsobů:

* K získání ID účtu použijte **web Video Indexer**:

    1. Přejděte na web [Video Indexer](https://www.videoindexer.ai/) a přihlaste se.
    2. Přejděte na stránku **Settings** (Nastavení).
    3. Zkopírujte ID účtu.

        ![Nastavení Video Indexer a ID účtu](./media/video-indexer-use-apis/account-id.png)

* K získání ID účtu prostřednictvím programu použijte **Video Indexer Developer Portal**.

    Použijte rozhraní [Get Account](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Account) API.

    > [!TIP]
    > Přístupové tokeny pro účty můžete vygenerovat tak, že definujete `generateAccessTokens=true`.

* Získejte ID účtu z adresy URL stránky přehrávače ve vašem účtu.

    Při sledování videa se ID zobrazuje za částí `accounts` a před částí `videos`.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>Doporučení

Tato část uvádí několik doporučení pro používání rozhraní API Video Indexeru.

- Pokud plánujete odeslat video, doporučujeme umístit soubor do umístění v některé veřejné síti (například účet Azure Blob Storage). Získejte odkaz na video a zadejte adresu URL jako parametr souboru k nahrání.

    Adresa URL poskytnutá Video Indexeru musí odkazovat na soubor média (zvukový nebo video). Jednoduché ověření adresy URL (nebo adresy URL SAS) je vložení do prohlížeče, pokud se soubor začne přehrávat/stahovat, je to nejspíš dobrá adresa URL. Pokud prohlížeč vykresluje určitou vizualizaci, nejedná se nejspíš o odkaz na soubor, ale na stránku HTML.

- Při volání rozhraní API, které získává přehledy ze zadaného videa získáte jako obsah odpovědi podrobný výstup JSON. [Podrobnosti o vráceném JSON najdete v tomto tématu](video-indexer-output-json-v2.md).

## <a name="code-sample"></a>Ukázka kódu

Následující fragment kódu v jazyce C# předvádí společné použití všech rozhraní API Video Indexeru.

```csharp
var apiUrl = "https://api.videoindexer.ai";
var accountId = "..."; 
var location = "westus2"; // replace with the account's location, or with “trial” if this is a trial account
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

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení tohoto kurzu odstraňte prostředky, které neplánujete použít.

## <a name="see-also"></a>Viz také

- [Přehled Video Indexeru](video-indexer-overview.md)
- [Oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)

## <a name="next-steps"></a>Další kroky

- [Projděte si podrobnosti výstupního formátu JSON.](video-indexer-output-json-v2.md)
- Podívejte se na [vzorový kód](https://github.com/Azure-Samples/media-services-video-indexer) , který ukazuje důležité aspekty nahrávání a indexování videa. Následující kód vám poskytne dobrý nápad na použití našeho rozhraní API pro základní funkce. Nezapomeňte si přečíst vložené komentáře a poznamenat naše rady k osvědčeným postupům.

