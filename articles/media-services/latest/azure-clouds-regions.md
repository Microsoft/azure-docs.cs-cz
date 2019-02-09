---
title: Cloudy a oblasti, které Azure Media Services v3 je k dispozici | Dokumentace Microsoftu
description: Tento článek pojednává o cloudech Azure a oblastech, které Azure Media Services v3 je k dispozici.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/07/2019
ms.author: juliako
ms.openlocfilehash: a5a4205c97e4db25d5d0a92472610364d912b278
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55963433"
---
# <a name="clouds-and-regions-in-which-azure-media-services-v3-exists"></a>Cloudy a oblasti, ve které Azure Media Services v3 existuje

Azure Media Services v3 je k dispozici prostřednictvím Azure Resource Manageru manifestu v globální Azure, Azure Government, Azure Germany, Azure China 21Vianet. Ne všechny funkce Media Services jsou však k dispozici ve všech cloudech Azure. Tento dokument popisuje dostupnosti hlavní pomocí komponent Media Services v3.

## <a name="feature-availability-in-azure-clouds"></a>Dostupnost funkcí v cloudech Azure

| Funkce|Globálními oblastmi Azure | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| [Azure EventGrid](reacting-to-media-services-events.md) | K dispozici. | Není k dispozici. | Není k dispozici. | Není k dispozici. |
| [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) |  K dispozici. | Není k dispozici. | Není k dispozici. | Není k dispozici. |
| [AudioAnalyzerPreset](analyzing-video-audio-files-concept.md) |  K dispozici. | Není k dispozici. | Není k dispozici. | Není k dispozici. |
| [StandardEncoderPreset](encoding-concept.md) | K dispozici. | K dispozici. | K dispozici. | K dispozici. |
| [LiveEvents](live-streaming-overview.md) | K dispozici. | K dispozici. | K dispozici. | K dispozici. |
| [Koncové body streamování](streaming-endpoint-concept.md) | K dispozici. | K dispozici. | K dispozici. | K dispozici. |

## <a name="regionsgeographieslocations"></a>Oblastí nebo geografické oblasti/umístění

* [Oblasti Azure](https://azure.microsoft.com/global-infrastructure/regions/)
* [Podle oblasti produktu](https://azure.microsoft.com/global-infrastructure/services/)
* [Zeměpisné oblasti Azure](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/)

### <a name="region-code-name"></a>Název oblasti kódu 

Pokud je potřeba zadat **umístění** parametr, musíte zadat název oblasti kódu jako **umístění** hodnotu. Získat kód název oblasti, které váš účet a že vaše volání by měl směrovat na, můžete spustit následující řádek [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

```bash
az account list-locations
```

Po spuštění řádku vidíte výše, získání seznamu všech oblastech Azure. Přejděte do oblasti Azure, který má *displayName* hledáte a použít jeho *název* hodnota **umístění** parametru.

Například pro oblasti Azure USA – západ 2 (zobrazené níže), který použijete "westus2" **umístění** parametru.

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

Následující koncové body jsou důležité vědět, při připojení k účtů Media Services z různých národních cloudů Azure.

### <a name="global-azure"></a>Globální Azure

|Koncové body ||
| --- | --- | 
| Azure Resource Manager |  `https://management.azure.com/` |
| Authentication | `https://login.microsoftonline.com/` | 
| Cílové skupiny tokenu | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure Government

|Koncové body||
| --- | --- | 
| Azure Resource Manager |  `https://management.usgovcloudapi.net/` |
| Authentication | `https://login.microsoftonline.us/` | 
| Cílové skupiny tokenu | `https://management.core.usgovcloudapi.net/` |

### <a name="azure-germany"></a>Azure Germany

| Koncové body ||
| --- | --- |  
| Azure Resource Manager | `https://management.cloudapi.de/` |
| Authentication | `https://login.microsoftonline.de/` |
| Cílové skupiny tokenu | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure China 21Vianet

|Koncové body||
| --- | --- | 
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| Authentication | `https://login.chinacloudapi.cn/` |
| Cílové skupiny tokenu |  `https://management.core.chinacloudapi.cn/` |

## <a name="next-steps"></a>Další postup

[Přehled služby Media Services v3](media-services-overview.md)
