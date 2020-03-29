---
title: Analýza videoobsahu pro nežádoucí materiál v C# - Content Moderator
titleSuffix: Azure Cognitive Services
description: Jak analyzovat video obsah pro různé nežádoucí materiály pomocí sady Content Moderator SDK pro rozhraní .NET
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 71858755fe31823d4d7ef8623b915db851530116
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "72755242"
---
# <a name="analyze-video-content-for-objectionable-material-in-c"></a>Analyzujte video obsah pro nežádoucí materiál v C #

Tento článek obsahuje informace a ukázky kódu, které vám pomohou začít používat [sadu Content Moderator SDK pro rozhraní .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) ke skenování obsahu videa pro obsah pro dospělé nebo pikantní obsah.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete. 

## <a name="prerequisites"></a>Požadavky
- Libovolná edice sady [Visual Studio 2015 nebo 2017](https://www.visualstudio.com/downloads/)

## <a name="set-up-azure-resources"></a>Nastavení prostředků Azure

Funkce moderování videa moderátora obsahu je k dispozici jako bezplatný **mediální procesor** public preview ve službě Azure Media Services (AMS). Azure Media Services je specializovaná služba Azure pro ukládání a streamování obsahu videa. 

### <a name="create-an-azure-media-services-account"></a>Vytvoření účtu Azure Media Services

Podle pokynů v [části Vytvoření účtu Mediální služby Azure](https://docs.microsoft.com/azure/media-services/media-services-portal-create-account) se přihlaste k odběru Služby a mši a vytvořte přidružený účet úložiště Azure. V tomto účtu úložiště vytvořte nový kontejner úložiště objektů Blob.

### <a name="create-an-azure-active-directory-application"></a>Vytvoření aplikace Azure Active Directory

Přejděte na nové předplatné AMS na webu Azure Portal a vyberte **přístup k rozhraní API** z boční nabídky. Vyberte **Připojit k Mediální službě Azure pomocí instančního objektu**. Poznamenejte si hodnotu v poli **koncového bodu rozhraní REST API.** budete to potřebovat později.

V části **aplikace Azure AD** vyberte **Vytvořit nový** a pojmenujte novou registraci nové aplikace Azure AD (například "VideoModADApp"). Klikněte na **Uložit** a počkejte několik minut, než je aplikace nakonfigurována. Pak byste měli vidět registraci nové aplikace v části **aplikace Azure AD** na stránce.

Vyberte registraci aplikace a klikněte na tlačítko **Spravovat aplikaci** pod ní. Poznamenejte si hodnotu v poli **ID aplikace.** budete to potřebovat později. Vyberte **nastavení** > **kláves**a zadejte popis nového klíče (například VideoModKey"). Klikněte na **Uložit**a pak si všimněte nové hodnoty klíče. Zkopírujte tento řetězec a uložte jej na bezpečném místě.

Podrobnější návod k výše uvedenému procesu najdete v tématu [Začínáme s ověřováním Azure AD](https://docs.microsoft.com/azure/media-services/media-services-portal-get-started-with-aad).

Jakmile to uděláte, můžete použít mediální procesor moderování videa dvěma různými způsoby.

## <a name="use-azure-media-services-explorer"></a>Použití Průzkumníka mediálních služeb Azure

Průzkumník mediálních služeb Azure je uživatelsky přívětivý front-end pro AMS. Použijte jej k procházení účtu AMS, nahrávání videí a skenování obsahu pomocí mediálního procesoru Content Moderator. Stáhněte si a nainstalujte ho z [GitHubu](https://github.com/Azure/Azure-Media-Services-Explorer/releases)nebo si přečtěte [blogový příspěvek průzkumníka Mediálních služeb Azure,](https://azure.microsoft.com/blog/managing-media-workflows-with-the-new-azure-media-services-explorer-tool/) kde najdete další informace.

![Průzkumník Mediálních služeb Azure s moderátorem obsahu](images/ams-explorer-content-moderator.PNG)

## <a name="create-the-visual-studio-project"></a>Vytvoření projektu sady Visual Studio

1. V sadě Visual Studio vytvořte nový projekt **aplikace Console (.NET Framework)** a pojmenujte ji **VideoModeration**. 
1. Pokud vaše řešení obsahuje i jiné projekty, vyberte tento projekt jako jediný spouštěný projekt.
1. Získejte požadované balíčky NuGet. Klikněte pravým tlačítkem na svůj projekt v Průzkumníku řešení a vyberte **Spravovat balíčky NuGet**. Potom vyhledejte a nainstalujte následující balíčky:
    - windowsazure.mediaServices
    - windowsazure.mediaservices.extensions

## <a name="add-video-moderation-code"></a>Přidání kódu moderování videa

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

### <a name="set-up-resource-references"></a>Nastavení odkazů na zdroje

Do třídy **Program** přidejte následující statická pole v _Program.cs_. Tato pole uchovávají informace potřebné pro připojení k předplatnému AMS. Vyplňte je s hodnotami, které jste dostali ve výše uvedených krocích. Všimněte `CLIENT_ID` si, že je hodnota **ID** aplikace `CLIENT_SECRET` aplikace Azure AD a je hodnota "VideoModKey", které jste vytvořili pro tuto aplikaci.

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

Pokud chcete použít místní video soubor (nejjednodušší případ), přidejte jej do `INPUT_FILE` projektu a zadejte jeho cestu jako hodnotu (relativní cesty jsou relativní vzhledem k adresáři spuštění).

Budete také muset vytvořit soubor _preset.json_ v aktuálním adresáři a použít jej k určení čísla verze. Například:

```JSON
{
    "version": "2.0"
}
```

### <a name="load-the-input-videos"></a>Načtení vstupního videa (videa)

**Hlavní** metoda **třídy Program** vytvoří kontext médií Azure a pak kontext úložiště Azure (v případě, že vaše videa jsou v úložišti objektů blob). Zbývající kód prohledá video z místní složky, objektu blob nebo více objektů BLOB v kontejneru úložiště Azure. Můžete vyzkoušet všechny možnosti zakomentováním ostatnířádky kódu.

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

### <a name="create-an-azure-media-context"></a>Vytvoření kontextu médií Azure

Do třídy **Program** přidejte následující metodu. To používá vaše Pověření AMS k povolení komunikace s AMS.

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

### <a name="add-the-code-to-create-an-azure-storage-context"></a>Přidání kódu k vytvoření kontextu úložiště Azure

Do třídy **Program** přidejte následující metodu. K přístupu k úložišti objektů blob se používá kontext úložiště vytvořený z vašich přihlašovacích údajů k úložišti objektů blob.

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

### <a name="add-the-code-to-create-azure-media-assets-from-local-file-and-blob"></a>Přidání kódu k vytvoření mediálních prostředků Azure z místního souboru a objektu blob

Mediální procesor Content Moderator spouští úlohy v **datových zdrojích** v rámci platformy Azure Media Services.
Tyto metody vytvořit prostředky z místního souboru nebo přidružený objekt blob.

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

### <a name="add-the-code-to-scan-a-collection-of-videos-as-blobs-within-a-container"></a>Přidání kódu pro skenování kolekce videí (jako objektů BLOB) v kontejneru

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

### <a name="add-the-method-to-run-the-content-moderator-job"></a>Přidání metody pro spuštění úlohy moderátora obsahu

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

### <a name="add-helper-functions"></a>Přidání pomocné funkce

Tyto metody stáhnout výstupní soubor Content Moderator (JSON) z prostředku Azure Media Services a pomoci sledovat stav úlohy moderování tak, aby program můžete protokolovat stav spuštění do konzoly.

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

Po dokončení úlohy moderování obsahu analyzujte odpověď JSON. Skládá se z těchto prvků:

- Shrnutí informací o videu
- **Záběry** jako "**fragmenty**"
- **Klíčové snímky** jako "**události**" s **recenzeDoporučeno" (= pravda nebo nepravda)"** příznak založený na **skóre pro dospělé** a **racy**
- **start**, **duration**, **totalDuration**a **timestamp** jsou v "ticks". Vydělte **časovou osou,** abyste získali číslo v sekundách.
 
> [!NOTE]
> - `adultScore`představuje potenciální skóre přítomnosti a predikce obsahu, který může být v určitých situacích považován za sexuálně explicitní nebo dospělý.
> - `racyScore`představuje potenciální skóre přítomnosti a predikce obsahu, který může být v určitých situacích považován za sexuálně sugestivní nebo zralý.
> - `adultScore`a `racyScore` jsou mezi 0 a 1. Čím vyšší je skóre, tím vyšší je model předpovídá, že kategorie může být použitelná. Tento náhled závisí na statistickém modelu, nikoli na ručně kódovaných výsledcích. Doporučujeme testování s vlastním obsahem, abyste zjistili, jak jednotlivé kategorie odpovídají vašim požadavkům.
> - `reviewRecommended`je pravda nebo nepravda v závislosti na prahových hodnotách vnitřního skóre. Zákazníci by měli posoudit, zda mají tuto hodnotu použít, nebo se rozhodnout pro vlastní prahové hodnoty na základě svých zásad obsahu.

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

Přečtěte si, jak generovat [recenze videí](video-reviews-quickstart-dotnet.md) z výstupu moderování.

Přidejte do svých videorecenzí [moderování přepisu.](video-transcript-moderation-review-tutorial-dotnet.md)

Podívejte se na podrobný návod, jak vytvořit [kompletní video a přepis moderování řešení](video-transcript-moderation-review-tutorial-dotnet.md).

[Stáhněte si řešení sady Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) pro tento a další rychlé starty Content Moderator pro rozhraní .NET.
