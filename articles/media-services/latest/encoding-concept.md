---
title: Kódování v cloudu pomocí Azure Media Services | Dokumentace Microsoftu
description: Toto téma popisuje proces kódování při použití služby Azure Media Services
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: 452502d5d6a0a35f642de7e14b2a7ee7fc573bfa
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49378665"
---
# <a name="encoding-with-azure-media-services"></a>Kódování pomocí Azure Media Services

Azure Media Services můžete digitální vysoce kvalitní mediální soubory můžete zakódovat formáty, které můžete přehrát na širokou škálu prohlížečů a zařízení. Například můžete chtít svůj obsah streamovat ve formátu Apple HLS nebo MPEG DASH. Služba Media Services také umožňuje analyzovat vaše videa nebo zvukový obsah. V tomto tématu poskytují pokyny o tom, jak zakódovat svůj obsah pomocí služby Media Services v3.

Kódování pomocí Media Services v3, budete muset vytvořit transformace a úlohy. Transformace definuje předpisu pro kódování nastavení a výstupů a úloha je instance předpisu. Další informace najdete v tématu [transformuje a úlohy](transform-concept.md)

Kódování pomocí Azure Media Services, použijete přednastavení kodér zjistit, jak by se měly zpracovat vstupními multimediálními soubory. Můžete například zadat rozlišení a/nebo počet zvukové kanály, které chcete v kódovaném obsahu. 

Můžete začít rychle s jedním z doporučených předdefinované předvolby založená na osvědčených postupech oboru nebo můžete vytvářet vlastní přednastavení cílit na konkrétní scénář nebo zařízení požadavky. Další informace najdete v tématu [kódování pomocí Azure Media Services](encoding-concept.md). 

## <a name="built-in-presets"></a>Předdefinované předvolby

Služba Media Services momentálně podporuje následující předdefinované kódovací předvolby:  

|**Název předvolby**|**Scénář**|**Podrobnosti**|
|---|---|---|
|**AudioAnalyzerPreset**|Analýza zvuku|Přednastavení platí předem definovanou sadu operace analýzy založené na AI včetně určené k transkripci řeči. V současné době podporuje přednastavení zpracování obsahu pomocí jednoho zvuková stopa.<br/>Můžete určit jazyk pro zvuk datovou část vstup ve formátu BCP-47 "jazyk značky region" (například "en US"). Seznam podporovaných jazyků, které jsou, "en US", "en-GB", "es-ES", "es-MX", "fr-FR", "it-IT", "ja-JP", "pt-BR", "zh-CN".|
|**VideoAnalyzerPreset**|Analýza zvuku a videa|Extrahuje insights (bohatých metadat) z audio a video a uloží soubor formátu JSON. Můžete určit, zda chcete pouze poznatky zvuku při zpracování souboru videa. Další informace najdete v tématu [analyzovat video](analyze-videos-tutorial-with-api.md).|
|**BuiltInStandardEncoderPreset**|Streamování|Slouží k nastavení předdefinované předvolby pro kódování vstupního videa se kodér úrovně Standard. <br/>Tyto předvolby se aktuálně podporují:<br/>**EncoderNamedPreset.AdaptiveStreaming** (doporučeno). Další informace najdete v tématu [automatické generování přenosových](autogen-bitrate-ladder.md).<br/>**EncoderNamedPreset.AACGoodQualityAudio** -vytvoří jeden soubor MP4 obsahující pouze stereo zvuk kódovaný v 192 kb/s.<br/>**EncoderNamedPreset.H264MultipleBitrate1080p** -vytvoří sadu 8 soubory MP4 zarovnaný GOP od 6000 kb/s až 400 kb/s a stereo AAC zvuku. Rozlišení začíná 1080p a platí až 360 p.<br/>**EncoderNamedPreset.H264MultipleBitrate720p** -vytvoří sadu 6 soubory MP4 zarovnaný GOP od 3400 kb/s až 400 kb/s a stereo AAC zvuku. Rozlišení začíná 720p a platí až 360 p.<br/>**EncoderNamedPreset.H264MultipleBitrateSD** -vytvoří sadu 5 soubory MP4 zarovnaný GOP od 1600 kb/s až 400 kb/s a stereo AAC zvuku. Rozlišení začíná 480p a platí až 360 p.<br/><br/>Další informace najdete v tématu [nahrávání, kódování a streamování souborů](stream-files-tutorial-with-api.md).|
|**StandardEncoderPreset**|Streamování|Popisuje nastavení, která se použije při kódování vstupního videa se kodér úrovně Standard. <br/>Použijte přednastavení při přizpůsobování přednastavení transformace. Další informace najdete v tématu [přizpůsobení transformace přednastavení](customize-encoder-presets-how-to.md).|

## <a name="custom-presets"></a>Vlastní předvolby

Služba Media Services plně podporuje všechny hodnoty v předvolbách pro splnění potřeb kódování a požadavky na přizpůsobení. Můžete použít **StandardEncoderPreset** přednastavení při přizpůsobování přednastavení transformace. Pro detailní vysvětlení a příklad najdete v části [přizpůsobení kódovací Předvolby](customize-encoder-presets-how-to.md).

## <a name="scaling-encoding-in-v3"></a>Škálování kódování ve verzi 3

V současné době zákazníci mají nastavení ru pomocí webu Azure portal nebo rozhraní API pro AMS v2 (jak je popsáno v [škálování zpracování médií](../previous/media-services-scale-media-processing-overview.md). 

## <a name="next-steps"></a>Další postup

### <a name="tutorials"></a>Kurzy

Následující tutorals ukazují, jak zakódovat svůj obsah pomocí služby Media Services:

* [Nahrávání, kódování a streamování využívajícího službu Azure Media Services](stream-files-tutorial-with-api.md)
* [Analýza videa pomocí služby Azure Media Services](analyze-videos-tutorial-with-api.md)

### <a name="code-samples"></a>Ukázky kódů

Následující ukázky kódu obsahovat kód, který ukazuje, jak kódování pomocí Media Services:

* [.NET Core](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore)
* [Azure CLI](https://github.com/Azure/azure-docs-cli-python-samples/tree/master/media-services)

### <a name="sdks"></a>Sady SDK

Zakódovat svůj obsah můžete použít některý z následujících podporovaných Media Services v3 sad SDK.

* [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
* [REST](https://docs.microsoft.com/rest/api/media/transforms)
* [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)
* [Java](https://docs.microsoft.com/java/api/overview/azure/mediaservices)
* [Python](https://docs.microsoft.com/python/api/overview/azure/media-services?view=azure-python)

