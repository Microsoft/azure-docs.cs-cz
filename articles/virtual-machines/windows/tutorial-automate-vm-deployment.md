---
title: Kurz instalace aplikací na virtuální počítač s Windows v Azure | Microsoft Docs
description: V tomto kurzu zjistíte, jak používat rozšíření vlastních skriptů ke spouštění skriptů a nasazení aplikací na virtuálních počítačích s Windows v Azure
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
ms.date: 11/29/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 354625accb39344d07a22f2d3935cf4cf022d491
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55977656"
---
# <a name="tutorial---deploy-applications-to-a-windows-virtual-machine-in-azure-with-the-custom-script-extension"></a>Kurz nasazení aplikací na virtuální počítač s Windows v Azure pomocí rozšíření vlastních skriptů

Ke konfiguraci virtuálních počítačů (VM) v podobě rychle a konzistentně, můžete použít [vlastní skript rozšíření pro Windows](extensions-customscript.md). V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Použít rozšíření vlastních skriptů k instalaci služby IIS
> * Vytvořit virtuální počítač, který používá rozšíření vlastních skriptů
> * Zobrazit spuštěný web služby IIS po použití tohoto rozšíření

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Pokud chcete otevřít Cloud Shell, vyberte **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče na adrese [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

## <a name="custom-script-extension-overview"></a>Přehled rozšíření vlastních skriptů
Rozšíření vlastních skriptů stahuje a spouští skripty na virtuálních počítačích Azure. Toto rozšíření je užitečné pro konfiguraci po nasazení, instalaci softwaru nebo jakékoli jiné úlohy konfigurace nebo správy. Skripty si můžete stáhnout z Azure Storage nebo z GitHubu, případně je za běhu rozšíření najdete na portálu Azure Portal.

Rozšíření vlastních skriptů integruje šablony Azure Resource Manageru a je možné ho spustit také pomocí rozhraní příkazového řádku Azure, PowerShellu, portálu Azure Portal nebo REST API pro virtuální počítač Azure.

Rozšíření vlastních skriptů můžete použít s virtuálními počítači se systémem Windows a Linux.


## <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače
Nastavte uživatelské jméno správce a heslo pro virtuální počítač s [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Nyní můžete vytvořit virtuální počítač pomocí [rutiny New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). Následující příklad vytvoří virtuální počítač s názvem *myVM* v umístění *EastUS*. Pokud ještě neexistuje, vytvoří se skupina prostředků *myResourceGroupAutomate* a podpůrné síťové prostředky. Za účelem povolení webového provozu rutina také otevře port *80*.

```azurepowershell-interactive
New-AzVm `
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
Použití [Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) k instalaci rozšíření vlastních skriptů. Rozšíření spustí `powershell Add-WindowsFeature Web-Server` za účelem instalace webového serveru služby IIS a potom aktualizuje stránku *Default.htm*, aby zobrazovala název hostitele virtuálního počítače:

```azurepowershell-interactive
Set-AzVMExtension -ResourceGroupName "myResourceGroupAutomate" `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location "EastUS" `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```


## <a name="test-web-site"></a>Testovací web
Získat veřejnou IP adresu vašeho nástroje pro vyrovnávání zatížení s [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress). Následující příklad získá dříve vytvořenou IP adresu pro *myPublicIPAddress*:

```azurepowershell-interactive
Get-AzPublicIPAddress `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myPublicIPAddress" | select IpAddress
```

Veřejnou IP adresu pak můžete zadat do webového prohlížeče. Zobrazí se web, včetně názvu hostitele virtuálního počítače, do kterého nástroj pro vyrovnávání zatížení distribuoval provoz, jako v následujícím příkladu:

![Spuštění webu služby IIS](./media/tutorial-automate-vm-deployment/running-iis-website.png)


## <a name="next-steps"></a>Další postup

V tomto kurzu jste automatizovali instalaci služby IIS na virtuálním počítači. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Použít rozšíření vlastních skriptů k instalaci služby IIS
> * Vytvořit virtuální počítač, který používá rozšíření vlastních skriptů
> * Zobrazit spuštěný web služby IIS po použití tohoto rozšíření

V dalším kurzu se dozvíte, jak vytvořit vlastní image virtuálních počítačů.

> [!div class="nextstepaction"]
> [Vytváření vlastních imagí virtuálních počítačů](./tutorial-custom-images.md)
