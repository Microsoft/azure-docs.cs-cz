---
title: Starší součásti Azure Media Services | Dokumenty společnosti Microsoft
description: Toto téma popisuje starší součásti Azure Media Services.
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
ms.date: 02/27/2020
ms.author: juliako
ms.openlocfilehash: 94a70a1234d902787f248890f0cb538a4ba9c2f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921075"
---
# <a name="azure-media-services-legacy-components"></a>Starší součásti Azure Media Services

V průběhu času došlo k neustálému zlepšování a vylepšení komponent mediální služby. V důsledku toho byly některé starší součásti vyřazeny. Pokyny k migraci aplikace ze starší součásti do aktuální součásti naleznete v následujících článcích.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Plány vyřazení starších komponent a pokyny pro migraci

Oznamujeme vyřazení mediálních procesorů *Windows Azure Media Encoder* (WAME) a *Azure Media Encoder* (AME). Tyto procesory jsou vyřazeny na 31.března 2020.

* [Migrace z kodéru médií Windows Azure do standardu kodéru médií](migrate-windows-azure-media-encoder.md)
* [Migrace z kodéru médií Azure do standardu kodéru médií](migrate-azure-media-encoder.md)

Oznamujeme také vyřazení následujících mediálních procesorů Media Analytics: 
 
|Název procesoru médií|Datum odchodu do důchodu|Další poznámky|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)|1. ledna 2020|Tento mediální procesor je nahrazován [programem Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Další informace najdete [v tématu Migrace z Azure Media Indexer 2 do Azure Media Services Video Indexer](migrate-indexer-v1-v2.md).|
|[Azure Media Indexer](media-services-index-content.md)|1. března 2023|Tento mediální procesor je nahrazován [programem Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Další informace najdete [v tématu Migrace z Azure Media Indexer do Azure Media Services Video Indexer](migrate-indexer-v1-v2.md)|
|[Detekce pohybu](media-services-motion-detection.md)|1. června 2020|V tuto chvíli žádné náhradní plány.|
|[Shrnutí videa](media-services-video-summarization.md)|1. června 2020|V tuto chvíli žádné náhradní plány.|
|[Optické rozpoznávání znaků videa](media-services-video-optical-character-recognition.md)|1. června 2020|Tento mediální procesor je nahrazován [programem Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Zvažte také použití [rozhraní Azure Media Services v3 API](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept). <br/>Viz [Porovnání předvoleb Azure Media Services v3 a Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|
|[Detektor tváří](media-services-face-and-emotion-detection.md)|1. června 2020|Tento mediální procesor je nahrazován [programem Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Zvažte také použití [rozhraní Azure Media Services v3 API](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept). <br/>Viz [Porovnání předvoleb Azure Media Services v3 a Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|
|[Content Moderator](media-services-content-moderation.md)|1. června 2020|Tento mediální procesor je nahrazován [programem Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Zvažte také použití [rozhraní Azure Media Services v3 API](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept). <br/>Viz [Porovnání předvoleb Azure Media Services v3 a Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|

## <a name="next-steps"></a>Další kroky

[Pokyny pro migraci pro přechod z Media Services v2 na v3](../latest/migrate-from-v2-to-v3.md)
