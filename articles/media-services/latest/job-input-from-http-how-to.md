---
title: Vytvořit úlohu služby Azure média vstup z adresy URL http (s) | Microsoft Docs
description: Toto téma ukazuje, jak vytvořit úlohu vstup z adresy URL http (s).
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: a47dc3e4939dd27d7ac11be823a09b8a6cba1f60
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="create-a-job-input-from-an-https-url"></a>Vytvoření úlohy vstup z adresy URL http (s)

Ve službě Media Services v3 při odesílání úloh zpracujte videa, budete muset říct Media Services, kde najít vstupní video. Jednu z možností je zadat adresu URL http (s) jako úlohu vstup (jak je uvedeno v tomto příkladu). Úplný příklad najdete [githubu ukázka](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>Ukázka rozhraní .net

Následující kód ukazuje, jak vytvořit úlohu s adresu URL HTTPS vstup.

```csharp
private static Job SubmitJob(IAzureMediaServicesClient client, string transformName, string jobName)
{
    Asset outputAsset = client.Assets.CreateOrUpdate(Guid.NewGuid().ToString() + "-output", new Asset());

    JobInputHttp jobInput = 
        new JobInputHttp(files: new[] { "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4" });

    JobOutput[] jobOutputs =
    {
        new JobOutputAsset(outputAsset.Name),
    };

    Job job = client.Jobs.CreateOrUpdate(
        transformName,
        jobName,
        new Job
        {
            Input = jobInput,
            Outputs = jobOutputs,
        });

    return job;
}
```

## <a name="next-steps"></a>Další postup

[Vytvořit úlohu vstupní z místního souboru](job-input-from-local-file-how-to.md).
