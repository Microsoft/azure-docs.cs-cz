---
title: Kurz – vysoká dostupnost pro virtuální počítače s Windows v Azure
description: V tomto kurzu zjistíte, jak používat Azure PowerShell k nasazení vysoce dostupných virtuálních počítačů ve skupinách dostupnosti
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: tutorial
ms.date: 11/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 7fe1c01542df2fcc38982fe2a30f9e94c712eacb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87065257"
---
# <a name="tutorial-create-and-deploy-highly-available-virtual-machines-with-azure-powershell"></a>Kurz: Vytvoření a nasazení vysoce dostupných virtuálních počítačů v Azure PowerShellu

V tomto kurzu zjistíte, jak zvýšit dostupnost a spolehlivost vašich Virtual Machines (virtuálních počítačů) pomocí skupin dostupnosti. Skupiny dostupnosti zajišťují, že virtuální počítače, které nasazujete v Azure, jsou distribuované napříč několika izolovanými hardwarovými uzly v clusteru. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření skupiny dostupnosti
> * Vytvoření virtuálního počítače ve skupině dostupnosti
> * Kontrola dostupných velikostí virtuálních počítačů
> * Kontrola Azure Advisoru


## <a name="availability-set-overview"></a>Přehled skupiny dostupnosti

Skupina dostupnosti je logická možnost seskupení pro izolaci prostředků virtuálních počítačů při jejich nasazení. Azure zajišťuje, aby virtuální počítače, které umístíte do skupiny dostupnosti, běžely na několika fyzických serverech, výpočetních skříních, jednotkách úložiště a síťových přepínačích. Pokud dojde k selhání hardwaru nebo softwaru, bude ovlivněna pouze podmnožina virtuálních počítačů a vaše celkové řešení zůstane v provozu. Skupiny dostupnosti jsou zásadní pro vytváření spolehlivých cloudových řešení.

Představte si běžné řešení založené na virtuálních počítačích, ve kterém máte čtyři front-endové webové servery a dva back-endové virtuální počítače. S Azure budete chtít před nasazením virtuálních počítačů definovat dvě skupiny dostupnosti: jednu pro webovou vrstvu a jednu pro úroveň back. Když vytváříte nový virtuální počítač, zadáváte skupinu dostupnosti jako parametr. Azure zajišťuje izolaci virtuálních počítačů napříč několika fyzickými hardwarovými prostředky. Pokud má fyzický hardware, na kterém je spuštěný některý z vašich serverů, víte, že ostatní instance vašich serverů budou pořád spuštěné, protože jsou na jiném hardwaru.

Skupiny dostupnosti použijte v případě, že chcete v Azure nasadit spolehlivá řešení založená na virtuálních počítačích.

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

Nejdřív pomocí rutiny [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential?view=powershell-5.1) nastavte uživatelské jméno a heslo správce virtuálního počítače:

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
