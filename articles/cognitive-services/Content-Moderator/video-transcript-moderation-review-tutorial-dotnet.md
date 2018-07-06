---
title: Azure Content Moderator – moderování videa a záznamy o studiu v rozhraní .NET | Dokumentace Microsoftu
description: Jak používat Content Moderator, obscénnost, videa a záznamy o studiu v rozhraní .NET.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 1/27/2018
ms.author: sajagtap
ms.openlocfilehash: 0f851c030a05880d79a998ed4b4a941082c057b9
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37865467"
---
# <a name="video-and-transcript-moderation-tutorial"></a>Kurz moderování videa a přepis

Rozhraní API pro video Content Moderator umožňují moderování videa a vytvářet videa revize v nástroji pro recenze prováděné lidmi. 

Tento podrobný kurz pomůže pochopit, jak sestavit kompletní řešení moderování videa a přepis moderování s podporou počítače a vytvoření revize lidských v the smyčky.

Stáhněte si [konzolovou aplikaci C#](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) pro účely tohoto kurzu. Konzolová aplikace používá sadu SDK a související balíčky provádět následující úlohy:

- Komprese vstupní video(s) pro rychlejší zpracování
- Moderování videa, snímky a rámce s přehledy
- Použít časové razítko rámce k vytvoření miniatury (imagí)
- Odeslat časová razítka a miniatury k vytvoření videa revize
- Převod videa řeči na text (přepisu) pomocí rozhraní API služby Media Indexer
- Střední přepisu službou moderování textu
- Přidat moderované přepisu videa revize

## <a name="sample-program-outputs"></a>Ukázkový program výstupy

Než budete pokračovat, Podívejme se na výstupy naleznete ukázky z programu:

- [Výstup konzoly](#program-output)
- [Video revize](#video-review-default-view)
- [Zobrazit přepis](#video-review-transcript-view)

## <a name="prerequisites"></a>Požadavky

1. Zaregistrovat [nástroj pro kontrolu Content Moderatoru](https://contentmoderator.cognitive.microsoft.com/) webu a [vytvořit vlastní značky](Review-Tool-User-Guide/tags.md) , který přiřazuje konzolovou aplikaci C# z v rámci kódu. Následující obrazovka ukazuje vlastní značky.

  ![Vlastní značky moderování videa](images/video-tutorial-custom-tags.png)

1. Spuštění ukázkové aplikace, budete potřebovat účet Azure a účet Azure Media Services. Kromě toho potřebujete přístup k privátní verzi preview Content Moderatoru. Nakonec se vyžadují pověření ověřování Azure Active Directory. Podrobnosti o získání těchto informací najdete v tématu [rychlý start rozhraní API pro moderování videa](video-moderation-api.md).

1. Upravte soubor `App.config` , přidejte název tenanta Active Directory, koncové body služby a klíče předplatného indikován `#####`. Budete potřebovat následující informace:

|Klíč|Popis|
|-|-|
|`AzureMediaServiceRestApiEndpoint`|Koncový bod pro rozhraní API služby Azure Media Services (AMS)|
|`ClientSecret`|Klíč předplatného pro Azure Media Services|
|`ClientId`|ID klienta pro Azure Media Services|
|`AzureAdTenantName`|Název tenanta Active Directory představující vaší organizaci|
|`ContentModeratorReviewApiSubscriptionKey`|Klíč předplatného pro Content Moderator, projděte si rozhraní API|
|`ContentModeratorApiEndpoint`|Koncový bod pro rozhraní API Content Moderatoru|
|`ContentModeratorTeamId`|ID týmu Content moderatoru|

## <a name="getting-started"></a>Začínáme

Třída `Program` v `Program.cs` je hlavní vstupní bod do aplikace moderování videa.

### <a name="methods-of-class-program"></a>Metody třídy Program

|Metoda|Popis|
|-|-|
|`Main`|Analyzuje příkazového řádku, shromažďuje vstup uživatele a spustí zpracování.|
|`ProcessVideo`|Komprimuje, nahraje, moderates a vytvoří videa revize.|
|`CreateVideoStreamingRequest`|Vytvoří datový proud nahrát video|
|`GetUserInputs`|Shromažďuje vstup uživatele; použít, pokud jsou k dispozici žádné možnosti příkazového řádku|
|`Initialize`|Inicializuje objektů potřebných pro proces moderování|

### <a name="the-main-method"></a>Main – metoda

`Main()` je, kde spuštění, tak, aby byl místo zahájení Principy procesu moderování videa.

    static void Main(string[] args)
    {
        if (args.Length == 0)
        {
            string videoPath = string.Empty;
            GetUserInputs(out videoPath);
            Initialize();
            AmsConfigurations.logFilePath = Path.Combine(Path.GetDirectoryName(videoPath), "log.txt");
            try
            {
                ProcessVideo(videoPath).Wait();
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
        }
        else
        {
            DirectoryInfo directoryInfo = new DirectoryInfo(args[0]);
            if (args.Length == 2)
                bool.TryParse(args[1], out generateVtt);
            Initialize();
            AmsConfigurations.logFilePath = Path.Combine(args[0], "log.txt");
            var files = directoryInfo.GetFiles("*.mp4", SearchOption.AllDirectories);
            foreach (var file in files)
            {
                try
                {
                    ProcessVideo(file.FullName).Wait();
                }
                catch (Exception ex)
                {
                    Console.WriteLine(ex.Message);
                }
            }
        }
    }

`Main()` zpracuje argumenty příkazového řádku následující:

- Cesta k adresáři obsahujícímu MPEG-4 video soubory k odeslání pro moderování. Všechny `*.mp4` soubory v tomto adresáři a jeho podadresářích jsou odeslány pro moderování.
- Volitelně můžete příznak logické hodnoty (true/false) určující, zda záznamy o studiu text by měl být vygenerován pro účely moderování zvuk.

Pokud nejsou žádné argumenty příkazového řádku `Main()` volání `GetUserInputs()`. Tato metoda se zobrazí výzva k zadání cesty do jednoho souboru videa a k určení, zda by měl být vygenerován text přepisu.

> [!NOTE]
> Využívá konzolovou aplikaci [rozhraní API služby Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) ke generování záznamy o studiu z Nahraná videa, zvuková stopa. Výsledky jsou k dispozici ve formátu WebVTT. Další informace v tomto formátu najdete v tématu [webové Video textové stopy formátu](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API).

### <a name="initialize-and-processvideo-methods"></a>Inicializace a ProcessVideo metody

Bez ohledu na to, zda možnosti programu pochází z příkazového řádku nebo z interaktivní uživatelský vstup `Main()` další volání `Initialize()` k vytvoření následujících případech:

|Třída|Popis|
|-|-|
|`AMSComponent`|Komprimuje videosoubory před odesláním pro moderování.|
|`AMSconfigurations`|Rozhraní pro aplikace konfigurační data, součástí `App.config`.|
|`VideoModerator`| Nahrávání, kódování, šifrování a moderování pomocí AMS SDK|
|`VideoReviewApi`|Spravuje videa revizí ve službě Content Moderatoru|

Tyto třídy (ze aside `AMSConfigurations`, což je jednoduchá) se budeme věnovat jednotlivě podrobněji v nadcházejících částech tohoto kurzu.

A konečně, video soubory jsou zpracované jeden po druhém voláním `ProcessVideo()` pro každý.

    private static async Task ProcessVideo(string videoPath)
    {
        Stopwatch sw = new Stopwatch();
        sw.Start();
        Console.ForegroundColor = ConsoleColor.White;
        Console.WriteLine("\nVideo compression process started...");

        var compressedVideoPath = amsComponent.CompressVideo(videoPath);
        if (string.IsNullOrWhiteSpace(compressedVideoPath))
        {
            Console.ForegroundColor = ConsoleColor.Red;
            Console.WriteLine("Video Compression failed.");
        }

        Console.WriteLine("\nVideo compression process completed...");

        UploadVideoStreamRequest uploadVideoStreamRequest = CreateVideoStreamingRequest(compressedVideoPath);
        UploadAssetResult uploadResult = new UploadAssetResult();

        if (generateVtt)
        {
            uploadResult.GenerateVTT = generateVtt;
        }
        Console.WriteLine("\nVideo moderation process started...");

        if (!videoModerator.CreateAzureMediaServicesJobToModerateVideo(uploadVideoStreamRequest, uploadResult))
        {
            Console.ForegroundColor = ConsoleColor.Red;
            Console.WriteLine("Video Review process failed.");
        }

        Console.WriteLine("\nVideo moderation process completed...");
        Console.WriteLine("\nVideo review process started...");
        string reviewId = await videoReviewApi.CreateVideoReviewInContentModerator(uploadResult);
        Console.WriteLine("\nVideo review successfully completed...");
        sw.Stop();
        Console.WriteLine("\nTotal Elapsed Time: {0}", sw.Elapsed);
        using (var stw = new StreamWriter(AmsConfigurations.logFilePath, true))
        {
            stw.WriteLine("Video File Name: " + Path.GetFileName(videoPath));
            stw.WriteLine($"ReviewId: {reviewId}");
            stw.WriteLine("Total Elapsed Time: {0}", sw.Elapsed);
        }
    }


`ProcessVideo()` Metoda je poměrně jednoduché. Provádí následující operace v uvedeném pořadí:

- Komprimuje videa
- Nahrání videa do prostředku Azure Media Services
- Vytvoří úlohu, obscénnost video služby AMS
- Vytvoří videa kontrolu v Content Moderatoru

Zvažte následující části podrobněji některé z jednotlivých procesů vyvolávat `ProcessVideo()`. 

## <a name="compressing-the-video"></a>Komprese videa

Chcete-li minimalizovat síťový provoz, aplikace převede videosouborů na formát H.264 (AVC MPEG-4) a škálují na maximální šířku 640 pixelů. Kodek H.264 se nedoporučuje, protože jeho vysoké účinnosti (komprese rychlost). Komprese se provádí pomocí bezplatnou `ffmpeg` nástroj příkazového řádku, který je součástí `Lib` složku řešení sady Visual Studio. Vstupní soubory mohou být libovolný formát podporovaný `ffmpeg`, včetně nejčastěji používané videosoubor formáty a kodeky ve službě.

> [!NOTE]
> Když spustíte program pomocí možnosti příkazového řádku, zadejte adresář obsahující video soubory k odeslání pro moderování. Všechny soubory tento adresář tím, že `.mp4` příponu názvu souboru se zpracovávají. Pokud chcete zpracovávat jiné přípony názvů souborů, aktualizujte `Main()` metoda `Program.cs` zahrnout požadované rozšíření.

Je kód, který komprimuje jednoho souboru videa `AmsComponent` třídy v `AMSComponent.cs`. Metoda za tuto funkci je `CompressVideo()`, je vidět tady.

    public string CompressVideo(string videoPath)
    {
        string ffmpegBlobUrl;
        if (!ValidatePreRequisites())
        {
            Console.WriteLine("Configurations check failed. Please cross check the configurations!");
            throw new Exception();
        }

        if (File.Exists(_configObj.FfmpegExecutablePath))
        {
            ffmpegBlobUrl = this._configObj.FfmpegExecutablePath;
        }
        else
        {
            Console.WriteLine("ffmpeg.exe is missing. Please check the Lib folder");
            throw new Exception();
        }

        string videoFilePathCom = videoPath.Split('.')[0] + "_c.mp4";
        ProcessStartInfo processStartInfo = new ProcessStartInfo();
        processStartInfo.WindowStyle = ProcessWindowStyle.Hidden;
        processStartInfo.FileName = ffmpegBlobUrl;
        processStartInfo.Arguments = "-i \"" + videoPath + "\" -vcodec libx264 -n -crf 32 -preset veryfast -vf scale=640:-1 -c:a aac -aq 1 -ac 2 -threads 0 \"" + videoFilePathCom + "\"";
        var process = Process.Start(processStartInfo);
        process.WaitForExit();
        process.Close();
        return videoFilePathCom;
    }

Kód provede následující kroky:

- Kontroluje, ujistěte se, že konfigurace v `App.config` obsahuje všechna potřebná data
- Kontroluje, ujistěte se, `ffmpeg` binární je k dispozici
- Vytvoří název výstupního souboru přidáním `_c.mp4` základní název souboru (například `Example.mp4`  ->  `E>xample_c.mp4`)
- Vytvoří řetězec příkazového řádku k provedení převodu
- Spuštění `ffmpeg` zpracovat pomocí příkazového řádku
- Čeká na video ke zpracování

> [!NOTE]
> Pokud znáte videí už jsou komprimované pomocí H.264 a mít odpovídající dimenze, můžete přepsat `CompressVideo()` přeskočit komprese.

Metoda vrátí název souboru komprimované výstupního souboru.

## <a name="uploading-and-moderating-the-video"></a>Odesílání a moderování videa

Video musí být uložen ve službě Azure Media Services, předtím, než mohou být zpracovány služba moderování obsahu. `Program` Třídy v `Program.cs` má metodu krátký `CreateVideoStreamingRequest()` , která vrací objekt, který reprezentuje streamování požadavek umožňuje nahrát video.

    private static UploadVideoStreamRequest CreateVideoStreamingRequest(string compressedVideoFilePath)
    {
        return
            new UploadVideoStreamRequest
            {
                VideoStream = File.ReadAllBytes(compressedVideoFilePath),
                VideoName = Path.GetFileName(compressedVideoFilePath),
                EncodingRequest = new EncodingRequest()
                {
                    EncodingBitrate = AmsEncoding.AdaptiveStreaming
                },
                VideoFilePath = compressedVideoFilePath
            };
    }

Výsledná `UploadVideoStreamRequest` objekt je definovaný v `UploadVideoStreamRequest.cs` (a jeho nadřazeným prvkem, `UploadVideoRequest`v `UploadVideoRequest.cs`). Tyto třídy nejsou zobrazeny. jsou krátká a slouží pouze k uložení komprimovaných dat videa a informace o něm. Jiné třídy pouze data, `UploadAssetResult` (`UploadAssetResult.cs`) se používá k ukládání výsledků procesu nahrávání. Nyní je možné, abyste pochopili tyto řádky v `ProcessVideo()`:

    UploadVideoStreamRequest uploadVideoStreamRequest = CreateVideoStreamingRequest(compressedVideoPath);
    UploadAssetResult uploadResult = new UploadAssetResult();

    if (generateVtt)
    {
        uploadResult.GenerateVTT = generateVtt;
    }
    Console.WriteLine("\nVideo moderation process started...");

    if (!videoModerator.CreateAzureMediaServicesJobToModerateVideo(uploadVideoStreamRequest, uploadResult))
    {
        Console.ForegroundColor = ConsoleColor.Red;
        Console.WriteLine("Video Review process failed.");
    }

Tyto řádky provádět následující úlohy:

- Vytvoření `UploadVideoStreamRequest` k odeslání komprimované video
- Nastavení žádosti `GenerateVTT` příznak, pokud uživatel požaduje text přepisu
- Volání `CreateAzureMediaServicesJobToModerateVideo()` provádět nahrávání a získání výsledku

## <a name="deep-dive-into-video-moderation"></a>Podrobné informace o moderování videa

Metoda `CreateAzureMediaServicesJobToModerateVideo()` probíhá `VideoModerator.cs`, které obsahuje velkou část kódu, který komunikuje se službou Azure Media Services. Metody zdrojový kód je uveden v následující extrakce.

    public bool CreateAzureMediaServicesJobToModerateVideo(UploadVideoStreamRequest uploadVideoRequest, UploadAssetResult uploadResult)
    {
        asset = CreateAsset(uploadVideoRequest);
        uploadResult.VideoName = uploadVideoRequest.VideoName;
        // Encoding the asset , Moderating the asset, Generating transcript in parallel
        IAsset encodedAsset = null;
        //Creates the job for the tasks.
        IJob job = this._mediaContext.Jobs.Create("AMS Review Job");

        //Adding encoding task to job.
        ConfigureEncodeAssetTask(uploadVideoRequest.EncodingRequest, job);

        ConfigureContentModerationTask(job);

        //adding transcript task to job.
        if (uploadResult.GenerateVTT)
        {
            ConfigureTranscriptTask(job);
        }

        Stopwatch timer = new Stopwatch();
        timer.Start();
        //submit and execute job.
        job.Submit();
        job.GetExecutionProgressTask(new CancellationTokenSource().Token).Wait();
        timer.Stop();
        using (var sw = new StreamWriter(AmsConfigurations.logFilePath, true))
        {
            sw.WriteLine("AMS Job Elapsed Time: {0}", timer.Elapsed);
        }

        if (job.State == JobState.Error)
        {
            throw new Exception("Video moderation has failed due to AMS Job error.");
        }

        UploadAssetResult result = uploadResult;
        encodedAsset = job.OutputMediaAssets[0];
        result.ModeratedJson = GetCmDetail(job.OutputMediaAssets[1]);
        // Check for valid Moderated JSON
        var jsonModerateObject = JsonConvert.DeserializeObject<VideoModerationResult>(result.ModeratedJson);

        if (jsonModerateObject == null)
        {
            return false;
        }
        if (uploadResult.GenerateVTT)
        {
            GenerateTranscript(job.OutputMediaAssets.Last());
        }

        uploadResult.StreamingUrlDetails = PublishAsset(encodedAsset);
        string downloadUrl = GenerateDownloadUrl(asset, uploadVideoRequest.VideoName);
        uploadResult.StreamingUrlDetails.DownloadUri = downloadUrl;
        uploadResult.VideoName = uploadVideoRequest.VideoName;
        uploadResult.VideoFilePath = uploadVideoRequest.VideoFilePath;
        return true;
    }

Tento kód provede následující úlohy:

- Vytvoří úlohu pro zpracování udělat služby AMS
- Přidá úkoly pro kódování souboru videa, moderování je a generuje text přepisu
- Odešle úlohu nahrávání souborů a zahájení zpracování
- Načte výsledky moderování, text přepisu (je-li požadovány) a další informace

## <a name="sample-video-moderation-response"></a>Ukázková odpověď moderování videa

Výsledek úlohy moderování videa (viz [moderování videa quickstart](video-moderation-api.md) je datová struktura JSON obsahující výsledky moderování. Tyto výsledky obsahovat přehled fragmenty (snímky) v rámci video obsahující události (klipy) s použitím klíčových snímků, které byly označeny příznakem ke kontrole. Každý klíčový snímek skóre pravděpodobnosti, že obsahuje obsah pro dospělé nebo pikantní. Následující příklad ukazuje odpověď JSON:

    {
        "version": 2,
        "timescale": 90000,
        "offset": 0,
        "framerate": 50,
        "width": 1280,
        "height": 720,
        "totalDuration": 18696321,
        "fragments": [
        {
            "start": 0,
            "duration": 18000
        },
        {
            "start": 18000,
            "duration": 3600,
            "interval": 3600,
            "events": [
            [
            {
                "reviewRecommended": false,
                "adultScore": 0.00001,
                "racyScore": 0.03077,
                "index": 5,
                "timestamp": 18000,
                "shotIndex": 0
            }
            ]
        ]
        },
        {
            "start": 18386372,
            "duration": 119149,
            "interval": 119149,
            "events": [
            [
            {
                "reviewRecommended": true,
                "adultScore": 0.00000,
                "racyScore": 0.91902,
                "index": 5085,
                "timestamp": 18386372,
                "shotIndex": 62
            }
        ]
        ]
        }
    ]
    }

Přepisu zvuku z videa je také vytvořen, když `GenerateVTT` je nastavený příznak.

> [!NOTE]
> Využívá konzolovou aplikaci [rozhraní API služby Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) ke generování záznamy o studiu z Nahraná videa, zvuková stopa. Výsledky jsou k dispozici ve formátu WebVTT. Další informace v tomto formátu najdete v tématu [webové Video textové stopy formátu](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API).


## <a name="creating-the-human-in-the-loop-review"></a>Vytváření kontroly lidských v the smyčky

Proces moderování vrátí seznam hodnot klíčové snímky z videa, spolu s řádné záznamy o studiu jeho zvukové stopy. Dalším krokem je vytvoření kontrolu v nástroji pro kontrolu Content Moderatoru pro lidské moderátory. Po návratu do `ProcessVideo()` metoda `Program.cs`, viz volání `CreateVideoReviewInContentModerator()` metoda. Tato metoda je v `videoReviewApi` třídy, která je v `VideoReviewAPI.cs`a je znázorněna zde.

    public async Task<string> CreateVideoReviewInContentModerator(UploadAssetResult uploadAssetResult)
    {
    
        string reviewId = string.Empty;
        List<ProcessedFrameDetails> frameEntityList = framegenerator.CreateVideoFrames(uploadAssetResult);
        string path = uploadAssetResult.GenerateVTT == true ? this._amsConfig.FfmpegFramesOutputPath + Path.GetFileNameWithoutExtension (uploadAssetResult.VideoName) + "_aud_SpReco.vtt" : "";
        TranscriptScreenTextResult screenTextResult = new TranscriptScreenTextResult();
        
    if (File.Exists(path))
        {
            screenTextResult = await GenerateTextScreenProfanity(reviewId, path, frameEntityList);
            uploadAssetResult.Category1TextScore = screenTextResult.Category1Score;
            uploadAssetResult.Category2TextScore = screenTextResult.Category2Score;
            uploadAssetResult.Category3TextScore = screenTextResult.Category3Score;
            uploadAssetResult.Category1TextTag = screenTextResult.Category1Tag;
            uploadAssetResult.Category2TextTag = screenTextResult.Category2Tag;
            uploadAssetResult.Category3TextTag = screenTextResult.Category3Tag;
        }
        
        var reviewVideoRequestJson = CreateReviewRequestObject(uploadAssetResult, frameEntityList);
        if (string.IsNullOrWhiteSpace(reviewVideoRequestJson))
        {
            throw new Exception("Video review process failed in CreateVideoReviewInContentModerator");
        }
        
        reviewId = JsonConvert.DeserializeObject<List<string>>(ExecuteCreateReviewApi(reviewVideoRequestJson).Result).FirstOrDefault();
        frameEntityList = framegenerator.GenerateFrameImages(frameEntityList, uploadAssetResult, reviewId);
        await CreateAndPublishReviewInContentModerator(uploadAssetResult, frameEntityList, reviewId, path, screenTextResult);
        return reviewId;
    
    }

`CreateVideoReviewInContentModerator()` volání několik dalších metod, jak provádět následující úlohy:

> [!NOTE]
> Využívá konzolovou aplikaci [FFmpeg](https://ffmpeg.org/) knihovny pro generování miniatur. Tyto miniatury (imagí) odpovídají rámce časová razítka v [moderování videa výstup](#sample-video-moderation-response).

|Úkol|Metody|File|
|-|-|-|
|Extrahovat klíč snímky z videa a vytvoří miniatury obrázků|`CreateVideoFrames()`<br>`GenerateFrameImages()`|`FrameGeneratorServices.cs`|
|Kontrola text přepisu, pokud je k dispozici, vyhledejte pro dospělé nebo pikantního zvuku|`GenerateTextScreenProfanity()`| `VideoReviewAPI.cs`|
|Připraví a odešle žádost videa revize pro lidskou kontrolu|`CreateReviewRequestObject()`<br> `ExecuteCreateReviewApi()`<br>`CreateAndPublishReviewInContentModerator()`|`VideoReviewAPI.cs`|

## <a name="video-review-default-view"></a>Výchozí zobrazení videa revize

Na následujícím obrázku zobrazuje výsledky v předchozích krocích.

![Výchozí zobrazení videa revize](images/video-tutorial-default-view.PNG)

## <a name="transcript-generation"></a>Generování přepisu

Kód uvedený v tomto kurzu má až doteď, zaměřuje na vizuální obsah. Přehled mluveného obsahu je samostatný a volitelné proces, který jak už bylo zmíněno, používá přepisu vygenerovaná zvuku. Je aktuální čas se podívat, jak jsou záznamy o studiu textu vytvořené a použít v procesu kontroly. Úloha generování přepisu spadá [Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-index-content) služby.

Aplikace provádí následující úlohy:

|Úkol|Metody|File|
|-|-|-|
|Určení, zda text záznamy o studiu se mají generovat|`Main()`<br>`GetUserInputs()`|`Program.cs`|
|Pokud ano, odešlete úlohu určené k transkripci jako součást moderování|`ConfigureTranscriptTask()`|`VideoModerator.cs`|
|Získání lokální kopie přepisu|`GenerateTranscript()`|`VideoModerator.cs`|
|Příznak snímky z videa, které obsahují nevhodný zvuku|`GenerateTextScreenProfanity()`<br>`TextScreen()`|`VideoReviewAPI.cs`|
|Přidání výsledků kontroly|`UploadScreenTextResult()`<br>`ExecuteAddTranscriptSupportFile()`|`VideoReviewAPI.cs`|

### <a name="task-configuration"></a>Konfigurace úlohy

Přejděme právo k odeslání úlohy určené k transkripci. `CreateAzureMediaServicesJobToModerateVideo()` (již bylo zmíněno výše) volání `ConfigureTranscriptTask()`.

    private void ConfigureTranscriptTask(IJob job)
    {
        string mediaProcessorName = _amsConfigurations.MediaIndexer2MediaProcessor;
        IMediaProcessor processor = _mediaContext.MediaProcessors.GetLatestMediaProcessorByName(mediaProcessorName);

        string configuration = File.ReadAllText(_amsConfigurations.MediaIndexerConfigurationJson);
        ITask task = job.Tasks.AddNew("AudioIndexing Task", processor, configuration, TaskOptions.None);
        task.InputAssets.Add(asset);
        task.OutputAssets.AddNew("AudioIndexing Output Asset", AssetCreationOptions.None);
    }

Konfigurace přepisu úkolu je pro čtení ze souboru `MediaIndexerConfig.json` v rámci řešení `Lib` složky. Prostředky pro AMS jsou vytvořeny pro konfigurační soubor a výstupem procesu určené k transkripci. Po spuštění úlohy AMS, tato úloha vytváří přepisu text ze souboru videa, zvuková stopa.

> [!NOTE]
> Ukázková aplikace rozpozná pouze řeči v Americkou angličtinu.

### <a name="transcript-generation"></a>Generování přepisu

Přepisu je publikován jako prostředek AMS. Kontrola přepis pro tvorbu problematický obsah aplikace stáhne prostředku ze služby Azure Media Services. `CreateAzureMediaServicesJobToModerateVideo()` volání `GenerateTranscript()`, jak je znázorněno zde načíst soubor.

    public bool GenerateTranscript(IAsset asset)
    {
        try
        {
            var outputFolder = this._amsConfigurations.FfmpegFramesOutputPath;
            IAsset outputAsset = asset;
            IAccessPolicy policy = null;
            ILocator locator = null;
            policy = _mediaContext.AccessPolicies.Create("My 30 days readonly policy", TimeSpan.FromDays(360), AccessPermissions.Read);
            locator = _mediaContext.Locators.CreateLocator(LocatorType.Sas, outputAsset, policy, DateTime.UtcNow.AddMinutes(-5));
            DownloadAssetToLocal(outputAsset, outputFolder);
            locator.Delete();
            return true;
        }
        catch
        {   //TODO:  Logging
            Console.WriteLine("Exception occured while generating index for video.");
            throw;
        }
    }

Po dokončení instalace některých nezbytné AMS vlastní stahování se provádí pomocí volání `DownloadAssetToLocal()`, obecné funkce, která zkopíruje prostředek AMS do místního souboru.

## <a name="transcript-moderation"></a>Moderování přepisu

S přepisu vždy po ruce je zkontrolovat a použít v revizi. Vytváření kontroly je působnosti `CreateVideoReviewInContentModerator()`, který volá `GenerateTextScreenProfanity()` provedete úlohy. Tato metoda volá `TextScreen()`, který obsahuje většinu funkcí. 

`TextScreen()` provádí následující úlohy:

- Analýzy přepisu dobu tamps a popisky
- Odešlete každý popisek pro moderování textu
- Příznak jakékoli snímky, které mohou mít nežádoucí mluveného obsahu

Podívejme se na každou podrobněji tyto úlohy:

### <a name="initialize-the-code"></a>Inicializace kódu

Nejprve inicializujte všechny proměnné a kolekce.

    private async Task<TranscriptScreenTextResult> TextScreen(string filepath, List<ProcessedFrameDetails> frameEntityList)
    {
        List<TranscriptProfanity> profanityList = new List<TranscriptProfanity>();
        string responseContent = string.Empty;
        HttpResponseMessage response;
        bool category1Tag = false;
        bool category2Tag = false;
        bool category3Tag = false;
        double category1Score = 0;
        double category2Score = 0;
        double category3Score = 0;
        List<string> vttLines = File.ReadAllLines(filepath).Where(line => !line.Contains("NOTE Confidence:") && line.Length > 0).ToList();
        StringBuilder sb = new StringBuilder();
        List<CaptionScreentextResult> csrList = new List<CaptionScreentextResult>();
        CaptionScreentextResult captionScreentextResult = new CaptionScreentextResult() { Captions = new List<string>() };

        // Code from the next sections in the tutorial
    

### <a name="parse-the-transcript-for-captions"></a>Analyzovat přepis pro tvorbu titulků

Dále analyzovat VTT formátovaný přepis pro tvorbu titulků a časová razítka. Nástroj pro recenze zobrazí tyto popisky v přepisu kartu na obrazovce revize videa. Časová razítka se používají k synchronizaci titulků s odpovídající snímky videí.

        // Code from the previous section(s) in the tutorial

        //
        // Parse the transcript
        //
        foreach (var line in vttLines.Skip(1))
        {
                if (line.Contains("-->"))
                {
                    if (sb.Length > 0)
                    {
                        captionScreentextResult.Captions.Add(sb.ToString());
                        sb.Clear();
                    }
                    if (captionScreentextResult.Captions.Count > 0)
                    {
                        csrList.Add(captionScreentextResult);
                        captionScreentextResult = new CaptionScreentextResult() { Captions = new List<string>() };
                    }
                    string[] times = line.Split(new string[] { "-->" }, StringSplitOptions.RemoveEmptyEntries);
                    string startTimeString = times[0].Trim();
                    string endTimeString = times[1].Trim();
                    int startTime = (int)TimeSpan.ParseExact(startTimeString, @"hh\:mm\:ss\.fff", CultureInfo.InvariantCulture).TotalMilliseconds;
                    int endTime = (int)TimeSpan.ParseExact(endTimeString, @"hh\:mm\:ss\.fff", CultureInfo.InvariantCulture).TotalMilliseconds;
                    captionScreentextResult.StartTime = startTime;
                    captionScreentextResult.EndTime = endTime;
                }
                else
                {
                    sb.Append(line);
                }
                if (sb.Length + line.Length > 1024)
                {
                    captionScreentextResult.Captions.Add(sb.ToString());
                    sb.Clear();
                }
            }
            if (sb.Length > 0)
            {
                captionScreentextResult.Captions.Add(sb.ToString());
            }
            if (captionScreentextResult.Captions.Count > 0)
            {
                csrList.Add(captionScreentextResult);
            }

            // Code from the following section in the quickstart

### <a name="moderate-captions-with-the-text-moderation-service"></a>Střední popisků ve službě moderování textu

Dále jsme kontrolovat parsovaného textu popisků textem Content Moderator API.

> [!NOTE]
> Klíč služby Content Moderator má požadavků za druhé omezení četnosti (předávajících stran). Při překročení limitu, sada SDK dojde k výjimce s kódem chyby 429. 
>
> Klíč úroveň free má omezení četnosti jeden RPS.

    //
    // Moderate the captions or cues
    //
    int waitTime = 1000;
    foreach (var csr in csrList)
    {
                bool captionAdultTextTag = false;
                bool captionRacyTextTag = false;
                bool captionOffensiveTextTag = false;
                bool retry = true;

                foreach (var caption in csr.Captions)
                {
                    while (retry)
                    {
                        try
                        {
                            System.Threading.Thread.Sleep(waitTime);
                            var lang = await CMClient.TextModeration.DetectLanguageAsync("text/plain", caption);
                            var oRes = await CMClient.TextModeration.ScreenTextWithHttpMessagesAsync(lang.DetectedLanguageProperty, "text/plain", caption, null, null, null, true);
                            response = oRes.Response;
                            responseContent = await response.Content.ReadAsStringAsync();
                            retry = false;
                        }
                        catch (Exception e)
                        {
                            if (e.Message.Contains("429"))
                            {
                                Console.WriteLine($"Moderation API call failed. Message: {e.Message}");
                                waitTime = (int)(waitTime * 1.5);
                                Console.WriteLine($"wait time: {waitTime}");
                            }
                            else
                            {
                                retry = false;
                                Console.WriteLine($"Moderation API call failed. Message: {e.Message}");
                            }
                        }
                    }
                    var jsonTextScreen = JsonConvert.DeserializeObject<TextScreen>(responseContent);
                    if (jsonTextScreen != null)
                    {
                        TranscriptProfanity transcriptProfanity = new TranscriptProfanity();
                        transcriptProfanity.TimeStamp = "";
                        List<Terms> transcriptTerm = new List<Terms>();
                        if (jsonTextScreen.Terms != null)
                        {
                            foreach (var term in jsonTextScreen.Terms)
                            {
                                var profanityobject = new Terms
                                {
                                    Term = term.Term,
                                    Index = term.Index
                                };
                                transcriptTerm.Add(profanityobject);
                            }
                            transcriptProfanity.Terms = transcriptTerm;
                            profanityList.Add(transcriptProfanity);
                        }
                        if (jsonTextScreen.Classification.Category1.Score > _amsConfig.Category1TextThreshold) captionAdultTextTag = true;
                        if (jsonTextScreen.Classification.Category2.Score > _amsConfig.Category2TextThreshold) captionRacyTextTag = true;
                        if (jsonTextScreen.Classification.Category3.Score > _amsConfig.Category3TextThreshold) captionOffensiveTextTag = true;
                        if (jsonTextScreen.Classification.Category1.Score > _amsConfig.Category1TextThreshold) category1Tag = true;
                        if (jsonTextScreen.Classification.Category2.Score > _amsConfig.Category2TextThreshold) category2Tag = true;
                        if (jsonTextScreen.Classification.Category3.Score > _amsConfig.Category3TextThreshold) category3Tag = true;
                        category1Score = jsonTextScreen.Classification.Category1.Score > category1Score ? jsonTextScreen.Classification.Category1.Score : category1Score;
                        category2Score = jsonTextScreen.Classification.Category2.Score > category2Score ? jsonTextScreen.Classification.Category2.Score : category2Score;
                        category3Score = jsonTextScreen.Classification.Category3.Score > category3Score ? jsonTextScreen.Classification.Category3.Score : category3Score;
                    }
                    foreach (var frame in frameEntityList.Where(x => x.TimeStamp >= csr.StartTime && x.TimeStamp <= csr.EndTime))
                    {
                        frame.IsAdultTextContent = captionAdultTextTag;
                        frame.IsRacyTextContent = captionRacyTextTag;
                        frame.IsOffensiveTextContent = captionOffensiveTextTag;
                    }
                }
            }
            TranscriptScreenTextResult screenTextResult = new TranscriptScreenTextResult()
            {
                TranscriptProfanity = profanityList,
                Category1Tag = category1Tag,
                Category2Tag = category2Tag,
                Category3Tag = category3Tag,
                Category1Score = category1Score,
                Category2Score = category2Score,
                Category3Score = category3Score
            };
            return screenTextResult;
    }

### <a name="breaking-down-the-text-moderation-step"></a>Popis vnitřních principů krok moderování textu

`TextScreen()` Pojďme to ujasnit představuje metodu podstatné.

1. Metoda nejprve načte soubor přepisu řádek po řádku. Ignoruje prázdné řádky a řádky, které obsahují `NOTE` s skóre spolehlivosti. Extrahuje časová razítka a textu položky z *pomůcky* v souboru. Hromádka představuje text z zvukové stopy a zahrnuje počátečním a koncovým časem. Hromádka začíná na časové ose razítko s řetězcem `-->`. Následuje jedna nebo více řádků textu.

1. Instance `CaptionScreentextResult` (definované v `TranscriptProfanity.cs`) se používají k uložení informace získá analýzou z každé upozornění.  Když se zjistí nové razítko časovou osu, nebo je dosaženo o maximální délce 1024 znaků., nový `CaptionScreentextResult` se přidá do `csrList`. 

1. Metoda odešle vedle jednotlivých upozornění do rozhraní API pro moderování textu. Obě volá `ContentModeratorClient.TextModeration.DetectLanguageAsync()` a `ContentModeratorClient.TextModeration.ScreenTextWithHttpMessagesAsync()`, které jsou definovány v `Microsoft.Azure.CognitiveServices.ContentModerator` sestavení. Pokud chcete vyhnout, je míra časově omezené, metody pozastaví sekundy před odesláním každé upozornění.

1. Po přijetí výsledky ze služby moderování textu, metoda pak analyzuje, je chcete zobrazit, jestli splňují spolehlivosti prahové hodnoty. Tyto hodnoty jsou nastavené v `App.config` jako `OffensiveTextThreshold`, `RacyTextThreshold`, a `AdultTextThreshold`. Nakonec jsou také uloženy problematický podmínky sami. Všechny bloky v rámci upozornění na časový rozsah se označí jako obsahující urážlivých, pikantní nebo dospělého text.

1. `TextScreen()` Vrátí `TranscriptScreenTextResult` instance, která obsahuje výsledek moderování textu z videa jako celek. Tento objekt obsahuje příznaky a stanoví skóre pro různé typy problematický obsah spolu se seznam všech problematický podmínky. Volající, `CreateVideoReviewInContentModerator()`, volání `UploadScreenTextResult()` připojit tyto informace k revizi, tak, aby byl k dispozici pro lidské revidující.
 
## <a name="video-review-transcript-view"></a>Zobrazit přepis videa revize

Na následujícím obrázku zobrazuje výsledek přepisu postup generování a moderování.

![Zobrazit přepis moderování videa](images/video-tutorial-transcript-view.PNG)

## <a name="program-output"></a>Výstup programu

Následující příkazový řádek výstup z programu ukazuje různé úlohy, jako jsou dokončené. Kromě toho jsou k dispozici ve stejném adresáři jako původní videosoubory moderování výsledek (ve formátu JSON) a přepis řeči.

    Microsoft.ContentModerator.AMSComponentClient
    Enter the fully qualified local path for Uploading the video :
    "Your File Name.MP4"
    Generate Video Transcript? [y/n] : y
    
    Video compression process started...
    Video compression process completed...
    
    Video moderation process started...
    Video moderation process completed...
    
    Video review process started...
    Video Frames Creation inprogress...
    Frames(83) created successfully.
    Review Created Successfully and the review Id 201801va8ec2108d6e043229ba7a9e6373edec5
    Video review successfully completed...
    
    Total Elapsed Time: 00:05:56.8420355


## <a name="next-steps"></a>Další postup

[Stáhněte si řešení sady Visual Studio](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) ukázkové soubory a požadované knihovny pro účely tohoto kurzu a začít používat svoji integraci.
