---
title: Detekovat pohyby pomocí Azure Media Analytics | Microsoft Docs
description: Procesor Azure Media Motion Detector Media (MP) umožňuje efektivně identifikovat oddíly zájmu v jiném dlouhodobém a nenáročném videu.
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
ms.openlocfilehash: c053e4dfc38fc0f055ec91a6622ef7f767c13a86
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "69015323"
---
# <a name="detect-motions-with-azure-media-analytics"></a>Detekce pohybů pomocí Azure Media Analytics
## <a name="overview"></a>Přehled
Procesor **Azure Media Motion Detector** Media (MP) umožňuje efektivně identifikovat oddíly zájmu v jiném dlouhodobém a nenáročném videu. Detekce pohybu se dá použít ve statických záběrech fotoaparátu k identifikaci částí videa, kde dochází k pohybu. Vygeneruje soubor JSON obsahující metadata s časovými razítky a ohraničující oblastí, kde došlo k události.

Tato technologie je zaměřená na kanály videa o zabezpečení, takže je možné kategorizovat pohyb do relevantních událostí a falešně pozitivních změn, jako jsou například stíny a osvětlení. Díky tomu můžete generovat výstrahy zabezpečení z kanálů kamery, aniž byste museli být zasíláni proti nevyžádané práci s nepodstatnými nepodstatnými událostmi, a zároveň je možné extrahovat oznámení z dlouhých videí o kontrole.

Sada **Azure Media Motion Detector** MP je momentálně ve verzi Preview.

Tento článek obsahuje podrobné informace o **Azure Media Motion Detector** a ukazuje, jak ho používat s Media Services SDK pro .NET.

## <a name="motion-detector-input-files"></a>Vstupní soubory snímače pohybu
Videosoubory. V současné době jsou podporovány následující formáty: MP4, MOV a WMV.

## <a name="task-configuration-preset"></a>Konfigurace úlohy (předvolba)
Při vytváření úlohy s **Azure Media Motion Detector**je nutné zadat předvolby konfigurace. 

### <a name="parameters"></a>Parametry
Můžete použít následující parametry:

| Name | Možnosti | Popis | Výchozí |
| --- | --- | --- | --- |
| sensitivityLevel |Řetězec: ' nízká ', ' střední ', ' High ' |Nastaví úroveň citlivosti, na které jsou nahlášeny pohyby. Upravte tuto hodnotu pro úpravu počtu falešně pozitivních hodnot. |úrovně |
| frameSamplingValue |Kladné celé číslo |Nastaví četnost spuštění algoritmu. 1 se rovná každému snímku, 2 znamená každý druhý rámec atd. |1 |
| detectLightChange |Boolean: ' true ', ' false ' |Nastaví, zda jsou ve výsledcích hlášeny světlé změny. |Chybné |
| mergeTimeThreshold |Čas xs: Hh: mm: SS<br/>Příklad: 00:00:03 |Určuje časový interval mezi událostmi pohybu, kde jsou dvě události kombinovány a hlášeny jako 1. |00:00:00 |
| detectionZones |Pole zón detekce:<br/>-Zóna detekce je pole o 3 nebo více bodech.<br/>-Point je souřadnice x a y od 0 do 1. |Popisuje seznam oblastí detekce mnohoúhelníku, které se mají použít.<br/>Výsledky se nahlásí se zónami jako s ID a prvním identifikátorem ID: 0. |Jedna zóna, která pokrývá celý rámec. |

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
Úloha detekce pohybu vrátí soubor JSON ve výstupním prostředku, který popisuje výstrahy pohybu a jejich kategorie v rámci videa. Soubor obsahuje informace o čase a době trvání pohybu zjištěného ve videu.

Rozhraní API detektoru pohybu poskytuje indikátory, jakmile se v pevném videu na pozadí vyskytnou objekty (například sledovací video). Detektor pohybu je vyškolený k omezení falešných poplachů, jako jsou například změny osvětlení a stín. Současná omezení algoritmů zahrnují videa o nočních a částečně transparentních objektech a malých objektech.

