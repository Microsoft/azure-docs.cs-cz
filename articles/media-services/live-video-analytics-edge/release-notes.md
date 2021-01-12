---
title: Live video Analytics na IoT Edge – poznámky k verzi – Azure
description: Toto téma obsahuje poznámky k verzi Live video Analytics o IoT Edge vydáních, vylepšeních, opravách chyb a známých problémech.
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 7f8957d1ec93259cf6defe7980f19298f782ea5e
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121239"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>Poznámky k verzi pro Live video Analytics na IoT Edge

>Přečtěte si informace o tom, kdy se má tato stránka na aktualizace znovu navštívit kopírováním a vložením této adresy URL: `https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us` do čtečky kanálů RSS.

V tomto článku najdete informace o:

* Nejnovější verze
* Známé problémy
* Opravy chyb
* Zastaralé funkce

<hr width=100%>

## <a name="january-12-2021"></a>12. ledna 2021

Tato značka vydání je pro aktualizaci tohoto modulu v lednu 2021:

```
mcr.microsoft.com/media/live-video-analytics:2.0.1
```

> [!NOTE]
> V rychlých startech a kurzech manifesty nasazení používají značku 2 (živé video-Analytics: 2). Takže jednoduše znovu nasadíte takové manifesty, které by měly aktualizovat modul na hraničních > zařízeních.
### <a name="bug-fixes"></a>Opravy chyb 

* Pole `ActivationSignalOffset` `MinimumActivationTime` a `MaximumActivationTime` v procesorech brány signálu byly nesprávně nastaveny jako požadované vlastnosti. Nyní jsou **volitelné** vlastnosti.
* Opravili jsme chybu použití, která způsobí, že při nasazení v určitých oblastech dojde k chybě v modulu analýzy živých videí v IoT Edge.

<hr width=100%>

## <a name="december-14-2020"></a>14. prosince 2020
Tato verze je ve verzi Public Preview k disukázce pro živé video analýzy v IoT Edge. Značka verze je

