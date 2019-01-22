---
title: Kurz vysoké dostupnosti pro virtuální počítače s Windows v Azure | Microsoft Docs
description: V tomto kurzu zjistíte, jak používat Azure PowerShell k nasazení vysoce dostupných virtuálních počítačů ve skupinách dostupnosti
documentationcenter: ''
services: virtual-machines-windows
author: zr-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: tutorial
ms.date: 02/09/2018
ms.author: zarhoads
ms.custom: mvc
ms.openlocfilehash: 71ae05abef1cf70b64c0390d81cb7e50bab22c5a
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54429476"
---
# <a name="tutorial-create-and-deploy-highly-available-virtual-machines-with-azure-powershell"></a>Kurz: Vytváření a nasazování vysoce dostupných virtuálních počítačů pomocí Azure Powershellu

V tomto kurzu zjistíte, jak zvýšit dostupnost a spolehlivost svých řešení, která využívají virtuální počítače v Azure, pomocí funkce označované jako skupiny dostupnosti. Skupiny dostupnosti zajišťují distribuci virtuálních počítačů nasazených v Azure napříč několika izolovanými hardwarovými uzly v clusteru. To zajišťuje, že pokud dojde k selhání hardwaru nebo softwaru v rámci Azure, ovlivní to pouze dílčí část vašich virtuálních počítačů a vaše celkové řešení zůstane dostupné a funkční.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření skupiny dostupnosti
> * Vytvoření virtuálního počítače ve skupině dostupnosti
> * Kontrola dostupných velikostí virtuálních počítačů
> * Kontrola Azure Advisoru

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte v tomto kurzu použít modul Azure PowerShell verze 5.7.0 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzureRmAccount` pro vytvoření připojení k Azure.

## <a name="availability-set-overview"></a>Přehled skupiny dostupnosti

Skupina dostupnosti je funkce logického seskupení, pomocí které můžete v Azure zajistit, že prostředky virtuálních počítačů, které do ní umístíte, jsou při nasazení v datacentru Azure od sebe navzájem izolované. Azure zajišťuje, že virtuální počítače, které umístíte do skupiny dostupnosti, se budou spouštět napříč několika fyzickými servery, výpočetními racky, jednotkami úložiště a síťovými přepínači. Pokud dojde k selhání hardwaru nebo softwaru Azure, ovlivní to pouze dílčí část vašich virtuálních počítačů a váš výpočetní systém zůstane v provozu a bude pro zákazníky dál dostupný. Skupiny dostupnosti představují základní funkci pro vytváření spolehlivých cloudových řešení.

Představte si běžné řešení založené na virtuálních počítačích, ve kterém máte čtyři front-endové webové servery a dva back-endové virtuální počítače. Pokud používáte Azure, před nasazením virtuálních počítačů byste měli definovat dvě skupiny dostupnosti: jednu skupinu dostupnosti pro webovou vrstvu a jednu skupinu dostupnosti pro vrstvu back-endu. Při vytváření nového virtuálního počítače pak můžete zadat skupinu dostupnosti jako parametr příkazu az vm create a Azure automaticky zajistí izolaci virtuálních počítačů vytvořených v rámci skupiny dostupnosti napříč několika fyzickými hardwarovými prostředky. Pokud dojde k problému s fyzickým hardwarem, na kterém běží virtuální počítače s webovým serverem nebo back-endem, máte jistotu, že ostatní instance virtuálních počítačů s webovým serverem a back-endem zůstanou spuštěné, protože jsou na jiném hardwaru.

Skupiny dostupnosti použijte v případě, že chcete v Azure nasadit spolehlivá řešení založená na virtuálních počítačích.

## <a name="create-an-availability-set"></a>Vytvoření skupiny dostupnosti

Skupinu dostupnosti můžete vytvořit pomocí příkazu [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset). V tomto příkladu nastavíme počet aktualizačních domén i domén selhání na *2* pro skupinu dostupnosti *myAvailabilitySet* ve skupině prostředků *myResourceGroupAvailability*.

Vytvořte skupinu prostředků.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroupAvailability -Location EastUS
```

