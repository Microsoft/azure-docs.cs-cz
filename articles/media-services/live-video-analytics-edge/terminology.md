---
title: Live video Analytics v terminologii IoT EDG – Azure
description: Tento článek poskytuje přehled živé analýzy videí na IoT Edge terminologii.
ms.topic: conceptual
ms.date: 05/30/2020
ms.openlocfilehash: e3a77b69adf2241a4af2652db4edb6673a63b4f0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88690608"
---
# <a name="terminology"></a>Terminologie

Tento článek poskytuje přehled terminologie týkající se [živé analýzy videí v IoT Edge](overview.md).

## <a name="azure-media-services"></a>Azure Media Services

Azure Media Services je cloudová mediální platforma, která umožňuje sestavovat řešení médií. Další informace najdete v dokumentaci k [Azure Media Services](../latest/media-services-overview.md) .

## <a name="asset"></a>Prostředek

[Asset](../latest/assets-concept.md) je entita v Azure Media Services, která se mapuje na kontejner objektů BLOB v účtu služby Azure Storage, který je připojený k Media Servicesmu účtu. Všechny soubory přidružené k assetu se ukládají jako objekty BLOB v tomto kontejneru, když Media Services drží metadata (například název, popis, čas vytvoření) přidružený k assetu.

Live video Analytics na IoT Edge může vytvářet assety nebo přidávat data do stávajících assetů. To umožňuje scénáře nepřetržitého nahrávání a přehrávání videa založeného na událostech (pomocí zachycení videa na hraničním zařízení, nahrávání do Azure Media Services a přehrávání prostřednictvím stávajících možností streamování Azure Media Services).

## <a name="grpc"></a>gRPC

[gRPC](https://grpc.io/docs/guides/) je jazyk nezávislá, vysoce výkonné rozhraní vzdáleného volání procedur (RPC). Používá strukturovaná schémata založená na relacích prostřednictvím [vyrovnávací paměti protokolu 3](https://developers.google.com/protocol-buffers/docs/proto3) jako základní formát pro výměnu zpráv pro komunikaci.

## <a name="media-graph"></a>Graf médií

[Media Graph](media-graph-concept.md) umožňuje definovat, odkud se mají média zachytit, jak by měla být zpracována a kde se mají výsledky doručovat. Umožňuje definovat graf skládající se ze zdrojů, procesorů a uzlů jímky a díky tomu je možné vytvářet živé aplikace analýzy videí. Media Graph je podrobně popsán na stránce konceptu mediálního grafu.

## <a name="recording"></a>Nahrávání

V kontextu systému správy videí pro bezpečnostní kamery se záznam videa označuje procesem digitalizace videa z kamer a jejich uložení do souboru (nebo souborů) pro následné zobrazení prostřednictvím mobilních a prohlížečových aplikací. Záznam videa je možné kategorizovat do [nepřetržitého](continuous-video-recording-concept.md) záznamu videa a [záznamu videa založeného na událostech](event-based-video-recording-concept.md). Tyto informace jsou podrobněji vysvětleny na stránce koncept [záznamu videa](video-recording-concept.md) .

## <a name="rtsp"></a>PROTOKOL

[RTSP](https://tools.ietf.org/html/rfc2326) označuje protokol streamování Real-Time. Jedná se o protokol na úrovni aplikace pro kontrolu nad doručením dat pomocí vlastností v reálném čase. RTSP poskytuje rozšiřitelnou architekturu, která umožňuje řízené doručování dat v reálném čase, jako je třeba zvuk a video, na vyžádání. 

## <a name="streaming"></a>Streamování

Pokud máte na mobilním zařízení sledované video ze služeb, jako jsou Netflix, YouTube a další, máte streamované video. Přehrávání začne brzy po klepnutí na tlačítko "přehrát" (Pokud máte dostatečnou šířku pásma) a můžete hledat a zpátky na časové ose videa. Díky streamování je nápad doručovat pouze část videa, která je sledována, a nechat prohlížeč začít přehrávat video, zatímco data jsou stále přenášena ze serveru do klienta pro přehrávání. V souvislosti s Azure Media Services [streamování](https://en.wikipedia.org/wiki/Streaming_media) odkazuje na proces doručování médií z [Azure Media Services](../azure-media-player/azure-media-player-overview.md) na klienta streamování (například Azure Media Player). Můžete použít Azure Media Services ke streamování videa klientům pomocí standardních protokolů pro streamování médií založených na protokolu HTTP, jako je například [http Live Streaming (HLS)](https://developer.apple.com/streaming/) a [MPEG-pomlčka](https://dashif.org/about/). HLS podporuje aplikace Azure Media Player a webové přehrávače, jako je [JW Player](https://www.jwplayer.com/), [hls.js](https://github.com/video-dev/hls.js/), [VideoJS](https://videojs.com/), [Shaka Player Google](https://github.com/google/shaka-player), nebo můžete nakreslit nativně v mobilních aplikacích s využitím [Exoplayer](https://github.com/google/ExoPlayer) v Androidu a iOS [Foundation](https://developer.apple.com/av-foundation/). Formát MPEG-POMLČKa je také podporován nástrojem Azure Media Player. [na této stránce najdete seznam klientů](https://dashif.org/clients/). 

Díky použití [Media graphu](#media-graph)s k nahrávání videí do assetu v Azure Media Services můžete využívat možnosti streamování v Media Services, abyste mohli doručovat streamy videa v HLS a spojovníkech. Další informace najdete v článku o [přehrávání videa](video-playback-concept.md) .

## <a name="vms"></a>VZTAHUJE

[Virtuální počítače](https://en.wikipedia.org/wiki/Video_management_system) odkazují na systém správy videí. Tyto systémy se používají ke konfiguraci a řízení kamer CCTV, k zaznamenávání a nahrávání videí z nich. Tyto systémy také poskytují klientské aplikace pro přehrávání zaznamenaného videa.

## <a name="next-steps"></a>Další kroky

[Mediální grafy](media-graph-concept.md)
