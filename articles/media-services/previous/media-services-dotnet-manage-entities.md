---
title: Správa prostředků a související entity pomocí služby Media Services .NET SDK
description: Další informace o správě prostředků a přidružených entit pomocí sady Media Services SDK pro .NET.
author: juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 1bd8fd42-7306-463d-bfe5-f642802f1906
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: juliako
ms.openlocfilehash: 7cab21919eca9ba62fa57e1c6b2089c0b8e115dc
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55979968"
---
# <a name="managing-assets-and-related-entities-with-media-services-net-sdk"></a>Správa prostředků a související entity pomocí služby Media Services .NET SDK
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-manage-entities.md)
> * [REST](media-services-rest-manage-entities.md)
> 
> 

Toto téma ukazuje, jak spravovat entity Azure Media Services pomocí rozhraní .NET (starší verze)

>[!NOTE]
> Od 1. dubna 2017 se automaticky odstraní libovolný záznam úlohy ve vašem účtu, který je starší než 90 dní. Spolu s ním se odstraní přidružené záznamy úkolů, a to i v případě, že celkový počet záznamů je nižší než maximální kvóta. Například na 1. dubna 2017 se libovolný záznam úlohy ve vašem účtu, který je starší než 31. prosince 2016, se automaticky odstraní. Pokud potřebujete informace o úlohách/úkolech archivovat, můžete použít kód popsaný v tomto tématu.

## <a name="prerequisites"></a>Požadavky

Nastavte své vývojové prostředí a v souboru app.config vyplňte informace o připojení, jak je popsáno v tématu [Vývoj pro Media Services v .NET](media-services-dotnet-how-to-use.md). 

## <a name="get-an-asset-reference"></a>Získat odkaz na prostředek
Časté úlohy je k získání odkazu na existující prostředek, ve službě Media Services. Následující příklad kódu ukazuje, jak lze získat odkaz na prostředek z kolekce prostředků na serveru objekt kontextu, podle ID prostředku Následující příklad kódu používá k získání odkazu na existující objekt IAsset dotazu Linq.

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

## <a name="list-all-assets"></a>Seznam všech prostředků
S růstem počtu prostředků, které máte v úložišti je vhodné seznamu vaše prostředky. Následující příklad kódu ukazuje, jak k iteraci v rámci kolekce prostředků na objekt kontextu serveru. Každý assetu příklad kódu také zapíše některé z jeho hodnot vlastností do konzoly. Například každý prostředek může obsahovat mnoho multimediálních souborů. Příklad kódu zapíše všechny soubory spojené s každou asset.

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

## <a name="get-a-job-reference"></a>Získejte odkaz na úlohu

Při práci se zpracováním úloh v Media Services kódu, často potřebujete k získání odkazu na existující úlohy podle Id. Následující příklad kódu ukazuje, jak získat odkaz na objekt IJob z kolekce úloh.

Budete muset získat odkaz pomocí úlohy při spuštění úlohy kódování dlouhotrvající a potřeba zkontrolovat stav úlohy ve vlákně. V takových případech po návratu metody z vlákna, budete muset načíst aktualizovat odkaz na úlohu.

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
Důležité úlohy související s je seznam prostředků s jejich přidružených úloh ve službě Media Services. Následující příklad kódu ukazuje, jak zobrazit seznam každého objektu IJob a potom pro každou úlohu, zobrazí vlastnosti úlohy, všechny úlohy související se službou, všechny vstupní prostředků a všechny výstupní assety. Kód v tomto příkladu může být užitečné pro mnoho dalších úkolů. Například pokud chcete seznam výstupní assety z jednoho nebo více úloh kódování, které jste dříve spustili, tento kód ukazuje, jak získat přístup k prostředkům výstup. Až budete mít odkaz na výstupní asset, můžete pak doručit obsah do jiných uživatelů nebo aplikací stáhnout nebo poskytnutím adresy URL. 

Další informace o možnostech pro různé prostředky naleznete v tématu [prostředky doručit pomocí Media Services SDK pro .NET](media-services-deliver-streaming-content.md).

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

