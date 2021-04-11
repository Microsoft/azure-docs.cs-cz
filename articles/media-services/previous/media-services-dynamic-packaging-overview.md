---
title: Přehled dynamického balení Azure Media Services | Microsoft Docs
description: Tyto články poskytují přehled o Microsoft Azure Media Services dynamickém balení.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: d2394cba157a7049e3b63b09fda9e018f0a0b32f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103013614"
---
# <a name="dynamic-packaging"></a>Dynamické balení

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Vyberte verzi Media Services, kterou používáte:"]
> * [Verze 3](../latest/dynamic-packaging-overview.md)
> * [Verze 2](media-services-dynamic-packaging-overview.md)

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi [Media Services V3](../latest/index.yml). Podívejte se taky na [pokyny k migraci z v2 na V3](../latest/migrate-v-2-v-3-migration-introduction.md) .

Microsoft Azure Media Services lze použít k doručování mnoha mediálních formátů zdrojového souboru, formátů datových proudů médií a formátů ochrany obsahu na nejrůznější technologie klienta (například iOS, XBOX, Silverlight, Windows 8). Tito klienti rozumí různým protokolům, například iOS vyžaduje formát HTTP Live Streaming (HLS) v4 a Silverlight a Xbox vyžadují Smooth Streaming. Pokud máte sadu adaptivních přenosů (s více přenosovými rychlostmi) souborů MP4 (ISO Base Media 14496-12) nebo sadu adaptivní přenosové rychlosti Smooth Streaming soubory, které chcete sloužit klientům, kteří porozuměli formátu MPEG, HLS nebo Smooth Streaming, měli byste využít Media Services dynamické balení.

Díky dynamickému balení budete potřebovat vytvořit Asset, který obsahuje sadu souborů MP4 s adaptivní přenosovou rychlostí nebo adaptivní přenosové rychlosti Smooth Streaming souborů. Pak na základě zadaného formátu v manifestu nebo v žádosti o fragment, server streamování na vyžádání zajistí, že se datový proud dostane v protokolu, který jste zvolili. Díky tomu pak stačí uložit (a platit) soubory pouze v jednom úložném formátu a služba Media Services bude sestavovat a dodávat vhodný formát streamování v reakci na požadavky klientů.

Následující diagram znázorňuje tradiční kódování a statický pracovní postup balení.

![Statické kódování](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

Následující diagram znázorňuje pracovní postup dynamického balení.

![Dynamické kódování](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="common-scenario"></a>Běžný scénář

1. Nahrání vstupního souboru (označovaného jako soubor Mezzanine). Například H. 264, MP4 nebo WMV (pro seznam podporovaných formátů naleznete v tématu [formáty podporované Media Encoder Standard](media-services-media-encoder-standard-formats.md).
2. Zakódovat soubor Mezzanine do nastavení H. 264 MP4 s adaptivní přenosovou rychlostí.
3. Publikujte Asset, který obsahuje sadu MP4 s adaptivní přenosovou rychlostí vytvořením lokátoru na vyžádání.
4. Sestavujte adresy URL streamování pro přístup k obsahu a jeho streamování.

## <a name="preparing-assets-for-dynamic-streaming"></a>Příprava prostředků pro dynamické streamování

K přípravě assetu na dynamické streamování máte následující možnosti:

- [Nahrajte hlavní soubor](media-services-dotnet-upload-files.md).
- [Pomocí kodéru Media Encoder Standard vytvoříme sady s adaptivní přenosovou rychlostí H. 264 MP4](media-services-dotnet-encode-with-media-encoder-standard.md).
- [Streamování obsahu](media-services-deliver-content-overview.md)

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Zvukové kodeky podporované dynamickým balením

Dynamické balení podporuje soubory MP4, které obsahují zvuk zakódovaný pomocí formátu [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC V1, IT-AAC v2), [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus)(Enhanced AC-3 nebo E-AC3), Dolby ATMOS nebo [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, DTS LBR, DTS HD, DTS HD, DTS-bezeztráte). Streamování obsahu Dolby ATMOS se podporuje u standardů, jako je protokol MPEG-POMLČKa s formátem CSF (Common Streaming Format) nebo Common Media Application Format (CMAF) s fragmentací MP4 a prostřednictvím HTTP Live Streaming (HLS) s CMAF.

> [!NOTE]
> Dynamické balení nepodporuje soubory, které obsahují zvuk [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) (Jedná se o zastaralý kodek).

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
