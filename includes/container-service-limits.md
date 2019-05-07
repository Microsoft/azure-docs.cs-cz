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
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65072893"
---
| Resource | Výchozí omezení |
| --- | :--- |
| Maximální clustery na předplatné | 100 |
| Maximální počet uzlů na clusteru | 100 |
| Maximální podů podle počtu uzlů: [Základní síť] [ basic-networking] s Kubenet | 110 |
| Maximální podů podle počtu uzlů: [Rozšířeného sítě] [ advanced-networking] s síťové rozhraní kontejneru Azure | Nasazení v Azure CLI: 30<sup>1</sup><br />Šablona Azure Resource Manageru: 30<sup>1</sup><br />Nasazení portálu: 30 |

<sup>1</sup>při nasazování clusteru služby Azure Kubernetes Service (AKS) pomocí šablony Resource Manageru nebo rozhraní příkazového řádku Azure, tato hodnota je konfigurovatelná až 250 podů na jeden uzel. Maximální podů na uzel nelze nakonfigurovat, jakmile již nasadíte AKS cluster, nebo pokud nasadíte cluster s použitím webu Azure portal.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
