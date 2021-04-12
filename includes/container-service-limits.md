---
title: zahrnout soubor
description: zahrnout soubor
services: container-service
author: mlearned
ms.service: container-service
ms.topic: include
ms.date: 11/22/2019
ms.author: mlearned
ms.custom: include file
ms.openlocfilehash: 1c2dec106ae72ddead7bda54792fa74e38eb6660
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081150"
---
| Prostředek                                                                                                           | Omezení                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------ | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Maximální počet clusterů na předplatné                                                                                  | 1000                                                                                                                                                                                                        |
| Maximální počet uzlů na cluster se skupinami dostupnosti virtuálních počítačů a základní Load Balancer SKU                       | 100                                                                                                                                                                                                         |
| Maximální počet uzlů na cluster s Virtual Machine Scale Sets a [Standard Load BALANCER SKU][standard-load-balancer] | 1000 (100 uzlů na [fond uzlů][node-pool])                                                                                                                                                                 |
| Maximální počet lusků na uzel: [základní sítě][basic-networking] s Kubenet                                           | 110                                                                                                                                                                                                         |
| Maximální počet lusků na uzel: [Pokročilé sítě][advanced-networking] s rozhraním Azure Container Networking        | Nasazení Azure CLI: 30<sup>1</sup><br />Šablona Azure Resource Manager: 30<sup>1</sup><br />Nasazení na portálu: 30                                                                                        |
| Otevřená síť pro OSM (Open Service) AKS addon Preview                                                                          | Verze clusteru Kubernetes: 1.19 +<sup>2</sup><br />OSM řadiče na cluster: 1<sup>2</sup><br />Lusky na řadič OSM: 500<sup>2</sup><br />Účty služby Kubernetes spravované pomocí OSM: 50<sup>2</sup> |

<sup>1</sup> Když nasadíte cluster Azure Kubernetes Service (AKS) pomocí Azure CLI nebo šablony Správce prostředků, tato hodnota se dá nakonfigurovat až na 250 lusky na jeden uzel. Po nasazení clusteru AKS nebo pokud nasazujete cluster pomocí Azure Portal, nemůžete nakonfigurovat maximální počet lusků na uzel.<br />

<sup>2</sup> . Doplněk OSM pro AKS je ve verzi Preview a před všeobecnou dostupností (GA) se bude podrobit dalším vylepšením. Ve fázi Preview se nedoporučuje překračovat zobrazené limity.<br />

<!-- LINKS - Internal -->

[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->

[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
