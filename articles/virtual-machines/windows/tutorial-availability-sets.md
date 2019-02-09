---
title: Kurz vysoké dostupnosti pro virtuální počítače s Windows v Azure | Microsoft Docs
description: V tomto kurzu zjistíte, jak používat Azure PowerShell k nasazení vysoce dostupných virtuálních počítačů ve skupinách dostupnosti
documentationcenter: ''
services: virtual-machines-windows
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: tutorial
ms.date: 11/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 3ee9740f9ef7e364c47bb205315683d1e4ea9294
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55977117"
---
# <a name="tutorial-create-and-deploy-highly-available-virtual-machines-with-azure-powershell"></a>Kurz: Vytváření a nasazování vysoce dostupných virtuálních počítačů pomocí Azure Powershellu

V tomto kurzu se dozvíte, jak zvýšit dostupnost a spolehlivost vašich virtuálních počítačů (VM) pomocí skupin dostupnosti. Skupiny dostupnosti Ujistěte se, že virtuálních počítačů nasazených v Azure jsou distribuované napříč několika izolovanými hardwarovými uzly v clusteru. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření skupiny dostupnosti
> * Vytvoření virtuálního počítače ve skupině dostupnosti
> * Kontrola dostupných velikostí virtuálních počítačů
> * Kontrola Azure Advisoru


## <a name="availability-set-overview"></a>Přehled skupiny dostupnosti

Skupina dostupnosti je funkce logického seskupení pro izolaci prostředků virtuálních počítačů od sebe navzájem při nasazení. Azure zajišťuje, že virtuální počítače, které umístíte do skupiny dostupnosti spouštět napříč několika fyzickými servery, compute racky, jednotkami úložiště a síťové přepínače. Pokud dojde k selhání hardwaru nebo softwaru, ovlivní to pouze dílčí část vašich virtuálních počítačů a vaše celkové řešení zůstane funkční. Skupiny dostupnosti jsou nezbytné pro vytváření spolehlivých cloudových řešení.

Představte si běžné řešení založené na virtuálních počítačích, ve kterém máte čtyři front-endové webové servery a dva back-endové virtuální počítače. S Azure, byste měli definovat dvě skupiny dostupnosti, před nasazením virtuálních počítačů: jeden pro webovou vrstvu a jeden pro úroveň zpět. Při vytváření nového virtuálního počítače je zadat jako parametr skupinu dostupnosti. Azure zajistí že izolaci virtuálních počítačů napříč několika fyzickými hardwarovými prostředky. Pokud fyzický hardware, jeden z vašich serverů se systémem na problém, víte, že v ostatních instancích serverů zachovat poběží, protože jsou uvedené na jiný hardware.

Skupiny dostupnosti použijte v případě, že chcete v Azure nasadit spolehlivá řešení založená na virtuálních počítačích.

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Pokud chcete otevřít Cloud Shell, vyberte **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče na adrese [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

## <a name="create-an-availability-set"></a>Vytvoření skupiny dostupnosti

Hardware ve vybraném umístění je rozdělený do několika aktualizačních domén a domén selhání. **Aktualizační doména** je skupina virtuálních počítačů a podřízeného fyzického hardwaru, který je možné současně restartovat. Virtuální počítače v jedné **doméně selhání** sdílejí společné úložiště a také zdroj napájení a síťový přepínač.  

Můžete vytvořit dostupnosti pomocí [New-AzAvailabilitySet](https://docs.microsoft.com/powershell/module/az.compute/new-azavailabilityset). V tomto příkladu je počet domén, aktualizace a odolnost *2* a skupina dostupnosti se jmenuje *myAvailabilitySet*.

Vytvořte skupinu prostředků.

```azurepowershell-interactive
New-AzResourceGroup `
   -Name myResourceGroupAvailability `
   -Location EastUS
```

Vytvořte spravovanou skupinu dostupnosti pomocí [New-AzAvailabilitySet](https://docs.microsoft.com/powershell/module/az.compute/new-azavailabilityset) s `-sku aligned` parametru.

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
Virtuální počítače se musí vytvořit ve skupině dostupnosti, abyste měli jistotu, že jste správně distribuované napříč hardwarem. Nelze přidat existující virtuální počítač do skupiny dostupnosti po jeho vytvoření. 


Při vytváření virtuálního počítače s [rutiny New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm), je použít `-AvailabilitySetName` parametr zadejte název skupiny dostupnosti.

Nejdřív pomocí rutiny [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) nastavte uživatelské jméno a heslo správce virtuálního počítače:

```azurepowershell-interactive
$cred = Get-Credential
```

Teď vytvořte dva virtuální počítače s [rutiny New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) ve skupině dostupnosti nastavena.

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

Pokud se podíváte na dostupnosti na portálu přejděte k **skupiny prostředků** > **myResourceGroupAvailability** > **myAvailabilitySet**, byste měli vidět, jak virtuální počítače distribuují napříč dvěma doménami selhání a aktualizačními doménami.

![Skupina dostupnosti na portálu](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Kontrola dostupných velikostí virtuálních počítačů 

Později můžete do skupiny dostupnosti přidat další virtuální počítače, potřebujete ale vědět, jaké velikosti virtuálních počítačů jsou na konkrétním hardwaru k dispozici. Použití [Get-AzVMSize](https://docs.microsoft.com/powershell/module/az.compute/get-azvmsize) seznam všech dostupných velikostí na hardwarovém clusteru pro skupinu dostupnosti.

```azurepowershell-interactive
Get-AzVMSize `
   -ResourceGroupName "myResourceGroupAvailability" `
   -AvailabilitySetName "myAvailabilitySet"
```

## <a name="check-azure-advisor"></a>Kontrola Azure Advisoru 

Azure Advisor. můžete také získat další informace o tom, jak zlepšit dostupnost vašich virtuálních počítačů. Azure Advisor analyzuje konfiguraci a telemetrii využití a doporučí řešení, která vám může pomoct vylepšit nákladové efektivity, výkonu, dostupnosti a zabezpečení vašich prostředků Azure.

Přihlaste se na [Azure Portal](https://portal.azure.com), vyberte **Všechny služby** a zadejte **Advisor**. Řídicí panel služby Advisor zobrazí individuální doporučení pro vybrané předplatné. Další informace najdete v tématu [Začínáme se službou Azure Advisor](../../advisor/advisor-get-started.md).


## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření skupiny dostupnosti
> * Vytvoření virtuálního počítače ve skupině dostupnosti
> * Kontrola dostupných velikostí virtuálních počítačů
> * Kontrola Azure Advisoru

V dalším kurzu najdete informace o škálovacích sadách virtuálních počítačů.

> [!div class="nextstepaction"]
> [Vytvoření škálovací sady virtuálních počítačů](tutorial-create-vmss.md)


