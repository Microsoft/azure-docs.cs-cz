---
title: 'Kurz: Moderování videa a záznamy o studiu v rozhraní .NET – Content Moderator'
titlesuffix: Azure Cognitive Services
description: Tento kurz vám pomůže porozumět jak sestavit kompletní řešení moderování videa a přepis moderování s podporou počítače a vytvoření revize lidských v the smyčky.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: tutorial
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 45f1b4d16bd982e17b67d6fb337e0d917f635851
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54332689"
---
# <a name="tutorial-video-and-transcript-moderation"></a>Kurz: Moderování videa a přepis

Rozhraní API pro video v Content Moderatoru umožňují moderování videí a vytváření kontrol videí v nástroji pro kontroly prováděné člověkem. 

Tento kurz vám pomůže porozumět jak sestavit kompletní řešení moderování videa a přepis moderování s podporou počítače a vytvoření revize lidských v the smyčky.

Pro účely tohoto kurzu si stáhněte [konzolovou aplikaci C#](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp). Konzolová aplikace s pomocí sady SDK a souvisejících balíčků provádí následující úlohy:

- Komprese vstupních videí pro rychlejší zpracování
- Moderování videa k získání snímků s poznatky
- Použití časových razítek snímků k vytvoření miniatur (obrázků)
- Odeslání časových razítek a miniatur k vytvoření kontrol videa
- Převod řeči ve videu na text (přepis) pomocí rozhraní API funkce Media Indexer
- Moderování přepisu pomocí služby moderování textu
- Přidání moderovaného přepisu do kontroly videa

## <a name="sample-program-outputs"></a>Ukázky výstupů programů

Než budete pokračovat, Podívejme se na následující ukázka výstupy z programu:

- [Výstup konzoly](#program-output)
- [Kontrola videa](#video-review-default-view)
- [Zobrazení přepisu](#video-review-transcript-view)

## <a name="prerequisites"></a>Požadavky

1. Zaregistrujte se na webu [Nástroje pro kontrolu Content Moderatoru](https://contentmoderator.cognitive.microsoft.com/) a [vytvořte vlastní značky](Review-Tool-User-Guide/tags.md), které přiřadí konzolová aplikace C# v rámci kódu. Na následující obrazovce vidíte vlastní značky.

  ![Vlastní značky moderování videa](images/video-tutorial-custom-tags.png)

1. Pokud chcete spustit ukázku aplikace, budete potřebovat účet Azure a účet Azure Media Services. Kromě toho potřebujete přístup k privátní verzi preview Content Moderatoru. A nakonec budete potřebovat přihlašovací údaje k ověření v Azure Active Directory. Podrobnosti o získání těchto informací najdete v tématu [Rychlý start rozhraní API pro moderování videa](video-moderation-api.md).

1. Upravte soubor `App.config` a přidejte název tenanta Active Directory, koncové body služby a klíče předplatného označené `#####`. Potřebujete tyto informace:

|Klíč|Popis|
|-|-|
|`AzureMediaServiceRestApiEndpoint`|Koncový bod pro rozhraní API služby Azure Media Services (AMS)|
|`ClientSecret`|Klíč předplatného pro Azure Media Services|
|`ClientId`|ID klienta pro Azure Media Services|
|`AzureAdTenantName`|Název tenanta Active Directory zastupujícího vaši organizaci|
|`ContentModeratorReviewApiSubscriptionKey`|Klíč předplatného pro rozhraní API pro kontrolu v Content Moderatoru|
|`ContentModeratorApiEndpoint`|Další informace o rozhraní API Content Moderatoru|
|`ContentModeratorTeamId`|Vytvoření týmového ID Content Moderatoru|

## <a name="getting-started"></a>Začínáme

Třída `Program` v souboru `Program.cs` je hlavní vstupní bod do aplikace moderování videa.

### <a name="methods-of-class-program"></a>Metody třídy Program

|Metoda|Popis|
|-|-|
|`Main`|Analyzuje příkazový řádek, shromáždí vstup uživatele a spustí zpracování.|
|`ProcessVideo`|Zkomprimuje, nahraje, moderuje a vytvoří kontroly videa.|
|`CreateVideoStreamingRequest`|Vytvoří datový proud pro nahrání videa.|
|`GetUserInputs`|Shromáždí vstup uživatele. Používá se, když nejsou k dispozici žádné možnosti příkazového řádku.|
|`Initialize`|Inicializuje objekty potřebné pro proces moderování.|

### <a name="the-main-method"></a>Metoda Main

`Main()` je místo, kde se zahajuje spuštění, je to tedy místo, kde je dobré začít s vysvětlením procesu moderování videa.

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

`Main()` zpracuje následující argumenty příkazového řádku:

- Cesta k adresáři obsahujícímu videosoubory MPEG-4 k odeslání pro moderování. Všechny soubory `*.mp4` v tomto adresáři a v jeho podadresářích se odešlou k moderování.
- Volitelně můžete použít příznak logické hodnoty (true/false) určující, jestli se mají pro účely moderování zvuku generovat přepisy textu.

Pokud nejsou přítomné žádné argumenty příkazového řádku, `Main()` zavolá metodu `GetUserInputs()`. Tato metoda vyzve uživatele, aby zadal cestu k jedinému souboru videa a určil, jestli se má vygenerovat přepis textu.

> [!NOTE]
> Konzolová aplikace používá [rozhraní API služby Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) ke generování přepisů z nahrané zvukové stopy videa. Výsledky jsou k dispozici ve formátu WebVTT. Další informace o tomto formátu najdete v tématu [Formáty sledování textu webového videa](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API).

### <a name="initialize-and-processvideo-methods"></a>Metody Initialize a ProcessVideo

Bez ohledu na to, jestli pochází možnosti programu z příkazového řádku nebo z interaktivního uživatelského vstupu, zavolá dále `Main()` metodu `Initialize()` k vytvoření následujících instancí:

|Třída|Popis|
|-|-|
|`AMSComponent`|Komprese videosouborů před jejich odesláním pro moderování|
|`AMSconfigurations`|Převod na rozhraní konfiguračních dat aplikace, která se nachází v souboru `App.config`|
|`VideoModerator`| Nahrávání, kódování, šifrování a moderování pomocí AMS SDK|
|`VideoReviewApi`|Správa kontrol videa ve službě Content Moderator|

Tyto třídy (s výjimkou `AMSConfigurations`, která je jednoduchá) jsou podrobněji popsané v následujících částech tohoto kurzu.

Nakonec se videosoubory postupně zpracují tak, že se pro každý z nich zavolá `ProcessVideo()`.

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


Metoda `ProcessVideo()` je poměrně jednoduchá. V uvedeném pořadí provádí následující operace:

- Komprimuje video.
- Nahraje video do prostředku Azure Media Services.
- Vytvoří úlohu AMS pro moderování videa.
- Vytvoří kontrolu videa v Content Moderatoru.

V následujících částech probereme podrobněji některé z jednotlivých procesů vyvolaných metodou `ProcessVideo()`. 

## <a name="compressing-the-video"></a>Komprese videa

Aby se minimalizoval síťový provoz, převede aplikace videosoubory na formát H.264 (MPEG-4 AVC) a škáluje je na maximální šířku 640 pixelů. Doporučujeme použít kodek H.264, protože je velmi výkonný (rychlost komprese). Komprese se provádí pomocí bezplatného nástroje příkazového řádku `ffmpeg`, který je součástí řešení sady Visual Studio ve složce `Lib`. Vstupní soubory můžou mít kterýkoliv z formátů, které podporuje `ffmpeg`, včetně nejčastěji používaných formátů a kodeků videosouborů.

> [!NOTE]
> Když spouštíte program pomocí možností příkazového řádku, zadáváte adresář obsahující videosoubory k odeslání pro moderování. Zpracují se všechny soubory v tomto adresáři, které mají příponu názvu souboru `.mp4`. Pokud chcete zpracovat další přípony názvů souborů, aktualizujte metodu `Main()` v souboru `Program.cs` tak, aby zahrnovala požadované přípony.

Kód, který komprimuje jediný soubor videa, je třída `AmsComponent` v souboru `AMSComponent.cs`. Metoda, která odpovídá za tuto funkci, je tady uvedená metoda `CompressVideo()`.

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

- Zkontroluje, že konfigurace v souboru `App.config` obsahuje všechna nutná data.
- Zkontroluje, že je přítomný binární soubor `ffmpeg`.
- Vytvoří název výstupního souboru přidáním `_c.mp4` k základnímu názvu souboru (například `Example.mp4` -> `Example_c.mp4`).
- Vytvoří řetězec příkazového řádku k provedení konverze.
- S použitím příkazového řádku spustí proces `ffmpeg`.
- Čeká na video ke zpracování.

> [!NOTE]
> Pokud víte, že už jsou videa komprimovaná pomocí H.264 a že mají odpovídající velikosti, můžete `CompressVideo()` přepsat a kompresi přeskočit.

Metoda vrátí název komprimovaného výstupního souboru.

## <a name="uploading-and-moderating-the-video"></a>Odeslání a moderování videa

Než bude možné video zpracovat ve službě Content Moderation, musí být uložené ve službě Azure Media Services. Třída `Program` v souboru `Program.cs` má krátkou metodu `CreateVideoStreamingRequest()`, která vrací objekt, který reprezentuje žádost o streamování sloužící k nahrání videa.

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

Výsledný objekt `UploadVideoStreamRequest` je definovaný v souboru `UploadVideoStreamRequest.cs` (a jeho nadřazený objekt `UploadVideoRequest` v souboru `UploadVideoRequest.cs`). Tyto třídy tady nevidíte. Jsou krátké a slouží pouze k uložení komprimovaných dat videa a informací o něm. Jiná pouze datová třída `UploadAssetResult` (`UploadAssetResult.cs`) se používá k ukládání výsledků procesu nahrávání. Teď je možné vysvětlit tyto řádky v metodě `ProcessVideo()`:

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

Tyto řádky provedou následující úlohy:

- Vytvoří `UploadVideoStreamRequest` k odeslání komprimovaného videa.
- Pokud uživatel požaduje přepis textu, nastaví příznak žádosti `GenerateVTT`.
- Zavolá metodu `CreateAzureMediaServicesJobToModerateVideo()`, aby se nahrál a získal výsledek.

## <a name="deep-dive-into-video-moderation"></a>Podrobné informace o moderování videa

Metoda `CreateAzureMediaServicesJobToModerateVideo()` je v souboru `VideoModerator.cs`, kde je velká část kódu, který komunikuje se službou Azure Media Services. Zdrojový kód metody můžete vidět v následujícím výtažku.

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

Tento kód provádí následující úlohy:

- Vytvoří úlohu AMS pro zpracování, které se má provést.
- Přidá úlohy pro kódování souboru videa, jeho moderování a generování přepisu textu.
- Odešle úlohu, nahraje soubor a zahájí zpracování.
- Načte výsledky moderování, text přepisu (pokud se požaduje) a další informace.

## <a name="sample-video-moderation-response"></a>Ukázka odpovědi moderování videa

Výsledek úlohy moderování videa (viz [rychlý start moderování videa](video-moderation-api.md) je datová struktura JSON obsahující výsledky moderování. V těchto výsledcích vidíte v rámci videa rozdělení do fragmentů (snímků), z nichž každý obsahuje události (klipy) s klíčovými snímky označenými příznakem pro kontrolu. Každý klíčový snímek má skóre označující pravděpodobnost, že je na něm obsah pro dospělé nebo pikantní obsah. Následující příklad ukazuje odpověď JSON:

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

Přepis zvuku z videa se vytvoří také při nastavení příznaku `GenerateVTT`.

> [!NOTE]
> Konzolová aplikace používá [rozhraní API služby Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) ke generování přepisů z nahrané zvukové stopy videa. Výsledky jsou k dispozici ve formátu WebVTT. Další informace o tomto formátu najdete v tématu [Formáty sledování textu webového videa](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API).


## <a name="creating-the-human-in-the-loop-review"></a>Vytvoření kontroly HITL (human-in-the-loop)

Proces moderování vrátí seznam klíčových snímků z videa společně s přepisem zvukových stop. Dalším krokem je vytvoření kontroly v Nástroji pro kontrolu Content Moderatoru pro moderování prováděné lidmi. Po návratu k metodě `ProcessVideo()` v souboru `Program.cs` vidíte, že se volá metoda `CreateVideoReviewInContentModerator()`. Tato metoda je ve třídě `videoReviewApi`, která je v souboru `VideoReviewAPI.cs`, a je ukázaná tady.

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

`CreateVideoReviewInContentModerator()` volá několik dalších metod k provedení následujících úloh:

> [!NOTE]
> Konzolová aplikace použije knihovnu [FFmpeg](https://ffmpeg.org/) ke generování miniatur. Tyto miniatury (obrázky) odpovídají časovým razítkům snímků ve [výstupu moderování videa](#sample-video-moderation-response).

|Úkol|Metody|File|
|-|-|-|
|Extrakce klíčových snímků z videa a vytvoření miniatur z těchto snímků|`CreateVideoFrames()`<br>`GenerateFrameImages()`|`FrameGeneratorServices.cs`|
|Kontrola přepisu textu, pokud je k dispozici, pro účely vyhledání obsahu pro dospělé nebo pikantního obsahu|`GenerateTextScreenProfanity()`| `VideoReviewAPI.cs`|
|Příprava a odeslání žádosti o kontrolu videa pro kontrolu člověkem|`CreateReviewRequestObject()`<br> `ExecuteCreateReviewApi()`<br>`CreateAndPublishReviewInContentModerator()`|`VideoReviewAPI.cs`|

## <a name="video-review-default-view"></a>Výchozí zobrazení kontroly videa

Na následujícím obrázku vidíte výsledky předchozích kroků.

![Výchozí zobrazení kontroly videa](images/video-tutorial-default-view.PNG)

## <a name="transcript-generation"></a>Generování přepisu

Dosud se kód uvedený v tomto kurzu zaměřoval na vizuální obsah. Kontrola mluveného obsahu je samostatný a volitelný proces, který (jak už bylo zmíněno) používá přepis vygenerovaný ze zvuku. Je čas podívat se, jak se vytváří přepisy textu a jak se používají při procesu kontroly. Úloha generování přepisu spadá pod službu [Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-index-content).

Aplikace provádí následující úlohy:

|Úkol|Metody|File|
|-|-|-|
|Určení, jestli se mají generovat přepisy textu|`Main()`<br>`GetUserInputs()`|`Program.cs`|
|Pokud ano, odeslání úlohy přepisu jako součásti moderování|`ConfigureTranscriptTask()`|`VideoModerator.cs`|
|Získání místní kopie přepisu|`GenerateTranscript()`|`VideoModerator.cs`|
|Označení snímků z videa, které obsahují nevhodný zvukový záznam příznakem|`GenerateTextScreenProfanity()`<br>`TextScreen()`|`VideoReviewAPI.cs`|
|Přidání výsledků do kontroly|`UploadScreenTextResult()`<br>`ExecuteAddTranscriptSupportFile()`|`VideoReviewAPI.cs`|

### <a name="task-configuration"></a>Konfigurace úlohy

Přejděme rovnou k odeslání úlohy přepisu. Metoda `CreateAzureMediaServicesJobToModerateVideo()` (popsaná výše) zavolá `ConfigureTranscriptTask()`.

    private void ConfigureTranscriptTask(IJob job)
    {
        string mediaProcessorName = _amsConfigurations.MediaIndexer2MediaProcessor;
        IMediaProcessor processor = _mediaContext.MediaProcessors.GetLatestMediaProcessorByName(mediaProcessorName);

        string configuration = File.ReadAllText(_amsConfigurations.MediaIndexerConfigurationJson);
        ITask task = job.Tasks.AddNew("AudioIndexing Task", processor, configuration, TaskOptions.None);
        task.InputAssets.Add(asset);
        task.OutputAssets.AddNew("AudioIndexing Output Asset", AssetCreationOptions.None);
    }

Konfigurace úlohy přepisu se přečte ze souboru `MediaIndexerConfig.json` v rámci složky `Lib` řešení. Vytvoří se prostředky AMS pro konfigurační soubor a pro výstup procesu přepisu. Úloha AMS vytvoří po spuštění přepis textu ze zvukové stopy videosouboru.

> [!NOTE]
> Ukázková aplikace rozpoznává řeč jenom v americké angličtině.

### <a name="transcript-generation"></a>Generování přepisu

Přepis se publikuje jako prostředek AMS. Pokud chcete prohledat přepis, jestli neobsahuje nevhodný obsah, stáhne aplikace tento prostředek ze služby Azure Media Services. `CreateAzureMediaServicesJobToModerateVideo()` zavolá `GenerateTranscript()`, jak vidíte tady, aby se načetl soubor.

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
            Console.WriteLine("Exception occurred while generating index for video.");
            throw;
        }
    }

Po dokončení některých nezbytných nastavení prostředku AMS se provede vlastní stažení na základě volání `DownloadAssetToLocal()`, obecné funkce, která zkopíruje prostředek AMS do místního souboru.

## <a name="transcript-moderation"></a>Moderování přepisu

S přepisem po ruce, se prohledá a použije při kontrole. Vytvoření kontroly spadá pod metodu `CreateVideoReviewInContentModerator()`, která k provedení úlohy zavolá `GenerateTextScreenProfanity()`. Tato metoda zase dál volá metodu `TextScreen()`, která obsahuje většinu funkcí. 

`TextScreen()` provede následující úlohy:

- Analýza přepisu, která zjistí časová razítka a titulky
- Odeslání jednotlivých titulků k moderování textu
- Použití příznaku u všech snímků, které můžou obsahovat nežádoucí mluvený obsah

Podívejme se na jednotlivé úlohy podrobněji:

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
    

### <a name="parse-the-transcript-for-captions"></a>Analýza přepisu, která zjišťuje titulky

Dále analyzujte přepis ve formátu WebVTT, jestli obsahuje titulky a časová razítka. Nástroj pro kontrolu zobrazí tyto titulky na kartě přepisu na obrazovce kontroly videa. Časová razítka se používají k synchronizaci titulků s odpovídajícími snímky videí.

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

### <a name="moderate-captions-with-the-text-moderation-service"></a>Moderování titulků pomocí služby moderování textu

Dále zkontrolujeme analyzované textové titulky pomocí rozhraní API pro text v Content Moderatoru.

> [!NOTE]
> Klíč služby Content Moderator má omezenou rychlost v jednotkách RPS (žádosti za sekundu). Když tento limit překročíte, sada SDK vyvolá výjimku s kódem chyby 429. 
>
> Klíč úrovně Free má limit nastavený na 1 RPS za sekundu.

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

### <a name="breaking-down-the-text-moderation-step"></a>Rozbor kroku moderování textu

`TextScreen()` je důležitá metoda, pojďme si ji tedy rozebrat.

1. Nejdříve tato metoda načte po jednotlivých řádcích soubor přepisu. Ignoruje prázdné řádky a řádky, které obsahují `NOTE` se skóre spolehlivosti. Z *upozornění* v souboru extrahuje časová razítka a textové položky. Upozornění představuje text ze zvukové stopy a zahrnuje počáteční a koncový čas. Upozornění začíná řádkem časového razítka s řetězcem `-->`. Pak následuje jeden nebo více řádků textu.

1. Instance `CaptionScreentextResult` (definované v souboru `TranscriptProfanity.cs`) se používají k uložení informací analyzovaných z jednotlivých upozornění.  Při zjištění řádku nového časového razítka nebo při dosažení maximální délky textu 1024 znaků se do seznamu `csrList` přidá nový výsledek `CaptionScreentextResult`. 

1. Tato metoda dále odešle jednotlivá upozornění do rozhraní API služby Moderování textu. Zavolá `ContentModeratorClient.TextModeration.DetectLanguageAsync()` i `ContentModeratorClient.TextModeration.ScreenTextWithHttpMessagesAsync()` definované v sestavení `Microsoft.Azure.CognitiveServices.ContentModerator`. Aby se neomezila rychlost, udělá metoda před odesláním jednotlivých upozornění sekundovou pauzu.

1. Metoda po přijetí výsledků ze služby Moderování textu tyto výsledky analyzuje a zjišťuje, jestli vyhovují limitům nastaveným pro důvěryhodnost. Tyto hodnoty jsou určené v souboru `App.config` jako `OffensiveTextThreshold`, `RacyTextThreshold` a `AdultTextThreshold`. Nakonec se uloží také samotné problematické termíny. Všechny snímky v rámci časového rozsahu upozornění se označí příznakem, který ukazuje na to, že obsahují urážlivý nebo pikantní text nebo text pro dospělé.

1. `TextScreen()` vrátí instanci `TranscriptScreenTextResult`, která obsahuje výsledek moderování textu z videa jako celku. Tento objekt obsahuje příznaky a skóre pro různé typy problematického obsahu spolu se seznamem všech problematických termínů. Volající metoda `CreateVideoReviewInContentModerator()` zavolá metodu `UploadScreenTextResult()`, aby se tyto informace připojily ke kontrole a měli je k dispozici ti, kdo dělají kontrolu.
 
## <a name="video-review-transcript-view"></a>Zobrazení přepisu kontroly videa

Na následujícím obrázku vidíte výsledek generování přepisu a postupu moderování.

![Zobrazení přepisu moderování videa](images/video-tutorial-transcript-view.PNG)

## <a name="program-output"></a>Výstup programu

V následujícím výstupu příkazového řádku z programu vidíte různé úlohy v průběhu jejich dokončení. Kromě toho jsou k dispozici ve stejném adresáři jako původní videosoubory výsledky moderování (ve formátu JSON) a přepis řeči.

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

[Stáhněte si řešení sady Visual Studio](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) a ukázkové soubory a požadované knihovny k tomuto kurzu a zahajte integraci.
