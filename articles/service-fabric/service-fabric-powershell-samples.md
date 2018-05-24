---
title: Ukázky Azure PowerShellu – Service Fabric | Microsoft Docs
description: Ukázky Azure PowerShellu – Service Fabric
services: service-fabric
documentationcenter: service-fabric
author: rwike77
manager: timlt
editor: ''
tags: ''
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: service-fabric
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: service-fabric
ms.date: 04/09/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 65f6252ecfa4e5850824fc7aa2e6eca2bdb88692
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34205752"
---
# <a name="azure-powershell-samples"></a>Ukázky Azure PowerShellu

Následující tabulka obsahuje odkazy na ukázkové skripty PowerShellu pro vytváření a správu clusterů, aplikací a služeb Service Fabric.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-powershell.md)]

| | |
|-|-|
| **Vytvoření clusteru** ||
| [Vytvoření clusteru (Azure)](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)| Vytvoří cluster Azure Service Fabric. |
|[Vytvoření testovacího clusteru (Azure)](./scripts/service-fabric-powershell-create-test-cluster.md)| Vytvoří v Azure testovací cluster Service Fabric se třemi uzly.|
| **Správa clusterů, uzlů a infrastruktury** ||
| [Přidání certifikátu aplikace](./scripts/service-fabric-powershell-add-application-certificate.md)| Přidá certifikát X.509 aplikace do všech uzlů v clusteru. |
| [Aktualizace rozsahu portů RDP na virtuálních počítačích clusteru](./scripts/service-fabric-powershell-change-rdp-port-range.md)|Změní rozsah portů RDP na virtuálních počítačích uzlů v nasazeném clusteru.|
| [Aktualizace uživatelského jména a hesla správce pro virtuální počítače uzlů clusteru](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) | Aktualizuje uživatelské jméno a heslo správce pro virtuální počítače uzlů clusteru. |
| [Otevření portu v nástroji pro vyrovnávání zatížení](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) | Otevře port aplikace v nástroji pro vyrovnávání zatížení Azure a tím povolí příchozí provoz na určitém portu. |
| [Vytvoření příchozího pravidla skupiny zabezpečení sítě](./scripts/service-fabric-powershell-add-nsg-rule.md) | Vytvoří příchozí pravidlo skupiny zabezpečení sítě, které povolí příchozí provoz do clusteru na určitém portu. |
| **Správa aplikací** ||
| [Nasazení aplikace](./scripts/service-fabric-powershell-deploy-application.md)| Nasadí aplikaci do clusteru.|
| [Upgrade aplikace](./scripts/service-fabric-powershell-upgrade-application.md)| Upgraduje aplikaci.|
| [Odebrání aplikace](./scripts/service-fabric-powershell-remove-application.md)| Odebere aplikaci z clusteru.|
