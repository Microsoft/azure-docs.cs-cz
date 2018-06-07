---
title: Začínáme s doručováním obsahu na vyžádání pomocí technologie .NET | Dokumentace Microsoftu
description: Tento kurz vás provede jednotlivými kroky implementace aplikace pro doručování obsahu na vyžádání pomocí služeb Azure Media Services a .NET.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 388b8928-9aa9-46b1-b60a-a918da75bd7b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 12/10/2017
ms.author: juliako
ms.openlocfilehash: a685043f0bc9841d2cc86f2c49469e8287a03b78
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "33780224"
---
# <a name="get-started-with-delivering-content-on-demand-using-net-sdk"></a>Začínáme s doručováním obsahu na vyžádání pomocí sady SDK pro .NET
[!INCLUDE [media-services-selector-get-started](../../../includes/media-services-selector-get-started.md)]

Tento kurz vás provede jednotlivými kroky implementace základní aplikace pro doručování videa na vyžádání (Video-on-Demand) pomocí služby Azure Media Services (AMS) s použitím sady SDK služby Azure Media Services .NET.

## <a name="prerequisites"></a>Požadavky

K dokončení kurzu potřebujete následující:

* Účet Azure. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* Účet Media Services. Pokud chcete vytvořit účet Media Services, přečtěte si článek [Jak vytvořit účet Media Services](media-services-portal-create-account.md).
* Rozhraní .NET 4.0 nebo novější.
* Visual Studio.

Tento kurz sestává z následujících úloh:

1. Spuštění koncového bodu streamování (pomocí webu Azure Portal).
2. Vytvoření a konfigurace projektu Visual Studia.
3. Připojení k účtu Media Services.
2. Nahrání videosouboru
3. Zakódování zdrojového souboru do sady souborů MP4 s adaptivní přenosovou rychlostí
4. Publikování assetu a získání adres URL streamování a progresivního stahování  
5. Přehrání obsahu

## <a name="overview"></a>Přehled
Tento kurz vás provede jednotlivými kroky implementace aplikace pro doručování videa na vyžádání (VoD, Video-on-Demand) pomocí sady SDK služby Azure Media Services (AMS) pro .NET.

Kurz představuje základní pracovní postup služby Media Services a nejběžnější programovací objekty a úkoly, které Media Services vyžaduje. Po dokončení kurzu bude umět streamovat nebo progresivně stáhnout ukázkový multimediální soubor, který jste odeslali, zakódovali a stáhli.

### <a name="ams-model"></a>Model AMS

Následující obrázek ukazuje některé z nejčastěji používaných objektů při vývoji aplikace VoD na základě modelu Media Services OData.

Kliknutím na obrázek zobrazíte jeho plnou velikost.  

<a href="./media/media-services-dotnet-get-started/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-dotnet-get-started/media-services-overview-object-model-small.png"></a> 

