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
ms.openlocfilehash: 62eb75ef18d3ac81be65783e57c21c0aefd7a429
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554718"
---
| Prostředek | Výchozí omezení |
| --- | :--- |
| Maximální clustery na předplatné | 100 |
| Maximální počet uzlů na clusteru | 100 |
| Maximální podů podle počtu uzlů: [Základní síť] [ basic-networking] s Kubenet | 110 |
| Maximální podů podle počtu uzlů: [Rozšířeného sítě] [ advanced-networking] s síťové rozhraní kontejneru Azure | Nasazení v Azure CLI: 30<sup>1</sup><br />Šablona Azure Resource Manageru: 30<sup>1</sup><br />Nasazení portálu: 30 |

<sup>1</sup>při nasazování clusteru služby Azure Kubernetes Service (AKS) pomocí šablony Resource Manageru nebo rozhraní příkazového řádku Azure, tato hodnota je konfigurovatelná maximálně 110 podů na jeden uzel. Maximální podů na uzel nelze nakonfigurovat, jakmile již nasadíte AKS cluster, nebo pokud nasadíte cluster s použitím webu Azure portal.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
