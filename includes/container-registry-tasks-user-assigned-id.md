---
title: zahrnout soubor
description: zahrnout soubor
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 07/12/2019
ms.author: danlep
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: d81b6f5367efa92c9249956faa058441edf98561
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92755993"
---
### <a name="create-a-user-assigned-identity"></a>Vytvoření identity přiřazené uživatelem

Pomocí příkazu [AZ identity Create][az-identity-create] vytvořte v předplatném identitu s názvem *myACRTasksId* . Stejnou skupinu prostředků, kterou jste použili dříve, můžete použít k vytvoření registru kontejnerů nebo jiné.

```azurecli
az identity create \
  --resource-group myResourceGroup \
  --name myACRTasksId
```

Pokud chcete nakonfigurovat identitu přiřazenou uživatelem v následujících krocích, pomocí příkazu [AZ identity show][az-identity-show] uložte ID prostředku identity, ID objektu zabezpečení a ID klienta v proměnných.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query id --output tsv)

# Get principal ID of the task's user-assigned identity
principalID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query principalId --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query clientId --output tsv)
```

<!-- LINKS - Internal -->
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
