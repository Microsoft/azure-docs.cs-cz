---
title: Kódování assetu pomocí Media Encoder Standard pomocí rozhraní .NET | Microsoft Docs
description: Tento článek ukazuje, jak použít .NET ke kódování assetu pomocí Media Encoder Standard.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 03431b64-5518-478a-a1c2-1de345999274
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewer: anilmur
ms.custom: devx-track-csharp
ms.openlocfilehash: b94ec0b50182381d2a4e1bca09f4c8bc482d13ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103014090"
---
# <a name="encode-an-asset-with-media-encoder-standard-using-net"></a>Kódování assetu pomocí Media Encoder Standard pomocí .NET

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]  

Úlohy kódování jsou jednou z nejběžnějších operací zpracování v Media Services. Vytvoříte úlohy kódování pro převod mediálních souborů z jednoho kódování na jiný. Při kódování můžete použít integrovaný kodér médií Media Services. Můžete také použít kodér poskytovaný Media Services partnerem. kodéry třetích stran jsou k dispozici prostřednictvím Azure Marketplace. 

Tento článek ukazuje, jak použít .NET ke kódování prostředků pomocí Media Encoder Standard (status). Media Encoder Standard se konfiguruje pomocí některého z přednastavení kodérů popsaných [tady](./media-services-mes-presets-overview.md).

Doporučuje se, aby se zdrojové soubory vždycky zakódovat do sady souborů MP4 s adaptivní přenosovou rychlostí, a pak tuto sadu převést na požadovaný formát pomocí [dynamického balení](media-services-dynamic-packaging-overview.md). 

Pokud je váš výstupní prostředek zašifrovaný z úložiště, musíte nakonfigurovat zásady doručení assetu. Další informace najdete v tématu [Konfigurace zásad doručení assetu](media-services-dotnet-configure-asset-delivery-policy.md).

> [!NOTE]
> Modul pro výstup vytvoří výstupní soubor s názvem, který obsahuje prvních 32 znaků názvu vstupního souboru. Název je založen na tom, co je zadáno v přednastaveném souboru. Například "FileName": "{Baseer} _ {index} {Extension}". {Base} je nahrazeno prvním 32 znaky názvu vstupního souboru.
> 
> 

### <a name="mes-formats"></a>Formáty tržních
[Formáty a kodeky](media-services-media-encoder-standard-formats.md)

### <a name="mes-presets"></a>Přednastavení MES
Media Encoder Standard se konfiguruje pomocí některého z přednastavení kodérů popsaných [tady](./media-services-mes-presets-overview.md).

### <a name="input-and-output-metadata"></a>Vstupní a výstupní metadata
Při kódování vstupního prostředku (nebo prostředků) pomocí nástroje pro vytváření se systémem získáte výstupní Asset po úspěšném dokončení této úlohy kódování. Výstupní Asset obsahuje video, zvuk, miniatury, manifest atd. na základě použité předvolby kódování.

Výstupní Asset obsahuje také soubor s metadaty o vstupním prostředku. Název souboru XML s metadaty má následující formát: <asset_id # C1_metadata.xml (například 41114ad3-eb5e-4c57-8d92-5354e2b7d4a4_metadata.xml), kde <asset_id> je AssetId hodnotou vstupního assetu. Schéma těchto vstupních metadat XML je popsáno [zde](media-services-input-metadata-schema.md).

Výstupní Asset obsahuje také soubor s metadaty o výstupním prostředku. Název souboru XML s metadaty má následující formát: <source_file_name # C1_manifest.xml (například BigBuckBunny_manifest.xml). Schéma tohoto výstupního metadat XML je popsáno [zde](media-services-output-metadata-schema.md).

Pokud chcete prostudovat některé ze dvou souborů metadat, můžete vytvořit Lokátor SAS a stáhnout soubor do místního počítače. Můžete najít příklad, jak vytvořit Lokátor SAS a stáhnout soubor pomocí rozšíření Media Services .NET SDK.

## <a name="download-sample"></a>Stažení ukázky
Z [tohoto místa](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/)můžete získat a spustit ukázku, která ukazuje, jak kódovat pomocí programu pro zjištění oproti tržnímu souboru.

## <a name="net-sample-code"></a>Vzorový kód .NET

Následující příklad kódu používá sadu Media Services .NET SDK k provádění následujících úloh:

* Vytvořte úlohu kódování.
* Získejte odkaz na kodér Media Encoder Standard.
* Určete, že se má použít přednastavení [adaptivního streamování](media-services-autogen-bitrate-ladder-with-mes.md) . 
* Přidejte do úlohy jednu úlohu kódování. 
* Určete vstupní Asset, který se má zakódovat.
* Vytvořte výstupní Asset, který obsahuje kódovaný Asset.
* Přidejte obslužnou rutinu události pro kontrolu průběhu úlohy.
* Odešlete úlohu.

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

## <a name="advanced-encoding-features-to-explore"></a>Pokročilé funkce kódování, které se mají prozkoumat
* [Jak generovat miniatury](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Generování miniatur během kódování](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Oříznout videa během kódování](media-services-crop-video.md)
* [Přizpůsobení předvoleb kódování](media-services-custom-mes-presets-with-dotnet.md)
* [Překrytí nebo zobrazení vodoznaku videa s obrázkem](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Další kroky
[Jak generovat miniaturu pomocí Media Encoder Standard s .NET](media-services-dotnet-generate-thumbnail-with-mes.md) 
 [Přehled kódování Media Services](media-services-encode-asset.md)