---
title: Konfigurace vyrovnávání zatížení a odchozích pravidel pomocí Azure PowerShell
titleSuffix: Azure Load Balancer
description: V tomto článku se dozvíte, jak nakonfigurovat pravidla vyrovnávání zatížení a odchozí pravidla v Standard Load Balancer pomocí Azure PowerShell.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: 5fd68f4559420ca688b3f4d6f6d66ee52db5191e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225447"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-azure-powershell"></a>Konfigurace vyrovnávání zatížení a odchozích pravidel v Standard Load Balancer pomocí Azure PowerShell

V tomto článku se dozvíte, jak nakonfigurovat odchozí pravidla v Standard Load Balancer pomocí Azure PowerShell.  

Po dokončení scénáře tohoto článku obsahuje prostředek nástroje pro vyrovnávání zatížení dva front-endy a jejich přidružená pravidla. Máte jeden front-end pro příchozí provoz a další front-end pro odchozí provoz.  

Každý front-end odkazuje na veřejnou IP adresu. V tomto scénáři se veřejná IP adresa pro příchozí přenosy liší od adresy pro odchozí provoz.   Pravidlo vyrovnávání zatížení poskytuje pouze příchozí vyrovnávání zatížení. Pravidlo odchozího řízení odchozího překladu adres (NAT) pro virtuální počítač.  

Scénář používá dva fondy back-endu: jeden pro příchozí provoz a jeden pro odchozí provoz. Tyto fondy ilustrují schopnost a poskytují flexibilitu pro scénář.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-your-azure-account"></a>Připojení k účtu Azure
Přihlaste se k předplatnému Azure pomocí příkazu [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) . Pak postupujte podle pokynů na obrazovce.
    
```azurepowershell-interactive
Connect-AzAccount
```
## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0). Skupina prostředků Azure je logický kontejner, do kterého se nasazují prostředky Azure. Prostředky se pak spravují ze skupiny.

Následující příklad vytvoří skupinu prostředků s názvem *myresourcegroupoutbound* v umístění *eastus2* :

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroupoutbound -Location eastus
```
## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
Vytvořte virtuální síť s názvem *myvnetoutbound*. Pojmenujte *mysubnetoutbound*podsítě. Umístěte ho v *myresourcegroupoutbound* pomocí [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=azps-2.6.0) a [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=azps-2.6.0).

```azurepowershell-interactive
$subnet = New-AzVirtualNetworkSubnetConfig -Name mysubnetoutbound -AddressPrefix "192.168.0.0/24"

New-AzVirtualNetwork -Name myvnetoutbound -ResourceGroupName myresourcegroupoutbound -Location eastus -AddressPrefix "192.168.0.0/16" -Subnet $subnet
```

## <a name="create-an-inbound-public-ip-address"></a>Vytvoření příchozí veřejné IP adresy 

Pro přístup k webové aplikaci na internetu budete potřebovat veřejnou IP adresu pro nástroj pro vyrovnávání zatížení. Standard Load Balancer podporuje pouze standardní veřejné IP adresy. 

Pomocí [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0) vytvořte standardní veřejnou IP adresu s názvem *mypublicipinbound* v *myresourcegroupoutbound*.

```azurepowershell-interactive
$pubIPin = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipinbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-outbound-public-ip-address"></a>Vytvoření odchozí veřejné IP adresy 

Pomocí [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0)vytvořte standardní IP adresu pro front-end odchozí konfiguraci služby Vyrovnávání zatížení.

