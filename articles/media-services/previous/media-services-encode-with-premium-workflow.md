---
title: Pokročilé kódování s prémiovým pracovním postupem kodéru médií | Dokumenty společnosti Microsoft
description: Přečtěte si, jak kódovat pomocí pracovního postupu kodéru médií Premium. Ukázky kódu jsou zapsány v c# a používají sady Media Services SDK pro rozhraní .NET.
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
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: ca5de657ad45f53cff0cb01d5fe9cc412baf4533
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792306"
---
# <a name="advanced-encoding-with-media-encoder-premium-workflow"></a>Pokročilé kódování pomocí Media Encoderu Premium Workflow
> [!NOTE]
> Mediální procesor Media Encoder Premium Workflow popsaný v tomto článku není v Číně k dispozici.
>
>

## <a name="overview"></a>Přehled
Mediální služba Microsoft Azure představuje mediální procesor **Media Encoder Premium Workflow.** Tento procesor nabízí pokročilé možnosti kódování pro vaše prémiové pracovní postupy na vyžádání.

Následující témata popisují podrobnosti týkající se pracovního postupu programu **Media Encoder Premium**:

* [Formáty podporované pracovním postupem Premium kodéru médií](media-services-premium-workflow-encoder-formats.md) – popisuje formáty souborů a kodeky podporované **pracovním postupem Media Encoder Premium**.
* [Přehled a porovnání kodéry médií Azure na vyžádání](media-services-encode-asset.md) porovnává možnosti kódování **pracovního postupu Media Encoder Premium** a Standard pro kódování **médií**.

Tento článek ukazuje, jak kódovat s **Media Encoder Premium Workflow** pomocí rozhraní .NET.

Úlohy kódování pro **pracovní postup kodéru kodéru médií vyžadují** samostatný konfigurační soubor nazývaný soubor pracovního postupu. Tyto soubory mají příponu .workflow a jsou vytvořeny pomocí nástroje [Návrhář pracovního postupu.](media-services-workflow-designer.md)

Zde můžete také získat výchozí [soubory](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)pracovního postupu . Složka také obsahuje popis těchto souborů.

Soubory pracovního postupu je třeba odeslat do účtu Mediální služby jako datový zdroj a tento datový zdroj by měl být předán do úlohy kódování.

## <a name="create-and-configure-a-visual-studio-project"></a>Vytvoření a konfigurace projektu Visual Studia

Nastavte své vývojové prostředí a v souboru app.config vyplňte informace o připojení, jak je popsáno v tématu [Vývoj pro Media Services v .NET](media-services-dotnet-how-to-use.md). 

## <a name="encoding-example"></a>Příklad kódování

Následující příklad ukazuje, jak kódovat s **Media Encoder Premium Workflow**.

Jsou prováděny následující kroky:

1. Vytvořte datový zdroj a nahrajte soubor pracovního postupu.
2. Vytvořte datový zdroj a nahrajte zdrojový mediální soubor.
3. Získejte mediální procesor "Media Encoder Premium Workflow".
4. Vytvořte úlohu a úkol.

    Ve většině případů je konfigurační řetězec pro úlohu prázdný (jako v následujícím příkladu). Existují některé pokročilé scénáře (které vyžadují dynamické nastavení vlastností za běhu), v takovém případě byste pro úlohu kódování poskytli řetězec XML. Příklady takových scénářů jsou: vytvoření překrytí, paralelní nebo sekvenční prošití médií, titulkování.
5. Přidejte do úkolu dva vstupní datové zdroje.

   1. 1. – datový zdroj pracovního postupu.
   2. 2. – video aktiv.

      >[!NOTE]
      >Datový zdroj pracovního postupu musí být přidán k úloze před mediální množinou.
      Konfigurační řetězec pro tuto úlohu by měl být prázdný.
   
6. Odešlete úlohu kódování.

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

## <a name="need-help"></a>Potřebujete pomoc?

Lístek podpory můžete otevřít tak, že přejdete na [Novou žádost o podporu.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
