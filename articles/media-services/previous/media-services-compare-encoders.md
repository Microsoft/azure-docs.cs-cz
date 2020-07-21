---
title: Porovnání kodérů médií v Azure na vyžádání | Microsoft Docs
description: Toto téma porovnává funkce kódování **Media Encoder Standard** a **Media Encoder Premium Workflow**.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: a79437c0-4832-423a-bca8-82632b2c47cc
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: ca17ffdc2a54b25bbfd855834a366ed381a54678
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86530384"
---
# <a name="comparison-of-azure-on-demand-media-encoders"></a>Porovnání kodérů médií na vyžádání v Azure  

Toto téma porovnává funkce kódování **Media Encoder Standard** a **Media Encoder Premium Workflow**.

## <a name="video-and-audio-processing-capabilities"></a>Možnosti zpracování videa a zvuku

V následující tabulce jsou porovnávány funkce Media Encoder Standard (status) a Media Encoder Premium Workflow (MEPW). 

|Schopnost|Media Encoder Standard|Pracovní postup kodéru Media Encoder Premium|
|---|---|---|
|Použití podmíněné logiky při kódování<br/>(například pokud vstup je HD a pak kódování 5,1 zvuk)|No|Ano|
|Skryté titulky|No|[Ano](media-services-premium-workflow-encoder-formats.md#closed_captioning)|
|[Oprava pro zvuk Dolby &reg; Professional](https://professional.dolby.com/product/broadcast/vm600/)<br/> s dialogovými inteligentními funkcemi&trade;|No|Ano|
|Zrušení prokládání, inverzní telecine|Základní|Kvalita vysílání|
|Detekovat a odebrat černá ohraničení <br/>(pillarboxes, letterboxes)|No|Ano|
|Generování miniatur|[Ano](media-services-dotnet-generate-thumbnail-with-mes.md)|[Ano](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)|
|Vystřihování, ořezávání a sezpůsobování videí|[Ano](media-services-advanced-encoding-with-mes.md#trim_video)|Ano|
|Překryvy zvuku nebo videa|[Ano](media-services-advanced-encoding-with-mes.md#overlay)|[Ano](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-1--overlay-an-image-on-top-of-the-video)|
|Překryvy grafiky|Z obrazových zdrojů|Z obrázků a zdrojů textu|
|Několik stop zvukového jazyka|Omezeně|[Ano](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-2--multiple-audio-language-encoding)|

## <a name="billing-meter-used-by-each-encoder"></a><a id="billing"></a>Měřič fakturace používaný jednotlivými kodéry
| Název procesoru médií | Příslušné ceny | Poznámky |
| --- | --- | --- |
| **Media Encoder Standard** |KODÉR |Úlohy kódování se budou účtovat podle celkové doby trvání (v minutách) všech mediálních souborů vytvořených jako výstup [, a to v zadané míře][1]ve sloupci kodér. |
| **Pracovní postup kodéru Media Encoder Premium** |KODÉR ÚROVNĚ PREMIUM |Úlohy kódování se budou účtovat podle celkové doby trvání (v minutách) všech mediálních souborů vytvořených jako výstup, a to v sazbě, která je [zde][1]uvedená, ve sloupci kodér úrovně Premium. |

## <a name="input-containerfile-formats"></a>Vstupní formáty kontejneru nebo souborů
| Formáty vstupního kontejneru nebo souboru | Media Encoder Standard | Pracovní postup kodéru Media Encoder Premium |
| --- | --- | --- |
| Adobe &reg; Flash &reg; F4V |Ano |Ano |
| MXF/SMPTE 377M |Ano |Ano |
| GXF |Ano |Ano |
| Přenosové streamy MPEG-2 |Ano |Ano |
| Datové proudy programu MPEG-2 |Ano |Ano |
| MPEG-4/MP4 |Ano |Ano |
| Windows Media/ASF |Ano |Ano |
| AVI (nekomprimovaný 8bit/10bit) |Ano |Ano |
| 3GPP/SPECIFIKACÍ 3GPP2 |Ano |No |
| Smooth Streaming formát souboru (PIFF 1,3) |Ano |No |
| [Microsoft Digital Video Recording (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) |Ano |No |
| Matroska/WebM |Ano |No |
| QuickTime (.mov) |Ano |No |

## <a name="input-video-codecs"></a>Vstupní kodeky videa
| Kodeky vstupního videa | Media Encoder Standard | Pracovní postup kodéru Media Encoder Premium |
| --- | --- | --- |
| AVC s 8bitovou nebo 10bitovou hloubkou až do vzorkování 4:2:2, včetně AVCIntra |8bitová hloubka, 4:2:0 a 4:2:2 |Ano |
| Avid DNxHD (v souborech MXF) |Ano |Ano |
| DVCPro a DVCProHD (v souborech MXF) |Ano |Ano |
| JPEG2000 |Ano |Ano |
| MPEG-2 (až 422 profilu a vysoké úrovně; včetně variant, jako je například XDCAM, XDCAM HD, XDCAM IMX, CableLabs &reg; a D10) |Až do profilu 422 |Ano |
| MPEG-1 |Ano |Ano |
| Windows Media Video/VC-1 |Ano |Ano |
| Canopus HQ a HQX |No |No |
| MPEG-4 Part 2 |Ano |No |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Ano |No |
| Apple ProRes 422 |Ano |No |
| Apple ProRes 422 LT |Ano |No |
| Apple ProRes 422 HQ |Ano |No |
| Apple ProRes Proxy |Ano |No |
| Apple ProRes 4444 |Ano |No |
| Apple ProRes 4444 XQ |Ano |No |
| HEVC/H. 265|Hlavní profil|Hlavní a hlavní 10 profil|

## <a name="input-audio-codecs"></a>Vstupní zvukové kodeky
| Kodeky vstupního zvuku | Media Encoder Standard | Pracovní postup kodéru Media Encoder Premium |
| --- | --- | --- |
| AES (SMPTE 331M a 302M, AES3-2003) |No |Ano |
| Dolby &reg; E |No |Ano |
| Zvuk Dolby &reg; Digital (AC3) |No |Ano |
| Dolby &reg; Digital Plus (E-AC3) |No |Ano |
| AAC (AAC-LC, AAC-HE a AAC-HEv2; až do prostorového zvuku 5.1) |Ano |Ano |
| MPEG Layer 2 |Ano |Ano |
| MP3 (MPEG-1 Audio Layer 3) |Ano |Ano |
| Windows Media Audio |Ano |Ano |
| WAV a PCM |Ano |Ano |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Ano |No |
| [Opus](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |Ano |No |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Ano |No |

## <a name="output-containerfile-formats"></a>Výstupní formát kontejneru/souboru
| Výstupní formát kontejneru/souboru | Media Encoder Standard | Pracovní postup kodéru Media Encoder Premium |
| --- | --- | --- |
| Adobe &reg; Flash &reg; F4V |No |Ano |
| MXF (OP1a, XDCAM a AS02) |No |Ano |
| DPP (včetně AS11) |No |Ano |
| GXF |No |Ano |
| MPEG-4/MP4 |Ano |Ano |
| MPEG-TS |Ano |Ano |
| Windows Media/ASF |No |Ano |
| AVI (nekomprimovaný 8bit/10bit) |No |Ano |
| Smooth Streaming formát souboru (PIFF 1,3) |No |Ano |

## <a name="output-video-codecs"></a>Video s výstupními kodeky
| Video s výstupními kodeky | Media Encoder Standard | Pracovní postup kodéru Media Encoder Premium |
| --- | --- | --- |
| AVC (H. 264; 8 bitů; až do vysokého profilu, úroveň 5,2; 4K Ultra HD; AVC uvnitř) |Jenom 8 bitů 4:2:0 |Ano |
| HEVC (H. 265; 8 bitů a 10 bitů;)  |No |Ano |
| Avid DNxHD (v souborech MXF) |No |Ano |
| MPEG-2 (až 422 profilu a vysoké úrovně; včetně variant, jako je například XDCAM, XDCAM HD, XDCAM IMX, CableLabs &reg; a D10) |No |Ano |
| MPEG-1 |No |Ano |
| Windows Media Video/VC-1 |No |Ano |
| Vytvoření Miniatury JPEG |Ano |Ano |
| Vytvoření miniatury PNG |Ano |Ano |
| Vytvoření miniatury BMP |Ano |No |

## <a name="output-audio-codecs"></a>Výstupní zvukové kodeky
| Výstupní zvukové kodeky | Media Encoder Standard | Pracovní postup kodéru Media Encoder Premium |
| --- | --- | --- |
| AES (SMPTE 331M a 302M, AES3-2003) |No |Ano |
| Zvuk Dolby &reg; Digital (AC3) |No |Ano |
| Dolby &reg; Digital Plus (E-AC3) až 7,1 |No |Ano |
| AAC (AAC-LC, AAC-HE a AAC-HEv2; až do prostorového zvuku 5.1) |Ano |Ano |
| MPEG Layer 2 |No |Ano |
| MP3 (MPEG-1 Audio Layer 3) |No |Ano |
| Windows Media Audio |No |Ano |

>[!NOTE]
>Pokud kódujete do &reg; formátu Dolby Digital (AC3), může být výstup zapsán pouze do souboru ISO MP4.

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Související články
* [Provádění pokročilých úloh kódování přizpůsobením Media Encoder Standard přednastavení](media-services-custom-mes-presets-with-dotnet.md)
* [Kvóty a omezení](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
