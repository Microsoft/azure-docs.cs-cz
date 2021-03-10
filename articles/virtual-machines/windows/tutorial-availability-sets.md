---
title: Nasazení virtuálních počítačů ve skupině dostupnosti pomocí Azure PowerShell
description: Naučte se používat Azure PowerShell k nasazení virtuálních počítačů s vysokou dostupností do skupin dostupnosti.
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 3/8/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.custom: mvc
ms.openlocfilehash: 178a29ea37195ddd2013ca5220663a75132beb24
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102555903"
---
# <a name="create-and-deploy-virtual-machines-in-an-availability-set-using-azure-powershell"></a>Vytvoření a nasazení virtuálních počítačů ve skupině dostupnosti pomocí Azure PowerShell

V tomto kurzu zjistíte, jak zvýšit dostupnost a spolehlivost vašich Virtual Machines (virtuálních počítačů) pomocí skupin dostupnosti. Skupiny dostupnosti zajišťují, že virtuální počítače, které nasazujete v Azure, jsou distribuované napříč několika izolovanými hardwarovými uzly v clusteru. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření skupiny dostupnosti
> * Vytvoření virtuálního počítače ve skupině dostupnosti
> * Kontrola dostupných velikostí virtuálních počítačů
> * Kontrola Azure Advisoru


## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Pokud chcete otevřít Cloud Shell, vyberte položku **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče tak, že přejdete na [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

## <a name="create-an-availability-set"></a>Vytvoření skupiny dostupnosti

Hardware ve vybraném umístění je rozdělený do několika aktualizačních domén a domén selhání. **Aktualizační doména** je skupina virtuálních počítačů a podřízeného fyzického hardwaru, který je možné současně restartovat. Virtuální počítače v jedné **doméně selhání** sdílejí společné úložiště a také zdroj napájení a síťový přepínač.  

Skupinu dostupnosti můžete vytvořit pomocí [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset). V tomto příkladu je počet domén aktualizace i selhání *2* a skupina dostupnosti má název *myAvailabilitySet*.

Vytvořte skupinu prostředků.

```azurepowershell-interactive
New-AzResourceGroup `
   -Name myResourceGroupAvailability `
   -Location EastUS
```

Vytvořte spravovanou skupinu dostupnosti pomocí [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) s `-sku aligned` parametrem.

```azurepowershell-interactive
New-AzAvailabilitySet `
   -Location "EastUS" `
   -Name "myAvailabilitySet" `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Sku aligned `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## <a name="create-vms-inside-an-availability-set"></a>Vytvoření virtuálních počítačů ve skupině dostupnosti
Virtuální počítače musí být vytvořené v rámci skupiny dostupnosti, abyste se ujistili, že jsou správně distribuované napříč hardwarem. Existující virtuální počítač nemůžete přidat do skupiny dostupnosti po jeho vytvoření. 


Když vytvoříte virtuální počítač pomocí [New-AzVM](/powershell/module/az.compute/new-azvm), použijete `-AvailabilitySetName` parametr k zadání názvu skupiny dostupnosti.

Nejdřív pomocí rutiny [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) nastavte uživatelské jméno a heslo správce virtuálního počítače:

```azurepowershell-interactive
$cred = Get-Credential
```

Teď vytvořte dva virtuální počítače pomocí [New-AzVM](/powershell/module/az.compute/new-azvm) ve skupině dostupnosti.

```azurepowershell-interactive
for ($i=1; $i -le 2; $i++)
{
    New-AzVm `
        -ResourceGroupName "myResourceGroupAvailability" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -PublicIpAddressName "myPublicIpAddress$i" `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred
}
```

Vytvoření a konfigurace obou virtuálních počítačů bude trvat několik minut. Po dokončení budete mít dva virtuální počítače distribuované napříč základním hardwarem. 

Pokud se podíváte na skupinu dostupnosti na portálu, přejděte na **skupiny prostředků**  >  **myResourceGroupAvailability**  >  **myAvailabilitySet**, měli byste vidět, jak se virtuální počítače distribuují napříč dvěma doménami selhání a aktualizačními doménami.

![Skupina dostupnosti na portálu](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Kontrola dostupných velikostí virtuálních počítačů 

Když vytvoříte virtuální počítač ve skupině dostupnosti, budete potřebovat zjistit, jaké velikosti virtuálních počítačů jsou na hardwaru k dispozici. Pomocí příkazu [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize) získáte všechny dostupné velikosti pro virtuální počítače, které můžete nasadit ve skupině dostupnosti.

```azurepowershell-interactive
Get-AzVMSize `
   -ResourceGroupName "myResourceGroupAvailability" `
   -AvailabilitySetName "myAvailabilitySet"
```

## <a name="check-azure-advisor"></a>Kontrola Azure Advisoru 

K získání dalších informací o tom, jak zlepšit dostupnost virtuálních počítačů, můžete použít také Azure Advisor. Azure Advisor analyzuje vaši telemetrii o konfiguraci a využití a pak doporučuje řešení, která vám pomůžou vylepšit efektivitu nákladů, výkon, dostupnost a zabezpečení vašich prostředků Azure.

Přihlaste se na [Azure Portal](https://portal.azure.com), vyberte **Všechny služby** a zadejte **Advisor**. Řídicí panel poradce zobrazuje individuální doporučení pro vybrané předplatné. Další informace najdete v tématu [Začínáme se službou Azure Advisor](../../advisor/advisor-get-started.md).


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření skupiny dostupnosti
> * Vytvoření virtuálního počítače ve skupině dostupnosti
> * Kontrola dostupných velikostí virtuálních počítačů
> * Kontrola Azure Advisoru

Přejděte k dalšímu kurzu, kde se seznámíte se škálovacími sadami virtuálních počítačů.

> [!div class="nextstepaction"]
> [Vytvoření škálovací sady virtuálních počítačů](tutorial-create-vmss.md)
