---
title: Vývoj řešení Azure Functions pomocí služby Media Services
description: Toto téma ukazuje, jak začít vyvíjet Azure Functions pomocí služby Media Services pomocí portálu Azure.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 51bdcb01-1846-4e1f-bd90-70020ab471b0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/09/2017
ms.author: juliako
ms.openlocfilehash: 31a12d43ba71f1a0eacbb12887b047f2fafe3b53
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="develop-azure-functions-with-media-services"></a>Vývoj řešení Azure Functions pomocí služby Media Services

Tento článek ukazuje, jak začít s vytvářením Azure Functions, která pomocí služby Media Services. Funkce Azure, které jsou definované v tomto článku monitoruje kontejner účet úložiště s názvem **vstupní** pro nové soubory MP4. Až do kontejneru úložiště, bude vynechána soubor spouští aktivační události objektu blob funkce. Azure functions najdete v tématu [přehled](../../azure-functions/functions-overview.md) a další témata **Azure functions** části.

Pokud chcete prozkoumat a nasadit existující funkce Azure, který pomocí Azure Media Services, podívejte se na [funkce Azure Media Services](https://github.com/Azure-Samples/media-services-dotnet-functions-integration). Toto úložiště obsahuje příklady, které používají služby Media Services zobrazíte pracovní postupy související s příjem obsahu přímo z úložiště objektů blob, kódování a zápis obsahu zpět do úložiště objektů blob. Zahrnuje také příklady, jak monitorovat úlohy oznámení prostřednictvím Webhooky a fronty Azure. Také můžete vyvíjet funkcí podle příklady v [funkce Azure Media Services](https://github.com/Azure-Samples/media-services-dotnet-functions-integration) úložiště. Chcete-li nasadit funkce, stiskněte **nasadit do Azure** tlačítko.

## <a name="prerequisites"></a>Požadavky

- Je nutné, abyste před vytvořením první funkce měli aktivní účet Azure. Pokud ještě nemáte účet Azure, [můžete použít bezplatné účty](https://azure.microsoft.com/free/).
- Pokud chcete vytvořit Azure Functions, který provádět akce na vašem účtu Azure Media Services (AMS) nebo poslouchat událostí odeslaných službou Media Services, měli byste vytvořit účet AMS, jak je popsáno [zde](media-services-portal-create-account.md).
    
## <a name="create-a-function-app"></a>Vytvoření Function App

1. Přejděte na web [Azure Portal](http://portal.azure.com) a přihlaste se pomocí účtu Azure.
2. Vytvoření aplikace pro funkce, jak je popsáno [zde](../../azure-functions/functions-create-function-app-portal.md).

>[!NOTE]
> Účet úložiště, který určíte v **StorageConnection** proměnná prostředí (viz další krok) by měla být ve stejné oblasti jako vaše aplikace.

## <a name="configure-function-app-settings"></a>Konfigurovat nastavení aplikace – funkce

Při vývoji funkce Media Services, je užitečný pro přidání proměnné prostředí, které se používají v rámci funkcí. Chcete-li nakonfigurovat nastavení aplikace, klikněte na odkaz nakonfigurovat nastavení aplikace. Další informace najdete v tématu [postup konfigurace nastavení aplikace funkce Azure](../../azure-functions/functions-how-to-use-azure-function-app-settings.md). 

Funkci definované v tomto článku předpokládá, že máte následující proměnné prostředí v nastavení aplikace:

**AMSAADTenantDomain**: koncový bod tenant Azure AD. Další informace o připojení k rozhraní API pro AMS najdete v tématu [to](media-services-use-aad-auth-to-access-ams-api.md) článku.

**AMSRESTAPIEndpoint**: identifikátor URI, který představuje koncový bod REST API. 

**AMSClientId**: ID klienta aplikace Azure AD

**AMSClientSecret**: tajný klíč klienta aplikace Azure AD.

**StorageConnection**: připojení úložiště účtu přidruženého k účtu Media Services. Tato hodnota se používá **function.json** souboru a **run.csx** souboru (popsaný níže).

## <a name="create-a-function"></a>Vytvoření funkce

Po nasazení aplikace funkce najdete ji mezi **App Services** Azure Functions.

1. Vyberte svou aplikaci funkce a klikněte na tlačítko **novou funkci**.
2. Vyberte **C#** jazyk a **zpracování dat** scénář.
3. Zvolte **BlobTrigger** šablony. Tato funkce se aktivuje vždy, když se nahraje do objektu blob **vstupní** kontejneru. **Vstupní** název je zadán v **cesta**, v dalším kroku.

    ![souborů](./media/media-services-azure-functions/media-services-azure-functions004.png)

4. Jakmile vyberete **BlobTrigger**, některé další ovládací prvky zobrazí na stránce.

    ![souborů](./media/media-services-azure-functions/media-services-azure-functions005.png)

4. Klikněte na možnost **Vytvořit**. 

## <a name="files"></a>Soubory

Funkce Azure je přidružen soubory kódu a další soubory, které jsou popsané v této části. Při použití portálu Azure vytvořit funkci, **function.json** a **run.csx** jsou vytvořené pro vás. Je třeba přidat nebo odeslání **project.json** souboru. Zbývající část tohoto oddílu stručné vysvětlení jednotlivých souborů, zobrazuje a jejich definice.

![souborů](./media/media-services-azure-functions/media-services-azure-functions003.png)

### <a name="functionjson"></a>function.json

Soubor function.json definuje vazby funkcí a dalších nastavení konfigurace. Modul runtime používá tento soubor k určení události k monitorování a jak předat data do a ze spuštění funkce vrátit data. Další informace najdete v tématu [Azure functions vazby HTTP a webhooku](../../azure-functions/functions-reference.md#function-code).

>[!NOTE]
>Nastavit **zakázáno** vlastnost **true** zabránit funkci spouštěna. 

Nahraďte obsah souboru existující function.json následujícím kódem:

```json
{
  "bindings": [
    {
      "name": "myBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "input/{filename}.mp4",
      "connection": "ConnectionString"
    }
  ],
  "disabled": false
}
```

### <a name="projectjson"></a>project.json

Soubor project.json obsahuje závislosti. Tady je příklad **project.json** soubor, který zahrnuje požadované balíčky .NET Azure Media Services z Nuget. Všimněte si, že číslo verze změnu s nejnovějšími aktualizacemi do balíčků, tak ověřte, zda nejnovější verze. 

Přidejte následující definice do souboru project.json. 

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "windowsazure.mediaservices": "4.0.0.4",
        "windowsazure.mediaservices.extensions": "4.0.0.4",
        "Microsoft.IdentityModel.Clients.ActiveDirectory": "3.13.1",
        "Microsoft.IdentityModel.Protocol.Extensions": "1.0.2.206221351"
      }
    }
   }
}