Vytvořte spravovanou skupinu dostupnosti pomocí příkazu [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset) s parametrem `-sku aligned`.

```azurepowershell-interactive
New-AzureRmAvailabilitySet `
   -Location "EastUS" `
   -Name "myAvailabilitySet" `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Sku aligned `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## <a name="create-vms-inside-an-availability-set"></a>Vytvoření virtuálních počítačů ve skupině dostupnosti
Virtuální počítače je nutné vytvořit ve skupině dostupnosti, abyste měli jistotu, že jsou správně distribuované napříč hardwarem. Existující virtuální počítač není možné přidat do vytvořené skupiny dostupnosti. 

Hardware ve vybraném umístění je rozdělený do několika aktualizačních domén a domén selhání. **Aktualizační doména** je skupina virtuálních počítačů a podřízeného fyzického hardwaru, který je možné současně restartovat. Virtuální počítače v jedné **doméně selhání** sdílejí společné úložiště a také zdroj napájení a síťový přepínač. 

Když vytváříte virtuální počítač příkazem [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm), pomocí parametru `-AvailabilitySetName` specifikujte název dané skupiny dostupnosti.

Nejdřív pomocí rutiny [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) nastavte uživatelské jméno a heslo správce virtuálního počítače:

```azurepowershell-interactive
$cred = Get-Credential
```

Teď ve skupině dostupně vytvořte pomocí příkazu [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) dva virtuální počítače.

```azurepowershell-interactive
for ($i=1; $i -le 2; $i++)
{
    New-AzureRmVm `
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

Parametr `-AsJob` vytvoří virtuální počítač jako úlohu na pozadí, takže budete mít k dispozici příkazový řádek PowerShellu. Podrobnosti úloh na pozadí můžete zobrazit pomocí rutiny `Job`. Vytvoření a konfigurace obou virtuálních počítačů bude trvat několik minut. Po dokončení budete mít dva virtuální počítače distribuované napříč základním hardwarem. 

Pokud se na skupinu dostupnosti podíváte na portálu v části Skupiny dostupnosti > myResourceGroupAvailability > myAvailabilitySet, měli byste vidět, jak se virtuální počítače distribuují napříč dvěma doménami selhání a aktualizačními doménami.

![Skupina dostupnosti na portálu](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Kontrola dostupných velikostí virtuálních počítačů 

Později můžete do skupiny dostupnosti přidat další virtuální počítače, potřebujete ale vědět, jaké velikosti virtuálních počítačů jsou na konkrétním hardwaru k dispozici. Pomocí příkazu [Get-AzureRMVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) získáte výpis všech dostupných velikostí na hardwarovém clusteru pro skupinu dostupnosti.

```azurepowershell-interactive
Get-AzureRmVMSize `
   -ResourceGroupName "myResourceGroupAvailability" `
   -AvailabilitySetName "myAvailabilitySet"
```

## <a name="check-azure-advisor"></a>Kontrola Azure Advisoru 

Další informace o tom, jak zlepšit dostupnost vašich virtuálních počítačů, vám nabídne i služba Azure Advisor. Azure Advisor pomáhá dodržovat osvědčené postupy pro optimalizaci nasazení Azure. Analyzuje konfiguraci vašich prostředků a telemetrii jejich využívání a následně doporučí řešení, která zlepší finanční úspornost, výkon, dostupnost a zabezpečení vašich prostředků Azure.

Přihlaste se na [Azure Portal](https://portal.azure.com), vyberte **Všechny služby** a zadejte **Advisor**. Řídicí panel služby Advisor zobrazí řešení vytvořená na míru vybranému předplatnému. Další informace najdete v tématu [Začínáme se službou Azure Advisor](../../advisor/advisor-get-started.md).


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


