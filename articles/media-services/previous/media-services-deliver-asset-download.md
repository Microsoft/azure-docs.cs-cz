---
title: Stažení Media Services prostředků do počítače – Azure | Microsoft Docs
description: Přečtěte si o stažení prostředků do svého počítače. Ukázky kódu jsou napsané v jazyce C# a používají sadu Media Services SDK pro .NET.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 8908a1dd-3ffb-4f18-955d-4c8e2d82fc5d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 60f91e97a9bce1427b4ed8d251fe297d9eb7d969
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103016657"
---
# <a name="how-to-deliver-an-asset-by-download"></a>Postupy: doručení assetu stažením

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Tento článek popisuje možnosti pro doručování mediálních prostředků odeslaných do Media Services. Obsah Media Services můžete doručovat v mnoha scénářích aplikací. Po kódování Stáhněte vygenerované mediální prostředky nebo k nim přihlaste pomocí lokátoru streamování. Pro zlepšení výkonu a škálovatelnosti můžete obsah doručovat také pomocí Content Delivery Network (CDN).

Tento příklad ukazuje, jak stáhnout mediální prostředky z Media Services do místního počítače. Kód dotazuje úlohy přidružené k účtu Media Services podle ID úlohy a přistupuje ke své kolekci **OutputMediaAssets** (což je sada jednoho nebo více výstupních prostředků médií, které jsou výsledkem spuštění úlohy). Tento příklad ukazuje, jak stáhnout výstupní mediální prostředky z úlohy, ale můžete použít stejný přístup ke stažení jiných prostředků.

>[!NOTE]
>Je stanovený limit 1 000 000 různých zásad AMS (třeba zásady lokátoru nebo ContentKeyAuthorizationPolicy). Použijte stejné ID zásad, pokud vždycky používáte stejné dny nebo přístupová oprávnění, například zásady pro Lokátory, které mají zůstat v platnosti po dlouhou dobu (zásady bez nahrávání). Další informace najdete v [tomto](media-services-dotnet-manage-entities.md#limit-access-policies) článku.

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
[Doručení obsahu streamování](media-services-deliver-streaming-content.md)

