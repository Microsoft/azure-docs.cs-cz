---
title: Odstranit cluster Azure Kubernetes Service (AKS)
description: Odstranění a AKS clusteru pomocí portálu Azure nebo rozhraní příkazového řádku.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 2/05/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: c8eab17a5c635560d9a5274eb038845238968e02
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39439931"
---
# <a name="delete-an-azure-kubernetes-service-aks-cluster"></a>Odstranit cluster Azure Kubernetes Service (AKS)

Při odstraňování clusteru Azure Kubernetes Service, skupinu prostředků, ve kterém byl nasazen cluster zůstane, ale se odstraní všechny AKS související prostředky. Tento dokument ukazuje, jak odstranit cluster AKS pomocí Azure CLI a webu Azure portal.

Kromě odstranění clusteru, skupinu prostředků, ve kterém byl nasazen můžete odstranit, který se odstraní také clusteru AKS.

## <a name="azure-cli"></a>Azure CLI

Použití [az aks odstranit] [ az-aks-delete] příkazu k odstranění clusteru AKS.

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```

Tyto možnosti jsou dostupné `az aks delete` příkazu.

| Argument | Popis | Požaduje se |
|---|---|:---:|
| `--name` `-n` | Název prostředku pro spravovaný cluster. | ano |
| `--resource-group` `-g` | Název skupiny prostředků Azure Kubernetes Service. | ano |
| `--no-wait` | Nechcete čekat na dokončení dlouho běžící operace. | ne |
| `--yes` `-y` | Nezobrazovat výzvu k potvrzení. | ne |

## <a name="azure-portal"></a>portál Azure

V portálu Azure přejděte na skupinu prostředků obsahující prostředků Azure Kubernetes Service (AKS), vyberte prostředek a klikněte na tlačítko **odstranit**. Zobrazí se výzva k potvrzení operace odstranění.

![Odstranit portál clusteru AKS](media/container-service-delete-cluster/delete-aks-portal.png)

<!-- LINKS - internal -->
[az-aks-delete]: /cli/azure/aks?view=azure-cli-latest#az-aks-delete