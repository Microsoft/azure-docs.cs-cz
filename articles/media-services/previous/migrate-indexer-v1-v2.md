---
title: Migrace z indexeru v1 a v2 do videoindexeru Azure Media Services | Dokumenty společnosti Microsoft
description: Toto téma popisuje, jak migrovat z Azure Media Indexer v1 a v2 na Azure Media Services Video Indexer.
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
ms.date: 09/20/2019
ms.author: juliako
ms.openlocfilehash: 2268c074480f99ca23117ca2ffd2c87c1dbb10a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513231"
---
# <a name="migrate-from-media-indexer-and-media-indexer-2-to-video-indexer"></a>Migrace z média Indexer a Media Indexer 2 na Video Indexer

Mediální procesor [Azure Media Indexer](media-services-index-content.md) a mediální procesory [Azure Media Indexer 2 Preview](media-services-process-content-with-indexer2.md) jsou vyřazeny. Data vyřazení naleznete v tomto tématu [starších součástí.](legacy-components.md) [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) nahradí tyto starší mediální procesory.

Azure Media Services Video Indexer je integrovaný na Azure Media Analytics, Azure Cognitive Search, Cognitive Services (jako je rozhraní API pro rozpoznávání tváře, Microsoft Translator, rozhraní API pro počítačové zpracování obrazu a vlastní služba řeči). Umožňuje extrahovat přehledy z videí pomocí zvukových modelů a videomodelů Video Indexeru. Chcete-li zjistit, v jakých scénářích lze indexer videa použít, jaké funkce nabízí a jak začít, přečtěte si informace o [modelech videa a zvuku videoindexeru](../video-indexer/video-indexer-overview.md). 

Přehledy z video souborů a zvukových souborů můžete extrahovat pomocí [přednastavení analyzátoru Azure Media Services v3](../latest/analyzing-video-audio-files-concept.md) nebo přímo pomocí [api pro indexer videa](https://api-portal.videoindexer.ai/). V současné době dochází k překrývání funkcí nabízených api video indexeru a mediální služby v3 API.

> [!NOTE]
> Chcete-li pochopit, kdy chcete použít přednastavení analyzátoru Video Indexer vs. Media Services, podívejte se na [srovnávací dokument](../video-indexer/compare-video-indexer-with-media-services-presets.md). 

Tento článek popisuje kroky pro migraci z Azure Media Indexer a Azure Media Indexer 2 do Azure Media Services Video Indexer.  

## <a name="migration-options"></a>Možnosti migrace 

|Pokud požadujete  |pak |
|---|---|
|řešení, které poskytuje přepis řeči na text pro libovolný formát mediálního souboru ve formátech souborů s uzavřeným titulkem: VTT, SRT nebo TTML<br/>stejně jako další audio postřehy, jako jsou: klíčová slova, odvozování tématu, akustické události, diarizace reproduktorů, extrakce a překlad entit| aktualizujte své aplikace tak, aby používaly funkce Azure Video Indexer prostřednictvím rozhraní API REKTOVního nástroje Video Indexer v2 nebo azure media services v3 Audio Analyzer.|
|funkce převodu řeči na text| používat rozhraní Api řeči služby Cognitive Services přímo.|  

## <a name="getting-started-with-video-indexer"></a>Začínáme s video indexerem

V následující části se odkazuje na příslušné odkazy: [Jak mohu začít s Video Indexer?](https://docs.microsoft.com/azure/media-services/video-indexer/video-indexer-overview#how-can-i-get-started-with-video-indexer) 

## <a name="getting-started-with-media-services-v3-apis"></a>Začínáme s množiny API služby Media Services v3

Rozhraní Azure Media Services v3 API umožňuje extrahovat přehledy z vašich video a zvukových souborů prostřednictvím [přednastavení analyzátoru Azure Media Services v3](../latest/analyzing-video-audio-files-concept.md). 

**AudioAnalyzerPreset** umožňuje extrahovat více zvukových poznatků ze zvukového souboru nebo videosouboru. Výstup obsahuje soubor VTT nebo TTML pro zvukový přepis a soubor JSON (se všemi dalšími zvukovými poznatky). Zvukové přehledy zahrnují klíčová slova, indexování řečníků a analýzu nálady řeči. AudioAnalyzerPreset také podporuje detekci jazyka pro konkrétní jazyky. Podrobné informace naleznete v [tématu Transformace](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset).

### <a name="get-started"></a>Začínáme

Chcete-li začít, podívejte se na:

* [Tutorial](../latest/analyze-videos-tutorial-with-api.md)
* Ukázky audioanalyzer preset: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer) nebo [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)
* VideoAnalyzer Preset ukázky: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer) nebo [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/VideoAnalytics/VideoAnalyzer)

## <a name="getting-started-with-cognitive-services-speech-services"></a>Začínáme se službami Cognitive Services Speech Services

[Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/) poskytuje službu převodu řeči na text, která přepisuje zvukové proudy na text v reálném čase, který vaše aplikace, nástroje nebo zařízení můžou využívat nebo zobrazovat. Převod řeči na text můžete použít k [přizpůsobení vlastního akustického modelu, jazykového modelu nebo modelu výslovnosti](../../cognitive-services/speech-service/how-to-custom-speech-train-model.md). Další informace naleznete [v tématu Cognitive Services speech-to-text](../../cognitive-services/speech-service/speech-to-text.md). 

> [!NOTE] 
> Služba převodu řeči na text nepřebírá formáty souborů videa a přebírá pouze [určité zvukové formáty](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-speech-to-text#audio-formats). 

Další informace o službě převodu textu na řeč a o tom, jak začít, naleznete v [tématu Co je převod řeči na text?](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-to-text)

## <a name="known-differences-from-deprecated-services"></a>Známé rozdíly od zastaralých služeb 

Zjistíte, že Video Indexer, Azure Media Services v3 AudioAnalyzerPreset a služby Cognitive Services Speech Services jsou spolehlivější a vytvářejí kvalitnější výstup než vyřazené procesory Azure Media Indexer 1 a Azure Media Indexer 2.  

Některé známé rozdíly zahrnují: 

* Služba Speech Services služby Cognitive Services nepodporuje extrakci klíčových slov. Nicméně, Video Indexer a Media Services v3 AudioAnalyzerPreset oba nabízejí robustnější sadu klíčových slov ve formátu souboru JSON. 

## <a name="need-help"></a>Potřebujete pomoc?

Lístek podpory můžete otevřít tak, že přejdete na [Novou žádost o podporu.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-steps"></a>Další kroky

* [Starší verze komponent](legacy-components.md)
* [Stránka s cenami](https://azure.microsoft.com/pricing/details/media-services/#encoding)


