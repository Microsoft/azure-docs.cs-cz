---
title: Rychlý start – Vytvoření virtuálního počítače s Windows pomocí Azure PowerShellu | Microsoft Docs
description: V tomto rychlém startu zjistíte, jak pomocí Azure PowerShellu vytvořit virtuální počítač s Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/22/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 44da9bc528883b2686adca6f4310212d785847d8
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55981481"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-azure-with-powershell"></a>Rychlý start: Vytvoření virtuálního počítače Windows v Azure pomocí Powershellu

Modul Azure PowerShell slouží k vytváření a správě prostředků Azure z příkazového řádku PowerShellu nebo ve skriptech. V tomto rychlém startu se dozvíte, jak pomocí modulu Azure Power Shell nasadit do Azure virtuální počítač s Windows Serverem 2016. Můžete se také RDP k virtuálnímu počítači a instalaci webového serveru IIS, chcete-li zobrazit virtuální počítač v akci.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Pokud chcete otevřít Cloud Shell, vyberte **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče na adrese [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.


## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků Azure pomocí [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvoření virtuálního počítače s [nové AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). Zadat názvy jednotlivých prostředků a `New-AzVM` rutina vytvoří, pokud ještě neexistují.

Po zobrazení výzvy zadejte uživatelské jméno a heslo, které má být použit jako přihlašovací údaje pro virtuální počítač:

```azurepowershell-interactive
New-AzVm `
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

Po dokončení nasazení se přihlaste k virtuálnímu počítači s využitím RDP. K zajištění fungování virtuálního počítače se nainstaluje webový server IIS.

Pokud chcete zjistit veřejnou IP adresu virtuálního počítače, použijte [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) rutiny:

```powershell
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" | Select "IpAddress"
```

Pomocí následujícího příkazu vytvořte ze svého místního počítače relaci vzdálené plochy. Nahraďte IP adresu veřejnou IP adresou vašeho virtuálního počítače. 

```powershell
mstsc /v:publicIpAddress
```

V okně **Zabezpečení systému Windows** vyberte **Další možnosti** a potom **Použít jiný účet**. Zadejte uživatelské jméno ve formátu **místní_hostitel**\\*uživatelské_jméno* a heslo, které jste pro virtuální počítač vytvořili, a potom klikněte na **OK**.

Během procesu přihlášení se může zobrazit upozornění certifikátu. Vytvořte připojení kliknutím na **Ano** nebo **Pokračovat**.

## <a name="install-web-server"></a>Instalace webového serveru

Pokud se chcete podívat na virtuální počítač v akci, nainstalujte webový server služby IIS. Ve virtuálním počítači otevřete příkazový řádek PowerShellu a spusťte následující příkaz:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Jakmile budete hotovi, ukončete připojení RDP k virtuálnímu počítači.

## <a name="view-the-web-server-in-action"></a>Zobrazení webového serveru v akci

S nainstalovanou službou IIS na virtuálním počítači a nyní otevřeným portem 80 z internetu můžete použít libovolný webový prohlížeč a zobrazit výchozí úvodní stránku služby IIS. Použijte veřejnou IP adresu virtuálního počítače, kterou jste získali v předchozím kroku. Následující příklad ukazuje výchozí webovou stránku služby IIS:

![Výchozí web služby IIS](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete použít [odebrat AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) rutina pro odebrání skupiny prostředků, virtuálního počítače a všech souvisejících prostředků:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste nasadili jednoduchý virtuální počítač, otevřeli jste síťový port pro webový provoz a nainstalovali jste základní webový server. Další informace o virtuálních počítačích Azure najdete v kurzu pro virtuální počítače s Windows.

> [!div class="nextstepaction"]
> [Kurzy pro virtuální počítače Azure s Windows](./tutorial-manage-vm.md)
