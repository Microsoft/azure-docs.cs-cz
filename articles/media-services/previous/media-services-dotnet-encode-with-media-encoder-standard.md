---
title: Kódování assetu pomocí kodéru Media Encoder Standard pomocí rozhraní .NET | Microsoft Docs
description: Tento článek ukazuje, jak pomocí rozhraní .NET kódování assetu pomocí kodéru standardní média.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 03431b64-5518-478a-a1c2-1de345999274
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2017
ms.author: juliako;anilmur
ms.openlocfilehash: 2964be45c98350fb1f3c82f25716943493979240
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="encode-an-asset-with-media-encoder-standard-using-net"></a>Kódování assetu pomocí kodéru Media Encoder Standard pomocí rozhraní .NET
Kódování úloh je jednou z nejběžnějších operací zpracování ve službě Media Services. K převodu mediálních souborů z jednoho kódování do druhého se využívají kódovací úlohy. Při kódování, můžete použít předdefinované Media Encoder Media Services. Můžete také použít kodér poskytovanými partnerem Media Services; třetí strany kodéry jsou k dispozici prostřednictvím Azure Marketplace. 

Tento článek ukazuje, jak používat .NET určený ke kódování vaše prostředky s Media Encoder Standard (MES). Media Encoder Standard je konfigurován pomocí jedno z přednastavení kodéry popsané [zde](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

Doporučuje se vždycky zakódovat zdrojové soubory do sady souborů MP4 adaptivní přenosovou rychlostí a pak sadu převést na požadovaný formát pomocí [dynamické balení](media-services-dynamic-packaging-overview.md). 

Pokud výstupní asset používá šifrování úložiště, musíte nakonfigurovat zásady doručení assetu. Další informace najdete v tématu [konfigurace zásad doručení assetu](media-services-dotnet-configure-asset-delivery-policy.md).

> [!NOTE]
> MES vytvoří výstupní soubor s názvem, který obsahuje první 32 znaků z názvu vstupní soubor. Název podle zadaných v přednastavené souboru. Například "název souboru": "{Basename} _ {Index} {rozšíření}". {Basename} je nahrazena nejprve 32 znaků z názvu vstupní soubor.
> 
> 

### <a name="mes-formats"></a>Formáty MES
[Formáty a kodeky](media-services-media-encoder-standard-formats.md)

### <a name="mes-presets"></a>Přednastavení MES
Media Encoder Standard je konfigurován pomocí jedno z přednastavení kodéry popsané [zde](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

### <a name="input-and-output-metadata"></a>Vstup a výstup metadat
Při kódování prostředek vstupní (nebo prostředky) pomocí MES získat prostředek výstup na úspěšné dokončení této kódování úloh. Výstupní asset obsahuje video, zvuk, miniatur, manifest atd., které můžete použít přednastavení kódování.

Výstupní asset obsahuje také soubor s metadata o vstupní asset. Název souboru XML metadat má následující formát: < asset_id > _metadata.xml (například 41114ad3-eb5e - 4c 57 8d 92-5354e2b7d4a4_metadata.xml), kde < asset_id > je hodnota ID vstupní prostředku. Schéma tato metadata vstupní XML je popsána [zde](media-services-input-metadata-schema.md).

Výstupní asset obsahuje také soubor s metadata o výstupní asset. Název souboru XML metadat má následující formát: < source_file_name > _manifest.xml (například BigBuckBunny_manifest.xml). Schéma tato metadata výstup XML je popsána [zde](media-services-output-metadata-schema.md).

Pokud chcete prozkoumat buď dva soubory metadat, můžete vytvořit lokátor SAS a stáhněte soubor do místního počítače. Příklad najdete na tom, jak vytvořit lokátor SAS a stáhnout soubor pomocí rozšíření Media Services .NET SDK.

## <a name="download-sample"></a>Stažení ukázky
Můžete získat a spustit ukázku, která ukazuje, jak ke kódování s MES z [zde](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="net-sample-code"></a>Ukázkový kód rozhraní .NET

Následující příklad kódu používá sadu Media Services .NET SDK k provádění následujících úloh:

* Vytvořte úlohu kódování.
* Získáte odkaz na kodéru Media Encoder Standard.
* Použít [adaptivní datové proudy](media-services-autogen-bitrate-ladder-with-mes.md) přednastavené. 
* Přidáte jednoho kódování úkolu do úlohy. 
* Zadejte vstupní asset, který je zakódován.
* Vytvoření výstupní asset, který obsahuje k zakódovanému assetu.
* Přidání obslužné rutiny události zkontrolovat průběh úlohy.
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

## <a name="advanced-encoding-features-to-explore"></a>Pokročilé funkce chcete prozkoumat kódování
* [Postup vytváření miniatur](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Vytváření miniatur během kódování](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Oříznutí videa během kódování](media-services-crop-video.md)
* [Přizpůsobení přednastavení kódování](media-services-custom-mes-presets-with-dotnet.md)
* [Překrytí nebo vodoznaku video s bitovou kopií](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Další postup
[Jak vygenerovat miniaturu pomocí kodéru Media Encoder Standard s .NET](media-services-dotnet-generate-thumbnail-with-mes.md)
[kódování Přehled služby Media Services](media-services-encode-asset.md)

