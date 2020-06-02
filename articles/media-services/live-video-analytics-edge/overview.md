---
title: Co je Live video Analytics na IoT Edge – Azure
description: V tomto tématu najdete přehled živé analýzy videí na IoT Edge. Platforma nabízí možnost, kterou můžete použít k vylepšení vašich řešení IoT. Můžete například zachytit, nahrávat, analyzovat živé video a publikovat výsledky (video a video Analytics) do služeb Azure.
ms.topic: overview
ms.date: 05/27/2020
ms.openlocfilehash: 81a67322d0a5e524d75b9bf3e481be7157e09e63
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2020
ms.locfileid: "84266793"
---
# <a name="what-is-live-video-analytics-on-iot-edge-preview"></a>Co je Live video Analytics na IoT Edge? (Preview)

Live video Analytics na IoT Edge poskytuje platformu pro sestavování inteligentních aplikací pro video, které přesahují hranice a Cloud. Platforma nabízí možnost zaznamenávat, nahrávat a analyzovat živé video spolu s publikováním výsledků (video a video Analytics) do služeb Azure (v cloudu a/nebo na hraničních zařízeních). Platforma se dá použít k vylepšení řešení IoT pomocí video Analytics. Živé analýzy videí o funkcích IoT Edge se dají kombinovat s dalšími moduly Azure IoT Edge, jako je Stream Analytics na IoT Edge, Cognitive Services na IoT Edge a také službami Azure v cloudu, jako je Media Services, centrum událostí, Cognitive Services atd. vytváření výkonných hybridních aplikací (například Edge + Cloud).

Live video Analytics na IoT Edge je navržená tak, aby byla rozšiřitelná platforma, která umožňuje propojit různé moduly pro analýzu videa (například kontejnery vydaných služeb, vlastní hraniční moduly, které jste vytvořili s vašimi vlastními daty), a používat je k analýze živého videa, aniž byste se museli starat o složitost sestavování a spouštění živého video kanálu.

## <a name="accelerate-iot-solutions-development"></a>Urychlení vývoje řešení IoT 

Řešení IoT, která kombinují video Analytics s signály z jiných senzorů IoT nebo podnikových dat, vám můžou přispět k automatizaci nebo částečně automatizaci obchodních rozhodnutí. výsledkem jsou vylepšení produktivity. Live video Analytics na IoT Edge umožňuje vytvářet taková řešení rychleji. Můžete se zaměřit na vytváření modulů a logiky analýzy videa, které jsou specifické pro vaši firmu, a umožnit tak platformě zjednodušit správu a spouštění video kanálu.

Díky živé analýze videí na IoT Edge můžete dál používat své [kamery CCTV](https://en.wikipedia.org/wiki/Closed-circuit_television_camera) s vašimi stávajícími [systémy pro správu videí](https://en.wikipedia.org/wiki/Video_management_system) a sestavovat aplikace pro video Analytics nezávisle. Analýza živých videí na IoT Edge se dá použít společně se sadami SDK pro počítačové zpracování a sady nástrojů k vytváření špičkových řešení IoT. Tento diagram znázorňuje tuto.

![Vývoj řešení IoT s využitím živých videí Analytics na IoT Edge](./media/overview/product-diagram.svg)

## <a name="supported-environments"></a>Podporovaná prostředí

Podporovaná prostředí Linux AMD64 a x64 jsou podporována.

## <a name="get-started"></a>Začínáme

Přečtěte si následující články o konceptech a potom si pomocí rychlého startu spusťte detekci pohybu na živém informačním kanálu videa.

### <a name="concepts"></a>Koncepty

* [Media Graph](media-graph-concept.md)
* [Záznam videa](video-recording-concept.md)
* [Přehrávání videa](video-playback-concept.md)
* [Nepřetržité nahrávání videa](continuous-video-recording-concept.md)
* [Nahrávání videa založeného na událostech](event-based-video-recording-concept.md)
* [Živé video analýza bez nahrávání videa](analyze-live-video-concept.md)

## <a name="next-steps"></a>Další kroky

* Postupujte podle pokynů v tématu [rychlý Start: spuštění živé analýzy videí pomocí vlastního modelu](use-your-model-quickstart.md) a podívejte se, jak můžete spustit detekci pohybů na živém informačním kanálu videa.
* Kontrola [terminologie](terminology.md)
* Prozkoumejte [materiál Open Source Analytics s dynamickým videem](https://github.com/Azure/live-video-analytics)

