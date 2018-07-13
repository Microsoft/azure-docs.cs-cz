---
title: Vytvořte pravidlo nástroje pro vyrovnávání zatížení Azure pro cluster
description: Konfigurace služby Azure Load Balancer otevřít porty pro váš cluster Azure Service Fabric.
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: ryanwi
ms.openlocfilehash: e4c3bf627c4a5e01c4d9001fcbb0feed0b92209f
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008038"
---
# <a name="open-ports-for-a-service-fabric-cluster"></a>Otevřete porty pro cluster Service Fabric

Nástroje pro vyrovnávání zatížení, který je nasazený pomocí clusteru Azure Service Fabric směruje provoz do vaší aplikace spuštěný v uzlu. Pokud změníte vaše aplikace bude moct používat jiný port, musí zveřejnit tento port (nebo směrovat jiný port) ve službě Azure Load Balancer.

Po nasazení clusteru Service Fabric do Azure pro vás byl automaticky vytvořen nástroj pro vyrovnávání zatížení. Pokud nemáte nástroj pro vyrovnávání zatížení, přečtěte si téma [konfigurace nástroje pro vyrovnávání zatížení přístupem k Internetu](..\load-balancer\load-balancer-get-started-internet-portal.md).

## <a name="configure-service-fabric"></a>Konfigurace service fabric

Aplikace Service Fabric **ServiceManifest.xml** konfigurační soubor definuje koncové body očekává, že vaše aplikace používat. Po aktualizaci konfiguračního souboru k definování koncového bodu, nástroj pro vyrovnávání zatížení musí být aktualizovány k vystavení, který (nebo jinou) portu. Další informace o tom, jak vytvořit koncový bod service fabric najdete v tématu [nastavení koncového bodu](service-fabric-service-manifest-resources.md).

## <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení otevře port přístupem k Internetu a předává provoz na portu vnitřní uzlu používaný vaší aplikací. Pokud nemáte nástroj pro vyrovnávání zatížení, přečtěte si téma [konfigurace nástroje pro vyrovnávání zatížení přístupem k Internetu](..\load-balancer\load-balancer-get-started-internet-portal.md).

Vytvořte pravidlo nástroje pro vyrovnávání zatížení, je třeba shromažďovat následující informace:

- Název nástroje pro vyrovnávání zatížení.
- Skupina prostředků clusteru zatížení nástroje pro vyrovnávání a service fabric.
- Externí port.
- Interní port.

## <a name="azure-cli"></a>Azure CLI
Vytvořte pravidlo nástroje pro vyrovnávání zatížení pomocí jediného příkazu trvá jen **rozhraní příkazového řádku Azure**. Potřebujete znát název zatížení nástroje pro vyrovnávání prostředků skupině a vytvořit nové pravidlo.

>[!NOTE]
>Pokud je potřeba určit název nástroje pro vyrovnávání zatížení, pomocí tohoto příkazu se rychle získat seznam všech nástrojů pro vyrovnávání zatížení a skupiny přidružený prostředek.
>
>`az network lb list --query "[].{ResourceGroup: resourceGroup, Name: name}"`
>


```azurecli
az network lb rule create --backend-port 40000 --frontend-port 39999 --protocol Tcp --lb-name LB-svcfab3 -g svcfab_cli -n my-app-rule
```

Pomocí příkazu Azure CLI má několik parametrů, které jsou popsány v následující tabulce:

| Parametr | Popis |
| --------- | ----------- |
| `--backend-port`  | Aplikace Service Fabric portu naslouchá. |
| `--frontend-port` | Port pro vyrovnávání zatížení poskytuje pro externí připojení. |
| `-lb-name` | Název nástroje pro vyrovnávání zatížení, chcete-li změnit. |
| `-g`       | Skupina prostředků, který má nástroj pro vyrovnávání zatížení a cluster Service Fabric. |
| `-n`       | Požadovaný název pravidla. |


>[!NOTE]
>Další informace o tom, jak vytvořit nástroj pro vyrovnávání zatížení pomocí Azure CLI najdete v tématu [vytvořit nástroj pro vyrovnávání zatížení pomocí Azure CLI](..\load-balancer\load-balancer-get-started-internet-arm-cli.md).

## <a name="powershell"></a>PowerShell

PowerShell je o něco složitější než rozhraní příkazového řádku Azure. Následujícím postupem koncepční vytvořit pravidlo:

1. Získejte nástroje pro vyrovnávání zatížení z Azure.
2. Vytvořte pravidlo.
3. Přidáte pravidlo pro vyrovnávání zatížení.
4. Aktualizujte nástroj pro vyrovnávání zatížení.

>[!NOTE]
>Pokud je potřeba určit název nástroje pro vyrovnávání zatížení, použijte tento příkaz rychle získat seznam všech nástrojů pro vyrovnávání zatížení a přidružený prostředek skupiny.
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

Ohledně `New-AzureRmLoadBalancerRuleConfig` příkazu `-FrontendPort` představuje port, který poskytuje nástroje pro vyrovnávání zatížení pro externí připojení, a `-BackendPort` představuje aplikace service fabric naslouchá na portu.

>[!NOTE]
>Další informace o tom, jak vytvořit nástroj pro vyrovnávání zatížení pomocí prostředí PowerShell najdete v tématu [vytvoření nástroje pro vyrovnávání zatížení pomocí prostředí PowerShell](..\load-balancer\load-balancer-get-started-internet-arm-ps.md).

## <a name="next-steps"></a>Další postup

Další informace o [sítě v Service Fabric](service-fabric-patterns-networking.md).