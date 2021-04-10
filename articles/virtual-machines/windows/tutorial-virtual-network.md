---
title: Kurz – vytváření a správa virtuálních sítí Azure pro virtuální počítače s Windows
description: V tomto kurzu zjistíte, jak pomocí Azure PowerShellu vytvářet a spravovat virtuální sítě Azure pro virtuální počítače s Windows.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.collection: windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/04/2020
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 4e7f08e3c9869eae76f6c2e0127cfdab8f770d04
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102562176"
---
# <a name="tutorial-create-and-manage-azure-virtual-networks-for-windows-virtual-machines-with-azure-powershell"></a>Kurz: Vytváření a správa virtuálních sítí Azure pro virtuální počítače s Windows pomocí Azure PowerShellu

Virtuální počítače Azure používají pro interní i externí síťovou komunikaci sítě Azure. Tento kurz vás provede nasazením dvou virtuálních počítačů a konfigurací sítě Azure pro tyto virtuální počítače. V příkladech v tomto kurzu se předpokládá, že virtuální počítače jsou hostiteli webové aplikace s back-end databází, ale v tomto kurzu není nasazená aplikace. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření virtuální sítě a podsítě
> * Vytvoření veřejné IP adresy
> * Vytvoření front-endového virtuálního počítače
> * Zabezpečení provozu sítě
> * Vytvoření back-endového virtuálního počítače


## <a name="vm-networking-overview"></a>Přehled sítí virtuálních počítačů

Virtuální sítě Azure umožňují Zabezpečená síťová připojení mezi virtuálními počítači, internetem a dalšími službami Azure, jako je Azure SQL Database. Virtuální sítě se skládají z logických segmentů zvaných podsítě. Podsítě se používají k řízení toku sítě a jako hranice zabezpečení. Když nasazujete virtuální počítač, obvykle zahrnuje virtuální síťové rozhraní, které je připojené k podsíti.

Při práci na tomto kurzu vytvoříte tyto prostředky:

![Virtuální síť se dvěma podsítěmi](./media/tutorial-virtual-network/networktutorial.png)

- *myVNet* – virtuální síť, kterou virtuální počítače používají ke komunikaci mezi sebou a internetem.
- *myFrontendSubnet* – podsíť ve virtuální síti *myVNet*, kterou používají front-endové prostředky.
- *myPublicIPAddress* – veřejná IP adresa, pomocí které se můžete z internetu připojit k virtuálnímu počítači *myFrontendVM*.
- *myFrontendNic* – síťové rozhraní, které virtuální počítač *myFrontendVM* používá ke komunikaci s virtuálním počítačem *myBackendVM*.
- *myFrontendVM* – virtuální počítač, pomocí kterého můžete komunikovat mezi internetem a virtuálním počítačem *myBackendVM*.
- *myBackendNSG* – skupina zabezpečení sítě, která řídí komunikaci mezi virtuálními počítači *myFrontendVM* a *myBackendVM*.
- *myBackendSubnet* – podsíť přidružená ke skupině zabezpečení sítě *myBackendNSG*, kterou používají back-endové prostředky.
- *myBackendNic* – síťové rozhraní, které virtuální počítač *myBackendVM* používá ke komunikaci s virtuálním počítačem *myFrontendVM*.
- *myBackendVM* – virtuální počítač, který používá port 1433 ke komunikaci s virtuálním počítačem *myFrontendVM*.


## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Pokud chcete otevřít Cloud Shell, vyberte položku **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče tak, že přejdete na [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.


## <a name="create-subnet"></a>Vytvoření podsítě 

V tomto kurzu vytvoříte virtuální síť se dvěma podsítěmi. Front-endovou podsíť k hostování webové aplikace a back-endovou podsíť pro hostování databázového serveru.

Než budete moct vytvořit virtuální síť, vytvořte skupinu prostředků pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Následující příklad vytvoří skupinu prostředků *myRGNetwork* v umístění *EastUS*:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myRGNetwork -Location EastUS
```

Vytvořte konfiguraci podsítě s názvem *myFrontendSubnet* pomocí [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig):

```azurepowershell-interactive
$frontendSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name myFrontendSubnet `
  -AddressPrefix 10.0.0.0/24
```

