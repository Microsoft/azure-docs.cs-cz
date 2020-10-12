---
title: Migrace z Windows Azure Media Encoder do Media Encoder Standard | Microsoft Docs
description: Toto téma popisuje, jak migrovat z Windows Azure Media Encoder do procesoru Media Encoder Standard Media.
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
ms.date: 10/17/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 9125639ab5b563a8db7cfa5730bd9673f383071c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90529243"
---
# <a name="migrate-from-windows-azure-media-encoder-to-media-encoder-standard"></a>Migrace z Windows Azure Media Encoder na Media Encoder Standard

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Tento článek popisuje postup migrace z staršího procesoru Windows Azure Media Encoder (WAME) Media (který se právě vyřazuje) do procesoru Media Encoder Standard Media. Informace o datech vyřazení najdete v tématu tyto [starší součásti](legacy-components.md) .

Při kódování souborů pomocí WAME zákazníci obvykle použili pojmenovaný přednastavený řetězec, například `H264 Adaptive Bitrate MP4 Set 1080p` . Aby bylo možné provést migraci, váš kód musí být aktualizován, aby používal procesor **Media Encoder Standard** médií namísto WAME a jednu z ekvivalentních [předvoleb systému](media-services-mes-presets-overview.md) jako `H264 Multiple Bitrate 1080p` . 

## <a name="migrating-to-media-encoder-standard"></a>Migrace na Media Encoder Standard

Zde je typický příklad kódu v jazyce C#, který používá starší verzi součásti. 

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("WAME Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Windows Azure Media Encoder"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Adaptive Bitrate MP4 Set 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Adaptive Bitrate MP4 Set 1080p", 
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

Pokud jste vytvořili vlastní předvolby kódování pro WAME pomocí svého schématu, existuje [stejné schéma pro Media Encoder Standard](media-services-mes-schema.md).

## <a name="known-differences"></a>Známé rozdíly 

Media Encoder Standard je robustnější, spolehlivější, má lepší výkon a produkuje kvalitní výstup než starší kodér WAME. Navíc: 

* Media Encoder Standard vytváří výstupní soubory s odlišnou konvencí pojmenování než WAME.
* Media Encoder Standard vytváří artefakty, jako jsou soubory obsahující [metadata vstupních souborů](media-services-input-metadata-schema.md) a [metadata výstupních souborů](media-services-output-metadata-schema.md).
* Jak je popsáno na [stránce s cenami](https://azure.microsoft.com/pricing/details/media-services/#encoding) (zejména v části Nejčastější dotazy), při kódování videí pomocí Media Encoder Standard se vám bude účtovat na základě doby trvání souborů vytvořených jako výstup. Pomocí WAME se bude účtovat podle velikosti vstupních videosouborů a souborů výstupních videí.

## <a name="need-help"></a>Potřebujete pomoc?

Lístek podpory můžete otevřít tak, že přejdete na [novou žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) .

## <a name="next-steps"></a>Další kroky

* [Starší verze komponent](legacy-components.md)
* [Stránka s cenami](https://azure.microsoft.com/pricing/details/media-services/#encoding)