```
    
### <a name="runcsx"></a>run.csx

Toto je kód C# pro funkce.  Funkce definovaná níže monitorování kontejner účet úložiště s názvem **vstupní** (který je co byl zadaný v cestě) pro nové soubory MP4. Až do kontejneru úložiště, bude vynechána soubor spouští aktivační události objektu blob funkce.
    
Příklad definované v této části ukazuje 

1. Postup ingestování prostředek do účtu Media Services (zkopírováním do objektu blob do assetu AMS) a 
2. jak odeslat kódování úlohu, která využívá Media Encoder Standard na "Adaptivní datové proudy" přednastavení.

Ve scénáři reálného života, budete pravděpodobně chtít sledovat průběh úlohy a pak publikujte zakódovanému assetu. Další informace najdete v tématu [Webhooky Azure použijte ke sledování služby Media Services úlohy oznámení](media-services-dotnet-check-job-progress-with-webhooks.md). Další příklady najdete v tématu [funkce Azure Media Services](https://github.com/Azure-Samples/media-services-dotnet-functions-integration).  

Nahraďte obsah souboru existující run.csx následujícím kódem: Po dokončení definování funkce klikněte na tlačítko **uložte a spusťte**.

```csharp
#r "Microsoft.WindowsAzure.Storage"
#r "Newtonsoft.Json"
#r "System.Web"

