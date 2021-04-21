---
title: zahrnout soubor
description: zahrnout soubor
services: container-service
author: mlearned
ms.service: container-service
ms.topic: include
ms.date: 04/06/2021
ms.author: mlearned
ms.custom: include file
ms.openlocfilehash: da22991b9a1c4b69d3a3d6eb6f76b0925a6ad3d4
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800171"
---
| Prostředek                                                                                                           | Omezení                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------ | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Maximální počet clusterů na předplatné                                                                                  | 5000                                                                                                                                                                                                        |
| Maximální počet uzlů na cluster se skupinami dostupnosti virtuálních počítačů a základní Load Balancer SKU                       | 100                                                                                                                                                                                                         |
| Maximální počet uzlů na cluster s Virtual Machine Scale Sets a [Standard Load BALANCER SKU][standard-load-balancer] | 1000 (v rámci všech [fondů uzlů][node-pool])                                            |
| Maximální počet fondů uzlů na cluster                                                                                     | 100                                                                                  |
| Maximální počet lusků na uzel: [základní sítě][basic-networking] s Kubenet                                           | Maximum: 250 <br /> Výchozí rozhraní příkazového řádku Azure: 110 <br /> Výchozí šablona Azure Resource Manager: 110 <br /> Výchozí nasazení Azure Portal: 30          |
| Maximální počet lusků na uzel: [Pokročilé sítě][advanced-networking] s rozhraním Azure Container Networking        | Maximum: 250 <br /> Výchozí: 30                                                      |
| Otevřená síť pro OSM (Open Service) AKS addon Preview                                                                          | Verze clusteru Kubernetes: 1.19 +<sup>1</sup><br />OSM řadiče na cluster: 1<sup>1</sup><br />Lusky na jeden kontroler OSM: 500<sup>1</sup><br />Účty služby Kubernetes spravované pomocí OSM: 50<sup>1</sup> |

<sup>1</sup> Doplněk OSM pro AKS je ve verzi Preview a před obecnou dostupností (GA) se bude podrobit dalším vylepšením. Ve fázi Preview se nedoporučuje překračovat zobrazené limity.<br />

<!-- LINKS - Internal -->

[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->

[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
