---
title: Ukázky Azure PowerShellu – Service Fabric
description: Seznamte se s vytvářením a správou clusterů, aplikací a služeb Azure Service Fabric pomocí prostředí PowerShell.
ms.topic: sample
ms.date: 11/29/2018
ms.custom: mvc
ms.openlocfilehash: 4b85fd604eb27f0963af882b41e823d800005dda
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86187094"
---
# <a name="azure-service-fabric-powershell-samples"></a>Ukázky Azure Service Fabric PowerShellu

Následující tabulka obsahuje odkazy na ukázkové skripty PowerShellu pro vytváření a správu clusterů, aplikací a služeb Service Fabric.

[!INCLUDE [links to azure CLI and service fabric CLI](../../includes/service-fabric-powershell.md)]

| Skript | Description |
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
