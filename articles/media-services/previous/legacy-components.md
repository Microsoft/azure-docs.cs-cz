---
title: Azure Media Services starší verze komponent | Microsoft Docs
description: Toto téma popisuje Azure Media Services starších komponent.
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
ms.openlocfilehash: 1c3e50fd64c113aafd2d70cc76c7f789e080ad23
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269518"
---
# <a name="azure-media-services-legacy-components"></a>Azure Media Services starší součásti

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

V průběhu času byla k dispozici stabilní vylepšení a vylepšení komponent Media Service. V důsledku toho byly některé starší komponenty vyřazeny. Pokyny, jak migrovat aplikaci ze starší verze na aktuální komponentu, najdete v následujících článcích.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Plány vyřazení zastaralých součástí a pokyny k migraci

Oznamujeme vyřazení *Windows Azure Media Encoder* (WAME) a *Azure Media Encoderch* (ázev) mediálních procesorů. Tyto procesory jsou vyřazené 31 2020.

* [Migrace z Windows Azure Media Encoder na Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Migrace z Azure Media Encoder na Media Encoder Standard](migrate-azure-media-encoder.md)

Také oznamujeme vyřazení následujících Media Analytics mediálních procesorů: 
 
|Název procesoru médií|Datum vyřazení|Další poznámky|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)|1. ledna 2020|Tento multimediální procesor se nahrazuje [Azure Media Services video indexer](../video-indexer/index.yml). Další informace najdete v tématu [migrace z Azure Media Indexer 2 do Azure Media Services video indexer](migrate-indexer-v1-v2.md).|
|[Azure Media Indexer](media-services-index-content.md)|1. března 2023|Tento multimediální procesor se nahrazuje [Azure Media Services video indexer](../video-indexer/index.yml). Další informace najdete v tématu [migrace z Azure Media Indexer do Azure Media Services video indexer](migrate-indexer-v1-v2.md)|
|[Detekce pohybu](media-services-motion-detection.md)|1. června 2020|V tuto chvíli nejsou k dispozici žádné náhradní plány.|
|[Shrnutí videa](media-services-video-summarization.md)|1. června 2020|V tuto chvíli nejsou k dispozici žádné náhradní plány.|
|[Optické rozpoznávání znaků pro video](media-services-video-optical-character-recognition.md)|1. června 2020|Tento multimediální procesor se nahrazuje [Azure Media Services video indexer](../video-indexer/index.yml). Zvažte také použití [Azure Media Services V3 API](../latest/analyzing-video-audio-files-concept.md). <br/>Viz [Compare Azure Media Services V3 předvolby a video indexer](../video-indexer/compare-video-indexer-with-media-services-presets.md)|
|[Detektor tváří](media-services-face-and-emotion-detection.md)|1. června 2020|Tento multimediální procesor se nahrazuje [Azure Media Services video indexer](../video-indexer/index.yml). Zvažte také použití [Azure Media Services V3 API](../latest/analyzing-video-audio-files-concept.md). <br/>Viz [Compare Azure Media Services V3 předvolby a video indexer](../video-indexer/compare-video-indexer-with-media-services-presets.md)|
|[Content Moderator](media-services-content-moderation.md)|1. června 2020|Tento multimediální procesor se nahrazuje [Azure Media Services video indexer](../video-indexer/index.yml). Zvažte také použití [Azure Media Services V3 API](../latest/analyzing-video-audio-files-concept.md). <br/>Viz [Compare Azure Media Services V3 předvolby a video indexer](../video-indexer/compare-video-indexer-with-media-services-presets.md)|

## <a name="next-steps"></a>Další kroky

[Pokyny k migraci pro přesun z Media Services V2 na V3](../latest/migrate-from-v2-to-v3.md)
