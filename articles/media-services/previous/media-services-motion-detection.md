---
title: Zjištění pohyby s Azure Media Analytics | Microsoft Docs
description: Procesor médií detektor pohybu médií Azure (PP) umožňuje efektivně identifikaci části týkající se v rámci souboru jinak dlouhé a bezproblémové video.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/09/2017
ms.author: milanga;juliako;
ms.openlocfilehash: 8488b968fe2ab823479d70a98ba86be97b28f67d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="detect-motions-with-azure-media-analytics"></a>Zjištění pohyby s Azure Media Analytics
## <a name="overview"></a>Přehled
**Detektor pohybu médií Azure** procesor médií (PP) umožňuje efektivně identifikaci části týkající se v rámci souboru jinak dlouhé a bezproblémové video. Detekce pohybu dají použít na statické kamer k identifikaci části videa, kde dochází k pohybu. Vygeneruje soubor JSON obsahující metadata s časová razítka a ohraničující oblasti, kde došlo k události.

Cílem směrem zabezpečení video informační kanály, tato technologie je možné zařadit do kategorií pohybu do příslušné události a falešně pozitivních například stínů a osvětlení změny. To umožňuje generovat výstrahy zabezpečení z fotoaparátu kanály bez nevyžádané pošty s nekonečná důležité události, při schopnost extrahovat z dlouho sledováním videa situacích, které vás zajímají.

**Detektor pohybu médií Azure** MP je aktuálně ve verzi Preview.

Tento článek obsahuje údaje o **detektor pohybu médií Azure** a ukazuje, jak pomocí sady Media Services SDK pro .NET

## <a name="motion-detector-input-files"></a>Vstupní soubory detekce pohybu
Video soubory. V současné době jsou podporovány následující formáty: MP4, MOV a WMV.

## <a name="task-configuration-preset"></a>Konfigurace úlohy (přednastavených)
Při vytváření úlohy s **detektor pohybu médií Azure**, je nutné zadat jedno z přednastavení konfigurace. 

### <a name="parameters"></a>Parametry
Můžete použít následující parametry:

| Název | Možnosti | Popis | Výchozí |
| --- | --- | --- | --- |
| sensitivityLevel |Řetězce: "nízká", 'střední', 'vysoká. |Nastaví úroveň citlivosti na pohyby, které jsou hlášeny. Upravte tak, aby upravit počet falešně pozitivních zjištění. |"střední" |
| frameSamplingValue |Kladné celé číslo |Nastaví frekvenci, na kterých běží algoritmus. každý snímek se rovná 1, 2 znamená každý druhý snímek a tak dále. |1 |
| detectLightChange |Logická hodnota: "PRAVDA", "Nepravda" |Nastaví, zda se ve výsledcích hlásí světla změny |"Nepravda" |
| mergeTimeThreshold |Čas xs: Hh: mm:<br/>Příklad: 00:00:03 |Určuje časový interval mezi pohybu událostí, kde jsou 2 události být kombinaci a hlášený jako 1. |00:00:00 |
| detectionZones |Pole detekce zóny:<br/>-Detekce zóny je pole 3 nebo více bodů<br/>– Bod je x a y souřadnic od 0 do 1. |Popisuje seznamu detekce polygonálních zón, který se má použít.<br/>Výsledky jsou uvedeny s zóny jako ID, se první z nich probíhá 'id': 0 |Jedné oblasti, které zahrnuje celou rámečku. |

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

## <a name="motion-detector-output-files"></a>Pohybu detektor výstupní soubory
Úlohu detekce pohybu vrátí soubor JSON v výstupní asset, která popisuje výstrahy pohybu a jejich kategorie, v rámci videa. Soubor obsahuje informace o čas a dobu trvání pohybu zjistil ve videu.

Rozhraní API detektor pohybu poskytuje indikátory, jakmile jsou objekty v pohybu pevné pozadí videa (například sledováním video). Detektor pohybu je vycvičena ke snížení falešné výstrahy, jako je například osvětlení a stínové změny. Aktuální omezení algoritmů zahrnují noci vize videa, poloprůhledné objekty a malé objekty.

