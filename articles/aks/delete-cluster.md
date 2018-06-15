---
title: Odstranění clusteru služby Azure Kubernetes služby (AKS)
description: Odstranění a AKS clusteru pomocí rozhraní CLI nebo Azure portálu.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 2/05/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 6391e36eff60634e07a90c1e6b5f0f44ee60d46b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
ms.locfileid: "33936441"
---
# <a name="delete-an-azure-kubernetes-service-aks-cluster"></a>Odstranění clusteru služby Azure Kubernetes služby (AKS)

Při odstraňování clusteru Azure Kubernetes Service, skupinu prostředků, ve kterém byl nasazen cluster zůstane, ale jsou odstraněny všechny AKS související prostředky. Tento dokument ukazuje postup odstranění clusteru služby AKS pomocí rozhraní příkazového řádku Azure a Azure portal.

Kromě odstranění clusteru, skupinu prostředků, ve kterém byl nasazen se dá odstranit, která také odstraňuje AKS clusteru.

## <a name="azure-cli"></a>Azure CLI

Použití [odstranit az aks] [ az-aks-delete] příkaz k odstranění AKS clusteru.

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```

Jsou k dispozici následující možnosti `az aks delete` příkaz.

| Argument | Popis | Požaduje se |
|---|---|:---:|
| `--name` `-n` | Název prostředku pro spravovaný cluster. | ano |
| `--resource-group` `-g` | Název skupiny prostředků Azure Kubernetes Service. | ano |
| `--no-wait` | Není počkejte na dokončení operace časově náročné. | ne |
| `--yes` `-y` | Nezobrazovat výzvu k potvrzení. | ne |

## <a name="azure-portal"></a>Azure Portal

V portálu Azure přejděte do skupiny prostředků obsahující prostředek Azure Kubernetes služby (AKS), vyberte prostředek a klikněte na tlačítko **odstranit**. Zobrazí se výzva k potvrzení operace odstranění.

![Odstranit AKS clusteru portálu](media/container-service-delete-cluster/delete-aks-portal.png)

<!-- LINKS - internal -->
[az-aks-delete]: /cli/azure/aks?view=azure-cli-latest#az_aks_delete