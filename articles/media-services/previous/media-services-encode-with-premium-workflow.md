---
title: Rozšířené kódování pomocí Media Encoder Premium Workflow | Microsoft Docs
description: Naučte se kódovat pomocí Media Encoder Premium Workflow. Ukázky kódu jsou napsané v jazyce C# a používají sadu Media Services SDK pro .NET.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 8d39d54498872201afe2f705da1fc5654d8befb2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105645001"
---
# <a name="advanced-encoding-with-media-encoder-premium-workflow"></a>Pokročilé kódování pomocí Media Encoderu Premium Workflow

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Procesor Media Encoder Premium Workflow médií popisovaný v tomto článku není v Číně k dispozici.
>
>

## <a name="overview"></a>Přehled
Microsoft Azure Media Services zavádí procesor **Media Encoder Premium Workflow** Media. Tento procesor nabízí pro pracovní postupy Premium na vyžádání možnosti kódování předem.

Následující témata popisují podrobnosti související s **Media Encoder Premium Workflow**:

* [Formáty podporované Media Encoder Premium Workflow](./media-services-encode-asset.md) – popisuje formáty souborů a kodeky, které **Media Encoder Premium Workflow** podporuje.
* [Přehled a porovnání kodérů médií na vyžádání v Azure](media-services-encode-asset.md) porovnává funkce kódování **Media Encoder Premium Workflow** a **Media Encoder Standard**.

Tento článek ukazuje, jak se zakódovat pomocí **Media Encoder Premium Workflow** pomocí rozhraní .NET.

Úlohy kódování pro **Media Encoder Premium Workflow** vyžadují samostatný konfigurační soubor, který se označuje jako soubor pracovního postupu. Tyto soubory mají příponu. Workflow a jsou vytvořeny pomocí nástroje [Návrhář postupu provádění](media-services-workflow-designer.md) .

Do [tohoto](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/media-services/previous/media-services-encode-with-premium-workflow.md
)pole můžete také získat výchozí soubory pracovního postupu. Složka obsahuje také popis těchto souborů.

Soubory pracovního postupu je třeba odeslat do účtu Media Services jako Asset a tento prostředek by měl být předán do úlohy kódování.

## <a name="create-and-configure-a-visual-studio-project"></a>Vytvoření a konfigurace projektu Visual Studia

Nastavte své vývojové prostředí a v souboru app.config vyplňte informace o připojení, jak je popsáno v tématu [Vývoj pro Media Services v .NET](media-services-dotnet-how-to-use.md). 

## <a name="encoding-example"></a>Příklad kódování

Následující příklad ukazuje, jak kódovat pomocí **Media Encoder Premium Workflow**.

Provedou se následující kroky:

1. Vytvořte Asset a nahrajte soubor pracovního postupu.
2. Vytvořte Asset a nahrajte zdrojový mediální soubor.
3. Získejte multimediální procesor "Media Encoder Premium Workflow".
4. Vytvořte úlohu a úlohu.

    Ve většině případů je konfigurační řetězec pro úlohu prázdný (jako v následujícím příkladu). Existují některé pokročilé scénáře (které vyžadují, abyste nastavili dynamické vlastnosti za běhu). v takovém případě byste k úloze kódování zadali řetězec XML. Příklady takových scénářů: Vytvoření překrytí, paralelního nebo sekvenčního spojování médií, Subtitling.
5. Přidejte do úkolu dva vstupní prostředky.

   1. 1st – prostředek pracovního postupu
   2. 2. video – Asset.

      >[!NOTE]
      >Prostředek pracovního postupu musí být přidán do úlohy před Assetem média.
      Konfigurační řetězec pro tento úkol by měl být prázdný.
   
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

Lístek podpory můžete otevřít tak, že přejdete na [novou žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) .

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
