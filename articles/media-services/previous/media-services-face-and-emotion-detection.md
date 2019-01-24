---
title: Zjištění tváří a Emocí pomocí Azure Media Analytics | Dokumentace Microsoftu
description: Toto téma ukazuje, jak detekovat tváří a emocí pomocí Azure Media Analytics.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 5ca4692c-23f1-451d-9d82-cbc8bf0fd707
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/09/2017
ms.author: milanga;juliako;
ms.openlocfilehash: 5aab8a5d48b7a7d17aa44b74d65ee70cb9322944
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54817550"
---
# <a name="detect-face-and-emotion-with-azure-media-analytics"></a>Zjištění tváří a Emocí pomocí Azure Media Analytics
## <a name="overview"></a>Přehled
**Azure Media Face Detectoru** procesor médií (PP) umožňuje počet, sledování pohybu a dokonce i vyhodnocovat zapojení cílové skupiny a reakce na ně prostřednictvím výrazu tváře. Tato služba obsahuje dvě funkce: 

* **Rozpoznávání tváře**
  
    Rozpoznávání tváře najde a sleduje lidské tváře ve videu. Více ploch lze zjistit a následně sledovat při přechodu, čas a umístění metadat vrátil v souboru JSON. Během sledování, pokusí se poskytnout konzistentní ID na stejný typ písma zatímco je osoba přesouvat na obrazovce, i když bráněno nebo stručně ponechejte rámce.
  
  > [!NOTE]
  > Tato služba neprovádí rozpoznávání obličeje. Osoba, která zůstanou rámce nebo bude bránit pro příliš dlouho dostanou nové ID když se vrátí.
  > 
  > 
* **Rozpoznávání emocí z výrazu**
  
    Rozpoznávání emocí z výrazu je volitelná součást pro rozpoznávání tváře detekce mediálním procesorem, který vrátí analýzy na více citové atributů z plochy zjistí, včetně štěstí, smutek, strach, hněv a další. 

**Azure Media Face Detectoru** sady Management Pack je aktuálně ve verzi Preview.

Tento článek obsahuje podrobnosti o **Azure Media Face Detectoru** a ukazuje, jak používat ve službě Media Services SDK pro .NET.

## <a name="face-detector-input-files"></a>Face Detectoru vstupních souborů
Video soubory. V současné době jsou podporovány následující formáty: MP4, MOV a WMV.

## <a name="face-detector-output-files"></a>Face Detectoru výstupní soubory
Rozhraní API pro zjišťování a sledování tváří poskytuje velmi přesné rozpoznávání tváře detekce a sledování pozice, který vyhledá ve videu až 64 lidských tváří. Přední tváří poskytují nejlepší výsledky, při straně tváří a malé tváří (menší než nebo roven 24 × 24 pixelů) nemusí být tak přesné.

Zjištěné a sleduje tváře se vrátí se souřadnicemi (vlevo, horní, šířku a výšku) určující umístění tváří v obrázku v pixelech, jakož i face ID číslo označující sledování tohoto jednotlivce. Face ID čísla jsou náchylné k obnovit okolností, když dojde ke ztrátě nebo překrývajících se v rámci, přední rozpoznávání tváře výsledkem několika jednotlivcům získávání přiřadit víc ID.

## <a id="output_elements"></a>Prvky výstupního souboru JSON

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

Detektor tváří využívá i metody pro fragmentaci (metadata, můžete se rozdělit podle času bloků dat a můžete si stáhnout vše, co potřebujete) a segmentace (kde události jsou rozdělit v případě, že vývojáři získají příliš velké). S transformací dat vám pomůže několik jednoduchých výpočtů. Například, pokud událost začalo 6300 (takty), s časovou 2997 (počet taktů za sekundu) a snímkovou 29,97 (snímků za sekundu), pak:

* Zahájení / časová osa = 2,1 sekund
* Sekundy x Framerate = 63 snímků

