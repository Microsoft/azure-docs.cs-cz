---
title: Redigování plošek s Azure Media Analytics | Microsoft Docs
description: Azure Media Redactor je Azure Media Analytics multimediální procesor, který nabízí škálovatelné redigování tváře v cloudu. Tento článek ukazuje, jak pomocí Azure Media Analytics naredigování obličeje.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 7d416810f6a39fb36bfa3c5225301fe87fdd128c
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103013461"
---
# <a name="redact-faces-with-azure-media-analytics"></a>Redigování ploch pomocí Azure Media Analytics

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Přehled

**Azure Media redactor** je [Azure Media Analytics](./legacy-components.md) multimediální procesor (MP), který nabízí škálovatelné redigování tváře v cloudu. Naredigování obličeje umožňuje upravit vaše video, aby se rozostří plošky vybraných jednotlivců. Je možné, že budete chtít použít službu redigování obličeje ve scénářích veřejného zabezpečení a média pro příspěvky. Několik minut záběrů, které obsahují více plošek, může trvat hodiny na redigování ručně, ale v rámci této služby bude proces redigování obličeje vyžadovat jenom několik jednoduchých kroků.

Tento článek obsahuje podrobné informace o **Azure Media redactor** a ukazuje, jak ho používat s Media Services SDK pro .NET.

## <a name="face-redaction-modes"></a>Režimy redigování obličeje

Redigování obličeje funguje tak, že detekuje obličeje v každém snímku videa a sleduje objekt Face v čase dopředu a dozadu, aby se stejná osoba mohla rozmazaný i z jiných úhlů. Automatizovaný proces redigování je složitý a nikdy nevytváří 100% požadovaného výstupu, z tohoto důvodu Media Analytics poskytuje několik způsobů, jak změnit konečný výstup.

Kromě plného automatického režimu je k dispozici pracovní postup se dvěma průchody, který umožňuje výběr a zrušení výběru nalezených obličeje pomocí seznamu ID. K provedení libovolných úprav v rámci jednotlivých snímků sada MP používá soubor metadat ve formátu JSON. Tento pracovní postup je rozdělen na režimy **analyzování** a **redigování** . Oba režimy můžete zkombinovat v jednom průchodu, který spouští obě úlohy v jedné úloze. Tento režim se nazývá **Kombinovaná**.

   > [!NOTE]
   > Procesor s médii pro rozpoznávání tváře je zastaralý od června 2020 [Azure Media Services starších součástí](./legacy-components.md). Zvažte použití rozhraní Azure Media Services V3 API.

### <a name="combined-mode"></a>Kombinovaný režim

Tím se automaticky vytvoří objekt MP4 (MP4) bez ručního vstupu.

| Fáze | Název souboru | Poznámky |
| --- | --- | --- |
| Vstupní Asset |foo. bar |Video ve formátu WMV, MOV nebo MP4 |
| Vstup konfigurace |Přednastavení konfigurace úlohy |{' Version ': ' 1.0 ', ' Options ': {' Mode ': ' kombinované '}} |
| Výstupní Asset |foo_redacted.mp4 |Video s použitím rozostření |

### <a name="analyze-mode"></a>Režim analýzy

Úspěšnost **analýzy** obousměrného pracovního postupu provede vstup videa a vytvoří soubor JSON pro umístění obličeje a obrázky ve formátu jpg každé zjištěné plochy.

| Fáze | Název souboru | Poznámky |
| --- | --- | --- |
| Vstupní Asset |foo. bar |Video ve formátu WMV, MPV nebo MP4 |
| Vstup konfigurace |Přednastavení konfigurace úlohy |{' Version ': ' 1.0 ', ' Options ': {' Mode ': ' Analyze '}} |
| Výstupní Asset |foo_annotations.jsna |Data poznámek pro umístění obličeje ve formátu JSON. Tuto možnost může upravit uživatel, aby bylo možné upravovat rozmazaný ohraničovací rámečky. Viz ukázka níže. |
| Výstupní Asset |foo_thumb% 06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |Oříznutý jpg každé zjištěné plochy, kde číslo označuje labelId obličej |

