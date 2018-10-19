---
title: zahrnout soubor
description: zahrnout soubor
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 08/31/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 71294824bd3dd5215c388cfcd44382c7eee123ad
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48874145"
---
| Prostředek | Výchozí omezení |
| --- | :--- |
| Maximální počet clusterů na předplatné | 100 |
| Maximální počet uzlů na cluster | 100 |
| Maximální počet podů na uzel: [Základní síťové funkce][basic-networking] s využitím Kubenet | 110 |
| Maximální počet podů na uzel: [Pokročilé síťové funkce][advanced-networking] s využitím Azure CNI | Nasazení Azure CLI: 30<sup>1</sup><br />Šablona Resource Manageru: 30<sup>1</sup><br />Nasazení na portálu: 30 |

<sup>1</sup> Tuto hodnotu je možné nakonfigurovat při nasazení clusteru AKS s využitím Azure CLI nebo šablony Resource Manageru.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/networking-overview.md#basic-networking
[advanced-networking]: ../articles/aks/networking-overview.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
