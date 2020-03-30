---
title: Porovnání kodéry médií Azure na vyžádání | Dokumenty společnosti Microsoft
description: Toto téma porovnává možnosti kódování **programu Media Encoder Standard** a Media **Encoder Premium Workflow**.
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
ms.openlocfilehash: 4767f7bb5ba02c838c0e21721e55a6564a14acd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "69016653"
---
# <a name="comparison-of-azure-on-demand-media-encoders"></a>Porovnání kodéry médií Azure na vyžádání  

Toto téma porovnává možnosti kódování **programu Media Encoder Standard** a Media **Encoder Premium Workflow**.

## <a name="video-and-audio-processing-capabilities"></a>Možnosti zpracování videa a zvuku

Následující tabulka porovnává funkce mezi standardem media encoder (MES) a pracovním postupem Premium kodéru médií (MEPW). 

|Schopnost|Media Encoder Standard|Pracovní postup kodéru Media Encoder Premium|
|---|---|---|
|Použití podmíněné logiky při kódování<br/>(například pokud je vstup HD, pak kódujte zvuk 5.1)|Ne|Ano|
|Skryté titulky|Ne|[Ano](media-services-premium-workflow-encoder-formats.md#closed_captioning)|
|[Dolby® profesionální korekce hlasitosti](https://www.dolby.com/us/en/technologies/dolby-professional-loudness-solutions.pdf)<br/> s dialogovou inteligencí™|Ne|Ano|
|Odstranění prokládání, inverzní telecine|Basic|Kvalita vysílání|
|Detekce a odebrání černých okrajů <br/>(schránky na pilíře, poštovní schránky)|Ne|Ano|
|Generování miniatur|[Ano](media-services-dotnet-generate-thumbnail-with-mes.md)|[Ano](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)|
|Ořezávání/ořezávání a šití videí|[Ano](media-services-advanced-encoding-with-mes.md#trim_video)|Ano|
|Překryvy zvuku nebo videa|[Ano](media-services-advanced-encoding-with-mes.md#overlay)|[Ano](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-1--overlay-an-image-on-top-of-the-video)|
|Překryvy grafiky|Ze zdrojů obrazu|Ze zdrojů obrázků a textu|
|Více zvukových jazykových stop|Omezeně|[Ano](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-2--multiple-audio-language-encoding)|

## <a name="billing-meter-used-by-each-encoder"></a><a id="billing"></a>Měřič fakturace používaný každým kodérem
| Název mediálního procesoru | Použitelné ceny | Poznámky |
| --- | --- | --- |
| **Media Encoder Standard** |Kodér |Úlohy kódování budou účtovány na základě celkové doby trvání všech mediálních souborů vytvořených jako výstup v minutách ve výši uvedené [zde][1]ve sloupci ENCODER. |
| **Pracovní postup kodéru Media Encoder Premium** |PRÉMIOVÝ KODÉR |Úlohy kódování budou účtovány na základě celkové doby trvání všech mediálních souborů vytvořených jako výstup v minutách ve výši uvedené [zde][1]ve sloupci PREMIUM ENCODER. |

## <a name="input-containerfile-formats"></a>Vstupní formáty kontejnerů/souborů
| Formáty vstupního kontejneru nebo souboru | Media Encoder Standard | Pracovní postup kodéru Media Encoder Premium |
| --- | --- | --- |
| Adobe® Flash® F4V |Ano |Ano |
| MXF/SMPTE 377M |Ano |Ano |
| GXF |Ano |Ano |
| Dopravní toky MPEG-2 |Ano |Ano |
| MPEG-2 Program streamy |Ano |Ano |
| MPEG-4/MP4 |Ano |Ano |
| Windows Media/ASF |Ano |Ano |
| AVI (nekomprimovaný 8bit/10bit) |Ano |Ano |
| 3GPP/3GPP2 |Ano |Ne |
| Formát souboru plynulého streamování (PIFF 1.3) |Ano |Ne |
| [Záznam digitálního videa společnosti Microsoft (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) |Ano |Ne |
| Matroska/WebM |Ano |Ne |
| QuickTime (.mov) |Ano |Ne |

## <a name="input-video-codecs"></a>Vstupní video kodeky
| Kodeky vstupního videa | Media Encoder Standard | Pracovní postup kodéru Media Encoder Premium |
| --- | --- | --- |
| AVC s 8bitovou nebo 10bitovou hloubkou až do vzorkování 4:2:2, včetně AVCIntra |8bitová hloubka, 4:2:0 a 4:2:2 |Ano |
| Avid DNxHD (v souborech MXF) |Ano |Ano |
| DVCPro a DVCProHD (v souborech MXF) |Ano |Ano |
| JPEG2000 |Ano |Ano |
| MPEG-2 (až 422 profil a vysoká úroveň, včetně variant, jako je XDCAM, XDCAM HD, XDCAM IMX, CableLabs® a D10) |Až do profilu 422 |Ano |
| MPEG-1 |Ano |Ano |
| Windows Media Video/VC-1 |Ano |Ano |
| Canopus HQ a HQX |Ne |Ne |
| MPEG-4 Part 2 |Ano |Ne |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Ano |Ne |
| Apple ProRes 422 |Ano |Ne |
| Apple ProRes 422 LT |Ano |Ne |
| Apple ProRes 422 HQ |Ano |Ne |
| Apple ProRes Proxy |Ano |Ne |
| Apple ProRes 4444 |Ano |Ne |
| Apple ProRes 4444 XQ |Ano |Ne |
| HEVC/H.265|Hlavní profil|Hlavní a hlavní profil 10|

## <a name="input-audio-codecs"></a>Vstupní zvukové kodeky
| Kodeky vstupního zvuku | Media Encoder Standard | Pracovní postup kodéru Media Encoder Premium |
| --- | --- | --- |
| AES (SMPTE 331M a 302M, AES3-2003) |Ne |Ano |
| Dolby® E |Ne |Ano |
| Dolby® Digital (AC3) |Ne |Ano |
| Dolby® Digital Plus (E-AC3) |Ne |Ano |
| AAC (AAC-LC, AAC-HE a AAC-HEv2; až do prostorového zvuku 5.1) |Ano |Ano |
| MPEG Layer 2 |Ano |Ano |
| MP3 (MPEG-1 Audio Layer 3) |Ano |Ano |
| Windows Media Audio |Ano |Ano |
| WAV a PCM |Ano |Ano |
| [Flac](https://en.wikipedia.org/wiki/FLAC)</a> |Ano |Ne |
| [Opus](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |Ano |Ne |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Ano |Ne |

## <a name="output-containerfile-formats"></a>Výstupní formáty kontejnerů/souborů
| Výstupní formáty kontejnerů/souborů | Media Encoder Standard | Pracovní postup kodéru Media Encoder Premium |
| --- | --- | --- |
| Adobe® Flash® F4V |Ne |Ano |
| MXF (OP1a, XDCAM a AS02) |Ne |Ano |
| DPP (včetně AS11) |Ne |Ano |
| GXF |Ne |Ano |
| MPEG-4/MP4 |Ano |Ano |
| MPEG-TS |Ano |Ano |
| Windows Media/ASF |Ne |Ano |
| AVI (nekomprimovaný 8bit/10bit) |Ne |Ano |
| Formát souboru plynulého streamování (PIFF 1.3) |Ne |Ano |

## <a name="output-video-codecs"></a>Výstupní video kodeky
| Výstupní video kodeky | Media Encoder Standard | Pracovní postup kodéru Media Encoder Premium |
| --- | --- | --- |
| AVC (H.264; 8-bit; až vysoký profil, úroveň 5.2; 4K Ultra HD; AVC Intra) |Pouze 8 bit4:2:0 |Ano |
| HEVC (H.265; 8-bit a 10-bit;)  |Ne |Ano |
| Avid DNxHD (v souborech MXF) |Ne |Ano |
| MPEG-2 (až 422 profil a vysoká úroveň, včetně variant, jako je XDCAM, XDCAM HD, XDCAM IMX, CableLabs® a D10) |Ne |Ano |
| MPEG-1 |Ne |Ano |
| Windows Media Video/VC-1 |Ne |Ano |
| Vytvoření miniatur jpeg |Ano |Ano |
| Vytvoření miniatur png |Ano |Ano |
| Vytvoření miniatury BMP |Ano |Ne |

## <a name="output-audio-codecs"></a>Výstupní zvukové kodeky
| Výstupní zvukové kodeky | Media Encoder Standard | Pracovní postup kodéru Media Encoder Premium |
| --- | --- | --- |
| AES (SMPTE 331M a 302M, AES3-2003) |Ne |Ano |
| Dolby® Digital (AC3) |Ne |Ano |
| Dolby® Digital Plus (E-AC3) až 7,1 |Ne |Ano |
| AAC (AAC-LC, AAC-HE a AAC-HEv2; až do prostorového zvuku 5.1) |Ano |Ano |
| MPEG Layer 2 |Ne |Ano |
| MP3 (MPEG-1 Audio Layer 3) |Ne |Ano |
| Windows Media Audio |Ne |Ano |

>[!NOTE]
>Pokud kódujete do Dolby® Digital (AC3), výstup lze zapsat pouze do souboru ISO MP4.

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Související články
* [Provádění pokročilých úloh kódování přizpůsobením standardních přednastavení kodéru médií](media-services-custom-mes-presets-with-dotnet.md)
* [Kvóty a omezení](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
