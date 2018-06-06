---
title: Běžné příkazy prostředí PowerShell pro virtuální počítače Azure | Microsoft Docs
description: Běžné příkazy prostředí PowerShell, abyste mohli začít vytváření a správu virtuálních počítačů Windows v Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ba3839a2-f3d5-4e19-a5de-95bfb1c0e61e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: ff861c21250a042191651ab4a4cffbf3928e4f26
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34738560"
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Běžné příkazy prostředí PowerShell pro vytváření a správu virtuálních počítačů Azure

Tento článek se zabývá některé příkazy Azure PowerShell, které můžete použít k vytváření a správě virtuálních počítačů ve vašem předplatném Azure.  Další pomoc s konkrétní přepínače příkazového řádku a možnosti, můžete použít **Get-Help** *příkaz*.

Projděte si článek [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview), kde najdete informace o instalaci nejnovější verze prostředí Azure PowerShell, výběru předplatného a přihlášení k účtu.

Tyto proměnné můžou být užitečné pro vás, pokud používá více než jeden z příkazů v tomto článku:

- $location - umístění virtuálního počítače. Můžete použít [Get-AzureRmLocation](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermlocation) najít [geografické oblasti](https://azure.microsoft.com/regions/) který vám vyhovuje.
- $myResourceGroup - název skupiny prostředků, která obsahuje virtuální počítač.
- $myVM - název virtuálního počítače.

## <a name="create-a-vm---simplified"></a>Vytvoření virtuálního počítače – zjednodušená

| Úkol | Příkaz |
| ---- | ------- |
| Vytvoření jednoduchého virtuálního počítače | [Nový-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) -název $myVM <BR></BR><BR></BR> Nový-AzureRMVM obsahuje sadu *zjednodušené* parametry, kde je vyžadováno je pouze jeden název. Hodnota pro – název se použije jako název pro všechny prostředky potřebné pro vytvoření nového virtuálního počítače. Můžete zadat informace, ale to je vše, která je požadována.|
| Vytvoření virtuálního počítače z vlastní image | Nový-AzureRmVm - ResourceGroupName $myResourceGroup-název $myVM ImageName "myImage"-umístění $location  <BR></BR><BR></BR>Je třeba jste již vytvořili vlastní [spravované image](capture-image-resource.md). Chcete-li více, můžete použít bitovou kopii identické virtuální počítače. |



## <a name="create-a-vm-configuration"></a>Vytvoření konfigurace virtuálního počítače

| Úkol | Příkaz |
| ---- | ------- |
| Vytvoření konfigurace virtuálního počítače |$vm = [New-AzureRmVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig) - VMName $myVM - VMSize "Standard_D1_v1"<BR></BR><BR></BR>Konfigurace virtuálního počítače se používá k definování nebo aktualizovat nastavení pro virtuální počítač. Konfigurace je inicializován s názvem virtuálního počítače a jeho [velikost](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Přidat nastavení konfigurace |$vm = [set-AzureRmVMOperatingSystem](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) - VM $vm-Windows - ComputerName $myVM-pověření $cred - ProvisionVMAgent - EnableAutoUpdate<BR></BR><BR></BR>Nastavení operačního systému, včetně [pověření](https://technet.microsoft.com/library/hh849815.aspx) jsou přidány do objekt konfigurace, který jste dříve vytvořili pomocí New-AzureRmVMConfig. |
| Přidání rozhraní sítě |$vm = [přidat AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/Add-AzureRmVMNetworkInterface) - VM $vm-Id $ síťový adaptér. ID<BR></BR><BR></BR>Virtuální počítač musí mít [síťové rozhraní](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pro komunikaci ve virtuální síti. Můžete také použít [Get-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) načíst existující objekt rozhraní sítě. |
| Zadat image platformy |$vm = [set-AzureRmVMSourceImage](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmsourceimage) - VM $vm - PublisherName "publisher_name"-nabízejí "publisher_offer" - SKU "product_sku"-"nejnovější" verze<BR></BR><BR></BR>[Informace o obrazu](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) se přidá do objekt konfigurace, který jste dříve vytvořili pomocí New-AzureRmVMConfig. Objekt vrácený tento příkaz slouží pouze při nastavení disk operačního systému použít image platformy. |
| Vytvoření virtuálního počítače |[Nový-AzureRmVM]() - ResourceGroupName $myResourceGroup-umístění $location - VM $vm<BR></BR><BR></BR>Všechny prostředky, které jsou vytvořené v [skupiny prostředků](../../azure-resource-manager/powershell-azure-resource-manager.md). Před spuštěním tohoto příkazu, spusťte nový AzureRmVMConfig, Set-AzureRmVMOperatingSystem, Set-AzureRmVMSourceImage, AzureRmVMNetworkInterface přidat a Set-AzureRmVMOSDisk. |
| Aktualizace virtuálního počítače |[Update-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvm) - ResourceGroupName $myResourceGroup - VM $vm<BR></BR><BR></BR>Získat aktuální konfigurace virtuálního počítače pomocí Get-AzureRmVM, změňte nastavení konfigurace na objekt virtuálního počítače a poté spusťte tento příkaz. |

## <a name="get-information-about-vms"></a>Získat informace o virtuální počítače

| Úkol | Příkaz |
| ---- | ------- |
| Seznam virtuálních počítačů v předplatném. |[Get-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvm) |
| Seznam virtuálních počítačů ve skupině prostředků |Get-AzureRmVM -ResourceGroupName $myResourceGroup<BR></BR><BR></BR>Chcete-li získat seznam skupin prostředků v rámci vašeho předplatného, použijte [Get-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermresourcegroup). |
| Získání informací o virtuálním počítači |Get-AzureRmVM - ResourceGroupName $myResourceGroup-název $myVM |

## <a name="manage-vms"></a>Správa virtuálních počítačů
| Úkol | Příkaz |
| --- | --- |
| Spuštění virtuálního počítače |[Start-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/start-azurermvm) - ResourceGroupName $myResourceGroup-název $myVM |
| Zastavení virtuálního počítače |[Stop-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/stop-azurermvm) - ResourceGroupName $myResourceGroup-název $myVM |
| Restartujte spuštění virtuálního počítače |[Restart-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/restart-azurermvm) - ResourceGroupName $myResourceGroup-název $myVM |
| Odstranění virtuálního počítače |[Remove-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvm) - ResourceGroupName $myResourceGroup-název $myVM |


## <a name="next-steps"></a>Další postup
* Informace o základní kroky pro vytvoření virtuálního počítače v [vytvořit virtuální počítač s Windows pomocí Resource Manageru a prostředí PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

