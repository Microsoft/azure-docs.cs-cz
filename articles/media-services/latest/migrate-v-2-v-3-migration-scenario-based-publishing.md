---
title: Pokyny k migraci balení a doručování
description: Tento článek vám poskytne návod na základě scénáře balení a doručování, který vám pomůže při migraci z Azure Media Services V2 na v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: f28a95ce6ef5e87eed6e5efcd013cc40b102fcba
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101721085"
---
# <a name="packaging-and-delivery-scenario-based-migration-guidance"></a>Pokyny k migraci na základě scénáře balení a doručení

![logo Průvodce migrací](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![kroky migrace 2](./media/migration-guide/steps-4.svg)

Tento článek obsahuje pokyny pro scénář balení a doručování, které vám pomůžou při migraci z Azure Media Services V2 na v3.

Významné změny způsobu, jakým se obsah zveřejňuje v rozhraní V3 API. Nový model publikování je zjednodušený a používá méně entit k vytvoření lokátoru streamování. Rozhraní API se snížilo na pouze dvě entity vs. čtyři entity byly předtím požadovány. Zásady klíčů obsahu a Lokátory streamování nyní nahrazují nutnost pro `ContentKeyAuthoriationPolicy` , `AssetDeliveyPolicy` , `ContentKey` a `AccessPolicy` .

## <a name="packaging-and-delivery-in-v3"></a>Balení a doručování v v3

1. Vytvořte [zásady pro klíč obsahu](content-key-policy-concept.md).
1. Vytvořte [Lokátory streamování](streaming-locators-concept.md).
1. Získání [cest streamování](create-streaming-locator-build-url.md) 
    1. Nakonfigurujte ho pro [přerušovaný](dynamic-packaging-overview.md#mpeg-dash-protocol) nebo [HLS](dynamic-packaging-overview.md#hls-protocol) přehrávač.

Konkrétní kroky najdete v tématu věnovaném publikování konceptů, výukových kurzů a návodů.

## <a name="publishing-concepts-tutorials-and-how-to-guides"></a>Publikování konceptů, výukových kurzů a návodů

### <a name="concepts"></a>Koncepty

- [Dynamické balení v Media Services V3](dynamic-packaging-overview.md)
- [Filtry](filters-concept.md)
- [Filtrování manifestů pomocí dynamického balíčku](filters-dynamic-manifest-overview.md)
- [Koncové body streamování (počátek) v Azure Media Services](streaming-endpoint-concept.md)
- [Streamování obsahu pomocí integrace CDN](scale-streaming-cdn.md)
- [Lokátory streamování](streaming-locators-concept.md)

### <a name="how-to-guides"></a>Průvodce postupy

- [Správa koncových bodů streamování pomocí Media Services V3](manage-streaming-endpoints-howto.md)
- [Ukázka CLI: Publikování assetu](cli-publish-asset.md)
- [Vytvoření lokátoru streamování a adres URL](create-streaming-locator-build-url.md)
- [Stažení výsledků úlohy](download-results-howto.md)
- [Signály popisné zvukové stopy](signal-descriptive-audio-howto.md)
- [Kompletní nastavení Azure Media Playeru](../azure-media-player/azure-media-player-full-setup.md)
- [Jak používat Video.js Player s Azure Media Services](how-to-video-js-player.md)
- [Jak používat Shaka Player s Azure Media Services](how-to-shaka-player.md)

## <a name="samples"></a>ukázky

[V ukázkách kódu můžete také porovnat kód v2 a V3](migrate-v-2-v-3-migration-samples.md).

## <a name="next-steps"></a>Další kroky

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]