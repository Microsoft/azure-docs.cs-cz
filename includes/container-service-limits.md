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
ms.openlocfilehash: 6b4678b381e769993b01bbedd1cb4c0aeefc0cc1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "80334747"
---
| Prostředek | Omezení |
| --- | :--- |
| Maximální počet clusterů na předplatné | 100 |
| Maximální počet uzlů na cluster se skupinami dostupnosti virtuálních počítačů a základní Load Balancer SKU  | 100 |
| Maximální počet uzlů na cluster s Virtual Machine Scale Sets a [Standard Load BALANCER SKU][standard-load-balancer] | 1000 (100 uzlů na [fond uzlů][node-pool]) |
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
