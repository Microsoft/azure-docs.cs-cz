---
title: Vývoj Azure Functions s využitím Media Services
description: V tomto tématu se dozvíte, jak začít s vývojem Azure Functions pomocí Media Services pomocí Azure Portal.
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
ms.date: 03/18/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 520ad8f68e0f995ea05456ebcf6de4c1ba3f9418
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030301"
---
# <a name="develop-azure-functions-with-media-services"></a>Vývoj Azure Functions s využitím Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

V tomto článku se dozvíte, jak začít s vytvářením Azure Functions, které používají Media Services. Funkce Azure definovaná v tomto článku sleduje kontejner účtu úložiště s názvem **vstup** pro nové soubory MP4. Po přetažení souboru do kontejneru úložiště spustí aktivační událost objektu BLOB funkci. Pokud chcete zkontrolovat Azure Functions, přečtěte si téma  [Přehled](../../azure-functions/functions-overview.md) a další témata v části **Azure Functions** .

Pokud chcete prozkoumat a nasadit existující Azure Functions, které používají Azure Media Services, podívejte se na [Media Services Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration). Toto úložiště obsahuje příklady, které používají Media Services k zobrazení pracovních postupů souvisejících s ingestování obsahu přímo z úložiště objektů blob, kódování a zápisu obsahu zpátky do úložiště objektů BLOB. Obsahuje taky příklady monitorování oznámení úloh prostřednictvím webhooků a front Azure. Můžete také vyvíjet své funkce na základě příkladů v úložišti [Media Services Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration) . Pokud chcete tyto funkce nasadit, klikněte na tlačítko **nasadit do Azure** .

## <a name="prerequisites"></a>Požadavky