## <a name="list-all-access-policies"></a>Seznam všech zásad přístupu
Ve službě Media Services můžete definovat zásady přístupu na prostředek nebo jeho soubory. Zásady přístupu definuje oprávnění pro soubor nebo prostředek (jaký typ přístupu, a dobu trvání). V kódu Media Services obvykle definovat zásady přístupu tak, že vytvoření objektu IAccessPolicy a přiřadí se mu existující prostředek. Poté vytvoříte objekt ILocator, který umožňuje poskytovat přímý přístup k prostředkům ve službě Media Services. Projekt aplikace Visual Studio, který doprovází tuto řadu dokumentace obsahuje několik příkladů kódu, které ukazují, jak vytvořit a přiřadit zásady přístupu a lokátory k výpočetním prostředkům.

Následující příklad kódu ukazuje, jak zobrazit seznam všech zásad přístupu na serveru a ukazuje typ spojené s jednotlivými oprávnění. Další užitečný způsob, jak zobrazit zásady přístupu je na seznamu všechny objekty ILocator na serveru a pak pro každý Lokátor můžete zobrazit seznam svých zásad přístupu pomocí její vlastnosti AccessPolicy.

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
    
## <a name="limit-access-policies"></a>Zásady omezení přístupu 

>[!NOTE]
> Je stanovený limit 1 000 000 různých zásad AMS (třeba zásady lokátoru nebo ContentKeyAuthorizationPolicy). Pokud vždy používáte stejné dny / přístupová oprávnění, například zásady pro lokátory, které mají zůstat na místě po dlouhou dobu (zásady bez odeslání), měli byste použít stejné ID zásad. 

Obecný sady zásad můžete například vytvořit s následujícím kódem, který by spustit pouze jednou v aplikaci. ID se můžete přihlásit do souboru protokolu pro pozdější použití:

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

Potom můžete použít stávající ID ve vašem kódu takto:

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

## <a name="list-all-locators"></a>Vypsat všechny lokátory
Lokátor je adresa URL, která poskytuje přímý cestu pro přístup k prostředku, spolu s oprávněními k prostředku v souladu s definicemi zásad přístupu přidružený k lokátoru. Každý prostředek může mít na kolekci objektů ILocator s ním spojená v jeho vlastnost lokátory. Kontext serveru má také lokátory kolekce, která obsahuje všechny lokátory.

Následující příklad kódu vypíše všechny lokátory na serveru. Pro každý Lokátor zobrazuje Id pro související zásady asset a přístup. Zobrazí také typ oprávnění, datum vypršení platnosti a úplnou cestu k assetu.

Mějte na paměti, že Lokátor cesty na prostředek, je pouze základní adresu URL k prostředku. Vytvořit přímé cestu pro jednotlivé soubory, které může procházet uživatele nebo aplikace, musí váš kód přidejte cestu konkrétního souboru lokátoru cesty. Další informace o tom, jak to provést, naleznete v tématu [prostředky doručit pomocí Media Services SDK pro .NET](media-services-deliver-streaming-content.md).

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

## <a name="enumerating-through-large-collections-of-entities"></a>Výčet prostřednictvím rozsáhlých kolekcí entit
Při dotazování entit, platí limit 1 000 entit najednou vrátit, protože veřejné v2 REST omezuje výsledky dotazu na 1000 výsledky. Budete muset použít Skip a Take při vytváření výčtu prostřednictvím rozsáhlých kolekcí entit. 

Následující funkce projde všechny úlohy v poskytnutý účet Media Services. Služba Media Services vrátí 1 000 úloh v kolekci úloh. Funkce využívá Skip a Take, abyste měli jistotu, že všechny úlohy výčtu (v případě máte více než 1 000 úloh ve vašem účtu).

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

## <a name="delete-an-asset"></a>Umožňuje odstranit prostředek
Následující příklad odstraní prostředek.

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

## <a name="delete-a-job"></a>Odstranit úlohu
Pokud chcete odstranit úlohu, musíte zkontrolovat stav úlohy, jak je uvedeno ve vlastnosti stavu. Úlohy, které jsou po dokončení nebo zrušení můžete odstranit, a úlohy, které jsou v některých stavech, například ve frontě, plánované nebo zpracování, je nutné nejprve zrušit, a poté lze odstranit.

Následující příklad kódu ukazuje metodu k odstranění projektu kontrolou stavy úloh a poté odstraní při stavu byla dokončena nebo zrušena. Tento kód závisí na předchozí části v tomto tématu pro získání odkazu na projekt: Získejte odkaz na úlohu.

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


## <a name="delete-an-access-policy"></a>Odstranit zásadu přístupu
Následující příklad kódu ukazuje, jak získat odkaz na zásady přístupu na základě zásad Id a pak odstraňte zásadu.

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


## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