```azurepowershell-interactive
$pubIPout = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipoutbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-azure-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení Azure

V této části se dozvíte, jak vytvořit a nakonfigurovat následující komponenty nástroje pro vyrovnávání zatížení:
  - Front-end IP adresa, která přijímá příchozí síťový provoz v nástroji pro vyrovnávání zatížení.
  - Fond back-end, kde front-end IP adresa odesílá síťový provoz s vyrovnáváním zatížení
  - Fond back-end pro odchozí připojení
  - Sonda stavu, která určuje stav instancí back-endu virtuálních počítačů.
  - Příchozí pravidlo pro vyrovnávání zatížení, které definuje způsob distribuce provozu do virtuálních počítačů
  - Odchozí pravidlo nástroje pro vyrovnávání zatížení, které definuje způsob distribuce provozu z virtuálních počítačů

### <a name="create-an-inbound-front-end-ip"></a>Vytvoření příchozí IP adresy front-endu
Vytvořte příchozí konfiguraci front-endu IP pro nástroj pro vyrovnávání zatížení pomocí [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0). Nástroj pro vyrovnávání zatížení by měl zahrnovat příchozí konfiguraci front-end IP adresy s názvem *myfrontendinbound*. Přidružte tuto konfiguraci k veřejné IP adrese *mypublicipinbound*.

```azurepowershell-interactive
$frontendIPin = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendinbound" -PublicIpAddress $pubIPin
```
### <a name="create-an-outbound-front-end-ip"></a>Vytvoření odchozí IP adresy front-endu
Vytvořte odchozí konfiguraci front-end IP adresy pro nástroj pro vyrovnávání zatížení pomocí [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0). Tento nástroj pro vyrovnávání zatížení by měl zahrnovat odchozí konfiguraci front-endové IP adresy s názvem *myfrontendoutbound*. Přidružte tuto konfiguraci k veřejné IP adrese *mypublicipoutbound*.

```azurepowershell-interactive
$frontendIPout = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendoutbound" -PublicIpAddress $pubIPout
```
### <a name="create-an-inbound-back-end-pool"></a>Vytvoření příchozího fondu back-endu
Pomocí [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0)vytvořte back-end příchozí fond pro nástroj pro vyrovnávání zatížení. Pojmenujte fond *bepoolinbound*.

```azurepowershell-interactive
$bepoolin = New-AzLoadBalancerBackendAddressPoolConfig -Name bepoolinbound
``` 

### <a name="create-an-outbound-back-end-pool"></a>Vytvoření odchozího fondu back-endu
Pomocí následujícího příkazu vytvořte další fond back-end adres pro definování odchozího připojení pro fond virtuálních počítačů pomocí příkazu [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0). Pojmenujte tento fond *bepooloutbound*. 

Vytvořením samostatného odchozího fondu zajistíte maximální flexibilitu. Ale tento krok můžete vynechat a použít jenom příchozí *bepoolinbound* , pokud to dáváte přednost.  

```azurepowershell-interactive
$bepoolout = New-AzLoadBalancerBackendAddressPoolConfig -Name bepooloutbound
``` 

### <a name="create-a-health-probe"></a>Vytvoření sondy stavu

Sonda stavu kontroluje všechny instance virtuálních počítačů, aby bylo zajištěno, že budou moci odesílat síťový provoz. Instance virtuálního počítače, která selhala při kontrolách testu, se z nástroje pro vyrovnávání zatížení odebere, dokud se nepřejde do režimu online a kontrola sondy zjistí, že je v pořádku. 

Pokud chcete monitorovat stav virtuálních počítačů, vytvořte sondu stavu pomocí [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig?view=azps-2.6.0). 

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name http -Protocol "http" -Port 80 -IntervalInSeconds 15 -ProbeCount 2 -RequestPath /
```
### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení definuje konfiguraci front-endové IP adresy pro příchozí provoz a fond back-end pro příjem provozu. Definuje také požadovaný zdrojový a cílový port. 

Vytvořte pravidlo nástroje pro vyrovnávání zatížení s názvem *myinboundlbrule* pomocí [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig?view=azps-2.6.0). Toto pravidlo bude naslouchat portu 80 ve front-end fondu *myfrontendinbound*. K odeslání síťového provozu s vyrovnáváním zatížení do fondu back-end adres *bepoolinbound*bude také použit port 80. 

