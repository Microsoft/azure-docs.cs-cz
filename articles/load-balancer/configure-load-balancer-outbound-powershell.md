---
title: Konfigurace pravidel vyrovnávání zatížení a odchozích pravidel pomocí Azure PowerShellu
titleSuffix: Azure Load Balancer
description: Tento článek ukazuje, jak nakonfigurovat vyrovnávání zatížení a odchozí pravidla ve standardním vyrovnávání zatížení pomocí Azure PowerShellu.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: 5fd68f4559420ca688b3f4d6f6d66ee52db5191e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74225447"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-azure-powershell"></a>Konfigurace pravidel vyrovnávání zatížení a odchozích pravidel ve standardním balanceru zatížení pomocí Azure PowerShellu

Tento článek ukazuje, jak nakonfigurovat odchozí pravidla ve standardním vyvyřizovači zatížení pomocí Azure PowerShellu.  

Po dokončení scénáře tohoto článku obsahuje prostředek vyrovnávání zatížení dva front-endy a jejich přidružená pravidla. Máte jeden front-end pro příchozí provoz a jiný front-end pro odchozí provoz.  

Každý front-end odkazuje na veřejnou IP adresu. V tomto scénáři se veřejná IP adresa pro příchozí provoz liší od adresy pro odchozí provoz.   Pravidlo vyrovnávání zatížení poskytuje pouze příchozí vyrovnávání zatížení. Odchozí pravidlo řídí překlad odchozích síťových adres (NAT) pro virtuální hod.  

Scénář používá dva back-endové fondy: jeden pro příchozí provoz a jeden pro odchozí provoz. Tyto fondy ilustrují schopnosti a poskytují flexibilitu pro scénář.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-your-azure-account"></a>Připojení k účtu Azure
Přihlaste se ke svému předplatnému Azure pomocí příkazu [Connect-AzAccount.](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) Poté postupujte podle pokynů na obrazovce.
    
```azurepowershell-interactive
Connect-AzAccount
```
## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí [new-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0). Skupina prostředků Azure je logický kontejner, do kterého se nasazují prostředky Azure. Prostředky jsou pak spravovány ze skupiny.

Následující příklad vytvoří skupinu prostředků s názvem *myresourcegroupoutbound* v umístění *eastus2:*

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroupoutbound -Location eastus
```
## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
Vytvořte virtuální síť s názvem *myvnetoutbound*. Pojmenujte jeho podsíť *mysubnetoutbound*. Umístěte jej do *myresourcegroupoutbound* pomocí [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=azps-2.6.0) a [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=azps-2.6.0).

```azurepowershell-interactive
$subnet = New-AzVirtualNetworkSubnetConfig -Name mysubnetoutbound -AddressPrefix "192.168.0.0/24"

New-AzVirtualNetwork -Name myvnetoutbound -ResourceGroupName myresourcegroupoutbound -Location eastus -AddressPrefix "192.168.0.0/16" -Subnet $subnet
```

## <a name="create-an-inbound-public-ip-address"></a>Vytvoření příchozí veřejné IP adresy 

Pro přístup k webové aplikaci na internetu potřebujete veřejnou IP adresu pro vyrovnávání zatížení. Standardní vyrovnávání zatížení podporuje pouze standardní veřejné IP adresy. 

Pomocí [funkce New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0) vytvořte standardní veřejnou IP adresu s názvem *mypublicipinbound* in *myresourcegroupoutbound*.

```azurepowershell-interactive
$pubIPin = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipinbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-outbound-public-ip-address"></a>Vytvoření odchozí veřejné IP adresy 

Vytvořte standardní adresu IP pro odchozí konfiguraci front-endu nástroje pro vyrovnávání zatížení pomocí služby [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0).

