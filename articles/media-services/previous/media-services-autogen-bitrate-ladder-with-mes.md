---
title: Použití Media Encoder Standard k automatickému vytvoření žebříku přenosové rychlosti – Azure | Microsoft Docs
description: V tomto tématu se dozvíte, jak použít Media Encoder Standard (status) k automatickému vygenerování přenosové žebříky na základě vstupních rozlišení a přenosové rychlosti.
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
ms.openlocfilehash: 6ea28d61cc142c3191d591721b92e08d651c7ed5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103014396"
---
#  <a name="use-media-encoder-standard-to-auto-generate-a-bitrate-ladder"></a>Použití Media Encoder Standard k automatickému vygenerování žebříku přenosové rychlosti

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]  

## <a name="overview"></a>Přehled

Tento článek ukazuje, jak použít Media Encoder Standard (status) k automatickému vygenerování přenosového žebříku (páry rozlišení přenosů) na základě vstupních rozlišení a přenosové rychlosti. Automaticky vygenerované předvolby nikdy nepřekročí vstupní rozlišení a přenosovou rychlost. Pokud je například vstup ve formátu 720p ve 3 MB/s, výstup bude ve formátu 720p nejlepší a bude začínat tarify nižšími než 3 MB/s.

### <a name="encoding-for-streaming-only"></a>Kódování jenom pro streamování

Pokud máte v úmyslu zakódovat zdrojové video jenom pro streamování, měli byste při vytváření úlohy kódování použít přednastavení adaptivního streamování. Když použijete přednastavení **adaptivního streamování** , kodér programu pro úpravu koncového vysílání inteligentně odsadí žebřík s přenosovou rychlostí. Nebudete však moci řídit náklady na kódování, protože služba Určuje, kolik vrstev se má použít a v jakém rozlišení. Příklady výstupních vrstev vyprodukovaných pomocí programu pro úpravu obsahu můžete zobrazit v důsledku kódování pomocí přednastavení **adaptivního streamování** na konci tohoto článku. Výstupní Asset obsahuje soubory MP4, kde není prokládaný zvuk a video.

### <a name="encoding-for-streaming-and-progressive-download"></a>Kódování pro streamování a progresivní stahování

Pokud je vaším záměrem kódovat zdrojové video pro streamování a vytvářet soubory MP4 pro progresivní stahování, měli byste při vytváření úlohy kódování použít předvolbu "obsah adaptivního přenosu více přenosů MP4". Když použijete přednastavení **MP4 s adaptivní přenosovou rychlostí** , použije kodér status Encoder stejnou logiku kódování jako výše, ale výstupní Asset teď bude obsahovat soubory MP4, ve kterých je audio a video prokládaný. Jako soubor progresivního stahování můžete použít jeden z těchto souborů MP4 (například nejvyšší verzi přenosové rychlosti).

## <a name="encoding-with-media-services-net-sdk"></a><a id="encoding_with_dotnet"></a>Kódování pomocí sady Media Services .NET SDK

Následující příklad kódu používá sadu Media Services .NET SDK k provádění následujících úloh:

- Vytvořte úlohu kódování.
- Získejte odkaz na kodér Media Encoder Standard.
- Přidejte úlohu kódování do úlohy a určete, že se má použít přednastavení **adaptivního streamování** . 
- Vytvořte výstupní Asset, který obsahuje kódovaný Asset.
- Přidejte obslužnou rutinu události pro kontrolu průběhu úlohy.
- Odešlete úlohu.

#### <a name="create-and-configure-a-visual-studio-project"></a>Vytvoření a konfigurace projektu Visual Studia

Nastavte své vývojové prostředí a v souboru app.config vyplňte informace o připojení, jak je popsáno v tématu [Vývoj pro Media Services v .NET](media-services-dotnet-how-to-use.md). 

#### <a name="example"></a>Příklad

```
using System;
using System.Configuration;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;

namespace AdaptiveStreamingMESPresest
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

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
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

## <a name="output"></a><a id="output"></a>Výstup

Tato část ukazuje tři příklady výstupních vrstev vyprodukovaných pomocí programu pro úpravu obsahu pomocí přednastavení **adaptivního streamování** . 

### <a name="example-1"></a>Příklad 1
Zdroj s výškou "1080" a snímkem "29,970" vytváří 6 vrstev videa:

|Vrstva|Height (Výška)|Width (Šířka)|Přenosová rychlost (KB/s)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Příklad 2
Zdroj s výškou "720" a snímkem "23,970" vytváří 5 vrstev videa:

|Vrstva|Height (Výška)|Width (Šířka)|Přenosová rychlost (KB/s)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Příklad 3
Zdroj s výškou "360" a snímkem "29,970" vytvoří 3 vrstvy videa:

|Vrstva|Height (Výška)|Width (Šířka)|Přenosová rychlost (KB/s)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|
## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Viz také
[Přehled kódování Media Services](media-services-encode-asset.md)

