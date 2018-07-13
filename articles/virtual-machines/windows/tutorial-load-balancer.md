---
title: Kurz – vyrovnávání zatížení virtuálních počítačů s Windows v Azure | Microsoft Docs
description: V tomto kurzu se dozvíte, jak pomocí Azure PowerShellu vytvořit nástroj pro vyrovnávání zatížení pro vysoce dostupnou a zabezpečenou aplikaci na třech virtuálních počítačích s Windows.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6d4dd900888e229c86685549c84b724044249429
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37932564"
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
> * Přidání virtuálních počítačů do nástroje pro vyrovnávání zatížení a jejich odebrání

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte v tomto kurzu použít modul Azure PowerShell verze 5.7.0 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzureRmAccount` pro vytvoření připojení k Azure.


## <a name="azure-load-balancer-overview"></a>Azure Load Balancer – přehled
Nástroj pro vyrovnávání zatížení Azure je nástroj pro vyrovnávání zatížení úrovně 4 (TCP, UDP), který poskytuje vysokou dostupnost díky distribuci příchozího provozu mezi virtuální počítače v dobrém stavu. Sonda stavu nástroje pro vyrovnávání zatížení na všech virtuálních počítačích monitoruje daný port a distribuuje provoz pouze do virtuálních počítačů, které jsou v provozu.

Nadefinujete konfiguraci front-endových IP adres, která obsahuje jednu nebo více veřejných IP adres. Tato konfigurace front-endových IP adres povoluje přístup k vašemu nástroji pro vyrovnávání zatížení a vašim aplikacím přes internet. 

Virtuální počítače se k nástroji pro vyrovnávání zatížení připojují pomocí své virtuální síťové karty. Za účelem distribuce provozu do virtuálních počítačů obsahuje fond back-endových adres IP adresy virtuálních síťových karet připojených k nástroji pro vyrovnávání zatížení.

Pro řízení toku provozu definujete pravidla nástroje pro vyrovnávání zatížení pro konkrétní porty a protokoly, které se mapují na vaše virtuální počítače.


## <a name="create-azure-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení Azure
Tato část podrobně popisuje vytvoření a konfiguraci jednotlivých komponent nástroje pro vyrovnávání zatížení. Než vytvoříte nástroj pro vyrovnávání zatížení, vytvořte skupinu prostředků pomocí rutiny [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroupLoadBalancer* v umístění *EastUS*:

```azurepowershell-interactive
New-AzureRmResourceGroup `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS"
```

### <a name="create-a-public-ip-address"></a>Vytvoření veřejné IP adresy
Pokud chcete mít k aplikaci přístup přes internet, potřebujete pro nástroj pro vyrovnávání zatížení veřejnou IP adresu. Vytvořte veřejnou IP adresu pomocí rutiny [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). Následující příklad vytvoří veřejnou IP adresu s názvem *myPublicIP* ve skupině prostředků *myResourceGroupLoadBalancer*:

```azurepowershell-interactive
$publicIP = New-AzureRmPublicIpAddress `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS" `
  -AllocationMethod "Static" `
  -Name "myPublicIP"
```

### <a name="create-a-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení
Vytvořte front-endový fond IP adres pomocí rutiny [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig). Následující příklad vytvoří front-endový fond IP adres s názvem *myFrontEndPool* a připojí adresu *myPublicIP*: 

```azurepowershell-interactive
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig `
  -Name "myFrontEndPool" `
  -PublicIpAddress $publicIP
```

Vytvořte back-endový fond adres pomocí rutiny [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig). Virtuální počítače se k tomuto back-endovému fondu připojí v dalších krocích. Následující příklad vytvoří back-endový fond adres s názvem *myBackEndPool*:

```azurepowershell-interactive
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"
```

Nyní vytvořte nástroj pro vyrovnávání zatížení pomocí rutiny [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer). Následující příklad vytvoří nástroj pro vyrovnávání zatížení s názvem *myLoadBalancer* pomocí front-endových a back-endových fondů IP adres vytvořených v předchozích krocích:

```azurepowershell-interactive
$lb = New-AzureRmLoadBalancer `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Name "myLoadBalancer" `
  -Location "EastUS" `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool
