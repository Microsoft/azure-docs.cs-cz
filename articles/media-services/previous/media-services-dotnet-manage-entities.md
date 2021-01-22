---
title: Správa prostředků a souvisejících entit pomocí sady Media Services .NET SDK
description: Naučte se spravovat prostředky a související entity pomocí sady Media Services SDK pro .NET.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 1bd8fd42-7306-463d-bfe5-f642802f1906
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: d90e48d15e20b21f4c6032547517b3190cb46085
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98695607"
---
# <a name="managing-assets-and-related-entities-with-media-services-net-sdk"></a>Správa prostředků a souvisejících entit pomocí sady Media Services .NET SDK

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-manage-entities.md)
> * [REST](media-services-rest-manage-entities.md)
> 
> 

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi [Media Services V3](../latest/index.yml). Podívejte se taky na [pokyny k migraci z v2 na V3](../latest/migrate-v-2-v-3-migration-introduction.md) .

V tomto tématu se dozvíte, jak spravovat Azure Media Services entit pomocí .NET.

Od 1. dubna 2017 se automaticky odstraní libovolný záznam úlohy ve vašem účtu, který je starší než 90 dní. Spolu s ním se odstraní přidružené záznamy úkolů, a to i v případě, že celkový počet záznamů je nižší než maximální kvóta. Například od 1. dubna 2017 se automaticky odstraní libovolný záznam úlohy ve vašem účtu, který je starší než 31. prosince 2016. Pokud potřebujete archivovat informace o úloze nebo úloze, můžete použít kód popsaný v tomto tématu.

## <a name="prerequisites"></a>Požadavky

Nastavte své vývojové prostředí a v souboru app.config vyplňte informace o připojení, jak je popsáno v tématu [Vývoj pro Media Services v .NET](media-services-dotnet-how-to-use.md). 

## <a name="get-an-asset-reference"></a>Získat odkaz na prostředek
Častým úkolem je získat odkaz na existující Asset v Media Services. Následující příklad kódu ukazuje, jak lze získat odkaz na prostředek z kolekce assets v objektu kontextu serveru na základě ID assetu. Následující příklad kódu používá dotaz LINQ k získání odkazu na existující objekt IAsset.

```csharp
    static IAsset GetAsset(string assetId)
    {
        // Use a LINQ Select query to get an asset.
        var assetInstance =
            from a in _context.Assets
            where a.Id == assetId
            select a;
        // Reference the asset as an IAsset.
        IAsset asset = assetInstance.FirstOrDefault();

        return asset;
    }
```

## <a name="list-all-assets"></a>Vypsat všechny prostředky
Vzhledem k nárůstu počtu prostředků, které máte v úložišti, je užitečné si vypsat své prostředky. Následující příklad kódu ukazuje, jak iterovat kolekci assets v objektu kontextu serveru. U každého assetu příklad kódu zapisuje také některé jeho hodnoty vlastností do konzoly. Každý Asset například může obsahovat mnoho mediálních souborů. Příklad kódu zapisuje všechny soubory přidružené k jednotlivým assetům.

```csharp
    static void ListAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);

        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();

        foreach (IAsset asset in _context.Assets)
        {
            // Display the collection of assets.
            builder.AppendLine("");
            builder.AppendLine("******ASSET******");
            builder.AppendLine("Asset ID: " + asset.Id);
            builder.AppendLine("Name: " + asset.Name);
            builder.AppendLine("==============");
            builder.AppendLine("******ASSET FILES******");

            // Display the files associated with each asset. 
            foreach (IAssetFile fileItem in asset.AssetFiles)
            {
                builder.AppendLine("Name: " + fileItem.Name);
                builder.AppendLine("Size: " + fileItem.ContentFileSize);
                builder.AppendLine("==============");
            }
        }

        // Display output in console.
        Console.Write(builder.ToString());
    }
```

## <a name="get-a-job-reference"></a>Získat odkaz na úlohu

Když pracujete se zpracováním úloh v Media Services kódu, často potřebujete získat odkaz na existující úlohu na základě ID. Následující příklad kódu ukazuje, jak získat odkaz na objekt IJob z kolekce Jobs.

Při spuštění dlouhotrvající úlohy kódování může být nutné získat odkaz na úlohu a je třeba kontrolovat stav úlohy ve vlákně. V takových případech, pokud se metoda vrátí z vlákna, je nutné načíst aktualizovaný odkaz na úlohu.

