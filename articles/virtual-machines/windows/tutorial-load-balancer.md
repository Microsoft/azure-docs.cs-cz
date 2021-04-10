---
title: Kurz – vyrovnávání zatížení virtuálních počítačů s Windows v Azure
description: V tomto kurzu se dozvíte, jak pomocí Azure PowerShellu vytvořit nástroj pro vyrovnávání zatížení pro vysoce dostupnou a zabezpečenou aplikaci na třech virtuálních počítačích s Windows.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 12/03/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: d6c872ecfbad6cb8bb01ad5a7c3df8c47aadaebe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102549103"
---
# <a name="tutorial-load-balance-windows-virtual-machines-in-azure-to-create-a-highly-available-application-with-azure-powershell"></a>Kurz: Vyrovnávání zatížení virtuálních počítačů s Windows v Azure za účelem vytvoření vysoce dostupné aplikace pomocí Azure PowerShellu
Vyrovnávání zatížení zajišťuje vyšší úroveň dostupnosti tím, že rozprostírá příchozí požadavky na více virtuálních počítačů. V tomto kurzu se seznámíte s různými komponentami nástroje pro vyrovnávání zatížení Azure, které distribuují provoz a zajišťují vysokou dostupnost. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření nástroje pro vyrovnávání zatížení Azure
> * Vytvoření sondy stavu nástroje pro vyrovnávání zatížení
> * Vytvoření pravidel provozu pro nástroj pro vyrovnávání zatížení
> * Použití rozšíření vlastních skriptů pro vytvoření základního webu služby IIS
> * Vytvoření virtuálních počítačů a jejich připojení k nástroji pro vyrovnávání zatížení
> * Zobrazení nástroje pro vyrovnávání zatížení v akci
> * Přidání virtuálních počítačů do nástroje pro vyrovnávání zatížení nebo jejich odebrání

## <a name="azure-load-balancer-overview"></a>Azure Load Balancer – přehled
Nástroj pro vyrovnávání zatížení Azure je nástroj pro vyrovnávání zatížení úrovně 4 (TCP, UDP), který poskytuje vysokou dostupnost díky distribuci příchozího provozu mezi virtuální počítače v dobrém stavu. Sonda stavu nástroje pro vyrovnávání zatížení na všech virtuálních počítačích monitoruje daný port a distribuuje provoz pouze do virtuálních počítačů, které jsou v provozu.

Nadefinujete konfiguraci front-endových IP adres, která obsahuje jednu nebo více veřejných IP adres. Tato konfigurace front-endových IP adres povoluje přístup k vašemu nástroji pro vyrovnávání zatížení a vašim aplikacím přes internet. 

Virtuální počítače se k nástroji pro vyrovnávání zatížení připojují pomocí své virtuální síťové karty. Za účelem distribuce provozu do virtuálních počítačů obsahuje fond back-endových adres IP adresy virtuálních síťových karet připojených k nástroji pro vyrovnávání zatížení.

