---
title: Live video Analytics na IoT Edge – poznámky k verzi – Azure
description: Toto téma obsahuje poznámky k verzi Live video Analytics o IoT Edge vydáních, vylepšeních, opravách chyb a známých problémech.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: fd607ee027b46cf386180338440e6530da911302
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261034"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>Poznámky k verzi pro Live video Analytics na IoT Edge

>Přečtěte si informace o tom, kdy se má tato stránka na aktualizace znovu navštívit kopírováním a vložením této adresy URL: `https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us` do čtečky kanálů RSS.

V tomto článku najdete informace o:

* Nejnovější verze
* Známé problémy
* Opravy chyb
* Zastaralé funkce

## <a name="june-1-2020"></a>1. června 2020

Tato verze je první verze Public Preview pro Live video Analytics na IoT Edge. Značka verze je

```
     mcr.microsoft.com/media/live-video-analytics:1.0.0
```

### <a name="supported-functionalities"></a>Podporované funkce
* Analyzovat živé streamy videa pomocí modulů AI podle vašeho výběru a volitelně nahrávat video na hraničním zařízení nebo v cloudu
* Použití v operačních systémech Linux AMD64 [podporovaných](https://docs.microsoft.com/azure/iot-edge/support) nástrojem IoT Edge
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
