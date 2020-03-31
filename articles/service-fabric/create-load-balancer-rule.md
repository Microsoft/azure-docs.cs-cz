---
title: Vytvoření pravidla Azure Load Balancer pro cluster
description: Nakonfigurujte Azure Balancer pro otevření portů pro váš cluster Azure Service Fabric.
ms.topic: conceptual
ms.date: 12/06/2017
ms.openlocfilehash: f4599b2e0174381ab7df04aeeb33db7e3ee60f26
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025380"
---
# <a name="open-ports-for-a-service-fabric-cluster"></a>Otevření portů pro cluster Service Fabric

Nástroj pro vyrovnávání zatížení nasazený pomocí clusteru Azure Service Fabric směruje provoz do vaší aplikace spuštěné na uzlu. Pokud změníte aplikaci tak, aby používala jiný port, musíte tento port (nebo směrovat jiný port) v Azure Load Balancer.

Když jste nasadili cluster Service Fabric do Azure, automaticky se pro vás vytvořil balancer. Pokud nemáte správce zatížení, přečtěte si informace [o konfiguraci internetového vyrovnávání zatížení](../load-balancer/load-balancer-get-started-internet-portal.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-service-fabric"></a>Konfigurace prostředků infrastruktury služeb

Konfigurační soubor service fabric aplikace **ServiceManifest.xml** definuje koncové body, které aplikace očekává. Po aktualizaci konfiguračního souboru, aby byl definován koncový bod, musí být nástroj pro vyrovnávání zatížení aktualizován, aby byl vystaven tomuto (nebo jinému) portu. Další informace o vytvoření koncového bodu prostředků infrastruktury služby naleznete v [tématu Nastavení koncového bodu](service-fabric-service-manifest-resources.md).

## <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo vyrovnávání zatížení otevře internetový port a předá přenos na port interního uzlu používaného vaší aplikací. Pokud nemáte správce zatížení, přečtěte si informace [o konfiguraci internetového vyrovnávání zatížení](../load-balancer/load-balancer-get-started-internet-portal.md).

Chcete-li vytvořit pravidlo pro vyrovnávání zatížení, je třeba shromáždit následující informace:

- Název pro vyrovnávání zatížení.
- Skupina prostředků vykladače zatížení a clusteru prostředků infrastruktury služeb.
- Externí port.
- Vnitřní port.

## <a name="azure-cli"></a>Azure CLI
Trvá pouze jeden příkaz k vytvoření pravidla vyrovnávání zatížení s **Azure CLI**. Stačí znát název správce zatížení a skupiny prostředků a vytvořit nové pravidlo.

>[!NOTE]
>Pokud potřebujete určit název správce zatížení, pomocí tohoto příkazu rychle získat seznam všech vyrovnávání zatížení a přidružené skupiny prostředků.
>
>`az network lb list --query "[].{ResourceGroup: resourceGroup, Name: name}"`
>


```azurecli
az network lb rule create --backend-port 40000 --frontend-port 39999 --protocol Tcp --lb-name LB-svcfab3 -g svcfab_cli -n my-app-rule
```

Příkaz Azure CLI má několik parametrů, které jsou popsány v následující tabulce:

| Parametr | Popis |
| --------- | ----------- |
| `--backend-port`  | Port aplikace Service Fabric naslouchá. |
| `--frontend-port` | Port, který vyrovnává zatížení zveřejňuje pro externí připojení. |
| `-lb-name` | Název provynace zatížení změnit. |
| `-g`       | Skupina prostředků, která má jak vyrovnávání zatížení, tak cluster Service Fabric. |
| `-n`       | Požadovaný název pravidla. |


>[!NOTE]
>Další informace o tom, jak vytvořit vyrovnávání zatížení pomocí azure cli, najdete v [tématu vytvoření vyrovnávání zatížení s Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md).

## <a name="powershell"></a>PowerShell

Prostředí PowerShell je trochu složitější než azure CLI. Chcete-li vytvořit pravidlo, postupujte takto:

1. Získejte z Azure pro vyrovnávání zatížení.
2. Vytvořte pravidlo.
3. Přidejte pravidlo do pravidla pro vyrovnávání zatížení.
4. Aktualizujte systém vyrovnávání zatížení.

>[!NOTE]
>Pokud potřebujete určit název správce zatížení, pomocí tohoto příkazu rychle získat seznam všech vyrovnávání zatížení a přidružené skupiny prostředků.
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

Pokud `New-AzLoadBalancerRuleConfig` jde o `-FrontendPort` příkaz představuje port vyrovnávání zatížení zpřístupňuje `-BackendPort` pro externí připojení a představuje port aplikace prostředků služby je naslouchání.

>[!NOTE]
>Další informace o tom, jak vytvořit vyvyřičůč ů s Prostředím PowerShell, najdete v [tématu Vytvoření systému pro vyrovnávání zatížení pomocí prostředí PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md).

## <a name="next-steps"></a>Další kroky

Další informace o [vytváření sítí v service fabric](service-fabric-patterns-networking.md).
