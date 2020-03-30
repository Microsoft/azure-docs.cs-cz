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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334747"
---
| Prostředek | Omezení |
| --- | :--- |
| Maximální počet clusterů na předplatné | 100 |
| Maximální počet uzlů na cluster se sadami dostupnosti virtuálních strojů a základní skladovou položkou nástroje pro vyrovnávání zatížení  | 100 |
| Maximální počet uzlů na cluster se sadami škálování virtuálních strojů a [standardní skladovou položkou nástroje pro vyrovnávání zatížení][standard-load-balancer] | 1000 (100 uzlů na [uzel fondu)][node-pool] |
| Maximální počet lusků na uzel: [Základní síť][basic-networking] s Kubenetem | 110 |
| Maximální počet podů na uzel: [Pokročilá síť][advanced-networking] s rozhraním Azure Container Networking Interface | Nasazení Azure CLI: 30<sup>1</sup><br />Šablona Azure Resource Manager: 30<sup>1</sup><br />Nasazení na portálu: 30 |

<sup>1.</sup> Když nasadíte cluster služby Azure Kubernetes Service (AKS) pomocí azure CLI nebo šablony Správce prostředků, tato hodnota je konfigurovatelná až na 250 podů na uzel. Maximální počet podů na uzel nelze nakonfigurovat po nasazení clusteru AKS nebo pokud nasadíte cluster pomocí portálu Azure.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-standard-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
