---
title: Kurz – instalace aplikací na virtuální počítač s Windows v Azure
description: V tomto kurzu zjistíte, jak používat rozšíření vlastních skriptů ke spouštění skriptů a nasazení aplikací na virtuálních počítačích s Windows v Azure
author: cynthn
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 11/29/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 14d0190a97c22a805065ceaf41dcd655b9e8182b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87065281"
---
# <a name="tutorial---deploy-applications-to-a-windows-virtual-machine-in-azure-with-the-custom-script-extension"></a>Kurz nasazení aplikací na virtuální počítač s Windows v Azure pomocí rozšíření vlastních skriptů

K rychlému a konzistentnímu konfigurování virtuálních počítačů můžete použít [rozšíření vlastních skriptů pro Windows](../extensions/custom-script-windows.md). Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Použít rozšíření vlastních skriptů k instalaci služby IIS
> * Vytvořit virtuální počítač, který používá rozšíření vlastních skriptů
> * Zobrazit spuštěný web služby IIS po použití tohoto rozšíření

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Pokud chcete otevřít Cloud Shell, vyberte položku **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče tak, že přejdete na [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

## <a name="custom-script-extension-overview"></a>Přehled rozšíření vlastních skriptů
Rozšíření vlastních skriptů stahuje a spouští skripty na virtuálních počítačích Azure. Toto rozšíření je užitečné pro konfiguraci po nasazení, instalaci softwaru nebo jakékoli jiné úlohy konfigurace nebo správy. Skripty si můžete stáhnout z Azure Storage nebo z GitHubu, případně je za běhu rozšíření najdete na portálu Azure Portal.

Rozšíření vlastních skriptů integruje šablony Azure Resource Manageru a je možné ho spustit také pomocí rozhraní příkazového řádku Azure, PowerShellu, portálu Azure Portal nebo REST API pro virtuální počítač Azure.

Rozšíření vlastních skriptů můžete použít s virtuálními počítači se systémem Windows a Linux.


## <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače
Nastavte uživatelské jméno a heslo správce virtuálního počítače pomocí [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential?view=powershell-5.1):

```azurepowershell-interactive
$cred = Get-Credential
```

Nyní můžete vytvořit virtuální počítač pomocí [New-AzVM](/powershell/module/az.compute/new-azvm). Následující příklad vytvoří virtuální počítač s názvem *myVM* v umístění *EastUS*. Pokud ještě neexistuje, vytvoří se skupina prostředků *myResourceGroupAutomate* a podpůrné síťové prostředky. Za účelem povolení webového provozu rutina také otevře port *80*.

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
K instalaci rozšíření vlastních skriptů použijte [set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) . Rozšíření spustí `powershell Add-WindowsFeature Web-Server` za účelem instalace webového serveru služby IIS a potom aktualizuje stránku *Default.htm*, aby zobrazovala název hostitele virtuálního počítače:

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
Získejte veřejnou IP adresu vašeho nástroje pro vyrovnávání zatížení pomocí [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress). Následující příklad získá dříve vytvořenou IP adresu pro *myPublicIPAddress*:

```azurepowershell-interactive
Get-AzPublicIPAddress `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myPublicIPAddress" | select IpAddress
```

Veřejnou IP adresu pak můžete zadat do webového prohlížeče. Zobrazí se web, včetně názvu hostitele virtuálního počítače, do kterého nástroj pro vyrovnávání zatížení distribuoval provoz, jako v následujícím příkladu:

![Spuštění webu služby IIS](./media/tutorial-automate-vm-deployment/running-iis-website.png)


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste automatizovali instalaci služby IIS na virtuálním počítači. Naučili jste se:

> [!div class="checklist"]
> * Použít rozšíření vlastních skriptů k instalaci služby IIS
> * Vytvořit virtuální počítač, který používá rozšíření vlastních skriptů
> * Zobrazit spuštěný web služby IIS po použití tohoto rozšíření

V dalším kurzu se dozvíte, jak vytvořit vlastní image virtuálních počítačů.

> [!div class="nextstepaction"]
> [Vytváření vlastních imagí virtuálních počítačů](./tutorial-custom-images.md)
