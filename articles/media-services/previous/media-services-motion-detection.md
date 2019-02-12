---
title: Zjištění mají s Azure Media Analytics | Dokumentace Microsoftu
description: Procesor médií Azure Media Motion Detectoru (PP) umožňuje efektivně identifikují oddíly zájmu v rámci jinak dlouhé a bezproblémové videa.
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
ms.date: 02/10/2019
ms.author: milanga;juliako;
ms.openlocfilehash: 26090067923c468b7102ac5b7bb78b9d7b7960bb
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55995608"
---
# <a name="detect-motions-with-azure-media-analytics"></a>Zjištění mají s Azure Media Analytics
## <a name="overview"></a>Přehled
**Detektor pohybu médií Azure** procesor médií (PP) umožňuje efektivně identifikují oddíly zájmu v rámci jinak dlouhé a bezproblémové videa. Detekce pohybu lze použít na statické kamer k identifikaci části videa, kde dochází k pohybu. Generuje soubor JSON obsahující metadata s časová razítka a ohraničující konkrétní oblasti, kde k události došlo.

Zaměřené na zabezpečení videa informační kanály, tato technologie je schopen kategorizace provozu do relevantní události a falešně pozitivních zpráv, jako je například stíny a změn osvětlení. To umožňuje generovat výstrahy zabezpečení z fotoaparátu kanálů bez nevyžádané pošty s nekonečnou relevantní události, při zachování nedokáže extrahovat okamžiků zájmu z dlouhé bezpečnostních videí.

**Detektor pohybu médií Azure** sady Management Pack je aktuálně ve verzi Preview.

Tento článek obsahuje podrobnosti o **detektor pohybu médií Azure** a ukazuje, jak používat ve službě Media Services SDK pro .NET

## <a name="motion-detector-input-files"></a>Vstupní soubory Motion Detectoru
Video soubory. V současné době jsou podporovány následující formáty: MP4, MOV a WMV.

## <a name="task-configuration-preset"></a>Konfigurace úlohy (nastavení)
Při vytváření úlohy s **detektor pohybu médií Azure**, je nutné zadat nastavení konfigurace. 

### <a name="parameters"></a>Parametry
Můžete použít následující parametry:

| Název | Možnosti | Popis | Výchozí |
| --- | --- | --- | --- |
| sensitivityLevel |Řetězec: "nízká", "střední", "vysoká" |Nastaví úroveň citlivosti na které mají jsou hlášeny. Upravte tak, aby upravit počet falešně pozitivních výsledků. |"střední" |
| frameSamplingValue |kladné celé číslo |Nastaví frekvenci, na které běhy algoritmus. každý snímek se rovná 1, 2 znamená, že každý druhý snímek a tak dále. |1 |
| detectLightChange |Boolean: "PRAVDA", "Nepravda" |Nastaví, zda jsou hlášeny změn ve výsledcích |"Nepravda" |
| mergeTimeThreshold |Čas xs: Hh: mm:<br/>Příklad: 00:00:03 |Určuje časový interval mezi událostmi pohybu, kde jsou 2 události možné kombinovat a předávat jako 1. |00:00:00 |
| detectionZones |Pole detekce zóny:<br/>-Zjišťování zóny je pole, 3 nebo více bodů<br/>– Bod je x a y souřadnic od 0 do 1. |Popisuje seznamu mnohoúhelníkové detekce zón, který se má použít.<br/>Výsledky jsou hlášeny s zóny jako Identifikátor, první z nich se 'id': 0 |Jedné zóně, což pokrývá celý snímek. |

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

## <a name="motion-detector-output-files"></a>Výstupní soubory Motion Detectoru
Úlohu detekce pohybu vrátí soubor JSON v prostředku výstupu, který popisuje výstrahy pohybu a jejich kategorie, ve videu. Soubor obsahuje informace o času a dobu trvání pohybu zjistil ve videu.

Rozhraní API detektoru pohybu poskytuje indikátory po objekty v pohybu pevné pozadí videa (například dohledu videa). Detektor pohybu je vyškolit tak, aby redukovat plané poplachy, jako je například osvětlení a stínové změny. Aktuální omezení algoritmů obsahují noční vizi videa, poloprůhledného objekty a malé objekty.

