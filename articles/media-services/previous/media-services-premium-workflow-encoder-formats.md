---
title: Formáty a kodeky Media Encoder Premium Workflow | Microsoft Docs
description: Toto téma poskytuje přehled formátů a kodeků formátů Media Encoder Premium Workflow.
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
ms.date: 03/19/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: 87cd7c63939331190530a46071a6b4c40480562f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84705096"
---
# <a name="media-encoder-premium-workflow-formats-and-codecs"></a>Media Encoder Premium Workflow formáty a kodeky

> [!NOTE]
> Procesor Media Encoder Premium Workflow médií popisovaný v tomto tématu není k dispozici v Číně. 

Tento dokument obsahuje seznam formátů vstupních a výstupních souborů a kodeků, které jsou podporovány verzí Public Preview nástroje **Media Encoder Premium Workflow** Encoder.

[Formáty a kodeky Media Encoder Premium Workflow Input](#input_formats)

Media Encoder Premium Workflow formáty výstupu a kodeky

**Media Encoder Premium Workflow** podporuje skryté titulky popsané v [této](#closed_captioning) části. 

## <a name="media-encoder-premium-workflow-input-formats-and-codecs"></a><a id="input_formats"></a>Formáty a kodeky Media Encoder Premium Workflow Input

V následující části jsou uvedeny kodeky a formáty souborů, které tento procesor médií podporuje jako vstup.

### <a name="input-containerfile-formats"></a>Formáty vstupního kontejneru nebo souboru

* Adobe® Flash® F4V
* MXF/SMPTE 377M
* GXF
* Přenosové streamy MPEG-2
* Datové proudy programu MPEG-2
* MPEG-4/MP4
* Windows Media/ASF
* AVI (nekomprimovaný 8bit/10bit)

### <a name="input-video-codecs"></a>Kodeky vstupního videa

* AVC s 8bitovou nebo 10bitovou hloubkou až do vzorkování 4:2:2, včetně AVCIntra
* Avid DNxHD (v souborech MXF)
* DVCPro a DVCProHD (v souborech MXF)
* HEVC/H. 265, Main a Main 10 Profile
* JPEG2000
* MPEG-2 (až 422 profilu a vysoké úrovně; včetně variant, jako je například XDCAM, XDCAM HD, XDCAM IMX, CableLabs® a D10)
* MPEG-1
* Windows Media Video/VC-1

### <a name="input-audio-codecs"></a>Kodeky vstupního zvuku

* AES (SMPTE 331M a 302M, AES3-2003)
* Dolby® E
* Dolby® Digital (AC3)
* AAC (AAC-LC, AAC-HE a AAC-HEv2; až do prostorového zvuku 5.1)
* MPEG Layer 2
* MP3 (MPEG-1 Audio Layer 3)
* Windows Media Audio
* WAV a PCM

## <a name="media-encoder-premium-workflow-output-formats-and-codecs"></a><a id="output_format"></a>Media Encoder Premium Workflow formáty výstupu a kodeky

V následující části jsou uvedené kodeky a formáty souborů, které jsou podporované jako výstup z tohoto multimediálního procesoru.

### <a name="output-containerfile-formats"></a>Výstupní formát kontejneru/souboru

* Adobe® Flash® F4V
* MXF (OP1a, XDCAM a AS02)
* DPP (včetně AS11)
* GXF
* MPEG-4/MP4
* Windows Media/ASF
* AVI (nekomprimovaný 8bit/10bit)
* Smooth Streaming formát souboru (PIFF 1,3)
* MPEG-TS 

### <a name="output-video-codecs"></a>Video s výstupními kodeky

* AVC (H. 264; 8 bitů; až do vysokého profilu, úroveň 5,2; 4K Ultra HD; AVC uvnitř)
* Avid DNxHD (v souborech MXF)
* DVCPro a DVCProHD (v souborech MXF)
* MPEG-2 (až 422 profilu a vysoké úrovně; včetně variant, jako je například XDCAM, XDCAM HD, XDCAM IMX, CableLabs® a D10)
* MPEG-1
* Windows Media Video/VC-1
* Vytvoření Miniatury JPEG
* HEVC (H. 265; 8 bitů a 10 bitů, hlavní a hlavní 10 profil)


### <a name="output-audio-codecs"></a>Výstupní zvukové kodeky

* AES (SMPTE 331M a 302M, AES3-2003)
* Dolby® Digital (AC3)
* Dolby® Digital Plus (E-AC3) až 7,1
* AAC (AAC-LC, AAC-HE a AAC-HEv2; až do prostorového zvuku 5.1)
* MPEG Layer 2
* MP3 (MPEG-1 Audio Layer 3)
* Windows Media Audio

>[!NOTE]
>Pokud kódujete do formátu Dolby® Digital (AC3), může být výstup zapsaný pouze do souboru ISO MP4.

## <a name="support-for-closed-captioning"></a><a id="closed_captioning"></a>Podpora pro skryté titulky

Při příjmu **Media Encoder Premium Workflow** podporuje:

1. Soubory SCC
2. Soubory ve formátu SMPTE a TT
3. CEA-608/CEA-708 – přenesené jako data uživatelů (SEI zprávy H. 264 základních datových proudů, ATSC/53, SCTE20) nebo přenesené jako pomocná data v souborech MXF/GXF
4. Soubory titulků STL

Na výstupu jsou k dispozici následující možnosti:

1. CEA-608 pro CEA-708 překlad
2. CEA-608/CEA-708 Pass (integrováno v SEI zprávách H. 264 základních datových proudů nebo přenesené jako pomocná data v souborech MXF)
3. SCC
4. Neomezený text SMPTE (ze zdroje CEA-608 na SMPTE RP2052; včetně DFXPového souboru)
5. Soubor podtitulku SRT aplikace
6. Streamy s titulky DVB

> [!NOTE]
> Ne všechny výše uvedené formáty výstupu jsou podporovány pro doručování prostřednictvím streamování v Azure Media Services.

## <a name="known-issues"></a>Známé problémy

Pokud vstupní video neobsahuje skryté titulky, bude výstupní Asset stále obsahovat prázdný soubor TTML. 

## <a name="need-help"></a>Potřebujete pomoc?

Lístek podpory můžete otevřít tak, že přejdete na [novou žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) .
## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

