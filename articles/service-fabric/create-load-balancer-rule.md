---
title: Vytvoření pravidla pro vyrovnávání zatížení Azure pro cluster
description: Konfigurace vyrovnávání zatížení Azure otevřete porty pro váš cluster Azure Service Fabric.
services: service-fabric
documentationcenter: na
author: thraka
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: adegeo
ms.openlocfilehash: 53dcd6c0705faa94e83d6e44f813fa9c575843e8
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="open-ports-for-a-service-fabric-cluster"></a>Otevřete porty pro cluster Service Fabric

Nástroje pro vyrovnávání zatížení, který je nasazen k vašemu clusteru Azure Service Fabric přesměruje přenosy na vaši aplikaci spuštěnou na uzlu. Pokud změníte vaší aplikaci, aby používala jiný port, musí vystavit tento port (nebo směrovat jiný port) nástroji pro vyrovnávání zatížení Azure.

Když jste nasadili cluster Service Fabric do Azure, byla automaticky vytvoří nástroj pro vyrovnávání zatížení. Pokud jste nástroj pro vyrovnávání zatížení, přečtěte si téma [konfigurace vyrovnávání zatížení internetového](..\load-balancer\load-balancer-get-started-internet-portal.md).

## <a name="configure-service-fabric"></a>Konfigurace infrastruktury služby

Aplikace Service Fabric **ServiceManifest.xml** definuje konfiguračním souboru aplikace očekává, že pomocí koncových bodů. Po aktualizaci do konfiguračního souboru k definování koncového bodu, nástroj pro vyrovnávání zatížení musí být aktualizovány k vystavení, (nebo jiné) port. Další informace o tom, jak vytvořit koncový bod prostředků infrastruktury služby najdete v tématu [nastavit koncový bod](service-fabric-service-manifest-resources.md).

## <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo Vyrovnávání zatížení otevře k portu internetového a předává přenos interní uzlu port používá vaše aplikace. Pokud jste nástroj pro vyrovnávání zatížení, přečtěte si téma [konfigurace vyrovnávání zatížení internetového](..\load-balancer\load-balancer-get-started-internet-portal.md).

K vytvoření pravidla pro vyrovnávání zatížení, která potřebujete shromáždit následující informace:

- Název služby Vyrovnávání zatížení.
- Skupiny prostředků clusteru zatížení vyrovnávání a služby infrastruktury.
- Externí port.
- Interní port.

## <a name="azure-cli"></a>Azure CLI
Potrvá to jenom jeden příkaz k vytvoření pravidla pro vyrovnávání zatížení s **rozhraní příkazového řádku Azure**. Potřebujete znát název skupiny, chcete-li vytvořit nové pravidlo vyrovnávání a prostředků zatížení.

>[!NOTE]
>Pokud potřebujete zjistit název služby Vyrovnávání zatížení, použijte tento příkaz rychle získat seznam všech služeb Vyrovnávání zatížení a skupin přidružených prostředků.
>
>`az network lb list --query "[].{ResourceGroup: resourceGroup, Name: name}"`
>


```azurecli
az network lb rule create --backend-port 40000 --frontend-port 39999 --protocol Tcp --lb-name LB-svcfab3 -g svcfab_cli -n my-app-rule
```

Příkaz příkazového řádku Azure CLI má několik parametrů, které jsou popsány v následující tabulce:

| Parametr | Popis |
| --------- | ----------- |
| `--backend-port`  | Aplikace Service Fabric portu naslouchá. |
| `--frontend-port` | Port pro vyrovnávání zatížení vystaví pro externí připojení. |
| `-lb-name` | Název služby Vyrovnávání zatížení, chcete-li změnit. |
| `-g`       | Skupinu prostředků, který má nástroj pro vyrovnávání zatížení a cluster Service Fabric. |
| `-n`       | Název požadované pravidlo. |


>[!NOTE]
>Další informace o tom, jak vytvořit nástroj pro vyrovnávání zatížení pomocí rozhraní příkazového řádku Azure najdete v tématu [vytvořit nástroj pro vyrovnávání zatížení pomocí Azure CLI](..\load-balancer\load-balancer-get-started-internet-arm-cli.md).

## <a name="powershell"></a>PowerShell

PowerShell je trochu složitější než rozhraní příkazového řádku Azure. Postupujte podle těchto koncepční kroky k vytvoření pravidla:

1. Získáte nástroje pro vyrovnávání zatížení z Azure.
2. Vytvořte pravidlo.
3. Přidáte pravidlo pro vyrovnávání zatížení.
4. Aktualizace nástroje pro vyrovnávání zatížení.

>[!NOTE]
>Pokud potřebujete zjistit název služby Vyrovnávání zatížení, použijte tento příkaz rychle získat seznam všech služeb Vyrovnávání zatížení a skupin přidružených prostředků.
>
>`Get-AzureRmLoadBalancer | Select Name, ResourceGroupName`

```powershell
# Get the load balancer
$lb = Get-AzureRmLoadBalancer -Name LB-svcfab3 -ResourceGroupName svcfab_cli

# Create the rule based on information from the load balancer.
$lbrule = New-AzureRmLoadBalancerRuleConfig -Name my-app-rule7 -Protocol Tcp -FrontendPort 39990 -BackendPort 40009 `
                                            -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
                                            -BackendAddressPool  $lb.BackendAddressPools[0] `
                                            -Probe $lb.Probes[0]

# Add the rule to the load balancer
$lb.LoadBalancingRules.Add($lbrule)

# Update the load balancer on Azure
$lb | Set-AzureRmLoadBalancer
```

Ohledně `New-AzureRmLoadBalancerRuleConfig` příkaz, `-FrontendPort` reprezentuje port pro vyrovnávání zatížení vystaví pro externí připojení a `-BackendPort` představuje aplikace service fabric naslouchá na portu.

>[!NOTE]
>Další informace o tom, jak vytvořit nástroj pro vyrovnávání zatížení v prostředí PowerShell najdete v tématu [vytvořit nástroj pro vyrovnávání zatížení v prostředí PowerShell](..\load-balancer\load-balancer-get-started-internet-arm-ps.md).

## <a name="next-steps"></a>Další postup

Další informace o [sítě v Service Fabric](service-fabric-patterns-networking.md).