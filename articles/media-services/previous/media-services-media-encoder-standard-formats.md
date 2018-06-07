---
title: Media Encoder Standard formáty a kodeky
description: Toto téma poskytuje přehled Media Encoder Standard formáty a kodeky.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: f334b1ce-2f56-4968-a019-f0a2b0016d9f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: juliako;anilmur
ms.openlocfilehash: 181a1b8ad6403045264ddc0bd502273f36df3eff
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34638326"
---
# <a name="media-encoder-standard-formats-and-codecs"></a>Kodeky a standardní formáty kodéru médií
Tento dokument obsahuje seznam nejčastějších import a export formáty souborů, které může používat pro Media Encoder Standard.

## <a name="input-containerfile-formats"></a>Zadejte kontejner nebo formátů
| Formáty souborů (přípony souborů) | Podporováno |
| --- | --- | --- | --- |
| FLV (kodeky H.264 a AAC) (.flv) |Ano |
| MXF (.mxf) |Ano |
| GXF (.gxf) |Ano |
| PS MPEG2, MPEG2-TS 3GP (.ts, PS, .3gp, .3gpp, MPG) |Ano |
| Windows Media Video (WMV) / amp (.wmv, ASF) |Ano |
| AVI (nekomprimované 8bitové/10 verze) (AVI) |Ano |
| MP4 (MP4, .m4a, .m4v) nebo ISMV (.isma, .ismv) |Ano |
| [Microsoft digitální Video Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (dvr ms) |Ano |
| Matroska/WBEM (.mkv) |Ano |
| WAVE/WAV (WAV) |Ano |
| QuickTime (MOV) |Ano |

> [!NOTE]
> Vyšší je seznam přípon běžně došlo k souboru. Media Encoder Standard podporuje mnoho dalších (například: .m2ts, .mpeg2video a Content). Pokud se pokusíte kódování souboru a zobrazí chybové hlášení o formátu, který není podporován, zadejte svůj názor [zde](https://feedback.azure.com/forums/169396-media-services/category/144411-encoding-and-processing/).
> 
> 

### <a name="audio-formats-in-input-containers"></a>Formáty zvuku ve vstupní kontejnery
Media Encoder Standard podporuje, provedení následujících formátů zvuk v kontejnerech vstupní:

* MXF, GXF a QuickTime soubory, které mají zvukových stop s prokládaná stereofonním systémem nebo 5.1 ukázky

nebo

* Soubory MXF, GXF a QuickTime kde zvukovém souboru se provádí jako samostatné stopy PCM ale mapování kanál (na stereofonním systémem nebo 5.1) lze odvodit z metadata souboru

V blízké budoucnosti bude poskytována podpora pro explicitní nebo uživatelem zadané kanál mapování.

## <a name="input-video-codecs"></a>Vstupní video kodeky
| Vstupní video kodeky | Podporováno |
| --- | --- | --- | --- |
| AVC 8-bit nebo 10-bit, až 4:2:2, včetně AVCIntra |8 bitů 4:2:0, 4:2:2 |
| Avid DNxHD (v MXF) |Ano |
| DVCPro/DVCProHD (v MXF) |Ano |
| Digitální video (DV) (v souborech souborů AVI) |Ano |
| JPEG 2000 |Ano |
| MPEG-2 (až 422 profil a vysokou úroveň, včetně například XDCAM, XDCAM HD, XDCAM IMX, CableLabs® a D10 variant) |Až 422 profilu |
| MPEG-1 |Ano |
| VC-1 NEBO WMV9 |Ano |
| Canopus Ústředí/HQX |Ne |
| MPEG-4 část 2 |Ano |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Ano |
| YUV420 nekomprimovaným nebo soubor mezzanine |Ano |
| Apple ProRes 422 |Ano |
| Apple ProRes 422 LT |Ano |
| Ústředí ProRes 422 Apple |Ano |
| Apple ProRes Proxy |Ano |
| Apple ProRes 4444 |Ano |
| Apple ProRes 4444 XQ |Ano |
| HEVC/H.265| Hlavní profilu|

## <a name="input-audio-codecs"></a>Vstupní zvukových kodeků
| Vstupní zvukových kodeků | Podporováno |
| --- | --- | --- | --- |
| AAC (AAC-LC, AAC HE a AAC-HEv2; až 5.1) |Ano |
| MPEG vrstvy 2 |Ano |
| MP3 (MPEG-1 zvuk vrstvy 3) |Ano |
| Zvuk média systému Windows |Ano |
| WAV NEBO PCM |Ano |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Ano |
| [Díle](http://go.microsoft.com/fwlink/?LinkId=822667) |Ano |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Ano |
| AMR (adaptivní více rychlost) |Ano |
| AES (SMPTE 331 M a 302 M, AES3-2003) |Ne |
| Dolby® E |Ne |
| Digitální Dolby® (AC3) |Ne |
| Digitální Dolby® Plus (E-AC3) |Ne |

## <a name="output-formats-and-codecs"></a>Formáty výstup a kodeky
Následující tabulka uvádí formáty kodeky a souborů, které jsou podporovány pro export.

| Formát souboru | Kodek videa | Zvukových kodeků |
| --- | --- | --- |
| MP4 <br/><br/>(včetně kontejnery více přenosovými rychlostmi MP4) |H.264 (vysoká, hlavní a profily standardních hodnot) |AAC-LC, HE-AAC v1, HE-AAC v2 |
| MPEG2 TS |H.264 (vysoká, hlavní a profily standardních hodnot) |AAC-LC, HE-AAC v1, HE-AAC v2 |

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Další informace najdete v tématech
[Kódování obsahu na vyžádání pomocí služby Azure Media Services](media-services-encode-asset.md)

[Postup Kódovat pomocí kodéru Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

