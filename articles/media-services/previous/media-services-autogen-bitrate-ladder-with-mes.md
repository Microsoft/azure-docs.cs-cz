---
title: Automaticky generovat žebříku přenosovou rychlostí pomocí Azure Media Encoder Standard | Microsoft Docs
description: Toto téma ukazuje způsob použití Media Encoder Standard (MES) pro automatické generování žebříku přenosovou rychlostí na základě vstupních řešení a přenosovou rychlostí. Vstupní řešení a přenosovou rychlostí se nikdy překročena. Například pokud je vstupní 720p na 3 MB/s, bude výstup zůstat v nejlépe 720p a spustí tempem nižší než 3 MB/s.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2017
ms.author: juliako
ms.openlocfilehash: 80f76f413ec2c267ba8fb93514480e168563f470
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
#  <a name="use-azure-media-encoder-standard-to-auto-generate-a-bitrate-ladder"></a>Pomocí Azure Media Encoder Standard pro automatické generování žebříku přenosovou rychlostí

## <a name="overview"></a>Přehled

Tento článek ukazuje způsob použití Media Encoder Standard (MES) pro automatické generování žebříku přenosovou rychlostí (přenosovou rychlostí řešení páry) na základě vstupních řešení a přenosovou rychlostí. Automaticky generovaný přednastavení se nikdy překročit vstupní řešení a přenosovou rychlostí. Například pokud vstupní 720p 3 MB/s, výstup v nejlépe zůstane 720p a začne tempem nižší než 3 MB/s.

### <a name="encoding-for-streaming-only"></a>Kódování pro streamování pouze

Pokud vaše je určený ke kódování videa zdroje jenom pro streamování, měli byste použít "Adaptivní streamování" přednastavení při vytvoření úlohy kódování. Při použití **adaptivní datové proudy** předvolby, kodér MES bude inteligentně cap žebříku přenosovou rychlostí. Ale nebudete moct kódování náklady, protože služba určuje, kolik vrstvách používat ovládací prvek a v jakém rozlišení. Vidíte příklady vytvoření MES v důsledku kódování s vrstev výstup **adaptivní datové proudy** přednastavení na konci tohoto článku. Výstupní Asset obsahuje soubory MP4, kde audio a video není prokládaný.

### <a name="encoding-for-streaming-and-progressive-download"></a>Kódování pro streamování a progresivní stahování

Pokud vaše je určený ke kódování zdroj videa pro streamování a také k vytváření souborů MP4 pro progresivní stahování, měli byste použít "obsahu adaptivní více přenosovou rychlostí MP4" přednastavení při vytvoření úlohy kódování. Při použití **obsahu adaptivní více MP4 přenosovou rychlostí** předvolby, kodér MES platí stejné logiky, která kódování, jak je uvedeno výše, ale teď výstupní asset bude obsahovat soubory MP4, kde audio a video je prokládaný. Jako soubor progresivní stahování můžete použít jednu z těchto souborů MP4 (například nejvyšší verze přenosovou rychlostí).

## <a id="encoding_with_dotnet"></a>Kódování pomocí služby Media Services .NET SDK

Následující příklad kódu používá sadu Media Services .NET SDK k provádění následujících úloh:

- Vytvořte úlohu kódování.
- Získáte odkaz na kodéru Media Encoder Standard.
- Přidejte kódování úkolů do úlohy a použít **adaptivní datové proudy** přednastavené. 
- Vytvoření výstupní asset, který obsahuje k zakódovanému assetu.
- Přidání obslužné rutiny události zkontrolovat průběh úlohy.
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

Tato část ukazuje tři příklady vytvoření MES v důsledku kódování s vrstev výstup **adaptivní datové proudy** přednastavené. 

### <a name="example-1"></a>Příklad 1
Zdroj s výšky "1080" a "29.970" kmitočet snímků vytváří 6 video vrstvy:

|Vrstva|Výška|Šířka|Bitrate(Kbps)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Příklad 2
Zdroj s výšky "720" a "23.970" kmitočet snímků vytváří 5 video vrstvy:

|Vrstva|Výška|Šířka|Bitrate(Kbps)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Příklad 3
Zdroj s výšky "360" a "29.970" kmitočet snímků vytváří vrstvy 3 videa:

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
[Kódování Přehled služby Media Services](media-services-encode-asset.md)

