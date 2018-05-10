---
title: Redigovat řezy s Azure Media Analytics | Microsoft Docs
description: Toto téma ukazuje, jak redigovat řezy s Azure media analytics.
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
ms.author: juliako;
ms.openlocfilehash: 910cc246aa19e19b109fc660682c6b2dc239cbb7
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="redact-faces-with-azure-media-analytics"></a>Redigovat řezy s Azure Media Analytics
## <a name="overview"></a>Přehled
**Azure Media Redactor** je [Azure Media Analytics](media-services-analytics-overview.md) procesor médií (PP), která nabízí redigování škálovatelné řez v cloudu. Vzhled redigování umožní vám upravit videa k rozostření řezy vybrané jednotlivce. Můžete použít službu redigování řez ve veřejné scénáře zabezpečení a média zprávy. Pár minut záznamů, která obsahuje více řezy může trvat hodiny redigovat ručně, ale s touto službou proces redigování řez bude vyžadovat několika jednoduchých kroků. Další informace najdete v tématu [to](https://azure.microsoft.com/blog/azure-media-redactor/) blogu.

Tento článek obsahuje údaje o **Azure Media Redactor** a ukazuje, jak pomocí sady Media Services SDK pro .NET.

## <a name="face-redaction-modes"></a>Vzhled redigování režimy
Obličeje redigování funguje zjišťování tyto řezy v každém snímku videa a sledováním vzhled objekt obou dopředný a zpětně v průběhu času, aby stejné jednotlivých můžete hranice z jiných úhly také. Proces automatického redigování je složitý a nemá vždy produktu 100 % požadované výstupu z tohoto důvodu Media Analytics nabízí několik způsobů, jak upravit finální výstup.

Kromě plně automatickými režimu je dva průchodu pracovní postup umožňuje výběr nebo deaktivuje-selection nalezen ploch prostřednictvím seznam identifikátorů. Také aby libovolný za rámce úpravy MP používá soubor metadat ve formátu JSON. Tento pracovní postup je rozdělená do **analyzovat** a **Redact** režimy. Zkombinováním dva režimy při jednom průchodu používající obě úlohy v úloh. Tento režim je volána **kombinované**.

### <a name="combined-mode"></a>Kombinovaná režimu
To vytváří zredigované mp4 automaticky bez jakékoli ruční vstup.

| Krok | Název souboru | Poznámky |
| --- | --- | --- |
| Vstupní prostředek |foo.bar |Video ve formátu WMV, MOV nebo MP4 |
| Vstupní konfigurace |Předvolby úlohy konfigurace |{'version':'1.0 ', 'možnosti': {"režim": "kombinovanou"}} |
| Výstupní asset |foo_redacted.mp4 |Video s stírá použít |

#### <a name="input-example"></a>Vstupní příklad:
[přehrát toto video](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fed99001d-72ee-4f91-9fc0-cd530d0adbbc%2FDancing.mp4)

#### <a name="output-example"></a>Příklad výstupu:
[přehrát toto video](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc6608001-e5da-429b-9ec8-d69d8f3bfc79%2Fdance_redacted.mp4)

### <a name="analyze-mode"></a>Analýza režimu
**Analyzovat** průchodu dva průchodu pracovního postupu přebírá vstup videa a vytvoří soubor JSON místa vzhled a obrázků jpg jednotlivých zjistila řez.

| Krok | Název souboru | Poznámky |
| --- | --- | --- |
| Vstupní prostředek |foo.bar |Video ve formátu WMV, MPV nebo MP4 |
| Vstupní konfigurace |Předvolby úlohy konfigurace |{'version':'1.0 ', 'možnosti': {'režimu': 'analyzovat.}} |
| Výstupní asset |foo_annotations.json |Poznámky data vzhled umístění ve formátu JSON. To se dá upravit uživatel k úpravě stírá ohraničujícího polí. Viz následující ukázka. |
| Výstupní asset |foo_thumb%06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |Oříznutý jpg jednotlivých zjistil vzhled, kde číslo udává labelId písmo |

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

### <a name="redact-mode"></a>Redigovat režimu
Druhé fázi pracovního postupu přebírá větší počet vstupních hodnot, které musí zkombinovat do jednoho datového zdroje.

To zahrnuje seznam ID k rozostření, původní video a poznámky JSON. Tento režim pomocí poznámky použije stírá na vstup videa.

Výstup z průchodu analyzovat nezahrnuje původní video. Video je třeba nahraje do vstupní asset pro úlohu režimu Redact a vybrat jako primární soubor.

| Krok | Název souboru | Poznámky |
| --- | --- | --- |
| Vstupní prostředek |foo.bar |Video ve formátu WMV, MPV nebo MP4. Stejné jako v kroku 1 videa. |
| Vstupní prostředek |foo_annotations.json |Soubor metadat poznámky z první fázi, pomocí volitelné úpravy. |
| Vstupní prostředek |foo_IDList.txt (volitelné) |Volitelné nový řádek oddělený seznam vzhled ID chcete redigovat. Pokud necháte prázdnou, tato rozostří všechny řezy. |
| Vstupní konfigurace |Předvolby úlohy konfigurace |{'version':'1.0 ', 'možnosti': {'režimu': 'redigovat.}} |
| Výstupní asset |foo_redacted.mp4 |Video s stírá použít podle poznámky |

#### <a name="example-output"></a>Příklad výstupu
Toto je výstup IDList s jedno ID vybrané.

[přehrát toto video](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fad6e24a2-4f9c-46ee-9fa7-bf05e20d19ac%2Fdance_redacted1.mp4)

Příklad foo_IDList.txt
 
     1
     2
     3

## <a name="blur-types"></a>Rozostření typy

V **kombinované** nebo **Redact** režimu, existují 5 rozostření různé režimy můžete vybrat z prostřednictvím konfigurace vstupu JSON: **nízká**, **Med**, **Vysokou**, **pole**, a **černé**. Ve výchozím nastavení **Med** se používá.

Můžete najít ukázky níže rozostření typů.

### <a name="example-json"></a>Příklad JSON:

```json
    {'version':'1.0', 'options': {'Mode': 'Combined', 'BlurType': 'High'}}
```

#### <a name="low"></a>Nízká

![Nízká](./media/media-services-face-redaction/blur1.png)
 
#### <a name="med"></a>Med

![Med](./media/media-services-face-redaction/blur2.png)

#### <a name="high"></a>Vysoký

![Vysoký](./media/media-services-face-redaction/blur3.png)

#### <a name="box"></a>Box

![Box](./media/media-services-face-redaction/blur4.png)

#### <a name="black"></a>Černá

![Černá](./media/media-services-face-redaction/blur5.png)

## <a name="elements-of-the-output-json-file"></a>Elementy výstupního souboru JSON

MP redigování poskytuje vysokou přesnost vzhled umístění detekce a sledování, může zjistit až 64 lidského řezy snímek videa. Čelní řezy zadejte nejlepších výsledků dosáhnete, při straně řezy a malé řezy (je menší než nebo rovno 24 x 24 pixelů) jsou dost náročné.

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

## <a name="net-sample-code"></a>Ukázkový kód rozhraní .NET

Program zobrazí následující postup:

1. Vytvořte asset a nahrajte soubor média do assetu.
2. Vytvořte úlohu s úkolem redigování vzhled podle konfigurační soubor, který obsahuje následující přednastavení json: 

    ```json
            {
                'version':'1.0',
                'options': {
                    'mode':'combined'
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

## <a name="next-steps"></a>Další postup

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Související odkazy
[Azure Media Services Analytics – přehled](media-services-analytics-overview.md)

[Ukázky služby Azure Media Analytics](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