```

### <a name="create-a-health-probe"></a>Vytvoření sondy stavu
Pokud chcete nástroji pro vyrovnávání zatížení povolit monitorování stavu vaší aplikace, použijte sondu stavu. Sonda stavu dynamicky přidává virtuální počítače do oběhu nástroje pro vyrovnávání zatížení nebo je z něj odebírá na základě jejich reakce na kontroly stavu. Ve výchozím nastavení se virtuální počítač odebere z distribuce nástroje pro vyrovnávání zatížení po dvou selháních po sobě v 15sekundových intervalech. Sondu stavu můžete vytvořit na základě protokolu nebo konkrétní stránky kontroly stavu pro vaši aplikaci. 

Následující příklad vytvoří sondu protokolu TCP. Pokud potřebujete jemněji odstupňované kontroly stavu, můžete vytvářet i vlastní sondy protokolu HTTP. Pokud použijete vlastní sondu protokolu HTTP, musíte vytvořit stránku kontroly stavu, například *healthcheck.aspx*. Aby nástroj pro vyrovnávání zatížení udržel hostitele v oběhu, musí sonda vracet odpověď **HTTP 200 OK**.

K vytvoření sondy stavu protokolu TCP použijte rutinu [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/add-azurermloadbalancerprobeconfig). Následující příklad vytvoří sondu stavu s názvem *myHealthProbe* monitorující jednotlivé virtuální počítače na portu *TCP* *80*:

```azurepowershell-interactive
Add-AzureRmLoadBalancerProbeConfig `
  -Name "myHealthProbe" `
  -LoadBalancer $lb `
  -Protocol tcp `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
```

Abyste mohli sondu stavu použít, aktualizujte nástroj pro vyrovnávání zatížení pomocí rutiny [Set-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer):

```azurepowershell-interactive
Set-AzureRmLoadBalancer -LoadBalancer $lb
```

### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení
Pravidlo nástroje pro vyrovnávání zatížení slouží k definování způsobu distribuce provozu do virtuálních počítačů. Nadefinujte konfiguraci front-endových IP adres pro příchozí provoz, back-endový fond IP adres pro příjem provozu a také požadovaný zdrojový a cílový port. Abyste zajistili, že provoz budou přijímat pouze virtuální počítače, které jsou v pořádku, nadefinujte také sondu stavu, která se má použít.

Vytvořte pravidlo nástroje pro vyrovnávání zatížení pomocí rutiny [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/add-azurermloadbalancerruleconfig). Následující příklad vytvoří pravidlo nástroje pro vyrovnávání zatížení *myLoadBalancerRule* a nastaví vyrovnávání provozu na portu *TCP* *80*:

```azurepowershell-interactive
$probe = Get-AzureRmLoadBalancerProbeConfig -LoadBalancer $lb -Name "myHealthProbe"

Add-AzureRmLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe $probe
```

Aktualizujte nástroj pro vyrovnávání zatížení pomocí rutiny [Set-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer):

```azurepowershell-interactive
Set-AzureRmLoadBalancer -LoadBalancer $lb
```

## <a name="configure-virtual-network"></a>Konfigurace virtuální sítě
Než nasadíte několik virtuálních počítačů a budete moci otestovat svůj nástroj pro vyrovnávání zatížení, vytvořte podpůrné prostředky virtuální sítě. Další informace o virtuálních sítích najdete v kurzu [Správa virtuálních sítí Azure](tutorial-virtual-network.md).

### <a name="create-network-resources"></a>Vytvoření síťových prostředků
Vytvořte virtuální síť pomocí rutiny [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). Následující příklad vytvoří virtuální síť s názvem *myVnet* s podsítí *mySubnet*:

```azurepowershell-interactive
# Create subnet config
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 192.168.1.0/24

# Create the virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS" `
  -Name "myVnet" `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```

Virtuální síťové karty se vytvoří pomocí rutiny [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface). Následující příklad vytvoří tři virtuální síťové karty. (Jednu virtuální síťovou kartu pro každý virtuální počítač, který pro svou aplikaci vytvoříte v následujících krocích). Kdykoli můžete vytvořit další virtuální síťové karty a virtuální počítače a přidat je do nástroje pro vyrovnávání zatížení:

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
   New-AzureRmNetworkInterface `
     -ResourceGroupName "myResourceGroupLoadBalancer" `
     -Name myVM$i `
     -Location "EastUS" `
     -Subnet $vnet.Subnets[0] `
     -LoadBalancerBackendAddressPool $lb.BackendAddressPools[0]
}
```


## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů
Pokud chcete zlepšit vysokou dostupnost aplikace, umístěte své virtuální počítače do skupiny dostupnosti.

Skupinu dostupnosti můžete vytvořit pomocí rutiny [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset). Následující příklad vytvoří skupinu dostupnosti s názvem *myAvailabilitySet*:

```azurepowershell-interactive
$availabilitySet = New-AzureRmAvailabilitySet `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Name "myAvailabilitySet" `
  -Location "EastUS" `
  -Sku aligned `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2
```

Pomocí rutiny [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) nastavte uživatelské jméno a heslo správce virtuálních počítačů:

```azurepowershell-interactive
$cred = Get-Credential
```

