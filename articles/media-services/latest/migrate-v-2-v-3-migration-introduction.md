---
title: Migrace z Media Services V2 na V3 – Úvod
description: Tento článek představuje úvod do migrace z Media Services V2 na v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 3514a7c809e939ea2c45afa5ab60539232b8781f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98953287"
---
# <a name="migrate-from-media-services-v2-to-v3-introduction"></a>Migrace z Media Services V2 na V3 – Úvod

![logo Průvodce migrací](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

Průvodce migrací Media Services vám pomůže migrovat z rozhraní API Media Services V2 na V3 API na základě migrace, která využívá nové funkce a funkce, které jsou teď dostupné. Měli byste využít své nejlepší rozhodnutí a určit, co nejlépe vyhovuje vašemu scénáři.

## <a name="how-to-use-this-guide"></a>Jak používat tohoto průvodce

### <a name="navigating"></a>Navigace

V celé příručce se zobrazí následující obrázek.

![kroky migrace](./media/migration-guide/steps.svg)<br/>

Krok, který se nachází, bude označen změnou barvy v čísle kroku, například takto:

![kroky migrace 2](./media/migration-guide/steps-2.svg)<br/>

Na konci každé stránky se zobrazí odkazy na zbytek dokumentů migrace, které si můžete přečíst pod hlavičkou **Další postup** .

### <a name="guidance"></a>Pokyny

Pokyny, které jsou zde uvedeny, jsou *Obecné*. Zahrnuje obsah, který vylepšuje vaše povědomí o tom, co je teď dostupné ve verzi v3, a také to, co se změnilo v Media Services pracovních postupech.

Podrobnější pokyny, včetně obrazovek a koncepční grafiky, obsahuje odkazy na koncepty, kurzy, rychlé starty, ukázky a Reference k rozhraní API v jednotlivých tématech založených na scénářích. Také uvádíme ukázky, které vám pomůžou porovnat rozhraní API v2 s rozhraním API v3.

## <a name="migration-guidance-overview"></a>Přehled pokynů k migraci

Při migraci je potřeba provést čtyři obecné kroky:

## <a name="step-1-benefits"></a>Krok 1 – výhody

<hr color="#5ea0ef" size="10">

[Pochopte výhody](migrate-v-2-v-3-migration-benefits.md) migrace na rozhraní Media Services API v3.

## <a name="step-2-differences"></a>Krok 2 – rozdíly

<hr color="#5ea0ef" size="10">

Pochopte rozdíly mezi Media Services V2 API a rozhraním API v3.

- [Přístup přes rozhraní API](migrate-v-2-v-3-differences-api-access.md)
- [Mezery funkcí](migrate-v-2-v-3-differences-feature-gaps.md)
- [Změny v terminologii a entitě](migrate-v-2-v-3-differences-terminology.md)

## <a name="step-3-sdk-setup"></a>Krok 3 instalace sady SDK

<hr color="#5ea0ef" size="10">

Seznamte se s rozdíly v sadě SDK a [nastavte migraci na verzi V3 REST API nebo klientskou sadu SDK](migrate-v-2-v-3-migration-setup.md).

## <a name="step-4-scenario-based-guidance"></a>Krok 4: pokyny na základě scénáře

<hr color="#5ea0ef" size="10">

Vaše aplikace Media Services V2 může být jedinečná. Proto jsme poskytli pokyny založené na scénářích na základě toho, jak jste v minulosti *mohli* používat službu Media Services, a postup pro jednotlivé funkce služby, jako je například:

- [Kódování](migrate-v-2-v-3-migration-scenario-based-encoding.md)
- [Živé streamování](migrate-v-2-v-3-migration-scenario-based-live-streaming.md)
- [Balení a doručování](migrate-v-2-v-3-migration-scenario-based-publishing.md)
- [Ochrana obsahu](migrate-v-2-v-3-migration-scenario-based-content-protection.md)
- [Rezervované jednotky médií (MRU)](migrate-v-2-v-3-migration-scenario-based-media-reserved-units.md)

## <a name="next-steps"></a>Další kroky

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
