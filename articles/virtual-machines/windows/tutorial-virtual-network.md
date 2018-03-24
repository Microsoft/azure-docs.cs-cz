---
title: Virtuální sítě Azure a virtuální počítače s Windows | Microsoft Docs
description: Kurz – Správa virtuálních sítí Azure a virtuální počítače s Windows pomocí Azure Powershellu
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: feaef679a3090491b64c69ac69bf22153c281d31
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="manage-azure-virtual-networks-and-windows-virtual-machines-with-azure-powershell"></a>Správa virtuálních sítí Azure a virtuální počítače s Windows pomocí Azure Powershellu

Virtuální počítače Azure používají pro interní i externí síťovou komunikaci sítě Azure. Tento kurz vás provede nasazením dvou virtuálních počítačů a konfigurací sítě Azure pro tyto virtuální počítače. Příklady v tomto kurzu předpokládají, že virtuální počítače hostují webovou aplikaci s back-endovou databází, ale aplikace se v tomto kurzu nenasazuje. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření virtuální sítě a podsítě
> * Vytvoření veřejné IP adresy
> * Vytvoření front-endového virtuálního počítače
> * Zabezpečení provozu sítě
> * Vytvoření back-endového virtuálního počítače



Tento kurz vyžaduje modul AzureRM.Compute verze 4.3.1 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM.Compute`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="vm-networking-overview"></a>Přehled sítí virtuálních počítačů

Virtuální sítě Azure umožňují zabezpečená síťová připojení mezi virtuálními počítači, internetem a dalšími službami Azure, jako je Databáze SQL Azure. Virtuální sítě se skládají z logických segmentů zvaných podsítě. Podsítě se používají k řízení toku sítě a jako hranice zabezpečení. Když nasazujete virtuální počítač, obvykle zahrnuje virtuální síťové rozhraní, které je připojené k podsíti.

Při práci na tomto kurzu vytvoříte tyto prostředky:

![Virtuální síť se dvěma podsítěmi](./media/tutorial-virtual-network/networktutorial.png)

- *myVNet* – virtuální síť, kterou virtuální počítače používají ke komunikaci mezi sebou a internetem.
- *myFrontendSubnet* – podsíť ve virtuální síti *myVNet*, kterou používají front-endové prostředky.
- *myPublicIPAddress* – veřejná IP adresa, pomocí které se můžete z internetu připojit k virtuálnímu počítači *myFrontendVM*.
- *myFrontentNic* – síťové rozhraní, které virtuální počítač *myFrontendVM* používá ke komunikaci s virtuálním počítačem *myBackendVM*.
- *myFrontendVM* – virtuální počítač, pomocí kterého můžete komunikovat mezi internetem a virtuálním počítačem *myBackendVM*.
- *myBackendNSG* – skupina zabezpečení sítě, která řídí komunikaci mezi virtuálními počítači *myFrontendVM* a *myBackendVM*.
- *myBackendSubnet* – podsíť přidružená ke skupině zabezpečení sítě *myBackendNSG*, kterou používají back-endové prostředky.
- *myBackendNic* – síťové rozhraní, které virtuální počítač *myBackendVM* používá ke komunikaci s virtuálním počítačem *myFrontendVM*.
- *myBackendVM* -virtuální počítač, který používá port 1433 ke komunikaci s *myFrontendVM*.


## <a name="create-a-virtual-network-and-subnet"></a>Vytvoření virtuální sítě a podsítě

V tomto kurzu vytvoříte virtuální síť se dvěma podsítěmi. Front-endovou podsíť k hostování webové aplikace a back-endovou podsíť pro hostování databázového serveru.

Než bude možné vytvořit virtuální síť, vytvořte skupinu prostředků pomocí [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Následující příklad vytvoří skupinu prostředků s názvem *myRGNetwork* v *EastUS* umístění:

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myRGNetwork -Location EastUS
```

### <a name="create-subnet-configurations"></a>Vytvoření konfigurací podsítě

Vytvoření konfigurace podsíť s názvem *myFrontendSubnet* pomocí [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig):

