---
title: Live video Analytics na IoT Edge – poznámky k verzi – Azure
description: Toto téma obsahuje poznámky k verzi Live video Analytics o IoT Edge vydáních, vylepšeních, opravách chyb a známých problémech.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 28260728532d9db52b8d36488c2e456bd11803ea
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091775"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>Poznámky k verzi pro Live video Analytics na IoT Edge

>Přečtěte si informace o tom, kdy se má tato stránka na aktualizace znovu navštívit kopírováním a vložením této adresy URL: `https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us` do čtečky kanálů RSS.

V tomto článku najdete informace o:

* Nejnovější verze
* Známé problémy
* Opravy chyb
* Zastaralé funkce

## <a name="july-13-2020"></a>13. července 2020

Tato značka vydání v červenci 2020 aktualizace modulu je:

```
     mcr.microsoft.com/media/live-video-analytics:1.0.2
```

> [!NOTE]
> V rychlých startech a kurzech manifesty nasazení používají značku 1 (Live-video-Analytics: 1). Takže jednoduše znovu nasadíte takové manifesty, které by měly aktualizovat modul na hraničních > zařízeních.

### <a name="new-features"></a>Nové funkce
* Nyní můžete vytvářet topologie grafů, které mají uzel jímky assetu a také uzel jímky souborů podřízeného uzlu procesoru brány signálu. Příklad najdete v [tomto](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-motion-assets-files) příkladu.

### <a name="bug-fixes"></a>Opravy chyb
* Vylepšení ověřování požadovaných vlastností

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
