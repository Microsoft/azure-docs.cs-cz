---
title: Vytvoření virtuálního počítače s Windows pomocí zjednodušené rutiny New-AzVM ve službě Azure Cloud Shell | Dokumentace Microsoftu
description: Rychle se naučíte, jak vytvářet virtuální počítače Windows pomocí zjednodušené rutiny New-AzVM ve službě Azure Cloud Shell.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 32862d06cfa6a9a8dd9b99459362ec53fd88cec2
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55978936"
---
# <a name="create-a-windows-virtual-machine-with-the-simplified-new-azvm-cmdlet-in-cloud-shell"></a>Vytvoření virtuálního počítače s Windows pomocí zjednodušené rutiny New-AzVM ve službě Cloud Shell 

[Rutiny New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm?view=azurermps-6.8.1) rutiny přidal zjednodušené sady parametrů pro vytvoření nového virtuálního počítače pomocí Powershellu. V tomto tématu se dozvíte, jak pomocí prostředí PowerShell ve službě Azure Cloud Shell s nejnovější verzí rutinu New-AzureVM předinstalován, chcete-li vytvořit nový virtuální počítač. Použijeme sadu zjednodušené parametr, který automaticky vytvoří všechny potřebné prostředky využitím inteligentních výchozích hodnot. 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

[!INCLUDE [cloud-shell-powershell](../../../includes/cloud-shell-powershell.md)]


## <a name="create-the-vm"></a>Vytvořte virtuální počítač.

Pomocí rutiny [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm?view=azurermps-6.8.1) můžete vytvořit virtuální počítač s inteligentním výchozím nastavením, které zahrnuje použití image Windows Serveru 2016 Datacenter z Azure Marketplace. Můžete použít rutiny New-AzVM s jenom **– název** parametr a použije tuto hodnotu pro všechny názvy prostředků. V tomto příkladu jsme jako parametr **-Name** nastavili *myVM*. 

Ověřte, že ve službě Cloud Shell je vybraný **PowerShell**, a zadejte:

```azurepowershell-interactive
New-AzVm -Name myVM
```

Zobrazí se výzva k vytvoření uživatelského jména a hesla pro virtuální počítač, které se dále v tomto tématu použijí při připojení k tomuto virtuálnímu počítači. Heslo musí mít 12 až 123 znaků a musí splňovat tři ze čtyř bezpečnostních požadavků: jedno malé písmeno, jedno velké písmeno, jedna číslice a jeden speciální znak.

Vytvoření virtuálního počítače a přidružených prostředků trvá zhruba minutu. Až budete hotovi, uvidíte všechny prostředky, které byly vytvořeny pomocí [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) rutiny.

```azurepowershell-interactive
Get-AzResource `
    -ResourceGroupName myVMResourceGroup | Format-Table Name
```

## <a name="connect-to-the-vm"></a>Připojení k virtuálnímu počítači

Po dokončení nasazení vytvořte připojení ke vzdálené ploše virtuálního počítače.

Použití [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) příkaz, který vrátí veřejnou IP adresu virtuálního počítače. Poznamenejte si tuto IP adresu.

```azurepowershell-interactive
Get-AzPublicIpAddress `
    -ResourceGroupName myVMResourceGroup | Select IpAddress
```

Na místním počítači, otevřete příkazový řádek a použít **mstsc** příkaz ke spuštění relace vzdálené plochy s novým virtuálním Počítačem. Parametr &lt;publicIPAddress&gt; nahraďte IP adresou vašeho virtuálního počítače. Po zobrazení výzvy zadejte uživatelské jméno a heslo, které jste zadali při vytváření tohoto virtuálního počítače.

```
mstsc /v:<publicIpAddress>
```
## <a name="specify-different-resource-names"></a>Zadejte názvy různých prostředků

Můžete také poskytnout více popisné názvy pro prostředky a ještě další je vytvořen automaticky. Tady je příklad kde několik prostředků mají pojmenované pro nový virtuální počítač, včetně novou skupinu prostředků.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 3389
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete použít [odebrat AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) příkazu k odebrání skupiny prostředků, virtuálního počítače, a všechny související prostředky.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myVM
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Další postup

V tomto tématu jsme nasadili jednoduchý virtuální počítač pomocí rutiny New-AzVM a potom se k němu připojili přes RDP. Další informace o virtuálních počítačích Azure najdete v kurzu pro virtuální počítače s Windows.

> [!div class="nextstepaction"]
> [Kurzy pro virtuální počítače Azure s Windows](./tutorial-manage-vm.md)
