---
title: zahrnout soubor
description: zahrnout soubor
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/22/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 0695416c65eed2bbf0a19d5ed1ea0c53a7ece332
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2019
ms.locfileid: "74485535"
---
| Prostředek | Výchozí omezení |
| --- | :--- |
| Maximální počet clusterů na předplatné | 100 |
| Maximální počet uzlů na cluster se skupinami dostupnosti virtuálních počítačů a základní Load Balancer SKU  | 100 |
| Maximální počet uzlů na cluster s Virtual Machine Scale Sets a [Standard Load BALANCER SKU][standard-load-balancer] | 800 (100 uzlů na [fond uzlů][node-pool]) |
| Maximální počet lusků na uzel: [základní sítě][basic-networking] s Kubenet | 110 |
| Maximální počet lusků na uzel: [Pokročilé sítě][advanced-networking] s rozhraním Azure Container Networking | Nasazení Azure CLI: 30<sup>1</sup><br />Šablona Azure Resource Manager: 30<sup>1</sup><br />Nasazení na portálu: 30 |

<sup>1</sup> Když nasadíte cluster Azure Kubernetes Service (AKS) pomocí Azure CLI nebo šablony Správce prostředků, tato hodnota se dá nakonfigurovat až na 250 lusky na jeden uzel. Po nasazení clusteru AKS nebo pokud nasazujete cluster pomocí Azure Portal, nemůžete nakonfigurovat maximální počet lusků na uzel.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-standard-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
