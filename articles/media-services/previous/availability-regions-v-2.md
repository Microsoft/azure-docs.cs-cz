---
title: Azure Media Services oblast dostupnosti | Microsoft Docs
description: Tento článek představuje přehled funkcí Microsoft Azure Media Services a dostupnost služeb.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: inhenkel
ms.custom: references_regions
ms.openlocfilehash: 458927afc78c46ad365d437a574d0b40b32fc11e
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98695793"
---
# <a name="media-services-regional-availability"></a>Media Services oblast dostupnosti

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. Podívejte se na nejnovější verzi [Media Services V3](../latest/media-services-overview.md). Podívejte se taky na [pokyny k migraci z v2 na V3](../latest/migrate-v-2-v-3-migration-introduction.md) .

Microsoft Azure Media Services (AMS) umožňuje bezpečně nahrávat, ukládat, kódovat a balit obsah (video nebo zvuk) doručovaný na vyžádání i živě streamovaný různým klientům (například do televizí, počítačů a mobilních zařízení).

AMS funguje ve více oblastech po celém světě a poskytuje flexibilitu při výběru místa pro sestavování aplikací. Tento článek představuje přehled funkcí Microsoft Azure Media Services a dostupnost služeb.

Další informace o celé globální infrastruktuře Azure najdete v tématu geografické [grafy Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="ams-accounts"></a>Účty AMS

Pomocí [produktů Azure podle oblastí](https://azure.microsoft.com/global-infrastructure/services/?products=media-services&regions=all) zjistíte, jestli je Media Services k dispozici v konkrétní oblasti.

## <a name="streaming-endpoints"></a>Koncové body streamování

Zákazníci Media Services si můžou zvolit koncový bod streamování **Standard**, nebo koncový bod streamování **Premium**.

|Name|Status|Region (Oblast)
|---|---|---|
|Standard|GA|Vše|
|Premium|GA|Vše|

## <a name="live-encoding"></a>Kódování v reálném čase

K dispozici ve všech oblastech kromě: Německo, Brazílie – jih, Indie – západ, Indie – jih a Indie – střed.

## <a name="encoding-media-processors"></a>Kódovací procesory médií

AMS nabízí dva kodéry na vyžádání – **Media Encoder Standard** a **Pracovní postup kodéru Media Encoder Premium**. Další informace najdete v tématu [Přehled a porovnání kodérů médií na vyžádání v Azure](media-services-encode-asset.md).

|Název procesoru médií|Status|Oblasti
|---|---|---|
|Media Encoder Standard|GA|Vše|
|Pracovní postup kodéru Media Encoder Premium|GA|Všechny s výjimkou Číny|

## <a name="analytics-media-processors"></a>Analytické procesory médií

Media Analytics je kolekce řečových a vizuálních komponent, které organizacím a podnikům umožňují, aby ze svých videosouborů odvodily prakticky využitelné informace. Další informace najdete v článku o [přehledu Azure Media Services Analytics](./legacy-components.md).

> [!NOTE]
> Některé procesory pro analýzu médií budou vyřazeny. Data o vyřazení najdete v tématu [starší verze součástí](legacy-components.md) .

|Název procesoru médií|Status|Region (Oblast)
|---|---|---|
|Azure Media Face Detector|Preview|Vše|
|Azure Media Indexer|GA|Vše|
|Azure Media Motion Detector|Preview|Vše|
|Azure Media OCR|Preview|Vše|
|Azure Media Redactor|GA|Vše|
|Azure Media Video Thumbnails|Preview|Vše|

## <a name="protection"></a>Ochrana

Microsoft Azure Media Services umožňuje zabezpečení médií od okamžiku opuštění počítače přes uložení a zpracování až po doručení. Další informace najdete v tématu [Ochrana obsahu AMS](media-services-content-protection-overview.md).

|Šifrování|Status|Oblasti|
|---|---|---| 
|Storage|GA|Vše|
|Klíče AES-128|GA|Vše|
|FairPlay|GA|Vše|
|PlayReady|GA|Vše|
|Widevine|GA|Vše kromě Německa, federální vlády a Číny.

> [!NOTE]
> Widevine je služba od společnosti Google Inc. v souladu s podmínkami služby a zásadami ochrany osobních údajů Google, Inc.

## <a name="reserved-units-rus"></a>Rezervované jednotky (RU)

Počet zřízených rezervovaných jednotek určuje počet úloh médií, které je možné v daném účtu zpracovávat současně.

K dispozici ve všech oblastech.

## <a name="reserved-unit-ru-type"></a>Typ rezervované jednotky (RU)

Účet Media Services je přidružen k typu rezervované jednotky, který určuje rychlost, s jakou jsou úlohy zpracování médií dokončeny. Můžete si vybrat mezi následujícími typy rezervovaných jednotek: S1, S2 nebo S3.

|Název typu RU|Status|Oblasti
|---|---|---|
|S1|GA|Vše|
|S2|GA|Všechna kromě oblastí Brazílie – jih a Indie – západ|
|S3|GA|Všechna kromě oblasti Indie – západ|

## <a name="next-steps"></a>Další kroky

[Migrace na Media Services v3](../latest/media-services-overview.md)

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]