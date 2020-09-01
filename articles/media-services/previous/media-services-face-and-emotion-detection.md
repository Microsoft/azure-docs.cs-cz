---
title: Rozpoznávání obličeje a emoce s Azure Media Analyticsem | Microsoft Docs
description: Toto téma ukazuje, jak detekovat obličeje a emoce pomocí Azure Media Analytics.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 5ca4692c-23f1-451d-9d82-cbc8bf0fd707
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.reviewer: milanga
ms.custom: devx-track-csharp
ms.openlocfilehash: ac27fa5773e266d546b6511a24c6a5e14c5fda7f
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2020
ms.locfileid: "89257836"
---
# <a name="detect-face-and-emotion-with-azure-media-analytics"></a>Detekce obličeje a emoce pomocí Azure Media Analytics

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Procesor **Azure Media Face Detector** médií bude vyřazen. Informace o datu vyřazení najdete v tématu [starší verze součástí](legacy-components.md) .

## <a name="overview"></a>Přehled

Procesor **Azure Media Face Detector** Media (MP) umožňuje počítat, sledovat pohyby a dokonce i vyhodnocovat podíl a reakci v cílové skupině prostřednictvím výrazů obličeje. Tato služba obsahuje dvě funkce: 

* **Rozpoznávání tváře**
  
    Detekce tváře vyhledává a sleduje lidské obličeje ve videu. Je možné detekovat více plošek a následně je sledovat při jejich přesunu s metadaty pro čas a umístění vrácenými v souboru JSON. Během sledování se pokusí dát stejnému vzhledu stejnou plošku, zatímco se osoba pohybuje na obrazovce, a to i v případě, že jsou zablokované nebo stručně opouští rámeček.
  
  > [!NOTE]
  > Tato služba neprovádí rozpoznávání obličeje. Jednotlivá osoba, která opustí rámec nebo se může zablokovat příliš dlouho, bude při návratu na ni předána novému ID.
  > 
  > 
* **Detekce emoce**
  
    Detekce emoce je volitelná součást procesoru Rozpoznávání tvářech médií, která vrací analýzu více atributů emocionálních ze zjištěných ploch, včetně štěstí, smutek, obav, hněv a dalších. 

Sada **Azure Media Face Detector** MP je momentálně ve verzi Preview.

Tento článek obsahuje podrobné informace o  **Azure Media Face Detector** a ukazuje, jak ho používat s Media Services SDK pro .NET.

## <a name="face-detector-input-files"></a>Vstupní soubory pro rozpoznávání tváře
Videosoubory. V současné době jsou podporovány následující formáty: MP4, MOV a WMV.

## <a name="face-detector-output-files"></a>Výstupní soubory pro rozpoznávání tváře
Rozhraní API pro detekci a sledování tváře poskytuje vysokou přesnost detekce a sledování polohy, která dokáže detekovat až 64 lidských plošek ve videu. Přední obličeje poskytují nejlepší výsledky, zatímco strany a malé plošky (méně než nebo rovnající se 24x24 pixelů) nemusí být přesné.

Zjištěné a sledované plošky se vrátí pomocí souřadnic (doleva, nahoru, Šířka a výška), které označují umístění ploch v obrazových bodech v pixelech, a také číslo ID obličeje, které indikuje sledování daného jednotlivce. Pokud dojde ke ztrátě nebo překrytí čelní plochy v rámci tohoto rámce, je pro ně možné resetovat čísla ID obličeje. Výsledkem je, že někteří jednotlivci mají přiřazené více ID.

## <a name="elements-of-the-output-json-file"></a><a id="output_elements"></a>Prvky výstupního souboru JSON

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

Obličejový detektor používá techniky fragmentace (kde se metadata můžou rozdělit do bloků dat založených na čase) a můžete si stáhnout jenom to, co potřebujete, a segmentace (kde se události rozdělují v případě příliš velkého množství). S transformací dat vám pomůže několik jednoduchých výpočtů. Pokud například událost spuštěná v 6300 (taktech), s časovou osou 2997 (takty/s) a snímkem 29,97 (rámce/s), pak:

* Zahájení / časová osa = 2,1 sekund
* Sekund × snímková frekvence = 63 snímků

