---
title: Pokročilé kódování pomocí Media Encoderu Premium Workflow | Dokumentace Microsoftu
description: Informace o kódování pomocí Media Encoderu Premium Workflow. Ukázky kódu jsou napsané C# a používat Media Services SDK pro .NET.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: juliako
ms.openlocfilehash: b718e2a3431be451c1c62079f92c18156f817a10
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50246241"
---
# <a name="advanced-encoding-with-media-encoder-premium-workflow"></a>Pokročilé kódování pomocí Media Encoderu Premium Workflow
> [!NOTE]
> Pracovní postup kodéru Media Encoder Premium mediálním procesorem popisovaných v tomto článku není k dispozici v Číně.
>
>

Dotazy kodér úrovně premium, e-mailu mepd@microsoft.com.

## <a name="overview"></a>Přehled
Představujeme Microsoft Azure Media Services **pracovní postup kodéru Media Encoder Premium** procesor médií. Tato záloha nabídky procesor kódování funkce pro pracovní postupy podle potřeby premium.

Následující témata popisují podrobností týkajících se **pracovní postup kodéru Media Encoder Premium**:

* [Formáty podporované serverem Media Encoderu Premium Workflow](media-services-premium-workflow-encoder-formats.md) – popisuje soubor formáty a kodeky podporuje **pracovní postup kodéru Media Encoder Premium**.
* [Přehled a porovnání kodérů médií na vyžádání Azure](media-services-encode-asset.md) porovnává možnosti kódování **pracovní postup kodéru Media Encoder Premium** a **kodéru Media Encoder Standard**.

Tento článek ukazuje, jak kódovat s **pracovní postup kodéru Media Encoder Premium** pomocí rozhraní .NET.

Úlohy pro kódování **pracovní postup kodéru Media Encoder Premium** vyžadují samostatného konfiguračního souboru, názvem souboru pracovního postupu. Tyto soubory mají příponu .workflow a jsou vytvářeny pomocí [návrháře postupu provádění](media-services-workflow-designer.md) nástroj.

Můžete také získat výchozí soubory pracovního postupu [tady](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). Složka také obsahuje popis těchto souborů.

Soubory pracovního postupu je třeba nahrát do účtu Media Services jako prostředek a tohoto prostředku by měly být předány v úlohu kódování.

## <a name="create-and-configure-a-visual-studio-project"></a>Vytvoření a konfigurace projektu Visual Studia

Nastavte své vývojové prostředí a v souboru app.config vyplňte informace o připojení, jak je popsáno v tématu [Vývoj pro Media Services v .NET](media-services-dotnet-how-to-use.md). 

## <a name="encoding-example"></a>Příklad kódování

Následující příklad ukazuje, jak kódovat s **pracovní postup kodéru Media Encoder Premium**.

Jsou prováděny následovně:

1. Vytvořte asset a nahrajte soubor pracovního postupu.
2. Vytvořte asset a nahrajte soubor zdrojového média.
3. Získejte procesor médií "Pracovní postup kodéru Media Encoder Premium".
4. Vytvoření úlohy a úlohy.

    Ve většině případů je prázdný řetězec konfigurace pro úlohu (jako v následujícím příkladu). Existují některé pokročilé scénáře (které vyžadují dynamické nastavení vlastnosti modulu runtime) v takovém případě je zadat jako řetězec XML pro úlohu kódování. Mezi tyto scénáře patří: vytváření překrytí, paralelní a sekvenční médií spojování, titulkování.
5. Přidáte dva vstupní datové zdroje do úlohy.

    1. 1. dne – asset pracovního postupu.
    2. 2 – asset videa.

    >[!NOTE]
    >Asset pracovního postupu musí přidat do úlohy před asset média.
   Konfigurační řetězec pro tuto úlohu by měla být prázdná.
   
6. Odešlete kódovací úlohu.

```csharp
using System;
using System.Linq;
using System.Configuration;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.WindowsAzure.MediaServices.Client;

namespace MediaEncoderPremiumWorkflowSample
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

        private static readonly string _supportFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _workflowFilePath =
            Path.GetFullPath(_supportFiles + @"\H264 Progressive Download MP4.workflow");

        private static readonly string _singleMP4InputFilePath =
            Path.GetFullPath(_supportFiles + @"\BigBuckBunny.mp4");

        static void Main(string[] args)
        {

            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            var workflowAsset = CreateAssetAndUploadSingleFile(_workflowFilePath);
            var videoAsset = CreateAssetAndUploadSingleFile(_singleMP4InputFilePath);
            IAsset outputAsset = CreateEncodingJob(workflowAsset, videoAsset);

        }

        static public IAsset CreateAssetAndUploadSingleFile(string singleFilePath)
        {
            var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
            var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);

            var fileName = Path.GetFileName(singleFilePath);

            var assetFile = asset.AssetFiles.Create(fileName);

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return asset;
        }

        static public IAsset CreateEncodingJob(IAsset workflow, IAsset video)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Premium Workflow encoding job");
            // Get a media processor reference, and pass to it the name of the
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Premium Workflow");

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("Premium Workflow encoding task",
                processor,
                "",
                TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(workflow);
            task.InputAssets.Add(video); // we add one asset
                                         // Add an output asset to contain the results of the job.
                                         // This output is specified as AssetCreationOptions.None, which
                                         // means the output asset is not encrypted.
            task.OutputAssets.AddNew("Output asset",
                AssetCreationOptions.None);

            // Use the following event handler to check job progress.  
            job.StateChanged += new
                    EventHandler<JobStateChangedEventArgs>(StateChanged);

            // Launch the job.
            job.Submit();

            // Check job execution and wait for job to finish.
            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
            progressJobTask.Wait();

            // If job state is Error the event handling
            // method for job progress should log errors.  Here we check
            // for error state and exit if needed.
            if (job.State == JobState.Error)
            {
                throw new Exception("\nExiting method due to job error.");
            }

            return job.OutputMediaAssets[0];
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
        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

            return processor;
        }
    }
}
```

Dotazy kodér úrovně premium, e-mailu mepd@microsoft.com.

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
