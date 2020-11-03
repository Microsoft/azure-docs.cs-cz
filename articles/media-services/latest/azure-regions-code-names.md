---
title: Cloudy a oblasti, ve kterých je Azure Media Services V3 k dispozici
description: Tento článek pojednává o adresách URL, které se používají pro koncové body a kód pro oblasti.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: reference
ms.date: 10/28/2020
ms.author: inhenkel
ms.openlocfilehash: 462cf6cf850885b31588123bac46ff99bf319ee8
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93243135"
---
# <a name="regional-code-names-and-endpoints"></a>Regionální názvy kódu a koncové body

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
| Ověřování | `https://login.microsoftonline.com/` |
| Cílová skupina tokenů | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure Government

| Služba | Koncový bod |
| ------- | -------- |
| Azure Resource Manager |  `https://management.usgovcloudapi.net/` |
| Ověřování | `https://login.microsoftonline.us/` |
| Cílová skupina tokenů | `https://management.core.usgovcloudapi.net/` |

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

### <a name="azure-germany"></a>Azure (Německo)

| Služba | Koncový bod |
| ------- | -------- |
| Azure Resource Manager | `https://management.cloudapi.de/` |
| Ověřování | `https://login.microsoftonline.de/` |
| Cílová skupina tokenů | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure (Čína) 21Vianet

| Služba | Koncový bod |
| ------- | -------- |
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| Ověřování | `https://login.chinacloudapi.cn/` |
| Cílová skupina tokenů |  `https://management.core.chinacloudapi.cn/` |

## <a name="see-also"></a>Viz také:

* [Oblasti Azure](https://azure.microsoft.com/global-infrastructure/regions/)
* [Regionální názvy kódu a koncové body](azure-regions-code-names.md)
* [Zeměpisné oblasti Azure](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>Další kroky

[Přehled Media Services V3](media-services-overview.md)
