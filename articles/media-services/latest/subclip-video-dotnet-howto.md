---
title: Rozříznout video při kódování pomocí Media Services
description: Toto téma popisuje, jak rozříznout video při kódování s Azure Media Services pomocí sady .NET SDK
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: how-to
ms.date: 06/09/2019
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: a9408adfe33fc71707532f3bf5646f3727fd7dda
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98955050"
---
# <a name="subclip-a-video-when-encoding-with-media-services---net"></a>Rozříznout video při kódování pomocí Media Services-.NET

Video můžete při kódování pomocí [úlohy](/rest/api/media/jobs)oříznout nebo vystřihnout. Tato funkce funguje s libovolnou [transformací](/rest/api/media/transforms) , která je sestavená buď pomocí přednastavení [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) , nebo z [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) předvoleb.

Následující příklad jazyka C# vytvoří úlohu, která ořízne video v Assetu, když odešle úlohu kódování. 

## <a name="prerequisites"></a>Předpoklady

K dokončení kroků popsaných v tomto tématu je třeba provést následující kroky:

- [Vytvoření účtu Azure Media Services](./create-account-howto.md)
- Vytvořte transformaci a vstupní a výstupní prostředky. Informace o tom, jak vytvořit transformace a vstupní a výstupní prostředky, najdete v kurzech k [nahrávání, kódování a streamování pomocí .NET](stream-files-tutorial-with-api.md) .
- Přečtěte si téma o [pojmu kódování](encoding-concept.md) .

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

[Kódování s vlastní transformací](customize-encoder-presets-how-to.md) 
