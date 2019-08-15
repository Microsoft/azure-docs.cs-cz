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
ms.openlocfilehash: 4767f7bb5ba02c838c0e21721e55a6564a14acd1
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "69016653"
---
# <a name="comparison-of-azure-on-demand-media-encoders"></a>Porovnání kodérů médií na vyžádání v Azure  

Toto téma porovnává funkce kódování **Media Encoder Standard** a **Media Encoder Premium Workflow**.

## <a name="video-and-audio-processing-capabilities"></a>Možnosti zpracování videa a zvuku

V následující tabulce jsou porovnávány funkce Media Encoder Standard (status) a Media Encoder Premium Workflow (MEPW). 

|Funkce|Media Encoder Standard|Pracovní postup kodéru Media Encoder Premium|
|---|---|---|
|Použití podmíněné logiky při kódování<br/>(například pokud vstup je HD a pak kódování 5,1 zvuk)|Ne|Ano|
|Skryté titulky|Ne|[Ano](media-services-premium-workflow-encoder-formats.md#closed_captioning)|
|[Korekce formátu Dolby® Professional](https://www.dolby.com/us/en/technologies/dolby-professional-loudness-solutions.pdf)<br/> s dialogovým Intelligence™|Ne|Ano|
|Zrušení prokládání, inverzní telecine|Basic|Kvalita vysílání|
|Detekovat a odebrat černá ohraničení <br/>(pillarboxes, letterboxes)|Ne|Ano|
|Generování miniatur|[Ano](media-services-dotnet-generate-thumbnail-with-mes.md)|[Ano](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)|
|Vystřihování, ořezávání a sezpůsobování videí|[Ano](media-services-advanced-encoding-with-mes.md#trim_video)|Ano|
|Překryvy zvuku nebo videa|[Ano](media-services-advanced-encoding-with-mes.md#overlay)|[Ano](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-1--overlay-an-image-on-top-of-the-video)|
|Překryvy grafiky|Z obrazových zdrojů|Z obrázků a zdrojů textu|
|Několik stop zvukového jazyka|Omezené|[Ano](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-2--multiple-audio-language-encoding)|

## <a id="billing"></a>Měřič fakturace používaný jednotlivými kodéry
| Název procesoru médií | Příslušné ceny | Poznámky |
| --- | --- | --- |
| **Media Encoder Standard** |KODÉR |Úlohy kódování se budou účtovat podle celkové doby trvání (v minutách) všech mediálních souborů vytvořených jako výstup, a to v zadané míře ve [][1]sloupci kodér. |
| **Pracovní postup kodéru Media Encoder Premium** |KODÉR ÚROVNĚ PREMIUM |Úlohy kódování se budou účtovat podle celkové doby trvání (v minutách) všech mediálních souborů vytvořených jako výstup, a to v sazbě, která je [zde][1]uvedená, ve sloupci kodér úrovně Premium. |

## <a name="input-containerfile-formats"></a>Vstupní formáty kontejneru nebo souborů
| Vstupní formáty kontejneru nebo souborů | Media Encoder Standard | Pracovní postup kodéru Media Encoder Premium |
| --- | --- | --- |
| Adobe® Flash® F4V |Ano |Ano |
| MXF/SMPTE 377M |Ano |Ano |
| GXF |Ano |Ano |
| Přenosové streamy MPEG-2 |Ano |Ano |
| Datové proudy programu MPEG-2 |Ano |Ano |
| MPEG-4/MP4 |Ano |Ano |
| Windows Media/ASF |Ano |Ano |
| AVI (nekomprimovaný 8bit/10bit) |Ano |Ano |
| 3GPP/3GPP2 |Ano |Ne |
| Smooth Streaming formát souboru (PIFF 1,3) |Ano |Ne |
| [Microsoft Digital Video Recording (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) |Ano |Ne |
| Matroska/WebM |Ano |Ne |
| QuickTime (. mov) |Ano |Ne |

## <a name="input-video-codecs"></a>Vstupní kodeky videa
| Vstupní kodeky videa | Media Encoder Standard | Pracovní postup kodéru Media Encoder Premium |
| --- | --- | --- |
| AVC 8-bit/10 bitů, až 4:2:2, včetně AVCIntra |8 bitů 4:2:0 a 4:2:2 |Ano |
| Avid DNxHD (v MXF) |Ano |Ano |
| DVCPro/DVCProHD (v MXF) |Ano |Ano |
| JPEG2000 |Ano |Ano |
| MPEG-2 (až 422 profilu a vysoké úrovně; včetně variant, jako je například XDCAM, XDCAM HD, XDCAM IMX, CableLabs® a D10) |Až 422 profil |Ano |
| MPEG-1 |Ano |Ano |
| Windows Media Video/VC-1 |Ano |Ano |
| Canopus sídel/HQX |Ne |Ne |
| MPEG-4 část 2 |Ano |Ne |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Ano |Ne |
| Apple ProRes 422 |Ano |Ne |
| Apple ProRes 422 LT |Ano |Ne |
| Apple ProRes 422 sídel |Ano |Ne |
| Proxy server Apple ProRes |Ano |Ne |
| Apple ProRes 4444 |Ano |Ne |
| Apple ProRes 4444 XQ |Ano |Ne |
| HEVC/H. 265|Hlavní profil|Hlavní a hlavní 10 profil|

## <a name="input-audio-codecs"></a>Vstupní zvukové kodeky
| Vstupní zvukové kodeky | Media Encoder Standard | Pracovní postup kodéru Media Encoder Premium |
| --- | --- | --- |
| AES (SMPTE 331M a 302M, AES3-2003) |Ne |Ano |
| Dolby® E |Ne |Ano |
| Dolby® Digital (AC3) |Ne |Ano |
| Dolby® Digital Plus (E-AC3) |Ne |Ano |
| AAC (AAC-LC, AAC-HE a AAC-HEv2; až do 5,1) |Ano |Ano |
| MPEG – vrstva 2 |Ano |Ano |
| MP3 (zvuková vrstva MPEG-1 3) |Ano |Ano |
| Windows Media Audio |Ano |Ano |
| WAV/PCM |Ano |Ano |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Ano |Ne |
| [Opus](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |Ano |Ne |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Ano |Ne |

## <a name="output-containerfile-formats"></a>Výstupní formát kontejneru/souboru
| Výstupní formát kontejneru/souboru | Media Encoder Standard | Pracovní postup kodéru Media Encoder Premium |
| --- | --- | --- |
| Adobe® Flash® F4V |Ne |Ano |
| MXF (OP1a, XDCAM a AS02) |Ne |Ano |
| DPP (včetně AS11) |Ne |Ano |
| GXF |Ne |Ano |
| MPEG-4/MP4 |Ano |Ano |
| MPEG-TS |Ano |Ano |
| Windows Media/ASF |Ne |Ano |
| AVI (nekomprimovaný 8bit/10bit) |Ne |Ano |
| Smooth Streaming formát souboru (PIFF 1,3) |Ne |Ano |

## <a name="output-video-codecs"></a>Video s výstupními kodeky
| Video s výstupními kodeky | Media Encoder Standard | Pracovní postup kodéru Media Encoder Premium |
| --- | --- | --- |
| AVC (H. 264; 8 bitů; až do vysokého profilu, úroveň 5,2; 4K Ultra HD; AVC uvnitř) |Jenom 8 bitů 4:2:0 |Ano |
| HEVC (H. 265; 8 bitů a 10 bitů;)  |Ne |Ano |
| Avid DNxHD (v MXF) |Ne |Ano |
| MPEG-2 (až 422 profilu a vysoké úrovně; včetně variant, jako je například XDCAM, XDCAM HD, XDCAM IMX, CableLabs® a D10) |Ne |Ano |
| MPEG-1 |Ne |Ano |
| Windows Media Video/VC-1 |Ne |Ano |
| Vytvoření Miniatury JPEG |Ano |Ano |
| Vytvoření miniatury PNG |Ano |Ano |
| Vytvoření miniatury BMP |Ano |Ne |

## <a name="output-audio-codecs"></a>Výstupní zvukové kodeky
| Výstupní zvukové kodeky | Media Encoder Standard | Pracovní postup kodéru Media Encoder Premium |
| --- | --- | --- |
| AES (SMPTE 331M a 302M, AES3-2003) |Ne |Ano |
| Dolby® Digital (AC3) |Ne |Ano |
| Dolby® Digital Plus (E-AC3) až 7,1 |Ne |Ano |
| AAC (AAC-LC, AAC-HE a AAC-HEv2; až do 5,1) |Ano |Ano |
| MPEG – vrstva 2 |Ne |Ano |
| MP3 (zvuková vrstva MPEG-1 3) |Ne |Ano |
| Windows Media Audio |Ne |Ano |

>[!NOTE]
>Pokud kódujete do formátu Dolby® Digital (AC3), může být výstup zapsaný pouze do souboru ISO MP4.

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Související články
* [Provádění pokročilých úloh kódování přizpůsobením Media Encoder Standard přednastavení](media-services-custom-mes-presets-with-dotnet.md)
* [Kvóty a omezení](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
