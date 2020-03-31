---
title: Redigovat tváře s Azure Media Analytics | Dokumenty společnosti Microsoft
description: Azure Media Redactor je mediální procesor Azure Media Analytics, který nabízí škálovatelnou redigování tváří v cloudu. Tento článek ukazuje, jak redigovat tváře pomocí azure media analytics.
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
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 6a1b7a76ef1efda51f09ac733b3d434235ff40ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900306"
---
# <a name="redact-faces-with-azure-media-analytics"></a>Redigovat tváře pomocí Azure Media Analytics 
## <a name="overview"></a>Přehled
**Azure Media Redactor** je mediální procesor [Azure Media Analytics](media-services-analytics-overview.md) (MP), který nabízí škálovatelnou redigování tváří v cloudu. Redigování obličeje umožňuje upravit video tak, aby rozmazalo tváře vybraných jedinců. Službu redigování obličeje můžete použít ve scénářích veřejné bezpečnosti a zpravodajských médií. Několik minut záznamu, který obsahuje více tváří, může trvat hodiny, než se ručně rediguje, ale s touto službou bude proces redigování obličeje vyžadovat jen několik jednoduchých kroků. Další informace naleznete v [tomto](https://azure.microsoft.com/blog/azure-media-redactor/) blogu.

Tento článek obsahuje podrobnosti o **Azure Media Redactor** a ukazuje, jak ji používat s Media Services SDK pro .NET.

## <a name="face-redaction-modes"></a>Režimy redigování obličeje
Redigování obličeje funguje tak, že detekuje tváře v každém snímku videa a sleduje objekt obličeje dopředu i dozadu v čase, takže stejný jedinec může být rozmazaný i z jiných úhlů. Automatizovaný proces redigování je složitý a ne vždy vytváří 100 % požadovaného výstupu, z tohoto důvodu vám služba Media Analytics poskytuje několik způsobů, jak upravit konečný výstup.

Kromě plně automatického režimu je k dispozici dvouprůchodový pracovní postup, který umožňuje výběr/devýběr nalezených ploch prostřednictvím seznamu ID. Chcete-li provést libovolné úpravy na snímek, mp používá soubor metadat ve formátu JSON. Tento pracovní postup je rozdělen do **režimu Analyzovat** a **redigovat.** Můžete kombinovat dva režimy v jednom průchodu, který spouští oba úkoly v jedné úloze; Tento režim se nazývá **Combined**.

### <a name="combined-mode"></a>Kombinovaný režim
Tím se automaticky vytvoří redigovaný mp4 bez ručního zadávání.

| Krok | Název souboru | Poznámky |
| --- | --- | --- |
| Vstupní majetek |foo.bar |Video ve formátu WMV, MOV nebo MP4 |
| Vstupní konfigurace |Přednastavení konfigurace úlohy |{'version':'1.0', 'options': {'mode':'combined'}} |
| Výstupní majetek |foo_redacted.mp4 |Video s použitým rozostřením |

#### <a name="input-example"></a>Příklad vstupu:
[zobrazit toto video](https://ampdemo.azureedge.net/?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fed99001d-72ee-4f91-9fc0-cd530d0adbbc%2FDancing.mp4)

#### <a name="output-example"></a>Příklad výstupu:
[zobrazit toto video](https://ampdemo.azureedge.net/?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc6608001-e5da-429b-9ec8-d69d8f3bfc79%2Fdance_redacted.mp4)

### <a name="analyze-mode"></a>Analyzovat režim
Průchod **analýzy** dvouprůchodového pracovního postupu přebírá vstup videa a vytváří soubor JSON s umístěními ploch a jpg obrazy každé zjištěné plochy.

| Krok | Název souboru | Poznámky |
| --- | --- | --- |
| Vstupní majetek |foo.bar |Video ve formátu WMV, MPV nebo MP4 |
| Vstupní konfigurace |Přednastavení konfigurace úlohy |{'version':'1.0', 'options': {'mode':'analyze'}} |
| Výstupní majetek |foo_annotations.json |Anotační data umístění ploch ve formátu JSON. Uživatel ji může upravit tak, aby upravoval ohraničovací rámečky rozostření. Viz ukázka níže. |
| Výstupní majetek |foo_thumb%06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |Oříznutý jpg každé zjištěné plochy, kde číslo označuje označení Id plochy |

#### <a name="output-example"></a>Příklad výstupu:

```json
    {
      "version": 1,
      "timescale": 24000,
      "offset": 0,
      "framerate": 23.976,
      "width": 1280,
      "height": 720,
      "fragments": [
        {
          "start": 0,
          "duration": 48048,
          "interval": 1001,
          "events": [
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [
              {
                "index": 13,
                "id": 1138,
                "x": 0.29537,
                "y": -0.18987,
                "width": 0.36239,
                "height": 0.80335
              },
              {
                "index": 13,
                "id": 2028,
                "x": 0.60427,
                "y": 0.16098,
                "width": 0.26958,
                "height": 0.57943
              }
            ],

    … truncated
```

### <a name="redact-mode"></a>Režim redigování
Druhý průchod pracovního postupu trvá větší počet vstupů, které musí být sloučeny do jednoho datového zdroje.

To zahrnuje seznam ID rozostření, původní video a poznámky JSON. Tento režim používá poznámky k aplikování rozostření na vstupní video.

Výstup z průchodu Analyzovat neobsahuje původní video. Video musí být nahráno do vstupního datového zdroje pro úlohu režimu Redact a vybráno jako primární soubor.

| Krok | Název souboru | Poznámky |
| --- | --- | --- |
| Vstupní majetek |foo.bar |Video ve formátu WMV, MPV nebo MP4. Stejné video jako v kroku 1. |
| Vstupní majetek |foo_annotations.json |soubor metadat anotací z první fáze s volitelnými úpravami. |
| Vstupní majetek |foo_IDList.txt (volitelné) |Volitelný nový řádek oddělený seznam id plochy redigovat. Pokud zůstane prázdná, rozostří to všechny tváře. |
| Vstupní konfigurace |Přednastavení konfigurace úlohy |{'version':'1.0', 'options': {'mode':'redact'}} |
| Výstupní majetek |foo_redacted.mp4 |Video s rozostřením na základě anotací |

#### <a name="example-output"></a>Příklad výstupu
Toto je výstup z IDList s jedním ID vybrané.

[zobrazit toto video](https://ampdemo.azureedge.net/?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fad6e24a2-4f9c-46ee-9fa7-bf05e20d19ac%2Fdance_redacted1.mp4)

Příklad foo_IDList.txt
 
     1
     2
     3

## <a name="blur-types"></a>Typy rozostření

V režimu **Combined** nebo **Redact** je k dispozici 5 různých režimů rozostření, ze kterých si můžete vybrat prostřednictvím vstupní konfigurace JSON: **Nízká**, **Med**, **Vysoká**, **Box**a **Černá**. Ve výchozím nastavení se používá **Med.**

Ukázky typů rozostření naleznete níže.

### <a name="example-json"></a>Příklad JSON:

```json
    {'version':'1.0', 'options': {'Mode': 'Combined', 'BlurType': 'High'}}
```

#### <a name="low"></a>Nízká

![Nízká](./media/media-services-face-redaction/blur1.png)
 
#### <a name="med"></a>Med

![Med](./media/media-services-face-redaction/blur2.png)

#### <a name="high"></a>Vysoká

![Vysoká](./media/media-services-face-redaction/blur3.png)

#### <a name="box"></a>Box

![Box](./media/media-services-face-redaction/blur4.png)

#### <a name="black"></a>Black

![Black](./media/media-services-face-redaction/blur5.png)

## <a name="elements-of-the-output-json-file"></a>Prvky výstupního souboru JSON

Redakce MP poskytuje vysoce přesné rozpoznávání polohy obličeje a sledování, které dokáže detekovat až 64 lidských tváří v rámci videa. Nejlepší výsledky poskytují čelní plochy, zatímco boční plochy a malé plochy (menší nebo rovné 24 x 24 pixelům) jsou náročné.

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

## <a name="net-sample-code"></a>Ukázkový kód rozhraní .NET

Následující program ukazuje, jak:

1. Vytvořte datový zdroj a nahrajte do něj mediální soubor.
2. Vytvořte úlohu s úlohou redigování obličeje na základě konfiguračního souboru, který obsahuje následující přednastavení json: 

    ```json
            {
                'version':'1.0',
                'options': {
                    'mode':'combined'
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

namespace FaceRedaction
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

            // Run the FaceRedaction job.
            var asset = RunFaceRedactionJob(@"C:\supportFiles\FaceRedaction\SomeFootage.mp4",
                        @"C:\supportFiles\FaceRedaction\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\FaceRedaction\Output");
        }

        static IAsset RunFaceRedactionJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
            "My Face Redaction Input Asset",
            AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Face Redaction Job");

            // Get a reference to Azure Media Redactor.
            string MediaProcessorName = "Azure Media Redactor";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Face Redaction Task",
            processor,
            configuration,
            TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Face Redaction Output Asset", AssetCreationOptions.None);

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

## <a name="next-steps"></a>Další kroky

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Související odkazy
[Přehled analýzy Mediálních služeb Azure](media-services-analytics-overview.md)

[Ukázky Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