Vytvořte také konfiguraci podsítě *myBackendSubnet*:

```azurepowershell-interactive
$backendSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24
```

## <a name="create-virtual-network"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť s názvem *myVNet* pomocí *myFrontendSubnet* a *myBackendSubnet* pomocí [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork):

```azurepowershell-interactive
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $frontendSubnet, $backendSubnet
```

Nyní jste vytvořili síť rozdělenou do dvou podsítí – jedné pro front-endové služby a druhé pro back-endové služby. V další části vytvoříme virtuální počítače a připojíme je k těmto podsítím.

## <a name="create-a-public-ip-address"></a>Vytvoření veřejné IP adresy

Veřejná IP adresa umožňuje připojení k prostředkům Azure z internetu. Metodu přidělování veřejné IP adresy je možné nakonfigurovat dynamicky nebo staticky. Ve výchozím nastavení se veřejná IP adresa přiděluje dynamicky. Dynamické IP adresy se vydávají při zrušení přidělení virtuálního počítače. Toto chování znamená, že se IP adresa změní při libovolné operaci, která zahrnuje zrušení přidělení virtuálního počítače.

Metodu alokace lze nastavit na statickou, což zajistí, že IP adresa zůstane přiřazena k virtuálnímu počítači, i když je stav navráceno. Pokud používáte statickou IP adresu, nemůžete zadat samotnou IP adresu. Místo toho je přiděleno z fondu dostupných adres.

Vytvořte veřejnou IP adresu s názvem *myPublicIPAddress* pomocí [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress):

```azurepowershell-interactive
$pip = New-AzPublicIpAddress `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -AllocationMethod Dynamic `
  -Name myPublicIPAddress
```

Pokud chcete přiřadit statickou veřejnou IP adresu, můžete změnit parametr -AllocationMethod na hodnotu `Static`.

## <a name="create-a-front-end-vm"></a>Vytvoření front-endového virtuálního počítače

Virtuální počítač ke komunikaci ve virtuální síti potřebuje virtuální síťové rozhraní. Vytvořte síťovou kartu pomocí [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface):

```azurepowershell-interactive
$frontendNic = New-AzNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontend `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

Nastavte uživatelské jméno a heslo potřebné pro účet správce na virtuálním počítači pomocí rutiny [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential). Tyto přihlašovací údaje použijete k připojení k virtuálnímu počítači v dalších krocích:

```azurepowershell-interactive
$cred = Get-Credential
```

Vytvořte virtuální počítače pomocí [New-AzVM](/powershell/module/az.compute/new-azvm).

```azurepowershell-interactive
New-AzVM `
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

Skupina zabezpečení sítě (NSG) obsahuje seznam pravidel zabezpečení, která prostředkům připojeným k virtuálním sítím Azure povolují nebo odpírají síťový provoz. Skupinu zabezpečení sítě můžete přidružit k podsítím nebo jednotlivým síťovým rozhraním. NSG je přidružená jenom k síťovému rozhraní, které se vztahuje jenom na přidružený virtuální počítač. Pokud je skupina zabezpečení sítě přidružená k podsíti, pravidla se vztahují na všechny prostředky, které jsou připojené k příslušné podsíti.

### <a name="network-security-group-rules"></a>Pravidla skupiny zabezpečení sítě

Pravidla skupiny zabezpečení sítě definují síťové porty, které provoz buď povolují, nebo zakazují. Pravidla mohou zahrnovat zdrojové a cílové rozsahy IP adres, aby se provoz řídil mezi určitými systémy nebo podsítěmi. Pravidla skupiny zabezpečení sítě také obsahují prioritu (1–4096). Pravidla se vyhodnocují v pořadí podle priority. Pravidlo s prioritou 100 se vyhodnotí před pravidlem s prioritou 200.

Všechny skupiny NSG obsahují sadu výchozích pravidel. Výchozí pravidla nelze odstranit, ale vzhledem k tomu, že mají přiřazenou nejnižší prioritu, mohou být přepsána vytvořenými pravidly.