### <a id="output_elements"></a>Elementy výstupního souboru JSON
> [!NOTE]
> Nejnovější verze formátu JSON výstupu se změnil a pro někteří zákazníci mohou představovat narušující změně.
> 
> 

Následující tabulka popisuje elementy výstupního souboru JSON.

| Element | Popis |
| --- | --- |
| Verze |Vztahuje se na verzi rozhraní API Video. Aktuální verze je 2. |
| Časová osa |"Rysky" za sekundu videa. |
| Posun |Časový posun pro časová razítka v "rysky." Ve verzi 1.0 rozhraní API, Video bude vždy 0. V budoucích scénáře, které podporujeme, tato hodnota může změnit. |
| kmitočet snímků |Počet snímků za sekundu videa. |
| Šířka, Výška |Odkazuje na šířku a výšku videa v pixelech. |
| Start |Razítka start "rysky". |
| Doba trvání |Délka události v "rysky". |
| Interval |Interval každou položku v události v "rysky". |
| Události |Každý fragment událostí obsahuje pohybu během této doby trvání. |
| Typ |V aktuální verzi je to vždy (2) pro obecné pohybu. Tento popisek poskytuje rozhraní API Video flexibilitu zařadit do kategorií pohybu v budoucích verzích. |
| RegionID |Jak je popsáno výše, bude vždy 0 v této verzi. Tento popisek poskytuje rozhraní API Video flexibilitu najít pohybu v různých oblastech v budoucích verzích. |
| Oblasti |Odkazuje na oblasti v videa, kde se zajímáte o pohybu. <br/><br/>-"id" představuje oblasti oblast – v této verzi je jen jeden, ID 0. <br/>-"typ" představuje obrazec oblasti, která se zajímáte o pro pohybu. V současné době jsou podporovány "Obdélník" a "mnohoúhelníku".<br/> Pokud jste zadali "Obdélník", oblast má dimenzí v X, Y, šířka a výška. Souřadnice X a Y představují horní levé XY souřadnice oblasti v normalizovaný škále od 0,0 do 1,0. Šířka a výška představují velikost oblasti v normalizovaný škále od 0,0 do 1,0. V aktuální verzi X, Y, šířka a výška vždy stanoví na 0, 0 a 1, 1. <br/>Pokud jste zadali "mnohoúhelníku", má oblast dimenzí v bodech. <br/> |
| fragmenty |Metadata se blokové až do různých segmentů názvem fragmenty. Každý fragment obsahuje počáteční, doba trvání, číslo intervalu a událostí. Fragment s žádné události znamená, že žádné pohybu byl nalezen během této počáteční čas a dobu trvání. |
| Hranaté závorky] |Každá závorka představuje jeden interval v události. Byla zjištěna prázdný závorky pro tento interval znamená, že žádné pohybu. |
| Umístění |Tento nový záznam v části události uvádí umístění, kde došlo k chybě provozu. Toto je konkrétnější než detekce zóny. |

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
* Podporované formáty vstupní video zahrnují MP4, MOV a WMV.
* Detekce pohybu je optimalizovaná pro stojící pozadí videa. Algoritmus se zaměřuje na snížení falešné výstrahy, jako je například změny osvětlení a stínů.
* Některé pohybu nemusí být detekována z důvodu technické problémy; například noci vize videa, poloprůhledné objekty a malé objekty.

## <a name="net-sample-code"></a>Ukázkový kód rozhraní .NET

Program zobrazí následující postup:

1. Vytvořte asset a nahrajte soubor média do assetu.
2. Vytvořte úlohu s úkolem detekce pohybu na videu podle konfigurační soubor, který obsahuje následující přednastavení json: 
   
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

3. Stáhněte soubory JSON výstupu. 

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
            task.OutputAssets.AddNew("My Video Motion Detectoion Output Asset", AssetCreationOptions.None);

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
[Blog Azure detektor pohybu Media Services](https://azure.microsoft.com/blog/motion-detector-update/)

[Azure Media Services Analytics – přehled](media-services-analytics-overview.md)

[Ukázky služby Azure Media Analytics](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

