---
title: Kódování prostředku pomocí kodéru Media Encoder Standard pomocí rozhraní .NET | Dokumentace Microsoftu
description: Tento článek ukazuje, jak pomocí .NET určený ke kódování prostředku pomocí Media Encoderu standardní.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 03431b64-5518-478a-a1c2-1de345999274
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: juliako;anilmur
ms.openlocfilehash: 426ebb90a0d7716b14664f874db259a0cca2aa7e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51241085"
---
# <a name="encode-an-asset-with-media-encoder-standard-using-net"></a>Kódování prostředku pomocí kodéru Media Encoder Standard pomocí rozhraní .NET 
Kódování úloh je jednou z nejběžnějších operací zpracování ve službě Media Services. K převodu mediálních souborů z jednoho kódování do druhého se využívají kódovací úlohy. Při kódování, můžete použít předdefinované Media Encoder Media Services. Můžete také použít kodér poskytovanými partnerem Media Services; kodérů třetích stran jsou k dispozici prostřednictvím Azure Marketplace. 

Tento článek ukazuje, jak pomocí .NET určený ke kódování vašich prostředků pomocí Media Encoder Standard (MES). Media Encoder Standard je nakonfigurovaný pomocí jedné z přednastavení kodérů popsané [tady](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

Doporučuje se vždy kódování zdrojové soubory do sady MP4 adaptivní přenosové rychlosti a převeďte sady na požadovaný formát pomocí [dynamické balení](media-services-dynamic-packaging-overview.md). 

Pokud výstupní asset používá šifrování úložiště, musíte nakonfigurovat zásady doručení assetu. Další informace najdete v tématu [konfigurace zásad doručení assetu](media-services-dotnet-configure-asset-delivery-policy.md).

> [!NOTE]
> MES vytvoří výstupní soubor s názvem, který obsahuje prvních 32 znaků z názvu vstupního souboru. Název je podle zadaných v souboru předvolby. Například "název_souboru": "{Basename} _ {Index} {Extension}". {Basename} se nahradí prvních 32 znaků název vstupního souboru.
> 
> 

### <a name="mes-formats"></a>Formáty MES
[Formáty a kodeky](media-services-media-encoder-standard-formats.md)

### <a name="mes-presets"></a>Přednastavení MES
Media Encoder Standard je nakonfigurovaný pomocí jedné z přednastavení kodérů popsané [tady](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

### <a name="input-and-output-metadata"></a>Vstupní a výstupní metadata
Při kódování vstupní prostředek (nebo prostředky) pomocí MES získáte výstupního prostředku na úspěšné dokončení, která kódování úloh. Výstupní asset obsahuje video, zvuk, miniatury, manifest a podobně podle použitá předvolba kódování, které používáte.

Výstupní asset obsahuje také soubor s metadaty o vstupní asset. Název souboru XML metadat má následující formát: < asset_id > _metadata.xml (například d 57 8 41114ad3-eb5e - 4c 92-5354e2b7d4a4_metadata.xml), kde < asset_id > je hodnotou AssetId vstupní asset. Schéma této vstupní kód XML metadat je popsán [tady](media-services-input-metadata-schema.md).

Výstupní asset obsahuje také soubor s metadaty o výstupního prostředku. Název souboru XML metadat má následující formát: < source_file_name > _manifest.xml (například BigBuckBunny_manifest.xml). Schéma tato metadata výstup XML je popsána [tady](media-services-output-metadata-schema.md).

Pokud chcete prozkoumat buď dva soubory metadat, můžete vytvořit lokátor SAS a stáhněte si soubor do místního počítače. Příklad najdete na tom, jak vytvořit lokátor SAS a stáhnout soubor přes rozšíření Media Services .NET SDK.

## <a name="download-sample"></a>Stažení ukázky
Můžete získat a spuštění ukázky, která ukazuje, jak kódování pomocí MES z [tady](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="net-sample-code"></a>Vzorový kód .NET

Následující příklad kódu používá sadu Media Services .NET SDK k provádění následujících úloh:

* Vytvořte úlohu kódování.
* Získání odkazu na kodéru Media Encoder Standard.
* Nastavení, aby používal [adaptivní streamování](media-services-autogen-bitrate-ladder-with-mes.md) předvolby. 
* Přidáte jednoho kódování úkolu do úlohy. 
* Zadejte vstupní asset kódovaný.
* Vytvoření výstupní asset, který obsahuje zakódovanému assetu.
* Přidáte obslužnou rutinu události chcete zkontrolovat průběh úlohy.
* Odeslání úlohy.

#### <a name="create-and-configure-a-visual-studio-project"></a>Vytvoření a konfigurace projektu Visual Studia

Nastavte své vývojové prostředí a v souboru app.config vyplňte informace o připojení, jak je popsáno v tématu [Vývoj pro Media Services v .NET](media-services-dotnet-how-to-use.md). 

#### <a name="example"></a>Příklad: 

```csharp
using System;
using System.Linq;
using System.Configuration;
using System.IO;
using System.Threading;
using Microsoft.WindowsAzure.MediaServices.Client;

namespace MediaEncoderStandardSample
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

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);


            // Get an uploaded asset.
            var asset = _context.Assets.FirstOrDefault();

            // Encode and generate the output using the "Adaptive Streaming" preset.
            EncodeToAdaptiveBitrateMP4Set(asset);

            Console.ReadLine();
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Create a task with the encoding details, using a string preset.
            // In this case "Adaptive Streaming" preset is used.
            ITask task = job.Tasks.AddNew("My encoding task",
                processor,
                "Adaptive Streaming",
                TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset",
                AssetCreationOptions.None);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }

        private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);
            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
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

## <a name="advanced-encoding-features-to-explore"></a>Pokročilé funkce Encoding prozkoumat
* [Postup generování miniatur](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Generování miniatur při kódování](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Oříznutí videa při kódování](media-services-crop-video.md)
* [Přizpůsobení kódovací předvolby](media-services-custom-mes-presets-with-dotnet.md)
* [Překryv nebo vodoznak videa s obrázkem](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Další postup
[Postup generování miniatur pomocí kodéru Media Encoder Standard s využitím .NET](media-services-dotnet-generate-thumbnail-with-mes.md)
[kódování Přehled služby Media Services](media-services-encode-asset.md)

