---
title: Cloudy a oblasti, ve kterých je Azure Media Services V3 k dispozici
description: Tento článek pojednává o cloudech a oblastech Azure, ve kterých je k dispozici Azure Media Services V3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 5/28/2020
ms.author: inhenkel
ms.custom: references_regions
ms.openlocfilehash: cefd366e29fe1c6f68ec38816247f78bb53cd6e6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84196243"
---
# <a name="clouds-and-regions-in-which-azure-media-services-v3-exists"></a>Cloudy a oblasti, ve kterých existuje Azure Media Services V3

Azure Media Services V3 je k dispozici prostřednictvím Azure Resource Manager manifestu v globálním Azure, Azure Government, Azure Německo, Azure Čína 21Vianet. Ne všechny funkce Media Services ale jsou dostupné ve všech cloudech Azure. Tento dokument popisuje nákup hlavních komponent Media Services V3.

## <a name="feature-availability-in-azure-clouds"></a>Dostupnost funkcí v cloudech Azure

| Funkce|Globální oblasti Azure | Azure Government|Azure Germany|Azure (Čína) 21Vianet|
| --- | --- | --- | --- | --- |
| [Azure EventGrid](reacting-to-media-services-events.md) | K dispozici | Není k dispozici | Není k dispozici | Není k dispozici |
| [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) |  K dispozici | Není k dispozici | Není k dispozici | Není k dispozici |
| [AudioAnalyzerPreset](analyzing-video-audio-files-concept.md) |  K dispozici | Není k dispozici | Není k dispozici | Není k dispozici |
| [StandardEncoderPreset](encoding-concept.md) | K dispozici | K dispozici | K dispozici | K dispozici |
| [LiveEvents](live-streaming-overview.md) | K dispozici | K dispozici | K dispozici | K dispozici |
| [Starají](streaming-endpoint-concept.md) | K dispozici | K dispozici | K dispozici | K dispozici |

## <a name="feature-availability-in-preview"></a>Dostupnost funkcí ve verzi Preview

[LiveTranscription](live-transcription.md) je k dispozici v následujících oblastech:

- Jihovýchodní Asie
- Západní Evropa
- Severní Evropa
- USA – východ
- USA – střed
- USA – středojih
- USA – západ 2
- Brazílie – jih

## <a name="regionsgeographieslocations"></a>Oblasti/geografické oblasti/umístění

[Oblasti, ve kterých je nasazená služba Azure Media Services](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)

### <a name="region-code-name"></a>Název kódu oblasti

Pokud potřebujete zadat parametr **umístění** , musíte jako hodnotu **umístění** zadat název kódu oblasti. Pokud chcete získat název kódu pro oblast, ve které je váš účet, a na to, na který má být vaše volání směrováno, můžete spustit následující řádek v rozhraní příkazového řádku [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) .

```azurecli-interactive
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

|Koncové body||
| --- | --- |
| Azure Resource Manager |  `https://management.azure.com/` |
| Authentication | `https://login.microsoftonline.com/` |
| Cílová skupina tokenů | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure Government

|Koncové body||
| --- | --- |
| Azure Resource Manager |  `https://management.usgovcloudapi.net/` |
| Authentication | `https://login.microsoftonline.us/` |
| Cílová skupina tokenů | `https://management.core.usgovcloudapi.net/` |

### <a name="azure-germany"></a>Azure (Německo)

| Koncové body ||
| --- | --- |  
| Azure Resource Manager | `https://management.cloudapi.de/` |
| Authentication | `https://login.microsoftonline.de/` |
| Cílová skupina tokenů | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure (Čína) 21Vianet

|Koncové body||
| --- | --- |
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| Authentication | `https://login.chinacloudapi.cn/` |
| Cílová skupina tokenů |  `https://management.core.chinacloudapi.cn/` |

## <a name="see-also"></a>Viz také

* [Oblast Azure](https://azure.microsoft.com/global-infrastructure/regions/)
* [Zeměpisné oblasti Azure](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>Další kroky

[Přehled Media Services V3](media-services-overview.md)
