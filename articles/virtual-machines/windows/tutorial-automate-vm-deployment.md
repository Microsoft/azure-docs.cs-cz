---
title: Kurz instalace aplikací na virtuální počítač s Windows v Azure | Microsoft Docs
description: V tomto kurzu zjistíte, jak používat rozšíření vlastních skriptů ke spouštění skriptů a nasazení aplikací na virtuálních počítačích s Windows v Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
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
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 51a66d524e72968d95f3415b890567015165063a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial---deploy-applications-to-a-windows-virtual-machine-in-azure-with-the-custom-script-extension"></a>Kurz nasazení aplikací na virtuální počítač s Windows v Azure pomocí rozšíření vlastních skriptů

Pokud chcete konfigurovat virtuální počítače rychle a konzistentně, zřejmě uvítáte nějakou formu automatizace. Běžným přístupem k přizpůsobení virtuálního počítače s Windows je použití [rozšíření vlastních skriptů pro Windows](extensions-customscript.md). V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Použít rozšíření vlastních skriptů k instalaci služby IIS
> * Vytvořit virtuální počítač, který používá rozšíření vlastních skriptů
> * Zobrazit spuštěný web služby IIS po použití tohoto rozšíření

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte v tomto kurzu použít modul Azure PowerShell verze 5.7.0 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzureRmAccount` pro vytvoření připojení k Azure.


## <a name="custom-script-extension-overview"></a>Přehled rozšíření vlastních skriptů
Rozšíření vlastních skriptů stahuje a spouští skripty na virtuálních počítačích Azure. Toto rozšíření je užitečné pro konfiguraci po nasazení, instalaci softwaru nebo jakékoli jiné úlohy konfigurace nebo správy. Skripty si můžete stáhnout z Azure Storage nebo z GitHubu, případně je za běhu rozšíření najdete na portálu Azure Portal.

Rozšíření vlastních skriptů integruje šablony Azure Resource Manageru a je možné ho spustit také pomocí rozhraní příkazového řádku Azure, PowerShellu, portálu Azure Portal nebo REST API pro virtuální počítač Azure.

Rozšíření vlastních skriptů můžete použít s virtuálními počítači se systémem Windows a Linux.


## <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače
Nejdřív pomocí rutiny [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) nastavte uživatelské jméno a heslo správce virtuálního počítače:

```azurepowershell-interactive
$cred = Get-Credential
```

Nyní můžete vytvořit virtuální počítač pomocí rutiny [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Následující příklad vytvoří virtuální počítač s názvem *myVM* v umístění *EastUS*. Pokud ještě neexistuje, vytvoří se skupina prostředků *myResourceGroupAutomate* a podpůrné síťové prostředky. Za účelem povolení webového provozu rutina také otevře port *80*.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Credential $cred
```

Vytvoření prostředků a virtuálního počítače trvá několik minut.


## <a name="automate-iis-install"></a>Automatizace instalace služby IIS
Pro instalaci rozšíření vlastních skriptů použijte rutinu [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). Rozšíření spustí `powershell Add-WindowsFeature Web-Server` za účelem instalace webového serveru služby IIS a potom aktualizuje stránku *Default.htm*, aby zobrazovala název hostitele virtuálního počítače:

```azurepowershell-interactive
Set-AzureRmVMExtension -ResourceGroupName "myResourceGroupAutomate" `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location "EastUS" `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```


## <a name="test-web-site"></a>Testovací web
Získejte veřejnou IP adresu vašeho nástroje pro vyrovnávání zatížení pomocí rutiny [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). Následující příklad získá dříve vytvořenou IP adresu pro *myPublicIPAddress*:

```azurepowershell-interactive
Get-AzureRmPublicIPAddress `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myPublicIPAddress" | select IpAddress
```

Veřejnou IP adresu pak můžete zadat do webového prohlížeče. Zobrazí se web, včetně názvu hostitele virtuálního počítače, do kterého nástroj pro vyrovnávání zatížení distribuoval provoz, jako v následujícím příkladu:

![Spuštění webu služby IIS](./media/tutorial-automate-vm-deployment/running-iis-website.png)


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste automatizovali instalaci služby IIS na virtuálním počítači. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Použít rozšíření vlastních skriptů k instalaci služby IIS
> * Vytvořit virtuální počítač, který používá rozšíření vlastních skriptů
> * Zobrazit spuštěný web služby IIS po použití tohoto rozšíření

V dalším kurzu se dozvíte, jak vytvořit vlastní image virtuálních počítačů.

> [!div class="nextstepaction"]
> [Vytváření vlastních imagí virtuálních počítačů](./tutorial-custom-images.md)
