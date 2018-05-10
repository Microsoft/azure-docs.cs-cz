---
title: Správa prostředků a související entity pomocí služby Media Services .NET SDK
description: Naučte se spravovat prostředky a entit v relaci pomocí sady Media Services SDK pro .NET.
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
ms.date: 07/17/2017
ms.author: juliako
ms.openlocfilehash: af5baf3444196e5a0e8412d9ab4f019fdccb033e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="managing-assets-and-related-entities-with-media-services-net-sdk"></a>Správa prostředků a související entity pomocí služby Media Services .NET SDK
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-manage-entities.md)
> * [REST](media-services-rest-manage-entities.md)
> 
> 

Toto téma ukazuje, jak spravovat entit služby Azure Media Services pomocí rozhraní .NET. 

>[!NOTE]
> Od 1. dubna 2017 se automaticky odstraní libovolný záznam úlohy ve vašem účtu, který je starší než 90 dní. Spolu s ním se odstraní přidružené záznamy úkolů, a to i v případě, že celkový počet záznamů je nižší než maximální kvóta. Například na 1. dubna 2017 záznam všechny úlohy ve vašem účtu, který je starší než 31. prosinci 2016, se automaticky odstraní. Pokud potřebujete úloh informace archivovat, můžete použít kód popsaných v tomto tématu.

## <a name="prerequisites"></a>Požadavky

Nastavte své vývojové prostředí a v souboru app.config vyplňte informace o připojení, jak je popsáno v tématu [Vývoj pro Media Services v .NET](media-services-dotnet-how-to-use.md). 

## <a name="get-an-asset-reference"></a>Získat odkaz na prostředek
Časté úlohy je získat odkaz na prostředek existující ve službě Media Services. Následující příklad kódu ukazuje, jak můžete získat odkaz na prostředek z kolekce prostředků na serveru objekt kontextu, v závislosti na prostředek ID. Následující příklad kódu používá k získání odkazu na existující objekt IAsset dotaz Linq.

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

## <a name="list-all-assets"></a>Zobrazí seznam všech prostředků
S růstem počtu prostředků, které máte v úložišti je užitečné k zobrazení seznamu vaše prostředky. Následující příklad kódu ukazuje, jak k iteraci v rámci kolekce prostředky na objekt kontextu serveru. S každou asset příklad kódu se zapisují taky do některé z jeho hodnot vlastností ke konzole. Každý prostředek může například obsahovat mnoho mediálních souborů. Příklad kódu vypisuje všechny soubory, které jsou spojené s každou asset.

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

Při práci s zpracování úlohy v kódu Media Services, můžete často potřebují k získání odkazu ke stávající úloze podle Id. Následující příklad kódu ukazuje, jak odkazovat na objekt IJob z kolekce úloh.

Musíte získat odkaz na úlohu při spouštění úlohy kódování dlouho běžící a potřebují kontrolovat stav úlohy na vlákno. V takových případech když se metoda vrátí z vlákna, budete muset načíst aktualizovat odkaz na úlohu.

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

## <a name="list-jobs-and-assets"></a>Seznam úloh a prostředky
Seznam prostředků s jejich přidruženou úlohu ve službě Media Services je důležitá související úloha. Následující příklad kódu ukazuje, jak zobrazit každý objekt IJob a potom pro každou úlohu, se zobrazí vlastnosti úlohy, všechny související úkoly, všechny vstupní prostředky a všechny prostředky výstup. Kód v tomto příkladu může být užitečné pro mnoho dalších úkolů. Například pokud chcete do seznamu prostředků výstup z jednoho nebo více úloh kódování, které jste spustili dříve, tento kód ukazuje, jak pro přístup k výstupu prostředky. Až budete mít odkaz na výstupní asset, abyste pak zajistit obsah na jiné uživatele nebo aplikace stáhnout nebo poskytnutím adresy URL. 

Další informace o možnostech pro různé prostředky naleznete v části [poskytovat prostředky pomocí sady Media Services SDK pro .NET](media-services-deliver-streaming-content.md).

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

