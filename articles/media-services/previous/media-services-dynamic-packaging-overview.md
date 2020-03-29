---
title: Přehled dynamických obalů Azure Media Services | Dokumenty společnosti Microsoft
description: Tento článek poskytuje přehled dynamického balení Microsoft Azure Media Services.
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
ms.date: 03/21/2019
ms.author: juliako
ms.openlocfilehash: 079094965775c140c0343da98e40fd008995d45a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74901188"
---
# <a name="dynamic-packaging"></a>Dynamické balení

> [!div class="op_single_selector" title1="Vyberte verzi služby Media Services, kterou používáte:"]
> * [Verze 3](../latest/dynamic-packaging-overview.md)
> * [Verze 2](media-services-dynamic-packaging-overview.md)

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Viz také [pokyny k migraci z v2 na v3](../latest/migrate-from-v2-to-v3.md)

Mediální služby Microsoft Azure lze použít k doručování mnoha formátů souborů zdroje médií, formátů datových proudů médií a formátů ochrany obsahu do různých klientských technologií (například iOS, XBOX, Silverlight, Windows 8). Tito klienti rozumí různým protokolům, například iOS vyžaduje formát V4 (HTTP Live Streaming) a Silverlight a Xbox vyžadují plynulé streamování. Pokud máte sadu souborů mp4 (MULTI-bitrate) MP4 (ISO Base Media 14496-12) nebo sadu adaptivních datových toků Smooth Streaming souborů, které chcete sloužit klientům, kteří rozumí MPEG DASH, HLS nebo Smooth Streaming, měli byste využít média Služby dynamické balení.

S dynamickým balením vše, co potřebujete, je vytvořit datový zdroj, který obsahuje sadu adaptivní datový chod MP4 souborů nebo adaptivní datový tok Smooth Streaming soubory. Potom na základě zadaného formátu v požadavku manifestu nebo fragmentu server streamování na vyžádání zajistí, že obdržíte datový proud v protokolu, který jste zvolili. Díky tomu pak stačí uložit (a platit) soubory pouze v jednom úložném formátu a služba Media Services bude sestavovat a dodávat vhodný formát streamování v reakci na požadavky klientů.

Následující diagram znázorňuje tradiční kódování a statické balení workflow.

![Statické kódování](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

Následující diagram znázorňuje pracovní postup dynamického balení.

![Dynamické kódování](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="common-scenario"></a>Běžný scénář

1. Nahrajte vstupní soubor (nazývaný mezipatrový soubor). Například H.264, MP4 nebo WMV (seznam podporovaných formátů viz [Formáty podporované standardem kodéru médií](media-services-media-encoder-standard-formats.md).
2. Zakódujte svůj mezaninový soubor do adaptivních datových tokových sad H.264 MP4.
3. Publikujte datový zdroj, který obsahuje sadu MP4 s adaptivní přenosovou rychlostí, vytvořením lokátoru na vyžádání.
4. Vytvářejte adresy URL datových proudů pro přístup k obsahu a jejich streamování.

## <a name="preparing-assets-for-dynamic-streaming"></a>Příprava datových zdrojů pro dynamické streamování

Chcete-li připravit datový zdroj na dynamické streamování, máte následující možnosti:

- [Nahrajte hlavní soubor](media-services-dotnet-upload-files.md).
- [Pomocí standardního kodéru média můžete vyrábět sady adaptivního přenosového rychlosti H.264 MP4](media-services-dotnet-encode-with-media-encoder-standard.md).
- [Streamujte svůj obsah](media-services-deliver-content-overview.md).

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Zvukové kodeky podporované dynamickým balením

Dynamické balení podporuje soubory MP4, které obsahují zvuk kódovaný [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, HE-AAC v2), [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus)(Enhanced AC-3 nebo E-AC3), Dolby Atmos nebo [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, DTS LBR, DTS HD, DTS HD Lossless). Streamování obsahu Dolby Atmos je podporováno pro standardy, jako je protokol MPEG-DASH s fragmentovaným mp4 Common Streaming Format (CSF) nebo Common Media Application Format (CMAF), a prostřednictvím HTTP Live Streaming (HLS) s CMAF.

> [!NOTE]
> Dynamické balení nepodporuje soubory, které obsahují zvuk [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) (jedná se o starší kodek).

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