using System;
using System.Net;
using System.Net.Http;
using Newtonsoft.Json;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.IO;
using System.Web;
using Microsoft.Azure;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.Azure.WebJobs;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
  
// Read values from the App.config file.

static readonly string _AADTenantDomain = Environment.GetEnvironmentVariable("AMSAADTenantDomain");
static readonly string _RESTAPIEndpoint = Environment.GetEnvironmentVariable("AMSRESTAPIEndpoint");
 
static readonly string _mediaservicesClientId = Environment.GetEnvironmentVariable("AMSClientId");
static readonly string _mediaservicesClientSecret = Environment.GetEnvironmentVariable("AMSClientSecret");

static readonly string _connectionString = Environment.GetEnvironmentVariable("ConnectionString");  

private static CloudMediaContext _context = null;
private static CloudStorageAccount _destinationStorageAccount = null;

public static void Run(CloudBlockBlob myBlob, string fileName, TraceWriter log)
{
    // NOTE that the variables {fileName} here come from the path setting in function.json
    // and are passed into the  Run method signature above. We can use this to make decisions on what type of file
    // was dropped into the input container for the function. 

    // No need to do any Retry strategy in this function, By default, the SDK calls a function up to 5 times for a 
    // given blob. If the fifth try fails, the SDK adds a message to a queue named webjobs-blobtrigger-poison.

    log.Info($"C# Blob trigger function processed: {fileName}.mp4");
    log.Info($"Media Services REST endpoint : {_RESTAPIEndpoint}");

    try
    {
        AzureAdTokenCredentials tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain,
                            new AzureAdClientSymmetricKey(_mediaservicesClientId, _mediaservicesClientSecret),
                            AzureEnvironments.AzureCloudEnvironment);
 
        AzureAdTokenProvider tokenProvider = new AzureAdTokenProvider(tokenCredentials);
 
        _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

        IAsset newAsset = CreateAssetFromBlob(myBlob, fileName, log).GetAwaiter().GetResult();

        // Step 2: Create an Encoding Job

        // Declare a new encoding job with the Standard encoder
        IJob job = _context.Jobs.Create("Azure Function - MES Job");

        // Get a media processor reference, and pass to it the name of the 
        // processor to use for the specific task.
        IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

        // Create a task with the encoding details, using a custom preset
        ITask task = job.Tasks.AddNew("Encode with Adaptive Streaming",
            processor,
            "Adaptive Streaming",
            TaskOptions.None); 

        // Specify the input asset to be encoded.
        task.InputAssets.Add(newAsset);

        // Add an output asset to contain the results of the job. 
        // This output is specified as AssetCreationOptions.None, which 
        // means the output asset is not encrypted. 
        task.OutputAssets.AddNew(fileName, AssetCreationOptions.None);

        job.Submit();
        log.Info("Job Submitted");

    }
    catch (Exception ex)
    {
        log.Error("ERROR: failed.");
        log.Info($"StackTrace : {ex.StackTrace}");
        throw ex;
    }
}

private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
{
    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

    if (processor == null)
    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

    return processor;
}

public static async Task<IAsset> CreateAssetFromBlob(CloudBlockBlob blob, string assetName, TraceWriter log){
    IAsset newAsset = null;

    try{
        Task<IAsset> copyAssetTask = CreateAssetFromBlobAsync(blob, assetName, log);
        newAsset = await copyAssetTask;
        log.Info($"Asset Copied : {newAsset.Id}");
    }
    catch(Exception ex){
        log.Info("Copy Failed");
        log.Info($"ERROR : {ex.Message}");
        throw ex;
    }

    return newAsset;
}

