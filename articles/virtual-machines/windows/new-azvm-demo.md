---
title: Vytvoření virtuálního počítače s Windows pomocí zjednodušené rutiny New-AzureRMVM ve službě Azure Cloud Shell | Dokumentace Microsoftu
description: Rychle se naučíte, jak vytvářet virtuální počítače Windows pomocí rutiny New-AzureRMVM zjednodušené ve službě Azure Cloud Shell.
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
ms.openlocfilehash: ede8fab67c04eb7ce8d26280de2d1563b6cc8ad2
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38678736"
---
# <a name="create-a-windows-virtual-machine-with-the-simplified-new-azurermvm-cmdlet-in-cloud-shell"></a>Vytvoření virtuálního počítače s Windows pomocí rutiny New-AzureRMVM zjednodušené ve službě Cloud Shell 

[New-AzureRMVM](/powershell/module/azurerm.resources/new-azurermvm) rutiny přidal zjednodušené sady parametrů pro vytvoření nového virtuálního počítače pomocí Powershellu. V tomto tématu se dozvíte, jak pomocí prostředí PowerShell ve službě Azure Cloud Shell s nejnovější verzí rutinu New-AzureVM předinstalován, chcete-li vytvořit nový virtuální počítač. Použijeme sadu zjednodušené parametr, který automaticky vytvoří všechny potřebné prostředky využitím inteligentních výchozích hodnot. 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.


[!INCLUDE [cloud-shell-powershell](../../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít modul Azure PowerShell verze 5.1.1 nebo novější. Verzi zjistíte spuštěním příkazu ` Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzureRmAccount` pro vytvoření připojení k Azure.

## <a name="create-the-vm"></a>Vytvořte virtuální počítač.

Můžete použít [New-AzureRMVM](/powershell/module/azurerm.resources/new-azurermvm) rutina pro vytvoření virtuálního počítače s inteligentním výchozím nastavením, které zahrnuje použití image Windows serveru 2016 Datacenter z Azure Marketplace. Můžete použít New-AzureRMVM s jenom **– název** parametr a použije tuto hodnotu pro všechny názvy prostředků. V tomto příkladu jsme jako parametr **-Name** nastavili *myVM*. 

Ověřte, že ve službě Cloud Shell je vybraný **PowerShell**, a zadejte:

```azurepowershell-interactive
New-AzureRMVm -Name myVM
```

Zobrazí se výzva k vytvoření uživatelského jména a hesla pro virtuální počítač, které se dále v tomto tématu použijí při připojení k tomuto virtuálnímu počítači. Heslo musí mít 12 až 123 znaků a musí splňovat tři ze čtyř bezpečnostních požadavků: jedno malé písmeno, jedno velké písmeno, jedna číslice a jeden speciální znak.

Vytvoření virtuálního počítače a přidružených prostředků trvá zhruba minutu. Až budete hotovi, uvidíte všechny prostředky, které byly vytvořeny pomocí [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) rutiny.

```azurepowershell-interactive
Get-AzureRmResource `
    -ResourceGroupName myVMResourceGroup | Format-Table Name
```

## <a name="connect-to-the-vm"></a>Připojení k virtuálnímu počítači

Po dokončení nasazení vytvořte připojení ke vzdálené ploše virtuálního počítače.

Použijte příkaz [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress), který vrátí veřejnou IP adresu virtuálního počítače. Poznamenejte si tuto IP adresu.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
    -ResourceGroupName myVMResourceGroup | Select IpAddress
```

Na místním počítači, otevřete příkazový řádek a použít **mstsc** příkaz ke spuštění relace vzdálené plochy s novým virtuálním Počítačem. Parametr &lt;publicIPAddress&gt; nahraďte IP adresou vašeho virtuálního počítače. Po zobrazení výzvy zadejte uživatelské jméno a heslo, které jste zadali při vytváření tohoto virtuálního počítače.

```
mstsc /v:<publicIpAddress>
```
## <a name="specify-different-resource-names"></a>Zadejte názvy různých prostředků

Můžete také poskytnout více popisné názvy pro prostředky a ještě další je vytvořen automaticky. Tady je příklad kde několik prostředků mají pojmenované pro nový virtuální počítač, včetně novou skupinu prostředků.

```azurepowershell-interactive
New-AzureRmVm `
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

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, virtuálního počítače a všech souvisejících prostředků použít příkaz [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myVM
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Další postup

V tomto tématu jsme nasadili jednoduchý virtuální počítač pomocí rutiny New-AzVM a potom se k němu připojili přes RDP. Další informace o virtuálních počítačích Azure najdete v kurzu pro virtuální počítače s Windows.

> [!div class="nextstepaction"]
> [Kurzy pro virtuální počítače Azure s Windows](./tutorial-manage-vm.md)
