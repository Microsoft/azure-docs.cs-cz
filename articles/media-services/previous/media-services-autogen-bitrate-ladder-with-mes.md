---
title: Automatické generování žebříku pro přenosovou rychlost pomocí standardu Media Encoder Standard – Azure | Dokumenty společnosti Microsoft
description: Toto téma ukazuje, jak pomocí standardu Media Encoder Standard (MES) automaticky generovat žebřík s přenosovou rychlostí na základě vstupního rozlišení a přenosového toku.
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
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: b7f0b77ba11a0c9c1670ec240caf45fcf61a934d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896022"
---
#  <a name="use-media-encoder-standard-to-auto-generate-a-bitrate-ladder"></a>Automatické generování žebříku s přenosovou rychlostí pomocí standardu media encoder standardu  

## <a name="overview"></a>Přehled

Tento článek ukazuje, jak používat Standard kodéru médií (MES) k automatickému generování žebříku s přenosovou rychlostí (dvojice bitrate-rozlišení) na základě vstupního rozlišení a přenosového toku. Automaticky generované přednastavení nikdy nepřekročí vstupní rozlišení a přenosovou rychlost. Například pokud je vstup 720p při 3 Mbps, výstup zůstane v nejlepším případě 720p a začne rychlostí nižší než 3 Mbps.

### <a name="encoding-for-streaming-only"></a>Kódování pouze pro streamování

Pokud máte v úmyslu kódovat zdrojové video pouze pro streamování, měli byste při vytváření úlohy kódování použít přednastavení "Adaptivní streamování". Při použití přednastavení **adaptivního streamování** bude kodér MES inteligentně završovat žebřík s datovým tokem. Však nebude možné řídit náklady na kódování, protože služba určuje, kolik vrstev použít a v jakém rozlišení. Můžete zobrazit příklady výstupních vrstev vytvořených MES v důsledku kódování s přednastavení **adaptivní streamování** na konci tohoto článku. Výstupní datový zdroj obsahuje soubory MP4, kde není zvuk a video prokládáno.

### <a name="encoding-for-streaming-and-progressive-download"></a>Kódování pro streamování a postupné stahování

Pokud je vaším záměrem zakódovat zdrojové video pro streamování, stejně jako vytvářet soubory MP4 pro postupné stahování, měli byste při vytváření úlohy kódování použít přednastavení "Content Adaptive Multiple Bitrate MP4". Při použití přednastavení **MP4 pro adaptivní vícebitý tok obsahu** používá kodér MES stejnou logiku kódování jako výše, ale nyní bude výstupní datový zdroj obsahovat soubory MP4, kde je zvuk a video prokládány. Jeden z těchto souborů MP4 (například verze s nejvyšším přenosovým tokem) můžete použít jako soubor s postupným stahováním.

## <a name="encoding-with-media-services-net-sdk"></a><a id="encoding_with_dotnet"></a>Kódování pomocí sady Media Services .NET SDK

Následující příklad kódu používá k provádění následujících úloh sad Media Services .NET SDK následující úkoly:

- Vytvořte úlohu kódování.
- Získejte odkaz na standardní kodér mediálního kodéru.
- Přidejte úlohu kódování do úlohy a určete, aby se používalo přednastavení **Adaptivní streamování.** 
- Vytvořte výstupní datový zdroj, který obsahuje kódovaný datový zdroj.
- Přidejte obslužnou rutinu události pro kontrolu průběhu úlohy.
- Odešlete práci.

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

Tato část ukazuje tři příklady výstupních vrstev vytvořených MES v důsledku kódování s **přednastavením adaptivního streamování.** 

### <a name="example-1"></a>Příklad 1
Zdroj s výškou "1080" a snímkovou frekvencí "29.970" vytváří 6 vrstev videa:

|Vrstva|Vlastnost Height|impulzu|Bitrate (kbps)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Příklad 2
Zdroj s výškou "720" a snímkovou frekvencí "23.970" vytváří 5 vrstev videa:

|Vrstva|Vlastnost Height|impulzu|Bitrate (kbps)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Příklad 3
Zdroj s výškou "360" a snímkovou frekvencí "29.970" vytváří 3 vrstvy videa:

|Vrstva|Vlastnost Height|impulzu|Bitrate (kbps)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|
## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Viz také
[Přehled kódování mediálních služeb](media-services-encode-asset.md)

