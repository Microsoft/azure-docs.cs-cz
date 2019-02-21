---
title: Azure Media Services dynamické balení – přehled | Dokumentace Microsoftu
description: Téma s přehledem výhody dynamického balení ve službě Media Services.
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
ms.date: 02/19/2019
ms.author: juliako
ms.openlocfilehash: d1d07402bca5f01cf63d0b039c085e46bb0f0d62
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447918"
---
# <a name="dynamic-packaging"></a>Dynamické balení

Microsoft Azure Media Services umožňuje doručovat mnoho média zdrojového formátů souborů, formáty, streamování médií a ochrana obsahu formáty do různých technologií klienta (třeba iOS a XBOX). Tito klienti pochopit různé protokoly, například iOS vyžaduje, že formát protokolu HTTP Live Streaming (HLS) a služby Xbox vyžadují, technologie Smooth Streaming. Pokud máte sadu s adaptivní přenosovou rychlostí (s více přenosovými rychlostmi) MP4 soubory (ISO Base médií 14496-12) nebo sadu s adaptivní přenosovou rychlostí soubory technologie Smooth Streaming, které mají sloužit ke klientům, kteří pochopit, HLS, MPEG DASH nebo Smooth Streaming, by měl využít výhod média Dynamické balení služby.

Při dynamickém balení stačí je pouze vytvořit asset, který obsahuje sadu souborů MP4. Potom založené na formátu určeného v manifestu nebo fragment požadavek On-Demand Streaming serveru zajistí datový proud obdrželi v protokolu, kterou jste zvolili. Díky tomu pak stačí uložit (a platit) soubory pouze v jednom úložném formátu a služba Media Services bude sestavovat a dodávat vhodný formát streamování v reakci na požadavky klientů.

Následující diagram znázorňuje tradiční kódování a statické balení pracovního postupu.

![Statické kódování](./media/dynamic-packaging-overview/media-services-static-packaging.png)

Následující diagram znázorňuje dynamické balení pracovního postupu.

![Dynamické kódování](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="dynamic-packaging-workflow"></a>Dynamické balení pracovního postupu

1. Nahrání vstupního souboru (označované jako soubor mezzanine). Například H.264 MP4 nebo WMV (seznam podporovaných formátů naleznete v části [formátů podporovaných Media Encoder Standard](media-encoder-standard-formats.md).
2. Zakódujte váš soubor mezzanine do sady H.264 MP4 s adaptivní přenosovou rychlostí.
3. Publikujte asset, který obsahuje s adaptivní přenosovou sady souborů MP4.
4. Vytvoření datových proudů adres URL pro přístup k a streamování vašeho obsahu.

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Zvukových kodeků podporuje dynamické balení

Dynamické balení podporuje soubory MP4, které obsahují zvuk zakódovány [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, v2 HE-AAC), [Dolby digitální Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (rozšířené AC-3 nebo E AC3), nebo [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, DTS LBR, DTS HD, High Density, DTS HD, High Density beze ztrát).

> [!NOTE]
> Dynamické balení nepodporuje soubory, které obsahují [Dolby digitální](https://en.wikipedia.org/wiki/Dolby_Digital) zvuku (AC3) (je starší verze kodek).

## <a name="next-steps"></a>Další postup

[Nahrávání, kódování videa služby stream](stream-files-tutorial-with-api.md)
