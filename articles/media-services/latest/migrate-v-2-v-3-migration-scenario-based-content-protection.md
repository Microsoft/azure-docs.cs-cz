---
title: Pokyny k migraci obsahu Protection
description: Tento článek vám poskytne návod na základě scénáře ochrany obsahu, který vám pomůže při minimální migraci z Azure Media Services V2 na v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 9c0040c0ad34b019eaa90bbd1571377ad3539578
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98940107"
---
# <a name="content-protection-scenario-based-migration-guidance"></a>Pokyny k migraci na základě scénáře ochrany obsahu

![logo Průvodce migrací](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![kroky migrace 2](./media/migration-guide/steps-4.svg)

Tento článek vám poskytne návod na základě scénáře ochrany obsahu, který vám pomůže při minimální migraci z Azure Media Services V2 na v3.

## <a name="protect-content-in-v3-api"></a>Ochrana obsahu v rozhraní V3 API

V novém rozhraní API V3 použijte podporu pro [multi-klíčové](design-multi-drm-system-with-access-control.md) funkce.

Konkrétní postup najdete v tématu věnovaném konceptům, kurzům a návodům k ochraně obsahu.

## <a name="content-protection-concepts-tutorials-and-how-to-guides"></a>Koncepty, kurzy a návody k ochraně obsahu

### <a name="concepts"></a>Koncepty

- [Ochrana obsahu pomocí Media Services dynamického šifrování](content-protection-overview.md)
- [Návrh systému ochrany obsahu s více variantami DRM s využitím řízení přístupu](design-multi-drm-system-with-access-control.md)
- [Media Services V3 se šablonou licence PlayReady](playready-license-template-overview.md)
- [Přehled šablon licencí Media Services V3 with Widevine](widevine-license-template-overview.md)
- [Konfigurace a licenční požadavky pro Apple FairPlay](fairplay-license-overview.md)
- [Zásady streamování](streaming-policy-concept.md)
- [Zásady pro klíč obsahu](content-key-policy-concept.md)

### <a name="tutorials"></a>Kurzy

[Rychlý Start: použití portálu k šifrování obsahu](encrypt-content-quickstart.md)

### <a name="how-to-guides"></a>Průvodce postupy

- [Získání podpisového klíče ze stávajících zásad](get-content-key-policy-dotnet-howto.md)
- [Online streamování FairPlay pro iOS s Media Services V3](offline-fairplay-for-ios.md)
- [Online streamování Widevine pro Android s Media Services V3](offline-widevine-for-android.md)
- [Online streamování PlayReady pro Windows 10 s Media Services V3](offline-plaready-streaming-for-windows-10.md)

## <a name="samples"></a>ukázky

[V ukázkách kódu můžete také porovnat kód v2 a V3](migrate-v-2-v-3-migration-samples.md).

## <a name="next-steps"></a>Další kroky

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]