### <a id="output_elements"></a>Prvky výstupního souboru JSON
> [!NOTE]
> V nejnovější verzi formátu výstupu JSON došlo ke změně a může představovat rozbíjející změny pro některé zákazníky.
> 
> 

Následující tabulka popisuje prvky výstupního souboru JSON.

| Element | Popis |
| --- | --- |
| Verze |To se vztahuje na verzi rozhraní API pro Video. Aktuální verze je 2. |
| Timescale |"Značky" za sekundu videa. |
| Posun |Posun času pro časová razítka v "impulzech." Ve verzi 1.0 rozhraní API pro Video bude vždy 0. V budoucnu scénáře, které podporujeme, tato hodnota se může změnit. |
| Framerate |Počet snímků ve videu za sekundu. |
| Šířka, Výška |Odkazuje na šířku a výšku v pixelech. |
| Start |Časové razítko zahájení v "impulzech". |
| Doba trvání |Délka události v "značky". |
| Interval |Interval každá položka v události v "značky". |
| Události |Každá událost fragment obsahuje pohybu, během tohoto dobu trvání. |
| Type |V aktuální verzi je to vždy pro obecné pohybu ' 2'. Tento popisek poskytuje rozhraní API pro Video flexibilitu zařadit do pohybu v budoucích verzích. |
| RegionID |Jak jsme vysvětlili výše, bude vždy 0 v této verzi. Tento popisek poskytuje rozhraní Video API flexibilitu při hledání pohybu v různých oblastech v budoucích verzích. |
| Oblasti |Odkazuje na oblast ve videu, pokud vás zajímají pohybu. <br/><br/>-"id" představuje oblasti oblast – v této verzi je pouze jeden, ID 0. <br/>-"type" představuje obrazec oblasti záleží pro pohybu. V současné době jsou podporovány "Obdélník" a "mnohoúhelníku".<br/> Pokud jste zadali "Obdélník", oblast má dimenze v X, Y, šířku a výšku. Souřadnice X a Y představují horní levé prvků jejich souřadnice XY oblasti v normalizovaných rozsahu od 0,0 do 1,0. Šířku a výšku reprezentaci velikosti oblasti v normalizovaných rozsahu od 0,0 do 1,0. V aktuální verzi jsou X, Y, šířka a výška vždy pevně nastavena na 0, 0 a 1, 1. <br/>Pokud jste zadali "mnohoúhelníku", má oblast rozměrů v bodech. <br/> |
| Fragments |Metadata je rozdělený do bloků dat až do různých segmentů zvaných fragmenty. Každý fragment obsahuje začátek, dobu trvání, číslo intervalu a události. Fragment se žádné události znamená, že se během tohoto čas spuštění a trvání zjistil bez pohybu. |
| Hranaté závorky] |Každá závorka představuje jeden interval v události. Byla zjištěna prázdné závorky pro tohoto intervalu znamená, že bez pohybu. |
| umístění |Tento nový záznam v rámci události obsahuje umístění, kde k pohybu došlo. Toto je konkrétnější než detekce zóny. |

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
* Podporované formáty vstupního videa zahrnout MP4, MOV a WMV.
* Detekce pohybu je optimalizovaná pro videa setrvá na pozadí. Algoritmus se zaměřuje na snížení falešných poplachů, jako je například změny osvětlení a shadows.
* Některé pohybu nemusí být detekována kvůli technické komplikace; například noční videa pro zpracování obrazu, poloprůhledného objekty a malé objekty.

## <a name="net-sample-code"></a>Vzorový kód .NET

Následující program ukazuje postup:

1. Vytvoření assetu a uložit do assetu soubor média.
2. Vytvořte úlohu s úkolem detekce pohybu na videu založené na konfigurační soubor, který obsahuje následující přednastavení json: 
   
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

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Související odkazy
[Blog o Azure Media Services Motion Detectoru](https://azure.microsoft.com/blog/motion-detector-update/)

[Azure Media Services Analytics Overview](media-services-analytics-overview.md)

[Azure Media Analytics demos](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