### <a id="output_elements"></a>Prvky výstupního souboru JSON
> [!NOTE]
> V nejnovější verzi se výstupní formát JSON změnil a může představovat zásadní změnu pro některé zákazníky.
> 
> 

Následující tabulka popisuje prvky výstupního souboru JSON.

| Prvek | Popis |
| --- | --- |
| version |To odkazuje na verzi rozhraní API pro video. Aktuální verze je 2. |
| timescale |"Takty" za sekundu videa. |
| offset |Časový posun pro časová razítka v "taktech". Ve verzi 1,0 rozhraní API pro video bude tato hodnota vždycky 0. V budoucích scénářích, které podporujeme, se tato hodnota může změnit. |
| framerate |Počet snímků ve videu za sekundu. |
| width, height |Odkazuje na šířku a výšku videa v pixelech. |
| start |Počáteční časové razítko v "taktech". |
| duration |Délka události v "taktech". |
| interval |Interval každého záznamu v události, v "taktech". |
| events |Každý fragment události obsahuje pohyb zjištěný během tohoto časového období. |
| type |V aktuální verzi je to vždycky "2" pro obecný pohyb. Tento popisek poskytuje rozhraní API pro video, které flexibilita rozděluje pohyb v budoucích verzích. |
| regionId |Jak bylo vysvětleno výše, bude tato verze vždy 0 v této verzi. Tento popisek poskytuje rozhraní API pro video, které nabízí flexibilitu při hledání pohybu v různých oblastech v budoucích verzích. |
| regions |Odkazuje na oblast ve videu, kde se zajímáte o pohybu. <br/><br/>-"ID" představuje oblast oblasti – v této verzi je pouze jeden identifikátor 0. <br/>– "typ" představuje tvar oblasti, o které se zajímáte pro pohyb. V současné době jsou podporovány "obdélníky" a "mnohoúhelník".<br/> Pokud jste zadali "Rectangle", oblast má rozměry v X, Y, šířce a výšce. Souřadnice X a Y reprezentují horní levý horní souřadnici oblasti v normalizovaném rozsahu od 0,0 do 1,0. Šířka a výška představuje velikost oblasti v normalizovaném rozsahu od 0,0 do 1,0. V aktuální verzi jsou hodnoty X, Y, Šířka a výška vždy opraveny na 0, 0 a 1, 1. <br/>Pokud jste zadali "mnohoúhelník", má oblast rozměry v bodech. <br/> |
| fragments |Metadata jsou rozdělená do různých segmentů s názvem fragmenty. Každý fragment obsahuje začátek, dobu trvání, číslo intervalu a události. Fragment bez událostí znamená, že během tohoto počátečního času a doby trvání nebyl zjištěn žádný pohyb. |
| brackets [] |Každá závorka představuje jeden interval v události. Prázdné hranaté závorky pro tento interval znamenají, že nebyl zjištěn žádný pohyb. |
| locations |Tato nová položka v části události uvádí umístění, kde k pohybu došlo. Tato informace je konkrétnější než zóny detekce. |

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
* Mezi podporované vstupní formáty videa patří MP4, MOV a WMV.
* Detekce pohybu je optimalizovaná pro stacionární videa na pozadí. Algoritmus se zaměřuje na snížení falešných poplachů, jako jsou například změny osvětlení a stíny.
* Určitý pohyb nemusí být zjištěn v důsledku technických výzev; například videa o nočních a částečně transparentních objektech a malých objektech.

## <a name="net-sample-code"></a>Vzorový kód .NET

Následující program ukazuje, jak:

1. Vytvořte Asset a nahrajte do něj mediální soubor.
2. Vytvořte úlohu s úkolem detekce pohybu videa na základě konfiguračního souboru, který obsahuje následující předvolbu JSON: 
   
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

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Související odkazy
[Blog o Azure Media Services detektoru pohybu](https://azure.microsoft.com/blog/motion-detector-update/)

[Přehled analýzy Azure Media Services](media-services-analytics-overview.md)

[Ukázky Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