```azurepowershell-interactive
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myFrontendSubnet `
  -AddressPrefix 10.0.0.0/24
```

A vytvořit konfiguraci podsíť s názvem *myBackendSubnet*:

```azurepowershell-interactive
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24
```

### <a name="create-virtual-network"></a>Vytvoření virtuální sítě

Vytvoření virtuální sítě s názvem *myVNet* pomocí *myFrontendSubnet* a *myBackendSubnet* pomocí [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork):

```azurepowershell-interactive
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $frontendSubnet, $backendSubnet
```

Nyní jste vytvořili síť rozdělenou do dvou podsítí – jedné pro front-endové služby a druhé pro back-endové služby. V další části vytvoříme virtuální počítače a připojíme je k těmto podsítím.

## <a name="create-a-public-ip-address"></a>Vytvoření veřejné IP adresy

Veřejná IP adresa umožňuje připojení k prostředkům Azure z internetu. Metodu přidělování veřejné IP adresy je možné nakonfigurovat dynamicky nebo staticky. Ve výchozím nastavení se veřejná IP adresa přiděluje dynamicky. Dynamické IP adresy se vydávají při zrušení přidělení virtuálního počítače. Toto chování znamená, že se IP adresa změní při libovolné operaci, která zahrnuje zrušení přidělení virtuálního počítače.

Metodu přidělování je možné nastavit staticky, což zajistí, že IP adresa zůstane virtuálnímu počítači přidělená i ve stavu zrušeného přidělení. Když používáte staticky přidělenou IP adresu, není ji možné určit. Místo toho se přiděluje z fondu dostupných adres.

Vytvoření veřejné IP adresy s názvem *myPublicIPAddress* pomocí [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress):

```azurepowershell-interactive
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -AllocationMethod Dynamic `
  -Name myPublicIPAddress
```

Můžete změnit pomocí parametru - AllocationMethod `Static` přiřadit statickou veřejnou IP adresu.

## <a name="create-a-front-end-vm"></a>Vytvoření front-endového virtuálního počítače

Pro virtuální počítač pro komunikaci ve virtuální síti musí být virtuální síťové rozhraní (NIC). Vytvoření síťový adaptér pomocí [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface):

```azurepowershell-interactive
$frontendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontend `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

Nastavte uživatelské jméno a heslo, které potřebuje pro účet správce na virtuální počítač pomocí [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential). Použít tyto přihlašovací údaje pro připojení k virtuálnímu počítači v dalších krocích:

```azurepowershell-interactive
$cred = Get-Credential
```