- Je nutné, abyste před vytvořením první funkce měli aktivní účet Azure. Pokud ještě nemáte účet Azure, [můžete použít bezplatné účty](https://azure.microsoft.com/free/).
- Pokud budete vytvářet Azure Functions, které provádějí akce v účtu Azure Media Services (AMS) nebo naslouchat událostem odesílaným Media Services, měli byste vytvořit účet AMS, jak je popsáno [zde](media-services-portal-create-account.md).
    
## <a name="create-a-function-app"></a>Vytvoření aplikace funkcí

1. Přejděte na web [Azure Portal](https://portal.azure.com) a přihlaste se pomocí účtu Azure.
2. Vytvořte aplikaci Function App, jak je popsáno [zde](../../azure-functions/functions-create-function-app-portal.md).

>[!NOTE]
> Účet úložiště, který zadáte v proměnné prostředí **StorageConnection** (viz další krok), by měl být ve stejné oblasti jako vaše aplikace.

## <a name="configure-function-app-settings"></a>Konfigurovat nastavení aplikace Function App

Při vývoji Media Servicesch funkcí je užitečné přidat proměnné prostředí, které budou použity v rámci svých funkcí. Pokud chcete nakonfigurovat nastavení aplikace, klikněte na odkaz konfigurovat nastavení aplikace. Další informace najdete v tématu  [Konfigurace nastavení Azure Function App](../../azure-functions/functions-how-to-use-azure-function-app-settings.md). 

Funkce definovaná v tomto článku předpokládá, že máte v nastavení aplikace následující proměnné prostředí:

**AMSAADTenantDomain**: koncový bod TENANTA Azure AD. Další informace o připojení k rozhraní API AMS najdete v [tomto](media-services-use-aad-auth-to-access-ams-api.md) článku.

**AMSRESTAPIEndpoint**: identifikátor URI, který představuje koncový bod REST API. 

**AMSClientId**: ID klienta aplikace Azure AD.

**AMSClientSecret**: tajný kód klienta aplikace Azure AD.

**StorageConnection**: připojení k úložišti účtu přidruženého k Media Servicesmu účtu. Tato hodnota se používá v **function.js** souboru a spustit soubor **. csx** (popsaný níže).

## <a name="create-a-function"></a>Vytvoření funkce

Po nasazení aplikace Function App ji můžete najít mezi **App Services** Azure Functions.

1. Vyberte aplikaci Function App a klikněte na **Nová funkce**.
2. Vyberte scénář jazyka **C#** a **zpracování dat** .
3. Vyberte šablonu **BlobTrigger** . Tato funkce se aktivuje pokaždé, když se do **vstupního** kontejneru nahraje objekt BLOB. **Vstupní** název je zadán v **cestě** v dalším kroku.

    ![Snímek obrazovky se zobrazí dialogové okno Zvolit šablonu s vybraným BlobTrigger.](./media/media-services-azure-functions/media-services-azure-functions004.png)

4. Jakmile vyberete možnost **BlobTrigger**, zobrazí se na stránce některé další ovládací prvky.

    ![Snímek obrazovky se zobrazí v dialogovém okně název vaší funkce.](./media/media-services-azure-functions/media-services-azure-functions005.png)

4. Klikněte na **Vytvořit**. 

## <a name="files"></a>Soubory

Vaše funkce Azure je přidružená k souborům kódu a dalším souborům, které jsou popsány v této části. Když použijete Azure Portal k vytvoření funkce, **function.jsna** a **spusťte. csx** jsou vytvořeny za vás. Je nutné přidat nebo odeslat **project.js** do souboru. Zbytek této části poskytuje stručné vysvětlení jednotlivých souborů a ukazuje jejich definice.

![Snímek obrazovky se zobrazí ve vašem projektu soubory JSON.](./media/media-services-azure-functions/media-services-azure-functions003.png)

### <a name="functionjson"></a>function.json

function.jsv souboru definuje vazby funkcí a další nastavení konfigurace. Modul runtime pomocí tohoto souboru určí události, které se mají monitorovat, a způsob předávání dat do a návratové data z provádění funkce. Další informace najdete v tématu [Vazby protokolu HTTP služby Azure Functions a vazby webhooku](../../azure-functions/functions-reference.md#function-code).

>[!NOTE]
>Nastavte vlastnost **disabled** na **hodnotu true** , chcete-li zabránit provedení této funkce. 

Nahraďte obsah existující function.jsv souboru následujícím kódem:

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

project.jsv souboru obsahuje závislosti. Tady je příklad **project.js** souboru, který obsahuje požadované balíčky Azure Media Services .NET z nugetu. Všimněte si, že se čísla verzí mění s nejnovějšími aktualizacemi balíčků, takže byste měli potvrdit nejnovější verze. 

Přidejte následující definici pro project.js. 

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
    
### <a name="runcsx"></a>spustit. csx

Toto je kód jazyka C# pro vaši funkci.  Funkce definovaná níže sleduje kontejner účtu úložiště s názvem **input** (to je to, co bylo zadáno v cestě) pro nové soubory MP4. Po přetažení souboru do kontejneru úložiště spustí aktivační událost objektu BLOB funkci.
    
Příklad definovaný v této části ukazuje 

1. Postup ingestování prostředků do účtu Media Services (kopírování objektu blob do assetu AMS) a 
2. Jak odeslat úlohu kódování, která používá přednastavení "adaptivního streamování" Media Encoder Standard.

Ve scénáři reálného života pravděpodobně budete chtít sledovat průběh úloh a pak publikovat kódovaný Asset. Další informace najdete v tématu [použití webhooků Azure k monitorování oznámení úloh Media Services](media-services-dotnet-check-job-progress-with-webhooks.md). Další příklady najdete v tématu [Media Services Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration).  

Obsah existujícího souboru run. csx nahraďte následujícím kódem: Jakmile dokončíte definování funkce, klikněte na **Uložit a spustit**.

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

Chcete-li otestovat funkci, je třeba nahrát soubor MP4 do **vstupního** kontejneru účtu úložiště, který jste zadali v připojovacím řetězci.  

1. Vyberte účet úložiště, který jste zadali v proměnné prostředí **StorageConnection** .
2. Klikněte na **objekty blob**.
3. Klikněte na **+ Kontejner**. **Zadejte** název kontejneru.
4. Stiskněte **nahrát** a přejděte k souboru. mp4, který chcete nahrát.

>[!NOTE]
> Při použití triggeru objektu BLOB v plánu spotřeby může po dobu nečinnosti aplikace Function App docházet ke zpoždění zpracování nových objektů BLOB až na 10 minut. Po spuštění aplikace Function App se objekty blob zpracovávají okamžitě. Další informace najdete v tématu [triggery a vazby služby Blob Storage](../../azure-functions/functions-bindings-storage-blob.md).

## <a name="next-steps"></a>Další kroky

V tuto chvíli jste připraveni začít s vývojem aplikace Media Services. 
 
Další podrobnosti a kompletní ukázky/řešení použití Azure Functions a Logic Apps s Azure Media Services k vytváření vlastních pracovních postupů pro vytváření obsahu Media Services najdete v tématu [Ukázka integrace funkcí .NET Functions na GitHubu](https://github.com/Azure-Samples/media-services-dotnet-functions-integration) .

Další informace najdete také v tématu [použití webhooků Azure k monitorování oznámení úloh Media Services pomocí .NET](media-services-dotnet-check-job-progress-with-webhooks.md). 

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
