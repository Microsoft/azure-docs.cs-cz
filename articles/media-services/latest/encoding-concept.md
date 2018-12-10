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
ms.date: 12/08/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 7c16fc61a8654fa6b7236b9c5252ed4874787d50
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53141743"
---
# <a name="encoding-with-media-services"></a>Kódování pomocí Media Services

Azure Media Services můžete digitální vysoce kvalitní mediální soubory můžete zakódovat formáty, které můžete přehrát na širokou škálu prohlížečů a zařízení. Například můžete chtít svůj obsah streamovat ve formátu Apple HLS nebo MPEG DASH. Toto téma poskytuje pokyny o tom, jak zakódovat svůj obsah pomocí služby Media Services v3.

Kódování pomocí Media Services v3, budete muset vytvořit transformace a úlohy. Transformace definuje předpisu pro kódování nastavení a výstupů a úloha je instance předpisu. Další informace najdete v tématu [transformuje a úlohy](transform-concept.md)

Kódování pomocí Media Services, použijete přednastavení kodér zjistit, jak by se měly zpracovat vstupními multimediálními soubory. Můžete například zadat rozlišení a/nebo počet zvukové kanály, které chcete v kódovaném obsahu. 

Můžete začít rychle s jedním z doporučených předdefinované předvolby založená na osvědčených postupech oboru nebo můžete vytvářet vlastní přednastavení cílit na konkrétní scénář nebo zařízení požadavky. Další informace najdete v tématu [kódovat s vlastní transformace](customize-encoder-presets-how-to.md). 

## <a name="built-in-presets"></a>Předdefinované předvolby

Služba Media Services momentálně podporuje následující předdefinované kódovací předvolby:  

|**Název předvolby**|**Scénář**|**Podrobnosti**|
|---|---|---|
|**BuiltInStandardEncoderPreset**|Streamování|Slouží k nastavení předdefinované předvolby pro kódování vstupního videa se kodér úrovně Standard. <br/>Tyto předvolby se aktuálně podporují:<br/>**EncoderNamedPreset.AdaptiveStreaming** (doporučeno). Další informace najdete v tématu [automatické generování přenosových](autogen-bitrate-ladder.md).<br/>**EncoderNamedPreset.AACGoodQualityAudio** -vytvoří jeden soubor MP4 obsahující pouze stereo zvuk kódovaný v 192 kb/s.<br/>**EncoderNamedPreset.H264MultipleBitrate1080p** -vytvoří sadu 8 soubory MP4 zarovnaný GOP od 6000 kb/s až 400 kb/s a stereo AAC zvuku. Rozlišení začíná 1080p a platí až 360 p.<br/>**EncoderNamedPreset.H264MultipleBitrate720p** -vytvoří sadu 6 soubory MP4 zarovnaný GOP od 3400 kb/s až 400 kb/s a stereo AAC zvuku. Rozlišení začíná 720p a platí až 360 p.<br/>**EncoderNamedPreset.H264MultipleBitrateSD** -vytvoří sadu 5 soubory MP4 zarovnaný GOP od 1600 kb/s až 400 kb/s a stereo AAC zvuku. Rozlišení začíná 480p a platí až 360 p.<br/><br/>Další informace najdete v tématu [nahrávání, kódování a streamování souborů](stream-files-tutorial-with-api.md).|
|**StandardEncoderPreset**|Streamování|Popisuje nastavení, která se použije při kódování vstupního videa se kodér úrovně Standard. <br/>Použijte přednastavení při přizpůsobování přednastavení transformace. Další informace najdete v tématu [přizpůsobení transformace přednastavení](customize-encoder-presets-how-to.md).|

## <a name="custom-presets"></a>Vlastní předvolby

Služba Media Services plně podporuje všechny hodnoty v předvolbách pro splnění potřeb kódování a požadavky na přizpůsobení. Můžete použít **StandardEncoderPreset** přednastavení při přizpůsobování přednastavení transformace. Pro detailní vysvětlení a příklad najdete v části [přizpůsobení kódovací Předvolby](customize-encoder-presets-how-to.md).

## <a name="scaling-encoding-in-v3"></a>Škálování kódování ve verzi 3

V současné době zákazníci mají nastavení ru pomocí webu Azure portal nebo rozhraní API služby Media Services v2 (jak je popsáno v [škálování zpracování médií](../previous/media-services-scale-media-processing-overview.md). 

## <a name="next-steps"></a>Další postup

### <a name="tutorials"></a>Kurzy

V následujícím kurzu ukazuje, jak zakódovat svůj obsah pomocí služby Media Services:

* [Nahrávání, kódování a streamování pomocí služby Media Services](stream-files-tutorial-with-api.md)

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

