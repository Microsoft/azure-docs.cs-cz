---
title: Kodeky a formáty Media Encoderu Standard
description: Toto téma obsahuje přehled kodéru Media Encoder Standard formáty a kodeky ve službě.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: f334b1ce-2f56-4968-a019-f0a2b0016d9f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: juliako;anilmur
ms.openlocfilehash: ef74ebfab9450d0aa22b5a2f3287491d96573cff
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50249165"
---
# <a name="media-encoder-standard-formats-and-codecs"></a>Kodeky a standardní formáty kodéru médií
Tento dokument obsahuje seznam nejběžnějších import a export formátů souborů, které můžete použít pomocí kodéru Media Encoder Standard.

## <a name="input-containerfile-formats"></a>Vstupní formáty kontejneru nebo souboru
| Formáty souborů (přípony souborů) | Podporováno |
| --- | --- | --- | --- |
| FLV (s kodeky H.264 a AAC) (.flv) |Ano |
| MXF (.mxf) |Ano |
| GXF (.gxf) |Ano |
| 3GP MPEG2-PS, MPEG2-TS (.ts, PS, .3gp, .3gpp, MPG) |Ano |
| Windows Media Video (WMV) a ASF (.wmv, .asf) |Ano |
| AVI (nekomprimovaný 8 bitů nebo 10bitovým) (.avi) |Ano |
| MP4 (.mp4, .m4a, .m4v) a ISMV (.isma, .ismv) |Ano |
| [Microsoft Digital Video Recording](https://msdn.microsoft.com/library/windows/desktop/dd692984) (dvr ms) |Ano |
| Matroska a WebM (.mkv) |Ano |
| WAVE a WAV (.wav) |Ano |
| QuickTime (QuickTime) |Ano |

> [!NOTE]
> Výše uvedený seznam běžnější přípony. Media Encoder Standard podporuje řadu dalších (například: .m2ts, .mpeg2video, .qt). Pokud se pokusíte kódování souboru a získat chybovou zprávu o formát není podporován, poskytněte zpětnou vazbu [tady](https://feedback.azure.com/forums/169396-media-services/category/144411-encoding-and-processing/).
> 
> 

### <a name="audio-formats-in-input-containers"></a>Formáty zvuku ve vstupních kontejnerech
Media Encoder Standard podporuje následující formáty zvuku ve vstupních kontejnerech:

* Soubory MXF, GXF a QuickTime, které mají zvukové stopy s prokládané stereo nebo 5.1 ukázky

nebo

* Soubory MXF, GXF a QuickTime, kde se zvuk přenáší jako samostatné stopy PCM, ale mapování kanálů (na stereo nebo 5.1) je možné odvodit z metadat souboru

Pro explicitní nebo uživatelem zadaný kanál mapování poskytneme vám podporu v blízké budoucnosti.

## <a name="input-video-codecs"></a>Kodeky vstupního videa
| Kodeky vstupního videa | Podporováno |
| --- | --- | --- | --- |
| AVC 8-bit/10-bit, až 4:2:2, včetně AVCIntra |8bitová hloubka, 4:2:0 a 4:2:2 |
| Avid DNxHD (v souborech MXF) |Ano |
| DVCPro a DVCProHD (v souborech MXF) |Ano |
| Digitální video (DV) (v souborech AVI) |Ano |
| JPEG 2000 |Ano |
| MPEG-2 (až do profilu 422 a vysoké úrovně; včetně variant, jako jsou XDCAM, XDCAM HD, XDCAM IMX, CableLabs® a D10) |Až do profilu 422 |
| MPEG-1 |Ano |
| VC-1/WMV9 |Ano |
| Canopus HQ a HQX |Ne |
| 2. část MPEG-4 |Ano |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Ano |
| Nekomprimovaný formát YUV420 nebo mezzanine |Ano |
| Apple ProRes 422 |Ano |
| Apple ProRes 422 LT |Ano |
| Sídel ProRes 422 Apple |Ano |
| Apple ProRes Proxy |Ano |
| Apple ProRes 4444 |Ano |
| Apple ProRes 4444 XQ |Ano |
| HEVC/H.265| Hlavní profil|

## <a name="input-audio-codecs"></a>Kodeky vstupního zvuku
| Kodeky vstupního zvuku | Podporováno |
| --- | --- | --- | --- |
| AAC (AAC-LC, AAC-HE a AAC-HEv2; až 5.1) |Ano |
| MPEG vrstvy 2 |Ano |
| MP3 (zvuk vrstvu MPEG-1 3) |Ano |
| Windows Media zvuku |Ano |
| WAV/PCM |Ano |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Ano |
| [Díle](http://go.microsoft.com/fwlink/?LinkId=822667) |Ano |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Ano |
| AMR (Adaptivní s více rychlost) |Ano |
| AES (SMPTE 331 M a 302 M, AES3-2003) |Ne |
| Dolby® E |Ne |
| Digitální Dolby® (AC3) |Ne |
| Digitální Dolby® Plus (E-AC3) |Ne |

## <a name="output-formats-and-codecs"></a>Kodeky a formáty výstupu
V následující tabulce jsou uvedeny kodeky a soubor formáty, které jsou podporovány pro export.

| Formát souboru | Kodek videa | Zvukový kodek |
| --- | --- | --- |
| MP4 <br/><br/>(včetně kontejnerů MP4 s více přenosovými rychlostmi) |H.264 (vysoká, hlavní a standardních hodnot profily) |AAC-LC, HE-AAC v1, v2 HE-AAC |
| MPEG2-TS |H.264 (vysoká, hlavní a standardních hodnot profily) |AAC-LC, HE-AAC v1, v2 HE-AAC |

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Další informace najdete v tématech
[Kódování obsahu na vyžádání pomocí Azure Media Services](media-services-encode-asset.md)

[Kódování pomocí Media Encoderu Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