```csharp
    static IJob GetJob(string jobId)
    {
        // Use a Linq select query to get an updated 
        // reference by Id. 
        var jobInstance =
            from j in _context.Jobs
            where j.Id == jobId
            select j;
        // Return the job reference as an Ijob. 
        IJob job = jobInstance.FirstOrDefault();

        return job;
    }
```

## <a name="list-jobs-and-assets"></a>Seznam úloh a prostředků
Významnou související úlohou je vypsat prostředky s přidruženou úlohou v Media Services. Následující příklad kódu ukazuje, jak zobrazit seznam jednotlivých objektů IJob a pak pro každou úlohu zobrazit vlastnosti úlohy, všechny související úlohy, všechny vstupní prostředky a všechny výstupní prostředky. Kód v tomto příkladu může být užitečný pro celou řadu dalších úloh. Například pokud chcete vypsat výstupní prostředky z jedné nebo více úloh kódování, které jste spustili dříve, tento kód ukazuje, jak získat přístup k výstupním assetům. Pokud máte odkaz na výstupní prostředek, můžete obsah doručovat ostatním uživatelům nebo aplikacím stažením nebo zadáním adres URL. 

Další informace o možnostech poskytování prostředků najdete v tématu [Delivery assets with Media Services SDK for .NET](media-services-deliver-streaming-content.md).

```csharp
    // List all jobs on the server, and for each job, also list 
    // all tasks, all input assets, all output assets.

    static void ListJobsAndAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);

        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();

        foreach (IJob job in _context.Jobs)
        {
            // Display the collection of jobs on the server.
            builder.AppendLine("");
            builder.AppendLine("******JOB*******");
            builder.AppendLine("Job ID: " + job.Id);
            builder.AppendLine("Name: " + job.Name);
            builder.AppendLine("State: " + job.State);
            builder.AppendLine("Order: " + job.Priority);
            builder.AppendLine("==============");


            // For each job, display the associated tasks (a job  
            // has one or more tasks). 
            builder.AppendLine("******TASKS*******");
            foreach (ITask task in job.Tasks)
            {
                builder.AppendLine("Task Id: " + task.Id);
                builder.AppendLine("Name: " + task.Name);
                builder.AppendLine("Progress: " + task.Progress);
                builder.AppendLine("Configuration: " + task.Configuration);
                if (task.ErrorDetails != null)
                {
                    builder.AppendLine("Error: " + task.ErrorDetails);
                }
                builder.AppendLine("==============");
            }

            // For each job, display the list of input media assets.
            builder.AppendLine("******JOB INPUT MEDIA ASSETS*******");
            foreach (IAsset inputAsset in job.InputMediaAssets)
            {

                if (inputAsset != null)
                {
                    builder.AppendLine("Input Asset Id: " + inputAsset.Id);
                    builder.AppendLine("Name: " + inputAsset.Name);
                    builder.AppendLine("==============");
                }
            }

            // For each job, display the list of output media assets.
            builder.AppendLine("******JOB OUTPUT MEDIA ASSETS*******");
            foreach (IAsset theAsset in job.OutputMediaAssets)
            {
                if (theAsset != null)
                {
                    builder.AppendLine("Output Asset Id: " + theAsset.Id);
                    builder.AppendLine("Name: " + theAsset.Name);
                    builder.AppendLine("==============");
                }
            }

        }

        // Display output in console.
        Console.Write(builder.ToString());
    }
```

## <a name="list-all-access-policies"></a>Vypsat všechny zásady přístupu
V Media Services můžete definovat zásady přístupu pro Asset nebo jeho soubory. Zásady přístupu definují oprávnění k souboru nebo prostředku (jaký typ přístupu a dobu trvání). V kódu Media Services obvykle definujete zásady přístupu vytvořením objektu IAccessPolicy a pak ho přidružíte k existujícímu assetu. Pak vytvoříte objekt ILocator, který vám umožní poskytnout přímý přístup k prostředkům v Media Services. Projekt sady Visual Studio, který je součástí této řady dokumentace, obsahuje několik příkladů kódu, které ukazují, jak vytvořit a přiřadit zásady přístupu a Lokátory k assetům.

Následující příklad kódu ukazuje, jak zobrazit seznam všech zásad přístupu na serveru a zobrazuje typ oprávnění přidružených k jednotlivýmu. Dalším užitečným způsobem, jak zobrazit zásady přístupu, je vypsat všechny objekty ILocator na serveru a potom pro jednotlivé Lokátory zobrazit seznam přidružených zásad přístupu pomocí vlastnosti AccessPolicy.

