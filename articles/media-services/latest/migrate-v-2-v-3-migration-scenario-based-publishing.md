---
title: Pokyny k migraci balení a doručování
description: Tento článek vám poskytne návod na základě scénáře balení a doručování, který vám pomůže při migraci z Azure Media Services V2 na v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 0a0052fa3d78a3b77094cfccbd4c011321ac5925
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279013"
---
# <a name="packaging-and-delivery-scenario-based-migration-guidance"></a>Pokyny k migraci na základě scénáře balení a doručení

![logo Průvodce migrací](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![kroky migrace 2](./media/migration-guide/steps-4.svg)

Tento článek obsahuje pokyny pro scénář balení a doručování, které vám pomůžou při migraci z Azure Media Services V2 na v3.

Významné změny způsobu, jakým se obsah zveřejňuje v rozhraní V3 API. Nový model publikování je zjednodušený a používá méně entit k vytvoření lokátoru streamování. Rozhraní API se snížilo na pouze dvě entity vs. čtyři entity byly předtím požadovány. Zásady klíčů obsahu a Lokátory streamování nyní nahrazují nutnost pro `ContentKeyAuthoriationPolicy` , `AssetDeliveyPolicy` , `ContentKey` a `AccessPolicy` .

## <a name="packaging-and-delivery-in-v3"></a>Balení a doručování v v3

1. Vytvořte [zásady pro klíč obsahu](drm-content-key-policy-concept.md).
1. Vytvořte [Lokátory streamování](stream-streaming-locators-concept.md).
1. Získání [cest streamování](create-streaming-locator-build-url.md) 
    1. Nakonfigurujte ho pro [přerušovaný](encode-dynamic-packaging-concept.md#mpeg-dash-protocol) nebo [HLS](encode-dynamic-packaging-concept.md#hls-protocol) přehrávač.

Konkrétní kroky najdete v tématu věnovaném publikování konceptů, výukových kurzů a návodů.

## <a name="publishing-concepts-tutorials-and-how-to-guides"></a>Publikování konceptů, výukových kurzů a návodů

### <a name="concepts"></a>Koncepty

- [Dynamické balení v Media Services V3](encode-dynamic-packaging-concept.md)
- [Filtry](filters-concept.md)
- [Filtrování manifestů pomocí dynamického balíčku](filters-dynamic-manifest-concept.md)
- [Koncové body streamování (počátek) v Azure Media Services](stream-streaming-endpoint-concept.md)
- [Streamování obsahu pomocí integrace CDN](stream-scale-streaming-cdn-concept.md)
- [Lokátory streamování](stream-streaming-locators-concept.md)

### <a name="how-to-guides"></a>Průvodce postupy

- [Správa koncových bodů streamování pomocí Media Services V3](stream-manage-streaming-endpoints-how-to.md)
- [Ukázka CLI: Publikování assetu](cli-publish-asset.md)
- [Vytvoření lokátoru streamování a adres URL](create-streaming-locator-build-url.md)
- [Stažení výsledků úlohy](job-download-results-how-to.md)
- [Signály popisné zvukové stopy](signal-descriptive-audio-howto.md)
- [Kompletní nastavení Azure Media Playeru](../azure-media-player/azure-media-player-full-setup.md)
- [Jak používat Video.js Player s Azure Media Services](player-how-to-video-js-player.md)
- [Jak používat Shaka Player s Azure Media Services](player-shaka-player-how-to.md)

## <a name="samples"></a>ukázky

[V ukázkách kódu můžete také porovnat kód v2 a V3](migrate-v-2-v-3-migration-samples.md).