## <a name="list-all-access-policies"></a>Zobrazí seznam všech zásad přístupu
Ve službě Media Services můžete definovat zásady přístupu na prostředek nebo jeho soubory. Zásady přístupu definuje oprávnění pro soubor nebo prostředek (jaký typ přístupu, a jeho trvání). V kódu Media Services obvykle definovat zásady přístupu vytvořením objektu IAccessPolicy a přiřadí se mu existující prostředek. Poté vytvoříte objekt ILocator, který vám umožňuje poskytuje přímý přístup k prostředkům ve službě Media Services. Projekt Visual Studio, který doprovází tato řada dokumentace obsahuje několik příkladů kódu, které ukazují, jak vytvořit a přiřadit zásady přístupu a lokátory k prostředkům.

Následující příklad kódu ukazuje, jak zobrazit seznam všech zásad přístupu na serveru a zobrazuje typ oprávnění spojená s každým. Další užitečné možností zobrazení zásady přístupu je seznam všech objektů ILocator na serveru, a pak pro každý Lokátor můžete vytvořit seznam svých zásad přidružené přístup pomocí jeho AccessPolicy vlastnost.

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

Můžete například vytvořit obecné sady zásad s následující kód, který by spustit pouze jednou v aplikaci. ID může přihlásit do souboru protokolu pro pozdější použití:

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

Pak můžete použít existující ID ve vašem kódu takto:

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

## <a name="list-all-locators"></a>Zobrazí seznam všech lokátory
Lokátor je adresu URL, která poskytuje přímý cestu pro přístup k assetu, společně s oprávnění pro daný prostředek podle definice zásady přidružené přístup lokátoru. Každý prostředek může mít na kolekci objektů ILocator u jeho vlastnost lokátory přidruženo. Kontext server má také lokátory kolekce, která obsahuje všechny lokátory.

Následující příklad kódu zobrazuje seznam všech lokátory na serveru. Pro každý Lokátor zobrazuje Id související zásady asset a přístup. Také zobrazuje typ oprávnění, datum vypršení platnosti a úplnou cestu pro daný prostředek.

Všimněte si, že Lokátor cesty pro prostředek jenom základní adresu URL pro daný prostředek. Vytvořit přímé cestu pro jednotlivé soubory, které by mohly vyhledejte uživatele nebo aplikace, musí váš kód přidejte cestu konkrétní soubor lokátoru cesty. Další informace o tom, jak to udělat, najdete v tématu [poskytovat prostředky pomocí sady Media Services SDK pro .NET](media-services-deliver-streaming-content.md).

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
Při dotazování entity, existuje omezení 1000 entit vrátí najednou, protože veřejné v2 REST omezí výsledky dotazu a 1000 výsledky. Budete muset použít přeskočit a proveďte při vytváření výčtu prostřednictvím rozsáhlých kolekcí entit. 

Následující funkce projde všechny úlohy v zadané účtu Media Services. Služba Media Services vrátí 1000 úloh v kolekci úloh. Funkce využívá přeskočit a provést, abyste měli jistotu, že všechny úlohy budou vyčísleny (v případě, že máte více než 1 000 úloh ve vašem účtu).

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

## <a name="delete-an-asset"></a>Odstranit prostředek
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
Pokud chcete odstranit úlohu, je nutné zkontrolovat stav úlohy, které je uvedené ve vlastnosti stavu. Úlohy, které jsou po dokončení nebo zrušení můžete odstranit, zatímco úlohy, které jsou v některých stavech, jako jsou ve frontě, plánované nebo zpracování, je nutné nejprve zrušit, a pak můžete je odstranit.

Následující příklad kódu ukazuje metodu pro odstranění úlohy stavy úlohy a pak odstranění, když se stav Dokončeno nebo došlo ke zrušení. Tento kód závisí na předchozí části v tomto tématu pro získání odkaz na úlohu: Získejte odkaz na úlohu.

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


## <a name="delete-an-access-policy"></a>Odstranit zásady přístupu
Následující příklad kódu ukazuje, jak získat odkaz na zásady přístupu na základě zásad Id a pak ji odstraňte.

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

