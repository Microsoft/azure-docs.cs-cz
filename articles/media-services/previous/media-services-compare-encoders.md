---
title: Porovnání Azure na vyžádání kodérů médií | Dokumentace Microsoftu
description: Toto téma porovnává možnosti kódování **kodéru Media Encoder Standard** a **pracovní postup kodéru Media Encoder Premium**.
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
ms.date: 10/24/2018
ms.author: juliako;anilmur
ms.openlocfilehash: c08759f4682c6010c2338ff7aaf61cda92eb0484
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232083"
---
# <a name="comparison-of-azure-on-demand-media-encoders"></a>Porovnání Azure na vyžádání kodérů médií

Toto téma porovnává možnosti kódování **kodéru Media Encoder Standard** a **pracovní postup kodéru Media Encoder Premium**.

## <a name="video-and-audio-processing-capabilities"></a>Možnosti zpracování videa a zvuku

Následující tabulka obsahuje porovnání funkcí mezi Media Encoder Standard (MES) a Media Encoder Premium Workflow (MEPW). 

|Schopnost|Media Encoder Standard|Pracovní postup kodéru Media Encoder Premium|
|---|---|---|
|Použití podmíněné logiky při kódování<br/>(například, pokud je vstup HD, High Density, pak kódování zvuk 5.1)|Ne|Ano|
|Skryté titulky|Ne|[Ano](media-services-premium-workflow-encoder-formats.md#closed_captioning)|
|[Oprava Dolby® profesionální hlasitosti](http://www.dolby.com/us/en/technologies/dolby-professional-loudness-solutions.pdf)<br/> pomocí dialogu Intelligence™|Ne|Ano|
|Odstraňování prokládání, inverzní telecine|Basic|Kvalitě pro vysílání|
|Zjistit a odebrat černé ohraničení <br/>(pillarboxes letterboxes)|Ne|Ano|
|Generování miniatur|[Ano](media-services-dotnet-generate-thumbnail-with-mes.md)|[Ano](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)|
|Výstřižek/ořezávání a spojování videí|[Ano](media-services-advanced-encoding-with-mes.md#trim_video)|Ano|
|Překryvy zvuku nebo videa|[Ano](media-services-advanced-encoding-with-mes.md#overlay)|[Ano](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-1--overlay-an-image-on-top-of-the-video)|
|Překryvy grafiky|Ze zdroje obrázků|Ze zdroje obrázků a textu|
|Několik zvukových stop|Omezená|[Ano](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-2--multiple-audio-language-encoding)|

## <a id="billing"></a>Fakturace měřiče, které používají každý kodér
| Název procesoru médií | Platné ceny | Poznámky |
| --- | --- | --- |
| **Media Encoder Standard** |KODÉR |Kódování úloh bude účtovat v závislosti na celková doba trvání v minutách, všechny soubory médií vytvořen jako výstup sazbou uvedenou [tady][1], ve sloupci KODÉR. |
| **Pracovní postup kodéru Media Encoder Premium** |KODÉR ÚROVNĚ PREMIUM |Kódování úloh bude účtovat v závislosti na celková doba trvání v minutách, všechny soubory médií vytvořen jako výstup sazbou uvedenou [tady][1], ve sloupci KODÉR úrovně PREMIUM. |

## <a name="input-containerfile-formats"></a>Vstupní formáty kontejneru nebo souboru
| Vstupní formáty kontejneru nebo souboru | Media Encoder Standard | Pracovní postup kodéru Media Encoder Premium |
| --- | --- | --- |
| Adobe® Flash® F4V |Ano |Ano |
| MXF/SMPTE 377M |Ano |Ano |
| GXF |Ano |Ano |
| Datové proudy MPEG-2 Transport |Ano |Ano |
| Datové proudy MPEG-2 programu |Ano |Ano |
| MPEG-4/MP4 |Ano |Ano |
| Windows Media/amp |Ano |Ano |
| AVI (nekomprimovaný 8 bitů nebo 10bitovým) |Ano |Ano |
| 3GPP/3GPP2 |Ano |Ne |
| Technologie Smooth Streaming formát souboru (PIFF 1.3) |Ano |Ne |
| [Microsoft Digital Video Recording](https://msdn.microsoft.com/library/windows/desktop/dd692984) |Ano |Ne |
| Matroska a WebM |Ano |Ne |
| QuickTime (QuickTime) |Ano |Ne |

## <a name="input-video-codecs"></a>Kodeky vstupního videa
| Kodeky vstupního videa | Media Encoder Standard | Pracovní postup kodéru Media Encoder Premium |
| --- | --- | --- |
| AVC 8-bit/10-bit, až 4:2:2, včetně AVCIntra |8bitová hloubka, 4:2:0 a 4:2:2 |Ano |
| Avid DNxHD (v souborech MXF) |Ano |Ano |
| DVCPro a DVCProHD (v souborech MXF) |Ano |Ano |
| JPEG2000 |Ano |Ano |
| MPEG-2 (až do profilu 422 a vysoké úrovně; včetně variant, jako jsou XDCAM, XDCAM HD, XDCAM IMX, CableLabs® a D10) |Až do profilu 422 |Ano |
| MPEG-1 |Ano |Ano |
| Windows Media Video/VC-1 |Ano |Ano |
| Canopus HQ a HQX |Ne |Ne |
| 2. část MPEG-4 |Ano |Ne |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Ano |Ne |
| Apple ProRes 422 |Ano |Ne |
| Apple ProRes 422 LT |Ano |Ne |
| Sídel ProRes 422 Apple |Ano |Ne |
| Apple ProRes Proxy |Ano |Ne |
| Apple ProRes 4444 |Ano |Ne |
| Apple ProRes 4444 XQ |Ano |Ne |
| HEVC/H.265|Hlavní profil|Hlavní a hlavní 10 profilu|

## <a name="input-audio-codecs"></a>Kodeky vstupního zvuku
| Kodeky vstupního zvuku | Media Encoder Standard | Pracovní postup kodéru Media Encoder Premium |
| --- | --- | --- |
| AES (SMPTE 331 M a 302 M, AES3-2003) |Ne |Ano |
| Dolby® E |Ne |Ano |
| Digitální Dolby® (AC3) |Ne |Ano |
| Digitální Dolby® Plus (E-AC3) |Ne |Ano |
| AAC (AAC-LC, AAC-HE a AAC-HEv2; až 5.1) |Ano |Ano |
| MPEG vrstvy 2 |Ano |Ano |
| MP3 (zvuk vrstvu MPEG-1 3) |Ano |Ano |
| Windows Media zvuku |Ano |Ano |
| WAV/PCM |Ano |Ano |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Ano |Ne |
| [Díle](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |Ano |Ne |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Ano |Ne |

## <a name="output-containerfile-formats"></a>Formáty výstupu kontejneru nebo souboru
| Formáty výstupu kontejneru nebo souboru | Media Encoder Standard | Pracovní postup kodéru Media Encoder Premium |
| --- | --- | --- |
| Adobe® Flash® F4V |Ne |Ano |
| MXF (OP1a, XDCAM a AS02) |Ne |Ano |
| DPP (včetně AS11) |Ne |Ano |
| GXF |Ne |Ano |
| MPEG-4/MP4 |Ano |Ano |
| MPEG-TS |Ano |Ano |
| Windows Media/amp |Ne |Ano |
| AVI (nekomprimovaný 8 bitů nebo 10bitovým) |Ne |Ano |
| Technologie Smooth Streaming formát souboru (PIFF 1.3) |Ne |Ano |

## <a name="output-video-codecs"></a>Video kodeků výstupu
| Video kodeků výstupu | Media Encoder Standard | Pracovní postup kodéru Media Encoder Premium |
| --- | --- | --- |
| AVC (H.264; 8-bit; až do profilu vysokou úroveň 5.2; 4 kB Ultra HD, High Density; Uvnitř AVC) |Pouze 8bitová hloubka, 4:2:0 |Ano |
| HEVC (H.265; 8 bitů a 10-bit;)  |Ne |Ano |
| Avid DNxHD (v souborech MXF) |Ne |Ano |
| MPEG-2 (až do profilu 422 a vysoké úrovně; včetně variant, jako jsou XDCAM, XDCAM HD, XDCAM IMX, CableLabs® a D10) |Ne |Ano |
| MPEG-1 |Ne |Ano |
| Windows Media Video/VC-1 |Ne |Ano |
| Vytváření náhledů JPEG |Ano |Ano |
| Vytváření náhledů PNG |Ano |Ano |
| Vytváření náhledů BMP |Ano |Ne |

## <a name="output-audio-codecs"></a>Výstup zvukových kodeků
| Výstup zvukových kodeků | Media Encoder Standard | Pracovní postup kodéru Media Encoder Premium |
| --- | --- | --- |
| AES (SMPTE 331 M a 302 M, AES3-2003) |Ne |Ano |
| Digitální Dolby® (AC3) |Ne |Ano |
| Dolby® digitální Plus (E-AC3) až 7.1 |Ne |Ano |
| AAC (AAC-LC, AAC-HE a AAC-HEv2; až 5.1) |Ano |Ano |
| MPEG vrstvy 2 |Ne |Ano |
| MP3 (zvuk vrstvu MPEG-1 3) |Ne |Ano |
| Windows Media zvuku |Ne |Ano |

>[!NOTE]
>Pokud kódujete do Dolby® digitální (AC3), můžete výstup zapsat pouze do souboru ISO MP4.

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Související články
* [Pokročilé kódovací úlohy provést pomocí přizpůsobení předvoleb Media Encoderu Standard](media-services-custom-mes-presets-with-dotnet.md)
* [Kvóty a omezení](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/
