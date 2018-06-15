---
title: Použít miniatur videa Azure Media k vytvoření Videosouhrn | Microsoft Docs
description: Videosouhrn vám pomůžou vytvořit souhrnných informací o dlouho videa automaticky výběrem zajímavé fragmenty kódu z zdroj videa. To je užitečné, pokud byste chtěli poskytnout rychlý přehled toho, co očekávat při dlouhé video.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: a245529f-3150-4afc-93ec-e40d8a6b761d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/09/2017
ms.author: milanga;juliako;
ms.openlocfilehash: aba01314b26f11df41aef25215697389bc7f46b2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33790472"
---
# <a name="use-azure-media-video-thumbnails-to-create-a-video-summarization"></a>Použít miniatur videa Azure Media k vytvoření Videosouhrn
## <a name="overview"></a>Přehled
**Miniatur videa Azure Media** procesor médií (PP) můžete vytvořit souhrn video, které jsou užitečné pro zákazníky, kteří právě chcete zobrazit náhled souhrn dlouhé video. Například mohou zákazníci chtějí krátké "souhrn video" v tématu kdy myší na miniaturu. Podle postupně je upravujte parametry **miniatur videa v Azure Media** prostřednictvím konfigurace přednastavení, můžete použít sady MP výkonná snímek zřetězení a zjišťování technologie algorithmically generovat popisný subclip.  

**Azure Media Video miniaturu** MP je aktuálně ve verzi Preview.

Tento článek obsahuje údaje o **Azure Media Video miniaturu** a ukazuje, jak pomocí sady Media Services SDK pro .NET.

## <a name="limitations"></a>Omezení

V některých případech Pokud videa není skládá z různých scény výstup bude pouze jeden snímek.

## <a name="video-summary-example"></a>Příklad videa souhrn
Tady jsou některé příklady co můžete dělat procesor médií miniatur videa v Azure média:

### <a name="original-video"></a>Původní video
[Původní video](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Faed33834-ec2d-4788-88b5-a4505b3d032c%2FMicrosoft%27s%20HoloLens%20Live%20Demonstration.ism%2Fmanifest)

### <a name="video-thumbnail-result"></a>Video miniatur výsledek
[Video miniatur výsledek](http://ampdemo.azureedge.net/azuremediaplayer.html?url=http%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Ff5c91052-4232-41d4-b531-062e07b6a9ae%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

## <a name="task-configuration-preset"></a>Konfigurace úlohy (přednastavených)
Při vytváření miniatur videa úlohy s **miniatur videa Azure Media**, je nutné zadat jedno z přednastavení konfigurace. Ukázka výše miniatur byl vytvořen s následující konfigurací základní JSON:

```json
    {
        "version":"1.0"
    }
```

V současné době můžete změnit následující parametry:

| Param | Popis |
| --- | --- |
| outputAudio |Určuje, zda výsledné video obsahuje všechny zvukovém souboru. <br/>Povolené hodnoty jsou: True nebo False. Výchozí hodnota je True. |
| fadeInFadeOut |Určuje, jestli Objevování přejde se používají mezi samostatnou pohybu miniatur.  <br/>Povolené hodnoty jsou: True nebo False.  Výchozí hodnota je True. |
| maxMotionThumbnailDurationInSecs |Celé číslo, které určuje, jak dlouho musí být celé výsledné video.  Výchozí hodnota závisí na původní video doba trvání. |

Následující tabulka popisuje výchozí doba, kdy **maxMotionThumbnailInSecs** nepoužívá.

|  |  |  |
| --- | --- | --- | --- | --- |
| Video doba trvání |d < 3 min |3 min < d < 15 minut |
| Doba trvání miniatur |15 sekundu (scény 2-3) |30 sekund (scény 3 až 5) |

Následujícím kódu JSON nastaví dostupné parametry.

```json
    {
        "version": "1.0",
        "options": {
            "outputAudio": "true",
            "maxMotionThumbnailDurationInSecs": "10",
            "fadeInFadeOut": "true"
        }
    }
```

## <a name="net-sample-code"></a>Ukázkový kód rozhraní .NET

Program zobrazí následující postup:

1. Vytvořte asset a nahrajte soubor média do assetu.
2. Vytvoří úlohu s video miniatur úloh podle konfigurační soubor, který obsahuje následující přednastavení json: 
    
    ```json
            {                
                "version": "1.0",
                "options": {
                    "outputAudio": "true",
                    "maxMotionThumbnailDurationInSecs": "30",
                    "fadeInFadeOut": "false"
                }
            }
    ```

3. Výstupní soubory stáhne. 

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

    namespace VideoSummarization
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


                // Run the thumbnail job.
                var asset = RunVideoThumbnailJob(@"C:\supportFiles\VideoThumbnail\BigBuckBunny.mp4",
                                            @"C:\supportFiles\VideoThumbnail\config.json");

                // Download the job output asset.
                DownloadAsset(asset, @"C:\supportFiles\VideoThumbnail\Output");
            }

            static IAsset RunVideoThumbnailJob(string inputMediaFilePath, string configurationFile)
            {
                // Create an asset and upload the input media file to storage.
                IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                    "My Video Thumbnail Input Asset",
                    AssetCreationOptions.None);

                // Declare a new job.
                IJob job = _context.Jobs.Create("My Video Thumbnail Job");

                // Get a reference to Azure Media Video Thumbnails.
                string MediaProcessorName = "Azure Media Video Thumbnails";

                var processor = GetLatestMediaProcessorByName(MediaProcessorName);

                // Read configuration from the specified file.
                string configuration = File.ReadAllText(configurationFile);

                // Create a task with the encoding details, using a string preset.
                ITask task = job.Tasks.AddNew("My Video Thumbnail Task",
                    processor,
                    configuration,
                    TaskOptions.None);

                // Specify the input asset.
                task.InputAssets.Add(asset);

                // Add an output asset to contain the results of the job.
                task.OutputAssets.AddNew("My Video Thumbnail Output Asset", AssetCreationOptions.None);

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

### <a name="video-thumbnail-output"></a>Miniatury výstupu videa
[Miniatury výstupu videa](http://ampdemo.azureedge.net/azuremediaplayer.html?url=http%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Fd06f24dc-bc81-488e-a8d0-348b7dc41b56%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Související odkazy
[Azure Media Services Analytics – přehled](media-services-analytics-overview.md)

[Ukázky služby Azure Media Analytics](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