/// <summary>
/// Creates a new asset and copies blobs from the specifed storage account.
/// </summary>
/// <param name="blob">The specified blob.</param>
/// <returns>The new asset.</returns>
public static async Task<IAsset> CreateAssetFromBlobAsync(CloudBlockBlob blob, string assetName, TraceWriter log)
{
     //Get a reference to the storage account that is associated with the Media Services account. 
    _destinationStorageAccount = CloudStorageAccount.Parse(_connectionString);

    // Create a new asset. 
    var asset = _context.Assets.Create(blob.Name, AssetCreationOptions.None);
    log.Info($"Created new asset {asset.Name}");

    IAccessPolicy writePolicy = _context.AccessPolicies.Create("writePolicy",
    TimeSpan.FromHours(4), AccessPermissions.Write);
    ILocator destinationLocator = _context.Locators.CreateLocator(LocatorType.Sas, asset, writePolicy);
    CloudBlobClient destBlobStorage = _destinationStorageAccount.CreateCloudBlobClient();

    // Get the destination asset container reference
    string destinationContainerName = (new Uri(destinationLocator.Path)).Segments[1];
    CloudBlobContainer assetContainer = destBlobStorage.GetContainerReference(destinationContainerName);

    try{
    assetContainer.CreateIfNotExists();
    }
    catch (Exception ex)
    {
    log.Error ("ERROR:" + ex.Message);
    }

    log.Info("Created asset.");

    // Get hold of the destination blob
    CloudBlockBlob destinationBlob = assetContainer.GetBlockBlobReference(blob.Name);

    // Copy Blob
    try
    {
    using (var stream = await blob.OpenReadAsync()) 
    {            
        await destinationBlob.UploadFromStreamAsync(stream);          
    }

    log.Info("Copy Complete.");

    var assetFile = asset.AssetFiles.Create(blob.Name);
    assetFile.ContentFileSize = blob.Properties.Length;
    assetFile.IsPrimary = true;
    assetFile.Update();
    asset.Update();
    }
    catch (Exception ex)
    {
    log.Error(ex.Message);
    log.Info (ex.StackTrace);
    log.Info ("Copy Failed.");
    throw;
    }

    destinationLocator.Delete();
    writePolicy.Delete();

    return asset;
}
```

## <a name="test-your-function"></a>Otestování funkce

Chcete-li funkci otestovat, je potřeba nahrát soubor MP4 do **vstupní** kontejneru účtu úložiště, který jste zadali v připojovacím řetězci.  

1. Vyberte účet úložiště, který jste zadali v **StorageConnection** proměnné prostředí.
2. Klikněte na tlačítko **objekty BLOB**.
3. Klikněte na tlačítko **+ kontejner**. Název kontejneru **vstupní**.
4. Stiskněte klávesu **nahrát** a vyhledejte soubor MP4, který chcete nahrát.

>[!NOTE]
> Pokud používáte aktivační události objektu blob na plánu spotřeby, může být až 10 minut zpoždění při zpracování nové objekty BLOB po aplikaci funkce přešel nečinnosti. Po aplikaci funkce běží, objekty BLOB jsou zpracovávány okamžitě. Další informace najdete v tématu [objektu Blob úložiště triggerů a vazeb](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob#blob-storage-triggers-and-bindings).

## <a name="next-steps"></a>Další postup

V tomto okamžiku jste připravení začít s vývojem aplikací Media Services. 
 
Další podrobnosti a kompletní ukázky nebo řešení pomocí Azure Functions a Logic Apps pomocí Azure Media Services k vytváření pracovních postupů vlastní vytváření obsahu najdete v tématu [Media Services .NET funkce integrace ukázce na Githubu](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)

Další informace naleznete v [Webhooky Azure použijte ke sledování úloh oznámení Media Services pomocí rozhraní .NET](media-services-dotnet-check-job-progress-with-webhooks.md). 

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

