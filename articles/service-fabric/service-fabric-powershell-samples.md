---
title: Ukázky Azure PowerShellu – Service Fabric | Microsoft Docs
description: Ukázky Azure PowerShellu – Service Fabric
services: service-fabric
documentationcenter: service-fabric
author: athinanthny
manager: chackdan
editor: ''
tags: ''
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: service-fabric
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: service-fabric
ms.date: 11/29/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: cae88e142c3bca15e837db7f084eef68434a78ca
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903266"
---
# <a name="azure-powershell-samples"></a>Ukázky Azure PowerShellu

Následující tabulka obsahuje odkazy na ukázkové skripty PowerShellu pro vytváření a správu clusterů, aplikací a služeb Service Fabric.

[!INCLUDE [links to azure CLI and service fabric CLI](../../includes/service-fabric-powershell.md)]

| | |
|-|-|
| **Vytvoření clusteru** ||
| [Vytvoření clusteru (Azure)](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)| Vytvoří cluster Azure Service Fabric. |
| **Správa clusterů, uzlů a infrastruktury** ||
| [Přidání certifikátu aplikace](./scripts/service-fabric-powershell-add-application-certificate.md)| Vytvoří certifikát x509 pro Key Vault a nasadí ho do sady škálování virtuálních počítačů ve vašem clusteru. |
| [Aktualizace rozsahu portů RDP na virtuálních počítačích clusteru](./scripts/service-fabric-powershell-change-rdp-port-range.md)|Změní rozsah portů RDP na virtuálních počítačích uzlů v nasazeném clusteru.|
| [Aktualizace uživatelského jména a hesla správce pro virtuální počítače uzlů clusteru](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) | Aktualizuje uživatelské jméno a heslo správce pro virtuální počítače uzlů clusteru. |
| [Otevření portu v nástroji pro vyrovnávání zatížení](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) | Otevře port aplikace v nástroji pro vyrovnávání zatížení Azure a tím povolí příchozí provoz na určitém portu. |
| [Vytvoření příchozího pravidla skupiny zabezpečení sítě](./scripts/service-fabric-powershell-add-nsg-rule.md) | Vytvoří příchozí pravidlo skupiny zabezpečení sítě, které povolí příchozí provoz do clusteru na určitém portu. |
| **Správa aplikací** ||
| [Nasazení aplikace](./scripts/service-fabric-powershell-deploy-application.md)| Nasadí aplikaci do clusteru.|
| [Upgrade aplikace](./scripts/service-fabric-powershell-upgrade-application.md)| Upgraduje aplikaci.|
| [Odebrání aplikace](./scripts/service-fabric-powershell-remove-application.md)| Odebere aplikaci z clusteru.|
