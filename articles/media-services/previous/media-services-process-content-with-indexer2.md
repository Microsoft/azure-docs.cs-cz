---
title: Indexování mediálních souborů pomocí Azure Media Indexer 2 Preview | Microsoft Docs
description: Azure Media Indexer umožňuje zpřístupnění obsahu mediálních souborů a vytvoření fulltextového přepisu pro skryté titulky a klíčová slova. V tomto tématu se dozvíte, jak používat Media Indexer 2 Preview.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.reviewer: adsolank
ms.openlocfilehash: 8aa3082b15886234905edaebbbc9e1458bd7e3f8
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2019
ms.locfileid: "69015017"
---
# <a name="indexing-media-files-with-azure-media-indexer-2-preview"></a>Indexování mediálních souborů pomocí Azure Media Indexer 2 Preview
## <a name="overview"></a>Přehled
Procesor médií **verze Preview Azure Media Indexer 2** (MP) umožňuje vytvářet soubory médií a obsah prohledávatelné a také generovat skryté titulky. Ve srovnání s předchozí verzí [Azure Media Indexer](media-services-index-content.md)provádí **Azure Media Indexer 2 Preview** rychlejší indexování a nabízí širší podporu pro jazyky. Mezi podporované jazyky patří angličtina, španělština, francouzština, němčina, italština, čínština (Mandarin, zjednodušená), portugalština, arabština, ruština a japonština.

Sada Management Pack **Azure Media Indexer 2 Preview** je momentálně ve verzi Preview.

Tento článek ukazuje, jak vytvořit úlohy indexování pomocí **Azure Media Indexer 2 ve verzi Preview**.

> [!NOTE]
> Platí následující požadavky:
> 
> Indexer 2 není v Azure Čína a Azure Government podporován.
> 
> Při indexování obsahu se ujistěte, že používáte mediální soubory, které mají velmi jasný zvuk (bez hudby, hluku, efektů nebo mikrofonu Hiss). Mezi příklady příslušného obsahu patří: zaznamenané schůzky, přednášky nebo prezentace. Následující obsah nemusí být vhodný pro indexování: filmy, televizní pořady, cokoli se smíšeným zvukovým a zvukovým efektem, špatně zaznamenaného obsahu s hlukem na pozadí (Hiss).
> 
> 

Tento článek obsahuje podrobnosti o **Azure Media Indexer 2 Preview** a ukazuje, jak ho používat s Media Services SDK pro .NET.

## <a name="input-and-output-files"></a>Vstupní a výstupní soubory
### <a name="input-files"></a>Vstupní soubory
Zvukové soubory nebo videosoubory

### <a name="output-files"></a>Výstupní soubory
Úloha indexování může generovat soubory titulků v následujících formátech:  

* **SAMI**
* **TTML**
* **WebVTT**

Soubory skrytého titulku (CC) v těchto formátech lze použít k zpřístupnění zvukových a videosouborů uživatelům s postižením sluchu.

## <a name="task-configuration-preset"></a>Konfigurace úlohy (předvolba)
Při vytváření úlohy indexování pomocí **Azure Media Indexer 2 Preview**je nutné zadat předvolby konfigurace.

Následující sady JSON mají dostupné parametry.

```json
    {
      "version":"1.0",
      "Features":
        [
           {
           "Options": {
                "Formats":["WebVtt","ttml"],
                "Language":"enUs",
                "Type":"RecoOptions"
           },
           "Type":"SpReco"
        }]
    }
```

## <a name="supported-languages"></a>Podporované jazyky
Azure Media Indexer 2 Preview podporuje převod řeči na text pro následující jazyky (při zadávání názvu jazyka v konfiguraci úlohy použijte 4 znaky kódu v závorkách, jak je znázorněno níže):

* Angličtina [EnUs]
* Španělština [EsEs]
* Čínština (Mandarin, zjednodušená) [ZhCn]
* Francouzština [FrFr]
* Němčina [DeDe]
* Italština [ItIt]
* Portugalština [PtBr]
* Arabština (Egypt) [ArEg]
* Japonština [JaJp]
* Ruština [RuRu]
* British – angličtina [EnGb]
* Španělština (Mexiko) [EsMx] 

## <a name="supported-file-types"></a>Podporované typy souborů

Informace o podporovaných typech souborů najdete v části [podporované kodeky a formáty](media-services-media-encoder-standard-formats.md#input-containerfile-formats) .

## <a name="net-sample-code"></a>Vzorový kód .NET

Následující program ukazuje, jak:

1. Vytvořte Asset a nahrajte do něj mediální soubor.
2. Vytvořte úlohu s úlohou indexování na základě konfiguračního souboru, který obsahuje následující předvolbu JSON:

    ```json
            {
            "version":"1.0",
            "Features":
                [
                {
                "Options": {
                        "Formats":["WebVtt","ttml"],
                        "Language":"enUs",
                        "Type":"RecoOptions"
                },
                "Type":"SpReco"
                }]
            }
    ```
    
3. Stáhněte si výstupní soubory. 
   
#### <a name="create-and-configure-a-visual-studio-project"></a>Vytvoření a konfigurace projektu Visual Studia

Nastavte své vývojové prostředí a v souboru app.config vyplňte informace o připojení, jak je popsáno v tématu [Vývoj pro Media Services v .NET](media-services-dotnet-how-to-use.md). 

#### <a name="example"></a>Příklad

```csharp
using System;
using System.Configuration;
using System.IO;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;
using System.Threading.Tasks;

namespace IndexContent
{
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        // Field for service context.
        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Run indexing job.
            var asset = RunIndexingJob(@"C:\supportFiles\Indexer\BigBuckBunny.mp4",
                                        @"C:\supportFiles\Indexer\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\Indexer\Output");
        }

        static IAsset RunIndexingJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My Indexing Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Indexing Job");

            // Get a reference to Azure Media Indexer 2 Preview.
            string MediaProcessorName = "Azure Media Indexer 2 Preview";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Indexing Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset to be indexed.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);

            // Use the following event handler to check job progress.  
            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

            // Launch the job.
            job.Submit();

            // Check job execution and wait for job to finish.
            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);

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
                return null;
            }

            return job.OutputMediaAssets[0];
        }

        static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
        {
            IAsset asset = _context.Assets.Create(assetName, options);

            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            assetFile.Upload(filePath);

            return asset;
        }

        static void DownloadAsset(IAsset asset, string outputDirectory)
        {
            foreach (IAssetFile file in asset.AssetFiles)
            {
                file.Download(Path.Combine(outputDirectory, file.Name));
            }
        }

        static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors
                .Where(p => p.Name == mediaProcessorName)
                .ToList()
                .OrderBy(p => new Version(p.Version))
                .LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor",
                                                           mediaProcessorName));

            return processor;
        }

        static private void StateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);

            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished.");
                    Console.WriteLine();
                    break;
                case JobState.Canceling:
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    Console.WriteLine("Please wait...\n");
                    break;
                case JobState.Canceled:
                case JobState.Error:
                    // Cast sender as a job.
                    IJob job = (IJob)sender;
                    // Display or log error details as needed.
                    // LogJobStop(job.Id);
                    break;
                default:
                    break;
            }
        }
    }
}
```

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Související odkazy
[Přehled analýzy Azure Media Services](media-services-analytics-overview.md)

[Ukázky Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

