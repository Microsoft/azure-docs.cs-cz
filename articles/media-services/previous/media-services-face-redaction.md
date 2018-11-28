---
title: Zákona o svobodném přístupu pomocí Azure Media Analytics tváří | Dokumentace Microsoftu
description: Toto téma ukazuje, jak pomocí Azure media analytics tváří zákona o svobodném přístupu.
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
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "33789380"
---
# <a name="redact-faces-with-azure-media-analytics"></a>Zákona o svobodném přístupu tváří pomocí Azure Media Analytics
## <a name="overview"></a>Přehled
**Azure Media Redactor** je [Azure Media Analytics](media-services-analytics-overview.md) procesor médií (PP), která nabízí škálovatelné face redactoru v cloudu. Rozmazání obličejů umožňuje upravovat vaše video, aby bylo možné rozostření tváří vybraných jedinců. Můžete chtít použít službu rozmazání tváří v veřejného scénáře a sdělovací. Pár minut záběrů, který obsahuje více ploch může trvat hodiny zákona o svobodném přístupu ručně, ale s touto službou procesu rozmazání tváří vyžaduje jenom pár jednoduchých kroků. Další informace najdete v tématu [to](https://azure.microsoft.com/blog/azure-media-redactor/) blogu.

Tento článek obsahuje podrobnosti o **Azure Media Redactor** a ukazuje, jak používat ve službě Media Services SDK pro .NET.

## <a name="face-redaction-modes"></a>Režimy rozmazání tváří
Rozmazání tváří funguje tak, že rozpoznávání tváří v každém snímku video a ke sledování objektu pro rozpoznávání tváře obou vpřed a zpět v čase, takže stejnou jednotlivých můžete rozmazávají z jiných úhly také. Proces automatické redigování je složitá a nemá vždy vytvoří 100 % požadovaného výstupu z tohoto důvodu Media Analytics nabízí několik způsobů, jak upravit závěrečný výstup.

Kromě plně automatického režimu je dvoufázovou pracovní postup, který umožňuje výběr nebo rušení-selection nalezen ploch přes seznam identifikátorů. Také aby libovolného za úpravy snímků sadu Management Pack používá soubor metadat ve formátu JSON. Tento pracovní postup se dělí do **analyzovat** a **Redact** režimy. Můžete kombinovat dva režimy v jediném kroku, na kterém běží oba úkoly v úloh. Tento režim se nazývá **kombinované**.

### <a name="combined-mode"></a>Kombinovaný režim
Tímto se vytvoří zrevidovaně mp4 automaticky bez jakékoli manuální vstup.

| Krok | Název souboru | Poznámky |
| --- | --- | --- |
| Vstupní asset |foo.bar |Video ve formátu WMV, MOV nebo MP4 |
| Konfigurace vstupu |Konfigurace úlohy předvolby |{'version':'1.0 ', 'možnosti': {"režimu": "kombinovanou"}} |
| Výstupního prostředku |foo_redacted.mp4 |Video s rozostření použít |

#### <a name="input-example"></a>Příklad vstupu:
[Zobrazit toto video](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fed99001d-72ee-4f91-9fc0-cd530d0adbbc%2FDancing.mp4)

#### <a name="output-example"></a>Příklad výstupu:
[Zobrazit toto video](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc6608001-e5da-429b-9ec8-d69d8f3bfc79%2Fdance_redacted.mp4)

### <a name="analyze-mode"></a>Analýza režimu
**Analyzovat** pass dvoufázovou pracovního postupu přebírá video vstup a vytvoří soubor JSON pro rozpoznávání tváře umístění a obrázky ve formátu jpg jednotlivých zjistila pro rozpoznávání tváře.

| Krok | Název souboru | Poznámky |
| --- | --- | --- |
| Vstupní asset |foo.bar |Video ve formátu WMV, MPV nebo MP4 |
| Konfigurace vstupu |Konfigurace úlohy předvolby |{'version':'1.0 ', 'možnosti': {"režimu": "analyzovat"}} |
| Výstupního prostředku |foo_annotations.json |Poznámka data z umístění pro rozpoznávání tváře ve formátu JSON. To může upravit uživateli změnit rozostření ohraničující polí. Najdete v ukázce níže. |
| Výstupního prostředku |foo_thumb%06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |Oříznutý jpg jednotlivých zjistil rozpoznávání tváře, kde číslo označuje labelId typ písma |

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

### <a name="redact-mode"></a>Režim zákona o svobodném přístupu
Překontrolovat pracovního postupu přebírá větší počet vstupů, které se musí zkombinovat do jednoho datového zdroje.

Jedná se o seznam ID rozostření původního videa a poznámky JSON. Tento režim se pomocí poznámky použije rozostření na vstupním videu.

Výstup z průchodu analyzovat nezahrnuje původního videa. Video musí nahráli do vstupní asset úlohy Redact režimu a vybrali jako primární soubor.

| Krok | Název souboru | Poznámky |
| --- | --- | --- |
| Vstupní asset |foo.bar |Video ve formátu WMV, MPV nebo MP4. Stejné jako v kroku 1 videa. |
| Vstupní asset |foo_annotations.json |Soubor metadat poznámky z fáze, volitelné změny. |
| Vstupní asset |foo_IDList.txt (volitelné) |Volitelné nový řádek oddělený seznam ID zákona o svobodném přístupu pro rozpoznávání tváře. Pokud je ponecháno prázdné, tato všechny rozmazává obličeje ve. |
| Konfigurace vstupu |Konfigurace úlohy předvolby |{'version':'1.0 ', 'možnosti': {"režimu": "zákona o svobodném přístupu"}} |
| Výstupního prostředku |foo_redacted.mp4 |Video s rozostření použít podle poznámky |

#### <a name="example-output"></a>Příklad výstupu
Toto je výstup z IDList s jedno ID vybrané.

[Zobrazit toto video](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fad6e24a2-4f9c-46ee-9fa7-bf05e20d19ac%2Fdance_redacted1.mp4)

Příklad foo_IDList.txt
 
     1
     2
     3

## <a name="blur-types"></a>Rozostření typy

V **kombinované** nebo **Redact** režimu 5 různých rozostření režimech, můžete si vybrat z prostřednictvím konfigurace vstupu JSON: **nízká**, **Med**, **Vysokou**, **pole**, a **černé**. Ve výchozím nastavení **Med** se používá.

Můžete najít ukázky níže rozostření typů.

### <a name="example-json"></a>Ukázkový soubor JSON:

```json
    {'version':'1.0', 'options': {'Mode': 'Combined', 'BlurType': 'High'}}
```

#### <a name="low"></a>Nízká

![Nízká](./media/media-services-face-redaction/blur1.png)
 
#### <a name="med"></a>Reproduktoru

![Reproduktoru](./media/media-services-face-redaction/blur2.png)

#### <a name="high"></a>Vysoký

![Vysoký](./media/media-services-face-redaction/blur3.png)

#### <a name="box"></a>Box

![Box](./media/media-services-face-redaction/blur4.png)

#### <a name="black"></a>Black

![Black](./media/media-services-face-redaction/blur5.png)

## <a name="elements-of-the-output-json-file"></a>Prvky výstupního souboru JSON

Rozmazání sadu Management Pack poskytuje velmi přesné rozpoznávání umístění a sledování, která dokáže detekovat až 64 lidských tváří v rámci video. Přední tváří poskytují nejlepší výsledky, při straně tváří a malé tváří (menší než nebo roven 24 × 24 pixelů) jsou náročné.

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

## <a name="net-sample-code"></a>Vzorový kód .NET

Následující program ukazuje postup:

1. Vytvoření assetu a uložit do assetu soubor média.
2. Vytvořte úlohu s úkolem redigování pro rozpoznávání tváře založené na konfigurační soubor, který obsahuje následující přednastavení json: 

    ```json
            {
                'version':'1.0',
                'options': {
                    'mode':'combined'
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

[Ukázky Azure Media Analytics](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

