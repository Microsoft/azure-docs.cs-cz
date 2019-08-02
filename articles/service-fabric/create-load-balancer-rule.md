---
title: Vytvoření pravidla Azure Load Balancer pro cluster
description: Nakonfigurujte Azure Load Balancer pro otevření portů pro cluster Azure Service Fabric.
services: service-fabric
documentationcenter: na
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: atsenthi
ms.openlocfilehash: 440d5e37e03e58a39275e715ebce4fa07961cc84
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598581"
---
# <a name="open-ports-for-a-service-fabric-cluster"></a>Otevření portů pro cluster Service Fabric

Nástroj pro vyrovnávání zatížení, který je nasazený s vaším clusterem Azure Service Fabric, směruje provoz do vaší aplikace spuštěné na uzlu. Pokud změníte svoji aplikaci tak, aby používala jiný port, musíte tento port (nebo směrovat jiný port) v Azure Load Balancer.

Když nasadíte Cluster Service Fabric do Azure, automaticky se pro vás vytvoří nástroj pro vyrovnávání zatížení. Pokud nemáte Nástroj pro vyrovnávání zatížení, přečtěte si téma [Konfigurace internetového nástroje pro vyrovnávání zatížení](../load-balancer/load-balancer-get-started-internet-portal.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-service-fabric"></a>Konfigurace Service Fabric

Konfigurační soubor **ServiceManifest. XML** aplikace Service Fabric definuje koncové body, které vaše aplikace očekává použít. Po aktualizaci konfiguračního souboru pro definování koncového bodu musí být Nástroj pro vyrovnávání zatížení aktualizovaný, aby vystavil tento port (nebo jiný). Další informace o tom, jak vytvořit koncový bod Service Fabric, najdete v tématu [Nastavení koncového bodu](service-fabric-service-manifest-resources.md).

## <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo Load Balancer otevře Internetový port a přepošle provoz na port interního uzlu, který vaše aplikace používá. Pokud nemáte Nástroj pro vyrovnávání zatížení, přečtěte si téma [Konfigurace internetového nástroje pro vyrovnávání zatížení](../load-balancer/load-balancer-get-started-internet-portal.md).

Chcete-li vytvořit pravidlo Load Balancer, je nutné shromáždit následující informace:

- Název nástroje pro vyrovnávání zatížení.
- Skupina prostředků nástroje pro vyrovnávání zatížení a clusteru Service Fabric.
- Externí port.
- Interní port.

## <a name="azure-cli"></a>Azure CLI
K vytvoření pravidla nástroje pro vyrovnávání zatížení pomocí **Azure CLI**se používá jenom jeden příkaz. Jenom potřebujete znát název nástroje pro vyrovnávání zatížení a skupinu prostředků, abyste mohli vytvořit nové pravidlo.

>[!NOTE]
>Pokud potřebujete určit název nástroje pro vyrovnávání zatížení, použijte tento příkaz k rychlému získání seznamu všech nástrojů pro vyrovnávání zatížení a přidružených skupin prostředků.
>
>`az network lb list --query "[].{ResourceGroup: resourceGroup, Name: name}"`
>


```azurecli
az network lb rule create --backend-port 40000 --frontend-port 39999 --protocol Tcp --lb-name LB-svcfab3 -g svcfab_cli -n my-app-rule
```

Příkaz Azure CLI má několik parametrů, které jsou popsané v následující tabulce:

| Parametr | Popis |
| --------- | ----------- |
| `--backend-port`  | Port, na který aplikace Service Fabric naslouchá. |
| `--frontend-port` | Port, který nástroj pro vyrovnávání zatížení zveřejňuje pro externí připojení. |
| `-lb-name` | Název nástroje pro vyrovnávání zatížení, který se má změnit. |
| `-g`       | Skupina prostředků, která obsahuje nástroj pro vyrovnávání zatížení i Cluster Service Fabric. |
| `-n`       | Požadovaný název pravidla. |


>[!NOTE]
>Další informace o tom, jak vytvořit nástroj pro vyrovnávání zatížení pomocí Azure CLI, najdete v tématu [Vytvoření nástroje pro vyrovnávání zatížení pomocí Azure CLI](../load-balancer/load-balancer-get-started-internet-arm-cli.md).

## <a name="powershell"></a>PowerShell

PowerShell je trochu složitější než Azure CLI. Pomocí těchto koncepčních kroků vytvořte pravidlo:

1. Získejte nástroj pro vyrovnávání zatížení z Azure.
2. Vytvořte pravidlo.
3. Přidejte pravidlo do nástroje pro vyrovnávání zatížení.
4. Aktualizujte Nástroj pro vyrovnávání zatížení.

>[!NOTE]
>Pokud potřebujete určit název nástroje pro vyrovnávání zatížení, použijte tento příkaz k rychlému získání seznamu všech nástrojů pro vyrovnávání zatížení a přidružených skupin prostředků.
>
>`Get-AzLoadBalancer | Select Name, ResourceGroupName`

```powershell
# Get the load balancer
$lb = Get-AzLoadBalancer -Name LB-svcfab3 -ResourceGroupName svcfab_cli

# Create the rule based on information from the load balancer.
$lbrule = New-AzLoadBalancerRuleConfig -Name my-app-rule7 -Protocol Tcp -FrontendPort 39990 -BackendPort 40009 `
                                            -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
                                            -BackendAddressPool  $lb.BackendAddressPools[0] `
                                            -Probe $lb.Probes[0]

# Add the rule to the load balancer
$lb.LoadBalancingRules.Add($lbrule)

# Update the load balancer on Azure
$lb | Set-AzLoadBalancer
```

V `New-AzLoadBalancerRuleConfig` `-BackendPort` souvislosti s příkazem představuje port, který nástroj pro vyrovnávání zatížení zveřejňuje pro externí připojení, a představuje port, na který aplikace Service Fabric naslouchá. `-FrontendPort`

>[!NOTE]
>Další informace o tom, jak vytvořit nástroj pro vyrovnávání zatížení pomocí PowerShellu, najdete v tématu [Vytvoření nástroje pro vyrovnávání zatížení pomocí PowerShellu](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="next-steps"></a>Další postup

Přečtěte si další informace o [sítích v Service Fabric](service-fabric-patterns-networking.md). rvice-Fabric-Patterns-Networking.MD).