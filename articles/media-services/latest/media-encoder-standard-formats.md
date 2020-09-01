---
title: Formáty a kodeky Standard Encoder – Azure
description: Tento článek obsahuje seznam nejběžnějších formátů souborů pro import a export, které můžete použít se službou StandardEncoderPreset.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.reviewer: anilmur
ms.openlocfilehash: c8c111d046bfaefac11df576e455162d20acecd0
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2020
ms.locfileid: "89266557"
---
# <a name="standard-encoder-formats-and-codecs"></a>Formáty a kodeky Standard Encoder

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Tento článek obsahuje seznam nejběžnějších formátů souborů pro import a export, které můžete použít se službou [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset). Informace o tom, jak vytvořit vlastní přednastavení pomocí **StandardEncoderPreset**, najdete v tématu [Vytvoření transformace s vlastní předvolbumi](customize-encoder-presets-how-to.md).

## <a name="input-containerfile-formats"></a>Vstupní formáty kontejneru nebo souborů

| Formáty souborů (přípony souborů) | Podporováno |
| --- | --- |
| FLV (s kodeky H.264 a AAC) (.flv) |Ano |
| MXF (.mxf) |Ano |
| GXF (.gxf) |Ano |
| MPEG2-PS, MPEG2-TS, 3GP (.ts, .ps, .3gp, .3gpp, .mpg) |Ano |
| Windows Media Video (WMV) a ASF (.wmv, .asf) |Ano |
| AVI (nekomprimovaný s 8bitovým nebo 10bitovým kódováním) (.avi) |Ano |
| MP4 (.mp4, .m4a, .m4v) a ISMV (.isma, .ismv) |Ano |
| [Microsoft Digital Video Recording (DVR-MS)](/previous-versions/windows/desktop/mstv/about-the-dvr-ms-file-format) (.dvr-ms) |Ano |
| Matroska a WebM (.mkv) |Ano |
| WAVE a WAV (.wav) |Ano |
| QuickTime (.mov) |Ano |

### <a name="audio-formats-in-input-containers"></a>Formáty zvuku ve vstupních kontejnerech

Kodér standard podporuje ve vstupních kontejnerech následující formáty zvuku:

* Soubory MXF, GXF a QuickTime, které obsahují zvukové stopy s prokládanými ukázkami stereo nebo 5,1

nebo

* Soubory MXF, GXF a QuickTime, kde se zvuk přenáší jako samostatné stopy PCM, ale mapování kanálů (na stereo nebo prostorový zvuk 5.1) je možné odvodit z metadat souboru

## <a name="input-video-codecs"></a>Vstupní kodeky videa
| Vstupní kodeky videa | Podporováno |
| --- | --- |
| AVC s 8bitovou nebo 10bitovou hloubkou až do vzorkování 4:2:2, včetně AVCIntra |8bitová hloubka, 4:2:0 a 4:2:2 |
| Avid DNxHD (v souborech MXF) |Ano |
| DVCPro a DVCProHD (v souborech MXF) |Ano |
| Digitální video (DV) (v souborech AVI) |Ano |
| JPEG 2000 |Ano |
| MPEG-2 (až do profilu 422 a vysoké úrovně; včetně variant, jako jsou XDCAM, XDCAM HD, XDCAM IMX, CableLabs® a D10) |Až do profilu 422 |
| MPEG-1 |Ano |
| VC-1 a WMV9 |Ano |
| Canopus HQ a HQX |Ne |
| MPEG-4 Part 2 |Ano |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Ano |
| Nekomprimovaný formát YUV420 nebo Mezzanine |Ano |
| Apple ProRes 422 |Ano |
| Apple ProRes 422 LT |Ano |
| Apple ProRes 422 HQ |Ano |
| Apple ProRes Proxy |Ano |
| Apple ProRes 4444 |Ano |
| Apple ProRes 4444 XQ |Ano |
| HEVC/H. 265| Hlavní profil|

## <a name="input-audio-codecs"></a>Vstupní zvukové kodeky
| Kodeky vstupního zvuku | Podporováno |
| --- | --- |
| AAC (AAC-LC, AAC-HE a AAC-HEv2; až do prostorového zvuku 5.1) |Ano |
| MPEG Layer 2 |Ano |
| MP3 (MPEG-1 Audio Layer 3) |Ano |
| Windows Media Audio |Ano |
| WAV a PCM |Ano |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Ano |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |Ano |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Ano |
| AMR (adaptivní s více rychlostmi) |Ano |
| AES (SMPTE 331M a 302M, AES3-2003) |Ne |
| Dolby® E |Ne |
| Dolby® Digital (AC3) |Ne |
| Dolby® Digital Plus (E-AC3) |Ne |

## <a name="output-formats-and-codecs"></a>Formáty výstupu a kodeky
V následující tabulce jsou uvedeny kodeky a formáty souborů, které jsou podporovány pro export.

| Formát souboru | Kodek videa | Zvukový kodek |
| --- | --- | --- |
| MP4 <br/><br/>(včetně kontejnerů MP4 s více přenosovými rychlostmi) |H. 264 (profily vysoké, hlavní a základní) |AAC-LC, HE-AAC V1, IT-AAC v2 |
| MPEG2 – TS |H. 264 (profily vysoké, hlavní a základní) |AAC-LC, HE-AAC V1, IT-AAC v2 |

## <a name="next-steps"></a>Další kroky

[Vytvoření transformace pomocí vlastní předvolby](customize-encoder-presets-how-to.md)