```azurepowershell-interactive
$pubIPout = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipoutbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-azure-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení Azure

Tato část vysvětluje, jak vytvořit a nakonfigurovat následující součásti systému pro vyrovnávání zatížení:
  - Front-endOVÁ IP adresa, která přijímá příchozí síťový provoz na vyrovnávání zatížení
  - Back-endový fond, kde front-end IP odesílá síťový provoz s vyrovnáváním zatížení
  - Back-endový fond pro odchozí připojení
  - Sonda stavu, která určuje stav instancí back-endového virtuálního soudu
  - Pravidlo příchozího vyvažovače zatížení, které definuje způsob distribuce provozu na virtuální chod
  - Odchozí pravidlo vyvažovače zatížení, které definuje způsob distribuce provozu z virtuálních mkven.

### <a name="create-an-inbound-front-end-ip"></a>Vytvoření příchozí adresy IP front-endu
Vytvořte příchozí front-endovou konfiguraci IP pro nástroj pro vyrovnávání zatížení pomocí [nástroje New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0). Nástroj pro vyrovnávání zatížení by měl obsahovat příchozí front-end ovou konfiguraci IP s názvem *myfrontendinbound*. Přidružte tuto konfiguraci k veřejné IP adrese *mypublicipinbound*.

```azurepowershell-interactive
$frontendIPin = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendinbound" -PublicIpAddress $pubIPin
```
### <a name="create-an-outbound-front-end-ip"></a>Vytvoření odchozí adresy IP front-endu
Vytvořte konfiguraci ip adresy front-end pro nástroj pro vyrovnávání zatížení pomocí [nástroje New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0). Tento nástroj pro vyrovnávání zatížení by měl obsahovat odchozí front-end IP konfiguraci s názvem *myfrontendoutbound*. Přidružte tuto konfiguraci k veřejné IP adrese *mypublicipoutbound*.

```azurepowershell-interactive
$frontendIPout = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendoutbound" -PublicIpAddress $pubIPout
```
### <a name="create-an-inbound-back-end-pool"></a>Vytvoření příchozího back-endového fondu
Vytvořte back-endový příchozí fond pro nástroj pro vyrovnávání zatížení pomocí [aplikace New-AzLoadBalancerBackendAddressComfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0). Pojmenujte fond *bepoolinbound*.

```azurepowershell-interactive
$bepoolin = New-AzLoadBalancerBackendAddressPoolConfig -Name bepoolinbound
``` 

### <a name="create-an-outbound-back-end-pool"></a>Vytvoření odchozího back-endového fondu
Pomocí následujícího příkazu vytvořte jiný fond adres back-end k definování odchozího připojení pro fond virtuálních zařízení pomocí [příkazu New-AzLoadBalancerBackendEndConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0). Pojmenujte tento fond *bepooloutbound*. 

Vytvořením samostatného odchozího fondu poskytujete maximální flexibilitu. Ale můžete vynechat tento krok a použít pouze příchozí *bepoolinbound,* pokud dáváte přednost.  

```azurepowershell-interactive
$bepoolout = New-AzLoadBalancerBackendAddressPoolConfig -Name bepooloutbound
``` 

### <a name="create-a-health-probe"></a>Vytvoření sondy stavu

Sonda stavu kontroluje všechny instance virtuálních připojení a ujistěte se, že mohou odesílat síťový provoz. Instance virtuálního virtuálního serveru, která selže kontroly sondy je odebrána z vykladače zatížení, dokud přejde zpět do režimu online a kontrola sondy určuje, že je v pořádku. 

Chcete-li sledovat stav virtuálních měn, vytvořte sondu stavu pomocí [aplikace New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig?view=azps-2.6.0). 

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name http -Protocol "http" -Port 80 -IntervalInSeconds 15 -ProbeCount 2 -RequestPath /
```
### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení definuje konfiguraci ip adres front-endu pro příchozí provoz a back-endový fond pro příjem provozu. Definuje také požadovaný zdrojový a cílový port. 

Vytvořte pravidlo nástrojpro vyrovnávání zatížení s názvem *myinboundlbrule* pomocí [new-azloadbalancerruleconfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig?view=azps-2.6.0). Toto pravidlo bude poslouchat port 80 v front-end pool *myfrontendinbound*. Bude také používat port 80 k odeslání zatížení s vyrovnáváním síťového provozu do fondu back-endových adres *bepoolinbound*. 