```csharp
    static void ListAllPolicies()
    {
        foreach (IAccessPolicy policy in _context.AccessPolicies)
        {
            Console.WriteLine("");
            Console.WriteLine("Name:  " + policy.Name);
            Console.WriteLine("ID:  " + policy.Id);
            Console.WriteLine("Permissions: " + policy.Permissions);
            Console.WriteLine("==============");

        }
    }
```
    
## <a name="limit-access-policies"></a>Omezení zásad přístupu 

>[!NOTE]
> Je stanovený limit 1 000 000 různých zásad AMS (třeba zásady lokátoru nebo ContentKeyAuthorizationPolicy). Pokud vždy používáte stejné dny / přístupová oprávnění, například zásady pro lokátory, které mají zůstat na místě po dlouhou dobu (zásady bez odeslání), měli byste použít stejné ID zásad. 

Můžete například vytvořit obecnou sadu zásad s následujícím kódem, který se spustí pouze jednou ve vaší aplikaci. Můžete protokolovat ID do souboru protokolu pro pozdější použití:

```csharp
    double year = 365.25;
    double week = 7;
    IAccessPolicy policyYear = _context.AccessPolicies.Create("One Year", TimeSpan.FromDays(year), AccessPermissions.Read);
    IAccessPolicy policy100Year = _context.AccessPolicies.Create("Hundred Years", TimeSpan.FromDays(year * 100), AccessPermissions.Read);
    IAccessPolicy policyWeek = _context.AccessPolicies.Create("One Week", TimeSpan.FromDays(week), AccessPermissions.Read);

    Console.WriteLine("One year policy ID is: " + policyYear.Id);
    Console.WriteLine("100 year policy ID is: " + policy100Year.Id);
    Console.WriteLine("One week policy ID is: " + policyWeek.Id);
```

Pak můžete použít existující ID v kódu takto:

```csharp
    const string policy1YearId = "nb:pid:UUID:2a4f0104-51a9-4078-ae26-c730f88d35cf";


    // Get the standard policy for 1 year read only
    var tempPolicyId = from b in _context.AccessPolicies
                       where b.Id == policy1YearId
                       select b;
    IAccessPolicy policy1Year = tempPolicyId.FirstOrDefault();

    // Get the existing asset
    var tempAsset = from a in _context.Assets
                where a.Id == assetID
                select a;
    IAsset asset = tempAsset.SingleOrDefault();

    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
        policy1Year,
        DateTime.UtcNow.AddMinutes(-5));
    Console.WriteLine("The locator base path is " + originLocator.BaseUri.ToString());
```

## <a name="list-all-locators"></a>Vypsat všechny Lokátory
Lokátor je adresa URL, která poskytuje přímou cestu k přístupu k assetu, a oprávnění k assetu, jak je definovala zásada přístupu k lokátoru. Ke každému assetu může být přidružena kolekce objektů ILocator, které jsou k němu přidruženy ve vlastnosti lokátory. Kontext serveru má také kolekci lokátorů, která obsahuje všechny lokátory.

Následující příklad kódu uvádí všechny lokátory na serveru. Pro každý Lokátor zobrazuje ID souvisejícího prostředku a zásady přístupu. Zobrazuje také typ oprávnění, datum vypršení platnosti a úplnou cestu k assetu.

Všimněte si, že cesta lokátoru k prostředku je jenom základní adresa URL assetu. Chcete-li vytvořit přímou cestu k jednotlivým souborům, které může uživatel nebo aplikace Procházet, váš kód musí do cesty lokátoru přidat konkrétní cestu k souboru. Další informace o tom, jak to provést, naleznete v tématu [Delivery assets to Media Services SDK for .NET](media-services-deliver-streaming-content.md).

```csharp
    static void ListAllLocators()
    {
        foreach (ILocator locator in _context.Locators)
        {
            Console.WriteLine("***********");
            Console.WriteLine("Locator Id: " + locator.Id);
            Console.WriteLine("Locator asset Id: " + locator.AssetId);
            Console.WriteLine("Locator access policy Id: " + locator.AccessPolicyId);
            Console.WriteLine("Access policy permissions: " + locator.AccessPolicy.Permissions);
            Console.WriteLine("Locator expiration: " + locator.ExpirationDateTime);
            // The locator path is the base or parent path (with included permissions) to access  
            // the media content of an asset. To create a full URL to a specific media file, take 
            // the locator path and then append a file name and info as needed.  
            Console.WriteLine("Locator base path: " + locator.Path);
            Console.WriteLine("");
        }
    }
```

