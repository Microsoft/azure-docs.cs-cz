---
title: Ukázky Azure PowerShellu – Service Fabric
description: Seznamte se s vytvářením a správou clusterů, aplikací a služeb Azure Service Fabric pomocí prostředí PowerShell.
ms.topic: sample
ms.date: 11/29/2018
ms.custom: mvc
ms.openlocfilehash: f9ba1b4833b3aff13284a68c23f398ea47ef2ae2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "75645646"
---
# <a name="azure-service-fabric-powershell-samples"></a>Ukázky Azure Service Fabric PowerShellu

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
