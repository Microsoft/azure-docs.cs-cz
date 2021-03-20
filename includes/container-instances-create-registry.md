---
title: zahrnout soubor
description: zahrnout soubor
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 08/13/2020
ms.author: danlep
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 173c9156f253e43111299b53287e97ab7b2c0aa5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92746950"
---
## <a name="create-azure-container-registry"></a>Vytvoření registru kontejneru Azure

Než vytvoříte registr kontejneru, budete potřebovat *skupinu prostředků*, do které ho budete moct nasadit. Skupina prostředků je logická kolekce, ve které se nasazují a spravují všech prostředky Azure.

Vytvořte skupinu prostředků pomocí příkazu [az group create][az-group-create]. V následujícím příkladu se vytvoří skupina prostředků s názvem *myResourceGroup* v oblasti *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Jakmile vytvoříte skupinu prostředků, vytvořte registr kontejneru Azure pomocí příkazu [az acr create][az-acr-create]. Název registru kontejneru musí být v rámci prostředí Azure jedinečný a musí obsahovat 5 až 50 alfanumerických znaků. Nahraďte položku `<acrName>` jedinečným názvem pro svůj registr:

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

Tady je částečný výstup nového registru kontejneru Azure s názvem *mycontainerregistry082*:

```output
{
  "creationDate": "2020-07-16T21:54:47.297875+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/mycontainerregistry082",
  "location": "eastus",
  "loginServer": "mycontainerregistry082.azurecr.io",
  "name": "mycontainerregistry082",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Zbývající část tohoto kurzu používá `<acrName>` jako zástupný text pro název registru kontejneru, který zvolíte v tomto kroku.

## <a name="log-in-to-container-registry"></a>Přihlášení k registru kontejneru

Před nahráváním imagí do instance služby Azure Container Registry se k ní musíte přihlásit. Dokončete operaci pomocí příkazu [az acr login][az-acr-login]. Je třeba uvést jedinečný název, který jste zvolili pro registr kontejneru při jeho vytváření.

```azurecli
az acr login --name <acrName>
```

Například:

```azurecli
az acr login --name mycontainerregistry082
```

Příkaz po dokončení vrátí zprávu `Login Succeeded` (Přihlášení bylo úspěšné):

```output
Login Succeeded
```

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-group-create]: /cli/azure/group#az-group-create
