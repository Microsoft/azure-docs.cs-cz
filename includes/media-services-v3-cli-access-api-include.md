---
title: zahrnout soubor
description: zahrnout soubor
services: media-services
author: WenJason
ms.service: media-services
ms.topic: include
origin.date: 05/29/2018
ms.date: 06/25/2018
ms.author: v-nany
ms.custom: include file
ms.openlocfilehash: 4dde0a47f0452da2dd951df86ccb6e02a44521ed
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60309031"
---
## <a name="access-the-media-services-api"></a>Přístup k rozhraní API služby Media Services

Pro připojení k rozhraním Azure Media Services API použijte ověřování instančního objektu Azure AD. Následující příkaz vytvoří aplikaci Azure AD a připojí k danému účtu instanční objekt. Vrácené hodnoty byste měli použít ke konfiguraci vaší aplikace.

Před spuštěním skriptu můžete nahradit `amsaccount` a `amsResourceGroup` názvy, které jste zvolili při vytváření těchto prostředků. `amsaccount` je název účtu Azure Media Services, ke kterému se daný instanční objekt připojí.

Následující příkaz vrací výstup `json`:

```cli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup
```

Tento příkaz vytvoří odpověď, která je podobná této:

```json
{
  "AadClientId": "00000000-4cdd-418a-8a72-0755ace03de5",
  "AadEndpoint": "https://login.partner.microsoftonline.cn",
  "AadSecret": "00000000-02f5-4bf2-9057-1c4f7baff155",
  "AadTenantId": "00000000-86f1-41af-91ab-2d7cd011db47",
  "AccountName": "amsaccount22",
  "ArmAadAudience": "https://management.core.chinacloudapi.cn/",
  "ArmEndpoint": "https://management.chinacloudapi.cn/",
  "Region": "chinaeast",
  "ResourceGroup": "amsResourceGroup2",
  "SubscriptionId": "00000000-6753-4ca2-b1ae-193798e2c9d8"
}
```

Pokud chcete v odpovědi mít `xml`, použijte následující příkaz:

```cli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup --xml
```
