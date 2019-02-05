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
ms.openlocfilehash: 23c25953d2f493d2dd799bfd11dbbb69db002d1b
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2019
ms.locfileid: "55735977"
---
| Prostředek | Výchozí omezení |
| --- | :--- |
| Maximální počet clusterů na předplatné | 100 |
| Maximální počet uzlů na cluster | 100 |
| Maximální počet podů na uzel: [Základní síť] [ basic-networking] s Kubenet | 110 |
| Maximální počet podů na uzel: [Rozšířeného sítě] [ advanced-networking] s Azure CNI | Nasazení v Azure CLI: 30<sup>1</sup><br />Šablony Resource Manageru: 30<sup>1</sup><br />Nasazení portálu: 30 |

<sup>1</sup> Při nasazování clusteru AKS s využitím Azure CLI nebo šablony Resource Manageru můžete tuto hodnotu nakonfigurovat až na **110 podů na uzel**. Pokud už jste cluster AKS nasadili nebo k nasazení clusteru používáte Azure Portal, maximální počet podů na uzel není možné nakonfigurovat.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
