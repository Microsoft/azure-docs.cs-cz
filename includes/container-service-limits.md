---
title: zahrnout soubor
description: zahrnout soubor
services: container-service
author: mmacy
ms.service: container-service
ms.topic: include
ms.date: 08/31/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 1e8913d31677f3da9b6eb9d2216d8d9ec8b186b4
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666900"
---
| Prostředek | Výchozí omezení |
| --- | :--- |
| Maximální počet clusterů na předplatné | 100 |
| Maximální počet uzlů na cluster | 100 |
| Maximální počet podů na uzel: [Základní síťové funkce][basic-networking] s využitím Kubenet | 110 |
| Maximální počet podů na uzel: [Pokročilé síťové funkce][advanced-networking] s využitím Azure CNI | Nasazení Azure CLI: 110<sup>1</sup><br />Šablona Resource Manageru: 110<sup>1</sup><br />Nasazení na portálu: 30 |

<sup>1</sup> Tuto hodnotu je možné nakonfigurovat při nasazení clusteru AKS s využitím Azure CLI nebo šablony Resource Manageru.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/networking-overview.md#basic-networking
[advanced-networking]: ../articles/aks/networking-overview.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
