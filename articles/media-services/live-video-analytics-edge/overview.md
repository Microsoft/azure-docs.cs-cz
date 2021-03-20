---
title: Co je Live video Analytics na IoT Edge – Azure
description: V tomto tématu najdete přehled živé analýzy videí na IoT Edge. Platforma nabízí možnost, kterou můžete použít k vylepšení vašich řešení IoT. Můžete například zachytit, nahrávat, analyzovat živé video a publikovat výsledky (video a video Analytics) do služeb Azure.
ms.topic: overview
ms.date: 05/27/2020
ms.openlocfilehash: b9b8726c5e8ad6850e05aeee48fccabee703080e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "90904357"
---
# <a name="what-is-live-video-analytics-on-iot-edge-preview"></a>Co je Live video Analytics na IoT Edge? (Preview)

Analýza živého videa ve službě IoT Edge poskytuje platformu pro vytváření inteligentních videoaplikací, které fungují na hraničních zařízeních i v cloudu. Tato platforma nabízí možnost zachytávat, nahrávat a analyzovat živé video a také publikovat výsledky (videa nebo analýzy videa) ve službách Azure (v cloudu nebo na hraničních zařízeních). Platforma se dá použít k vylepšení řešení IoT pomocí video Analytics. Živé analýzy videí o funkcích IoT Edge se dají kombinovat s dalšími moduly Azure IoT Edge, jako je Stream Analytics na IoT Edge, Cognitive Services na IoT Edge a také službami Azure v cloudu, jako je Media Services, centrum událostí, Cognitive Services atd. vytváření výkonných hybridních aplikací (například Edge + Cloud).

Live video Analytics na IoT Edge je navržená tak, aby byla rozšiřitelná platforma, která umožňuje propojit různé moduly pro analýzu videa (například kontejnery vydaných služeb, vlastní hraniční moduly, které jste vytvořili s vašimi vlastními daty), a používat je k analýze živého videa, aniž byste se museli starat o složitost sestavování a spouštění živého video kanálu.

## <a name="accelerate-iot-solutions-development"></a>Urychlení vývoje řešení IoT 

Řešení IoT, která kombinují analýzu videa se signály z dalších senzorů IoT nebo s obchodními daty vám můžou pomoct automatizovat nebo částečně automatizovat obchodní rozhodování a tím zvýšit produktivitu. Analýza živého videa ve službě IoT Edge umožňuje vytvářet taková řešení rychleji. Můžete se soustředit na vytváření modulů analýzy videa a logiky specifické pro vaši firmu a platforma se na pozadí postará o složitou správu a spouštění kanálu videa.

Díky živé analýze videí na IoT Edge můžete dál používat své [kamery CCTV](https://en.wikipedia.org/wiki/Closed-circuit_television_camera) s vašimi stávajícími [systémy pro správu videí](https://en.wikipedia.org/wiki/Video_management_system) a sestavovat aplikace pro video Analytics nezávisle. Analýzu živého videa ve službě IoT Edge je možné použít v kombinaci se sadami nástrojů a sadami SDK pro počítačové zpracování obrazu a vytvářet tak špičková řešení IoT. Následující diagram to znázorňuje.

![Vývoj řešení IoT s využitím živých videí Analytics na IoT Edge](./media/overview/product-diagram.svg)

## <a name="supported-environments"></a>Podporovaná prostředí

Jsou podporována prostředí Linux X86-64 a ARM64.
> [!NOTE]
> Podpora pro zařízení ARM64 je k dispozici v sestavách `1.0.4` a novějších verzích.
> Podpora spouštění Azure IoT Edge runtime na zařízeních ARM64 je ve [verzi Public Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="get-started"></a>Začínáme

Přečtěte si následující články o konceptech a potom si pomocí rychlého startu spusťte detekci pohybu na živém informačním kanálu videa.

### <a name="concepts"></a>Koncepty

* [Graf médií](media-graph-concept.md)
* [Záznam videa](video-recording-concept.md)
* [Přehrávání videa](video-playback-concept.md)
* [Nepřetržité nahrávání videa](continuous-video-recording-concept.md)
* [Nahrávání videa na základě událostí](event-based-video-recording-concept.md)
* [Analýza živého videa bez nahrávání videa](analyze-live-video-concept.md)

## <a name="next-steps"></a>Další kroky

* Postupujte podle pokynů v tématu [rychlý Start: spuštění živé analýzy videí pomocí vlastního modelu](use-your-model-quickstart.md) a podívejte se, jak můžete spustit detekci pohybů na živém informačním kanálu videa.
* Kontrola [terminologie](terminology.md)
* Prozkoumejte [materiál Open Source Analytics s dynamickým videem](https://github.com/Azure/live-video-analytics)

