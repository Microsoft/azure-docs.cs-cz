---
title: Migrace z kodéru médií Azure do standardu media encoder | Dokumenty společnosti Microsoft
description: Toto téma popisuje, jak migrovat z Azure Media Encoder do mediálního procesoru Media Encoder Standard.
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
ms.date: 08/21/2019
ms.author: juliako
ms.openlocfilehash: f8fe1b13db6473e80f0d7cdc638b775a0c8062c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513497"
---
# <a name="migrate-from-azure-media-encoder-to-media-encoder-standard"></a>Migrace z kodéru médií Azure do standardu kodéru médií

Tento článek popisuje kroky pro migraci ze staršího mediálního procesoru Azure Media Encoder (AME) (který je vyřazen) do mediálního procesoru Media Encoder Standard. Data vyřazení naleznete v tomto tématu [starších součástí.](legacy-components.md)

Při kódování souborů pomocí objektu AME zákazníci obvykle `H264 Adaptive Bitrate MP4 Set 1080p`používali pojmenovaný přednastavený řetězec, například . Chcete-li migrovat, je třeba aktualizovat kód, aby bylo možné místo ame použít mediální procesor `H264 Multiple Bitrate 1080p` **Media Encoder Standard** a jeden z ekvivalentních [systémových přednastavení,](media-services-mes-presets-overview.md) jako je . 

## <a name="migrating-to-media-encoder-standard"></a>Migrace na standard kodéru médií

Zde je typická ukázka kódu Jazyka C#, která používá starší mediální procesor. 

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("AME Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Adaptive Bitrate MP4 Set 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    " H264 Adaptive Bitrate MP4 Set 1080p", 
    TaskOptions.None); 
```

Zde je aktualizovaná verze, která používá standard kodéru médií.

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("Media Encoder Standard Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Multiple Bitrate 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Multiple Bitrate 1080p", 
    TaskOptions.None); 
```

### <a name="advanced-scenarios"></a>Pokročilé scénáře 

Pokud jste vytvořili vlastní přednastavení kódování pro AME pomocí jeho schématu, existuje [ekvivalentní schéma pro standard kodéru médií](media-services-mes-schema.md). Máte-li dotazy, jak mapovat starší nastavení na nový kodér, obraťte se na nás prostřednictvímmailto:amshelp@microsoft.com  
## <a name="known-differences"></a>Známé rozdíly 

Standard Media Encoder Standard je robustnější, spolehlivější, má lepší výkon a vytváří kvalitnější výstup než starší kodér AME. Navíc platí: 

* Standard programu Media Encoder Standard vytváří výstupní soubory s jinou konvencí pojmenování než AME.
* Standard Media Encoder Standard vytváří artefakty, jako jsou soubory obsahující [metadata vstupního souboru](media-services-input-metadata-schema.md) a [metadata výstupních souborů](media-services-output-metadata-schema.md).

## <a name="next-steps"></a>Další kroky

* [Starší verze komponent](legacy-components.md)
* [Stránka s cenami](https://azure.microsoft.com/pricing/details/media-services/#encoding)
