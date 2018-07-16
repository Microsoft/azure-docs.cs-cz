---
title: zahrnout soubor
description: zahrnout soubor
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 05/29/2018
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: f4600413e05950446db71f988c4c4302f0dcacb3
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39062929"
---
## <a name="access-the-media-services-api"></a>Přístup k rozhraní API služby Media Services

Pro připojení k rozhraním Azure Media Services API použijte ověřování instančního objektu Azure AD. Následující příkaz vytvoří aplikaci Azure AD a připojí k danému účtu instanční objekt. Vrácené hodnoty byste měli použít ke konfiguraci vaší aplikace.

Před spuštěním skriptu můžete nahradit `amsaccount` a `amsResourceGroup` názvy, které jste zvolili při vytváření těchto prostředků. `amsaccount` je název účtu Azure Media Services, ke kterému se daný instanční objekt připojí.

Následující příkaz vrací výstup `json`:

```azurecli-interactive
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup
```

Tento příkaz vytvoří odpověď, která je podobná této:

```json
{
  "AadClientId": "00000000-0000-0000-0000-000000000000",
  "AadEndpoint": "https://login.microsoftonline.com",
  "AadSecret": "00000000-0000-0000-0000-000000000000",
  "AadTenantId": "00000000-0000-0000-0000-000000000000",
  "AccountName": "amsaccount",
  "ArmAadAudience": "https://management.core.windows.net/",
  "ArmEndpoint": "https://management.azure.com/",
  "Region": "West US 2",
  "ResourceGroup": "amsResourceGroup",
  "SubscriptionId": "00000000-0000-0000-0000-000000000000"
}
```

Pokud chcete v odpovědi mít `xml`, použijte následující příkaz:

```azurecli-interactive
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup --xml
```
