---
title: Stažení datových zdrojů mediálních služeb do počítače – Azure | Dokumenty společnosti Microsoft
description: Přečtěte si o stahování datových zdrojů do počítače. Ukázky kódu jsou zapsány v c# a používají sady Media Services SDK pro rozhraní .NET.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 8908a1dd-3ffb-4f18-955d-4c8e2d82fc5d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 21fcc6ae09718ffbb22e1d438926586dd3cde71d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "61465656"
---
# <a name="how-to-deliver-an-asset-by-download"></a>Postup: Dodání datového zdroje stažením  
Tento článek popisuje možnosti doručování mediálních datových zdrojů nahraných do služby Media Services. Obsah služby Media Services můžete doručovat v mnoha scénářích aplikace. Po kódování stáhněte generované datové zdroje médií nebo k nim získejte přístup pomocí lokátoru streamování. Chcete-li zvýšit výkon a škálovatelnost, můžete také poskytovat obsah pomocí sítě pro doručování obsahu (CDN).

Tento příklad ukazuje, jak stáhnout datové zdroje médií ze služby Media Services do místního počítače. Kód dotazuje úlohy přidružené k účtu Media Services podle ID úlohy a přistupuje k jeho **OutputMediaAssets** kolekce (což je sada jednoho nebo více datových zdrojů výstupního média, který je výsledkem spuštění úlohy). Tento příklad ukazuje, jak stáhnout datové zdroje výstupních médií z úlohy, ale můžete použít stejný přístup ke stažení jiných datových zdrojů.

>[!NOTE]
>Je stanovený limit 1 000 000 různých zásad AMS (třeba zásady lokátoru nebo ContentKeyAuthorizationPolicy). Stejné ID zásad použijte, pokud vždy používáte stejné dny / přístupová oprávnění, například zásady pro lokátory, které mají zůstat na místě po dlouhou dobu (zásady bez nahrávání). Další informace naleznete v [tomto](media-services-dotnet-manage-entities.md#limit-access-policies) článku.

```csharp
    // Download the output asset of the specified job to a local folder.
    static IAsset DownloadAssetToLocal( string jobId, string outputFolder)
    {
        // This method illustrates how to download a single asset. 
        // However, you can iterate through the OutputAssets
        // collection, and download all assets if there are many. 

        // Get a reference to the job. 
        IJob job = GetJob(jobId);

        // Get a reference to the first output asset. If there were multiple 
        // output media assets you could iterate and handle each one.
        IAsset outputAsset = job.OutputMediaAssets[0];

        // Create a SAS locator to download the asset
        IAccessPolicy accessPolicy = _context.AccessPolicies.Create("File Download Policy", TimeSpan.FromDays(30), AccessPermissions.Read);
        ILocator locator = _context.Locators.CreateLocator(LocatorType.Sas, outputAsset, accessPolicy);

        BlobTransferClient blobTransfer = new BlobTransferClient
        {
            NumberOfConcurrentTransfers = 20,
            ParallelTransferThreadCount = 20
        };

        var downloadTasks = new List<Task>();
        foreach (IAssetFile outputFile in outputAsset.AssetFiles)
        {
            // Use the following event handler to check download progress.
            outputFile.DownloadProgressChanged += DownloadProgress;

            string localDownloadPath = Path.Combine(outputFolder, outputFile.Name);

            Console.WriteLine("File download path:  " + localDownloadPath);

            downloadTasks.Add(outputFile.DownloadAsync(Path.GetFullPath(localDownloadPath), blobTransfer, locator, CancellationToken.None));

            outputFile.DownloadProgressChanged -= DownloadProgress;
        }

        Task.WaitAll(downloadTasks.ToArray());

        return outputAsset;
    }

    static void DownloadProgress(object sender, DownloadProgressChangedEventArgs e)
    {
        Console.WriteLine(string.Format("{0} % download progress. ", e.Progress));
    }
```


## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Viz také
[Doručování streamovaného obsahu](media-services-deliver-streaming-content.md)

