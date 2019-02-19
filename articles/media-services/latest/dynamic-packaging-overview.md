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
ms.date: 02/17/2019
ms.author: juliako
ms.openlocfilehash: 21b2d9bdef5e6974126c78b1005bd4786182c881
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2019
ms.locfileid: "56405704"
---
# <a name="dynamic-packaging"></a>Dynamické balení

Ochrana obsahu formáty pro širokou škálu technologií klienta a Microsoft Azure Media Services umožňuje doručovat mnoho média zdrojového formátů souborů, formáty, streamování médií (třeba s Iosem, XBOX, Silverlight, Windows 8). Tito klienti pochopit různé protokoly, například iOS vyžaduje formátu V4 HTTP Live Streaming (HLS) a Silverlight a Xbox vyžadují, technologie Smooth Streaming. Pokud máte sadu s adaptivní přenosovou rychlostí (s více přenosovými rychlostmi) MP4 soubory (ISO Base médií 14496-12) nebo sadu s adaptivní přenosovou rychlostí soubory technologie Smooth Streaming, které mají sloužit ke klientům, kteří pochopit MPEG DASH, HLS nebo technologie Smooth Streaming, by měl využít výhod média Dynamické balení služby.

Dynamické balení, všechny, je nutné je pouze vytvořit asset, který obsahuje sadu souborů MP4 nebo soubory technologie Smooth Streaming s adaptivní přenosovou rychlostí. Potom založené na formátu určeného v manifestu nebo fragment požadavek On-Demand Streaming serveru zajistí datový proud obdrželi v protokolu, kterou jste zvolili. Díky tomu pak stačí uložit (a platit) soubory pouze v jednom úložném formátu a služba Media Services bude sestavovat a dodávat vhodný formát streamování v reakci na požadavky klientů.

Následující diagram znázorňuje tradiční kódování a statické balení pracovního postupu.

![Statické kódování](./media/dynamic-packaging-overview/media-services-static-packaging.png)

Následující diagram znázorňuje dynamické balení pracovního postupu.

![Dynamické kódování](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="common-scenario"></a>Běžný scénář

1. Nahrání vstupního souboru (označované jako soubor mezzanine). Například H.264 MP4 nebo WMV (seznam podporovaných formátů naleznete v části [formátů podporovaných Media Encoder Standard](media-encoder-standard-formats.md).
2. Zakódujte váš soubor mezzanine do sady H.264 MP4 s adaptivní přenosovou rychlostí.
3. Publikujte asset, který obsahuje s adaptivní přenosovou sady souborů MP4.
4. Vytvoření datových proudů adres URL pro přístup k a streamování vašeho obsahu.

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Zvukových kodeků podporuje dynamické balení

Dynamické balení podporuje soubory MP4 (nebo Smooth Streaming soubory), které obsahují zvuk kódovaný pomocí [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, v2 HE-AAC), [Dolby digitální Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (rozšířené AC-3 nebo E AC3), nebo [ DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, DTS LBR, DTS HD, High Density, DTS HD, High Density beze ztrát).

> [!Note]
> Dynamické balení nepodporuje soubory, které obsahují [Dolby digitální](https://en.wikipedia.org/wiki/Dolby_Digital) zvuku (AC3) (je starší verze kodek).

## <a name="next-steps"></a>Další postup

[Nahrávání, kódování videa služby stream](stream-files-tutorial-with-api.md)