Nyní můžete vytvořit virtuální počítače pomocí rutiny [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Následující příklad vytvoří tři virtuální počítače a požadované komponenty virtuální sítě (pokud ještě neexistují):

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
    New-AzureRmVm `
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

Pro instalaci rozšíření vlastních skriptů použijte rutinu [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). Rozšíření spustí `powershell Add-WindowsFeature Web-Server` za účelem instalace webového serveru služby IIS a potom aktualizuje stránku *Default.htm*, aby zobrazovala název hostitele virtuálního počítače:

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
   Set-AzureRmVMExtension `
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
Získejte veřejnou IP adresu vašeho nástroje pro vyrovnávání zatížení pomocí rutiny [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). Následující příklad získá dříve vytvořenou IP adresu pro *myPublicIP*:

```azurepowershell-interactive
Get-AzureRmPublicIPAddress `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Name "myPublicIP" | select IpAddress
```

Veřejnou IP adresu pak můžete zadat do webového prohlížeče. Zobrazí se web, včetně názvu hostitele virtuálního počítače, do kterého nástroj pro vyrovnávání zatížení distribuoval provoz, jako v následujícím příkladu:

![Spuštění webu služby IIS](./media/tutorial-load-balancer/running-iis-website.png)

Pokud chcete zobrazit distribuci provozu nástrojem pro vyrovnávání zatížení mezi všechny tři virtuální počítače, na kterých je vaše aplikace spuštěná, můžete vynutit aktualizaci webového prohlížeče.


## <a name="add-and-remove-vms"></a>Přidání a odebrání virtuálních počítačů
Na virtuálních počítačích, na kterých je vaše aplikace spuštěná, možná budete potřebovat provést údržbu, například nainstalovat aktualizace operačního systému. Abyste si poradili se zvýšením provozu do vaší aplikace, možná budete muset přidat další virtuální počítače. V této části se dozvíte, jak z nástroje pro vyrovnávání zatížení odebrat virtuální počítač nebo ho do něj přidat.

### <a name="remove-a-vm-from-the-load-balancer"></a>Odebrání virtuálního počítače z nástroje pro vyrovnávání zatížení
Získejte síťovou kartu pomocí rutiny [Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface), potom nastavte vlastnost *LoadBalancerBackendAddressPools* virtuální síťové karty na hodnotu *$null*. Nakonec virtuální síťovou kartu aktualizujte.

```azurepowershell-interactive
$nic = Get-AzureRmNetworkInterface `
    -ResourceGroupName "myResourceGroupLoadBalancer" `
    -Name "myVM2"
$nic.Ipconfigurations[0].LoadBalancerBackendAddressPools=$null
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

Pokud chcete zobrazit distribuci provozu nástrojem pro vyrovnávání zatížení mezi zbývající dva virtuální počítače, na kterých je vaše aplikace spuštěná, můžete vynutit aktualizaci webového prohlížeče. Teď můžete na virtuálním počítači provést údržbu, například nainstalovat aktualizace operačního systému nebo provést restartování virtuálního počítače.

### <a name="add-a-vm-to-the-load-balancer"></a>Přidání virtuálního počítače do nástroje pro vyrovnávání zatížení
Po provedení údržby virtuálních počítačů nebo pokud je potřeba rozšířit kapacitu, nastavte vlastnost *LoadBalancerBackendAddressPools* virtuální síťové karty na *BackendAddressPool* pomocí rutiny [Get-AzureRMLoadBalancer](/powershell/module/azurerm.network/get-azurermloadbalancer):

Získejte nástroj pro vyrovnávání zatížení:

```azurepowershell-interactive
$lb = Get-AzureRMLoadBalancer `
    -ResourceGroupName myResourceGroupLoadBalancer `
    -Name myLoadBalancer 
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$lb.BackendAddressPools[0]
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili nástroj pro vyrovnávání zatížení a připojili jste k němu virtuální počítače. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Vytvoření nástroje pro vyrovnávání zatížení Azure
> * Vytvoření sondy stavu nástroje pro vyrovnávání zatížení
> * Vytvoření pravidel provozu pro nástroj pro vyrovnávání zatížení
> * Použití rozšíření vlastních skriptů pro vytvoření základního webu služby IIS
> * Vytvoření virtuálních počítačů a jejich připojení k nástroji pro vyrovnávání zatížení
> * Zobrazení nástroje pro vyrovnávání zatížení v akci
> * Přidání virtuálních počítačů do nástroje pro vyrovnávání zatížení a jejich odebrání

V dalším kurzu se dozvíte, jak spravovat síť virtuálních počítačů.

> [!div class="nextstepaction"]
> [Správa virtuálních počítačů a virtuálních sítí](./tutorial-virtual-network.md)
