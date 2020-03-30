---
title: Úvodní příručka – vytvoření virtuálního počítače s Windows pomocí Azure PowerShellu
description: V tomto rychlém startu zjistíte, jak pomocí Azure PowerShellu vytvořit virtuální počítač s Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/02/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 607ad17af42b88737d8ca4ebc379504d9b0b40df
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "74073446"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-azure-with-powershell"></a>Rychlý start: Vytvoření virtuálního počítače s Windows v Azure pomocí PowerShellu

Modul Azure PowerShell slouží k vytváření a správě prostředků Azure z příkazového řádku PowerShellu nebo ve skriptech. V tomto rychlém startu se dozvíte, jak pomocí modulu Azure Power Shell nasadit do Azure virtuální počítač s Windows Serverem 2016. Budete také RDP na virtuální ms a nainstalovat webový server služby IIS, chcete-li zobrazit virtuální ho v akci.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Pokud chcete otevřít Cloud Shell, vyberte položku **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné [https://shell.azure.com/powershell](https://shell.azure.com/powershell)kartě prohlížeče tak, že přejdete na . Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.


## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků Azure s [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvořte virtuální hosti s [novým AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). Zadejte názvy pro každý `New-AzVM` z prostředků a rutina vytvoří, pokud již neexistují.

Po zobrazení výzvy zadejte uživatelské jméno a heslo, které se mají použít jako přihlašovací pověření pro virtuální počítače:

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

Chcete-li zobrazit veřejnou IP adresu virtuálního počítačů, použijte rutinu [Get-AzPublicIpAddress:](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress)

```powershell
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" | Select "IpAddress"
```

Pomocí následujícího příkazu vytvořte ze svého místního počítače relaci vzdálené plochy. Nahraďte IP adresu veřejnou IP adresou vašeho virtuálního počítače. 

```powershell
mstsc /v:publicIpAddress
```

V okně **Zabezpečení systému Windows** vyberte **Další možnosti** a potom **Použít jiný účet**. Zadejte uživatelské jméno jako*uživatelské jméno* **localhost**\\, zadejte heslo, které jste vytvořili pro virtuální počítač, a klepněte na tlačítko **OK**.

Během procesu přihlášení se může zobrazit upozornění certifikátu. Chcete-li vytvořit připojení, klepněte na tlačítko **Ano** nebo **Pokračovat.**

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

Když už nepotřebujete, můžete pomocí rutiny [Odebrat AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) odebrat skupinu prostředků, virtuální hod a všechny související prostředky:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili jednoduchý virtuální počítač, otevřeli jste síťový port pro webový provoz a nainstalovali jste základní webový server. Další informace o virtuálních počítačích Azure najdete v kurzu pro virtuální počítače s Windows.

> [!div class="nextstepaction"]
> [Kurzy pro virtuální počítače Azure s Windows](./tutorial-manage-vm.md)
