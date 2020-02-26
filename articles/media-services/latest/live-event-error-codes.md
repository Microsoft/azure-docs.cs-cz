---
title: Azure Media Services kódy chyb živé události | Microsoft Docs
description: V tomto článku jsou uvedeny kódy chyb živé události.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2020
ms.author: juliako
ms.openlocfilehash: bfbef771d33ad4d63ec8eaef83331e497d476071
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599467"
---
# <a name="media-services-live-event-error-codes"></a>Media Services kódy chyb živé události

V následující tabulce jsou uvedeny kódy chyb [živé události](live-events-outputs-concept.md) :

|Chyba|Popis|
|---|---| 
|MPE_INGEST_FRAMERATE_EXCEEDED|K této chybě dochází, když příchozí kodér posílá streamy překračující 30fps pro kódování živých událostí nebo kanálů.|
|MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED|K této chybě dochází, pokud příchozí kodér posílá proudy překračující následující rozlišení: 1920x1088 pro kódování živých událostí a kanálů a 4096 x 2160 pro předávací živé události a kanály.|

## <a name="see-also"></a>Viz také

[Kódy chyb koncového bodu streamování (Origin)](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>Další kroky

[Kurz: živé streamování pomocí Media Services](stream-live-tutorial-with-api.md)
