---
title: Vývoj funkcí Azure pomocí mediálních služeb
description: Toto téma ukazuje, jak začít vyvíjet funkce Azure s mediálními službami pomocí portálu Azure.
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
ms.openlocfilehash: 618acae10b874eb5ebd5b6da7fe081368528dbd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251164"
---
# <a name="develop-azure-functions-with-media-services"></a>Vývoj funkcí Azure pomocí mediálních služeb

Tento článek ukazuje, jak začít s vytvářením funkcí Azure, které používají Mediální služby. Funkce Azure definovaná v tomto článku monitoruje kontejner účtu úložiště s názvem **input** pro nové soubory MP4. Jakmile je soubor vynechán do kontejneru úložiště, aktivační událost objektu blob spustí funkci. Informace o funkcích Azure najdete v [tématu Přehled](../../azure-functions/functions-overview.md) a další témata v části **Funkce Azure.**

Pokud chcete prozkoumat a nasadit existující funkce Azure, které používají Azure Media Services, podívejte se na [Media Services Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration). Toto úložiště obsahuje příklady, které pomocí služby Media Services zobrazují pracovní postupy související s ingestováním obsahu přímo z úložiště objektů blob, kódování a zápisu obsahu zpět do úložiště objektů blob. Obsahuje také příklady, jak sledovat oznámení o úlohách prostřednictvím WebHooks a Azure Queues. Můžete také rozvíjet funkce na základě příkladů v úložišti [Media Services Azure Functions.](https://github.com/Azure-Samples/media-services-dotnet-functions-integration) Chcete-li nasadit funkce, stiskněte tlačítko **Nasadit do Azure.**

## <a name="prerequisites"></a>Požadavky

- Je nutné, abyste před vytvořením první funkce měli aktivní účet Azure. Pokud ještě nemáte účet Azure, [můžete použít bezplatné účty](https://azure.microsoft.com/free/).
- Pokud se chystáte vytvořit funkce Azure, které provádějí akce na vašem účtu Azure Media Services (AMS) nebo poslouchat události odeslané Media Services, měli byste vytvořit účet AMS, jak je popsáno [zde](media-services-portal-create-account.md).
    
## <a name="create-a-function-app"></a>Vytvoření Function App

1. Přejděte na web [Azure Portal](https://portal.azure.com) a přihlaste se pomocí účtu Azure.
2. Vytvořte aplikaci funkcí, jak je popsáno [zde](../../azure-functions/functions-create-function-app-portal.md).

>[!NOTE]
> Účet úložiště, který zadáte v proměnné prostředí **StorageConnection** (viz další krok) by měl být ve stejné oblasti jako vaše aplikace.

## <a name="configure-function-app-settings"></a>Konfigurace nastavení aplikace funkcí

Při vývoji funkcí media services je užitečné přidat proměnné prostředí, které budou použity v rámci vašich funkcí. Chcete-li konfigurovat nastavení aplikace, klikněte na odkaz Konfigurovat nastavení aplikace. Další informace najdete v tématu [Jak nakonfigurovat nastavení aplikace Azure Function](../../azure-functions/functions-how-to-use-azure-function-app-settings.md). 

Funkce definovaná v tomto článku předpokládá, že v nastavení aplikace máte následující proměnné prostředí:

**AMSAADTenantDomain**: Koncový bod klienta Azure AD. Další informace o připojení k rozhraní AMS API naleznete v [tomto](media-services-use-aad-auth-to-access-ams-api.md) článku.

**AMSRESTAPIEndpoint**: IDENTIFIKÁTOR URI, který představuje koncový bod rozhraní REST API. 

**AMSClientId**: ID klienta aplikace Azure AD.

**AMSClientSecret**: Tajný klíč klienta aplikace Azure AD.

**StorageConnection**: připojení úložiště účtu přidruženého k účtu Mediální ch služeb. Tato hodnota se používá v souboru **function.json** a **run.csx** (popsanéníže).

## <a name="create-a-function"></a>Vytvoření funkce

Po nasazení aplikace funkce ji najdete mezi funkcemi Azure Functions **pro aplikační služby.**

1. Vyberte aplikaci funkcí a klepněte na tlačítko **Nová funkce**.
2. Zvolte jazyk **Jazyka C#** a scénář **zpracování dat.**
3. Zvolte **šablonu BlobTrigger.** Tato funkce se aktivuje při každém nahrání objektu blob do **vstupního** kontejneru. **Vstupní** název je zadán v **cestě**, v dalším kroku.

    ![files](./media/media-services-azure-functions/media-services-azure-functions004.png)

4. Jakmile vyberete **blobTrigger**, zobrazí se na stránce další ovládací prvky.

    ![files](./media/media-services-azure-functions/media-services-azure-functions005.png)

4. Klikněte na **Vytvořit**. 

## <a name="files"></a>Soubory

Vaše funkce Azure je přidružena k souborům kódu a dalším souborům, které jsou popsány v této části. Při použití portálu Azure k vytvoření funkce, **function.json** a **run.csx** jsou vytvořeny pro vás. Je třeba přidat nebo nahrát soubor **project.json.** Zbytek této části poskytuje stručné vysvětlení každého souboru a zobrazuje jejich definice.

![files](./media/media-services-azure-functions/media-services-azure-functions003.png)

### <a name="functionjson"></a>function.json

Soubor function.json definuje vazby funkcí a další nastavení konfigurace. Runtime používá tento soubor k určení událostí ke sledování a jak předat data do a vrátit data z provádění funkce. Další informace naleznete v [tématu Azure functions HTTP a webhooku vazby](../../azure-functions/functions-reference.md#function-code).

>[!NOTE]
>Nastavte **vlastnost disabled** na **hodnotu true,** aby se zabránilo spuštění funkce. 

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

Soubor project.json obsahuje závislosti. Tady je příklad souboru **project.json,** který obsahuje požadované balíčky .NET Azure Media Services od nugetu. Všimněte si, že čísla verzí se mění s nejnovějšími aktualizacemi balíčků, takže byste měli potvrdit nejnovější verze. 

Do souboru project.json přidejte následující definici. 

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

Toto je kód jazyka C# pro vaši funkci.  Funkce definovaná níže monitoruje kontejner účtu úložiště s názvem **input** (to je to, co bylo zadáno v cestě) pro nové soubory MP4. Jakmile je soubor vynechán do kontejneru úložiště, aktivační událost objektu blob spustí funkci.
    
Příklad definovaný v této části ukazuje, že 

1. jak ingestovat datový zdroj do účtu Media Services (zvládnutí objektu blob do datového zdroje AMS) a 
2. jak odeslat úlohu kódování, která používá přednastavení "Adaptivní streamování" standardu Media Encoder Standard.

V reálném životě scénář s největší pravděpodobností chcete sledovat průběh úlohy a potom publikovat kódované aktivum. Další informace najdete [v tématu Azure WebHooks ke sledování oznámení o úlohách mediálních služeb](media-services-dotnet-check-job-progress-with-webhooks.md). Další příklady najdete v tématu [Media Services Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration).  

Nahraďte obsah existujícího souboru run.csx následujícím kódem: Po dokončení definování funkce klepněte na tlačítko **Uložit a spustit**.

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

Chcete-li otestovat funkci, musíte nahrát soubor MP4 do **vstupního** kontejneru účtu úložiště, který jste zadali v připojovacím řetězci.  

1. Vyberte účet úložiště, který jste zadali v proměnné prostředí **StorageConnection.**
2. Klepněte na **položku Objekty blob**.
3. Klikněte na **+ Kontejner**. Pojmenujte **vstup**kontejneru .
4. Stiskněte **Nahrát** a vyhledejte soubor MP4, který chcete nahrát.

>[!NOTE]
> Pokud používáte aktivační událost objektu blob v plánu spotřeby, může být až 10 minutové zpoždění při zpracování nových objektů BLOB po nečinnosti aplikace funkce. Po spuštění aplikace funkce objekty BLOB jsou zpracovány okamžitě. Další informace najdete v [tématu aktivační události úložiště objektů blob a vazby](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob).

## <a name="next-steps"></a>Další kroky

V tomto okamžiku jste připraveni začít vyvíjet aplikaci mediálních služeb. 
 
Další podrobnosti a kompletní ukázky/řešení používání funkcí a logických aplikací Azure ve službě Azure Media Services k vytvoření vlastních pracovních postupů pro vytváření obsahu najdete [v tématu Ukázka integrace funkcí Media Services .NET na GitHubu.](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)

Taky najdete [v tématu Použití Azure WebHooks ke sledování oznámení o úlohách Mediálních služeb pomocí rozhraní .NET](media-services-dotnet-check-job-progress-with-webhooks.md). 

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

