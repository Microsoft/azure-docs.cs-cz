---
title: Vývoj Azure Functions se službou Media Services
description: Toto téma ukazuje, jak začít s vývojem Azure Functions pomocí služby Media Services pomocí webu Azure portal.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 51bdcb01-1846-4e1f-bd90-70020ab471b0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 428934a6e93ced56274bd27123bea9d92cd45240
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55997936"
---
# <a name="develop-azure-functions-with-media-services"></a>Vývoj Azure Functions se službou Media Services

V tomto článku se dozvíte, jak začít s vytvářením Azure Functions, která pomocí služby Media Services. Funkce Azure Functions, který je definován v tomto článku monitoruje kontejner účtu úložiště s názvem **vstupní** dostupnost nových souborů MP4. Jakmile je soubor přetáhnout do kontejneru úložiště, spustí trigger objektu blob funkce. Služba Azure functions najdete v tématu [přehled](../../azure-functions/functions-overview.md) a dalšími tématy ve **Azure functions** oddílu.

Pokud chcete zkoumat a nasazovat existující Azure Functions, která pomocí služby Azure Media Services, přečtěte si [funkcí Azure Media Services](https://github.com/Azure-Samples/media-services-dotnet-functions-integration). Toto úložiště obsahuje příklady, které používají Media Services zobrazíte pracovní postupy související s ingestovat obsahu přímo z úložiště objektů blob, kódování a zápis obsahu zpět do úložiště objektů blob. Také obsahuje příklady, jak pro monitorování oznámení úloh pomocí Webhooků a fronty Azure. Můžete také vyvíjet funkce na základě příkladů v [funkcí Azure Media Services](https://github.com/Azure-Samples/media-services-dotnet-functions-integration) úložiště. Chcete-li nasadit funkce, stiskněte **nasadit do Azure** tlačítko.

## <a name="prerequisites"></a>Požadavky

- Je nutné, abyste před vytvořením první funkce měli aktivní účet Azure. Pokud ještě nemáte účet Azure, [můžete použít bezplatné účty](https://azure.microsoft.com/free/).
- Pokud se chystáte vytvořit Azure Functions, která na vašem účtu Azure Media Services (AMS) činnostem nebo naslouchat událostem odeslaných službou Media Services, měli byste vytvořit účtu AMS, jak je popsáno [tady](media-services-portal-create-account.md).
    
## <a name="create-a-function-app"></a>Vytvoření Function App

1. Přejděte na web [Azure Portal](http://portal.azure.com) a přihlaste se pomocí účtu Azure.
2. Vytvoření aplikace function app, jak je popsáno [tady](../../azure-functions/functions-create-function-app-portal.md).

>[!NOTE]
> Účet úložiště, který zadáte **StorageConnection** proměnné prostředí (viz dál) by měl být ve stejné oblasti jako vaše aplikace.

## <a name="configure-function-app-settings"></a>Konfigurace nastavení aplikace function app

Při vytváření funkcí služby Media Services, je užitečné pro přidání proměnné prostředí, které se použijí v celé vaší funkce. Ke konfiguraci nastavení aplikace, klikněte na odkaz Konfigurovat nastavení aplikace. Další informace najdete v tématu [jak nakonfigurovat nastavení aplikace Azure Function app](../../azure-functions/functions-how-to-use-azure-function-app-settings.md). 

Funkce definované v tomto článku se předpokládá, že máte následující proměnné prostředí v nastavení aplikace:

**AMSAADTenantDomain**: Koncový bod pro tenanta Azure AD. Další informace o připojení k rozhraní API pro AMS najdete v tématu [to](media-services-use-aad-auth-to-access-ams-api.md) článku.

**AMSRESTAPIEndpoint**:  Identifikátor URI, který představuje koncový bod rozhraní REST API. 

**AMSClientId**: Azure AD ID klienta aplikace.

**AMSClientSecret**: Azure AD tajný kód klienta aplikace.

**StorageConnection**: připojení úložiště z účtu přidruženého k účtu Media Services. Tato hodnota se používá v **function.json** souboru a **run.csx** souboru (popsaných níže).

## <a name="create-a-function"></a>Vytvoření funkce

Po nasazení aplikace funkcí se nachází mezi **App Services** Azure Functions.

1. Vyberte vaši aplikaci function app a klikněte na tlačítko **novou funkci**.
2. Zvolte **C#** jazyka a **zpracování dat** scénář.
3. Zvolte **BlobTrigger** šablony. Tato funkce se aktivuje pokaždé, když se nahraje do objektu blob **vstupní** kontejneru. **Vstupní** název je zadán v **cesta**, v dalším kroku.

    ![souborů](./media/media-services-azure-functions/media-services-azure-functions004.png)

4. Jakmile vyberete **BlobTrigger**, na stránce se zobrazí některé další ovládací prvky.

    ![souborů](./media/media-services-azure-functions/media-services-azure-functions005.png)

4. Klikněte na možnost **Vytvořit**. 

## <a name="files"></a>Soubory

Funkce Azure souvisí se soubory kódu a další soubory, které jsou popsané v této části. Při použití na webu Azure portal vytvořit funkci, **function.json** a **run.csx** se vytvoří za vás. Je třeba přidání nebo nahrání **project.json** souboru. Zbývající část nabízí stručné vysvětlení jednotlivých souborů a zobrazí jejich definice.

![souborů](./media/media-services-azure-functions/media-services-azure-functions003.png)

### <a name="functionjson"></a>function.json

Soubor function.json definuje vazby funkcí a dalších nastavení konfigurace. Modul runtime používá k určení události, které chcete sledovat tento soubor a jak předat data do a vrátit data z provádění funkce. Další informace najdete v tématu [Azure functions HTTP a webhookové vazby](../../azure-functions/functions-reference.md#function-code).

>[!NOTE]
>Nastavte **zakázané** vlastnost **true** zabránit při provádění funkce. 

Nahraďte obsah existujícího souboru function.json následujícím kódem:

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

Soubor project.json obsahuje závislosti. Tady je příklad **project.json** soubor, který obsahuje požadované balíčky .NET Azure Media Services z Nuget. Pamatujte, že čísla verzí změnit s nejnovějšími aktualizacemi pro balíčky, takže ověřte, zda nejnovější verze. 

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

Toto je C# kód vaší funkce.  Funkce definována níže monitorování kontejner účtu úložiště s názvem **vstupní** (to je co byl zadaný v cestě) pro nové soubory MP4. Jakmile je soubor přetáhnout do kontejneru úložiště, spustí trigger objektu blob funkce.
    
Příklad definované v této části ukazuje 

1. jak ingestování prostředek do účtu Azure Media Services (zkopírováním objektu blob do assetu AMS) a 
2. jak se odeslat kódovací úlohu, která používá Media Encoder Standard na "Adaptivní streamování" předvolby.

V reálné situaci budete pravděpodobně chtít sledovat průběh úlohy a potom publikovat zakódovanému assetu. Další informace najdete v tématu [Webhooky Azure používá pro monitorování oznámení úloh pro Media Services](media-services-dotnet-check-job-progress-with-webhooks.md). Další příklady najdete v tématu [funkcí Azure Media Services](https://github.com/Azure-Samples/media-services-dotnet-functions-integration).  

Nahraďte obsah existujícího souboru run.csx následujícím kódem: Po dokončení definování vašich funkce klikněte na tlačítko **uložte a spusťte**.

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

Chcete-li funkci otestovat, budete muset nahrát soubor MP4 do **vstupní** kontejneru účtu úložiště, který jste zadali v připojovacím řetězci.  

1. Vyberte účet úložiště, který jste zadali v **StorageConnection** proměnné prostředí.
2. Klikněte na tlačítko **objekty BLOB**.
3. Klikněte na tlačítko **+ kontejner**. Název kontejneru **vstupní**.
4. Stisknutím klávesy **nahrát** a vyhledejte soubor .mp4, který chcete nahrát.

>[!NOTE]
> Pokud používáte aktivační událost objektů blob v plánu Consumption, může být až 10 minut zpoždění při zpracování nové objekty BLOB po aplikaci function app náramků RFID nečinnosti. Po spuštění aplikace function app, objekty BLOB jsou zpracovány okamžitě. Další informace najdete v tématu [služby Blob storage triggerů a vazeb](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob).

## <a name="next-steps"></a>Další postup

V tuto chvíli jste připraveni začít vyvíjet aplikace služby Media Services. 
 
Další podrobnosti a kompletní ukázky/řešení pomocí Azure Functions a Logic Apps v Azure Media Services k vytvoření pracovních postupů při vytváření vlastního obsahu, najdete v článku [Media Services .NET funkce integrace ukázka na Githubu](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)

Viz také [Webhooky Azure používá pro monitorování oznámení úloh pro Media Services s .NET](media-services-dotnet-check-job-progress-with-webhooks.md). 

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

