---
title: Cloudy a oblasti pro Azure Media Services V3
description: Tento článek pojednává o adresách URL, které se používají pro koncové body a kód pro oblasti.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: reference
ms.date: 10/28/2020
ms.author: inhenkel
ms.openlocfilehash: 74f30ebb766ea34603c0a30455837c47222967c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98954323"
---
# <a name="regional-code-names-and-endpoints"></a>Regionální kódové názvy a koncové body

### <a name="region-code-name"></a>Název kódu oblasti

Pokud se parametr **Location** používá v příkazu nebo žádosti, musíte jako hodnotu **umístění** zadat název kódu oblasti. Pokud chcete získat název kódu pro oblast, ve které je váš účet, a na to, na který má být vaše volání směrováno, můžete v Azure CLI spustit následující řádek.

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

| Služba | Koncový bod |
| ------- | -------- |
| Azure Resource Manager |  `https://management.azure.com/` |
| Authentication | `https://login.microsoftonline.com/` |
| Cílová skupina tokenů | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure Government

| Služba | Koncový bod |
| ------- | -------- |
| Azure Resource Manager |  `https://management.usgovcloudapi.net/` |
| Authentication | `https://login.microsoftonline.us/` |
| Cílová skupina tokenů | `https://management.core.usgovcloudapi.net/` |

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

### <a name="azure-germany"></a>Azure (Německo)

> [!NOTE]
> Koncové body Azure Německo platí jenom pro cloudy Svrchovan v Německu.

| Služba | Koncový bod |
| ------- | -------- |
| Azure Resource Manager | `https://management.cloudapi.de/` |
| Authentication | `https://login.microsoftonline.de/` |
| Cílová skupina tokenů | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure (Čína) 21Vianet

| Služba | Koncový bod |
| ------- | -------- |
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| Authentication | `https://login.chinacloudapi.cn/` |
| Cílová skupina tokenů |  `https://management.core.chinacloudapi.cn/` |

## <a name="see-also"></a>Viz také

* [Oblasti Azure](https://azure.microsoft.com/global-infrastructure/regions/)
* [Regionální kódové názvy a koncové body](azure-regions-code-names.md)
* [Zeměpisné oblasti Azure](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>Další kroky

[Přehled Media Services V3](media-services-overview.md)