## <a name="face-detection-input-and-output-example"></a>Příklad vstupu a výstupu detekce obličeje
### <a name="input-video"></a>Vstupní video
[Vstupní video](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Konfigurace úlohy (předvolba)
Při vytváření úlohy s **Azure Media Face Detector**je nutné zadat předvolby konfigurace. Následující předvolba konfigurace je jenom pro rozpoznávání tváře.

```json
    {
      "version":"1.0",
      "options":{
          "TrackingMode": "Fast"
      }
    }
```

#### <a name="attribute-descriptions"></a>Popisy atributů
| Název atributu | Popis |
| --- | --- |
| Mode |Rychlost rychlého zpracování, ale méně přesná (výchozí).|

### <a name="json-output"></a>Výstup JSON
Následující příklad výstupu JSON byl zkrácen.

```json
    {
    "version": 1,
    "timescale": 30000,
    "offset": 0,
    "framerate": 29.97,
    "width": 1280,
    "height": 720,
    "fragments": [
        {
        "start": 0,
        "duration": 60060
        },
        {
        "start": 60060,
        "duration": 60060,
        "interval": 1001,
        "events": [
            [
            {
                "id": 0,
                "x": 0.519531,
                "y": 0.180556,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517969,
                "y": 0.181944,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517187,
                "y": 0.183333,
                "width": 0.0851562,
                "height": 0.151389
            }
            ],
```


## <a name="emotion-detection-input-and-output-example"></a>Příklad vstupu a výstupu detekce podle emoce
### <a name="input-video"></a>Vstupní video
[Vstupní video](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Konfigurace úlohy (předvolba)
Při vytváření úlohy s **Azure Media Face Detector**je nutné zadat předvolby konfigurace. Následující předvolba konfigurace určuje, že se má vytvořit JSON na základě zjištění emoce.

```json
    {
      "version": "1.0",
      "options": {
        "aggregateEmotionWindowMs": "987",
        "mode": "aggregateEmotion",
        "aggregateEmotionIntervalMs": "342"
      }
    }
```


#### <a name="attribute-descriptions"></a>Popisy atributů
| Název atributu | Popis |
| --- | --- |
| Mode |Obličeje: jenom rozpoznávání tváře.<br/>PerFaceEmotion: vrátíte emoce nezávisle pro každou detekci obličeje.<br/>AggregateEmotion: vrátí průměrné hodnoty emoce pro všechny plošky v rámci rámečku. |
| AggregateEmotionWindowMs |Použijte, pokud je vybraný režim AggregateEmotion. Určuje délku videa, která se používá k získání každého agregovaného výsledku v milisekundách. |
| AggregateEmotionIntervalMs |Použijte, pokud je vybraný režim AggregateEmotion. Určuje, jakou četnost mají být výsledkem agregované výsledky. |

#### <a name="aggregate-defaults"></a>Agregační výchozí hodnoty
Níže jsou doporučené hodnoty pro agregované nastavení okna a intervalu. AggregateEmotionWindowMs by měla být delší než AggregateEmotionIntervalMs.

|| Výchozí hodnoty: | Maximální počet (s) | Minimum (s) |
|--- | --- | --- | --- |
| **AggregateEmotionWindowMs** |0,5 |2 |0,25|
| **AggregateEmotionIntervalMs** |0,5 |1 |0,25|

### <a name="json-output"></a>Výstup JSON
Výstup JSON pro agregovaná emoce (zkrácený):

```json
    {
     "version": 1,
     "timescale": 30000,
     "offset": 0,
     "framerate": 29.97,
     "width": 1280,
     "height": 720,
     "fragments": [
       {
         "start": 0,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ]
         ]
       },
       {
         "start": 60060,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0.688541,
                 "happiness": 0.0586323,
                 "surprise": 0.227184,
                 "sadness": 0.00945675,
                 "anger": 0.00592107,
                 "disgust": 0.00154993,
                 "fear": 0.00450447,
                 "contempt": 0.0042109
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
```

## <a name="limitations"></a>Omezení
* Mezi podporované vstupní formáty videa patří MP4, MOV a WMV.
* Zjistitelný rozsah velikosti obličeje je 24x24 na 2048x2048 pixelů. Plošky z tohoto rozsahu nebudou zjištěny.
* Pro každé video je maximální počet vrácených ploch 64.
* Některé plošky nemusejí být zjištěny kvůli technickým výzvám; například hodně velkých úhlů obličeje (pozice) a velké překrytíy. Nejlepší výsledky jsou přední a blízko čelních ploch.

## <a name="net-sample-code"></a>Vzorový kód .NET

Následující program ukazuje, jak:

1. Vytvořte Asset a nahrajte do něj mediální soubor.
2. Vytvořte úlohu s úkolem detekce obličeje na základě konfiguračního souboru, který obsahuje následující předvolbu JSON: 

    ```json
            {
                "version": "1.0"
            }
    ```
3. Stáhněte si výstupní soubory JSON. 

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

namespace FaceDetection
{
    class Program
    {
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

            // Run the FaceDetection job.
            var asset = RunFaceDetectionJob(@"C:\supportFiles\FaceDetection\BigBuckBunny.mp4",
                                        @"C:\supportFiles\FaceDetection\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\FaceDetection\Output");
        }

        static IAsset RunFaceDetectionJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My Face Detection Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Face Detection Job");

            // Get a reference to Azure Media Face Detector.
            string MediaProcessorName = "Azure Media Face Detector";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Face Detection Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Face Detection Output Asset", AssetCreationOptions.None);

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
[Přehled analýzy Azure Media Services](media-services-analytics-overview.md)

[Ukázky Azure Media Analytics](https://amslabs.azurewebsites.net/demos/Analytics.html)

