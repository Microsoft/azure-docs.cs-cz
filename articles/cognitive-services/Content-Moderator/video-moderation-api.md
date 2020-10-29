---
title: Analýza obsahu videa pro nevhodný materiál v C# – Content Moderator
titleSuffix: Azure Cognitive Services
description: Analýza obsahu videa pro různé problematické materiály pomocí sady Content Moderator SDK pro .NET
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: how-to
ms.date: 05/18/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: cd813c6db9d03b0b7c84497e5b44f6ecdb591437
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2020
ms.locfileid: "92912850"
---
# <a name="analyze-video-content-for-objectionable-material-in-c"></a>Analýza obsahu videa pro nevhodný materiál v jazyce C #

V tomto článku najdete informace a ukázky kódu, které vám pomůžou začít používat [sadu Content moderator SDK pro .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) ke skenování obsahu videa pro dospělé nebo pikantní obsah.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/cognitive-services/), ještě než začnete. 

## <a name="prerequisites"></a>Předpoklady
- Libovolná edice sady [Visual Studio 2015 nebo 2017](https://www.visualstudio.com/downloads/)

## <a name="set-up-azure-resources"></a>Nastavení prostředků Azure

Funkce pro moderování videa Content Moderator je k dispozici jako bezplatný **procesor multimediálních** verzí ve verzi Public preview v Azure Media Services (AMS). Azure Media Services je specializovaná služba Azure pro ukládání a streamování obsahu videa. 

### <a name="create-an-azure-media-services-account"></a>Vytvoření účtu Azure Media Services

Postupujte podle pokynů v části [Vytvoření účtu Azure Media Services](../../media-services/previous/media-services-portal-create-account.md) pro přihlášení k odběru AMS a vytvořte přidružený účet úložiště Azure. V tomto účtu úložiště vytvořte nový kontejner úložiště objektů BLOB.

### <a name="create-an-azure-active-directory-application"></a>Vytvoření aplikace Azure Active Directory

V Azure Portal přejděte do nového předplatného AMS a v postranní nabídce vyberte **přístup přes rozhraní API** . Vyberte **připojit k Azure Media Services s instančním objektem** . Poznamenejte si hodnotu v poli **REST API koncový bod** . budete ho potřebovat později.

V části **aplikace Azure AD** vyberte **vytvořit novou** a pojmenujte novou registraci aplikace služby Azure AD (například "VideoModADApp"). Klikněte na **Uložit** a počkejte několik minut, než se aplikace nakonfiguruje. Pak by se měla zobrazit nová registrace aplikace v části **aplikace Azure AD** stránky.

Vyberte registraci aplikace a klikněte na tlačítko **Spravovat aplikaci** pod ním. Poznamenejte si hodnotu v poli **ID aplikace** . budete ho potřebovat později. Vyberte **Nastavení**  >  **klíče** a zadejte popis nového klíče (například "VideoModKey"). Klikněte na **Uložit** a pak si všimněte nové hodnoty klíče. Zkopírujte tento řetězec a uložte ho někam do zabezpečení.

Podrobný návod k výše uvedenému procesu najdete v tématu Začínáme [s ověřováním Azure AD](../../media-services/previous/media-services-portal-get-started-with-aad.md).

Až to uděláte, můžete použít procesor médií pro moderování videa dvěma různými způsoby.

## <a name="use-azure-media-services-explorer"></a>Použití Průzkumníka Azure Media Services

Azure Media Services Explorer je uživatelsky přívětivý front-end pro AMS. Použijte ho k procházení účtu AMS, nahrávání videí a skenování obsahu pomocí procesoru Content Moderator Media. Můžete si ho stáhnout a nainstalovat z [GitHubu](https://github.com/Azure/Azure-Media-Services-Explorer/releases). Další informace najdete v [blogovém příspěvku o Azure Media Services v Průzkumníkovi](https://azure.microsoft.com/blog/managing-media-workflows-with-the-new-azure-media-services-explorer-tool/) .

![Azure Media Services Explorer s Content Moderator](images/ams-explorer-content-moderator.PNG)

## <a name="create-the-visual-studio-project"></a>Vytvoření projektu sady Visual Studio

1. V aplikaci Visual Studio vytvořte nový projekt **konzolové aplikace (.NET Framework)** a pojmenujte ho **VideoModeration** . 
1. Pokud vaše řešení obsahuje i jiné projekty, vyberte tento projekt jako jediný spouštěný projekt.
1. Získejte požadované balíčky NuGet. Klikněte pravým tlačítkem na svůj projekt v Průzkumníku řešení a vyberte **Spravovat balíčky NuGet** . Potom vyhledejte a nainstalujte následující balíčky:
    - windowsazure. MediaServices
    - windowsazure. MediaServices. Extensions

## <a name="add-video-moderation-code"></a>Přidat kód pro moderování videa

Dále zkopírováním kódu z této příručky a jeho vložením do svého projektu implementujete základní scénář moderování obsahu.

### <a name="update-the-programs-using-statements"></a>Aktualizace příkazů using programu

Na začátek souboru _Program.cs_ přidejte následující příkazy `using`.

```csharp
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
```

### <a name="set-up-resource-references"></a>Nastavení odkazů na prostředky

Do třídy **program** v _program.cs_ přidejte následující statická pole. Tato pole obsahují informace potřebné pro připojení k předplatnému AMS. Vyplňte je hodnotami, které jste získali v krocích výše. Všimněte si, že `CLIENT_ID` je hodnota **ID aplikace** vaší aplikace Azure AD a `CLIENT_SECRET` je hodnota "VideoModKey", kterou jste pro tuto aplikaci vytvořili.

```csharp
// declare constants and globals
private static CloudMediaContext _context = null;
private static CloudStorageAccount _StorageAccount = null;

// Azure Media Services (AMS) associated Storage Account, Key, and the Container that has 
// a list of Blobs to be processed.
static string STORAGE_NAME = "YOUR AMS ASSOCIATED BLOB STORAGE NAME";
static string STORAGE_KEY = "YOUR AMS ASSOCIATED BLOB STORAGE KEY";
static string STORAGE_CONTAINER_NAME = "YOUR BLOB CONTAINER FOR VIDEO FILES";

private static StorageCredentials _StorageCredentials = null;

// Azure Media Services authentication. 
private const string AZURE_AD_TENANT_NAME = "microsoft.onmicrosoft.com";
private const string CLIENT_ID = "YOUR CLIENT ID";
private const string CLIENT_SECRET = "YOUR CLIENT SECRET";

// REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".      
private const string REST_API_ENDPOINT = "YOUR API ENDPOINT";

// Content Moderator Media Processor Nam
private const string MEDIA_PROCESSOR = "Azure Media Content Moderator";

// Input and Output files in the current directory of the executable
private const string INPUT_FILE = "VIDEO FILE NAME";
private const string OUTPUT_FOLDER = "";

// JSON settings file
private static readonly string CONTENT_MODERATOR_PRESET_FILE = "preset.json";

```

Pokud chcete použít místní videosoubor (nejjednodušší případ), přidejte ho do projektu a zadejte jeho cestu jako `INPUT_FILE` hodnotu (relativní cesty jsou relativní vzhledem ke spouštěcímu adresáři).

Budete také muset vytvořit _preset.jspro_ soubor v aktuálním adresáři a použít ho k zadání čísla verze. Příklad:

```JSON
{
    "version": "2.0"
}
```

### <a name="load-the-input-videos"></a>Načíst vstupní video (y)

Metoda **Main** třídy **program** vytvoří kontext média Azure a pak Azure Storage kontext (v případě, že jsou vaše videa v úložišti objektů BLOB). Zbývající kód zkontroluje video z místní složky, objektu BLOB nebo několika objektů BLOB v rámci kontejneru úložiště Azure. Můžete vyzkoušet všechny možnosti zadáním komentáře k ostatním řádkům kódu.

```csharp
// Create Azure Media Context
CreateMediaContext();

// Create Storage Context
CreateStorageContext();

// Use a file as the input.
IAsset asset = CreateAssetfromFile();

// -- OR ---

// Or a blob as the input
// IAsset asset = CreateAssetfromBlob((CloudBlockBlob)GetBlobsList().First());

// Then submit the asset to Content Moderator
RunContentModeratorJob(asset);

//-- OR ----

// Just run the content moderator on all blobs in a list (from a Blob Container)
// RunContentModeratorJobOnBlobs();
```

### <a name="create-an-azure-media-context"></a>Vytvoření kontextu Azure Media

Do třídy **Program** přidejte následující metodu. Tato možnost používá vaše přihlašovací údaje AMS k umožnění komunikace s AMS.

```csharp
// Creates a media context from azure credentials
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
```

### <a name="add-the-code-to-create-an-azure-storage-context"></a>Přidejte kód pro vytvoření kontextu Azure Storage

Do třídy **Program** přidejte následující metodu. Pro přístup k úložišti objektů blob můžete použít kontext úložiště vytvořený z přihlašovacích údajů úložiště.

```csharp
// Creates a storage context from the AMS associated storage name and key
static void CreateStorageContext()
{
    // Get a reference to the storage account associated with a Media Services account. 
    if (_StorageCredentials == null)
    {
        _StorageCredentials = new StorageCredentials(STORAGE_NAME, STORAGE_KEY);
    }
    _StorageAccount = new CloudStorageAccount(_StorageCredentials, false);
}
```

### <a name="add-the-code-to-create-azure-media-assets-from-local-file-and-blob"></a>Přidejte kód pro vytvoření Azure Media assets z místního souboru a objektu BLOB

Procesor Content Moderatorového média spouští úlohy na **assetech** v rámci Azure Media Services platformy.
Tyto metody vytvoří prostředky z místního souboru nebo přidruženého objektu BLOB.

```csharp
// Creates an Azure Media Services Asset from the video file
static IAsset CreateAssetfromFile()
{
    return _context.Assets.CreateFromFile(INPUT_FILE, AssetCreationOptions.None); ;
}

// Creates an Azure Media Services asset from your blog storage
static IAsset CreateAssetfromBlob(CloudBlockBlob Blob)
{
    // Create asset from the FIRST blob in the list and return it
    return _context.Assets.CreateFromBlob(Blob, _StorageCredentials, AssetCreationOptions.None);
}
```

### <a name="add-the-code-to-scan-a-collection-of-videos-as-blobs-within-a-container"></a>Přidání kódu pro skenování kolekce videí (jako objektů BLOB) v rámci kontejneru

```csharp
// Runs the Content Moderator Job on all Blobs in a given container name
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

// Get all blobs in your container
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
```

### <a name="add-the-method-to-run-the-content-moderator-job"></a>Přidejte metodu pro spuštění úlohy Content Moderator

```csharp
// Run the Content Moderator job on the designated Asset from local file or blob storage
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
```

### <a name="add-helper-functions"></a>Přidat pomocné funkce

Tyto metody stáhnou Content Moderator výstupní soubor (JSON) z Azure Media Services assetu a pomůžou sledovat stav úlohy moderování tak, aby program mohl do konzoly protokolu zaprotokolovat běžící stav.

```csharp
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
```

### <a name="run-the-program-and-review-the-output"></a>Spuštění programu a kontrola výstupu

Po dokončení úlohy Moderování obsahu Analyzujte odpověď JSON. Skládá se z těchto elementů:

- Shrnutí informací o videu
- **Nasnímků** jako " **fragmenty** "
- **Klíčové snímky** jako " **události** " s příznakem "události" s **reviewRecommended "(= true nebo false)"** na základě výsledků z **dospělého** a **pikantní**
- možnost **Start** , **Duration** , **totalDuration** a **timestamp** jsou v "Ticks". Rozdělte podle **časové osy** k získání počtu v sekundách.
 
> [!NOTE]
> - `adultScore` představuje potenciální přítomnost a předpověď skóre obsahu, který může být v určitých situacích považován za zřejmý nebo dospělý.
> - `racyScore` představuje potenciální stav a předpověď skóre obsahu, který může být v určitých situacích považovaný za pohlavní sugestivní nebo vyspělý.
> - `adultScore` a `racyScore` jsou mezi 0 a 1. Čím vyšší je skóre, tím vyšší je model, který předpokládá, že se kategorie může použít. Tato verze Preview spoléhá na místo ručně kódovaných výsledků z statistického modelu. Doporučujeme, abyste při testování pomocí vlastního obsahu určili, jak jednotlivé kategorie odpovídají vašim požadavkům.
> - `reviewRecommended` je buď true, nebo false v závislosti na vnitřních prahech skóre. Zákazníci by měli posoudit, jestli tuto hodnotu použít, nebo se rozhodnout o vlastních prahech na základě zásad obsahu.

```json
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
```

## <a name="next-steps"></a>Další kroky

Naučte se generovat [recenze videí](video-reviews-quickstart-dotnet.md) z výstupu pro moderování.

Přidejte do recenze videí [moderování přepisu](video-transcript-moderation-review-tutorial-dotnet.md) .

Projděte si podrobný kurz týkající se sestavení [kompletního řešení pro moderování videa a přepisu](video-transcript-moderation-review-tutorial-dotnet.md).

[Stáhněte si řešení sady Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) pro tuto a další Content moderator rychlý Start pro .NET.