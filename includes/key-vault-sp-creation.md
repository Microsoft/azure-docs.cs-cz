---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 6dbdebb1a9fc31e225349cc7fca8366e8b8ed004
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89378541"
---
Nejjednodušší způsob, jak ověřit cloudovou aplikaci, je spravovaná identita. Podrobnosti najdete v tématu [ověření pro Key Vault](/azure/key-vault/general/authentication) .

V zájmu zjednodušení ale v tomto rychlém startu se vytvoří desktopová aplikace, která vyžaduje použití instančního objektu a zásad řízení přístupu. Váš instanční objekt vyžaduje jedinečný název ve formátu "http:// &lt; My-Unique-Service-Principal-Name &gt; ".

Vytvořte instanční objekt pomocí příkazu Azure CLI [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) :

```azurecli
az ad sp create-for-rbac -n "http://<my-unique-service-principal-name>" --sdk-auth
```

Tato operace vrátí řadu párů klíč/hodnota.

```console
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```
