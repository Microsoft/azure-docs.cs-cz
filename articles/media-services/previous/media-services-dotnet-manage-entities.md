---
title: Správa datových zdrojů a souvisejících subjektů pomocí sady Media Services .NET SDK
description: Zjistěte, jak spravovat datové zdroje a související entity pomocí sady Media Services SDK pro rozhraní .NET.
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
ms.openlocfilehash: a686465b0006c2e9aac6e06cb4ab12d30921e8c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251138"
---
# <a name="managing-assets-and-related-entities-with-media-services-net-sdk"></a>Správa datových zdrojů a souvisejících subjektů pomocí sady Media Services .NET SDK
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-manage-entities.md)
> * [Odpočinku](media-services-rest-manage-entities.md)
> 
> 

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Viz také [pokyny k migraci z v2 na v3](../latest/migrate-from-v2-to-v3.md)

Toto téma ukazuje, jak spravovat entity Azure Media Services s rozhraním .NET.

Od 1. dubna 2017 se automaticky odstraní libovolný záznam úlohy ve vašem účtu, který je starší než 90 dní. Spolu s ním se odstraní přidružené záznamy úkolů, a to i v případě, že celkový počet záznamů je nižší než maximální kvóta. Dubna 2017 bude například automaticky odstraněn veškerý záznam úlohy ve vašem účtu starší než 31. Pokud potřebujete archivovat informace o úloze/úloze, můžete použít kód popsaný v tomto tématu.

## <a name="prerequisites"></a>Požadavky

Nastavte své vývojové prostředí a v souboru app.config vyplňte informace o připojení, jak je popsáno v tématu [Vývoj pro Media Services v .NET](media-services-dotnet-how-to-use.md). 

## <a name="get-an-asset-reference"></a>Získání odkazu na majetek
Častým úkolem je získat odkaz na existující datový zdroj ve službě Media Services. Následující příklad kódu ukazuje, jak můžete získat odkaz na datový zdroj z kolekce Asset na objektu kontextu serveru na základě ID prostředku. Následující příklad kódu používá dotaz Linq k získání odkazu na existující objekt IAsset.

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

## <a name="list-all-assets"></a>Vypsat všechny datové zdroje
S tím, jak roste počet datových zdrojů, které máte v úložišti, je užitečné uvést vaše prostředky. Následující příklad kódu ukazuje, jak iterate prostřednictvím kolekce Assets na objekt kontextu serveru. S každým datovým zdrojem příklad kódu také zapíše některé hodnoty vlastností do konzoly. Každý datový zdroj může například obsahovat mnoho mediálních souborů. Příklad kódu zapíše všechny soubory přidružené ke každému datovému zdroji.

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

## <a name="get-a-job-reference"></a>Získat pracovní referenci

Při práci se zpracováním úloh v kódu služby Media Services často potřebujete získat odkaz na existující úlohu na základě ID. Následující příklad kódu ukazuje, jak získat odkaz na objekt IJob z kolekce Úlohy.

Možná budete muset získat odkaz na úlohu při spuštění dlouhotrvající úlohy kódování a je třeba zkontrolovat stav úlohy ve vlákně. V případech, jako je tento, když se metoda vrátí z vlákna, je třeba načíst aktualizovaný odkaz na úlohu.

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

## <a name="list-jobs-and-assets"></a>Seznam úloh a datových zdrojů
Důležitým souvisejícím úkolem je vypsat datové zdroje s jejich přidruženou úlohou ve službě Media Services. Následující příklad kódu ukazuje, jak vypsat každý objekt IJob a potom pro každou úlohu zobrazí vlastnosti o úloze, všech souvisejících úkolech, všech vstupních datových zdrojů a všech výstupních datových zdrojů. Kód v tomto příkladu může být užitečné pro mnoho dalších úkolů. Například pokud chcete seznam výstupních datových zdrojů z jedné nebo více úloh kódování, které jste spustili dříve, tento kód ukazuje, jak získat přístup k výstupním prostředkům. Pokud máte odkaz na výstupní datový zdroj, můžete obsah doručit jiným uživatelům nebo aplikacím jeho stažením nebo poskytnutím adres URL. 

