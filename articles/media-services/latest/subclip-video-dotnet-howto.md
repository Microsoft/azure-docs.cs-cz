---
title: Při kódování pomocí Azure Media Services můžete video podříznout
description: Toto téma popisuje, jak při kódování pomocí služby Azure Media Services pomocí sady .NET SDK podklipovat video.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/09/2019
ms.author: juliako
ms.openlocfilehash: 3d584ee742aa93cdecf4b04d942afb2ed83a7357
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67305145"
---
# <a name="subclip-a-video-when-encoding-with-media-services---net"></a>Subclip video při kódování s Media Services - .NET

Video můžete oříznout nebo podklipu při kódování pomocí [úlohy](https://docs.microsoft.com/rest/api/media/jobs). Tato funkce funguje s libovolnou [transformací,](https://docs.microsoft.com/rest/api/media/transforms) která je vytvořena pomocí [přednastavení BuiltInStandardEncoder Preset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) nebo [Přednastavení StandardEncoder.](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset)

Následující příklad Jazyka C# vytvoří úlohu, která ořízne video ve datovém zdroji při odesílání úlohy kódování. 

## <a name="prerequisites"></a>Požadavky

Chcete-li provést kroky popsané v tomto tématu, musíte:

- [Vytvoření účtu Azure Media Services](create-account-cli-how-to.md)
- Vytvořte transformace a vstupní a výstupní prostředky. Můžete vidět, jak vytvořit transformace a vstupní a výstupní prostředky v [upload, kódování a streamování videa pomocí .NET](stream-files-tutorial-with-api.md) kurzu.
- Projděte si téma [konceptu kódování.](encoding-concept.md)

## <a name="example"></a>Příklad

```csharp
/// <summary>
/// Submits a request to Media Services to apply the specified Transform to a given input video.
/// </summary>
/// <param name="client">The Media Services client.</param>
/// <param name="resourceGroupName">The name of the resource group within the Azure subscription.</param>
/// <param name="accountName"> The Media Services account name.</param>
/// <param name="transformName">The name of the transform.</param>
/// <param name="jobName">The (unique) name of the job.</param>
/// <param name="inputAssetName">The name of the input asset.</param>
/// <param name="outputAssetName">The (unique) name of the  output asset that will store the result of the encoding job. </param>
// <SubmitJob>
private static async Task<Job> JobWithBuiltInStandardEncoderWithSingleClipAsync(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string transformName,
    string jobName,
    string inputAssetName,
    string outputAssetName)
{
    var jobOutputs = new List<JobOutputAsset>
    {
        new JobOutputAsset(state: JobState.Queued, progress: 0, assetName: outputAssetName)
    };

    var clipStart = new AbsoluteClipTime()
    {
        Time = new TimeSpan(0, 0, 20)
    };

    var clipEnd = new AbsoluteClipTime()
    {
        Time = new TimeSpan(0, 0, 30)
    };

    var jobInput = new JobInputAsset(assetName: inputAssetName, start: clipStart, end: clipEnd);

    Job job = await client.Jobs.CreateAsync(
        resourceGroupName,
        accountName,
        transformName,
        jobName,
        new Job(input: jobInput, outputs: jobOutputs.ToArray(), name: jobName)
        {
            Description = $"A Job with transform {transformName} and single clip.",
            Priority = Priority.Normal,
        });

    return job;
}
```

## <a name="next-steps"></a>Další kroky

[Kódování pomocí vlastní transformace](customize-encoder-presets-how-to.md) 
