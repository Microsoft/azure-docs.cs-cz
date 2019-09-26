---
title: Konfigurace vyrovnávání zatížení a odchozích pravidel pomocí Azure PowerShell
titlesuffix: Azure Load Balancer
description: V tomto článku se dozvíte, jak nakonfigurovat pravidla vyrovnávání zatížení a odchozí pravidla v Standard Load Balancer pomocí Azure PowerShell.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: 6e85d31e40e35b9d796fc66394a6eb446c7302ad
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262619"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-using-azure-powershell"></a>Konfigurace vyrovnávání zatížení a odchozích pravidel ve službě Load Balancer úrovně Standard pomocí Azure PowerShell

V tomto článku se dozvíte, jak nakonfigurovat odchozí pravidla ve službě Load Balancer úrovně Standard pomocí Azure PowerShell.  

Až skončíte, prostředek nástroje pro vyrovnávání zatížení obsahuje dva front-endové a pravidla, která jsou k nim přidružená: jedno pro příchozí a další pro odchozí.  Každá front-endu obsahuje odkaz na veřejné IP adresy a tento scénář používá jinou veřejnou IP adresu pro příchozí a odchozí.   Pravidlo Vyrovnávání zatížení poskytuje Vyrovnávání zatížení pouze příchozí a odchozí pravidlo určuje odchozí NAT pro virtuální počítač k dispozici.  Tento článek používá dva samostatné back-end fondy, jeden pro příchozí a jeden pro odchozí, pro ilustraci možností a umožňuje flexibilitu pro tento scénář.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-azure-account"></a>Připojit k účtu Azure
Přihlaste se k předplatnému Azure pomocí příkazu [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) a postupujte podle pokynů na obrazovce:
    
```azurepowershell-interactive
Connect-AzAccount
```
## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Následující příklad vytvoří skupinu prostředků s názvem *myresourcegroupoutbound* v *eastus2* umístění:

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroupoutbound -Location eastus
```
## <a name="create-virtual-network"></a>Vytvoření virtuální sítě
Vytvořte virtuální síť s názvem *myvnetoutbound* s podsítí s názvem *mysubnetoutbound* v *myresourcegroupoutbound* pomocí [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=azps-2.6.0) a [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=azps-2.6.0):

```azurepowershell-interactive
$subnet = New-AzVirtualNetworkSubnetConfig -Name mysubnetoutbound -AddressPrefix "192.168.0.0/24"

New-AzVirtualNetwork -Name myvnetoutbound -ResourceGroupName myresourcegroupoutbound -Location eastus -AddressPrefix "192.168.0.0/16" -Subnet $subnet
```

## <a name="create-inbound-public-ip-address"></a>Vytvoření příchozí veřejné IP adresy 

Pokud chcete mít k webové aplikaci přístup přes internet, potřebujete pro nástroj pro vyrovnávání zatížení veřejnou IP adresu. Standardní nástroj pro vyrovnávání zatížení podporuje jenom standardní veřejné IP adresy. Pomocí [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0) vytvořte standardní veřejnou IP adresu s názvem *mypublicipinbound* v *myresourcegroupoutbound*.

```azurepowershell-interactive
$pubIPin = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipinbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-outbound-public-ip-address"></a>Vytvoření výstupní veřejné IP adresy 

Pomocí [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0)vytvořte standardní IP adresu pro odchozí konfiguraci front-endu nástroje pro vyrovnávání zatížení.

```azurepowershell-interactive
$pubIPout = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipoutbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-azure-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení Azure

Tato část podrobně popisuje vytvoření a konfiguraci následujících komponent nástroje pro vyrovnávání zatížení:
  - IP adresa front-endu, který přijímá příchozí síťový provoz do nástroje pro vyrovnávání zatížení.
  - Back-end fond, ve kterém front-end IP odesílá síťový provoz s vyrovnáváním zatížení.
  - Back-end fond pro odchozí připojení. 
  - Sonda stavu, která určuje stav back-endových instancí virtuálních počítačů.
  - Příchozí pravidlo služby load balancer, který definuje způsob distribuce provozu do virtuálních počítačů.
  - Odchozí pravidlo služby load balancer, který definuje způsob distribuce provozu z virtuálních počítačů.

### <a name="create-inbound-frontend-ip"></a>Vytvořit příchozí IP adresu front-endu
Vytvořte odchozí konfiguraci IP adresy front-endu pro nástroj pro vyrovnávání zatížení pomocí [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0) , která zahrnuje příchozí konfiguraci IP adresy front-endu s názvem *myfrontendinbound* , která je přidružená k veřejné IP adrese *. mypublicipinbound*

```azurepowershell-interactive
$frontendIPin = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendinbound" -PublicIpAddress $pubIPin
```
### <a name="create-outbound-frontend-ip"></a>Vytvoření front-endu odchozí IP adresy
Vytvořte odchozí konfiguraci IP adresy front-endu pro nástroj pro vyrovnávání zatížení pomocí [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0) , která zahrnuje odchozí IP konfiguraci s názvem *myfrontendoutbound* , která je přidružená k veřejné IP adrese *. mypublicipoutbound*:

```azurepowershell-interactive
$frontendIPout = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendoutbound" -PublicIpAddress $pubIPout
```
### <a name="create-inbound-backend-pool"></a>Vytvořit příchozí back-end fond
Vytvořte pro nástroj pro vyrovnávání zatížení příchozí fond back-endu pomocí [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0) s názvem *bepoolinbound*:

```azurepowershell-interactive
$bepoolin = New-AzLoadBalancerBackendAddressPoolConfig -Name bepoolinbound
``` 

### <a name="create-outbound-backend-pool"></a>Vytvořit výstupní back-end fond
Vytvořte další fond back-end adres pro definování odchozího připojení pro fond virtuálních počítačů pomocí [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0) s názvem *bepooloutbound*. Vytvoření samostatného odchozího fondu poskytuje maximální flexibilitu, ale tento krok můžete vynechat a zároveň použít jenom příchozí *bepoolinbound* .  :

```azurepowershell-interactive
$bepoolout = New-AzLoadBalancerBackendAddressPoolConfig -Name bepooloutbound
``` 

### <a name="create-health-probe"></a>Vytvoření sondy stavu

Test stavu kontroluje všechny instance virtuálních počítačů a ověřuje, že mohou posílat síťový provoz. Instance virtuálního počítače, u níž se kontroly testu nezdaří, se odebere z nástroje pro vyrovnávání zatížení do té doby, než znovu přejde do režimu online a kontrola testu určí, že je v pořádku. Vytvořte sondu stavu pomocí [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig?view=azps-2.6.0) , abyste mohli monitorovat stav virtuálních počítačů. 

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name http -Protocol "http" -Port 80 -IntervalInSeconds 15 -ProbeCount 2 -RequestPath /
```
### <a name="create-load-balancing-rule"></a>Vytvořit pravidlo Vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení definuje konfiguraci front-endová IP adres pro příchozí provoz a back-endový fond adres pro příjem provozu a také požadovaný zdrojový a cílový port. Vytvořte pravidlo nástroje pro vyrovnávání zatížení *myinboundlbrule* pomocí [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig?view=azps-2.6.0) pro naslouchání portu 80 ve front-endu *myfrontendinbound* a odeslání síťového provozu s vyrovnáváním zatížení do fondu *back-end adres. bepoolinbound* také pomocí portu 80. 

