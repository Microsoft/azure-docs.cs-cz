---
title: Pomocí Azure Media Encoder Standard můžete automaticky vygenerovat přenosových | Dokumentace Microsoftu
description: Toto téma ukazuje, jak automaticky vygenerovat přenosových na základě vstupního rozlišení a s přenosovou rychlostí pomocí Media Encoderu Standard (MES). Se nikdy překročí vstupního rozlišení a s přenosovou rychlostí. Například pokud je vstup 720p na 3 MB/s, výstup se nejlépe zůstanou 720p a začne sazby nižší než 3 MB/s.
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
ms.date: 02/08/2019
ms.author: juliako
ms.openlocfilehash: 5ccd53ac03235bd114218135488bef25891754be
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56004056"
---
#  <a name="use-azure-media-encoder-standard-to-auto-generate-a-bitrate-ladder"></a>Automaticky generovat přenosových pomocí Azure Media Encoder Standard  

## <a name="overview"></a>Přehled

Tento článek ukazuje, jak automaticky vygenerovat přenosových (řešení s přenosovou rychlostí páry) na základě vstupního rozlišení a s přenosovou rychlostí pomocí Media Encoderu Standard (MES). Automaticky generované přednastavení se nikdy nepřekročí vstupního rozlišení a s přenosovou rychlostí. Například pokud je vstup 720p na 3 MB/s, výstup zůstane 720p nejlépe a začne sazby nižší než 3 MB/s.

### <a name="encoding-for-streaming-only"></a>Kódování pro vysílání datového proudu pouze

Pokud máte v úmyslu zakódování zdrojového videa zdroje jenom pro streamování, měli byste použít "Adaptivní streamování" přednastavení při vytváření úlohu kódování. Při použití **adaptivní streamování** přednastavení MES kodér bude inteligentně cap přenosových. Ale nebudete mít ovládací prvek kódování náklady, protože služba určuje, kolik vrstvy použít a jaké rozlišením. Příklady výstup vrstvy vytvářených v důsledku kódování pomocí MES se můžete podívat **adaptivní streamování** přednastavení na konci tohoto článku. Výstupní Asset obsahuje soubory MP4, kde audio a video není prokládané.

### <a name="encoding-for-streaming-and-progressive-download"></a>Kódování pro streamování a progresivní stahování

Pokud máte v úmyslu k zakódování zdrojového videa zdroje pro streamování a také vytvářejí soubory MP4 pro progresivní stahování, měli byste použít "obsahu adaptivní více přenosovými rychlostmi MP4" přednastavení při vytváření úlohu kódování. Při použití **obsahu s přenosovou rychlostí MP4 více Adaptive** přednastavení, kodér MES použije stejný kódování logiku, jak je uvedeno výše, ale nyní výstupní asset bude obsahovat soubory MP4 tam, kde zvuku a prokládané video. Můžete použít některý z těchto souborů MP4 (například nejvyšší verze s přenosovou rychlostí) jako soubor progresivní stahování.

## <a id="encoding_with_dotnet"></a>Kódování pomocí Media Services .NET SDK

Následující příklad kódu používá sadu Media Services .NET SDK k provádění následujících úloh:

- Vytvořte úlohu kódování.
- Získání odkazu na kodéru Media Encoder Standard.
- Přidat úlohu kódování do projektu a nastavení, aby používal **adaptivní streamování** předvolby. 
- Vytvoření výstupní asset, který obsahuje zakódovanému assetu.
- Přidáte obslužnou rutinu události chcete zkontrolovat průběh úlohy.
- Odeslání úlohy.

#### <a name="create-and-configure-a-visual-studio-project"></a>Vytvoření a konfigurace projektu Visual Studia

Nastavte své vývojové prostředí a v souboru app.config vyplňte informace o připojení, jak je popsáno v tématu [Vývoj pro Media Services v .NET](media-services-dotnet-how-to-use.md). 

#### <a name="example"></a>Příklad:

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

## <a id="output"></a>Výstup

Tato část ukazuje tři příklady výstup vrstvy vytvářených v důsledku kódování pomocí MES **adaptivní streamování** předvolby. 

### <a name="example-1"></a>Příklad 1
Zdroj s výšku "1080" a "29.970" snímkovou vytvoří 6 videa vrstvy:

|Vrstva|Výška|Šířka|Bitrate(Kbps)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Příklad 2
Zdroj s výšku "720" a "23.970" snímkovou vytvoří 5 videa vrstvy:

|Vrstva|Výška|Šířka|Bitrate(Kbps)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Příklad 3
Zdroj s výšku "360" a "29.970" snímkovou vytvoří vrstvy 3 videa:

|Vrstva|Výška|Šířka|Bitrate(Kbps)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|
## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Viz také
[Media Services kódování – přehled](media-services-encode-asset.md)

