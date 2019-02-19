---
title: Kódování v cloudu s využitím Media Services – Azure | Dokumentace Microsoftu
description: Toto téma popisuje proces kódování při použití služby Azure Media Services
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/17/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 52e7fdf6de25300d4f78ee9822aca4ad83f646e9
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56408421"
---
# <a name="encoding-with-media-services"></a>Kódování pomocí Media Services

Azure Media Services můžete digitální vysoce kvalitní mediální soubory můžete zakódovat formáty, které můžete přehrát na širokou škálu prohlížečů a zařízení. Například můžete chtít svůj obsah streamovat ve formátu Apple HLS nebo MPEG DASH. Toto téma poskytuje pokyny o tom, jak zakódovat svůj obsah pomocí služby Media Services v3.

Ke kódování pomocí Media Services v3, je potřeba vytvořit [transformace](https://docs.microsoft.com/rest/api/media/transforms) a [úlohy](https://docs.microsoft.com/rest/api/media/jobs). Transformace definuje předpisu pro kódování nastavení a výstupů a úloha je instance předpisu. Další informace najdete v tématu [transformuje a úlohy](transforms-jobs-concept.md)

Kódování pomocí Media Services, použijete přednastavení kodér zjistit, jak by se měly zpracovat vstupními multimediálními soubory. Můžete například zadat rozlišení a/nebo počet zvukové kanály, které chcete v kódovaném obsahu. 

Můžete začít rychle s jedním z doporučených předdefinované předvolby založená na osvědčených postupech oboru nebo můžete vytvářet vlastní přednastavení cílit na konkrétní scénář nebo zařízení požadavky. Další informace najdete v tématu [kódovat s vlastní transformace](customize-encoder-presets-how-to.md). 

Počínaje lednem 2019 při kódování pomocí Media Encoder Standard na vytvářejí soubory MP4, je nový soubor .mpi generují a přidávají do výstupu Asset. Tento soubor MPI slouží pro zvýšení výkonu [dynamické balení](dynamic-packaging-overview.md) a streamování scénáře.

> [!NOTE]
> Nesmí změnit nebo odebrat soubor MPI či provést všechny závislosti ve své službě existence (nebo nemusíte) tyto souboru.

## <a name="built-in-presets"></a>Předdefinované předvolby

Služba Media Services momentálně podporuje následující předdefinované kódovací předvolby:  

### <a name="builtinstandardencoderpreset-preset"></a>Přednastavení BuiltInStandardEncoderPreset

[BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) slouží k nastavení předdefinované předvolby pro kódování vstupního videa se kodér úrovně Standard. 

Tyto předvolby se aktuálně podporují:

- **EncoderNamedPreset.AdaptiveStreaming** (doporučeno). Další informace najdete v tématu [automatické generování přenosových](autogen-bitrate-ladder.md).
- **EncoderNamedPreset.AACGoodQualityAudio** -vytvoří jeden soubor MP4 obsahující pouze stereo zvuk kódovaný v 192 kb/s.
- **EncoderNamedPreset.H264MultipleBitrate1080p** -vytvoří sadu 8 soubory MP4 zarovnaný GOP od 6000 kb/s až 400 kb/s a stereo AAC zvuku. Rozlišení začíná 1080p a platí až 360 p.
- **EncoderNamedPreset.H264MultipleBitrate720p** -vytvoří sadu 6 soubory MP4 zarovnaný GOP od 3400 kb/s až 400 kb/s a stereo AAC zvuku. Rozlišení začíná 720p a platí až 360 p.
- **EncoderNamedPreset.H264MultipleBitrateSD** -vytvoří sadu 5 soubory MP4 zarovnaný GOP od 1600 kb/s až 400 kb/s a stereo AAC zvuku. Rozlišení začíná 480p a platí až 360 p.<br/><br/>Další informace najdete v tématu [nahrávání, kódování a streamování souborů](stream-files-tutorial-with-api.md).

### <a name="standardencoderpreset-preset"></a>Přednastavení StandardEncoderPreset

[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) popisuje nastavení, která se použije při kódování vstupního videa se kodér úrovně Standard. Použijte přednastavení při přizpůsobování přednastavení transformace. 

#### <a name="custom-presets"></a>Vlastní předvolby

Služba Media Services plně podporuje všechny hodnoty v předvolbách pro splnění potřeb kódování a požadavky na přizpůsobení. Můžete použít **StandardEncoderPreset** přednastavení při přizpůsobování přednastavení transformace. Pro detailní vysvětlení a příklad najdete v části [přizpůsobení kódovací Předvolby](customize-encoder-presets-how-to.md).

## <a name="scaling-encoding-in-v3"></a>Škálování kódování ve verzi 3

V současné době zákazníci mají nastavení ru pomocí webu Azure portal nebo rozhraní API služby Media Services v2 (jak je popsáno v [škálování zpracování médií](../previous/media-services-scale-media-processing-overview.md). 

## <a name="next-steps"></a>Další postup

* [Transformace a úlohy](transforms-jobs-concept.md)
* [Nahrávání, kódování a streamování pomocí služby Media Services](stream-files-tutorial-with-api.md)
