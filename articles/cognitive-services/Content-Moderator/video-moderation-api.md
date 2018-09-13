---
title: Azure Content Moderator – moderování videa | Dokumentace Microsoftu
description: Moderování videa pomocí můžete spustit kontrolu možné obsahu pro dospělé nebo pikantního obsahu.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 02/02/2018
ms.author: sajagtap
ms.openlocfilehash: 27e189d93573dea139c2b67c237c376a28100c2b
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44714305"
---
# <a name="video-moderation"></a>Moderování videa

V současné době online prohlížeče generovat miliardy zobrazení videa napříč weby Oblíbené a regionální sociálních médií a zvýšení. S použitím služeb založených na strojové učení k předpovědi potenciální obsahu pro dospělé nebo pikantního obsahu, snížit náklady na vaše úsilí moderování.

## <a name="sign-up-for-the-content-moderator-media-processor-preview"></a>Zaregistrujte si procesor médií Content Moderator (preview)

### <a name="create-a-free-azure-account"></a>Vytvořte si bezplatný účet Azure.

[Začněte tady](https://azure.microsoft.com/free/) vytvořit si bezplatný účet Azure, pokud již nemáte.

### <a name="create-an-azure-media-services-account"></a>Vytvoření účtu Azure Media Services

Content Moderator videa funkce je dostupná jako veřejná verze preview **procesor médií** v Azure Media Services (AMS) bez poplatků.

[Vytvoření účtu Azure Media Services](https://docs.microsoft.com/azure/media-services/media-services-portal-create-account) ve vašem předplatném Azure.

### <a name="get-azure-active-directory-credentials"></a>Získání přihlašovacích údajů Azure Active Directory

   1. Čtení [článku portálu služby Azure Media Services](https://docs.microsoft.com/azure/media-services/media-services-portal-get-started-with-aad) se naučíte získat přihlašovací údaje pro ověřování Azure AD pomocí webu Azure portal.
   1. Čtení [Azure Media Services .NET článku](https://docs.microsoft.com/azure/media-services/media-services-dotnet-get-started-with-aad) na další informace o použití přihlašovacích údajů Azure Active Directory pomocí .NET SDK.

   > [!NOTE]
   > Ukázkový kód v tomto rychlém startu používá **ověřování instančních objektů** metody popsané v obou článcích.

Jakmile se zobrazí vaše přihlašovací údaje AMS, zkuste procesor médií Content Moderator dvěma způsoby.

## <a name="use-azure-media-services-explorer"></a>Použití Azure Media Services Explorer

Pomocí interaktivního [Průzkumník Azure Media Services (AMS)](https://azure.microsoft.com/blog/managing-media-workflows-with-the-new-azure-media-services-explorer-tool/) vyhledejte svůj účet AMS, nahrát videa a zkontrolovat pomocí procesoru media Content Moderatoru. [Stáhněte si a nainstalujte ho](https://github.com/Azure/Azure-Media-Services-Explorer/releases) z Githubu, a [procházení zdrojového kódu](http://github.com/Azure/Azure-Media-Services-Explorer) věnovat pomocí AMS SDK se.

![Průzkumník služby Azure Media Services s Content Moderatorem](images/ams-explorer-content-moderator.PNG)

## <a name="net-quickstart-with-visual-studio-and-c"></a>Rychlý úvod k .NET pomocí sady Visual Studio a C#

1. Přidat nový **Konzolová aplikace (.NET Framework)** do svého řešení projekt.

   Ve vzorovém kódu, pojmenujte projekt **VideoModeration**.

1. Vyberte tento projekt jako jeden spouštěný projekt pro řešení.

### <a name="install-required-packages"></a>Instalace požadovaných balíčků

Nainstalujte následující balíčky NuGet, která je k dispozici na [NuGet](https://www.nuget.org/).

- windowsazure.mediaservices
- windowsazure.mediaservices.Extensions

### <a name="update-the-programs-using-statements"></a>Aktualizace programu v nástrojích příkazy

Upravit program v nástrojích příkazy.

    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.IO;
    using System.Threading;
    using Microsoft.WindowsAzure.Storage.Blob;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using System.Collections.Generic;


### <a name="initialize-application-specific-settings"></a>Inicializace nastavení specifické pro aplikaci

Přidejte následující statické pole na **Program** třída v souboru Program.cs.

    // declare constants and globals
    private static CloudMediaContext _context = null;
    private static CloudStorageAccount _StorageAccount = null;

    // Azure Media Services (AMS) associated Storage Account, Key, and the Container that has 
    // a list of Blobs to be processed.
    static string STORAGE_NAME = "YOUR AMS ASSOCIATED BLOB STORAGE NAME";
    static string STORAGE_KEY = "YOUR AMS ASSOCIATED BLOB STORAGE KEY";
    static string STORAGE_CONTAINER_NAME = "YOUR BLOB CONTAINER FOR VIDEO FILES";

    private static StorageCredentials _StorageCredentials = null;

    // Azure Media Services authentication. See the quickstart for how to get these.
    private const string AZURE_AD_TENANT_NAME = "microsoft.onmicrosoft.com";
    private const string CLIENT_ID = "YOUR CLIENT ID"
    private const string CLIENT_SECRET = "YOUR CLIENT SECRET";

    // REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".      
    private const string REST_API_ENDPOINT = "YOUR API ENDPOINT";

    // Content Moderator Media Processor Nam
    private const string MEDIA_PROCESSOR = "Azure Media Content Moderator";

    // Input and Output files in the current directory of the executable
    private const string INPUT_FILE = "VIDEO FILE NAME";
    private const string OUTPUT_FOLDER = "";

### <a name="create-a-preset-file-json"></a>Vytvořit soubor přednastavení (json)

Vytvořte soubor JSON v aktuálním adresáři s číslem verze.

    private static readonly string CONTENT_MODERATOR_PRESET_FILE = "preset.json";
    //Example file content:
    //        {
    //             "version": "2.0"
    //        }
    private static readonly string CONTENT_MODERATOR_PRESET_FILE = "preset.json";

### <a name="add-the-following-code-to-the-main-method"></a>Přidejte následující kód do metody main

Metoda main nejprve vytvoří objekt Context médií Azure a potom objekt Context úložiště Azure v případě, že jsou vaše videa ve službě blob storage.
Zbývající kód kontroluje videa z místní složky, objektů blob nebo více objektů BLOB v kontejneru služby Azure storage.
Všechny možnosti můžete vyzkoušet tak další řádky kódu.

    // Create Azure Media Context
    CreateMediaContext();

    // Create Storage Context
    CreateStorageContext();

    // Use a file as the input.
    // IAsset asset = CreateAssetfromFile();
    
    // -- OR ---
    
    // Or a blob as the input
    // IAsset asset = CreateAssetfromBlob((CloudBlockBlob)GetBlobsList().First());

    // Then submit the asset to Content Moderator
    // RunContentModeratorJob(asset);

    //-- OR ----

    // Just run the content moderator on all blobs in a list (from a Blob Container)
    RunContentModeratorJobOnBlobs();

### <a name="add-the-code-to-create-an-azure-media-context"></a>Přidejte kód k vytvoření Azure Media kontextu

    /// <summary>
    /// Creates a media context from azure credentials
    /// </summary>
    static void CreateMediaContext()
    {
        // Get Azure AD credentials
        var tokenCredentials = new AzureAdTokenCredentials(AZURE_AD_TENANT_NAME,
            new AzureAdClientSymmetricKey(CLIENT_ID, CLIENT_SECRET),
            AzureEnvironments.AzureCloudEnvironment);

        // Initialize an Azure AD token
        var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

        // Create a media context
        _context = new CloudMediaContext(new Uri(REST_API_ENDPOINT), tokenProvider);
    }

### <a name="add-the-code-to-create-an-azure-storage-context"></a>Přidejte kód k vytvoření kontextu Azure Storage
Kontext úložiště, vytvoří od přihlašovacích údajů úložiště používáte pro přístup k úložišti objektů blob.

    /// <summary>
    /// Creates a storage context from the AMS associated storage name and key
    /// </summary>
    static void CreateStorageContext()
    {
        // Get a reference to the storage account associated with a Media Services account. 
        if (_StorageCredentials == null)
        {
            _StorageCredentials = new StorageCredentials(STORAGE_NAME, STORAGE_KEY);
        }
        _StorageAccount = new CloudStorageAccount(_StorageCredentials, false);
    }

### <a name="add-the-code-to-create-azure-media-assets-from-local-file-and-blob"></a>Přidejte kód, který vytvořit mediální Assety Azure z místního souboru a objektů blob
Procesor médií Content Moderator spouštění úloh podle **prostředky** v rámci platformy Azure Media Services.
Tyto metody vytvoří prostředky z místního souboru nebo přidružený objekt blob.

    /// <summary>
    /// Creates an Azure Media Services Asset from the video file
    /// </summary>
    /// <returns>Asset</returns>
    static IAsset CreateAssetfromFile()
    {
        return _context.Assets.CreateFromFile(INPUT_FILE, AssetCreationOptions.None); ;
    }

    /// <summary>
    /// Creates an Azure Media Services asset from your blog storage
    /// </summary>
    /// <param name="Blob"></param>
    /// <returns>Asset</returns>
    static IAsset CreateAssetfromBlob(CloudBlockBlob Blob)
    {
        // Create asset from the FIRST blob in the list and return it
        return _context.Assets.CreateFromBlob(Blob, _StorageCredentials, AssetCreationOptions.None);
    }

### <a name="add-the-code-to-scan-a-collection-of-videos-as-blobs-within-a-container"></a>Přidejte kód pro skenování kolekce videa (jako objekty BLOB) v rámci kontejneru

    /// <summary>
    /// Runs the Content Moderator Job on all Blobs in a given container name
    /// </summary>
    static void RunContentModeratorJobOnBlobs()
    {
        // Get the reference to the list of Blobs. See the following method.
        var blobList = GetBlobsList();

        // Iterate over the Blob list items or work on specific ones as needed
        foreach (var sourceBlob in blobList)
        {
            // Create an Asset
            IAsset asset = _context.Assets.CreateFromBlob((CloudBlockBlob)sourceBlob,
                               _StorageCredentials, AssetCreationOptions.None);
            asset.Update();

            // Submit to Content Moderator
            RunContentModeratorJob(asset);
        }
    }

    /// <summary>
    /// Get all blobs in your container
    /// </summary>
    /// <returns></returns>
    static IEnumerable<IListBlobItem> GetBlobsList()
    {
        // Get a reference to the Container within the Storage Account
        // that has the files (blobs) for moderation
        CloudBlobClient CloudBlobClient = _StorageAccount.CreateCloudBlobClient();
        CloudBlobContainer MediaBlobContainer = CloudBlobClient.GetContainerReference(STORAGE_CONTAINER_NAME);

        // Get the reference to the list of Blobs 
        var blobList = MediaBlobContainer.ListBlobs();
        return blobList;
    }

### <a name="add-the-method-to-run-the-content-moderator-job"></a>Přidejte metodu ke spuštění úlohy Content Moderatoru

    /// <summary>
    /// Run the Content Moderator job on the designated Asset from local file or blob storage
    /// </summary>
    /// <param name="asset"></param>
    static void RunContentModeratorJob(IAsset asset)
    {
        // Grab the presets
        string configuration = File.ReadAllText(CONTENT_MODERATOR_PRESET_FILE);

        // grab instance of Azure Media Content Moderator MP
        IMediaProcessor mp = _context.MediaProcessors.GetLatestMediaProcessorByName(MEDIA_PROCESSOR);

        // create Job with Content Moderator task
        IJob job = _context.Jobs.Create(String.Format("Content Moderator {0}",
                asset.AssetFiles.First() + "_" + Guid.NewGuid()));

        ITask contentModeratorTask = job.Tasks.AddNew("Adult and racy classifier task",
                mp, configuration,
                TaskOptions.None);
        contentModeratorTask.InputAssets.Add(asset);
        contentModeratorTask.OutputAssets.AddNew("Adult and racy classifier output",
            AssetCreationOptions.None);

        job.Submit();


        // Create progress printing and querying tasks
        Task progressPrintTask = new Task(() =>
        {
            IJob jobQuery = null;
            do
            {
                var progressContext = _context;
                jobQuery = progressContext.Jobs
                .Where(j => j.Id == job.Id)
                    .First();
                    Console.WriteLine(string.Format("{0}\t{1}",
                    DateTime.Now,
                    jobQuery.State));
                    Thread.Sleep(10000);
             }
             while (jobQuery.State != JobState.Finished &&
             jobQuery.State != JobState.Error &&
             jobQuery.State != JobState.Canceled);
        });
        progressPrintTask.Start();

        Task progressJobTask = job.GetExecutionProgressTask(
        CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling 
        // method for job progress should log errors.  Here we check 
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            ErrorDetail error = job.Tasks.First().ErrorDetails.First();
            Console.WriteLine(string.Format("Error: {0}. {1}",
            error.Code,
            error.Message));
        }

        DownloadAsset(job.OutputMediaAssets.First(), OUTPUT_FOLDER);
    }

### <a name="add-a-couple-of-helper-functions"></a>Přidejte do ní několik pomocných funkcí

Tyto metody stahování prostředku Azure Media Services Content Moderator výstupního souboru (JSON) a sledovat stav úlohy moderování tak, že program může protokolovat stav spuštění do konzoly.

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }

    // event handler for Job State
    static void StateChanged(object sender, JobStateChangedEventArgs e)
    {
        Console.WriteLine("Job state changed event:");
        Console.WriteLine("  Previous state: " + e.PreviousState);
        Console.WriteLine("  Current state: " + e.CurrentState);
        switch (e.CurrentState)
        {
            case JobState.Finished:
                Console.WriteLine();
                Console.WriteLine("Job finished.");
                break;
            case JobState.Canceling:
            case JobState.Queued:
            case JobState.Scheduled:
            case JobState.Processing:
                Console.WriteLine("Please wait...\n");
                break;
            case JobState.Canceled:
                Console.WriteLine("Job is canceled.\n");
                break;
            case JobState.Error:
                Console.WriteLine("Job failed.\n");
                break;
            default:
                break;
        }
    }

### <a name="run-the-program-and-review-the-output"></a>Spusťte program a prohlédněte si výstup

Po dokončení úlohy moderování obsahu analyzujte odpověď JSON. Skládá se z těchto elementů:

- Souhrnné informace videa
- **Snímky** jako "**fragmenty**"
- **Klíčové snímky** jako "**události**" s **reviewRecommended "(= true nebo false)"** na základě příznaku **dospělé** a **Racy** skóre
- **Spustit**, **doba trvání**, **totalDuration**, a **časové razítko** v "značky". Dělení **časový rámec** Chcete-li získat číslo v sekundách.
 
> [!NOTE]

> - `adultScore` představuje potenciální prezentace a predikcí skóre obsah, který se dá považovat za sexuálně explicitní nebo pro dospělé v určitých situacích.
> - `racyScore` představuje potenciální prezentace a predikcí skóre obsah, který se dá považovat za sexuálně sugestivní nebo až po zralé v určitých situacích.
> - `adultScore` a `racyScore` jsou v rozmezí od 0 do 1. Čím více bodů vyšší model je predikce kategorii lze použít. V této verzi preview spoléhá na statistických modelů a nikoli ručně kódovaný výsledků. Doporučujeme ve Visual Basicu s vlastní obsah k určení, jak každou kategorii odpovídá vašim požadavkům.
> - `reviewRecommended` je PRAVDA nebo NEPRAVDA v závislosti na interní skóre prahové hodnoty. Zákazníci by měli zhodnotit, zda chcete použít tuto hodnotu nebo při rozhodování o vlastní prahové hodnoty na základě jejich obsahu zásad.
>

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

## <a name="next-steps"></a>Další postup

Zjistěte, jak generovat [video kontroly](video-reviews-quickstart-dotnet.md) z výstupu moderování.

Přidat [přepisu moderování](video-transcript-moderation-review-tutorial-dotnet.md) na video kontroly.

Podívejte se na podrobný kurz o tom, jak sestavit [dokončení řešení moderování videa a přepis](video-transcript-moderation-review-tutorial-dotnet.md).

[Stáhněte si řešení sady Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) pro tuto a další rychlé starty Content Moderator pro .NET.
