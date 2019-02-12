---
title: Indexování mediálních souborů pomocí Azure Media Indexer 2 ve verzi Preview | Dokumentace Microsoftu
description: Azure Media Indexer umožňuje provést prohledávatelný obsah vašich mediálních souborů a vygenerovat fulltextový přepis pro uzavřené titulků a klíčová slova. Toto téma ukazuje, jak použít Media Indexer 2 ve verzi Preview.
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
ms.date: 02/10/2019
ms.author: adsolank;juliako;
ms.openlocfilehash: dd09e8949c2d71e550d02cd79611a7424d8113fc
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56000911"
---
# <a name="indexing-media-files-with-azure-media-indexer-2-preview"></a>Indexování mediálních souborů pomocí Azure Media Indexer 2 ve verzi Preview
## <a name="overview"></a>Přehled
**Azure Media Indexer 2 Preview** procesor médií (PP) umožňuje vytvořit mediální soubory a obsah prohledávatelné, jakož i generovat uzavřené stopy titulků. Ve srovnání s předchozí verzi [Azure Media Indexer](media-services-index-content.md), **Azure Media Indexer 2 Preview** provádí rychlejší indexování a nabízí rozsáhlejší jazyková podpora. Mezi podporované jazyky patří angličtina, španělština, francouzština, němčina, italština, čínština (Mandarínština, zjednodušené), portugalština, arabština, ruština a japonština.

**Azure Media Indexer 2 Preview** sady Management Pack je aktuálně ve verzi Preview.

Tento článek ukazuje, jak vytvářet úlohy indexování s **Azure Media Indexer 2 Preview**.

> [!NOTE]
> Platí následující aspekty:
> 
> Indexer 2 se nepodporuje v Azure China a Azure Government.
> 
> Názvy při indexování obsahu, ujistěte se, že používají soubory multimédií, které mají velmi jasně řeči (bez pozadí Hudba, šum, účinky nebo mikrofon hiss). Tady je několik příkladů odpovídající obsahu: zaznamenané schůzky, přednášek nebo prezentace. Následující obsah, nemusí být vhodný pro indexování: filmy, televizní pořady, cokoli pomocí smíšené audio a zvukové efekty špatně zaznamenaného obsahu s šum na pozadí (hiss).
> 
> 

Tento článek obsahuje podrobnosti o **Azure Media Indexer 2 Preview** a ukazuje, jak používat ve službě Media Services SDK pro .NET

## <a name="input-and-output-files"></a>Vstupní a výstupní soubory
### <a name="input-files"></a>Vstupní soubory
Zvuk nebo video soubory

### <a name="output-files"></a>Výstupní soubory
Úloha indexování můžete vygenerovat soubory s titulky uvedené v následujících formátech:  

* **SAMI**
* **TTML**
* **WebVTT**

Zavření titulek (kopie) soubory do těchto formátů je možné provést zvukových souborů a videosouborů osobám s postižením sluchu.

## <a name="task-configuration-preset"></a>Konfigurace úlohy (nastavení)
Při vytváření indexování úloh s **Azure Media Indexer 2 Preview**, je nutné zadat nastavení konfigurace.

Následující kód JSON nastaví dostupné parametry.

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
Azure Media Indexer 2 ve verzi Preview podporuje převod řeči na text pro následující jazyky (při zadávání názvu jazyka v konfiguraci úloh, použít kód znaku 4 v závorkách, jak je uvedeno níže):

* Angličtina [EnUs]
* Španělština [EsEs]
* Čínština (Mandarínština, zjednodušená čínština) [ZhCn]
* Francouzština [FrFr]
* Němčina [DeDe]
* Italština [ItIt]
* Portugalština [PtBr]
* Arabština (Egyptian) [ArEg]
* Japonština [JaJp]
* Ruština [RuRu]
* Britské angličtiny [EnGb]
* Španělština (Mexiko) [EsMx] 

## <a name="supported-file-types"></a>Podporované typy souborů

Informace o typech podporovaných souborů najdete v tématu [podporované kodeky a formáty](media-services-media-encoder-standard-formats.md#input-containerfile-formats) oddílu.

## <a name="net-sample-code"></a>Vzorový kód .NET

Následující program ukazuje postup:

1. Vytvoření assetu a uložit do assetu soubor média.
2. Vytvoření úlohy indexování úlohu podle konfigurační soubor, který obsahuje následující přednastavení json:

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
    
3. Stažení výstupních souborů. 
   
#### <a name="create-and-configure-a-visual-studio-project"></a>Vytvoření a konfigurace projektu Visual Studia

Nastavte své vývojové prostředí a v souboru app.config vyplňte informace o připojení, jak je popsáno v tématu [Vývoj pro Media Services v .NET](media-services-dotnet-how-to-use.md). 

#### <a name="example"></a>Příklad:

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
[Azure Media Services Analytics Overview](media-services-analytics-overview.md)

[Azure Media Analytics demos](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

