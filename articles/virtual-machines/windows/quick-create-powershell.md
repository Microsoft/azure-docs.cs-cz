---
title: Rychlý start Azure – Vytvoření virtuálního počítače s Windows pomocí PowerShellu | Dokumentace Microsoftu
description: Rychle se naučíte, jak vytvářet virtuální počítače s Windows pomocí PowerShellu.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/12/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: f1ab714407bd686ac8bffb23c7ba9ec8951902ef
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-windows-virtual-machine-with-powershell"></a>Vytvoření virtuálního počítače s Windows pomocí PowerShellu

Modul Azure PowerShell slouží k vytváření a správě prostředků Azure z příkazového řádku PowerShellu nebo ve skriptech. Tento Rychlý start podrobně popisuje vytvoření virtuálního počítače s Windows Serverem 2016 pomocí PowerShellu. Po dokončení nasazení se připojíme k serveru a nainstalujeme službu IIS.  

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít modul Azure PowerShell verze 5.3.0 nebo novější. Verzi zjistíte spuštěním příkazu ` Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzureRmAccount` pro vytvoření připojení k Azure.



## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků Azure pomocí příkazu [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. 

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```


## <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvořte virtuální počítač pomocí příkazu [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Stačí zadat názvy jednotlivých prostředků, a pokud zatím neexistují, rutina New-AzureRMVM je vytvoří.

Při spuštění tohoto kroku se zobrazí výzva k zadání přihlašovacích údajů. Hodnoty, které zadáte, se nakonfigurují jako uživatelské jméno a heslo pro virtuální počítač.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80,3389  
```

## <a name="connect-to-virtual-machine"></a>Připojení k virtuálnímu počítači

Po dokončení nasazení vytvořte připojení ke vzdálené ploše virtuálního počítače.

Použijte příkaz [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress), který vrátí veřejnou IP adresu virtuálního počítače. Poznamenejte si tuto IP adresu, abyste se k ní v dalším kroku mohli pomocí prohlížeče připojit a otestovat připojení k webu.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Pomocí následujícího příkazu na svém místním počítači vytvořte s virtuálním počítačem relaci vzdálené plochy. IP adresu nahraďte veřejnou IP adresou (*publicIPAddress*) vašeho virtuálního počítače. Po zobrazení výzvy zadejte přihlašovací údaje, které jste použili při vytváření virtuálního počítače.

```
mstsc /v:<publicIpAddress>
```

## <a name="install-iis-via-powershell"></a>Instalace služby IIS pomocí PowerShellu

Když jste přihlášeni k virtuálnímu počítači Azure, můžete k instalaci služby IIS a k aktivaci pravidla místní brány firewall pro povolení webového provozu použít jeden řádek PowerShellu. Ve virtuálním počítači otevřete příkazový řádek PowerShellu a spusťte následující příkaz:

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Zobrazení úvodní stránky služby IIS

S nainstalovanou službou IIS na virtuálním počítači a nyní otevřeným portem 80 z internetu můžete použít libovolný webový prohlížeč a zobrazit výchozí úvodní stránku služby IIS. Nezapomeňte pro návštěvu výchozí stránky použít veřejnou IP adresu (*publicIpAddress*) popsanou výše. 

![Výchozí web služby IIS](./media/quick-create-powershell/default-iis-website.png) 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, virtuálního počítače a všech souvisejících prostředků použít příkaz [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto Rychlém startu jste nasadili jednoduchý virtuální počítač a pravidlo skupiny zabezpečení sítě a nainstalovali jste webový server. Další informace o virtuálních počítačích Azure najdete v kurzu pro virtuální počítače s Windows.

> [!div class="nextstepaction"]
> [Kurzy pro virtuální počítače Azure s Windows](./tutorial-manage-vm.md)
