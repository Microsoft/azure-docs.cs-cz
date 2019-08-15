---
title: Formáty a kodeky Media Encoder Standard – Azure
description: Toto téma poskytuje přehled formátů Media Encoder Standard a kodeků.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: 8fd68269f73506c2d605c0cb4560e6a57e1a2128
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "69016538"
---
# <a name="media-encoder-standard-formats-and-codecs"></a>Kodeky a standardní formáty kodéru médií

> [!div class="op_single_selector" title1="Vyberte verzi Media Services, kterou používáte:"]
> * [Verze 2](media-services-media-encoder-standard-formats.md)
> * [Verze 3](../latest/media-encoder-standard-formats.md)

Tento dokument obsahuje seznam nejběžnějších formátů souborů pro import a export, které můžete použít s Media Encoder Standard.

## <a name="input-containerfile-formats"></a>Vstupní formáty kontejneru nebo souborů
| Formáty souborů (přípony souborů) | Podporováno |
| --- | --- |
| FLV (s kodeky H. 264 a AAC) (. flv) |Ano |
| MXF    (.mxf) |Ano |
| GXF (. GXF) |Ano |
| MPEG2-PS, MPEG2-TS, 3GP (. TS,. PS,. 3gp,. 3GPP,. mpg) |Ano |
| Windows Media Video (WMV)/ASF (. wmv,. asf) |Ano |
| AVI (nekomprimovaný 8bit/10bit) (. avi) |Ano |
| MP4 (. mp4,. m4a,. m4v)/ISMV (. ISMA,. ISMV) |Ano |
| [Microsoft Digital Video Recording (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (. DVR-MS) |Ano |
| Matroska/WebM (. MKV) |Ano |
| WAVE/WAV (. wav) |Ano |
| QuickTime (. mov) |Ano |

> [!NOTE]
> Výše je uveden seznam častěji používaných přípon souborů. Media Encoder Standard podporuje mnoho dalších (například:. m2ts,. mpeg2video,. QT). Pokud se pokusíte soubor zakódovat a zobrazí se chybová zpráva s informacemi o tom, že formát není podporován, zadejte [](https://feedback.azure.com/forums/169396-media-services/category/144411-encoding-and-processing/)svou zpětnou vazbu.
> 
> 

### <a name="audio-formats-in-input-containers"></a>Formáty zvuku ve vstupních kontejnerech
Media Encoder Standard podporuje překonání následujících formátů zvuku ve vstupních kontejnerech:

* Soubory MXF, GXF a QuickTime, které obsahují zvukové stopy s prokládanými ukázkami stereo nebo 5,1

or

* Soubory MXF, GXF a QuickTime, kde se zvuk přenese jako samostatná stopa PCM, ale mapování kanálu (na stereo nebo 5,1) se dá odvodit z metadat souboru.

## <a name="input-video-codecs"></a>Vstupní kodeky videa
| Vstupní kodeky videa | Podporováno |
| --- | --- |
| AVC 8-bit/10 bitů, až 4:2:2, včetně AVCIntra |8 bitů 4:2:0 a 4:2:2 |
| Avid DNxHD (v MXF) |Ano |
| DVCPro/DVCProHD (v MXF) |Ano |
| Digitální video (DV) (v souborech AVI) |Ano |
| JPEG 2000 |Ano |
| MPEG-2 (až 422 profilu a vysoké úrovně; včetně variant, jako je XDCAM, XDCAM HD, XDCAM IMX, CableLabs® a D10) |Až 422 profil |
| MPEG-1 |Ano |
| VC-1/WMV9 |Ano |
| Canopus sídel/HQX |Ne |
| MPEG-4 část 2 |Ano |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Ano |
| YUV420 uncompressed nebo Mezzanine |Ano |
| Apple ProRes 422 |Ano |
| Apple ProRes 422 LT |Ano |
| Apple ProRes 422 sídel |Ano |
| Proxy server Apple ProRes |Ano |
| Apple ProRes 4444 |Ano |
| Apple ProRes 4444 XQ |Ano |
| HEVC/H. 265| Hlavní a hlavní profily 10&#42;()<br/>Hlavní Podpora profilů pro 10 je určená pro obsah 8bit 4:2:0. |

## <a name="input-audio-codecs"></a>Vstupní zvukové kodeky
| Vstupní zvukové kodeky | Podporováno |
| --- | --- |
| AAC (AAC-LC, AAC-HE a AAC-HEv2; až do 5,1) |Ano |
| MPEG – vrstva 2 |Ano |
| MP3 (zvuková vrstva MPEG-1 3) |Ano |
| Windows Media Audio |Ano |
| WAV/PCM |Ano |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Ano |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |Ano |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Ano |
| AMR (adaptivní s více sazbami) |Ano |
| AES (SMPTE 331M a 302M, AES3-2003) |Ne |
| Dolby® E |Ne |
| Dolby® Digital (AC3) |Ne |
| Dolby® Digital Plus (E-AC3) |Ne |

## <a name="output-formats-and-codecs"></a>Formáty výstupu a kodeky
V následující tabulce jsou uvedeny kodeky a formáty souborů, které jsou podporovány pro export.

| Formát souboru | Kodek videa | Zvukový kodek |
| --- | --- | --- |
| MP4 <br/><br/>(včetně kontejnerů MP4 s více přenosovými rychlostmi) |H. 264 (profily vysoké, hlavní a základní) |AAC-LC, HE-AAC v1, HE-AAC v2 |
| MPEG2-TS |H. 264 (profily vysoké, hlavní a základní) |AAC-LC, HE-AAC v1, HE-AAC v2 |

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Viz také:
[Kódování obsahu na vyžádání pomocí Azure Media Services](media-services-encode-asset.md)

[Postup při kódování pomocí Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

