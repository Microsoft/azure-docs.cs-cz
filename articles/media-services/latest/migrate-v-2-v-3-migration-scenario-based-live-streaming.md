---
title: Pokyny k migraci Media Services živého streamování
description: Tento článek vám poskytne návod na základě scénářů živého streamování, který vám pomůže při minimální migraci z Azure Media Services V2 na v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 6a2c6495ca3685aec1bc132ec7f8a88809ad2d87
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598277"
---
# <a name="live-streaming-scenario-based-migration-guidance"></a>Průvodce migrací na základě scénáře živého streamování

![logo Průvodce migrací](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![kroky migrace 2](./media/migration-guide/steps-4.svg)

Azure Portal teď podporuje nastavení a správu živých událostí.  Doporučujeme, abyste si ho vyzkoušeli při testování migrace v2 na v3.

## <a name="test-the-v3-live-event-workflow"></a>Testování pracovního postupu pro živou událost V3

> [!NOTE]
> Kanály a programy vytvořené pomocí v2 (které jsou namapované na živé události a živé výstupy ve verzi V3) se spravují pomocí rozhraní V3 API. Pokyny je přepnout na 3 až V3 živé události a živé výstupy v pohodlný čas, kdy můžete zastavit stávající kanál v2. V současné době není dostupná žádná podpora pro bezproblémové migrace nepřetržitě běžícího nepřetržitého živého kanálu na nové události V3 Live. Proto musí být prostoje údržby pro vaši cílovou skupinu a firmy koordinovány na nejlepší pohodlí.

Před přesunutím obsahu z verze V2 na V3 otestujte nový způsob doručování živých událostí pomocí Media Services. Tady jsou funkce v3, se kterými můžete pracovat, a zvažte migraci.

- Vytvoří novou [živou událost](live-events-outputs-concept.md#live-events) v3 pro kódování. Můžete povolit [Předvolby kódování 1080p a 720p](live-event-types-comparison.md#system-presets).
- Místo programů použít entitu [Live Output](live-events-outputs-concept.md#live-outputs)
- Vytvořte [Lokátory streamování](streaming-locators-concept.md).
- Berte v úvahu, že budete potřebovat [HLS a přerušované](dynamic-packaging-overview.md) živé streamování.
- Pokud budete vyžadovat rychlé spuštění živých událostí, prozkoumejte nové funkce v [pohotovostním režimu](live-events-outputs-concept.md#standby-mode) .
- Pokud chcete svou živou událost přepisovat, když se děje, prozkoumejte novou funkci pro [živý přepis](live-transcription.md) .
- Pokud potřebujete delší dobu trvání streamování, vytvořte v zcela nepřetržitou živé události v hodnotě v3.
- Pomocí [Event Grid](monitoring/monitor-events-portal-how-to.md) můžete monitorovat živé události.

Konkrétní kroky najdete v tématu Koncepty, kurzy a návody k dynamickým událostem.

## <a name="live-events-concepts-tutorials-and-how-to-guides"></a>Koncepty, kurzy a návody k živým událostem

### <a name="concepts"></a>Koncepty

- [Živé streamování s Azure Media Services V3](live-streaming-overview.md)
- [Živé události a živé výstupy v Media Services](live-events-outputs-concept.md)
- [Ověřená místní kodéry živého streamování](recommended-on-premises-live-encoders.md)
- [Použití časových posunutí a živých výstupů k vytvoření přehrávání videa na vyžádání](live-event-cloud-dvr.md)
- [Live – přepis (Preview)](live-transcription.md)
- [Porovnání typů živých událostí](live-event-types-comparison.md)
- [Stavy a fakturace živých událostí](live-event-states-billing.md)
- [Nastavení nízké latence události za provozu](live-event-latency.md)
- [Media Services kódy chyb živé události](live-event-error-codes.md)

### <a name="tutorials-and-quickstarts"></a>Kurzy a rychlé starty

- [Kurz: živé streamování pomocí Media Services](stream-live-tutorial-with-api.md)
- [Vytvoření živého datového proudu Azure Media Services pomocí OBS](live-events-obs-quickstart.md)
- [Rychlý Start: nahrání, kódování a streamování obsahu pomocí portálu](manage-assets-quickstart.md)
- [Rychlý Start: vytvoření streamu Azure Media Services Live pomocí Wirecast](live-events-wirecast-quickstart.md)

## <a name="samples"></a>ukázky

[V ukázkách kódu můžete také porovnat kód v2 a V3](migrate-v-2-v-3-migration-samples.md).

## <a name="next-steps"></a>Další kroky

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