## <a name="enumerating-through-large-collections-of-entities"></a>Vytváření výčtu velkých kolekcí entit
Při dotazování entit existuje limit 1000 entit vrácených v jednom okamžiku, protože veřejné REST v2 omezuje výsledky dotazu na 1000 výsledků. Při vytváření výčtu velkých kolekcí entit musíte použít Skip a vzít. 

Následující funkce projde všemi úlohami v zadaném Media Servicesm účtu. Media Services vrátí 1000 úlohy v kolekci Jobs. Tato funkce využívá funkci Skip a k tomu, aby se zajistilo, že jsou vyhodnoceny všechny úlohy (Pokud máte ve svém účtu více než 1000 úloh).

```csharp
    static void ProcessJobs()
    {
        try
        {

            int skipSize = 0;
            int batchSize = 1000;
            int currentBatch = 0;

            while (true)
            {
                // Loop through all Jobs (1000 at a time) in the Media Services account
                IQueryable _jobsCollectionQuery = _context.Jobs.Skip(skipSize).Take(batchSize);
                foreach (IJob job in _jobsCollectionQuery)
                {
                    currentBatch++;
                    Console.WriteLine("Processing Job Id:" + job.Id);
                }

                if (currentBatch == batchSize)
                {
                    skipSize += batchSize;
                    currentBatch = 0;
                }
                else
                {
                    break;
                }
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.Message);
        }
    }
```

## <a name="delete-an-asset"></a>Odstranění Assetu
Následující příklad odstraní Asset.

```csharp
    static void DeleteAsset( IAsset asset)
    {
        // delete the asset
        asset.Delete();

        // Verify asset deletion
        if (GetAsset(asset.Id) == null)
            Console.WriteLine("Deleted the Asset");

    }
```

## <a name="delete-a-job"></a>Odstranění úlohy
Chcete-li odstranit úlohu, je nutné zjistit stav úlohy, jak je uvedeno ve vlastnosti stav. Úlohy, které jsou dokončené nebo zrušené, je možné odstranit, zatímco úlohy, které jsou v některých dalších stavech, jako je například zařazení do fronty, naplánované nebo zpracování, je nutné nejprve zrušit a pak je odstranit.

Následující příklad kódu ukazuje metodu pro odstranění úlohy kontrolou stavu úlohy a následným odstraněním, když je stav dokončen nebo zrušen. Tento kód závisí na předchozí části tohoto tématu pro získání odkazu na úlohu: získat odkaz na úlohu.

```csharp
    static void DeleteJob(string jobId)
    {
        bool jobDeleted = false;

        while (!jobDeleted)
        {
            // Get an updated job reference.  
            IJob job = GetJob(jobId);

            // Check and handle various possible job states. You can 
            // only delete a job whose state is Finished, Error, or Canceled.   
            // You can cancel jobs that are Queued, Scheduled, or Processing,  
            // and then delete after they are canceled.
            switch (job.State)
            {
                case JobState.Finished:
                case JobState.Canceled:
                case JobState.Error:
                    // Job errors should already be logged by polling or event 
                    // handling methods such as CheckJobProgress or StateChanged.
                    // You can also call job.DeleteAsync to do async deletes.
                    job.Delete();
                    Console.WriteLine("Job has been deleted.");
                    jobDeleted = true;
                    break;
                case JobState.Canceling:
                    Console.WriteLine("Job is cancelling and will be deleted "
                        + "when finished.");
                    Console.WriteLine("Wait while job finishes canceling...");
                    Thread.Sleep(5000);
                    break;
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    job.Cancel();
                    Console.WriteLine("Job is scheduled or processing and will "
                        + "be deleted.");
                    break;
                default:
                    break;
            }

        }
    }
```


## <a name="delete-an-access-policy"></a>Odstranění zásady přístupu
Následující příklad kódu ukazuje, jak získat odkaz na zásadu přístupu na základě ID zásad a pak zásadu odstranit.

```csharp
    static void DeleteAccessPolicy(string existingPolicyId)
    {
        // To delete a specific access policy, get a reference to the policy.  
        // based on the policy Id passed to the method.
        var policyInstance =
                from p in _context.AccessPolicies
                where p.Id == existingPolicyId
                select p;
        IAccessPolicy policy = policyInstance.FirstOrDefault();

        policy.Delete();

    }
```


## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