- **Virtuální síť** – provoz směřující z virtuální sítě a do ní je povolený v příchozím i odchozím směru.
- **Internet** – odchozí provoz je povolený, ale příchozí provoz se blokuje.
- **Nástroj pro vyrovnávání zatížení** – umožňuje nástroji pro vyrovnávání zatížení Azure testovat stav virtuálních počítačů a instancí rolí. Pokud sadu s vyrovnáváním zatížení nepoužíváte, můžete toto pravidlo přepsat.

### <a name="create-network-security-groups"></a>Vytvoření skupin zabezpečení sítě

Vytvořte příchozí pravidlo s názvem *myFrontendNSGRule* , které povolí příchozí webový provoz na *myFrontendVM* pomocí [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig):

```azurepowershell-interactive
$nsgFrontendRule = New-AzNetworkSecurityRuleConfig `
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

Vytvořením skupiny zabezpečení sítě pro back-endovou podsíť můžete omezit zdroj interního provozu do virtuálního počítače *myBackendVM* pouze na virtuální počítač *myFrontendVM*. Následující příklad vytvoří pravidlo NSG *myBackendNSGRule*:

```azurepowershell-interactive
$nsgBackendRule = New-AzNetworkSecurityRuleConfig `
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

Přidejte skupinu zabezpečení sítě s názvem *myFrontendNSG* pomocí [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup):

```azurepowershell-interactive
$nsgFrontend = New-AzNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule
```

Teď přidejte skupinu zabezpečení sítě s názvem *myBackendNSG* pomocí New-AzNetworkSecurityGroup:

```azurepowershell-interactive
$nsgBackend = New-AzNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNSG `
  -SecurityRules $nsgBackendRule
```

Přidejte skupiny zabezpečení sítě do podsítí:

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Name myVNet
$frontendSubnet = $vnet.Subnets[0]
$backendSubnet = $vnet.Subnets[1]
$frontendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myFrontendSubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend
$backendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myBackendSubnet `
  -AddressPrefix $backendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgBackend
Set-AzVirtualNetwork -VirtualNetwork $vnet
```

## <a name="create-a-back-end-vm"></a>Vytvoření back-endového virtuálního počítače

Nejjednodušším způsobem, jak vytvořit back-endový virtuální počítač pro účely tohoto kurzu, je pomocí image SQL Serveru. V tomto kurzu se pouze vytvoří virtuální počítač s databázovým serverem, ale informace o přístupu k databázi kurz neobsahuje.

Vytvořte virtuální síťové rozhraní *myBackendNic*:

```azurepowershell-interactive
$backendNic = New-AzNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackend `
  -SubnetId $vnet.Subnets[1].Id
```

Nastavte uživatelské jméno a heslo potřebné pro účet správce na virtuálním počítači pomocí rutiny Get-Credential:

```azurepowershell-interactive
$cred = Get-Credential
```

Vytvořte virtuální počítač *myBackendVM*.

```azurepowershell-interactive
New-AzVM `
   -Credential $cred `
   -Name myBackend `
   -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -SubnetName MyBackendSubnet `
   -VirtualNetworkName myVNet
```

Image v tomto příkladu je SQL Server nainstalovaná, ale v tomto kurzu se nepoužívá. Je k dispozici postup, který vám ukáže, jak můžete nakonfigurovat virtuální počítač pro zpracování webového provozu a virtuálního počítače pro zpracování správy databází.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste v souvislosti s virtuálními počítači vytvořili a zabezpečené sítě Azure. 

> [!div class="checklist"]
> * Vytvoření virtuální sítě a podsítě
> * Vytvoření veřejné IP adresy
> * Vytvoření front-endového virtuálního počítače
> * Zabezpečení provozu sítě
> * Vytvoření back-endového virtuálního počítače

Další informace o ochraně disků virtuálních počítačů najdete v tématu [zálohování a zotavení po havárii pro disky](../backup-and-disaster-recovery-for-azure-iaas-disks.md).