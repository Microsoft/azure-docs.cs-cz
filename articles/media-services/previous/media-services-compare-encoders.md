---
title: Porovnání Azure na vyžádání média kodéry | Microsoft Docs
description: Toto téma porovnává kódování možnosti **Media Encoder Standard** a **Media Encoder Premium pracovního postupu**.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: a79437c0-4832-423a-bca8-82632b2c47cc
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: juliako;anilmur
ms.openlocfilehash: 13e8b3bcb7de4093116b3e2198b210950ac16d78
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="comparison-of-azure-on-demand-media-encoders"></a>Porovnání Azure na vyžádání média kodéry

Toto téma porovnává kódování možnosti **Media Encoder Standard** a **Media Encoder Premium pracovního postupu**.

## <a name="video-and-audio-processing-capabilities"></a>Možnosti zpracování videa a zvuku

Následující tabulka porovnává funkce mezi Media Encoder Standard (MES) a Media Encoder Premium pracovního postupu (MEPW). 

|Schopnost|Media Encoder Standard|Pracovní postup kodéru Media Encoder Premium|
|---|---|---|
|Použít podmíněnou logiku při kódování<br/>(například pokud vstupní HD, pak kódování 5.1 zvuku)|Ne|Ano|
|Titulky|Ne|[Ano](media-services-premium-workflow-encoder-formats.md#closed_captioning)|
|[Oprava Dolby® Professional hlasitosti](http://www.dolby.com/us/en/technologies/dolby-professional-loudness-solutions.pdf)<br/> pomocí dialogu Intelligence™|Ne|Ano|
|Odstraňování prokládání, inverzní telecine|Basic|Vysílání kvality|
|Detekovat a odstraňovat černé ohraničení <br/>(pillarboxes, letterboxes)|Ne|Ano|
|Vytváření miniatur|[Ano](media-services-dotnet-generate-thumbnail-with-mes.md)|[Ano](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)|
|Výstřižek nebo oříznutí a ve hřbetu videí|[Ano](media-services-advanced-encoding-with-mes.md#trim_video)|Ano|
|Překryvy zvuk nebo video|[Ano](media-services-advanced-encoding-with-mes.md#overlay)|[Ano](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-1--overlay-an-image-on-top-of-the-video)|
|Překryvy grafiky|Z bitové kopie zdrojů|Ze zdroje bitové kopie a text.|
|Zvuk vícejazyčných stop|Omezená|[Ano](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-2--multiple-audio-language-encoding)|

## <a id="billing"></a>Fakturace měření používá každý kodér
| Název procesoru média | Platných cenách | Poznámky |
| --- | --- | --- |
| **Media Encoder Standard** |KODÉR |Kódování úlohy se bude účtovat poplatek podle celková doba trvání v minutách, všechny soubory médií vytvořil jako výstup rychlostí zadaný [sem][1], ve sloupci KODÉR. |
| **Pracovní postup kodéru Media Encoder Premium** |KODÉR ÚROVNĚ PREMIUM |Kódování úlohy se bude účtovat poplatek podle celková doba trvání v minutách, všechny soubory médií vytvořil jako výstup rychlostí zadaný [sem][1], ve sloupci KODÉR úrovně PREMIUM. |

## <a name="input-containerfile-formats"></a>Zadejte kontejner nebo formátů
| Zadejte kontejner nebo formátů | Media Encoder Standard | Pracovní postup kodéru Media Encoder Premium |
| --- | --- | --- |
| Adobe® Flash® F4V |Ano |Ano |
| MXF/SMPTE 377M |Ano |Ano |
| GXF |Ano |Ano |
| Datové proudy MPEG-2 Transport |Ano |Ano |
| Datové proudy MPEG-2 programu |Ano |Ano |
| MPEG-4/MP4 |Ano |Ano |
| Windows Media/amp |Ano |Ano |
| AVI (nekomprimované 8bitové/10 bitů) |Ano |Ano |
| 3GPP/3GPP2 |Ano |Ne |
| Formát souborů Smooth datového proudu (PIFF 1.3) |Ano |Ne |
| [Microsoft digitální Video Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) |Ano |Ne |
| Matroska/WBEM |Ano |Ne |
| QuickTime (MOV) |Ano |Ne |

## <a name="input-video-codecs"></a>Vstupní video kodeky
| Vstupní Video kodeky | Media Encoder Standard | Pracovní postup kodéru Media Encoder Premium |
| --- | --- | --- |
| AVC 8-bit nebo 10-bit, až 4:2:2, včetně AVCIntra |8 bitů 4:2:0, 4:2:2 |Ano |
| Avid DNxHD (v MXF) |Ano |Ano |
| DVCPro/DVCProHD (v MXF) |Ano |Ano |
| JPEG2000 |Ano |Ano |
| MPEG-2 (až 422 profil a vysokou úroveň, včetně například XDCAM, XDCAM HD, XDCAM IMX, CableLabs® a D10 variant) |Až 422 profilu |Ano |
| MPEG-1 |Ano |Ano |
| Windows Media Video/VC-1 |Ano |Ano |
| Canopus Ústředí/HQX |Ne |Ne |
| MPEG-4 část 2 |Ano |Ne |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Ano |Ne |
| Apple ProRes 422 |Ano |Ne |
| Apple ProRes 422 LT |Ano |Ne |
| Ústředí ProRes 422 Apple |Ano |Ne |
| Apple ProRes Proxy |Ano |Ne |
| Apple ProRes 4444 |Ano |Ne |
| Apple ProRes 4444 XQ |Ano |Ne |

## <a name="input-audio-codecs"></a>Vstupní zvukových kodeků
| Vstupní zvukových kodeků | Media Encoder Standard | Pracovní postup kodéru Media Encoder Premium |
| --- | --- | --- |
| AES (SMPTE 331 M a 302 M, AES3-2003) |Ne |Ano |
| Dolby® E |Ne |Ano |
| Digitální Dolby® (AC3) |Ne |Ano |
| Digitální Dolby® Plus (E-AC3) |Ne |Ano |
| AAC (AAC-LC, AAC HE a AAC-HEv2; až 5.1) |Ano |Ano |
| MPEG vrstvy 2 |Ano |Ano |
| MP3 (MPEG-1 zvuk vrstvy 3) |Ano |Ano |
| Zvuk média systému Windows |Ano |Ano |
| WAV NEBO PCM |Ano |Ano |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Ano |Ne |
| [Díle](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |Ano |Ne |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Ano |Ne |

## <a name="output-containerfile-formats"></a>Výstup kontejneru nebo formátů
| Výstup kontejneru nebo formátů | Media Encoder Standard | Pracovní postup kodéru Media Encoder Premium |
| --- | --- | --- |
| Adobe® Flash® F4V |Ne |Ano |
| MXF (OP1a, XDCAM a AS02) |Ne |Ano |
| DPP (včetně AS11) |Ne |Ano |
| GXF |Ne |Ano |
| MPEG-4/MP4 |Ano |Ano |
| MPEG-TS |Ano |Ano |
| Windows Media/amp |Ne |Ano |
| AVI (nekomprimované 8bitové/10 bitů) |Ne |Ano |
| Formát souborů Smooth datového proudu (PIFF 1.3) |Ne |Ano |

## <a name="output-video-codecs"></a>Výstup kodeky videa
| Výstup kodeky videa | Media Encoder Standard | Pracovní postup kodéru Media Encoder Premium |
| --- | --- | --- |
| AVC (H.264; 8bitové; až profil vysokou úroveň 5.2; 4 kB Ultra HD; Uvnitř AVC) |Pouze 8 bitů 4:2:0 |Ano |
| HEVC (H.265; 8bitové a 10-bit;)  |Ne |Ano |
| Avid DNxHD (v MXF) |Ne |Ano |
| MPEG-2 (až 422 profil a vysokou úroveň, včetně například XDCAM, XDCAM HD, XDCAM IMX, CableLabs® a D10 variant) |Ne |Ano |
| MPEG-1 |Ne |Ano |
| Windows Media Video/VC-1 |Ne |Ano |
| Vytváření miniatur JPEG |Ano |Ano |
| Vytváření miniatur PNG |Ano |Ano |
| Vytváření miniatur BMP |Ano |Ne |

## <a name="output-audio-codecs"></a>Výstup zvukových kodeků
| Výstup zvukových kodeků | Media Encoder Standard | Pracovní postup kodéru Media Encoder Premium |
| --- | --- | --- |
| AES (SMPTE 331 M a 302 M, AES3-2003) |Ne |Ano |
| Digitální Dolby® (AC3) |Ne |Ano |
| Dolby® Digital Plus (E-AC3) až 7.1 |Ne |Ano |
| AAC (AAC-LC, AAC HE a AAC-HEv2; až 5.1) |Ano |Ano |
| MPEG vrstvy 2 |Ne |Ano |
| MP3 (MPEG-1 zvuk vrstvy 3) |Ne |Ano |
| Zvuk média systému Windows |Ne |Ano |

>[!NOTE]
>Pokud jste dekódovat digitální® Dolby (AC3), výstup lze zapsat pouze do souboru ISO MP4.

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Související články
* [Přizpůsobením Media Encoder Standard přednastavení provádět pokročilé úlohy kódování](media-services-custom-mes-presets-with-dotnet.md)
* [Kvóty a omezení](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/
