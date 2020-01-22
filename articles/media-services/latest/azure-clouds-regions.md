---
title: Cloudy a oblasti, ve kterých je Azure Media Services V3 k dispozici
description: Tento článek pojednává o cloudech a oblastech Azure, ve kterých je k dispozici Azure Media Services V3.
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
ms.openlocfilehash: 58b5b749e81aab4d8563d09cbfd139629520531c
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310559"
---
# <a name="clouds-and-regions-in-which-azure-media-services-v3-exists"></a>Cloudy a oblasti, ve kterých existuje Azure Media Services V3

Azure Media Services V3 je k dispozici prostřednictvím Azure Resource Manager manifestu v globálním Azure, Azure Government, Azure Německo, Azure Čína 21Vianet. Ne všechny funkce Media Services ale jsou dostupné ve všech cloudech Azure. Tento dokument popisuje nákup hlavních komponent Media Services V3.

## <a name="feature-availability-in-azure-clouds"></a>Dostupnost funkcí v cloudech Azure

| Funkce|Globální oblasti Azure | Azure Government|Azure (Německo)|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| [Azure EventGrid](reacting-to-media-services-events.md) | Dostupné | Není k dispozici | Není k dispozici | Není k dispozici |
| [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Dostupné | Není k dispozici | Není k dispozici | Není k dispozici |
| [AudioAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Dostupné | Není k dispozici | Není k dispozici | Není k dispozici |
| [StandardEncoderPreset](encoding-concept.md) | Dostupné | Dostupné | Dostupné | Dostupné |
| [LiveEvents](live-streaming-overview.md) | Dostupné | Dostupné | Dostupné | Dostupné |
| [Starají](streaming-endpoint-concept.md) | Dostupné | Dostupné | Dostupné | Dostupné |

## <a name="regionsgeographieslocations"></a>Oblasti/geografické oblasti/umístění

[Oblasti, ve kterých je nasazená služba Azure Media Services](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)

### <a name="region-code-name"></a>Název kódu oblasti 

Pokud potřebujete zadat parametr **umístění** , musíte jako hodnotu **umístění** zadat název kódu oblasti. Pokud chcete získat název kódu pro oblast, ve které je váš účet, a na to, na který má být vaše volání směrováno, můžete spustit následující řádek v rozhraní příkazového řádku [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) .

```bash
az account list-locations
```

Po spuštění výše uvedeného řádku získáte seznam všech oblastí Azure. Přejděte do oblasti Azure s názvem *DisplayName* , který hledáte, a použijte jeho *název* pro parametr **Location** .

Například pro oblast Azure Západní USA 2 (zobrazenou níže) budete pro parametr **Location** používat "westus2".

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

Následující koncové body jsou důležité pro informace o připojení k Media Services účtům z různých národních cloudů Azure.

### <a name="global-azure"></a>Globální Azure

|Koncové body ||
| --- | --- | 
| Azure Resource Manager |  `https://management.azure.com/` |
| Ověření | `https://login.microsoftonline.com/` | 
| Cílová skupina tokenů | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure Government

|Koncové body||
| --- | --- | 
| Azure Resource Manager |  `https://management.usgovcloudapi.net/` |
| Ověření | `https://login.microsoftonline.us/` | 
| Cílová skupina tokenů | `https://management.core.usgovcloudapi.net/` |

### <a name="azure-germany"></a>Azure (Německo)

| Koncové body ||
| --- | --- |  
| Azure Resource Manager | `https://management.cloudapi.de/` |
| Ověření | `https://login.microsoftonline.de/` |
| Cílová skupina tokenů | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure China 21Vianet

|Koncové body||
| --- | --- | 
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| Ověření | `https://login.chinacloudapi.cn/` |
| Cílová skupina tokenů |  `https://management.core.chinacloudapi.cn/` |

## <a name="see-also"></a>Další informace najdete v tématech

* [Oblasti Azure](https://azure.microsoft.com/global-infrastructure/regions/)
* [Zeměpisné oblasti Azure](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>Další kroky

[Přehled Media Services V3](media-services-overview.md)