Vytvořit virtuální počítače pomocí [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```azurepowershell-interactive
New-AzureRmVM `
   -Credential $cred `
   -Name myFrontend `
   -PublicIpAddressName myPublicIPAddress `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -Size Standard_D1 `
   -SubnetName myFrontendSubnet `
   -VirtualNetworkName myVNet
```

## <a name="secure-network-traffic"></a>Zabezpečení provozu sítě

Skupina zabezpečení sítě (NSG) obsahuje seznam pravidel zabezpečení, která prostředkům připojeným k virtuálním sítím Azure povolují nebo odpírají síťový provoz. Skupinu zabezpečení sítě můžete přidružit k podsítím nebo jednotlivým síťovým rozhraním. Pokud skupinu zabezpečení sítě přidružíte k síťovému rozhraní, bude se vztahovat pouze k přidruženému virtuálnímu počítači. Pokud je skupina zabezpečení sítě přidružená k podsíti, pravidla se vztahují na všechny prostředky, které jsou připojené k příslušné podsíti.

### <a name="network-security-group-rules"></a>Pravidla skupiny zabezpečení sítě

Pravidla skupiny zabezpečení sítě definují síťové porty, které provoz buď povolují, nebo zakazují. Pravidla mohou zahrnovat zdrojové a cílové rozsahy IP adres, aby se provoz řídil mezi určitými systémy nebo podsítěmi. Pravidla skupiny zabezpečení sítě také obsahují prioritu (1–4096). Pravidla se vyhodnocují v pořadí podle priority. Pravidlo s prioritou 100 se vyhodnotí před pravidlem s prioritou 200.

Všechny skupiny NSG obsahují sadu výchozích pravidel. Výchozí pravidla se nedají odstranit, ale protože je jim přiřazená nejnižší priorita, dají se přepsat pravidly, která vytvoříte.

- **Virtuální síť** – provoz směřující z virtuální sítě a do ní je povolený v příchozím i odchozím směru.
- **Internet** – odchozí provoz je povolený, ale příchozí provoz se blokuje.
- **Nástroj pro vyrovnávání zatížení** – umožňuje nástroji pro vyrovnávání zatížení Azure testovat stav virtuálních počítačů a instancí rolí. Pokud sadu s vyrovnáváním zatížení nepoužíváte, můžete toto pravidlo přepsat.

### <a name="create-network-security-groups"></a>Vytvoření skupin zabezpečení sítě

Vytvoření příchozího pravidla s názvem *myFrontendNSGRule* na Povolit příchozí webové přenosy *myFrontendVM* pomocí [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig):

```azurepowershell-interactive
$nsgFrontendRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myFrontendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow
```

Můžete omezit interní provoz *myBackendVM* z jenom *myFrontendVM* tak, že vytvoříte skupinu NSG pro podsíť back-end. Následující příklad vytvoří pravidlo NSG s názvem *myBackendNSGRule*:

```azurepowershell-interactive
$nsgBackendRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myBackendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix 10.0.0.0/24 `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 1433 `
  -Access Allow
```

Přidat skupinu zabezpečení sítě s názvem *myFrontendNSG* pomocí [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup):

```azurepowershell-interactive
$nsgFrontend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule
```

Nyní přidejte skupinu zabezpečení sítě s názvem *myBackendNSG* pomocí New-AzureRmNetworkSecurityGroup:

```azurepowershell-interactive
$nsgBackend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNSG `
  -SecurityRules $nsgBackendRule
```

Přidání skupin zabezpečení sítě k podsítím provozu:

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Name myVNet
$frontendSubnet = $vnet.Subnets[0]
$backendSubnet = $vnet.Subnets[1]
$frontendSubnetConfig = Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myFrontendSubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend
$backendSubnetConfig = Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myBackendSubnet `
  -AddressPrefix $backendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgBackend
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

## <a name="create-a-back-end-vm"></a>Vytvoření back-endového virtuálního počítače

Nejjednodušší způsob, jak vytvořit virtuální počítač back-end pro účely tohoto kurzu je pomocí bitové kopie systému SQL Server. V tomto kurzu pouze vytvoří virtuální počítač s databázovým serverem, ale neposkytuje informace o přístupu k databázi.

Vytvoření *myBackendNic*:

```azurepowershell-interactive
$backendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackend `
  -SubnetId $vnet.Subnets[1].Id
```

Nastavte uživatelské jméno a heslo pro účet správce ve virtuálním počítači s Get-Credential potřebné:

```azurepowershell-interactive
$cred = Get-Credential
```

Vytvoření *myBackendVM*.

```azurepowershell-interactive
New-AzureRmVM `
   -Credential $cred `
   -Name myBackend `
   -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -SubnetName myFrontendSubnet `
   -VirtualNetworkName myVNet
```

Obrázek, který je použit nainstalován server SQL, ale není použit v tomto kurzu. Je zahrnutá ukázat vám, jak můžete nakonfigurovat virtuální počítač pro zpracování webových přenosů a virtuální počítač pro správu databáze.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste v souvislosti s virtuálními počítači vytvořili a zabezpečené sítě Azure. 

> [!div class="checklist"]
> * Vytvoření virtuální sítě a podsítě
> * Vytvoření veřejné IP adresy
> * Vytvoření front-endového virtuálního počítače
> * Zabezpečení provozu sítě
> * Vytvoření back-endového virtuálního počítače

Přechodu na v dalším kurzu se dozvíte o monitorování zabezpečení dat na virtuální počítače pomocí zálohování Azure.

> [!div class="nextstepaction"]
> [Zálohovat virtuální počítače s Windows v Azure](./tutorial-backup-vms.md)
