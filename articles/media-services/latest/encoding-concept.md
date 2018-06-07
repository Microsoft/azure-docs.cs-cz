---
title: Kódování v cloudu pomocí Azure Media Services | Microsoft Docs
description: Toto téma popisuje proces kódování při použití služby Azure Media Services
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/21/2018
ms.author: juliako
ms.openlocfilehash: e1c7536c59b110ae3dd753ff5f4b01195f8dadca
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654859"
---
# <a name="encoding-with-azure-media-services"></a>Kódování pomocí služby Azure Media Services

Azure Media Services umožňuje zakódovat vysoce kvalitní digitální mediální soubory do formátů, které může být přehráván na širokou škálu prohlížečích a zařízeních. Například můžete chtít datový proud, který zformátuje vašeho obsahu v HLS nebo MPEG DASH společnosti Apple. Služba Media Services také umožňuje analyzovat video nebo zvuk obsah. V tomto tématu získáte informace o tom, jak zakódovat svůj obsah pomocí služby Media Services v3.

Ke kódování s v3 Media Services, musíte vytvořit transformace a úlohy. Transformace definuje recepturách kódování nastavení a výstupy a úloha je instance receptury. Další informace najdete v tématu [transformuje a úlohy](transform-concept.md)

Při kódování službou Azure Media Services, můžete použít přednastavení říct kodér zpracovávání vstupní mediálních souborů. Například zadejte rozlišení videa nebo počet zvukových kanálů, které chcete v kódovaném obsahu. 

Jste mohli rychle začít s jedním z doporučených předdefinované předvolby podle odvětví osvědčené postupy nebo můžete vytvořit vlastní přednastavení pro vaše konkrétní požadavky scénáři nebo zařízení. 

Vyhledávat podrobnosti o kodér v [OpenAPI specifikace](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2018-03-30-preview). 

## <a name="built-in-presets"></a>Předdefinované předvolby

Služba Media Services aktuálně podporuje následující předdefinované přednastavení kódování:  

|**Název přednastavení**|**Scénář**|**Podrobnosti**|
|---|---|---|
|**AudioAnalyzerPreset**|Analýza zvuk|Přednastavení platí předem definované sadě analýza založená na AI operace, včetně řeči přepis. V současné době přednastavení podporuje zpracování obsahu pomocí jediné zvukové stopy.<br/>Můžete zadat jazyk pro datové části Zvuk ve vstupu pomocí BCP 47 formát 'language značky region' (například "en US"). Seznam podporovaných jazyků jsou, "en US", "en-GB., 'es-ES,, es-MX", "fr-FR,, it-IT,, ja-JP,, pt-BR,, zh-CN.|
|**VideoAnalyzerPreset**|Analýza audio a video|Extrahuje přehledy (bohaté metadata) z audia a videa a uloží soubor ve formátu JSON. Můžete zadat, zda chcete pouze rozbalit zvuk insights při zpracování souboru videa. Další informace najdete v tématu [analyzovat video](analyze-videos-tutorial-with-api.md).|
|**BuiltInStandardEncoderPreset**|Streamování|Slouží k nastavení integrované předvolby pro vstupní video pomocí standardní kodéru pro kódování. <br/>Aktuálně jsou podporovány následující přednastavení:<br/>**EncoderNamedPreset.AdaptiveStreaming** (doporučeno). Další informace najdete v tématu [automatické generování žebříku přenosovou rychlostí](autogen-bitrate-ladder.md).<br/>**EncoderNamedPreset.AACGoodQualityAudio** -vytvoří jednoho souboru MP4 obsahující pouze stereofonní zvuk kódovaný v 192 kb/s.<br/>**EncoderNamedPreset.H264MultipleBitrate1080p** -vytvoří sadu 8 soubory MP4 zarovnaný GOP od 6000 kb/s až 400 kb/s a stereofonní zvuk AAC. Řešení začíná 1080p a přejde do 360 p.<br/>**EncoderNamedPreset.H264MultipleBitrate720p** -vytvoří sadu 6 soubory MP4 zarovnaný GOP od 3400 kb/s až 400 kb/s a stereofonní zvuk AAC. Řešení začíná 720p a přejde do 360 p.<br/>**EncoderNamedPreset.H264MultipleBitrateSD** -vytvoří sadu 5 soubory MP4 zarovnaný GOP od 1600 kb/s až 400 kb/s a stereofonní zvuk AAC. Řešení začíná 480p a přejde do 360 p.<br/><br/>Další informace najdete v tématu [odesílání, kódování a vysílání datového proudu soubory](stream-files-tutorial-with-api.md).|
|**StandardEncoderPreset**|Streamování|Popisuje nastavení, která má být použita, pokud vstupní video pomocí standardní kodéru pro kódování. <br/>Použijte přednastavení při přizpůsobení přednastavení transformace. Další informace najdete v tématu [postup přizpůsobení transformace přednastavení](customize-encoder-presets-how-to.md).|

## <a name="custom-presets"></a>Vlastní přednastavení

Služba Media Services plně podporuje přizpůsobení všech hodnot v přednastavení ke splnění vašich potřeb kódování a požadavky. Můžete použít **StandardEncoderPreset** přednastavení při přizpůsobení přednastavení transformace. Pro podrobné vysvětlení a příklad, najdete v části [postup přizpůsobení přednastavení kodér](customize-encoder-presets-how-to.md).

## <a name="scaling-encoding-in-v3"></a>Škálování kódování v v3

V současné době zákazníci, mají nastavení RUs pomocí portálu Azure nebo rozhraní API pro AMS v2 (jak je popsáno v [škálování zpracování média](../previous/media-services-scale-media-processing-overview.md). 

## <a name="next-steps"></a>Další postup

### <a name="tutorials"></a>Kurzy

Následující tutorals ukazují, jak zakódovat svůj obsah pomocí služby Media Services:

* [Odesílání, kódovat a Streamovat využívající Azure Media Services](stream-files-tutorial-with-api.md)
* [Analýza videa pomocí služby Azure Media Services](analyze-videos-tutorial-with-api.md)

### <a name="code-samples"></a>Ukázky kódů

Následující ukázky kódu obsahovat kód, který ukazuje, jak ke kódování pomocí služby Media Services:

* [.NET Core](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore)
* [CLI 2.0](https://github.com/Azure/azure-docs-cli-python-samples/tree/master/media-services)

### <a name="sdks"></a>Sady SDK

Některé z následujících podporovaných SDK v3 Media Services můžete zakódovat svůj obsah.

* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
* [REST](https://docs.microsoft.com/rest/api/media/transforms)
* [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)
* [Java](https://docs.microsoft.com/java/api/overview/azure/mediaservices)
* [Python](https://docs.microsoft.com/python/api/overview/azure/media-services?view=azure-python)

