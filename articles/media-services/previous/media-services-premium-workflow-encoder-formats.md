---
title: Formáty Media Encoder Premium pracovního postupu a kodeky | Microsoft Docs
description: Toto téma poskytuje přehled Media Encoder Premium pracovního postupu formáty formáty a kodeky
services: media-services
documentationcenter: ''
author: juliako
manager: erik43
editor: ''
ms.assetid: b197fce8-3b9b-4189-8d08-486810c0426f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako;anilmur
ms.openlocfilehash: 68ba76ee18b3acc1c2825a217641c21a799132c1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34638584"
---
# <a name="media-encoder-premium-workflow-formats-and-codecs"></a>Formáty Media Encoder Premium pracovního postupu a kodeky
> [!NOTE]
> Premium kodér otázky, e-mailu mepd@microsoft.com.
> 
> Procesor médií Media Encoder Premium pracovního postupu popsané v tomto tématu není k dispozici v Číně. 
> 
> 

Tento dokument obsahuje seznam formáty vstupních a výstupních souborů a kodeků, které jsou podporovány ve verzi public preview verzi **Media Encoder Premium pracovního postupu** kodér.

[Media Encoder Premium Worflow vstup formáty a kodeky](#input_formats)

[Media Encoder Premium Worflow výstupní formáty a kodeky](#output_formats)

**Pracovní postup Premium Media Encoder** podporuje titulky popsané v [to](#closed_captioning) části. 

## <a id="input_formats"></a>Pracovní postup Premium Media Encoder vstupní formáty a kodeky
V následující části jsou uvedeny kodeky a soubor formátů, které podporuje tento procesor médií jako vstup.

### <a name="input-containerfile-formats"></a>Zadejte kontejner nebo formátů
* Adobe® Flash® F4V
* MXF/SMPTE 377M
* GXF
* Datové proudy MPEG-2 Transport
* Datové proudy MPEG-2 programu
* MPEG-4/MP4
* Windows Media/amp
* AVI (nekomprimované 8bitové/10 bitů)

### <a name="input-video-codecs"></a>Vstupní Video kodeky
* AVC 8-bit nebo 10-bit, až 4:2:2, včetně AVCIntra
* Avid DNxHD (v MXF)
* DVCPro/DVCProHD (v MXF)
* HEVC/H.265, hlavní a hlavní 10 profil
* JPEG2000
* MPEG-2 (až 422 profil a vysokou úroveň, včetně například XDCAM, XDCAM HD, XDCAM IMX, CableLabs® a D10 variant)
* MPEG-1
* Windows Media Video/VC-1

### <a name="input-audio-codecs"></a>Vstupní zvukových kodeků
* AES (SMPTE 331 M a 302 M, AES3-2003)
* Dolby® E
* Digitální Dolby® (AC3)
* AAC (AAC-LC, AAC HE a AAC-HEv2; až 5.1)
* MPEG vrstvy 2
* MP3 (MPEG-1 zvuk vrstvy 3)
* Zvuk média systému Windows
* WAV NEBO PCM

## <a id="output_format"></a>Media Encoder Premium pracovního postupu výstupní formáty a kodeky
V následující části jsou uvedeny formáty kodeky a souborů, které jsou podporované jako výstup z tohoto média procesoru.

### <a name="output-containerfile-formats"></a>Výstup kontejneru nebo formátů
* Adobe® Flash® F4V
* MXF (OP1a, XDCAM a AS02)
* DPP (včetně AS11)
* GXF
* MPEG-4/MP4
* Windows Media/amp
* AVI (nekomprimované 8bitové/10 bitů)
* Formát souborů Smooth datového proudu (PIFF 1.3)
* MPEG-TS 

### <a name="output-video-codecs"></a>Výstup kodeky videa
* AVC (H.264; 8bitové; až profil vysokou úroveň 5.2; 4 kB Ultra HD; Uvnitř AVC)
* Avid DNxHD (v MXF)
* DVCPro/DVCProHD (v MXF)
* MPEG-2 (až 422 profil a vysokou úroveň, včetně například XDCAM, XDCAM HD, XDCAM IMX, CableLabs® a D10 variant)
* MPEG-1
* Windows Media Video/VC-1
* Vytváření miniatur JPEG

### <a name="output-audio-codecs"></a>Výstup zvukových kodeků
* AES (SMPTE 331 M a 302 M, AES3-2003)
* Digitální Dolby® (AC3)
* Dolby® Digital Plus (E-AC3) až 7.1
* AAC (AAC-LC, AAC HE a AAC-HEv2; až 5.1)
* MPEG vrstvy 2
* MP3 (MPEG-1 zvuk vrstvy 3)
* Zvuk média systému Windows

>[!NOTE]
>Pokud jste dekódovat digitální® Dolby (AC3), výstup lze zapsat pouze do souboru ISO MP4.

## <a id="closed_captioning"></a>Podpora pro titulků
Na ingestování, **Media Encoder Premium pracovního postupu** podporuje:

1. Soubory SCC
2. Soubory SMPTE TT
3. CEA-608/CEA-708 – provádí jako uživatelská data (SEI zpráv H.264 základní datové proudy, ATSC/53, SCTE20) nebo provádět jako pomocnou dat v souborech MXF/GXF
4. Soubory subtitle STL

Na výstupu jsou k dispozici následující možnosti:

1. CEA 608 k CEA 708 překlad
2. CEA-608/CEA-708 předávání (vložených v SEI zprávy základní datové proudy H.264 nebo provádět jako pomocnou dat v souborech MXF)
3. SCC
4. Text SMPTE vypršel časový limit (ze zdroje CEA 608 za SMPTE RP2052, včetně vytváření souborů DFXP)
5. Soubor Subtitle SRT aplikace
6. Subtitle DVB datové proudy

Poznámka: výše uvedené výstupním formátu jsou podporovány pro doručení pomocí vysílání datového proudu ve službě Azure Media Services.

## <a name="known-issues"></a>Známé problémy
Pokud vaše vstupní video neobsahuje uzavřen, přidávání titulků, výstup Asset bude stále obsahovat prázdný soubor TTML. 

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