>[!NOTE]
>Toto pravidlo vyrovnávání zatížení zakáže v důsledku tohoto pravidla automatický výstup (NAT) NAT s parametrem **-DisableOutboundSNAT** . Odchozí NAT se poskytuje jenom odchozí pravidlo.

```azurepowershell-interactive
$inboundRule = New-AzLoadBalancerRuleConfig -Name inboundlbrule -FrontendIPConfiguration $frontendIPin -BackendAddressPool $bepoolin -Probe $probe -Protocol "Tcp" -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -DisableOutboundSNAT
```

### <a name="create-outbound-rule"></a>Vytvoření odchozí pravidla

Odchozí pravidlo definuje veřejnou IP adresu front-endu, reprezentovaný front-endu *myfrontendoutbound*, který se bude používat pro veškerý odchozí provoz NAT, jakož i back-endový fond, do které bude použito toto pravidlo.  Vytvořit pravidlo odchozí *myoutboundrule* pro odchozí síťové překlad všechny virtuální počítače (Konfigurace IP adresy NIC) ve *bepool* back-endový fond.  Níže uvedený příkaz také odchozí časový limit nečinnosti se změní z 4 až 15 minut a přiděluje 10000 SNAT porty místo 1024.  Další podrobnosti najdete v [New-AzLoadBalancerOutboundRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalanceroutboundruleconfig?view=azps-2.7.0) .

```azurepowershell-interactive
 $outboundRule = New-AzLoadBalancerOutBoundRuleConfig -Name outboundrule -FrontendIPConfiguration $frontendIPout -BackendAddressPool $bepoolout -Protocol All -IdleTimeoutInMinutes 15 -AllocatedOutboundPort 10000
```
Pokud nechcete používat samostatný odchozí fond, můžete v předchozím příkazu změnit argument fondu adres, aby se místo toho určil *$bepoolin* .  Pro flexibilitu a čitelnost výsledných konfigurací doporučujeme používat samostatné fondy.

### <a name="create-load-balancer"></a>Vytvořit nástroj pro vyrovnávání zatížení

Vytvořte Nástroj pro vyrovnávání zatížení s příchozí IP adresou pomocí [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer?view=azps-2.6.0) s názvem " *kg* ", který zahrnuje příchozí IP adresu front-endu a back-end fond *bepoolinbound* , který je přidružený k veřejné IP adrese *. mypublicipinbound* , kterou jste vytvořili v předchozím kroku.

```azurepowershell-interactive
New-AzLoadBalancer -Name lb -Sku Standard -ResourceGroupName myresourcegroupoutbound -Location eastus -FrontendIpConfiguration $frontendIPin,$frontendIPout -BackendAddressPool $bepoolin,$bepoolout -Probe $probe -LoadBalancingRule $inboundrule -OutboundRule $outboundrule 
```

V tuto chvíli můžete pokračovat v přidávání virtuálních počítačů do back-endu *bepoolinbound* __a__ *bepooloutbound* tím, že aktualizujete konfiguraci protokolu IP příslušných prostředků síťových adaptérů pomocí [Add-AzNetworkInterfaceIpConfig](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, nástroje pro vyrovnávání zatížení a všech souvisejících prostředků použít příkaz [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.7.0) .

```azurepowershell-interactive 
  Remove-AzResourceGroup -Name myresourcegroupoutbound
```

## <a name="next-steps"></a>Další kroky
V tomto článku jste vytvořili standardní nástroj pro vyrovnávání zatížení, nakonfigurovali jste pravidla provozního provozu služby pro vyrovnávání zatížení, nakonfigurovaná a sonda stavu pro virtuální počítače ve fondu back-end. Pokud se chcete dozvědět víc o Azure Load Balancer, pokračujte v kurzech pro Azure Load Balancer.

> [!div class="nextstepaction"]
> [Kurzy o službě Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
