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
ms.date: 02/22/2019
ms.author: juliako
ms.openlocfilehash: 02af95de3793f1d56204b17b0a3d91efbb285e55
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2019
ms.locfileid: "56726410"
---
# <a name="dynamic-packaging"></a>Dynamické balení

Microsoft Azure Media Services umožňuje doručovat mnoho média zdrojového formátů souborů, formáty, streamování médií a ochrana obsahu formáty do různých technologií klienta (třeba iOS a XBOX). Tito klienti pochopit různé protokoly, například iOS vyžaduje, že formát protokolu HTTP Live Streaming (HLS) a služby Xbox vyžadují, technologie Smooth Streaming. Pokud máte sadu s adaptivní přenosovou rychlostí (s více přenosovými rychlostmi) MP4 soubory (ISO Base médií 14496-12) nebo sadu s adaptivní přenosovou rychlostí soubory technologie Smooth Streaming, které mají sloužit ke klientům, kteří pochopit, HLS, MPEG DASH nebo Smooth Streaming, můžete využít výhod dynamického Balení. Balení je nezávislé na rozlišení, jsou podporovány SD/HD, High Density/UHD - 4 kB.

[Koncové body streamování](streaming-endpoint-concept.md) je služba dynamického balení ve službě Media Services používá k doručování multimediálního obsahu pro klientské přehrávače. Dynamické balení je funkce, která obsahuje standardní na všechny koncové body streamování (Standard nebo Premium). Neexistuje žádné další náklady spojené s touto funkcí v Media Services v3. Při dynamickém balení je vše, co potřebujete asset, který obsahuje sadu souborů MP4 s soubory manifestu obsahují. Potom založené na formátu určeného v manifestu nebo fragment požadavek, můžete datový proud obdrželi v protokolu, kterou jste zvolili. Díky tomu pak stačí uložit (a platit) soubory pouze v jednom úložném formátu a služba Media Services bude sestavovat a dodávat vhodný formát streamování v reakci na požadavky klientů.

Ve službě Media Services se používá dynamické balení, zda jsou streamování na vyžádání nebo Live.

Následující diagram znázorňuje dynamické balení pracovního postupu.

![Dynamické kódování](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

## <a name="common-video-on-demand-workflow"></a>Běžné pracovní postup videa na vyžádání

Toto je běžné Media Services pracovní postup streamování, ve kterém se používá dynamické balení.

1. Nahrání vstupního souboru (označované jako soubor mezzanine). Například H.264 MP4 nebo WMV (seznam podporovaných formátů naleznete v části [formátů podporovaných Media Encoder Standard](media-encoder-standard-formats.md).
2. Zakódujte váš soubor mezzanine do sady H.264 MP4 s adaptivní přenosovou rychlostí.
3. Publikujte asset, který obsahuje s adaptivní přenosovou sady souborů MP4.
4. Vytvoření adres URL, které cílí různé formáty (HLS, Dash nebo Smooth Streaming). Koncový bod streamování by zajistíme obsluhující správný manifest a žádostí o těchto různých formátech.
 
## <a name="video-codecs-supported-by-dynamic-packaging"></a>Video kodeků podporuje dynamické balení

Dynamické balení podporuje soubory MP4, které obsahují video zakódovány [H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (AVC MPEG-4 nebo AVC1), [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC hev1 nebo hvc1).

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Zvukových kodeků podporuje dynamické balení

Dynamické balení podporuje soubory MP4, které obsahují zvuk zakódovány [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, v2 HE-AAC), [Dolby digitální Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (rozšířené AC-3 nebo E AC3), nebo [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, DTS LBR, DTS HD, High Density, DTS HD, High Density beze ztrát).

> [!NOTE]
> Dynamické balení nepodporuje soubory, které obsahují [Dolby digitální](https://en.wikipedia.org/wiki/Dolby_Digital) zvuku (AC3) (je starší verze kodek).

## <a name="next-steps"></a>Další postup

[Nahrávání, kódování videa služby stream](stream-files-tutorial-with-api.md)

