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
ms.openlocfilehash: ceda7bd6bd165df1eece555c6ce8a9a6c863b2c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77112273"
---
### <a name="create-a-user-assigned-identity"></a>Vytvoření identity přiřazené uživatelem

Vytvořte identitu s názvem *myACRTasksId* ve vašem předplatném pomocí příkazu [az identity create.][az-identity-create] Můžete použít stejnou skupinu prostředků, kterou jste použili dříve k vytvoření registru kontejneru nebo jiného.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRTasksId
```

Chcete-li nakonfigurovat identitu přiřazenou uživateli v následujících krocích, použijte příkaz [az identity show][az-identity-show] k uložení ID prostředku identity, ID objektu zabezpečení a ID klienta v proměnných.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query id --output tsv)

# Get principal ID of the task's user-assigned identity
principalID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query principalId --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query clientId --output tsv)
```

<!-- LINKS - Internal -->
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show