---
title: Detekce tváře a emocí pomocí Azure Media Analytics | Dokumenty společnosti Microsoft
description: Toto téma ukazuje, jak rozpoznat tváře a emoce pomocí Azure Media Analytics.
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
ms.openlocfilehash: 2d746167f993438e5fce467365844df2078c08a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919307"
---
# <a name="detect-face-and-emotion-with-azure-media-analytics"></a>Detekce tváře a emocí pomocí Azure Media Analytics

> [!NOTE]
> Mediální procesor **Azure Media Face Detector** bude vyřazen. Datum vyřazení naleznete v tématu [starších součástí.](legacy-components.md)

## <a name="overview"></a>Přehled

Mediální procesor **Azure Media Face Detector** (MP) umožňuje počítat, sledovat pohyby a dokonce měřit účast publika a reakci pomocí výrazů obličeje. Tato služba obsahuje dvě funkce: 

* **Detekce obličeje**
  
    Detekce obličeje vyhledá a sleduje lidské tváře ve videu. Více tváří lze zjistit a následně sledovat, jak se pohybují, s časem a umístění metadata vrácena v souboru JSON. Během sledování se pokusí poskytnout konzistentní ID na stejnou tvář, zatímco osoba se pohybuje na obrazovce, i když jsou blokovány nebo krátce opustit rám.
  
  > [!NOTE]
  > Tato služba neprovádí rozpoznávání obličeje. Jedinec, který opustí rám nebo se stane překážkou příliš dlouho, dostane při návratu nové ID.
  > 
  > 
* **Detekce emocí**
  
    Detekce emocí je volitelná součást editoru médií pro detekci obličeje, která vrací analýzu několika emočních atributů z detekovaných tváří, včetně štěstí, smutku, strachu, hněvu a dalších. 

Azure **Media Face Detector** MP je aktuálně ve verzi Preview.

Tento článek obsahuje podrobnosti o **Azure Media Face Detector** a ukazuje, jak ji používat s Media Services SDK pro .NET.

## <a name="face-detector-input-files"></a>Vstupní soubory detektoru obličeje
Video soubory. V současné době jsou podporovány následující formáty: MP4, MOV a WMV.

## <a name="face-detector-output-files"></a>Výstupní soubory detektoru obličeje
Rozhraní API pro detekci a sledování obličeje poskytuje vysoce přesnou detekci polohy obličeje a sledování, které dokáže detekovat až 64 lidských tváří ve videu. Nejlepší výsledky poskytují čelní plochy, zatímco boční plochy a malé plochy (menší nebo rovna 24 x 24 pixelům) nemusí být tak přesné.

Zjištěné a sledované plochy jsou vráceny se souřadnicemi (vlevo, nahoře, šířkou a výškou), které označují umístění ploch v obraze v obrazových bodech a také číslo ID plochy označující sledování této osoby. Čísla Face ID jsou náchylná k resetování za okolností, kdy dojde ke ztrátě nebo překrytí čelní plochy v rámečku, což vede k tomu, že některým osobám je přiřazeno více ID.

## <a name="elements-of-the-output-json-file"></a><a id="output_elements"></a>Prvky výstupního souboru JSON

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

Detektor obličeje používá techniky fragmentace (kde metadata mohou být rozděleny v čas-založené bloky a můžete si stáhnout pouze to, co potřebujete), a segmentace (kde jsou události rozděleny v případě, že příliš velké). S transformací dat vám pomůže několik jednoduchých výpočtů. Pokud například událost začala na 6300 (značky), s časovým měřítkem 2997 (značky/s) a snímkovou frekvencí 29,97 (snímky/s), pak:

* Zahájení / časová osa = 2,1 sekund
* Sekundy x snímková frekvence = 63 snímků

## <a name="face-detection-input-and-output-example"></a>Příklad vstupu a výstupu detekce obličeje
### <a name="input-video"></a>Vstupní video
[Vstupní video](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Konfigurace úloh (přednastavení)
Při vytváření úlohy pomocí **azure media face detector**je nutné zadat přednastavení konfigurace. Následující přednastavení konfigurace je pouze pro detekci obličeje.

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
| Mode |Rychlé - rychlá rychlost zpracování, ale méně přesná (výchozí).|

### <a name="json-output"></a>JSON výstup
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


## <a name="emotion-detection-input-and-output-example"></a>Příklad vstupu a výstupu detekce emocí
### <a name="input-video"></a>Vstupní video
[Vstupní video](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Konfigurace úloh (přednastavení)
Při vytváření úlohy pomocí **azure media face detector**je nutné zadat přednastavení konfigurace. Následující přednastavení konfigurace určuje vytvoření json na základě detekce emocí.

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
| Mode |Plochy: Pouze detekce obličeje.<br/>PerFaceEmotion: Vrátit emoce nezávisle pro každou detekci obličeje.<br/>AggregateEmotion: Vrátí průměrné hodnoty emocí pro všechny plochy v rámečku. |
| AggregateEmotionWindowMs |Použijte, pokud je vybrán režim AggregateEmotion. Určuje délku videa použitého k vytvoření každého souhrnného výsledku v milisekundách. |
| AggregateEmotionIntervals |Použijte, pokud je vybrán režim AggregateEmotion. Určuje, s jakou frekvencí mají být vytvářeny agregované výsledky. |

#### <a name="aggregate-defaults"></a>Agregovat výchozí hodnoty
Níže jsou uvedeny doporučené hodnoty pro agregované nastavení okna a intervalu. AggregateEmotionWindowMs by měla být delší než AggregateEmotionIntervalMs.

|| Výchozí hodnoty | Maximální počet | Min(y) |
|--- | --- | --- | --- |
| AggregateEmotionWindowMs |0,5 |2 |0,25|
| AggregateEmotionIntervals |0,5 |1 |0,25|

### <a name="json-output"></a>JSON výstup
JSON výstup pro celkové emoce (zkrácen):

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
* Podporované vstupní video formáty zahrnují MP4, MOV a WMV.
* Detekovatelný rozsah velikostí obličeje je 24x24 až 2048x2048 pixelů. Plochy mimo tento rozsah nebudou rozpoznány.
* Pro každé video je maximální počet vrácených ploch 64.
* Některé tváře nemusí být detekovány kvůli technickým problémům; například velmi velké úhly obličeje (hlava-představovat), a velké okluze. Čelní a téměř čelní plochy mají nejlepší výsledky.

## <a name="net-sample-code"></a>Ukázkový kód rozhraní .NET

Následující program ukazuje, jak:

1. Vytvořte datový zdroj a nahrajte do něj mediální soubor.
2. Vytvořte úlohu s úlohou rozpoznávání obličeje založenou na konfiguračním souboru, který obsahuje následující přednastavení json: 

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
[Přehled analýzy Mediálních služeb Azure](media-services-analytics-overview.md)

[Ukázky Azure Media Analytics](https://amslabs.azurewebsites.net/demos/Analytics.html)

