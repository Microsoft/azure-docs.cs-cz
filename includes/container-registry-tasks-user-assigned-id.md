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
ms.openlocfilehash: 012800806aeff81939baa2cee88e78191e4fb6c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "82195260"
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