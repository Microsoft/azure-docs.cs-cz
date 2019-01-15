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
ms.date: 01/11/2019
ms.author: juliako
ms.openlocfilehash: 8eb49010d89c3039f46e5c84cd305b7d0b5ca025
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2019
ms.locfileid: "54306998"
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

## <a name="regions"></a>Oblasti 

Pokud je potřeba zadat **umístění** parametr, musíte zadat název oblasti kódu jako **umístění** hodnotu. Získat kód název oblasti, které váš účet a že vaše volání by měl směrovat na, můžete spustit následující řádek [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest):

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

## <a name="next-steps"></a>Další postup

[Přehled služby Media Services v3](media-services-overview.md)
