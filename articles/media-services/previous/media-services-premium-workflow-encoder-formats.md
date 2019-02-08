---
title: Kodeky a formáty Media Encoderu Premium Workflow | Dokumentace Microsoftu
description: Toto téma poskytuje přehled o formáty Media Encoderu Premium pracovní postup, formáty a kodeky
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
ms.date: 01/15/2019
ms.author: juliako;anilmur
ms.openlocfilehash: a24448507fa16a48ab3522c3307261d989d773ee
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55895013"
---
# <a name="media-encoder-premium-workflow-formats-and-codecs"></a>Kodeky a formáty Media Encoderu Premium Workflow

> [!NOTE]
> Dotazy kodér úrovně premium, e-mailu mepd@microsoft.com.
> 
> Pracovní postup kodéru Media Encoder Premium mediálním procesorem popsané v tomto tématu není k dispozici v Číně. 
> 
> 

Tento dokument obsahuje seznam vstupních a výstupních formátů a kodeků, které podporuje verzi public preview **pracovní postup kodéru Media Encoder Premium** kodér.

[Pracovní postup kodéru Media Encoder Premium vstupní formáty a kodeky](#input_formats)

Kodeky a formáty výstupu pro pracovního postupu Media Encoder Premium

**Pracovní postup kodéru Media Encoder Premium** podporuje titulků je popsáno v [to](#closed_captioning) oddílu. 

## <a id="input_formats"></a>Pracovní postup kodéru Media Encoder Premium vstupní formáty a kodeky

V následující části jsou uvedené formáty kodeky a souborů, které tento procesor médií podporuje jako vstup.

### <a name="input-containerfile-formats"></a>Vstupní formáty kontejneru nebo souboru

* Adobe® Flash® F4V
* MXF/SMPTE 377M
* GXF
* Datové proudy MPEG-2 Transport
* Datové proudy MPEG-2 programu
* MPEG-4/MP4
* Windows Media/ASF
* AVI (nekomprimovaný 8 bitů nebo 10bitovým)

### <a name="input-video-codecs"></a>Kodeky vstupního videa

* AVC 8-bit/10-bit, až 4:2:2, včetně AVCIntra
* Avid DNxHD (in MXF)
* DVCPro/DVCProHD (in MXF)
* HEVC/H.265, hlavní a hlavní 10 profilu
* JPEG2000
* MPEG-2 (až do profilu 422 a vysoké úrovně; včetně variant, jako jsou XDCAM, XDCAM HD, XDCAM IMX, CableLabs® a D10)
* MPEG-1
* Windows Media Video/VC-1

### <a name="input-audio-codecs"></a>Kodeky vstupního zvuku

* AES (SMPTE 331 M a 302 M, AES3-2003)
* Dolby® E
* Digitální Dolby® (AC3)
* AAC (AAC-LC, AAC-HE a AAC-HEv2; až 5.1)
* MPEG vrstvy 2
* MP3 (MPEG-1 Audio Layer 3)
* Windows Media Audio
* WAV/PCM

## <a id="output_format"></a>Kodeky a formáty výstupu pro pracovního postupu Media Encoder Premium

V následující části jsou uvedeny kodeky a soubor formáty, které jsou podporované jako výstup z tohoto média procesoru.

### <a name="output-containerfile-formats"></a>Formáty výstupu kontejneru nebo souboru

* Adobe® Flash® F4V
* MXF (OP1a, XDCAM a AS02)
* DPP (včetně AS11)
* GXF
* MPEG-4/MP4
* Windows Media/ASF
* AVI (nekomprimovaný 8 bitů nebo 10bitovým)
* Technologie Smooth Streaming formát souboru (PIFF 1.3)
* MPEG-TS 

### <a name="output-video-codecs"></a>Video kodeků výstupu

* AVC (H.264; 8-bit; až do profilu vysokou úroveň 5.2; 4 kB Ultra HD, High Density; Uvnitř AVC)
* Avid DNxHD (in MXF)
* DVCPro/DVCProHD (in MXF)
* MPEG-2 (až do profilu 422 a vysoké úrovně; včetně variant, jako jsou XDCAM, XDCAM HD, XDCAM IMX, CableLabs® a D10)
* MPEG-1
* Windows Media Video/VC-1
* Vytváření náhledů JPEG
* HEVC (H.265; 8 bitů a 10bitovým, hlavní a hlavní 10 profil)

  Podpora pro HDR 10 je k dispozici v některých scénářích, obraťte se prosím na mepd@microsoft.com Další informace


### <a name="output-audio-codecs"></a>Výstup zvukových kodeků

* AES (SMPTE 331 M a 302 M, AES3-2003)
* Digitální Dolby® (AC3)
* Dolby® digitální Plus (E-AC3) až 7.1
* AAC (AAC-LC, AAC-HE a AAC-HEv2; až 5.1)
* MPEG vrstvy 2
* MP3 (MPEG-1 Audio Layer 3)
* Windows Media Audio

>[!NOTE]
>Pokud kódujete do Dolby® digitální (AC3), můžete výstup zapsat pouze do souboru ISO MP4.

## <a id="closed_captioning"></a>Podpora pro skryté titulky

Na ingestování **pracovní postup kodéru Media Encoder Premium** podporuje:

1. Soubory SCC
2. Soubory SMPTE TT
3. Skryté titulky CEA-608/skryté titulky CEA-708 – přenáší jako uživatelská data (SEI zprávy H.264 základní datové proudy, ATSC/53, SCTE20) nebo provést jako pomocné dat v souborech MXF/GXF
4. Soubory titulků STL

Na výstupu jsou k dispozici následující možnosti:

1. Skryté titulky CEA-608 k překladu skryté titulky CEA-708
2. Skryté titulky CEA-608/skryté titulky CEA-708 předávání (vložené do SEI zpráv základní datové proudy H.264 nebo provádět jako pomocné dat v souborech MXF)
3. SCC
4. Text SMPTE vypršel časový limit (ze zdroje skryté titulky CEA-608 za SMPTE RP2052, včetně vytvoření souboru DFXP)
5. Soubor s titulky SRT aplikace
6. Datové proudy DVB titulek

> [!NOTE]
> Ne všechny výše uvedené formáty výstupu jsou podporovány pro doručení pomocí vysílání datového proudu ve službě Azure Media Services.

## <a name="known-issues"></a>Známé problémy

Pokud vaše vstupní video neobsahuje titulků, výstup Asset bude stále obsahovat prázdný soubor TTML. 

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

