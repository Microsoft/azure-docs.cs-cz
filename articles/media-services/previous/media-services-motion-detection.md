---
title: Detekce pohybu pomocí Azure Media Analytics | Dokumenty společnosti Microsoft
description: Mediální procesor Azure Media Motion Detector (MP) umožňuje efektivně identifikovat části zájmu v rámci jinak dlouhého a bezproblémového videa.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.reviewer: milanga
ms.openlocfilehash: f4c021531a4d04bf16e5dbee4172952433f675d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77913000"
---
# <a name="detect-motions-with-azure-media-analytics"></a>Detekce pohybu pomocí Azure Media Analytics

> [!NOTE]
> Mediální procesor **Azure Media Motion Detector** bude vyřazen. Datum vyřazení naleznete v tématu [starších součástí.](legacy-components.md)
 
## <a name="overview"></a>Přehled

Mediální procesor **Azure Media Motion Detector** (MP) umožňuje efektivně identifikovat části zájmu v rámci jinak dlouhého a bezproblémového videa. Detekci pohybu lze použít na statických záběrech kamery k identifikaci částí videa, kde dochází k pohybu. Generuje soubor JSON obsahující metadata s časovými razítky a ohraničující oblasti, kde došlo k události.

Tato technologie je zaměřena na bezpečnostní videozáznamy a je schopna kategorizovat pohyb do relevantních událostí a falešných poplachů, jako jsou stíny a změny osvětlení. To vám umožní generovat výstrahy zabezpečení z kamerových kanálů, aniž byste byli nevyžádanou poštou s nekonečnými irelevantními událostmi, a zároveň být schopni extrahovat okamžiky zájmu z dlouhých sledovacích videí.

Azure **Media Motion Detector** MP je aktuálně ve verzi Preview.

Tento článek obsahuje podrobnosti o **Azure Media Motion Detector** a ukazuje, jak ji používat s Media Services SDK pro .NET

## <a name="motion-detector-input-files"></a>Vstupní soubory detektoru pohybu
Video soubory. V současné době jsou podporovány následující formáty: MP4, MOV a WMV.

## <a name="task-configuration-preset"></a>Konfigurace úloh (přednastavení)
Při vytváření úlohy pomocí **Azure Media Motion Detector**je nutné zadat přednastavení konfigurace. 

### <a name="parameters"></a>Parametry
Můžete použít následující parametry:

| Name (Název) | Možnosti | Popis | Výchozí |
| --- | --- | --- | --- |
| citlivostÚroveň |Řetězec:'low', 'medium', 'high' |Nastaví úroveň citlivosti, při které jsou hlášeny pohyby. Upravte to toto nastavení počtu falešných poplachů. |"střední" |
| frameSamplingValue |Celé celé číslo |Nastaví frekvenci, na které algoritmus běží. 1 se rovná každému snímku, 2 znamená každý druhý snímek a tak dále. |1 |
| detectLightChange |Logické:'true', 'false' |Nastaví, zda jsou ve výsledcích uvedeny lehké změny |"Nepravdivé" |
| mergeTimeThreshold |Xs-čas: Hh:mm:ss<br/>Příklad: 00:00:03 |Určuje časové okno mezi událostmi pohybu, kde jsou kombinovány 2 události a vykazovány jako 1. |00:00:00 |
| detectionZones |Pole detekčních zón:<br/>- Detekční zóna je pole 3 nebo více bodů<br/>- Point je souřadnice x a y od 0 do 1. |Popisuje seznam polygonoldetačních zón, které mají být použity.<br/>Výsledky jsou hlášeny s zónami jako ID, přičemž první z nich je 'id':0 |Jedna zóna, která pokrývá celý rám. |

### <a name="json-example"></a>Příklad JSON

```json
    {
      "version": "1.0",
      "options": {
        "sensitivityLevel": "medium",
        "frameSamplingValue": 1,
        "detectLightChange": "False",
        "mergeTimeThreshold":
        "00:00:02",
        "detectionZones": [
          [
            {"x": 0, "y": 0},
            {"x": 0.5, "y": 0},
            {"x": 0, "y": 1}
           ],
          [
            {"x": 0.3, "y": 0.3},
            {"x": 0.55, "y": 0.3},
            {"x": 0.8, "y": 0.3},
            {"x": 0.8, "y": 0.55},
            {"x": 0.8, "y": 0.8},
            {"x": 0.55, "y": 0.8},
            {"x": 0.3, "y": 0.8},
            {"x": 0.3, "y": 0.55}
          ]
        ]
      }
    }
```

## <a name="motion-detector-output-files"></a>Výstupní soubory detektoru pohybu
Úloha detekce pohybu vrátí soubor JSON ve výstupním datovém zdroji, který popisuje upozornění na pohyb a jejich kategorie v rámci videa. Soubor obsahuje informace o době a době trvání pohybu zjištěného ve videu.

Rozhraní API detektoru pohybu poskytuje indikátory, jakmile jsou objekty v pohybu v pevném videu na pozadí (například sledovací video). Detektor pohybu je vyškolen ke snížení falešných poplachů, jako jsou změny osvětlení a stínů. Aktuální omezení algoritmů zahrnují videa s nočním viděním, poloprůhledné objekty a malé objekty.

### <a name="elements-of-the-output-json-file"></a><a id="output_elements"></a>Prvky výstupního souboru JSON
> [!NOTE]
> V nejnovější verzi se změnil formát Výstup JSON a může představovat narušující změnu pro některé zákazníky.
> 
> 

Následující tabulka popisuje prvky výstupního souboru JSON.

