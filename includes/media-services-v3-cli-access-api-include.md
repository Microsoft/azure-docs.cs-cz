---
title: zahrnout soubor
description: zahrnout soubor
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 05/01/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 9d8de8826fc3dfcc2360eb3b6c82b3ff8d65d845
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79461227"
---
### <a name="access-the-media-services-api"></a>Přístup k rozhraní Media Services API

Pro připojení k rozhraním Azure Media Services API použijte ověřování instančního objektu Azure AD. Následující příkaz vytvoří aplikaci Azure AD a připojí k danému účtu instanční objekt. Vrácené hodnoty byste měli použít ke konfiguraci vaší aplikace.

Před spuštěním skriptu byste `amsaccount` `amsResourceGroup` měli nahradit a s názvy, které jste zvolili při vytváření těchto prostředků. `amsaccount` je název účtu Azure Media Services, ke kterému se daný instanční objekt připojí.

Pokud máte přístup k více předplatným, nejprve nastavte aktivní předplatné na předplatné, kde byl vytvořen účet Mediální služby.

```azurecli
az account set --subscription subscriptionId
```

Následující příkaz vrací výstup `json`:

```azurecli
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

```azurecli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup --xml
```
