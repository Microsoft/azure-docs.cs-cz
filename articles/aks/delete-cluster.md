---
title: Odstranění clusteru služby Azure Kubernetes služby (AKS)
description: Odstranění a AKS clusteru pomocí rozhraní CLI nebo Azure portálu.
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/05/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: e006466d1450471900a8635c49d3bc6c3a73d476
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
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