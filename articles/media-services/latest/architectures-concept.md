---
title: Architektury Media Services
description: Tento článek popisuje architektury pro Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: inhenkel
ms.openlocfilehash: ad464eb1c0b6dec694c7c40868a0f95fcfeaf6e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98891484"
---
# <a name="media-services-architectures"></a>Architektury Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="live-streaming-digital-media"></a>Digitální média živého streamování

Řešení živého streamování umožňuje zachytit video v reálném čase a vysílat ho uživatelům v reálném čase, jako jsou například rozhovory o vysílání datového proudu, konference a sportovní události online. V tomto řešení se video zachycuje pomocí video kamery a odesílá se do vstupního koncového bodu kanálu. Kanál přijme živý vstupní datový proud a zpřístupní ho pro streamování prostřednictvím koncového bodu streamování do webového prohlížeče nebo mobilní aplikace. Kanál také poskytuje koncový bod monitorování Preview pro náhled a ověření datového proudu před dalším zpracováním a doručením. Kanál může také nahrávat a ukládat ingestný obsah, aby se mohl později streamovat (video-na vyžádání).

Toto řešení je postavené na spravovaných službách Azure: Media Services a Content Delivery Network. Tyto služby běží v prostředí s vysokou dostupností, které jsou opravené a podporované, takže se můžete soustředit na vaše řešení, a ne na prostředí, ve kterém běží.

Podívejte se na [živá streamovaná digitální média](/azure/architecture/solution-ideas/articles/digital-media-live-stream) v centru architektury Azure.

## <a name="video-on-demand-digital-media"></a>Digitální média VoD (Video-on-Demand)

Základní řešení videa na vyžádání, které poskytuje možnost streamování zaznamenaného obsahu videa, jako jsou filmy, novinové klipy, sportovní segmenty, školicí videa a kurzy zákaznické podpory, do libovolného zařízení koncového uživatele, mobilní aplikace nebo desktopového prohlížeče. Videosoubory se nahrají do úložiště objektů BLOB v Azure, které se zakóduje do standardního formátu s více přenosovými rychlostmi, a pak se pomocí všech hlavních přizpůsobitelných protokolů pro streamování s adaptivní přenosovou rychlostí (HLS, MPEG-SPOJOVNÍK, plynule) do klienta Azure Media Player.

Toto řešení je postavené na spravovaných službách Azure: Blob Storage, Content Delivery Network a Azure Media Player. Tyto služby běží v prostředí s vysokou dostupností, které jsou opravené a podporované, takže se můžete soustředit na vaše řešení, a ne na prostředí, ve kterém běží.

Podívejte se [na digitální média videa na vyžádání](/azure/architecture/solution-ideas/articles/digital-media-video) v centru architektury Azure.

## <a name="gridwich-media-processing-system"></a>Systém pro zpracování médií Gridwich

Systém Gridwich přináší osvědčené postupy pro zpracování a poskytování mediálních prostředků v Azure. I když je systém Gridwich specifický pro média, rozhraní pro zpracování zpráv a zpracování událostí se může vztahovat na libovolný stavový pracovní postup zpracování událostí.

Viz [Gridwich Media Processing System](/azure/architecture/reference-architectures/media-services/gridwich-architecture) v centru architektury Azure.

## <a name="next-steps"></a>Další kroky

> [Přehled služby Azure Media Services](media-services-overview.md)