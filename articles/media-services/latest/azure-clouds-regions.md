---
title: Cloudy a oblasti, ve kterých je azure media services v3 k dispozici
description: Tento článek popisuje cloudy Azure a oblasti, ve kterých je azure media services v3 k dispozici.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/21/2020
ms.author: juliako
ms.openlocfilehash: 1257bf4dfb0d5b2c4995cac760290f97293a0c0f
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382966"
---
# <a name="clouds-and-regions-in-which-azure-media-services-v3-exists"></a>Cloudy a oblasti, ve kterých existuje Azure Media Services v3

Azure Media Services v3 je dostupná přes manifest Azure Resource Manager v globálníazure, Azure Government, Azure Germany, Azure China 21Vianet. Ne všechny funkce Mediálních služeb jsou však k dispozici ve všech cloudech Azure. Tento dokument popisuje dostupnost hlavních součástí media services v3.

## <a name="feature-availability-in-azure-clouds"></a>Dostupnost funkcí v cloudech Azure

| Funkce|Globální oblasti Azure | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| [Azure EventGrid](reacting-to-media-services-events.md) | K dispozici. | Není k dispozici. | Není k dispozici. | Není k dispozici. |
| [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) |  K dispozici. | Není k dispozici. | Není k dispozici. | Není k dispozici. |
| [AudioAnalyzerPreset](analyzing-video-audio-files-concept.md) |  K dispozici. | Není k dispozici. | Není k dispozici. | Není k dispozici. |
| [StandardEncoderPreset](encoding-concept.md) | K dispozici. | K dispozici. | K dispozici. | K dispozici. |
| [Živé události](live-streaming-overview.md) | K dispozici. | K dispozici. | K dispozici. | K dispozici. |
| [Streamování koncových bodů](streaming-endpoint-concept.md) | K dispozici. | K dispozici. | K dispozici. | K dispozici. |

## <a name="regionsgeographieslocations"></a>Oblasti/zeměpisné oblasti/lokality

[Oblasti, ve kterých se nasazuje služba Azure Media Services](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)

### <a name="region-code-name"></a>Kódový název oblasti

Pokud potřebujete zadat parametr **lokace,** je třeba zadat kódový název oblasti jako hodnotu **umístění.** Chcete-li získat kódový název oblasti, ve které se váš účet nachází a do které by měl být hovor směrován, můžete spustit následující řádek v [nastavení nastavení nastavení účtu Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

```azurecli-interactive
az account list-locations
```

Po spuštění řádku uvedeného výše získáte seznam všech oblastí Azure. Přejděte do oblasti Azure, která má *displayName,* který hledáte, a použijte jeho hodnotu *názvu* pro parametr **umístění.**

Například pro oblast Azure West US 2 (zobrazeno níže) použijete "westus2" pro parametr **umístění.**

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="endpoints"></a>Koncové body  

Následující koncové body jsou důležité vědět při připojování k účtům Media Services z různých národních cloudů Azure.

### <a name="global-azure"></a>Globální Azure

|Koncové body||
| --- | --- |
| Azure Resource Manager |  `https://management.azure.com/` |
| Ověřování | `https://login.microsoftonline.com/` |
| Cílová skupina tokenů | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure Government

|Koncové body||
| --- | --- |
| Azure Resource Manager |  `https://management.usgovcloudapi.net/` |
| Ověřování | `https://login.microsoftonline.us/` |
| Cílová skupina tokenů | `https://management.core.usgovcloudapi.net/` |

### <a name="azure-germany"></a>Azure Germany

| Koncové body ||
| --- | --- |  
| Azure Resource Manager | `https://management.cloudapi.de/` |
| Ověřování | `https://login.microsoftonline.de/` |
| Cílová skupina tokenů | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure China 21Vianet

|Koncové body||
| --- | --- |
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| Ověřování | `https://login.chinacloudapi.cn/` |
| Cílová skupina tokenů |  `https://management.core.chinacloudapi.cn/` |

## <a name="see-also"></a>Viz také

* [Oblast Azure](https://azure.microsoft.com/global-infrastructure/regions/)
* [Zeměpisné oblasti Azure](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>Další kroky

[Mediální služby v3 – přehled](media-services-overview.md)
