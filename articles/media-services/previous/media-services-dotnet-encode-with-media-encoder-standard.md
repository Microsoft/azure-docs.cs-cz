---
title: Kódování datového zdroje pomocí standardu Media Encoder Standard pomocí rozhraní .NET | Dokumenty společnosti Microsoft
description: Tento článek ukazuje, jak pomocí rozhraní .NET kódovat datový zdroj pomocí standardu kodéru médií.
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
ms.date: 03/18/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: 259e32d55f25c4a146b7ff358eb503763dd5fab2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "69016590"
---
# <a name="encode-an-asset-with-media-encoder-standard-using-net"></a>Kódování datového zdroje pomocí standardu Media Encoder Standard pomocí rozhraní .NET  

Úlohy kódování jsou jednou z nejběžnějších operací zpracování ve službě Media Services. Vytvoření úloh kódování pro převod mediálních souborů z jednoho kódování do druhého. Při kódování můžete použít integrovaný kodéru médií. Můžete také použít kodér poskytovaný partnerem mediálních služeb. Kodéry třetích stran jsou k dispozici prostřednictvím Azure Marketplace. 

Tento článek ukazuje, jak pomocí rozhraní .NET kódovat datové zdroje pomocí standardu kodéru médií (MES). Standard kodéru médií je konfigurován pomocí jednoho z přednastavení kodéry popsaných [zde](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

Doporučujeme vždy zakódovat zdrojové soubory do sady MP4 s adaptivní množí datový tok a poté ji převést na požadovaný formát pomocí [dynamického balení](media-services-dynamic-packaging-overview.md). 

Pokud je váš výstupní prostředek zašifrován úložištěm, je nutné nakonfigurovat zásady doručování datových zdrojů. Další informace naleznete [v tématu Konfigurace zásad doručování majetku](media-services-dotnet-configure-asset-delivery-policy.md).

> [!NOTE]
> MES vytvoří výstupní soubor s názvem, který obsahuje prvních 32 znaků názvu vstupního souboru. Název je založen na tom, co je zadáno v přednastaveném souboru. Například "FileName": "{Basename}_{Index}{Extension}". {Basename} je nahrazen o prvních 32 znaků názvu vstupního souboru.
> 
> 

### <a name="mes-formats"></a>Formáty MES
[Formáty a kodeky](media-services-media-encoder-standard-formats.md)

### <a name="mes-presets"></a>Přednastavení MES
Standard kodéru médií je konfigurován pomocí jednoho z přednastavení kodéry popsaných [zde](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

### <a name="input-and-output-metadata"></a>Vstupní a výstupní metadata
Když kódujete vstupní prostředek (nebo datové zdroje) pomocí MES, získáte výstupní datový zdroj při úspěšném dokončení této úlohy kódování. Výstupní datový zdroj obsahuje video, zvuk, miniatury, manifest atd.

Výstupní datový zdroj také obsahuje soubor s metadaty o vstupním datovém zdroji. Název souboru XML metadat má následující formát: <asset_id>_metadata.xml (například 41114ad3-eb5e-4c57-8d92-5354e2b7d4a4_metadata.xml), kde <asset_id> je hodnota AssetId vstupního datového zdroje. Schéma tohoto vstupního souboru XML vstupních metadat je popsáno [zde](media-services-input-metadata-schema.md).

Výstupní datový zdroj také obsahuje soubor s metadaty o výstupním datovém zdroji. Název souboru XML metadat má následující formát: <source_file_name>_manifest.xml (například BigBuckBunny_manifest.xml). Schéma tohoto výstupního jazyka XML metadat je popsáno [zde](media-services-output-metadata-schema.md).

Chcete-li prozkoumat některý ze dvou souborů metadat, můžete vytvořit lokátor SAS a stáhnout soubor do místního počítače. Můžete najít příklad, jak vytvořit lokátor SAS a stáhnout soubor pomocí rozšíření Media Services .NET SDK.

## <a name="download-sample"></a>Stažení ukázky
Můžete získat a spustit ukázku, která ukazuje, jak kódovat s MES [odtud](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="net-sample-code"></a>Ukázkový kód rozhraní .NET

Následující příklad kódu používá k provádění následujících úloh sad Media Services .NET SDK následující úkoly:

* Vytvořte úlohu kódování.
* Získejte odkaz na standardní kodér mediálního kodéru.
* Určete, chcete-li použít přednastavení [adaptivního streamování.](media-services-autogen-bitrate-ladder-with-mes.md) 
* Přidejte do úlohy jednu úlohu kódování. 
* Zadejte vstupní datový zdroj, který má být kódován.
* Vytvořte výstupní datový zdroj, který obsahuje kódovaný datový zdroj.
* Přidejte obslužnou rutinu události pro kontrolu průběhu úlohy.
* Odešlete práci.

#### <a name="create-and-configure-a-visual-studio-project"></a>Vytvoření a konfigurace projektu Visual Studia

Nastavte své vývojové prostředí a v souboru app.config vyplňte informace o připojení, jak je popsáno v tématu [Vývoj pro Media Services v .NET](media-services-dotnet-how-to-use.md). 

#### <a name="example"></a>Příklad 

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

## <a name="advanced-encoding-features-to-explore"></a>Pokročilé funkce kódování k prozkoumání
* [Jak generovat miniatury](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Generování miniatur během kódování](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Oříznutí videí během kódování](media-services-crop-video.md)
* [Přizpůsobení přednastavení kódování](media-services-custom-mes-presets-with-dotnet.md)
* [Překrytí nebo vodoznak videa s obrázkem](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Další kroky
[Jak generovat miniatury pomocí standardu Media Encoder Standard s](media-services-dotnet-generate-thumbnail-with-mes.md)
[přehledem kódování .NET Media Services](media-services-encode-asset.md)

