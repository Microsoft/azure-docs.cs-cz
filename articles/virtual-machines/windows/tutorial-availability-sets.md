---
title: Kurz – vysoká dostupnost pro virtuální počítače s Windows v Azure
description: V tomto kurzu zjistíte, jak používat Azure PowerShell k nasazení vysoce dostupných virtuálních počítačů ve skupinách dostupnosti
documentationcenter: ''
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: tutorial
ms.date: 11/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 0f94f4d312cefec80a0f294e256ee1ad908b903c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74068137"
---
# <a name="tutorial-create-and-deploy-highly-available-virtual-machines-with-azure-powershell"></a>Kurz: Vytvoření a nasazení vysoce dostupných virtuálních počítačů v Azure PowerShellu

V tomto kurzu se dozvíte, jak zvýšit dostupnost a spolehlivost virtuálních počítačů (VM) pomocí sad dostupnosti. Dostupnost Sady ujistěte se, že virtuální počítače, které nasadíte v Azure jsou distribuovány mezi více, izolované hardwarové uzly, v clusteru. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření skupiny dostupnosti
> * Vytvoření virtuálního počítače ve skupině dostupnosti
> * Kontrola dostupných velikostí virtuálních počítačů
> * Kontrola Azure Advisoru


## <a name="availability-set-overview"></a>Přehled skupiny dostupnosti

Sada dostupnosti je logická funkce seskupení pro izolaci prostředků virtuálního zařízení od sebe navzájem, když jsou nasazeny. Azure zajišťuje, že virtuální počítače, které umístíte v rámci sady dostupnosti, běží na více fyzických serverech, výpočetních rackech, úložných jednotkách a síťových přepínačích. Pokud dojde k selhání hardwaru nebo softwaru, ovlivní se pouze podmnožina virtuálních počítačích a vaše celkové řešení zůstane funkční. Sady dostupnosti jsou nezbytné pro vytváření spolehlivých cloudových řešení.

Představte si běžné řešení založené na virtuálních počítačích, ve kterém máte čtyři front-endové webové servery a dva back-endové virtuální počítače. S Azure byste měli definovat dvě skupiny dostupnosti před nasazením virtuálních počítačů: jednu pro webovou vrstvu a jednu pro zadní vrstvu. Při vytváření nového virtuálního soudu určíte jako parametr sadu dostupnosti. Azure zajišťuje, že virtuální počítače jsou izolované napříč více prostředky fyzického hardwaru. Pokud má problém fyzický hardware, na kterém je spuštěn jeden z vašich serverů, víte, že ostatní instance serverů budou spuštěny, protože jsou na jiném hardwaru.

Skupiny dostupnosti použijte v případě, že chcete v Azure nasadit spolehlivá řešení založená na virtuálních počítačích.

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Pokud chcete otevřít Cloud Shell, vyberte položku **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné [https://shell.azure.com/powershell](https://shell.azure.com/powershell)kartě prohlížeče tak, že přejdete na . Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

## <a name="create-an-availability-set"></a>Vytvoření skupiny dostupnosti

Hardware ve vybraném umístění je rozdělený do několika aktualizačních domén a domén selhání. **Aktualizační doména** je skupina virtuálních počítačů a podřízeného fyzického hardwaru, který je možné současně restartovat. Virtuální počítače v jedné **doméně selhání** sdílejí společné úložiště a také zdroj napájení a síťový přepínač.  

Můžete vytvořit sadu dostupnosti pomocí [New-AzAvailabilitySet](https://docs.microsoft.com/powershell/module/az.compute/new-azavailabilityset). V tomto příkladu je počet domén aktualizace i chyb *2* a sada dostupnosti je *pojmenována myAvailabilitySet*.

Vytvořte skupinu prostředků.

```azurepowershell-interactive
New-AzResourceGroup `
   -Name myResourceGroupAvailability `
   -Location EastUS
```

Vytvořte spravovanou skupinu dostupnosti pomocí `-sku aligned` [funkce New-AzAvailabilitySet](https://docs.microsoft.com/powershell/module/az.compute/new-azavailabilityset) s parametrem.

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
Virtuální aplikace musí být vytvořeny v rámci sady dostupnosti a ujistěte se, že jsou správně distribuovány napříč hardwarem. Existující virtuální hod nelze přidat do skupiny dostupnosti po jeho vytvoření. 


Při vytváření virtuálního virtuálního provozu s [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm), můžete použít `-AvailabilitySetName` parametr k určení názvu skupiny dostupnosti.

Nejdřív pomocí rutiny [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) nastavte uživatelské jméno a heslo správce virtuálního počítače:

```azurepowershell-interactive
$cred = Get-Credential
```

Teď vytvořte dva virtuální hody s [Novým AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) v sadě dostupnosti.

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

Pokud se podíváte na dostupnost nastavit na portálu tím, že přejdete na **skupiny** > prostředků**myResourceGroupAvailability** > **myAvailabilitySet**, měli byste vidět, jak jsou virtuální chody distribuovány mezi dvě domény selhání a aktualizace.

![Skupina dostupnosti na portálu](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Kontrola dostupných velikostí virtuálních počítačů 

Později můžete do skupiny dostupnosti přidat další virtuální počítače, potřebujete však vědět, jaké velikosti virtuálních počítačů jsou na konkrétním hardwaru k dispozici. Pomocí [funkce Get-AzVMSize](https://docs.microsoft.com/powershell/module/az.compute/get-azvmsize) můžete vypsat všechny dostupné velikosti v hardwarovém clusteru pro sadu dostupnosti.

```azurepowershell-interactive
Get-AzVMSize `
   -ResourceGroupName "myResourceGroupAvailability" `
   -AvailabilitySetName "myAvailabilitySet"
```

## <a name="check-azure-advisor"></a>Kontrola Azure Advisoru 

Azure Advisor můžete taky použít k získání dalších informací o tom, jak zlepšit dostupnost virtuálních počítačů. Azure Advisor analyzuje vaši telemetrii konfigurace a využití a pak doporučí řešení, která vám pomůžou zlepšit efektivitu nákladů, výkon, dostupnost a zabezpečení vašich prostředků Azure.

Přihlaste se na [Azure Portal](https://portal.azure.com), vyberte **Všechny služby** a zadejte **Advisor**. Řídicí panel Poradce zobrazuje přizpůsobená doporučení pro vybrané předplatné. Další informace najdete v tématu [Začínáme se službou Azure Advisor](../../advisor/advisor-get-started.md).


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření skupiny dostupnosti
> * Vytvoření virtuálního počítače ve skupině dostupnosti
> * Kontrola dostupných velikostí virtuálních počítačů
> * Kontrola Azure Advisoru

Přejděte k dalšímu kurzu, kde se seznámíte se škálovacími sadami virtuálních počítačů.

> [!div class="nextstepaction"]
> [Vytvoření škálovací sady virtuálních mísy](tutorial-create-vmss.md)


