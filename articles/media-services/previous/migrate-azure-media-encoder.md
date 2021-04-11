---
title: Migrace z Azure Media Encoder na Media Encoder Standard | Microsoft Docs
description: Toto téma popisuje, jak migrovat z Azure Media Encoder do procesoru Media Encoder Standard Media.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 4f528cea9b475c0158524ad9b46623a78df5761d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103016198"
---
# <a name="migrate-from-azure-media-encoder-to-media-encoder-standard"></a>Migrace z Azure Media Encoder na Media Encoder Standard

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Tento článek popisuje kroky pro migraci z staršího (Azure Media Encoderho) procesoru médií (v tomto případě) do procesoru Media Encoder Standard Media. Informace o datech vyřazení najdete v tématu tyto [starší součásti](legacy-components.md) .

Při kódování souborů pomocí ázev zákazníci obvykle použili pojmenovaný přednastavený řetězec, například `H264 Adaptive Bitrate MP4 Set 1080p` . Aby bylo možné provést migraci, je nutné aktualizovat kód tak, aby používal procesor **Media Encoder Standard** Media místo ázev a jednu z ekvivalentních [předvoleb systému](media-services-mes-presets-overview.md) jako `H264 Multiple Bitrate 1080p` . 

## <a name="migrating-to-media-encoder-standard"></a>Migrace na Media Encoder Standard

Zde je typický ukázkový kód v jazyce C#, který používá starší verzi procesoru médií. 

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

Zde je aktualizovaná verze, která používá Media Encoder Standard.

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

Pokud jste vytvořili vlastní předvolby kódování pro ázev pro \ jméno pomocí svého schématu, existuje [stejné schéma pro Media Encoder Standard](media-services-mes-schema.md). Pokud máte dotazy k namapování staršího nastavení na nový kodér, obraťte se na nás přes mailto:amshelp@microsoft.com  
## <a name="known-differences"></a>Známé rozdíly 

Media Encoder Standard je robustnější, spolehlivější, má lepší výkon a produkuje kvalitní výstup než starší ázev kodéru. Dále musí být splněny všechny tyto podmínky: 

* Media Encoder Standard vytváří výstupní soubory s jinou konvencí pojmenování, než je ázev.
* Media Encoder Standard vytváří artefakty, jako jsou soubory obsahující [metadata vstupních souborů](media-services-input-metadata-schema.md) a [metadata výstupních souborů](media-services-output-metadata-schema.md).

## <a name="next-steps"></a>Další kroky

* [Starší verze komponent](legacy-components.md)
* [Stránka s cenami](https://azure.microsoft.com/pricing/details/media-services/#encoding)
