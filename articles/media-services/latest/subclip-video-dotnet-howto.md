---
title: Dílčí klip videa při kódování pomocí Azure Media Services
description: Toto téma popisuje, jak dílčího klipu videa při kódování pomocí Azure Media Services pomocí sady .NET SDK
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
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305145"
---
# <a name="subclip-a-video-when-encoding-with-media-services---net"></a>Dílčí klip videa při kódování pomocí Media Services – .NET

Můžete oříznout nebo dílčího klipu videa, když ho pomocí kódování [úlohy](https://docs.microsoft.com/rest/api/media/jobs). Tato funkce funguje s jakoukoli [transformace](https://docs.microsoft.com/rest/api/media/transforms) , který je sestaven buď pomocí [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) přednastavení, nebo [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) předvolby.

Následující C# příklad vytvoří úlohu, která ořízne videa v Assetu, jak odešle úlohu kódování. 

## <a name="prerequisites"></a>Požadavky

K dokončení kroků popsaných v tomto tématu, budete muset:

- [Vytvoření účtu Azure Media Services](create-account-cli-how-to.md)
- Vytvoření transformace a vstupní a výstupní Assety. Zobrazí se postup vytvoření transformace a vstupní a výstupní Assety v [nahrávání, kódování a streamování videí pomocí .NET](stream-files-tutorial-with-api.md) kurzu.
- Zkontrolujte [kódování koncept](encoding-concept.md) tématu.

## <a name="example"></a>Příklad:

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

## <a name="next-steps"></a>Další postup

[Kódování s vlastní transformace](customize-encoder-presets-how-to.md) 
