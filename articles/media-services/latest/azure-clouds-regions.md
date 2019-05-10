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
ms.date: 05/07/2019
ms.author: juliako
ms.openlocfilehash: 7b2691f543cf38a56eefb1e8521169aeccbf3221
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2019
ms.locfileid: "65409286"
---
# <a name="clouds-and-regions-in-which-azure-media-services-v3-exists"></a>Cloudy a oblasti, ve které Azure Media Services v3 existuje

Azure Media Services v3 je k dispozici prostřednictvím Azure Resource Manageru manifestu v globální Azure, Azure Government, Azure Germany, Azure China 21Vianet. Ne všechny funkce Media Services jsou však k dispozici ve všech cloudech Azure. Tento dokument popisuje dostupnosti hlavní pomocí komponent Media Services v3.

## <a name="feature-availability-in-azure-clouds"></a>Dostupnost funkcí v cloudech Azure

| Funkce|Globálními oblastmi Azure | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| [Azure EventGrid](reacting-to-media-services-events.md) | K dispozici | Není dostupné | Není dostupné | Není dostupné |
| [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) |  K dispozici | Není dostupné | Není dostupné | Není dostupné |
| [AudioAnalyzerPreset](analyzing-video-audio-files-concept.md) |  K dispozici | Není dostupné | Není dostupné | Není dostupné |
| [StandardEncoderPreset](encoding-concept.md) | K dispozici | K dispozici | K dispozici | K dispozici |
| [LiveEvents](live-streaming-overview.md) | K dispozici | K dispozici | K dispozici | K dispozici |
| [Koncové body streamování](streaming-endpoint-concept.md) | K dispozici | K dispozici | K dispozici | K dispozici |

## <a name="regionsgeographieslocations"></a>Oblastí nebo geografické oblasti/umístění

[Oblasti, ve které nasazení služby Azure Media Services](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)

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

## <a name="see-also"></a>Další informace najdete v tématech

* [Oblasti Azure](https://azure.microsoft.com/global-infrastructure/regions/)
* [Zeměpisné oblasti Azure](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>Další postup

[Přehled služby Media Services v3](media-services-overview.md)
