---
title: Indexování mediálních souborů pomocí azure media indexeru 2 Preview | Dokumenty společnosti Microsoft
description: Azure Media Indexer umožňuje prohledávat obsah mediálních souborů a generovat fulltextový přepis pro skryté titulky a klíčová slova. Toto téma ukazuje, jak používat Media Indexer 2 Preview.
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
ms.date: 09/22/2019
ms.author: juliako
ms.reviewer: adsolank
ms.openlocfilehash: c24218dc116803ca0e0a1f166b7b54b24fc4d5ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78163790"
---
# <a name="indexing-media-files-with-azure-media-indexer-2-preview"></a>Indexování mediálních souborů pomocí Azure Media Indexer 2 Preview

> [!NOTE]
> Mediální procesor **Azure Media Indexer 2** bude vyřazen. Data vyřazení naleznete v tomto tématu [starších součástí.](legacy-components.md) [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) nahradí tento starší mediální procesor. Další informace najdete [v tématu Migrace z Azure Media Indexer a Azure Media Indexer 2 do Azure Media Services Video Indexer](migrate-indexer-v1-v2.md).

Mediální procesor **Azure Media Indexer 2 Preview** (MP) umožňuje prohledávat mediální soubory a obsah a také generovat stopy skrytých titulků. Ve srovnání s předchozí verzí [Azure Media Indexer](media-services-index-content.md), **Azure Media Indexer 2 Preview** provádí rychlejší indexování a nabízí širší jazykovou podporu. Mezi podporované jazyky patří angličtina, španělština, francouzština, němčina, italština, čínština (mandarínština, zjednodušená), portugalština, arabština, ruština a japonština.

**Azure Media Indexer 2 Preview** MP je aktuálně ve verzi Preview.

Tento článek ukazuje, jak vytvořit úlohy indexování pomocí **Azure Media Indexer 2 Preview**.

## <a name="considerations"></a>Požadavky

Platí následující důležité informace:
 
* Indexer 2 není podporován v Azure China 21Vianet a Azure Government.
* Při indexování obsahu používejte mediální soubory, které mají velmi jasnou řeč (bez hudby na pozadí, šumu, efektů nebo syčení mikrofonu). Příklady vhodného obsahu jsou: nahrané schůzky, přednášky nebo prezentace. Následující obsah nemusí být vhodný pro indexování: filmy, televizní pořady, cokolise se smíšenými zvukovými a zvukovými efekty, špatně nahraný obsah s šumem na pozadí (syčení).
 
## <a name="input-and-output-files"></a>Vstupní a výstupní soubory
### <a name="input-files"></a>Vstupní soubory
Zvukové soubory nebo videosoubory

### <a name="output-files"></a>Výstupní soubory
Úloha indexování může generovat soubory skrytých titulků v následujících formátech:  

* **TTML**
* **WebVTT**

Soubory s uzavřeným titulkem (CC) v těchto formátech lze použít k tomu, aby byly zvukové soubory a video soubory přístupné osobám se sluchovým postižením.

## <a name="task-configuration-preset"></a>Konfigurace úloh (přednastavení)
Při vytváření úlohy indexování pomocí **Azure Media Indexer 2 Preview**je nutné zadat přednastavení konfigurace.

Následující JSON nastaví dostupné parametry.

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
Azure Media Indexer 2 Preview podporuje převod řeči na text pro následující jazyky (při zadávání názvu jazyka v konfiguraci úlohy použijte čtyřmístný kód v závorkách, jak je znázorněno níže):

* Angličtina [EnUs]
* Španělština [Eses]
* Čínština (mandarínština, zjednodušená) [ZhCn]
* Francouzština [FrFr]
* Němčina [DeDe]
* Italština [Itit]
* Portugalština [PtBr]
* Arabština (egyptská) [ArEg]
* Japonština [Jajp]
* Ruština.[ RuRu]
* Britská angličtina [EnGb]
* Španělština (Mexiko) [EsMx] 

## <a name="supported-file-types"></a>Podporované typy souborů

Informace o podporovaných typech souborů naleznete v části [Podporované kodeky/formáty.](media-services-media-encoder-standard-formats.md#input-containerfile-formats)

## <a name="net-sample-code"></a>Ukázkový kód rozhraní .NET

Následující program ukazuje, jak:

1. Vytvořte datový zdroj a nahrajte do něj mediální soubor.
2. Vytvořte úlohu s úlohou indexování založenou na konfiguračním souboru, který obsahuje následující přednastavení json:

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

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Související odkazy
[Přehled analýzy Mediálních služeb Azure](media-services-analytics-overview.md)

[Ukázky Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