#### <a name="output-example"></a>Příklad výstupu

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

    ... truncated
```

### <a name="redact-mode"></a>Režim redigování

Druhý průchod pracovního postupu přebírá větší počet vstupů, které musí být sloučeny do jediného assetu.

To zahrnuje seznam ID, která se mají Rozostřit, původní video a poznámky JSON. Tento režim používá poznámky pro použití rozostření na vstupním videu.

Výstup z průchodu Analyze nezahrnuje původní video. Video se musí nahrát do vstupního assetu pro úlohu režimu redigování a vybrané jako primární soubor.

| Fáze | Název souboru | Poznámky |
| --- | --- | --- |
| Vstupní Asset |foo. bar |Video ve formátu WMV, MPV nebo MP4. Stejné video jako v kroku 1. |
| Vstupní Asset |foo_annotations.jsna |poznámky soubor metadat z fáze One, s volitelnými úpravami. |
| Vstupní Asset |foo_IDList.txt (volitelné) |Nepovinný nový řádek oddělený seznam ID tváře k redigování Pokud pole necháte prázdné, rozostří všechny plošky. |
| Vstup konfigurace |Přednastavení konfigurace úlohy |{' Version ': ' 1.0 ', ' Options ': {' Mode ': ' redigování}} |
| Výstupní Asset |foo_redacted.mp4 |Video s rozostřením aplikovaným na základě poznámek |

#### <a name="example-output"></a>Příklad výstupu

Toto je výstup z IDList s jedním vybraným ID.

Příklad foo_IDList.txt

```
1
2
3
```

## <a name="blur-types"></a>Typy rozostření

V **kombinovaném** , nebo v režimu **redigování** , existuje 5 různých režimů rozostření, ze kterých můžete vybírat prostřednictvím vstupní konfigurace JSON: **Nízká**, **med**, **High**, **box** a **Black**. Ve výchozím nastavení se používá **med** .

Můžete najít ukázky níže uvedených typů rozostření.

### <a name="example-json"></a>Ukázkový kód JSON

```json
{
    'version':'1.0',
    'options': {
        'Mode': 'Combined',
        'BlurType': 'High'
    }
}
```

#### <a name="low"></a>Nízká

![Nízká](./media/media-services-face-redaction/blur1.png)

#### <a name="med"></a>Tahač

![Tahač](./media/media-services-face-redaction/blur2.png)

#### <a name="high"></a>Vysoká

![Vysoká](./media/media-services-face-redaction/blur3.png)

#### <a name="box"></a>Box

![Box](./media/media-services-face-redaction/blur4.png)

#### <a name="black"></a>Black

![Black](./media/media-services-face-redaction/blur5.png)

## <a name="elements-of-the-output-json-file"></a>Prvky výstupního souboru JSON

MP pro redigování poskytuje detekci a sledování polohy s vysokou přesností, které dokáže detekovat až 64 lidských plošek ve snímku videa. Přední plošky poskytují nejlepší výsledky, zatímco strany a malé plošky (menší nebo rovny 24x24 pixelů) jsou náročné.

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

## <a name="net-sample-code"></a>Vzorový kód .NET

Následující program ukazuje, jak:

1. Vytvořte Asset a nahrajte do něj mediální soubor.
2. Vytvořte úlohu s úkolem redigování obličeje na základě konfiguračního souboru, který obsahuje následující předvolbu JSON: 

    ```json
            {
                'version':'1.0',
                'options': {
                    'mode':'combined'
                }
            }
    ```

3. Stáhněte si výstupní soubory JSON.

### <a name="create-and-configure-a-visual-studio-project"></a>Vytvoření a konfigurace projektu Visual Studia

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

[Přehled analýzy Azure Media Services](./legacy-components.md)

[Ukázky Azure Media Analytics](http://amslabs.azurewebsites.net/demos/Analytics.html)
