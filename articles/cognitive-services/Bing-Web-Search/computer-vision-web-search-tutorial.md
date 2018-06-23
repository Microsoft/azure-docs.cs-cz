---
title: Kurz mobilní aplikace Visual vyhledávání
description: Otevřete aplikaci zdroj C# implementace visual search pomocí rozhraní napříč platformami kognitivní Services počítače vize API, rozhraní API služby Bing webové Search a Xamarin.Forms.
services: bing-web-search, computer-vision
author: Aristoddle
manager: bking
ms.service: cognitive-services
ms.devlang: csharp
ms.topic: article
ms.date: 06/22/2017
ms.author: t-jolan
ms.openlocfilehash: 54dabaeaad2e49ba7cc138636054bc3dfa4b8379
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342742"
---
# <a name="visual-search-mobile-app-tutorial"></a>Kurz mobilní aplikace Visual vyhledávání

## <a name="introduction"></a>Úvod  
Jsou zde popsány v tomto kurzu [počítače vize API](https://azure.microsoft.com/services/cognitive-services/computer-vision/) a [rozhraní API služby Bing webové Search](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/) koncových bodů a ukazuje, jak můžete použít k vytvoření aplikace visual basic vyhledávání s [Xamarin.Forms](https://developer.xamarin.com/guides/xamarin-forms/).  Celkově platí tento kurz obsahuje následující témata: 

> [!div class="checklist"]
> * Nastavení sady Visual Studio k vývoji aplikací Xamarin.Forms
> * Pomocí [modul plug-in Media Xamarin](https://github.com/jamesmontemagno/MediaPlugin) k zaznamenání a importovat data bitové kopie
> * Analýza textu v bitové kopii pomocí rozhraní API pro počítačové zpracování obrazu
> * Odesílání vyhledávacích dotazů do služby Bing webového rozhraní API vyhledávání
> * Analýza JSON odpovědi z obou rozhraní API s [Json.NET](https://github.com/JamesNK/Newtonsoft.Json) (s LINQ a modelu objektu deserializace)
> * Vytváření napříč platformami Xamarin.Forms uživatelské rozhraní pro vizuální vyhledávání 

## <a name="prerequisites"></a>Požadavky

V tomto příkladu byla vyvinuta pomocí Xamarin.Forms s [Visual Studio 2017](https://www.visualstudio.com/downloads/). Mohou být použity Community Edition nástroje Visual Studio 2017. Další informace o používání Xamarin pomocí sady Visual Studio najdete v tématu [Xamarin dokumentaci](https://developer.xamarin.com/guides/cross-platform/getting_started/).

Tato ukázka se využívá následující balíčky NuGet:

> [!div class="checklist"]
> * [Modul plug-in Xamarin Media](https://github.com/jamesmontemagno/MediaPlugin)
> * [Json.NET](https://github.com/JamesNK/Newtonsoft.Json)

Aplikace používá následující kognitivní rozhraní API služby:

> [!div class="checklist"]
> * [Hledání Bing webové rozhraní API](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/) 
> * [Rozhraní API pro počítačové zpracování obrazu](https://azure.microsoft.com/services/cognitive-services/computer-vision/)

Získat 30denní zkušební verze klíče pro tato rozhraní API, najdete v části [zkuste kognitivní služby](https://azure.microsoft.com/try/cognitive-services/). Další informace o získávání klíčů pro komerční použití, najdete v části [cenová](https://azure.microsoft.com/pricing/calculator/).

## <a name="setup-for-development"></a>Instalační program pro vývoj  

### <a name="installing-xamarin-on-windows"></a>Nainstalovat Xamarin v systému Windows
S libovolná edice Visual Studio 2017 nainstalována, spusťte instalační program Visual Studio, vyberte v nabídce hamburger přidružené k instalaci sady Visual Studio a zvolte **upravit**.  

![Instalační program Visual Studio](./media/computer-vision-web-search-tutorial/visual-studio-installer.png) 

Posuňte se dolů mobilní a herní a povolit **vývoj mobilních řešení s .NET**, není-li již povoleno.

![Instalační program Visual Studio s Xamarin.Forms vybrané](./media/computer-vision-web-search-tutorial/xamarin-forms-is-enabled.png)

Nakonec klikněte na **upravit** v pravém dolním rohu okna a počkejte Xamarin pro instalaci.

### <a name="installing-xamarin-on-macos"></a>Instalace v systému macOS Xamarin
Xamarin obsahuje předem zabalené pomocí sady Visual Studio for Mac. Musí být není třeba žádná instalace.

## <a name="building-and-running-the-app"></a>Sestavení a spuštění aplikace

Soubor řešení sady Visual Studio *(.sln)* Visual hledání aplikace můžete stáhnout z [Visual aplikace hledání kognitivní službou](https://azure.microsoft.com/resources/samples/cognitive-services-xamarin-forms-computer-vision-search/). Můžete stáhnout archivu ZIP pomocí webového prohlížeče, klonování do pracovní stanice z webu GitHub nebo stáhnout pomocí sady Visual Studio.

Chcete-li začít pracovat s ukázkou, otevřete `VisualSearchApp.sln` v sadě Visual Studio.  Inicializace požadované součásti, může chvíli trvat.

Aplikace vyžaduje dvě knihovny třetích stran: **Json.NET** a **modul plug-in Media Xamarin**. Tyto knihovny vpravo můžete nainstalovat v sadě Visual Studio s Správce balíčků NuGet. Zvolte **nástroje > Správce balíčků NuGet > spravovat balíčky NuGet pro řešení**, nebo v Průzkumníku řešení klikněte pravým tlačítkem myši a zvolte **spravovat balíčky NuGet** v místní nabídce.

V okně NuGet, vyhledávat a instalovat **modul plug-in Xamarin Media** (Xam.Plugin.Media) verze 2.6.2 a **Json.NET** (Newtonsoft.Json) 10.0.3. Nezapomeňte vybrat všechny projekty, při instalaci.

Chcete-li sestavit aplikaci na všech platformách k dispozici, stiskněte **Ctrl + Shift + B**, nebo klikněte na tlačítko **sestavení** na pásu karet a zvolte **sestavit řešení**.  Pro zkompilování a testování kódu pro iOS ze systému Windows, najdete v části [Tato příručka](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/) nápovědu.

Než spustíte aplikaci, musíte vybrat cíle konfigurace, platformy a projekt.  Aplikace Xamarin.Forms kompilace nativního kódu pro Windows, Android a iOS. Tento kurz zahrnuje snímky obrazovky ukázkovou verzi systému Windows, ale všechny verze jsou funkčně rovnocenné. Nastavení nasazení, které jsou používané v tomto průvodci jsou zobrazeny zde.  

![Visual Studio nakonfigurovaný tak, aby kompilace pro telefon se systémem Android](./media/computer-vision-web-search-tutorial/configuration-selection.png)

Po dokončení procesu sestavení a máte vybraný cílovou platformu, klikněte na **spustit** na panelu nástrojů nebo stiskněte tlačítko **F5**. Visual Studio nasadí řešení na svou cílovou platformu a spustí ho.

Zobrazí se stránka Přidat klíče. (Tato stránka je definována v `AddKeysPage.xaml`.)  

![Obrázek přidat klíče stránky](./media/computer-vision-web-search-tutorial/add-keys-page.png)  

Zde vložte vaše vize počítače a rozhraní API pro Bing webové hledání klíče. Rozhraní API vize počítač také vyžaduje, abyste zvolte server, který je hostitelem koncového bodu.

> [!TIP]
> Pokud chcete přeskočit tuto stránku, zadejte tyto informace do vhodného umístění v `App.xaml.cs`. 

Aplikace klíče ověří tak, že provedete test dotazu, pak se trvá můžete vybrat rozpoznávání znaků stránka (definované v `OcrSelectPage.xaml`).
   
![Obrázek stránky vyberte rozpoznávání znaků](./media/computer-vision-web-search-tutorial/ocr-select-page.png)  

V horní části této obrazovce můžete vybrat, zda je text, který chcete rozpoznat vytisknout nebo rukou.

> [!TIP]
> Uložení položek, ze kterého se pokoušíte rozpoznat text jako úrovni nejblíže a ujistěte se, že je rovnoměrně lit s žádné odrazů. Jsme zjistili, že ručně psané někdy tato funkce lépe písem skript nebo jiný text "zvláštní".

Klikněte na tlačítko **trvat fotografie** nebo **Import fotografií** trvat fotografie použití fotoaparátu v zařízení, nebo zvolte fotografie uložený na vašem zařízení.

Po fotografie prováděné nebo vybrali, bitovou kopii je předán rozhraní API vize počítače. Stránka najít slova (definované v `OcrResultsPage.xaml`) zobrazí všechny slova rozpoznán v bitové kopii.

![Obrázek stránky s výsledky rozpoznávání znaků](./media/computer-vision-web-search-tutorial/ocr-results-page.png)  

> [!NOTE]
> Obrázek jsme použitý pro tyto výsledky je v úložišti zdrojů jako `SamplePhotos\TestImage.jpg`.

Po kliknutí na tlačítko položky na slova nalezena stránka, výsledky webové stránky (`WebResultsPage.xaml`) se zobrazí, ukazuje horní Bing výsledky pro toto hledání.

![Obrázek webové stránky s výsledky](./media/computer-vision-web-search-tutorial/web-results-page.png)  
Nakonec vyberte položku na výsledky webové stránky otevřete odkaz v embedded webové zobrazení. (Nebo přejděte zpět na zvolte jiné výsledky.)

![Obrázek zobrazení webové stránky](./media/computer-vision-web-search-tutorial/web-view-page.png)  

Můžete pracovat s stránky, jako by v libovolného webového prohlížeče, nebo přejděte zpět na stránku webové výsledky. 

## <a name="review-and-learn"></a>Zkontrolujte a další informace

Teď, když jste prováděné aplikace Visual hledání pro typu číselník, podíváme, přesně jak používáme dvě Microsoft kognitivní Services API.  Jestli používáte tuto ukázku jako výchozí bod pro vlastní aplikaci nebo jednoduše jako postupy pro rozhraní API, je důležité provede aplikace ve obrazovkách prozkoumat, přesně jak funguje.

### <a name="add-keys-page"></a>Přidat stránku klíče
Stránka Přidat klíče uživatelského rozhraní je definována v `AddKeysPage.xaml`, a jeho primární logika je definována v `AddKeysPage.xaml.cs`. Vzhledem k tomu, že klíče se ověří tak, že žádost o test, je čas zralé k navázání použití kognitivní služby koncové body v C#.  

Základní struktura tato interakce je: 

1. Vytvoření instance *objekt HttpResponseMessage* a *HttpClient* objekty od *System.Net.Http*
2. Připojte všechny požadované hlavičky (definovanou v každém koncovém bodu referenční dokumentace rozhraní API) k *HttpClient* objektu
3. Odeslat požadavek GET nebo POST s daty, přidání všech parametrů nezbytné k identifikátor URI koncového bodu
4. Ověřte, že odpověď byla úspěšná
5. Předat odpovědi na pro další zpracování.

Je funkce, která kontroluje platnost klíč rozhraní API pro vyhledávání Bing `CheckBingSearchKey()`, tady uvedené.

```csharp
async Task CheckBingSearchKey(object sender = null, EventArgs e = null)
{
    HttpResponseMessage response;
    HttpClient SearchApiClient = new HttpClient();

    SearchApiClient.DefaultRequestHeaders.Add(AppConstants.OcpApimSubscriptionKey, BingSearchKeyEntry.Text);

    try
    {
        response = await SearchApiClient.GetAsync(AppConstants.BingWebSearchApiUrl + "q=test");

        if (response.StatusCode != System.Net.HttpStatusCode.Unauthorized)
        {
            BingSearchKeyEntry.BackgroundColor = Color.Green;
            AppConstants.BingWebSearchApiKey = BingSearchKeyEntry.Text;
            bingSearchKeyWorks = true;
        }
        else
        {
            BingSearchKeyEntry.BackgroundColor = Color.Red;
            bingSearchKeyWorks = false;
        }
    }
    catch( Exception exception )
    {
        BingSearchKeyEntry.BackgroundColor = Color.Red;
        Console.WriteLine($"ERROR: {exception.Message}");
    }
}
```

Podobné funkce `CheckComputerVisionKey()`, slouží k ověření počítače vize klíč.

### <a name="ocr-select-page"></a>Stránka Vyberte rozpoznávání znaků

Stránce vyberte rozpoznávání znaků (`OcrSelectPage.xaml`) má dvě důležité role. První určuje, jaký druh rozpoznávání znaků se provádí na cílové fotografie. Druhý umožňuje uživateli zachytit nebo otevřete bitovou kopii, kterou chtějí zpracovat.

V druhé úloze je tradičně komplikované v aplikaci a platformy, protože každou platformu vyžaduje jiný kód. Modul plug-in Media Xamarin ji zpracovává několika řádků kódu.

Následující funkce obsahuje příklad použití modul plug-in Media Xamarin pro zaznamenání fotografii.  V něm jsme:

1. Ujistěte se, že fotoaparátu je k dispozici v aktuální zařízení
2. Vytváření instancí `StoreCameraMediaOptions` objekt pro určení místa k uložení zaznamenané bitové kopie
3. Zaznamenat bitovou kopii a získat `MediaFile` objekt obsahující data bitové kopie
4. Rozbalte `MediaFile` do bajtového pole
5. Vrátí pole bajtů pro další zpracování.

Tady je `TakePhoto()`, funkce, která používá modul plug-in Media Xamarin pro zaznamenání fotografii.  

```csharp
async Task<byte[]> TakePhoto()
{
    MediaFile photoMediaFile = null;
    byte[] photoByteArray = null;

    if (CrossMedia.Current.IsCameraAvailable)
    {
        var mediaOptions = new StoreCameraMediaOptions
        {
            PhotoSize = PhotoSize.Medium,
            AllowCropping = true,
            SaveToAlbum = true,
            Name = $"{DateTime.UtcNow}.jpg"
        };
        photoMediaFile = await CrossMedia.Current.TakePhotoAsync(mediaOptions);
        photoByteArray = MediaFileToByteArray(photoMediaFile);
    }
    else
    {
        await DisplayAlert("Error", "No camera found", "OK");
        Console.WriteLine($"ERROR: No camera found");
    }
    return photoByteArray;
}
```
Fotografie importovat nástroj funguje podobným způsobem. Oba údaje funkce lze nalézt v `OcrSelectPage.xaml.cs`. 
 
> [!NOTE]
> Koncový bod rukou rozpoznávání znaků lze zpracovat pouze fotografie, které jsou menší než 4 MB volného místa. Abyste předešli problémům s velikostí souboru, jsme snižte fotografie na 50 % jeho původní velikost nastavte možnost `PhotoSize = PhotoSize.Medium` na `StoreCameraMediaOptions` objektu.  Pokud vaše zařízení trvá fotografie výjimečně vysoké kvalitě a dochází k výskytu chyb, můžete vyzkoušet `PhotoSize = PhotoSize.Small` místo.

Tady je funkce nástroj použít k převodu *MediaFile* do bajtového pole.

```csharp
byte[] MediaFileToByteArray(MediaFile photoMediaFile)
{
    using (var memStream = new MemoryStream())
    {
        photoMediaFile.GetStream().CopyTo(memStream);
        return memStream.ToArray();
    }
}
```

### <a name="ocr-results-page"></a>Stránka s výsledky rozpoznávání znaků

Stránka s výsledky rozpoznávání znaků zobrazí text extrahovat z bitové kopie pomocí **Json.NET** [SelectToken metoda](http://www.newtonsoft.com/json/help/html/SelectToken.htm).  Dva koncové body rozpoznávání znaků fungovat trochu jinak, proto je důležité zabývat obě.  

Protože rozhraní API vize počítači je pouze hostované v několika umístění serveru, jeho koncového bodu musí zkonstruovat dynamicky za běhu. Funkce `SetOcrLocation` (součástí statických *AppConstants* nalezena třída v `AppConstants.cs`) nastaví umístění identifikátoru URI koncového bodu. Ho odpovídá výběru uživatele na stránce Přidat klíče nebo používá hodnotu nastavenou `App.xaml.cs`. 

```csharp
public static void SetOcrLocation(string location)
{
    ComputerVisionApiOcrUrl = $"https://{location}.api.cognitive.microsoft.com/vision/v1.0/ocr?language=en&detectOrientation=true";
    ComputerVisionApiHandwritingUrl = $"https://{location}.api.cognitive.microsoft.com/vision/v1.0/recognizeText?handwriting=true";
}
```

Podívejme bližší pohled na tyto požadavky rozhraní API. Rozhraní API pro rozpoznávání znaků vize počítače musí být schopné Analýza textu z neurčená jazyka, ale jsme určit, aby hledání anglické textu ke zlepšení výsledky. Můžeme vám také umožní rozhraní API zjistit orientaci textu pro nás. Pomocí pevné orientace může zvýšit naše výsledky analýzy, ale detekce orientace může být užitečná v mobilní aplikaci.

Další informace o parametrech, které jsou k dispozici k tomuto koncovému bodu z [referenční dokumentace rozhraní API optické rozpoznávání znaků tiskových](https://westus.dev.cognitive.microsoft.com/docs/services/56f91f2d778daf23d8ec6739/operations/56f91f2e778daf14a499e1fc).  

Rozhraní API rukou rozpoznávání znaků je stále ve verzi preview a je momentálně pracuje pouze s anglickou text.  Z tohoto důvodu je příznak označující, zda se má analyzovat ručně psaný text na všech aktuálně svůj jediný parametr. Rozhraní API psané rozpoznávání znaků, které mohou analyzovat i počítač text na tištěných a psané, ale `handwriting=false` jsou lepší výsledky na tištěných textu. 

Vzhledem k tomu, že tato aplikace je v angličtině, jsme může použít pouze rukou rozpoznávání znaků pro tuto ukázku a nastavte `handwriting` příznak podle co uživatele informuje, abychom mohli rozpoznat.  Pro ilustraci jsme použili oba koncové body.  

Další informace o parametrech, které jsou k dispozici k tomuto koncovému bodu z [referenční dokumentace rozhraní API rukou optické rozpoznávání znaků](https://westus.dev.cognitive.microsoft.com/docs/services/56f91f2d778daf23d8ec6739/operations/587f2c6a154055056008f200).

Nyní se podívejme se na funkce, které tato rozhraní API volat.

`FetchPrintedWordList()` koncový bod rozpoznávání tisk znaků se použije k analýze tištěné text z bitové kopie. Požadavek HTTP řídí strukturou podobná volání, které se použijí na stránce Přidat klíče k ověření klíče, ale je třeba odeslat fotografii. Fotografie je příliš velký předávat řetězec dotazu, musí požadavek HTTP POST používáme místo požadavek GET. Potřebujeme ke kódování naše fotografie (která již existuje v paměti jako bajtové pole) do `ByteArrayContent` objektu a přidat hlavičku do tohoto objektu, definování typu dat byla odeslána. 

Další informace o přípustné typy obsahu v [referenční dokumentace rozhraní API vize počítače](https://westus.dev.cognitive.microsoft.com/docs/services/56f91f2d778daf23d8ec6739/operations/587f2c6a154055056008f200).  

```csharp
async Task<ObservableCollection<string>> FetchPrintedWordList()
{
    ObservableCollection<string> wordList = new ObservableCollection<string>();
    if (photo != null)
    {
        HttpResponseMessage response = null;
        using (var content = new ByteArrayContent(photo))
        {
            // The media type of the body sent to the API. 
            // "application/octet-stream" defines an image represented 
            // as a byte array
            content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
            response = await visionApiClient.PostAsync(AppConstants.ComputerVisionApiOcrUrl, content);
        }

        string ResponseString = await response.Content.ReadAsStringAsync();
        JObject json = JObject.Parse(ResponseString);

        // Here, we pull down each "line" of text and then join it to 
        // make a string representing the entirety of each line.  
        // In the Handwritten endpoint, you are able to extract the 
        // "line" without any further processing.  If you would like 
        // to simply get a list of all extracted words,* you can do 
        // this with:
        // json.SelectTokens("$.regions[*].lines[*].words[*].text) 
        IEnumerable<JToken> lines = json.SelectTokens("$.regions[*].lines[*]");
        if (lines != null)
        {
            foreach (JToken line in lines)
            {
                IEnumerable<JToken> words = line.SelectTokens("$.words[*].text");
                if (words != null)
                {
                    wordList.Add(string.Join(" ", words.Select(x => x.ToString())));
                }
            }
        }
    }
    return wordList;
}
```
> [!TIP]
> Všimněte si, jak používáme **Json.NET** [SelectToken metoda](http://www.newtonsoft.com/json/help/html/SelectToken.htm) extrahovat text z objektu odpovědi. `SelectToken` je vhodné, když budeme potřebovat pouze konkrétní součást odpověď JSON, který můžete poté předat na další funkce. V další části kódu, jsme deserializovat JSON odpovědi do modelu objektů definované v `ModelObjects.cs`.

Hlavní rozdíl mezi rozpoznávání tisk znaků a rukou rozpoznávání znaků požadavek je, že služba rozpoznávání tisk znaků vrátí rozpoznaný text v rámci odpovědi HTTP při rozpoznávání rukou znaků služba vyžaduje, aby požadavek další získání informací o. Počáteční požadavek rukou rozpoznávání znaků vrátí stav HTTP 202, která pouze signalizuje, že bylo zahájeno zpracování. Tato odpověď obsahuje koncový bod, který musíte zkontrolovat, že klient získat dokončené odpovědi, když je k dispozici. V tématu `FetchHandwrittenWordList()` zobrazíte všechny fungování.

```csharp
async Task<ObservableCollection<string>> FetchHandwrittenWordList()
{
    ObservableCollection<string> wordList = new ObservableCollection<string>();
    if (photo != null)
    {
        // Make the POST request to the handwriting recognition URL
        HttpResponseMessage response = null;
        using (var content = new ByteArrayContent(photo))
        {
            // The media type of the body sent to the API. 
            // "application/octet-stream" defines an image represented 
            // as a byte array
            content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
            response = await visionApiClient.PostAsync(AppConstants.ComputerVisionApiHandwritingUrl, content);
        }

        // Fetch results
        IEnumerable<string> operationLocationValues;
        string statusUri = string.Empty;
        if (response.Headers.TryGetValues("Operation-Location", out operationLocationValues))
        {
            statusUri = operationLocationValues.FirstOrDefault();

            // Ping status URL, wait for processing to finish 
            JObject obj = await FetchResultFromStatusUri(statusUri);
            IEnumerable<JToken> strings = obj.SelectTokens("$.recognitionResult.lines[*].text");
            foreach (string s in strings)
            {
                wordList.Add((string)s);
            }
        }
    }
    return wordList;
}
```

`FetchResultFromStatusUri()` je druhá součást procesu rozpoznávání rukopisu znaků. Testuje se příkazem ping identifikátor URI extrahovat z metadat počáteční reakce buď dokud je výsledek nebo funkce časového limitu.  Je důležité, aby tato funkce je volána asynchronně ve vlastním vlákně. Tato metoda jinak by zamčení uživatelské rozhraní až po dokončení zpracování.

```csharp
// Takes in the url to check for handwritten text parsing results, and pings it per second until processing is finished
// Returns the JObject holding data for a successful parse
async Task<JObject> FetchResultFromStatusUri(string statusUri)
{
    JObject obj = null;
    int timeoutcounter = 0;
    HttpResponseMessage response = await visionApiClient.GetAsync(statusUri);
    string responseString = await response.Content.ReadAsStringAsync();
    obj = JObject.Parse(responseString);
    while ((!((string)obj.SelectToken("status")).Equals("Succeeded")) && (timeoutcounter++ < 60))
    {
        await Task.Delay(1000);
        response = await visionApiClient.GetAsync(statusUri);
        responseString = await response.Content.ReadAsStringAsync();
        obj = JObject.Parse(responseString);
    } 
    return obj;
}
```

### <a name="web-results-page"></a>Webové stránky s výsledky
Po výběru hledaný termín zobrazené na stránce výsledky rozpoznávání znaků, na stránku webové výsledky jsme přesunout.  Zde jsme vytvořit [rozhraní API služby Bing webové Search](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/) požadavku, odeslat ke koncovému bodu služby a deserializaci JSON odpovědi pomocí **Json.NET** [DeserializeObject](http://www.newtonsoft.com/json/help/html/DeserializeObject.htm) metoda.  

```csharp
async Task<WebResultsList> GetQueryResults()
{
    // URL-encode the query term
    var queryString = System.Net.WebUtility.UrlEncode(queryTerm);

    // Here we encode the URL that will be used for the GET request to 
    // find query results.  Its arguments are as follows:
    // 
    // - [count=20] This sets the number of webpage objects returned at 
    //   "$.webpages" in the JSON response.  Currently, the API asks for 
    //   20 webpages in the response
    //
    // - [mkt=en-US] This sets the market where the results come from.
    //   Currently, the API looks for english results based in the 
    //   United States.
    //
    // - [q=queryString] This sets the string queried using the Search 
    //   API.   
    //
    // - [responseFilter=Webpages] This filters the response to only 
    //   include Webpage results.  This tag can take a comma seperated 
    //   list of response types that you are looking for.  If left 
    //   blank, all responses (webPages, News, Videos, etc) are 
    //   returned.
    //
    // - [setLang=en] This sets the languge for user interface strings. 
    //   To learn more about UI strings, check the Web Search API 
    //   reference.
    //
    // - [API Reference] https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference
    string uri = AppConstants.BingWebSearchApiUrl + $"count=20&mkt=en-US&q={queryString}&responseFilter=Webpages&setLang=en";

    // Make the HTTP Request
    WebResultsList webResults = null;
    HttpResponseMessage httpResponseMessage = await searchApiClient.GetAsync(uri);
    var responseContentString = await httpResponseMessage.Content.ReadAsStringAsync();
    JObject json = JObject.Parse(responseContentString);
    JToken resultBlock = json.SelectToken("$.webPages");
    if (resultBlock != null)
    {
        webResults = JsonConvert.DeserializeObject<WebResultsList>(resultBlock.ToString());
    }
    return webResults;
}
```

Rozhraní API služby Bing webové Search funguje nejlíp, když je co nejvíce informací o uživateli, které je možné zadat. Konkrétně byste měli téměř vždy používat `mkt` a `setLang` paramaters (v tomto poli nastavený pro angličtinu) k identifikaci umístění a upřednostňovaný jazyk uživatele.

> [!NOTE]
> Jsme jednoduché naše Bing webové vyhledávací dotaz. Pokud chcete mít jistotu, že zdrojový kód byl pochopitelnější.  V reálné aplikaci měli byste přidat následující hlavičky do požadavku HTTP pro lepší výsledky. 
> * Uživatelský Agent  
> * X-MSEdge-ClientID  
> * X-vyhledávání – když  
> * Umístění X vyhledávání  
>
> Další informace o těchto hodnot hlavičky v [referenční dokumentace rozhraní API pro Bing webové vyhledávání](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#headers)

## <a name="next-steps"></a>Další kroky
Microsoft kognitivní služby poskytují mnoho dalších služeb, které by mohl snadno integrovat do této aplikace.  Například je může:

* Přidat [vyhledávání Entity Bing](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) k posílení vyhledané
* Odkládacího souboru v [Bing vlastní vyhledávání](https://azure.microsoft.com/services/cognitive-services/bing-custom-search/) místo hledání webové služby Bing
* Použití [vyhledávání bitové kopie Bingu](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) bitové kopie Statistika možnost Další informace o zachycenou image a Image podobné najít na webu
* Využívají [kontrola pravopisu Bing](https://azure.microsoft.com/services/cognitive-services/spell-check/) další vylepšení kvality analyzovaný text
* Integrace [Microsoft Translator](https://azure.microsoft.com/services/cognitive-services/translator-text-api/) zobrazíte extrahované text v různých jazycích
* Kombinovat a párovat jiné služby od [kognitivní portál služeb](https://azure.microsoft.com/services/cognitive-services/); je limit sky!