Celý model můžete zobrazit [zde](https://media.windows.net/API/$metadata?api-version=2.15).  

## <a name="start-streaming-endpoints-using-the-azure-portal"></a>Spuštění koncového bodu streamování pomocí webu Azure Portal

Při práci se službou Azure Media Services je jedním z nejběžnější scénářů doručování videa prostřednictvím streamování s adaptivní přenosovou rychlostí. Služba Media Services poskytuje dynamické balení, které umožňuje doručovat obsah s adaptivní přenosovou rychlostí s kódováním MP4 ve formátech streamování podporovaných službou Media Services (MPEG DASH, HLS, technologie Smooth Streaming). není přitom potřeba ukládat předem zabalené verze pro každý z těchto formátů streamování.

>[!NOTE]
>Po vytvoření účtu AMS se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **Spuštěno**.

Pokud chcete spustit koncový bod streamování, postupujte takto:

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
2. V okně Nastavení klikněte na Koncové body streamování.
3. Klikněte na výchozí koncový bod streamování.

    Zobrazí se okno VÝCHOZÍ KONCOVÝ BOD STREAMOVÁNÍ – PODROBNOSTI.

4. Klikněte na ikonu Spustit.
5. Kliknutím na tlačítko Uložit uložte provedené změny.

## <a name="create-and-configure-a-visual-studio-project"></a>Vytvoření a konfigurace projektu Visual Studia

1. Nastavte své vývojové prostředí a v souboru app.config vyplňte informace o připojení, jak je popsáno v tématu [Vývoj pro Media Services v .NET](media-services-dotnet-how-to-use.md). 
2. Vytvořte novou složku (kdekoli na místním disku) a zkopírujte si soubor .mp4, který chcete kódovat a streamovat nebo progresivně stahovat. V tomto příkladu používáme cestu „C:\VideoFiles“.

## <a name="connect-to-the-media-services-account"></a>Připojení k účtu Media Services

Když službu Media Services používáte s rozhraním .NET, musíte třídu **CloudMediaContext** používat pro většinu programovacích úloh: připojení k účtu Media Services, vytváření, aktualizace, otevírání a odstraňování následujících objektů: prostředky, soubory prostředků, úlohy, zásady přístupu, lokátory atd.

Výchozí třídu Program přepište následujícím kódem: Kód ukazuje, jak číst hodnoty připojení ze souboru App.config a jak vytvořit objekt **CloudMediaContext**, abyste se mohli připojit ke službě Media Services. Další informace najdete v tématu popisujícím [připojení k rozhraní API služby Media Services](media-services-use-aad-auth-to-access-ams-api.md).

Nezapomeňte aktualizovat název souboru a cestu podle umístění multimediálního souboru.

Funkce **Main** volá metody, které si definujeme v této části.

> [!NOTE]
> Dokud nepřidáte definice pro všechny funkce definované dále v tomto článku, budou se vám zobrazovat chyby kompilace.

```csharp
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
        try
        {
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Add calls to methods defined in this section.
            // Make sure to update the file name and path to where you have your media file.
            IAsset inputAsset =
            UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.None);

            IAsset encodedAsset =
            EncodeToAdaptiveBitrateMP4s(inputAsset, AssetCreationOptions.None);

            PublishAssetGetURLs(encodedAsset);
        }
        catch (Exception exception)
        {
            // Parse the XML error message in the Media Services response and create a new
            // exception with its content.
            exception = MediaServicesExceptionParser.Parse(exception);

            Console.Error.WriteLine(exception.Message);
        }
        finally
        {
            Console.ReadLine();
        }
        }
```

## <a name="create-a-new-asset-and-upload-a-video-file"></a>Vytvoření nového prostředku a odeslání videosouboru

Ve službě Media Services můžete digitální soubory nahrát (nebo ingestovat) do prostředku. Entita **Prostředek** může obsahovat video, zvuk, obrázky, kolekci miniatur, textové stopy a soubory titulků (a metadata o těchto souborech.)  Jakmile soubory odešlete, bude váš obsah bezpečně uložen v cloudu pro další zpracování a streamování. Soubory v prostředku se nazývají **soubory prostředku**.

Metoda **UploadFile** definovaná níže volá metodu **CreateFromFile** (definovanou v rozšíření sady SDK pro .NET). **CreateFromFile** vytvoří nový prostředek, do kterého se zadaný zdrojový soubor odešle.

Metoda **CreateFromFile** přijímá parametr \*\*AssetCreationOptions, který vám umožňuje určit jednu z následujících možností vytvoření prostředku:

* **Žádné** – nepoužívá se žádné šifrování. Toto je výchozí hodnota. Pamatujte, že při použití této možnosti není váš obsah chráněný během přenosu ani při umístění v úložišti.
  Pokud chcete pomocí progresivního stahování dodávat obsah ve formátu MP4, použijte tuto možnost.
* **StorageEncrypted** – tuto možnost použijte k místnímu šifrování nešifrovaného obsahu pomocí 256bitového šifrování AES (Advanced Encryption Standard). Obsah je poté odeslán do služby Azure Storage, kde bude uložený v zašifrované podobě. Prostředky chráněné pomocí šifrování úložiště jsou před kódováním automaticky bez šifrování umístěny do systému souborů EFS a volitelně se znovu zašifrují před jejich odesláním zpět v podobě nového výstupního prostředku. Případem primárního použití šifrování úložiště je situace, kdy chcete zabezpečit soubory s vysoce kvalitními vstupními multimediálními soubory pomocí silného šifrování na disku.
* **CommonEncryptionProtected** – tuto možnost použijte, pokud nahráváte zašifrovaný obsah chráněný běžným šifrováním nebo DRM s technologií PlayReady (například technologie Smooth Streaming chráněná pomocí DRM s technologií PlayReady).
* **EnvelopeEncryptionProtected** – tuto možnost použijte, pokud odesíláte HLS se šifrováním pomocí standardu AES. Pamatujte, že soubory musí být zakódované a zašifrované pomocí správce transformací.

Metoda **CreateFromFile** umožňuje také určení zpětného volání za účelem hlášení průběhu odesílání souboru.

V následujícím příkladu určíme pro možnosti prostředku možnost **Žádné**.

Přidejte následující metodu do třídy Program.

```csharp
    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Asset {0} created.", inputAsset.Id);

        return inputAsset;
    }
```

## <a name="encode-the-source-file-into-a-set-of-adaptive-bitrate-mp4-files"></a>Zakódování zdrojového souboru do sady souborů MP4 s adaptivní přenosovou rychlostí
Po zpracování prostředků ve službě Media Services a před jejich předáním klientům můžete média zakódovat, transmuxovat, označit vodoznakem a tak dále. Tyto aktivity se plánují a spouštějí s několika instancemi role na pozadí, abyste měli zajištěný vysoký výkon a dostupnost. Tyto aktivity se nazývají úlohy a každá úloha se skládá z atomických úloh, které vykonávají samotnou práci na souboru prostředku.

Jak jsem už zmínili dřív, při práci se službou Azure Media Services je jedním nejběžnější scénářů doručování streamování s adaptivní přenosovou rychlostí vašim klientům. Služba Media Services umí dynamicky balit sady souborů MP4 s adaptivní přenosovou rychlostí do následujících formátů: HTTP Live Streaming (HLS), technologie Smooth Streaming a MPEG DASH.

Pokud chcete využít výhod dynamického balení, musíte soubor mezzanine (zdrojový soubor) zakódovat nebo převést na sadu souborů MP4 nebo Smooth Streaming s adaptivní přenosovou rychlostí.  

Následující kód ukazuje, jak se odeslat kódovací úlohu. Úloha obsahuje jednu úlohu, která určuje převod souboru mezzanine do sady souborů MP4 s adaptivní přenosovou rychlostí pomocí **standardu pro kodér médií**. Kód předá úlohu a čeká na její dokončení.

Po dokončení úlohy budete moct streamovat prostředek nebo progresivně stahovat soubory MP4, které jste vytvořili překódováním.

Přidejte následující metodu do třídy Program.

```csharp
    static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
    {

        // Prepare a job with a single task to transcode the specified asset
        // into a multi-bitrate asset.

        IJob job = _context.Jobs.CreateWithSingleTask(
            "Media Encoder Standard",
            "Adaptive Streaming",
            asset,
            "Adaptive Bitrate MP4",
            options);

        Console.WriteLine("Submitting transcoding job...");


        // Submit the job and wait until it is completed.
        job.Submit();

        job = job.StartExecutionProgressTask(
            j =>
            {
                Console.WriteLine("Job state: {0}", j.State);
                Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
            },
            CancellationToken.None).Result;

        Console.WriteLine("Transcoding job finished.");

        IAsset outputAsset = job.OutputMediaAssets[0];

        return outputAsset;
    }
```

## <a name="publish-the-asset-and-get-urls-for-streaming-and-progressive-download"></a>Publikování prostředku a získání adres URL pro streamování a progresivní stahování

Pokud chcete prostředek streamovat nebo stáhnout, musíte ho nejdřív „publikovat“ vytvořením lokátoru. Lokátory zajišťují přístup k souborům, které jsou obsaženy v assetu. Služba Media Services podporuje dva typy lokátorů: lokátor OnDemandOrigin, používaný ke streamování médií (například MPEG DASH, HLS nebo technologie Smooth Streaming), a lokátor s přístupovým podpisem (SAS), používaný ke stahování mediálních souborů.

### <a name="some-details-about-url-formats"></a>Podrobnosti o formátech adres URL

Po vytvoření lokátorů můžete sestavit adresy URL, které budou sloužit ke streamování a stahování souborů. Ukázka v tomto kurzu zobrazí na výstupu adresy URL, které můžete vložit do příslušných prohlížečů. V této části je pár příkladů, jak vypadají jiné formáty.

#### <a name="a-streaming-url-for-mpeg-dash-has-the-following-format"></a>Streamovací adresa URL pro MPEG DASH má následující formát:

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest **(format=mpd-time-csf)**

#### <a name="a-streaming-url-for-hls-has-the-following-format"></a>Streamovací adresa URL pro HLS má následující formát:

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest **(format=m3u8-aapl)**

#### <a name="a-streaming-url-for-smooth-streaming-has-the-following-format"></a>Streamovací adresa URL pro technologii Smooth Streaming má následující formát:

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest


#### <a name="a-sas-url-used-to-download-files-has-the-following-format"></a>SAS adresa URL používaná ke stahování souborů má následující formát:

{blob container name}/{asset name}/{file name}/{SAS signature}

Rozšíření sady SDK služby Media Services pro .NET  nabízejí užitečné pomocné metody, které vracejí formátované adresy URL publikovaného prostředku.

Následující kód používá rozšíření sady SDK pro .NET k vytvoření lokátorů a k získání adres URL pro streamování a progresivní stahování. Kód také ukazuje způsob stahování souborů do místní složky.

Přidejte následující metodu do třídy Program.

```csharp
    static public void PublishAssetGetURLs(IAsset asset)
    {
        // Publish the output asset by creating an Origin locator for adaptive streaming,
        // and a SAS locator for progressive download.

        _context.Locators.Create(
            LocatorType.OnDemandOrigin,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));

        _context.Locators.Create(
            LocatorType.Sas,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));


        IEnumerable<IAssetFile> mp4AssetFiles = asset
                .AssetFiles
                .ToList()
                .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Get the Smooth Streaming, HLS and MPEG-DASH URLs for adaptive streaming,
        // and the Progressive Download URL.
        Uri smoothStreamingUri = asset.GetSmoothStreamingUri();
        Uri hlsUri = asset.GetHlsUri();
        Uri mpegDashUri = asset.GetMpegDashUri();

        // Get the URls for progressive download for each MP4 file that was generated as a result
        // of encoding.
        List<Uri> mp4ProgressiveDownloadUris = mp4AssetFiles.Select(af => af.GetSasUri()).ToList();


        // Display  the streaming URLs.
        Console.WriteLine("Use the following URLs for adaptive streaming: ");
        Console.WriteLine(smoothStreamingUri);
        Console.WriteLine(hlsUri);
        Console.WriteLine(mpegDashUri);
        Console.WriteLine();

        // Display the URLs for progressive download.
        Console.WriteLine("Use the following URLs for progressive download.");
        mp4ProgressiveDownloadUris.ForEach(uri => Console.WriteLine(uri + "\n"));
        Console.WriteLine();

        // Download the output asset to a local folder.
        string outputFolder = "job-output";
        if (!Directory.Exists(outputFolder))
        {
            Directory.CreateDirectory(outputFolder);
        }

        Console.WriteLine();
        Console.WriteLine("Downloading output asset files to a local folder...");
        asset.DownloadToFolder(
            outputFolder,
            (af, p) =>
            {
                Console.WriteLine("Downloading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Output asset files available at '{0}'.", Path.GetFullPath(outputFolder));
    }
```

## <a name="test-by-playing-your-content"></a>Testování přehráváním obsahu

Po spuštění programu definovaného v předchozí části se v okně konzoly zobrazí adresy URL, které se budou podobat následujícím.

Adresa URL adaptivního streamování:

Technologie Smooth Streaming

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

HLS

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

MPEG DASH

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)

Adresa URL progresivního stahování (audio a video).

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


Abyste mohli streamovat video, vložte adresu URL do textového pole URL v [přehrávači Azure Media Services](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

Pokud chcete otestovat progresivní stahování, vložte adresu URL do prohlížeče (například Internet Exploreru, Chromu nebo Safari).

Další informace najdete v následujících tématech:

- [Přehrávání obsahu ve stávajících přehrávačích](media-services-playback-content-with-existing-players.md)
- [Vývoj aplikací videopřehrávače](media-services-develop-video-players.md)
- [Vložení videa adaptivního streamování MPEG-DASH do aplikace HTML5 využívající DASH.js](media-services-embed-mpeg-dash-in-html5.md)

## <a name="download-sample"></a>Stažení ukázky
Následující ukázka kódu obsahuje kód, který jste vytvořili v tomto kurzu: [ukázka](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="next-steps"></a>Další kroky

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]



<!-- Anchors. -->


<!-- URLs. -->
[Web Platform Installer]: http://go.microsoft.com/fwlink/?linkid=255386
[Portal]: http://portal.azure.com/
