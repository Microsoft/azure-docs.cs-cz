---
title: Zpracování mediálních souborů pomocí Azure Media Hyperlapse | Microsoft Docs
description: Azure Media Hyperlapse vytvoří smooth vypršelo čas videa z první, kdo nebo akce fotoaparát obsah. Toto téma ukazuje způsob použití Media Indexer.
services: media-services
documentationcenter: ''
author: asolanki
manager: johndeu
editor: ''
ms.assetid: 37d54db6-9cf3-4ae9-b3c6-0d29c744e965
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2018
ms.author: adsolank
ms.openlocfilehash: ed64a616538ed4699abc03225a2dcf27d164521f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788484"
---
# <a name="hyperlapse-media-files-with-azure-media-hyperlapse"></a>Zpracování mediálních souborů pomocí Azure Media Hyperlapse
Azure Media Hyperlapse je média procesoru (PP) vytvářející smooth vypršelo čas videa z první osoba nebo akce fotoaparát obsahu.  Na stejné úrovni cloudu k [plochy Hyperlapse Pro a mobilního telefonu Hyperlapse Microsoft Research](http://aka.ms/hyperlapse), Microsoft Hyperlapse pro službu Azure Media Services využívá masivním měřítku na platformě Azure Media Services média zpracování vodorovně škálování a paralelní hromadné Hyperlapse zpracování.

> [!IMPORTANT]
> Microsoft Hyperlapse slouží nejlépe pracovat na první, kdo obsahu s přesunutí fotoaparát. I když stále kamer můžete i nadále fungovat, výkonu a kvalitě procesor médií Azure Media Hyperlapse nemůže zaručit pro jiné typy obsahu.
> 
> 

Azure Media Hyperlapse úlohy vezme jako vstupní asset soubor MP4, MOV nebo WMV společně s konfiguračního souboru, který určuje, které snímky video by měla být vypršelo čas a jaké rychlosti (například první 10 000 rámců na 2 x).  Výstup je stabilizované a čas vypršelo interpretace vstupu videa.

## <a name="hyperlapse-an-asset"></a>Hyperlapse prostředek
Nejdřív je potřeba nahrát požadované vstupní soubor k Azure Media Services.  Další informace o konceptech, které jsou spojené s odesílání a správu obsahu, najdete [správy obsahu článku](media-services-portal-vod-get-started.md).

### <a id="configuration"></a>Konfigurace předvolbu pro Hyperlapse
Jakmile vašeho obsahu v váš účet Media Services, musíte vytvořit přednastavených vaší konfigurace.  Následující tabulka vysvětluje pole definované uživatelem:

| Pole | Popis |
| --- | --- |
| StartFrame |Rámečku, na kterém by měl začínat Microsoft Hyperlapse zpracování. |
| NumFrames |Počet snímků při zpracování |
| Rychlost |Faktor, ke které má být urychlení vstupní video. |

Následuje příklad vyhovující konfiguračního souboru XML a JSON:

**Přednastavení XML:**
```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
        <Sources>
            <Source StartFrame="0" NumFrames="10000" />
        </Sources>
        <Options>
            <Speed>12</Speed>
        </Options>
    </Preset>
```

**Přednastavení JSON:**
```json
    {
        "Version":1.0,
        "Sources": [
            {
                "StartFrame":0,
                "NumFrames":2147483647
            }
        ],
        "Options": {
            "Speed":1,
            "Stabilize":false
        }
    }
```

### <a id="sample_code"></a> Microsoft Hyperlapse pomocí .NET SDK služby AMS
Následující metoda odešle soubor média jako prostředek a vytvoří úlohu s procesorem Azure Media Hyperlapse média.

> [!NOTE]
> CloudMediaContext byste již měli mít v oboru s názvem "context" pro tento kód pracovat.  Další informace o tom, najdete [správy obsahu článku](media-services-dotnet-get-started.md).
> 
> [!NOTE]
> Argument řetězce "hyperConfig" musí být konfiguraci vyhovující přednastavení v XML nebo JSON, jak je popsáno výše.
> 
> 

```csharp
        static bool RunHyperlapseJob(string input, string output, string hyperConfig)
        {
            // create asset with input file
            IAsset asset = context
            .Assets
            .CreateAssetAndUploadSingleFile(input, "My Hyperlapse Input", AssetCreationOptions.None);

            // grab instances of Azure Media Hyperlapse MP
            IMediaProcessor mp = context
            .MediaProcessors
            .GetLatestMediaProcessorByName("Azure Media Hyperlapse");

            // create Job with Hyperlapse task
            IJob job = context
            .Jobs
            .Create(String.Format("Hyperlapse {0}", input));

            if (String.IsNullOrEmpty(hyperConfig))
            {
            // config cannot be empty
            return false;
            }

            hyperConfig = File.ReadAllText(hyperConfig);

            ITask hyperlapseTask = job.Tasks.AddNew("Hyperlapse task",
            mp,
            hyperConfig,
            TaskOptions.None);
            hyperlapseTask.InputAssets.Add(asset);
            hyperlapseTask.OutputAssets.AddNew("Hyperlapse output",
            AssetCreationOptions.None);

            job.Submit();

            // Create progress printing and querying tasks
            Task progressPrintTask = new Task(() =>
            {

            IJob jobQuery = null;
            do
            {
                var progressContext = context;
                jobQuery = progressContext.Jobs
                .Where(j => j.Id == job.Id)
                .First();
                Console.WriteLine(string.Format("{0}\t{1}\t{2}",
                DateTime.Now,
                jobQuery.State,
                jobQuery.Tasks[0].Progress));
                Thread.Sleep(10000);
            }
            while (jobQuery.State != JobState.Finished &&
                                   jobQuery.State != JobState.Error &&
                                   jobQuery.State != JobState.Canceled);
                });
                
            progressPrintTask.Start();

            Task progressJobTask = job.GetExecutionProgressTask(
                                                 CancellationToken.None);
            progressJobTask.Wait();

            // If job state is Error, the event handling
            // method for job progress should log errors.  Here we check
            // for error state and exit if needed.
            if (job.State == JobState.Error)
            {
                ErrorDetail error = job.Tasks.First().ErrorDetails.First();
                Console.WriteLine(string.Format("Error: {0}. {1}",
                                                error.Code,
                                                error.Message));  
                return false;                  
            }

        DownloadAsset(job.OutputMediaAssets.First(), output);
        return true;
    }

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }


    static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
    {
        IAsset asset = context.Assets.Create(assetName, options);

        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        assetFile.Upload(filePath);

        return asset;
    }

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = context.MediaProcessors
        .Where(p => p.Name == mediaProcessorName)
        .ToList()
        .OrderBy(p => new Version(p.Version))
        .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }
```

### <a id="file_types"></a>Podporované typy souborů
* MP4
* MOV
* WMV

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Související odkazy
[Azure Media Services Analytics – přehled](media-services-analytics-overview.md)

[Ukázky služby Azure Media Analytics](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

