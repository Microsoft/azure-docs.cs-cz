---
title: Azure obsahu moderátora – střední videa a přepisy v rozhraní .NET | Microsoft Docs
description: Jak používat obsahu moderátora na střední videa a přepisy v rozhraní .NET.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 1/27/2018
ms.author: sajagtap
ms.openlocfilehash: a084b50e44fe26ba2547d0f7b7ed184fb71b190c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "35342745"
---
# <a name="video-and-transcript-moderation-tutorial"></a>Kurz přerušování video a přepis

Grafické rozhraní API obsahu moderátora umožňují střední videa a vytvořit video recenze v nástroji lidského revize. 

Tento podrobný kurz pomáhá pochopit, jak k vytvoření kompletního řešení přerušování video a přepis s přerušování s asistencí počítače a vytváření zkontrolujte lidské v the smyčky.

Stažení [konzolovou aplikaci C#](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) pro účely tohoto kurzu. Konzolové aplikace používá SDK a související balíčky k provádění následujících úloh:

- Komprimovat vstupní video(s) pro rychlejší zpracování
- Střední video snímky a rámce s insights
- Časová razítka rámce použít k vytvoření miniatur (imagí)
- Odeslání časová razítka a miniatury vytvořit video recenze
- Převést video řeči na text (přepis) s rozhraním API Indexer média
- Střední přepis přerušování službou textu
- Přidat moderované přepis ke kontrole video

## <a name="sample-program-outputs"></a>Ukázka programu výstupy

Než budete pokračovat, podíváme se na toto ukázkové výstupy programu:

- [Výstup konzoly](#program-output)
- [Zkontrolujte video](#video-review-default-view)
- [Přepis zobrazení](#video-review-transcript-view)

## <a name="prerequisites"></a>Požadavky

1. Zaregistrujte si [obsahu moderátora Zkontrolujte nástroj](https://contentmoderator.cognitive.microsoft.com/) webu a [vytvářet vlastní značky](Review-Tool-User-Guide/tags.md) který konzolovou aplikaci C# přiřazuje z v kódu. Následující obrazovka ukazuje vlastní značky.

  ![Video přerušování vlastní značky](images/video-tutorial-custom-tags.png)

1. Spuštění ukázkové aplikace, budete potřebovat účet Azure a účet služby Azure Media Services. Kromě toho potřebují přístup k obsahu moderátora privátní Preview verzi. Nakonec můžete pověření ověřování Azure Active Directory. Podrobnosti o získání tyto informace najdete v tématu [rychlý start Video přerušování API](video-moderation-api.md).

1. Upravte soubor `App.config` , přidejte název klienta služby Active Directory, koncové body služby a klíče předplatné indikován `#####`. Je třeba následující informace:

|Klíč|Popis|
|-|-|
|`AzureMediaServiceRestApiEndpoint`|Koncový bod pro rozhraní API služby Azure Media Services (AMS)|
|`ClientSecret`|Klíč předplatného pro Azure Media Services|
|`ClientId`|ID klienta pro službu Azure Media Services|
|`AzureAdTenantName`|Název klienta Active Directory, které jsou představující vaší organizace|
|`ContentModeratorReviewApiSubscriptionKey`|Klíč předplatného pro obsahu moderátora zkontrolujte rozhraní API|
|`ContentModeratorApiEndpoint`|Koncový bod pro obsahu moderátora rozhraní API|
|`ContentModeratorTeamId`|ID obsahu moderátora týmu|

## <a name="getting-started"></a>Začínáme

Třída `Program` v `Program.cs` je hlavní vstupní bod do aplikace video přerušování.

### <a name="methods-of-class-program"></a>Metody třídy programu

|Metoda|Popis|
|-|-|
|`Main`|Analyzuje příkazového řádku, shromažďuje vstup uživatele a spustí zpracování.|
|`ProcessVideo`|Komprimuje, odešle, moderates a vytvoří video recenze.|
|`CreateVideoStreamingRequest`|Vytvoří datový proud nahrát video|
|`GetUserInputs`|Shromažďuje vstup uživatele; použít, když nejsou přítomné žádné možnosti příkazového řádku|
|`Initialize`|Inicializuje objektů nezbytných pro proces přerušování|

### <a name="the-main-method"></a>Metodu Main

`Main()` je, kde začít provádění, tak, aby byl na místě zahájíte Principy video přerušování procesu.

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

`Main()` zpracovává následující argumenty příkazového řádku:

- Cesta k adresáři obsahující MPEG-4 video soubory k odeslání pro přerušování. Všechny `*.mp4` soubory v tomto adresáři a jejích podadresářů jsou odeslány pro přerušování.
- Volitelně logická hodnota (true nebo false) příznak označující, zda by měl být vygenerován text přepisy pro účely moderování zvukovém souboru.

Pokud nejsou žádné argumenty příkazového řádku `Main()` volání `GetUserInputs()`. Tato metoda vyzve uživatele k zadejte cestu k souboru jednoho videa a určit, zda by měl být vygenerován přepis text.

> [!NOTE]
> Konzolové aplikace používá [rozhraní API služby Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) generovat přepisy z zvuk sledovat nahrané video. Výsledky jsou uvedeny v WebVTT formátu. Další informace v tomto formátu najdete v tématu [webové Video textové stopy formátu](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API).

### <a name="initialize-and-processvideo-methods"></a>Inicializace a ProcessVideo metody

Bez ohledu na to, jestli program možnosti pocházejí z příkazového řádku nebo z interaktivní uživatelský vstup `Main()` další volání `Initialize()` vytvořit následující instance:

|Třída|Popis|
|-|-|
|`AMSComponent`|Komprimuje video soubory před jejich odesláním pro přerušování.|
|`AMSconfigurations`|Rozhraní pro aplikace konfigurační data v nalezen `App.config`.|
|`VideoModerator`| Nahrávání, kódování, šifrování a přerušování pomocí AMS SDK|
|`VideoReviewApi`|Spravuje video recenze ve službě obsahu moderátora|

Tyto třídy (aside z `AMSConfigurations`, což je přehledné) jsou podrobněji v nadcházející částech tohoto kurzu.

Nakonec video soubory jsou zpracovávány po dobu jedné voláním `ProcessVideo()` pro každou.

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


`ProcessVideo()` Metoda je poměrně jednoduché. V pořadí, provede následující operace:

- Komprimuje video
- Odešle videa pro prostředek služby Azure Media Services
- Vytvoří úlohu AMS na střední video
- Vytvoří kontrolu video v obsahu moderátora

V následujících částech zvažte podrobněji některé z jednotlivých procesů vyvolané `ProcessVideo()`. 

## <a name="compressing-the-video"></a>Komprese videa

Minimalizovat síťový provoz, aplikace převede videosouborů na formát H.264 (AVC MPEG-4) a lze je škálovat na maximální šířku 640 pixelů. Kodek H.264 se nedoporučuje, protože jeho vysoce účinné (komprese rychlost). Komprese se provádí pomocí bezplatnou `ffmpeg` nástroj příkazového řádku, který je součástí `Lib` složku řešení sady Visual Studio. Vstupní soubory mohou být libovolného formátu nepodporuje `ffmpeg`, včetně nejčastěji používané formátů videosouborů a kodeky.

> [!NOTE]
> Při spuštění programu pomocí možnosti příkazového řádku, zadejte adresář obsahující video soubory k odeslání pro přerušování. Všechny soubory v této directory nutnosti `.mp4` příponu názvu souboru se zpracovávají. Při zpracování jiné přípony názvů souborů, aktualizovat `Main()` metoda v `Program.cs` zahrnout požadované rozšíření.

Kód, který komprimaci jednoho video souboru je `AmsComponent` třídy v `AMSComponent.cs`. Metoda, která je zodpovědná za tato funkce je `CompressVideo()`, tady uvedené.

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

- Kontroluje, zkontrolujte konfiguraci v `App.config` obsahuje všechna potřebná data
- Kontroluje, zajistěte, aby `ffmpeg` binární je k dispozici
- Název výstupního souboru sestaví připojením `_c.mp4` na základní název souboru (například `Example.mp4`  ->  `E>xample_c.mp4`)
- Sestavení příkazového řádku provést převod řetězce
- Spustí `ffmpeg` zpracovat pomocí příkazového řádku
- Čeká na video, které mají být zpracovány

> [!NOTE]
> Pokud znáte videa jsou již komprimované pomocí H.264 a mít odpovídající dimenzí, je možné přepsat `CompressVideo()` tak, aby přeskočil komprese.

Název souboru komprimované výstupního souboru, vrátí metoda.

## <a name="uploading-and-moderating-the-video"></a>Odesílání a moderování video

Video musí být uložen ve službě Azure Media Services, než může být zpracována službou obsahu přerušování. `Program` Třídy v `Program.cs` má krátké metodu `CreateVideoStreamingRequest()` , vrátí objekt představující streamování žádost použity k nahrávání videa.

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

Výsledná `UploadVideoStreamRequest` objektu je definována v `UploadVideoStreamRequest.cs` (a jeho nadřazeným prvkem, `UploadVideoRequest`v `UploadVideoRequest.cs`). Tyto třídy nejsou zobrazeny zde; jsou krátká a slouží pouze k uložení komprimovaná videa data a informace o něm. Jiné třídy pouze data, `UploadAssetResult` (`UploadAssetResult.cs`) se používá k ukládání výsledků procesu nahrávání. Nyní je možné zjistit tyto řádky v `ProcessVideo()`:

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

- Vytvoření `UploadVideoStreamRequest` nahrát komprimované video
- Nastavit žádosti `GenerateVTT` příznak, pokud uživatel vyžaduje přepis textu
- Volání `CreateAzureMediaServicesJobToModerateVideo()` k provádět odesílání a příjmu výsledek

## <a name="deep-dive-into-video-moderation"></a>Podrobné informace do video přerušování

Metoda `CreateAzureMediaServicesJobToModerateVideo()` v `VideoModerator.cs`, který obsahuje hromadným kód, který komunikuje se službou Azure Media Services. Metody zdrojový kód je uveden v následující extract.

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

- Vytvoří úlohu pro zpracování provést služby AMS
- Přidá úlohy pro kódování souboru videa, moderování ho a generování přepis textu
- Odešle úlohu nahrávání souboru a zpracování začátku
- Načte výsledky přerušování, přepis text (Pokud je požadována) a další informace

## <a name="sample-video-moderation-response"></a>Ukázkové video přerušování odpovědi

Výsledek úlohy video přerušování (najdete v části [rychlý start video přerušování](video-moderation-api.md) je datová struktura JSON obsahující přerušování výsledky. Tyto výsledky obsahovat rozpis fragmenty (snímky) v rámci video, obsahující události (klipy) s klíčových snímků, které jsou opatření příznakem ke kontrole. Každý klíčových snímků skóre pro magnitudu podle pravděpodobnost, že obsahuje obsah pro dospělé nebo zájem. Následující příklad ukazuje odpověď JSON:

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

Přepis zvuku z videa je také vytváří, když `GenerateVTT` je nastavený příznak.

> [!NOTE]
> Konzolové aplikace používá [rozhraní API služby Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) ke generování přepisy z zvukové stopy nahrané video. Výsledky jsou uvedeny v WebVTT formátu. Další informace v tomto formátu najdete v tématu [webové Video textové stopy formátu](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API).


## <a name="creating-the-human-in-the-loop-review"></a>Vytváření zkontrolujte lidské v the smyčky

Proces přerušování vrátí seznam klíčových snímků z videa, společně s přepis jeho zvukových stop. Dalším krokem je vytvoření kontrolu v obsahu moderátora kontrolní nástroj pro lidské moderátorů. Po návratu do `ProcessVideo()` metoda v `Program.cs`, viz volání `CreateVideoReviewInContentModerator()` metoda. Tato metoda je v `videoReviewApi` třída, která je v `VideoReviewAPI.cs`a zobrazí se zde.

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

`CreateVideoReviewInContentModerator()` volá několik metod k provádění následujících úloh:

> [!NOTE]
> Konzolové aplikace používá [FFmpeg](https://ffmpeg.org/) knihovny pro vytváření miniatur. Tyto miniatury (imagí) odpovídají rámce časová razítka v [výstupu videa přerušování](#sample-video-moderation-response).

|Úkol|Metody|File|
|-|-|-|
|Extrakce klíč rámců z videa a vytvoří obrázky miniatur je|`CreateVideoFrames()`<br>`GenerateFrameImages()`|`FrameGeneratorServices.cs`|
|Kontrola přepis text, pokud je k dispozici, vyhledejte pro dospělé nebo zájem zvuk|`GenerateTextScreenProfanity()`| `VideoReviewAPI.cs`|
|Příprava a odešle požadavek na video revize pro lidské kontroly|`CreateReviewRequestObject()`<br> `ExecuteCreateReviewApi()`<br>`CreateAndPublishReviewInContentModerator()`|`VideoReviewAPI.cs`|

## <a name="video-review-default-view"></a>Video Zkontrolujte výchozí zobrazení

Na následující obrazovce se zobrazí výsledky předchozí kroky.

![Video Zkontrolujte výchozí zobrazení](images/video-tutorial-default-view.PNG)

## <a name="transcript-generation"></a>Přepis generování

Kód uvedený v tomto kurzu má dosud zaměřuje na vizuální obsah. Kontrola obsahu řeči je proces samostatné a volitelné používající, jak je uvedeno, přepis, generují z zvukovém souboru. Je čas nyní podívat se na způsob vytváření a použít v procesu zkontrolujte text přepisy. Úloha generování zápis spadá [Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-index-content) služby.

Aplikace provede následující úlohy:

|Úkol|Metody|File|
|-|-|-|
|Určení, zda text přepisy jsou má být vygenerován|`Main()`<br>`GetUserInputs()`|`Program.cs`|
|Pokud ano, odešlete úlohu přepis jako součást přerušování|`ConfigureTranscriptTask()`|`VideoModerator.cs`|
|Místní kopie zápis|`GenerateTranscript()`|`VideoModerator.cs`|
|Příznak snímky videa, které obsahují nevhodných zvuk|`GenerateTextScreenProfanity()`<br>`TextScreen()`|`VideoReviewAPI.cs`|
|Přidat výsledky do kontrola|`UploadScreenTextResult()`<br>`ExecuteAddTranscriptSupportFile()`|`VideoReviewAPI.cs`|

### <a name="task-configuration"></a>Konfigurace úlohy

Do odeslání úlohy přepis Vraťme vpravo. `CreateAzureMediaServicesJobToModerateVideo()` (už popsané) volání `ConfigureTranscriptTask()`.

    private void ConfigureTranscriptTask(IJob job)
    {
        string mediaProcessorName = _amsConfigurations.MediaIndexer2MediaProcessor;
        IMediaProcessor processor = _mediaContext.MediaProcessors.GetLatestMediaProcessorByName(mediaProcessorName);

        string configuration = File.ReadAllText(_amsConfigurations.MediaIndexerConfigurationJson);
        ITask task = job.Tasks.AddNew("AudioIndexing Task", processor, configuration, TaskOptions.None);
        task.InputAssets.Add(asset);
        task.OutputAssets.AddNew("AudioIndexing Output Asset", AssetCreationOptions.None);
    }

Konfigurace pro přepis úloh je čtení ze souboru `MediaIndexerConfig.json` v rámci řešení `Lib` složky. AMS prostředky jsou vytvořeny pro konfigurační soubor a výstup přepis procesu. Při spuštění úlohy AMS tato úloha vytvoří přepis text z sledovat audio video souboru.

> [!NOTE]
> Ukázkovou aplikaci rozpozná řeči pouze v angličtinu.

### <a name="transcript-generation"></a>Přepis generování

Zápis je publikován jako prostředek AMS. Postup kontroly přepis pro problematický obsah, stáhne aplikace prostředku ze služby Azure Media Services. `CreateAzureMediaServicesJobToModerateVideo()` volání `GenerateTranscript()`, uvedené tady načíst soubor.

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

Po některé potřebné instalační AMS vlastní stahování provádí volání `DownloadAssetToLocal()`, obecný funkci, která zkopíruje prostředek AMS do místního souboru.

## <a name="transcript-moderation"></a>Přepis přerušování

S přepis vždy po ruce je zkontrolovat a použít v kontrola. Vytvoření kontrola je kompetenci `CreateVideoReviewInContentModerator()`, že volání `GenerateTextScreenProfanity()` provedete úlohu. Naopak, tato metoda volá `TextScreen()`, který obsahuje většinu funkcí. 

`TextScreen()` provádí následující úlohy:

- Analýzy přepis dobu tamps a titulky
- Odeslání jednotlivých popiscích pro text přerušování
- Příznak žádné rámců, které mohou mít nežádoucí řeči obsahu

Podívejme se na všechny tyto úlohy podrobněji:

### <a name="initialize-the-code"></a>Inicializace kód

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
    

### <a name="parse-the-transcript-for-captions"></a>Analyzovat přepis pro titulky

V dalším kroku analyzovat formátovaný přepis VTT titulky a časová razítka. Nástroj zkontrolujte zobrazí na kartě přepis na obrazovce video Zkontrolujte tyto titulky. Časová razítka slouží k synchronizaci titulky s odpovídající snímky videa.

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

### <a name="moderate-captions-with-the-text-moderation-service"></a>Střední titulky přerušování službou textu

V dalším kroku jsme kontrolu titulky analyzovaný text textem obsahu moderátora rozhraní API.

> [!NOTE]
> Klíč obsahu moderátora služby má požadavky na druhý omezení četnosti (RPS). Pokud limit překročíte, sadu SDK vyvolá výjimku s kódem 429 chyby. 
>
> Úroveň free klíč může mít jeden RPS rychlost.

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

### <a name="breaking-down-the-text-moderation-step"></a>Rozdělení kroku přerušování textu

`TextScreen()` je výrazně metoda, můžeme rozdělit.

1. Metoda nejprve načte soubor přepis řádek po řádku. Ignoruje prázdné řádky a řádky, které obsahují `NOTE` s jistotou skóre. Extrahuje časová razítka a text položky z *upozornění* v souboru. Cue představuje text z zvukové stopy a zahrnuje počáteční a koncový čas. Cue začíná časové razítko ose řetězcem `-->`. Následuje jeden nebo více řádků textu.

1. Instance `CaptionScreentextResult` (definované v `TranscriptProfanity.cs`) slouží k uložení informace analyzovat z každé cue.  Když se zjistí nový řádek časové razítko, nebo je dosaženo text maximální délky 1024 znaků, nový `CaptionScreentextResult` je přidán do `csrList`. 

1. Metoda odesílá vedle jednotlivých cue rozhraní API přerušování Text. Obě zavolá `ContentModeratorClient.TextModeration.DetectLanguageAsync()` a `ContentModeratorClient.TextModeration.ScreenTextWithHttpMessagesAsync()`, která jsou definována v `Microsoft.Azure.CognitiveServices.ContentModerator` sestavení. Abyste se vyhnuli, je míra limited, metoda pozastaví sekundy před odesláním každý cue.

1. Po přijetí výsledky ze služby Text přerušování, analyzuje potom metoda má podívat, jestli splňují spolehlivosti prahové hodnoty. Tyto hodnoty jsou nastavené v `App.config` jako `OffensiveTextThreshold`, `RacyTextThreshold`, a `AdultTextThreshold`. Nakonec jsou uloženy i problematický podmínky sami. Všechny snímky v časovém rozsahu cue jsou označeny jako obsahující urážlivé, zájem nebo pro dospělé text.

1. `TextScreen()` Vrátí `TranscriptScreenTextResult` instanci, která obsahuje výsledek přerušování text z video jako celek. Tento objekt obsahuje příznaky a skóre pro různé typy problematický obsah, společně s seznam všech problematický podmínky. Volající, `CreateVideoReviewInContentModerator()`, volání `UploadScreenTextResult()` připojit tyto informace k kontrola tak, aby byl k dispozici pro lidské kontrolorů.
 
## <a name="video-review-transcript-view"></a>Video zkontrolujte přepis zobrazení

Následující obrazovka ukazuje výsledek zápis generování a přerušování kroky.

![Video přerušování přepis zobrazení](images/video-tutorial-transcript-view.PNG)

## <a name="program-output"></a>Výstup programu

Následující výstup příkazového řádku programu zobrazuje různé úlohy, jejich dokončení. Kromě toho jsou k dispozici ve stejném adresáři jako původní video soubory výsledek přerušování (ve formátu JSON) a přepis řeči.

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

[Stáhněte si řešení sady Visual Studio](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) ukázkové soubory a knihovny požadované pro tento kurz a začněte na svoji integraci.