Další informace o možnostech doručování datových zdrojů naleznete v [tématu Deliver Assets with the Media Services SDK for .NET](media-services-deliver-streaming-content.md).

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
Ve službě Media Services můžete definovat zásady přístupu k datovému zdroji nebo jeho souborům. Zásady přístupu definují oprávnění pro soubor nebo prostředek (jaký typ přístupu a doba trvání). V kódu mediálních služeb obvykle definujete zásadu přístupu vytvořením objektu IAccessPolicy a jeho následným spojením s existujícím datovým zdrojem. Potom vytvoříte objekt ILocator, který umožňuje poskytovat přímý přístup k datovým zdrojům ve službě Media Services. Projekt sady Visual Studio, který doprovází tuto řadu dokumentace obsahuje několik příkladů kódu, které ukazují, jak vytvořit a přiřadit zásady přístupu a lokátory k prostředkům.

Následující příklad kódu ukazuje, jak vypsat všechny zásady přístupu na serveru a zobrazuje typ oprávnění přidružených k jednotlivým. Dalším užitečným způsobem zobrazení zásad přístupu je seznam všech objektů ILocator na serveru a potom pro každý lokátor můžete uvést jeho přidružené zásady přístupu pomocí jeho AccessPolicy vlastnost.

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
    
## <a name="limit-access-policies"></a>Omezit zásady přístupu 

>[!NOTE]
> Je stanovený limit 1 000 000 různých zásad AMS (třeba zásady lokátoru nebo ContentKeyAuthorizationPolicy). Pokud vždy používáte stejné dny / přístupová oprávnění, například zásady pro lokátory, které mají zůstat na místě po dlouhou dobu (zásady bez odeslání), měli byste použít stejné ID zásad. 

Můžete například vytvořit obecnou sadu zásad s následujícím kódem, který by ve vaší aplikaci běžel pouze jednou. ID můžete protokolovat do souboru protokolu pro pozdější použití:

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

Potom můžete použít existující ID v kódu, jako je tento:

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

## <a name="list-all-locators"></a>Seznam všech lokátorů
Lokátor je adresa URL, která poskytuje přímou cestu k přístupu k datovému zdroji spolu s oprávněními k datovému zdroji, jak je definováno zásadami přidruženého přístupu lokátoru. Každý datový zdroj může mít kolekci objektů ILocator s ním spojené na jeho Locators vlastnost. Kontext serveru má také Locators kolekce, která obsahuje všechny lokátory.

Následující příklad kódu uvádí všechny lokátory na serveru. Pro každý lokátor zobrazuje ID pro související majetek a zásady přístupu. Zobrazuje také typ oprávnění, datum vypršení platnosti a úplnou cestu k datovému zdroji.

Všimněte si, že cesta lokátoru k datovému zdroji je pouze základní adresa URL datového zdroje. Chcete-li vytvořit přímou cestu k jednotlivým souborům, které by uživatel nebo aplikace mohl procházet, musí váš kód přidat konkrétní cestu k souboru do cesty lokátoru. Další informace o tom, jak to provést, naleznete v tématu [Deliver Assets with the Media Services SDK pro .NET](media-services-deliver-streaming-content.md).

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

## <a name="enumerating-through-large-collections-of-entities"></a>Výčet prostřednictvím velkých kolekcí entit
Při dotazování entit je limit 1000 entit vrácených najednou, protože veřejné REST v2 omezuje výsledky dotazu na 1000 výsledků. Musíte použít Skip a Take při výčtu prostřednictvím velké kolekce entit. 

Následující funkce prochází všechny úlohy v účtu poskytovaných mediálních služeb. Media Services vrátí 1000 úloh v kolekci úloh. Funkce využívá Skip a Take, abyste se ujistili, že jsou uvedeny všechny úlohy (v případě, že máte ve svém účtu více než 1000 úloh).

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

## <a name="delete-an-asset"></a>Odstranění datového zdroje
Následující příklad odstraní datový zdroj.

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
Chcete-li odstranit úlohu, musíte zkontrolovat stav úlohy, jak je uvedeno ve vlastnosti State. Úlohy, které jsou dokončeny nebo zrušeny, mohou být odstraněny, zatímco úlohy, které jsou v určitých jiných stavech, jako je například zařazeno do fronty, naplánováno nebo zpracování, musí být nejprve zrušeny a pak mohou být odstraněny.

Následující příklad kódu ukazuje metodu pro odstranění úlohy kontrolou stavů úlohy a následným odstraněním po dokončení nebo zrušení stavu. Tento kód závisí na předchozí části v tomto tématu pro získání odkazu na úlohu: Získat odkaz na úlohu.

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


## <a name="delete-an-access-policy"></a>Odstranění zásad přístupu
Následující příklad kódu ukazuje, jak získat odkaz na zásady přístupu na základě ID zásad a potom odstranit zásady.

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

