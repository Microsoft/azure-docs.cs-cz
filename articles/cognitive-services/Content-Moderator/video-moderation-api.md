---
title: Analýza videa obsah pro nežádoucím v C# – Content Moderator
titlesuffix: Azure Cognitive Services
description: Jak analyzovat obsah videa pro různé nežádoucím pomocí sady obsahu Moderator SDK pro .NET
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 7e987c1249360b14fddf8af57c61fdd1a46ee6c5
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55864787"
---
# <a name="analyze-video-content-for-objectionable-material-in-c"></a>Analýza videa obsah pro nežádoucím vC#

Tento článek obsahuje informace a ukázky kódu, které vám pomůžou začít používat [Content Moderator SDK pro .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) kontrolovat obsah pro dospělé nebo pikantní obsah videa.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

## <a name="prerequisites"></a>Požadavky
- Libovolná edice sady [Visual Studio 2015 nebo 2017](https://www.visualstudio.com/downloads/)

## <a name="set-up-azure-resources"></a>Nastavení prostředků Azure

Content Moderator moderování videa funkce je dostupná jako bezplatná verze public preview **procesor médií** v Azure Media Services (AMS). Azure Media Services je specializovaná služba Azure pro ukládání a streamování videa. 

### <a name="create-an-azure-media-services-account"></a>Vytvoření účtu Azure Media Services

Postupujte podle pokynů v [vytvoření účtu Azure Media Services](https://docs.microsoft.com/azure/media-services/media-services-portal-create-account) přihlásit k AMS odběru a vytvoření účtu služby Azure storage přidružené. V tomto účtu úložiště vytvořte nový kontejner úložiště objektů Blob.

### <a name="create-an-azure-active-directory-application"></a>Vytvoření aplikace Azure Active Directory

Přejděte do nového předplatného AMS v Azure portal a vyberte **přístup přes rozhraní API** v nabídce na straně. Vyberte **připojení k Azure Media Services pomocí instančního objektu**. Poznamenejte si hodnotu **koncový bod rozhraní REST API** pole; to budete potřebovat později.

V **aplikace Azure AD** vyberte **vytvořit nový** a pojmenujte nové registrace aplikace Azure AD (například "VideoModADApp"). Klikněte na tlačítko **Uložit** a aplikace je nakonfigurovaná. počkejte pár minut. Potom byste měli vidět vaše nové registrace aplikace v rámci **aplikace Azure AD** části stránky.

Vyberte registrace vaší aplikace a klikněte na tlačítko **spravovat aplikaci** tlačítko pod ní. Poznamenejte si hodnotu **ID aplikace** pole; to budete potřebovat později. Vyberte **nastavení** > **klíče**a zadejte popis nového klíče (například "VideoModKey"). Klikněte na tlačítko **Uložit**a potom novou hodnotu klíče. Tento řetězec zkopírujte a uložte ho někam zabezpečené.

Podrobnější pokyny k procesu výše, naleznete v tématu [Začínáme s ověřováním Azure AD](https://docs.microsoft.com/azure/media-services/media-services-portal-get-started-with-aad).

Jakmile kroky dokončíte, můžete použít procesor médií moderování videa dvěma různými způsoby.

## <a name="use-azure-media-services-explorer"></a>Použití Azure Media Services Explorer

Azure Media Services Explorer je uživatelsky přívětivé front-endu pro AMS. Vyhledejte svůj účet AMS, nahrát videa a zkontrolovat obsah pomocí procesoru media Content Moderator, použijte ho. Stáhněte si a nainstalujte ji z [Githubu](https://github.com/Azure/Azure-Media-Services-Explorer/releases), nebo se podívejte [příspěvek na blogu Azure Media Services Explorer](https://azure.microsoft.com/blog/managing-media-workflows-with-the-new-azure-media-services-explorer-tool/) Další informace.

![Průzkumník služby Azure Media Services s Content Moderatorem](images/ams-explorer-content-moderator.PNG)

## <a name="create-the-visual-studio-project"></a>Vytvoření projektu sady Visual Studio

1. V sadě Visual Studio vytvořte nový **Konzolová aplikace (.NET Framework)** projektu a pojmenujte ho **VideoModeration**. 
1. Pokud vaše řešení obsahuje i jiné projekty, vyberte tento projekt jako jediný spouštěný projekt.
1. Získejte požadované balíčky NuGet. Klikněte pravým tlačítkem na svůj projekt v Průzkumníku řešení a vyberte **Spravovat balíčky NuGet**. Potom vyhledejte a nainstalujte následující balíčky:
    - windowsazure.mediaservices
    - windowsazure.mediaservices.extensions

## <a name="add-video-moderation-code"></a>Přidejte kód moderování videa

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

### <a name="set-up-resource-references"></a>Nastavte odkazy na prostředky

Do třídy **Program** v souboru _Program.cs_ přidejte následující statická pole. Tato pole pojmout informace potřebné pro připojení k vašemu předplatnému AMS. Vyplní jejich hodnoty, které jste získali v předchozích krocích. Všimněte si, že `CLIENT_ID` je **ID aplikace** hodnotu vaše aplikace Azure AD a `CLIENT_SECRET` je hodnota "VideoModKey", který jste vytvořili pro tuto aplikaci.

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

Pokud chcete použít místní soubor videa (nejjednodušší případ), přidejte do projektu a zadejte jeho cesty, jako `INPUT_FILE` hodnotu (relativní cesty jsou relativní vzhledem k adresáři spuštění).

Budete také muset vytvořit _preset.json_ soubor v aktuálním adresáři a použilo ji k určení čísla verze. Příklad:

```JSON
{
    "version": "2.0"
}
```

### <a name="load-the-input-videos"></a>Načíst vstupní video(s)

**Hlavní** metodu **Program** třídy vytvoří objekt Context médií Azure a pak kontextu Azure Storage (v případě videí ve službě blob storage). Zbývající kód kontroluje videa z místní složky, objektů blob nebo více objektů BLOB v kontejneru služby Azure storage. Všechny možnosti můžete vyzkoušet tak další řádky kódu.

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

### <a name="create-an-azure-media-context"></a>Vytvořit objekt Context médií Azure

Do třídy **Program** přidejte následující metodu. Tento mechanismus využívá svoje přihlašovací údaje AMS k povolení komunikace pomocí služby AMS.

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

### <a name="add-the-code-to-create-an-azure-storage-context"></a>Přidejte kód k vytvoření kontextu Azure Storage

Do třídy **Program** přidejte následující metodu. Kontext úložiště, vytvoří od přihlašovacích údajů úložiště používáte pro přístup k úložišti objektů blob.

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

### <a name="add-the-code-to-create-azure-media-assets-from-local-file-and-blob"></a>Přidejte kód, který vytvořit mediální Assety Azure z místního souboru a objektů blob

Procesor médií Content Moderator spouštění úloh podle **prostředky** v rámci platformy Azure Media Services.
Tyto metody vytvoří prostředky z místního souboru nebo přidružený objekt blob.

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

### <a name="add-the-code-to-scan-a-collection-of-videos-as-blobs-within-a-container"></a>Přidejte kód pro skenování kolekce videa (jako objekty BLOB) v rámci kontejneru

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

### <a name="add-the-method-to-run-the-content-moderator-job"></a>Přidejte metodu ke spuštění úlohy Content Moderatoru

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

Tyto metody stahování prostředku Azure Media Services Content Moderator výstupního souboru (JSON) a sledovat stav úlohy moderování tak, že program může protokolovat stav spuštění do konzoly.

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

## <a name="next-steps"></a>Další postup

Zjistěte, jak generovat [video kontroly](video-reviews-quickstart-dotnet.md) z výstupu moderování.

Přidat [přepisu moderování](video-transcript-moderation-review-tutorial-dotnet.md) na video kontroly.

Podívejte se na podrobný kurz o tom, jak sestavit [dokončení řešení moderování videa a přepis](video-transcript-moderation-review-tutorial-dotnet.md).

[Stáhněte si řešení sady Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) pro tuto a další rychlé starty Content Moderator pro .NET.
