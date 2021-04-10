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
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.reviewer: anilmur
ms.openlocfilehash: f0cf89a755090010bb80507c52f90baa2a4e804a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102617620"
---
# <a name="standard-encoder-formats-and-codecs"></a>Formáty a kodeky Standard Encoder

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Tento článek obsahuje seznam nejběžnějších formátů souborů pro import a export, které můžete použít se službou [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset). Informace o tom, jak vytvořit vlastní přednastavení pomocí **StandardEncoderPreset**, najdete v tématu [Vytvoření transformace s vlastní předvolbumi](customize-encoder-presets-how-to.md).

## <a name="input-containerfile-formats"></a>Vstupní formáty kontejneru nebo souborů

| Formáty souborů (přípony souborů) | Podporováno |
| --- | --- |
| FLV (s kodeky H.264 a AAC) (.flv) |Yes |
| MXF (.mxf) |Yes |
| GXF (.gxf) |Yes |
| MPEG2-PS, MPEG2-TS, 3GP (.ts, .ps, .3gp, .3gpp, .mpg) |Yes |
| Windows Media Video (WMV) a ASF (.wmv, .asf) |Yes |
| AVI (nekomprimovaný s 8bitovým nebo 10bitovým kódováním) (.avi) |Yes |
| MP4 (.mp4, .m4a, .m4v) a ISMV (.isma, .ismv) |Yes |
| [Microsoft Digital Video Recording (DVR-MS)](/previous-versions/windows/desktop/mstv/about-the-dvr-ms-file-format) (.dvr-ms) |Yes |
| Matroska a WebM (.mkv) |Yes |
| WAVE a WAV (.wav) |Yes |
| QuickTime (.mov) |Yes |

### <a name="audio-formats-in-input-containers"></a>Formáty zvuku ve vstupních kontejnerech

Kodér standard podporuje ve vstupních kontejnerech následující formáty zvuku:

* Soubory MXF, GXF a QuickTime, které obsahují zvukové stopy s prokládanými ukázkami stereo nebo 5,1

nebo

* Soubory MXF, GXF a QuickTime, kde se zvuk přenáší jako samostatné stopy PCM, ale mapování kanálů (na stereo nebo prostorový zvuk 5.1) je možné odvodit z metadat souboru

## <a name="input-video-codecs"></a>Vstupní kodeky videa
| Vstupní kodeky videa | Podporováno |
| --- | --- |
| AVC s 8bitovou nebo 10bitovou hloubkou až do vzorkování 4:2:2, včetně AVCIntra |8bitová hloubka, 4:2:0 a 4:2:2 |
| Avid DNxHD (v souborech MXF) |Yes |
| DVCPro a DVCProHD (v souborech MXF) |Yes |
| Digitální video (DV) (v souborech AVI) |Yes |
| JPEG 2000 |Yes |
| MPEG-2 (až do profilu 422 a vysoké úrovně; včetně variant, jako jsou XDCAM, XDCAM HD, XDCAM IMX, CableLabs® a D10) |Až do profilu 422 |
| MPEG-1 |Yes |
| VC-1 a WMV9 |Yes |
| Canopus HQ a HQX |No |
| MPEG-4 Part 2 |Yes |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Yes |
| Nekomprimovaný formát YUV420 nebo Mezzanine |Yes |
| Apple ProRes 422 |Yes |
| Apple ProRes 422 LT |Yes |
| Apple ProRes 422 HQ |Yes |
| Apple ProRes Proxy |Yes |
| Apple ProRes 4444 |Yes |
| Apple ProRes 4444 XQ |Yes |
| HEVC/H. 265| Hlavní profil|

## <a name="input-audio-codecs"></a>Vstupní zvukové kodeky
| Kodeky vstupního zvuku | Podporováno |
| --- | --- |
| AAC (AAC-LC, AAC-HE a AAC-HEv2; až do prostorového zvuku 5.1) |Yes |
| MPEG Layer 2 |Yes |
| MP3 (MPEG-1 Audio Layer 3) |Yes |
| Windows Media Audio |Yes |
| WAV a PCM |Yes |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Yes |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |Yes |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Yes |
| AMR (adaptivní s více rychlostmi) |Yes |
| AES (SMPTE 331M a 302M, AES3-2003) |No |
| Dolby® E |No |
| Dolby® Digital (AC3) |No |
| Dolby® Digital Plus (E-AC3) |No |

## <a name="output-formats-and-codecs"></a>Formáty výstupu a kodeky
V následující tabulce jsou uvedeny kodeky a formáty souborů, které jsou podporovány pro export.

| Formát souboru | Kodek videa | Zvukový kodek |
| --- | --- | --- |
| MP4 <br/><br/>(včetně kontejnerů MP4 s více přenosovými rychlostmi) |H. 264 (profily vysoké, hlavní a základní), HEVC (H. 265) 8 bitů |AAC-LC, HE-AAC V1, IT-AAC v2 |
| MPEG2 – TS |H. 264 (profily vysoké, hlavní a základní) |AAC-LC, HE-AAC V1, IT-AAC v2 |

## <a name="next-steps"></a>Další kroky

[Vytvoření transformace pomocí vlastní předvolby](customize-encoder-presets-how-to.md)
