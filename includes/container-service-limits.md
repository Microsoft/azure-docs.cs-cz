---
title: zahrnout soubor
description: zahrnout soubor
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 10/11/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 4251f379c517d5ccfd0430987e3d5280208590ff
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49400178"
---
| Prostředek | Výchozí omezení |
| --- | :--- |
| Maximální počet clusterů na předplatné | 100 |
| Maximální počet uzlů na cluster | 100 |
| Maximální počet podů na uzel: [Základní síťové funkce][basic-networking] s využitím Kubenet | 110 |
| Maximální počet podů na uzel: [Pokročilé síťové funkce][advanced-networking] s využitím Azure CNI | Nasazení Azure CLI: 30<sup>1</sup><br />Šablona Resource Manageru: 30<sup>1</sup><br />Nasazení na portálu: 30 |

<sup>1</sup> Při nasazování clusteru AKS s využitím Azure CLI nebo šablony Resource Manageru můžete tuto hodnotu nakonfigurovat až na **110 podů na uzel**. Pokud už jste cluster AKS nasadili nebo k nasazení clusteru používáte Azure Portal, maximální počet podů na uzel není možné nakonfigurovat.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
