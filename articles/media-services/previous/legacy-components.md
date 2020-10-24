---
title: Azure Media Services starší verze komponent | Microsoft Docs
description: Toto téma popisuje Azure Media Services starších komponent.
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
ms.date: 10/21/2020
ms.author: inhenkel
ms.openlocfilehash: d85cbeb99264b5b730fe585fd39f658e6448467f
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92515759"
---
# <a name="azure-media-services-legacy-components"></a>Azure Media Services starší součásti

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

V průběhu času vylepšujeme komponenty Media Service a vyřadíme starší verze komponent. Tento článek vám pomůže migrovat aplikaci ze starší verze na aktuální komponentu.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Plány vyřazení zastaralých součástí a pokyny k migraci

Procesory médií *Windows Azure Media Encoder* (WAME) a *Azure Media Encoder* (ázev) jsou zastaralé.

* [Migrace z Windows Azure Media Encoder na Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Migrace z Azure Media Encoder na Media Encoder Standard](migrate-azure-media-encoder.md)

Následující Media Analytics multimediální procesory se už nepoužívají nebo už brzy nejsou zastaralé:

  
 
| **Název procesoru médií** | **Datum vyřazení** | **Další poznámky** |
| --- | --- | ---|
| Azure Media Indexer 2 | 1. ledna 2020 | Tento procesor médií bude nahrazen [AudioAnalyzerPreset základním režimem Media Services V3](../latest/analyzing-video-audio-files-concept.md). Další informace najdete v tématu [migrace z Azure Media Indexer 2 do Azure Media Services video indexer](migrate-indexer-v1-v2.md). |
| Azure Media Indexer | 1. března 2023 | Tento procesor médií bude nahrazen [AudioAnalyzerPreset základním režimem Media Services V3](../latest/analyzing-video-audio-files-concept.md). Další informace najdete v tématu [migrace z Azure Media Indexer 2 do Azure Media Services video indexer](migrate-indexer-v1-v2.md). |
| Detekce pohybu | 1. června 2020|V tuto chvíli nejsou k dispozici žádné náhradní plány. |
| Shrnutí videa |1. června 2020|V tuto chvíli nejsou k dispozici žádné náhradní plány.|
| Optické rozpoznávání znaků pro video | 1. června 2020 |Tento multimediální procesor byl nahrazen [Azure Media Services video indexer](../video-indexer/index.yml). Zvažte také použití [Azure Media Services V3 API](../latest/analyzing-video-audio-files-concept.md). <br/>Přečtěte si téma [porovnání Azure Media Services V3 předvolby a video indexer](../video-indexer/compare-video-indexer-with-media-services-presets.md). |
| Detektor tváří | 1. června 2020 | Tento multimediální procesor byl nahrazen [Azure Media Services video indexer](../video-indexer/index.yml). Zvažte také použití [Azure Media Services V3 API](../latest/analyzing-video-audio-files-concept.md). <br/>Přečtěte si téma [porovnání Azure Media Services V3 předvolby a video indexer](../video-indexer/compare-video-indexer-with-media-services-presets.md). |
| Content Moderator | 1. června 2020 |Tento multimediální procesor byl nahrazen [Azure Media Services video indexer](../video-indexer/index.yml). Zvažte také použití [Azure Media Services V3 API](../latest/analyzing-video-audio-files-concept.md). <br/>Přečtěte si téma [porovnání Azure Media Services V3 předvolby a video indexer](../video-indexer/compare-video-indexer-with-media-services-presets.md). |

## <a name="next-steps"></a>Další kroky

[Pokyny k migraci pro přesun z Media Services V2 na V3](../latest/migrate-from-v2-to-v3.md)