```azurepowershell-interactive
$inboundRule = New-AzLoadBalancerRuleConfig -Name inboundlbrule -FrontendIPConfiguration $frontendIPin -BackendAddressPool $bepoolin -Probe $probe -Protocol "Tcp" -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -DisableOutboundSNAT
```

>[!NOTE]
>Toto pravidlo vyrovnávání zatížení zakáže automatické odchozí zabezpečené NAT (SNAT) z důvodu parametru **-DisableOutboundSNAT** . Odchozí překlad adres (NAT) poskytuje jenom odchozí pravidlo.

### <a name="create-an-outbound-rule"></a>Vytvoření pravidla odchozích přenosů

Odchozí pravidlo definuje front-endové veřejné IP adresy, které jsou reprezentovány front-end *myfrontendoutbound*. Tento front-end bude použit pro všechny odchozí přenosy NAT i pro fond back-end, na který se pravidlo vztahuje.  

Pomocí následujícího příkazu vytvořte odchozí pravidlo *myoutboundrule* pro odchozí síťový překlad všech virtuálních počítačů (v KONFIGURACÍch IP síťových adaptérů) ve fondu back-end *bepool* .  Příkaz změní odchozí časový limit nečinnosti ze 4 na 15 minut. Přiděluje porty 10 000 SNAT místo 1 024. Další informace najdete v tématu [New-AzLoadBalancerOutboundRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalanceroutboundruleconfig?view=azps-2.7.0).

```azurepowershell-interactive
 $outboundRule = New-AzLoadBalancerOutBoundRuleConfig -Name outboundrule -FrontendIPConfiguration $frontendIPout -BackendAddressPool $bepoolout -Protocol All -IdleTimeoutInMinutes 15 -AllocatedOutboundPort 10000
```
Pokud nechcete používat samostatný odchozí fond, můžete v předchozím příkazu změnit argument fondu adres, aby se místo toho určil *$bepoolin* .  Pro zajištění flexibility a čitelnosti výsledných konfigurací doporučujeme použít samostatné fondy.

### <a name="create-a-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení

Pomocí následujícího příkazu vytvořte Nástroj pro vyrovnávání zatížení pro příchozí IP adresu pomocí příkazu [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer?view=azps-2.6.0). Pojmenujte vyrovnávání *zatížení.* Měl by obsahovat příchozí konfiguraci front-end IP adresy. Ke svému fondu back-end *bepoolinbound* by měl být přidružená veřejná IP adresa *mypublicipinbound* , kterou jste vytvořili v předchozím kroku.

```azurepowershell-interactive
New-AzLoadBalancer -Name lb -Sku Standard -ResourceGroupName myresourcegroupoutbound -Location eastus -FrontendIpConfiguration $frontendIPin,$frontendIPout -BackendAddressPool $bepoolin,$bepoolout -Probe $probe -LoadBalancingRule $inboundrule -OutboundRule $outboundrule 
```

V tuto chvíli můžete pokračovat v přidávání virtuálních počítačů do fondů back-end *bepoolinbound* i *bepooloutbound* tím, že aktualizujete konfiguraci protokolu IP příslušných prostředků síťových adaptérů. Aktualizujte konfiguraci prostředků pomocí [Add-AzNetworkInterfaceIpConfig](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nepotřebujete skupinu prostředků, nástroj pro vyrovnávání zatížení a související prostředky, můžete je odebrat pomocí [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.7.0).

```azurepowershell-interactive 
  Remove-AzResourceGroup -Name myresourcegroupoutbound
```

## <a name="next-steps"></a>Další kroky
V tomto článku jste vytvořili standardní nástroj pro vyrovnávání zatížení, nakonfigurovali jste pravidla pro příchozí i odchozí provoz vyrovnávání zatížení a nakonfigurovali sondu stavu pro virtuální počítače ve fondu back-end. 

Pokud se chcete dozvědět víc, přejděte k [kurzům Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md).
