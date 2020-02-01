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
ms.openlocfilehash: 58e9156091702718dccd75eb4a57e5b6d8c1f073
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2020
ms.locfileid: "76896325"
---
## <a name="access-the-media-services-api"></a>Přístup k rozhraní API služby Media Services

Pro připojení k rozhraním Azure Media Services API použijte ověřování instančního objektu Azure AD. Následující příkaz vytvoří aplikaci Azure AD a připojí k danému účtu instanční objekt. Vrácené hodnoty byste měli použít ke konfiguraci vaší aplikace.

Před spuštěním skriptu byste měli nahradit `amsaccount` a `amsResourceGroup` názvy, které jste si zvolili při vytváření těchto prostředků. `amsaccount` je název účtu Azure Media Services, ke kterému se daný instanční objekt připojí.

Pokud máte přístup k několika předplatným, nejdřív nastavte aktivní předplatné na předplatné, ve kterém se vytvořil účet Media Services.

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