## <a name="face-detection-input-and-output-example"></a>Detekce vstup pro rozpoznávání tváře a příklad výstupu
### <a name="input-video"></a>Vstupního videa
[Vstupního videa](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Konfigurace úlohy (nastavení)
Při vytváření úlohy s **Azure Media Face Detectoru**, je nutné zadat nastavení konfigurace. Následující nastavení konfigurace je jen pro rozpoznávání tváře.

```json
    {
      "version":"1.0",
      "options":{
          "TrackingMode": "Fast"
      }
    }
```

#### <a name="attribute-descriptions"></a>Popis atributu
| Název atributu | Popis |
| --- | --- |
| Mode |Rychle – se rychlé zpracování rychlost, ale méně přesné (výchozí).|

### <a name="json-output"></a>Výstup ve formátu JSON
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


## <a name="emotion-detection-input-and-output-example"></a>Rozpoznávání emocí z výrazu vstup a výstup příkladu
### <a name="input-video"></a>Vstupního videa
[Vstupního videa](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Konfigurace úlohy (nastavení)
Při vytváření úlohy s **Azure Media Face Detectoru**, je nutné zadat nastavení konfigurace. Následující nastavení konfigurace určuje vytvoření JSON založené na rozpoznávání emocí z výrazu.

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


#### <a name="attribute-descriptions"></a>Popis atributu
| Název atributu | Popis |
| --- | --- |
| Mode |Tváří: Pouze rozpoznávání tváře.<br/>PerFaceEmotion: Vrátí pro rozpoznávání emocí nezávisle pro každý rozpoznávání tváře.<br/>AggregateEmotion: Návratové hodnoty průměrné pro rozpoznávání emocí pro všechny tváří v rámci. |
| AggregateEmotionWindowMs |Použijte, pokud vybraný režim AggregateEmotion. Určuje délku videa použit k vytvoření každé agregace výsledků v milisekundách. |
| AggregateEmotionIntervalMs |Použijte, pokud vybraný režim AggregateEmotion. Určuje, jak často k vytvoření agregované výsledky. |

#### <a name="aggregate-defaults"></a>Agregační výchozí hodnoty
Níže jsou doporučené hodnoty pro nastavení okna a interval agregace. AggregateEmotionWindowMs by měl být delší než AggregateEmotionIntervalMs.

|| Výchozí hodnoty (s) | Max(s) | Min |
|--- | --- | --- | --- |
| AggregateEmotionWindowMs |0,5 |2 |0.25|
| AggregateEmotionIntervalMs |0,5 |1 |0.25|

### <a name="json-output"></a>Výstup ve formátu JSON
Výstup pro agregace pro rozpoznávání emocí (zkrácená) ve formátu JSON:

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
* Podporované formáty vstupního videa zahrnout MP4, MOV a WMV.
* Rozsah velikosti zjistitelné pro rozpoznávání tváře je 24 × 24 na 2 048 x 2048 pixelů. Tváří mimo tento rozsah nerozpozná.
* Maximální počet tváří vrátil pro každé video je 64.
* Z důvodu technické komplikace; nemusí být detekována některé tváří například velké pro rozpoznávání tváře úhlů (hlavní póza) a velké uzavření. Přední a téměř čelní strany mají nejlepších výsledků.

## <a name="net-sample-code"></a>Vzorový kód .NET

Následující program ukazuje postup:

1. Vytvoření assetu a uložit do assetu soubor média.
2. Vytvořte úlohu s úkolem pro rozpoznávání tváře detekce založené na konfigurační soubor, který obsahuje následující přednastavený kontext json: 

    ```json
            {
                "version": "1.0"
            }
    ```
3. Stažení výstupních souborů JSON. 

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

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Související odkazy
[Azure Media Services Analytics Overview](media-services-analytics-overview.md)

[Azure Media Analytics demos](http://amslabs.azurewebsites.net/demos/Analytics.html)

