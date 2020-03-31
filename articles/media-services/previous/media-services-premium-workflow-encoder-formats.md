---
title: Formáty a kodeky programu Premium pro média | Dokumenty společnosti Microsoft
description: Toto téma poskytuje přehled formátů a kodeků formátů a kodeků kodéry kodéru kodéru kodéru kodéru.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269754"
---
# <a name="media-encoder-premium-workflow-formats-and-codecs"></a>Formáty a kodeky Media Encoderu Premium Workflow

> [!NOTE]
> Mediální procesor Media Encoder Premium Workflow popsaný v tomto tématu není v Číně k dispozici. 

Tento dokument obsahuje seznam vstupních a výstupních formátů souborů a kodeků, které jsou podporovány verzí **veřejnéverze produktu Media Encoder Premium Workflow.**

[Vstupní formáty a kodeky pracovního postupu Pro kódování médií Premium](#input_formats)

Výstupní formáty a kodeky pro kodéru Premium

**Program Premium pro média** podporuje skryté titulky popsané v [této](#closed_captioning) části. 

## <a name="media-encoder-premium-workflow-input-formats-and-codecs"></a><a id="input_formats"></a>Vstupní formáty a kodeky pracovního postupu Pro kódování médií Premium

V následující části jsou uvedeny kodeky a formáty souborů, které tento mediální procesor podporuje jako vstup.

### <a name="input-containerfile-formats"></a>Formáty vstupního kontejneru nebo souboru

* Adobe® Flash® F4V
* MXF/SMPTE 377M
* GXF
* Dopravní toky MPEG-2
* MPEG-2 Program streamy
* MPEG-4/MP4
* Windows Media/ASF
* AVI (nekomprimovaný 8bit/10bit)

### <a name="input-video-codecs"></a>Kodeky vstupního videa

* AVC s 8bitovou nebo 10bitovou hloubkou až do vzorkování 4:2:2, včetně AVCIntra
* Avid DNxHD (v souborech MXF)
* DVCPro a DVCProHD (v souborech MXF)
* PROFIL HEVC/H.265, Hlavní a Hlavní 10
* JPEG2000
* MPEG-2 (až 422 profil a vysoká úroveň, včetně variant, jako je XDCAM, XDCAM HD, XDCAM IMX, CableLabs® a D10)
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

## <a name="media-encoder-premium-workflow-output-formats-and-codecs"></a><a id="output_format"></a>Výstupní formáty a kodeky pro kodéru Premium

V následující části jsou uvedeny kodeky a formáty souborů, které jsou podporovány jako výstup z tohoto mediálního procesoru.

### <a name="output-containerfile-formats"></a>Výstupní formáty kontejnerů/souborů

* Adobe® Flash® F4V
* MXF (OP1a, XDCAM a AS02)
* DPP (včetně AS11)
* GXF
* MPEG-4/MP4
* Windows Media/ASF
* AVI (nekomprimovaný 8bit/10bit)
* Formát souboru plynulého streamování (PIFF 1.3)
* MPEG-TS 

### <a name="output-video-codecs"></a>Výstupní video kodeky

* AVC (H.264; 8-bit; až vysoký profil, úroveň 5.2; 4K Ultra HD; AVC Intra)
* Avid DNxHD (v souborech MXF)
* DVCPro a DVCProHD (v souborech MXF)
* MPEG-2 (až 422 profil a vysoká úroveň, včetně variant, jako je XDCAM, XDCAM HD, XDCAM IMX, CableLabs® a D10)
* MPEG-1
* Windows Media Video/VC-1
* Vytvoření miniatur jpeg
* HEVC (H.265; 8 bitů a 10 bitů, hlavní a hlavní profil 10)


### <a name="output-audio-codecs"></a>Výstupní zvukové kodeky

* AES (SMPTE 331M a 302M, AES3-2003)
* Dolby® Digital (AC3)
* Dolby® Digital Plus (E-AC3) až 7,1
* AAC (AAC-LC, AAC-HE a AAC-HEv2; až do prostorového zvuku 5.1)
* MPEG Layer 2
* MP3 (MPEG-1 Audio Layer 3)
* Windows Media Audio

>[!NOTE]
>Pokud kódujete do Dolby® Digital (AC3), výstup lze zapsat pouze do souboru ISO MP4.

## <a name="support-for-closed-captioning"></a><a id="closed_captioning"></a>Podpora skrytých titulků

Při ingestování podporuje **program Media Encoder Premium:**

1. Soubory SCC
2. Soubory SMPTE-TT
3. CEA-608/CEA-708 – neseno jako uživatelská data (SEI zprávy základních proudů H.264, ATSC/53, SCTE20) nebo nesené jako pomocná data v souborech MXF/GXF
4. Soubory titulků STL

Na výstupu jsou k dispozici následující možnosti:

1. CEA-608 až CEA-708 překlad
2. CEA-608/CEA-708 projít (vložené do SEI zpráv H.264 elementární proudy, nebo provádí jako pomocná data v souborech MXF)
3. Scc
4. SMPTE Časovaný text (ze zdroje CEA-608 za SMPTE RP2052; včetně vytvoření souboru DFXP)
5. Soubor titulků SRT
6. Dvb titulky proudy

> [!NOTE]
> Ne všechny výše uvedené výstupní formáty jsou podporované pro doručování prostřednictvím streamování ve službě Azure Media Services.

## <a name="known-issues"></a>Známé problémy

Pokud vstupní video neobsahuje skryté titulky, bude výstupní datový zdroj stále obsahovat prázdný soubor TTML. 

## <a name="need-help"></a>Potřebujete pomoc?

Lístek podpory můžete otevřít tak, že přejdete na [Novou žádost o podporu.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)
## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