| Element | Popis |
| --- | --- |
| version |To se vztahuje k verzi rozhraní API pro video. Aktuální verze je 2. |
| Časové osy |"Klíšťata" za sekundu videa. |
| posun |Časový posun pro časová razítka v "značek." Ve verzi 1.0 video API to bude vždy 0. V budoucích scénářích, které podporujeme, se tato hodnota může změnit. |
| Framerate |Počet snímků ve videu za sekundu. |
| šířka, výška |Odkazuje na šířku a výšku videa v pixelech. |
| start |Časové razítko zahájení v "tiky". |
| doba trvání |Délka události v "klíšťají". |
| interval |Interval každé položky v události v "klíšťatách". |
| stránka events |Každý fragment události obsahuje pohyb zjištěný během této doby trvání. |
| type |V aktuální verzi je to vždy '2' pro obecný pohyb. Tento popisek poskytuje video API flexibilitu kategorizovat pohyb v budoucích verzích. |
| regionId |Jak je vysvětleno výše, bude to vždy 0 v této verzi. Tento popisek poskytuje rozhraní API videa flexibilitu při hledání pohybu v různých oblastech v budoucích verzích. |
| Regiony |Odkazuje na oblast ve videu, kde vám záleží na pohybu. <br/><br/>-"id" představuje oblast regionu – v této verzi je pouze jeden, ID 0. <br/>-"typ" představuje tvar regionu, na které vám záleží. V současné době jsou podporovány "obdélník" a "polygon".<br/> Pokud jste zadali "obdélník", oblast má rozměry v X, Y, Šířka a Výška. Souřadnice X a Y představují levé horní souřadnice XY oblasti v normalizované mašaleo 0,0 až 1,0. Šířka a výška představují velikost oblasti v normalizované škále 0,0 až 1,0. V aktuální verzi X, Y, Šířka a Výška jsou vždy stanoveny na 0, 0 a 1, 1. <br/>Pokud jste zadali "polygon", oblast má rozměry v bodech. <br/> |
| Fragmenty |Metadata jsou rozdělena do různých segmentů nazývaných fragmenty. Každý fragment obsahuje začátek, dobu trvání, číslo intervalu a události. Fragment bez událostí znamená, že během tohoto počátečního času a doby trvání nebyl zjištěn žádný pohyb. |
| závorky [] |Každá závorka představuje jeden interval v události. Prázdné závorky pro tento interval znamenají, že nebyl zjištěn žádný pohyb. |
| Umístění |Tato nová položka v části události uvádí umístění, kde došlo k pohybu. To je konkrétnější než detekční zóny. |

Následující příklad JSON ukazuje výstup:

```json
    {
      "version": 2,
      "timescale": 23976,
      "offset": 0,
      "framerate": 24,
      "width": 1280,
      "height": 720,
      "regions": [
        {
          "id": 0,
          "type": "polygon",
          "points": [{'x': 0, 'y': 0},
            {'x': 0.5, 'y': 0},
            {'x': 0, 'y': 1}]
        }
      ],
      "fragments": [
        {
          "start": 0,
          "duration": 226765
        },
        {
          "start": 226765,
          "duration": 47952,
          "interval": 999,
          "events": [
            [
              {
                "type": 2,
                "typeName": "motion",
                "locations": [
                  {
                    "x": 0.004184,
                    "y": 0.007463,
                    "width": 0.991667,
                    "height": 0.985185
                  }
                ],
                "regionId": 0
              }
            ],
```

## <a name="limitations"></a>Omezení
* Podporované vstupní video formáty zahrnují MP4, MOV a WMV.
* Detekce pohybu je optimalizována pro stacionární videa na pozadí. Algoritmus se zaměřuje na snížení falešných poplachů, jako jsou změny osvětlení a stíny.
* Určitý pohyb nemusí být detekován z důvodu technických problémů; například videa s nočním viděním, poloprůhledné objekty a malé objekty.

## <a name="net-sample-code"></a>Ukázkový kód rozhraní .NET

Následující program ukazuje, jak:

1. Vytvořte datový zdroj a nahrajte do něj mediální soubor.
2. Vytvořte úlohu s úlohou detekce pohybu videa založenou na konfiguračním souboru, který obsahuje následující přednastavení json: 
   
    ```json
            {
            "Version": "1.0",
            "Options": {
                "SensitivityLevel": "medium",
                "FrameSamplingValue": 1,
                "DetectLightChange": "False",
                "MergeTimeThreshold":
                "00:00:02",
                "DetectionZones": [
                [
                    {"x": 0, "y": 0},
                    {"x": 0.5, "y": 0},
                    {"x": 0, "y": 1}
                ],
                [
                    {"x": 0.3, "y": 0.3},
                    {"x": 0.55, "y": 0.3},
                    {"x": 0.8, "y": 0.3},
                    {"x": 0.8, "y": 0.55},
                    {"x": 0.8, "y": 0.8},
                    {"x": 0.55, "y": 0.8},
                    {"x": 0.3, "y": 0.8},
                    {"x": 0.3, "y": 0.55}
                ]
                ]
            }
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

namespace VideoMotionDetection
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

            // Run the VideoMotionDetection job.
            var asset = RunVideoMotionDetectionJob(@"C:\supportFiles\VideoMotionDetection\BigBuckBunny.mp4",
                                        @"C:\supportFiles\VideoMotionDetection\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\VideoMotionDetection\Output");
        }

        static IAsset RunVideoMotionDetectionJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My Video Motion Detection Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Video Motion Detection Job");

            // Get a reference to Azure Media Motion Detector.
            string MediaProcessorName = "Azure Media Motion Detector";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Video Motion Detection Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Video Motion Detection Output Asset", AssetCreationOptions.None);

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
[Blog o detektoru pohybu azure media services](https://azure.microsoft.com/blog/motion-detector-update/)

[Přehled analýzy Mediálních služeb Azure](media-services-analytics-overview.md)

[Ukázky Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