```azurepowershell-interactive
$inboundRule = New-AzLoadBalancerRuleConfig -Name inboundlbrule -FrontendIPConfiguration $frontendIPin -BackendAddressPool $bepoolin -Probe $probe -Protocol "Tcp" -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -DisableOutboundSNAT
```

>[!NOTE]
>Toto pravidlo vyrovnávání zatížení zakáže automatické odchozí zabezpečené NAT (SNAT) z důvodu parametru **-DisableOutBoundSNAT.** Odchozí NAT je poskytována pouze odchozí pravidlo.

### <a name="create-an-outbound-rule"></a>Vytvoření pravidla odchozích přenosů

Odchozí pravidlo definuje front-end ovou veřejnou IP adresu, která je reprezentována *front-endendoutbound*. Tento front-end bude použit pro všechny odchozí přenosy NAT, stejně jako back-end ový fond, na který se pravidlo vztahuje.  

Pomocí následujícího příkazu vytvořte odchozí pravidlo *myoutboundrule* pro odchozí síťový překlad všech virtuálních počítačů (v konfiguracích IP síťových připojení) v back-endfondu *bepool.*  Příkaz změní časový limit odchozí horečné jízdy ze 4 na 15 minut. Přiděluje 10 000 portů SNAT namísto 1 024. Další informace naleznete v [tématu New-AzLoadBalancerOutboundRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalanceroutboundruleconfig?view=azps-2.7.0).

```azurepowershell-interactive
 $outboundRule = New-AzLoadBalancerOutBoundRuleConfig -Name outboundrule -FrontendIPConfiguration $frontendIPout -BackendAddressPool $bepoolout -Protocol All -IdleTimeoutInMinutes 15 -AllocatedOutboundPort 10000
```
Pokud nechcete používat samostatný odchozí fond, můžete místo toho změnit argument fondu adres v předchozím příkazu a zadat *tak $bepoolin.*  Doporučujeme používat samostatné fondy, aby výsledná konfigurace byla flexibilní a čitelná.

### <a name="create-a-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení

Pomocí následujícího příkazu vytvořte pro příchozí adresu IP pomocí příkazu [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer?view=azps-2.6.0)příkaz ustavit vyvažování zatížení . Pojmenujte i provyčitatele *zatížení lb*. Měla by obsahovat příchozí front-endovou konfiguraci IP adres. Jeho back-end ový fond *bepoolinbound* by měl být přidružen k veřejné IP adrese *mypublicipinbound,* kterou jste vytvořili v předchozím kroku.

```azurepowershell-interactive
New-AzLoadBalancer -Name lb -Sku Standard -ResourceGroupName myresourcegroupoutbound -Location eastus -FrontendIpConfiguration $frontendIPin,$frontendIPout -BackendAddressPool $bepoolin,$bepoolout -Probe $probe -LoadBalancingRule $inboundrule -OutboundRule $outboundrule 
```

V tomto okamžiku můžete pokračovat v přidávání virtuálních počítačů do *fondů back-end u vzátohých* i *bepooloutbound* aktualizací konfigurace IP příslušných prostředků nic. Aktualizujte konfiguraci prostředku pomocí [funkce Add-AzNetworkInterfaceIpConfig](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete skupinu prostředků, vyrovnávání zatížení a související prostředky, můžete je odebrat pomocí [skupiny Odebrat AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.7.0).

```azurepowershell-interactive 
  Remove-AzResourceGroup -Name myresourcegroupoutbound
```

## <a name="next-steps"></a>Další kroky
V tomto článku jste vytvořili standardní vyrovnávání zatížení, nakonfiguroval příchozí i odchozí pravidla provozu vyrovnávání zatížení a nakonfiguroval sondu stavu pro virtuální počítače v back-endfondu. 

Další informace najdete v [kurzech pro Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md).