Pro řízení toku provozu definujete pravidla nástroje pro vyrovnávání zatížení pro konkrétní porty a protokoly, které se mapují na vaše virtuální počítače.

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Pokud chcete otevřít Cloud Shell, vyberte položku **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče tak, že přejdete na [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

## <a name="create-azure-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení Azure
Tato část podrobně popisuje vytvoření a konfiguraci jednotlivých komponent nástroje pro vyrovnávání zatížení. Než budete moct vytvořit nástroj pro vyrovnávání zatížení, vytvořte skupinu prostředků pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroupLoadBalancer* v umístění *EastUS* :

```azurepowershell-interactive
New-AzResourceGroup `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS"
```

### <a name="create-a-public-ip-address"></a>Vytvoření veřejné IP adresy
Pokud chcete mít k aplikaci přístup přes internet, potřebujete pro nástroj pro vyrovnávání zatížení veřejnou IP adresu. Vytvořte veřejnou IP adresu pomocí [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Následující příklad vytvoří veřejnou IP adresu s názvem *myPublicIP* ve skupině prostředků *myResourceGroupLoadBalancer*:

```azurepowershell-interactive
$publicIP = New-AzPublicIpAddress `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS" `
  -AllocationMethod "Static" `
  -Name "myPublicIP"
```

### <a name="create-a-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení
Vytvořte fond IP adres front-endu pomocí [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). Následující příklad vytvoří front-endový fond IP adres s názvem *myFrontEndPool* a připojí adresu *myPublicIP*: 

```azurepowershell-interactive
$frontendIP = New-AzLoadBalancerFrontendIpConfig `
  -Name "myFrontEndPool" `
  -PublicIpAddress $publicIP
```

Vytvořte fond adres back-endu pomocí [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). Virtuální počítače se k tomuto back-endovému fondu připojí v dalších krocích. Následující příklad vytvoří back-end fond adres s názvem *myBackEndPool*:

```azurepowershell-interactive
$backendPool = New-AzLoadBalancerBackendAddressPoolConfig `
  -Name "myBackEndPool"
```

Teď vytvořte Nástroj pro vyrovnávání zatížení pomocí [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). Následující příklad vytvoří nástroj pro vyrovnávání zatížení s názvem *myLoadBalancer* pomocí front-endových a back-endových fondů IP adres vytvořených v předchozích krocích:

```azurepowershell-interactive
$lb = New-AzLoadBalancer `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Name "myLoadBalancer" `
  -Location "EastUS" `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool
```

### <a name="create-a-health-probe"></a>Vytvoření sondy stavu
Pokud chcete nástroji pro vyrovnávání zatížení povolit monitorování stavu vaší aplikace, použijte sondu stavu. Sonda stavu dynamicky přidává virtuální počítače do oběhu nástroje pro vyrovnávání zatížení nebo je z něj odebírá na základě jejich reakce na kontroly stavu. Ve výchozím nastavení se virtuální počítač odebere z distribuce nástroje pro vyrovnávání zatížení po dvou selháních po sobě v 15sekundových intervalech. Sondu stavu můžete vytvořit na základě protokolu nebo konkrétní stránky kontroly stavu pro vaši aplikaci. 

Následující příklad vytvoří sondu protokolu TCP. Pokud potřebujete jemněji odstupňované kontroly stavu, můžete vytvářet i vlastní sondy protokolu HTTP. Pokud použijete vlastní sondu protokolu HTTP, musíte vytvořit stránku kontroly stavu, například *healthcheck.aspx*. Aby nástroj pro vyrovnávání zatížení udržel hostitele v oběhu, musí sonda vracet odpověď **HTTP 200 OK**.

K vytvoření sondy stavu protokolu TCP použijte [příkaz Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig). Následující příklad vytvoří sondu stavu s názvem *myHealthProbe* monitorující jednotlivé virtuální počítače na portu *TCP**80*:

```azurepowershell-interactive
Add-AzLoadBalancerProbeConfig `
  -Name "myHealthProbe" `
  -LoadBalancer $lb `
  -Protocol tcp `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
```

Pokud chcete použít sondu stavu, aktualizujte Nástroj pro vyrovnávání zatížení pomocí [set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer):

```azurepowershell-interactive
Set-AzLoadBalancer -LoadBalancer $lb
```

### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení
Pravidlo nástroje pro vyrovnávání zatížení slouží k definování způsobu distribuce provozu do virtuálních počítačů. Nadefinujte konfiguraci front-endových IP adres pro příchozí provoz, back-endový fond IP adres pro příjem provozu a také požadovaný zdrojový a cílový port. Abyste zajistili, že provoz budou přijímat pouze virtuální počítače, které jsou v pořádku, nadefinujete také sondu stavu, která se má použít.

Vytvořte pravidlo nástroje pro vyrovnávání zatížení pomocí [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). Následující příklad vytvoří pravidlo nástroje pro vyrovnávání zatížení *myLoadBalancerRule* a nastaví vyrovnávání provozu na portu *TCP**80*:

```azurepowershell-interactive
$probe = Get-AzLoadBalancerProbeConfig -LoadBalancer $lb -Name "myHealthProbe"

Add-AzLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe $probe
```

Aktualizujte Nástroj pro vyrovnávání zatížení pomocí [set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer):

```azurepowershell-interactive
Set-AzLoadBalancer -LoadBalancer $lb
```

## <a name="configure-virtual-network"></a>Konfigurace virtuální sítě
Než nasadíte několik virtuálních počítačů a budete moci otestovat svůj nástroj pro vyrovnávání zatížení, vytvořte podpůrné prostředky virtuální sítě. Další informace o virtuálních sítích najdete v kurzu [Správa virtuálních sítí Azure](tutorial-virtual-network.md).

### <a name="create-network-resources"></a>Vytvoření síťových prostředků
Vytvořte virtuální síť pomocí [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Následující příklad vytvoří virtuální síť s názvem *myVnet* s podsítí *mySubnet*:

```azurepowershell-interactive
# Create subnet config
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 192.168.1.0/24

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS" `
  -Name "myVnet" `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```

Virtuální síťové karty se vytvářejí pomocí [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). Následující příklad vytvoří tři virtuální síťové karty. (Jednu virtuální síťovou kartu pro každý virtuální počítač, který pro svou aplikaci vytvoříte v následujících krocích). Kdykoli můžete vytvořit další virtuální síťové karty a virtuální počítače a přidat je do nástroje pro vyrovnávání zatížení:

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
   New-AzNetworkInterface `
     -ResourceGroupName "myResourceGroupLoadBalancer" `
     -Name myVM$i `
     -Location "EastUS" `
     -Subnet $vnet.Subnets[0] `
     -LoadBalancerBackendAddressPool $lb.BackendAddressPools[0]
}
```


## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů
Pokud chcete zlepšit vysokou dostupnost aplikace, umístěte své virtuální počítače do skupiny dostupnosti.

Vytvořte skupinu dostupnosti pomocí [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset). Následující příklad vytvoří skupinu dostupnosti *myAvailabilitySet*:

```azurepowershell-interactive
$availabilitySet = New-AzAvailabilitySet `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Name "myAvailabilitySet" `
  -Location "EastUS" `
  -Sku aligned `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2
```

Pomocí rutiny [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) nastavte uživatelské jméno a heslo správce virtuálních počítačů:

```azurepowershell-interactive
$cred = Get-Credential
```

Nyní můžete vytvořit virtuální počítače pomocí [New-AzVM](/powershell/module/az.compute/new-azvm). Následující příklad vytvoří tři virtuální počítače a požadované komponenty virtuální sítě (pokud ještě neexistují):

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
    New-AzVm `
        -ResourceGroupName "myResourceGroupLoadBalancer" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -OpenPorts 80 `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred `
        -AsJob
}
```

Parametr `-AsJob` vytvoří virtuální počítač jako úlohu na pozadí, takže budete mít k dispozici příkazový řádek PowerShellu. Podrobnosti úloh na pozadí můžete zobrazit pomocí rutiny `Job`. Vytvoření a konfigurace všech tří virtuálních počítačů bude trvat několik minut.


### <a name="install-iis-with-custom-script-extension"></a>Instalace služby IIS pomocí rozšíření vlastních skriptů
V předchozím kurzu týkajícím se [postupu přizpůsobení virtuálního počítače s Windows](tutorial-automate-vm-deployment.md) jste se dozvěděli, jak automatizovat přizpůsobení virtuálního počítače s rozšířením vlastních skriptů pro Windows. Stejný přístup můžete použít pro instalaci a konfiguraci služby IIS na vašich virtuálních počítačích.

K instalaci rozšíření vlastních skriptů použijte [set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) . Rozšíření spustí `powershell Add-WindowsFeature Web-Server` za účelem instalace webového serveru služby IIS a potom aktualizuje stránku *Default.htm*, aby zobrazovala název hostitele virtuálního počítače:

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
   Set-AzVMExtension `
     -ResourceGroupName "myResourceGroupLoadBalancer" `
     -ExtensionName "IIS" `
     -VMName myVM$i `
     -Publisher Microsoft.Compute `
     -ExtensionType CustomScriptExtension `
     -TypeHandlerVersion 1.8 `
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
     -Location EastUS
}
```

## <a name="test-load-balancer"></a>Test nástroje pro vyrovnávání zatížení
Získejte veřejnou IP adresu vašeho nástroje pro vyrovnávání zatížení pomocí [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress). Následující příklad získá dříve vytvořenou IP adresu *myPublicIP*:

```azurepowershell-interactive
Get-AzPublicIPAddress `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Name "myPublicIP" | select IpAddress
```

Veřejnou IP adresu pak můžete zadat do webového prohlížeče. Zobrazí se web, včetně názvu hostitele virtuálního počítače, do kterého nástroj pro vyrovnávání zatížení distribuoval provoz, jako v následujícím příkladu:

![Spuštění webu služby IIS](./media/tutorial-load-balancer/running-iis-website.png)

Pokud chcete zobrazit distribuci provozu nástrojem pro vyrovnávání zatížení mezi všechny virtuální počítače, na kterých je vaše aplikace spuštěná, můžete vynutit aktualizaci webového prohlížeče.


## <a name="add-and-remove-vms"></a>Přidání a odebrání virtuálních počítačů
Na virtuálních počítačích, na kterých je vaše aplikace spuštěná, možná budete potřebovat provést údržbu, například nainstalovat aktualizace operačního systému. Abyste si poradili se zvýšením provozu do vaší aplikace, možná budete muset přidat další virtuální počítače. V této části se dozvíte, jak z nástroje pro vyrovnávání zatížení odebrat virtuální počítač nebo ho do něj přidat.

### <a name="remove-a-vm-from-the-load-balancer"></a>Odebrání virtuálního počítače z nástroje pro vyrovnávání zatížení
Pomocí [Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface)Získejte síťovou kartu a pak nastavte vlastnost *LoadBalancerBackendAddressPools* virtuální síťové karty na *$null*. Nakonec virtuální síťovou kartu aktualizujte.

```azurepowershell-interactive
$nic = Get-AzNetworkInterface `
    -ResourceGroupName "myResourceGroupLoadBalancer" `
    -Name "myVM2"
$nic.Ipconfigurations[0].LoadBalancerBackendAddressPools=$null
Set-AzNetworkInterface -NetworkInterface $nic
```

Pokud chcete zobrazit distribuci provozu nástrojem pro vyrovnávání zatížení mezi zbývající dva virtuální počítače, na kterých je vaše aplikace spuštěná, můžete vynutit aktualizaci webového prohlížeče. Teď můžete na virtuálním počítači provést údržbu, například nainstalovat aktualizace operačního systému nebo provést restartování virtuálního počítače.

### <a name="add-a-vm-to-the-load-balancer"></a>Přidání virtuálního počítače do nástroje pro vyrovnávání zatížení
Po provedení údržby virtuálních počítačů nebo v případě, že potřebujete rozšířit kapacitu, nastavte vlastnost *LoadBalancerBackendAddressPools* virtuální síťové karty na *BackendAddressPool* z [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer):

Získejte nástroj pro vyrovnávání zatížení:

```azurepowershell-interactive
$lb = Get-AzLoadBalancer `
    -ResourceGroupName myResourceGroupLoadBalancer `
    -Name myLoadBalancer 
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$lb.BackendAddressPools[0]
Set-AzNetworkInterface -NetworkInterface $nic
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili nástroj pro vyrovnávání zatížení a připojili jste k němu virtuální počítače. Naučili jste se:

> [!div class="checklist"]
> * Vytvoření nástroje pro vyrovnávání zatížení Azure
> * Vytvoření sondy stavu nástroje pro vyrovnávání zatížení
> * Vytvoření pravidel provozu pro nástroj pro vyrovnávání zatížení
> * Použití rozšíření vlastních skriptů pro vytvoření základního webu služby IIS
> * Vytvoření virtuálních počítačů a jejich připojení k nástroji pro vyrovnávání zatížení
> * Zobrazení nástroje pro vyrovnávání zatížení v akci
> * Přidání virtuálních počítačů do nástroje pro vyrovnávání zatížení nebo jejich odebrání

V dalším kurzu se dozvíte, jak spravovat síť virtuálních počítačů.

> [!div class="nextstepaction"]
> [Správa virtuálních počítačů a virtuálních sítí](./tutorial-virtual-network.md)
