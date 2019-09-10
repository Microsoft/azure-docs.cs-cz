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
ms.openlocfilehash: a2729af6a689daa551fc01f585324d53a8770a9b
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "67175370"
---
| Resource | Výchozí omezení |
| --- | :--- |
| Maximální počet clusterů na předplatné | 100 |
| Maximální počet uzlů na cluster | 100 |
| Maximální počet lusků na uzel: [Základní sítě][basic-networking] s Kubenet | 110 |
| Maximální počet lusků na uzel: [Pokročilé sítě][advanced-networking] s rozhraním Azure Container Networking | Nasazení rozhraní příkazového řádku Azure CLI: 30<sup>1</sup><br />Šablona Azure Resource Manager: 30<sup>1</sup><br />Nasazení portálu: 30 |

<sup>1</sup> Když nasadíte cluster Azure Kubernetes Service (AKS) pomocí Azure CLI nebo šablony Správce prostředků, tato hodnota se dá nakonfigurovat až na 250 lusky na jeden uzel. Po nasazení clusteru AKS nebo pokud nasazujete cluster pomocí Azure Portal, nemůžete nakonfigurovat maximální počet lusků na uzel.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
