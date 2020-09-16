---
title: Azure Media Services starší verze komponent | Microsoft Docs
description: Toto téma popisuje Azure Media Services starších komponent.
services: media-services
documentationcenter: ''
author: myoungerman
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2020
ms.author: v-myoung
ms.openlocfilehash: d5a10ccc887124b2bc5595d66baad833a32275cc
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2020
ms.locfileid: "90600887"
---
# <a name="azure-media-services-legacy-components"></a>Azure Media Services starší součásti

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

V průběhu času vylepšujeme komponenty Media Service a vyřadíme starší verze komponent. Tento článek vám pomůže migrovat aplikaci ze starší verze na aktuální komponentu.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Plány vyřazení zastaralých součástí a pokyny k migraci

Procesory médií *Windows Azure Media Encoder* (WAME) a *Azure Media Encoder* (ázev) jsou zastaralé.

* [Migrace z Windows Azure Media Encoder na Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Migrace z Azure Media Encoder na Media Encoder Standard](migrate-azure-media-encoder.md)

Následující Media Analytics multimediální procesory se už nepoužívají nebo už brzy nejsou zastaralé:  
 
|Název procesoru médií|Datum vyřazení|Další poznámky|
|---|---|
|Azure Media Indexer 2|1. ledna 2020|Tento multimediální procesor byl nahrazen [Azure Media Services video indexer](../video-indexer/index.yml). Další informace najdete v tématu [migrace z Azure Media Indexer 2 do Azure Media Services video indexer](migrate-indexer-v1-v2.md).|
|[Azure Media Indexer](media-services-index-content.md)|1. března 2023|Tento procesor médií bude nahrazen [Azure Media Services video indexer](../video-indexer/index.yml). Další informace najdete v tématu [migrace z Azure Media Indexer do Azure Media Services video indexer](migrate-indexer-v1-v2.md).|
|Detekce pohybu|1. června 2020|V tuto chvíli nejsou k dispozici žádné náhradní plány.|
|Shrnutí videa] (Media-Services-video-summarization.md|1. června 2020|V tuto chvíli nejsou k dispozici žádné náhradní plány.|
|Optické rozpoznávání znaků pro video|1. června 2020|Tento multimediální procesor byl nahrazen [Azure Media Services video indexer](../video-indexer/index.yml). Zvažte také použití [Azure Media Services V3 API](../latest/analyzing-video-audio-files-concept.md). <br/>Přečtěte si téma [porovnání Azure Media Services V3 předvolby a video indexer](../video-indexer/compare-video-indexer-with-media-services-presets.md).|
|Detektor tváří|1. června 2020|Tento multimediální procesor byl nahrazen [Azure Media Services video indexer](../video-indexer/index.yml). Zvažte také použití [Azure Media Services V3 API](../latest/analyzing-video-audio-files-concept.md). <br/>Přečtěte si téma [porovnání Azure Media Services V3 předvolby a video indexer](../video-indexer/compare-video-indexer-with-media-services-presets.md).|
|Content Moderator|1. června 2020|Tento multimediální procesor byl nahrazen [Azure Media Services video indexer](../video-indexer/index.yml). Zvažte také použití [Azure Media Services V3 API](../latest/analyzing-video-audio-files-concept.md). <br/>Přečtěte si téma [porovnání Azure Media Services V3 předvolby a video indexer](../video-indexer/compare-video-indexer-with-media-services-presets.md).|

## <a name="next-steps"></a>Další kroky

[Pokyny k migraci pro přesun z Media Services V2 na V3](../latest/migrate-from-v2-to-v3.md)
