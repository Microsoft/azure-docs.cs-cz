---
title: zahrnout soubor
description: zahrnout soubor
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 07/12/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 0e2acb346fad87e0c1c7fd7de1389d8fc86206d0
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642108"
---
### <a name="create-a-user-assigned-identity"></a>Vytvoření identity přiřazené uživatelem

Pomocí příkazu [AZ identity Create][az-identity-create] vytvořte v předplatném identitu s názvem *myACRTasksId* . Stejnou skupinu prostředků, kterou jste použili dříve, můžete použít k vytvoření registru kontejnerů nebo jiné.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRTasksId
```

Pokud chcete nakonfigurovat identitu přiřazenou uživatelem v následujících krocích, pomocí příkazu [AZ identity show][az-identity-show] uložte ID prostředku identity, ID objektu služby a ID klienta v proměnných.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query id --output tsv)

# Get service principal ID of the user-assigned identity
principalID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query principalId --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query clientId --output tsv)
```

<!-- LINKS - Internal -->
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show