```
     mcr.microsoft.com/media/live-video-analytics:2.0.0
```
### <a name="module-updates"></a>Aktualizace modulů
* Přidání podpory pro používání více než jednoho procesoru rozšíření HTTP a procesoru rozšíření gRPC na topologii grafu.
* Byla přidána podpora správy místa na disku pro uzly jímky.
* `MediaGraphGrpcExtension` uzel teď podporuje vlastnost [extensionConfiguration](grpc-extension-protocol.md) pro použití více modelů AI v rámci jednoho serveru gRPC.
* Přidali jsme podporu shromažďování metriky modulu Live video Analytics ve [formátu Prometheus](https://prometheus.io/docs/practices/naming/). Přečtěte si další informace o tom, jak [shromažďovat metriky a zobrazovat v Azure monitor.](monitoring-logging.md#azure-monitor-collection-via-telegraf) 
* Procesor filtru snímkové rychlosti je **zastaralý**.  
    * Správa frekvence snímků je nyní k dispozici v rámci samotného uzlu procesoru rozšíření grafu.

## <a name="september-22-2020"></a>22. září 2020

Tato značka vydání je určena pro 2020 aktualizace modulu v září:

```
mcr.microsoft.com/media/live-video-analytics:1.0.4
```

> [!NOTE]
> V rychlých startech a kurzech manifesty nasazení používají značku 1 (Live-video-Analytics: 1). Takže jednoduše znovu nasadíte takové manifesty, které by měly aktualizovat modul na hraničních > zařízeních.

### <a name="module-updates"></a>Aktualizace modulů

* Nový uzel rozšíření grafu [MediaGraphCognitiveServicesVisionExtension](spatial-analysis-tutorial.md) je k dispozici pro integraci s modulem [prostorová analýza](/legal/cognitive-services/computer-vision/intro-to-spatial-analysis-public-preview)(Preview) z Cognitive Services.
* Přidání podpory pro zařízení se systémem Linux ARM64 – použijte [ruční kroky](deploy-iot-edge-device.md) pro nasazení do takových zařízení.

### <a name="documentation-updates"></a>Aktualizace dokumentace

* [Pokyny](deploy-azure-stack-edge-how-to.md) jsou k dispozici pro používání služby Live video Analytics na IoT Edge v zařízeních Azure Stack Edge.
* Nový kurz pro vývoj různých modelů počítačového vidění pomocí [Custom Vision služby](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) a jeho použití k [analýze živého videa](custom-vision-tutorial.md) v reálném čase.

<hr width=100%>

## <a name="august-19-2020"></a>19. srpna 2020

Tato značka vydání je pro aktualizaci tohoto modulu v 2020. srpna:

```
mcr.microsoft.com/media/live-video-analytics:1.0.3
```

> [!NOTE]
> V rychlých startech a kurzech manifesty nasazení používají značku 1 (Live-video-Analytics: 1). Takže jednoduše znovu nasadíte takové manifesty, které by měly aktualizovat modul na hraničních > zařízeních.

### <a name="module-updates"></a>Aktualizace modulů

* Nyní můžete dosáhnout vysokého výkonu přenosu dat mezi živým analýzou videa na IoT Edge a vlastním rozšířením pomocí rozhraní gRPC Framework. Začněte [tím](analyze-live-video-use-your-grpc-model-quickstart.md) , že se zobrazí.
* Širší regionální nasazení živé analýzy videí a aktualizace jenom cloudové služby.  
* Live video Analytics je teď k dispozici v 25 dalších oblastech po celém světě. Tady je [seznam](https://azure.microsoft.com/global-infrastructure/services/?products=media-services) všech oblastí, které jsou k dispozici.  
* [Nastavení](https://aka.ms/lva-edge/setup-resources-for-samples) pro rychlé spuštění se aktualizovalo i v případě podpory nových oblastí.
    * Neexistuje žádné volání akce pro kohokoli, kdo již nastavil prostředky.

### <a name="bug-fixes"></a>Opravy chyb 

* Odebrání použití zastaralého rozšíření Azure ve skriptu pro nastavení

<hr width=100%>

## <a name="july-13-2020"></a>13. července 2020

Tato značka vydání je pro aktualizaci modulu v červenci 2020:

```
mcr.microsoft.com/media/live-video-analytics:1.0.2
```

> [!NOTE]
> V rychlých startech a kurzech manifesty nasazení používají značku 1 (Live-video-Analytics: 1). Takže jednoduše znovu nasadíte takové manifesty, které by měly aktualizovat modul na hraničních > zařízeních.

### <a name="module-updates"></a>Aktualizace modulů

* Nyní můžete vytvářet topologie grafů, které mají uzel jímky assetu a také uzel jímky souborů podřízeného uzlu procesoru brány signálu. Příklad najdete v [tomto](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-motion-assets-files) příkladu.

### <a name="bug-fixes"></a>Opravy chyb

* Vylepšení ověřování požadovaných vlastností

<hr width=100%>

## <a name="june-1-2020"></a>1\. června 2020

Tato verze je první verze Public Preview pro Live video Analytics na IoT Edge. Značka verze je

```
     mcr.microsoft.com/media/live-video-analytics:1.0.0
```

### <a name="supported-functionalities"></a>Podporované funkce

* Analyzovat živé streamy videa pomocí modulů AI podle vašeho výběru a volitelně nahrávat video na hraničním zařízení nebo v cloudu
* Použití v operačních systémech Linux AMD64 [podporovaných](../../iot-edge/support.md) nástrojem IoT Edge
* Nasazení a konfigurace modulu prostřednictvím IoT Hub pomocí Azure Portal nebo Visual Studio Code
* Spravujte [topologie grafů a instance grafů](media-graph-concept.md#media-graph-topologies-and-instances) vzdáleně nebo místně prostřednictvím následujících přímých volání metody.

    *   GraphTopologyGet
    *   GraphTopologySet
    *   GraphTopologyDelete
    *   GraphTopologyList
    *   GraphInstanceGet
    *   GraphInstanceSet
    *   GraphInstanceDelete
    *   GraphInstanceList

## <a name="next-steps"></a>Další kroky

[Přehled](overview.md)
