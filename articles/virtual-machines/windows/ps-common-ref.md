---
title: Běžné příkazy prostředí PowerShell pro virtuální počítače Azure | Microsoft Docs
description: Běžné příkazy prostředí PowerShell, abyste mohli začít vytváření a správu virtuálních počítačů Windows v Azure.
services: virtual-machines-windows
documentationcenter: ''
author: davidmu1
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ba3839a2-f3d5-4e19-a5de-95bfb1c0e61e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/17/2017
ms.author: davidmu
ms.openlocfilehash: f84473e73a32da43cc6cc80b21deb49ab4f3ceb9
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Běžné příkazy prostředí PowerShell pro vytváření a správu virtuálních počítačů Azure

Tento článek se zabývá některé příkazy Azure PowerShell, které můžete použít k vytváření a správě virtuálních počítačů ve vašem předplatném Azure.  Další pomoc s konkrétní přepínače příkazového řádku a možnosti, můžete použít **Get-Help** *příkaz*.

Projděte si článek [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview), kde najdete informace o instalaci nejnovější verze prostředí Azure PowerShell, výběru předplatného a přihlášení k účtu.

Tyto proměnné můžou být užitečné pro vás, pokud používá více než jeden z příkazů v tomto článku:

- $location - umístění virtuálního počítače. Můžete použít [Get-AzureRmLocation](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermlocation) najít [geografické oblasti](https://azure.microsoft.com/regions/) který vám vyhovuje.
- $myResourceGroup - název skupiny prostředků, která obsahuje virtuální počítač.
- $myVM - název virtuálního počítače.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

| Úkol | Příkaz |
| ---- | ------- |
| Vytvoření konfigurace virtuálního počítače |$vm = [New-AzureRmVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig) -VMName $myVM -VMSize "Standard_D1_v1"<BR></BR><BR></BR>Konfigurace virtuálního počítače se používá k definování nebo aktualizovat nastavení pro virtuální počítač. Konfigurace je inicializován s názvem virtuálního počítače a jeho [velikost](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Přidat nastavení konfigurace |$vm = [set-AzureRmVMOperatingSystem](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) - VM $vm-Windows - ComputerName $myVM-pověření $cred - ProvisionVMAgent - EnableAutoUpdate<BR></BR><BR></BR>Nastavení operačního systému, včetně [pověření](https://technet.microsoft.com/library/hh849815.aspx) jsou přidány do objekt konfigurace, který jste dříve vytvořili pomocí New-AzureRmVMConfig. |
| Přidání rozhraní sítě |$vm = [přidat AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/Add-AzureRmVMNetworkInterface) - VM $vm-Id $ síťový adaptér. ID<BR></BR><BR></BR>Virtuální počítač musí mít [síťové rozhraní](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pro komunikaci ve virtuální síti. Můžete také použít [Get-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) načíst existující objekt rozhraní sítě. |
| Zadat image platformy |$vm = [set-AzureRmVMSourceImage](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmsourceimage) - VM $vm - PublisherName "publisher_name"-nabízejí "publisher_offer" - SKU "product_sku"-"nejnovější" verze<BR></BR><BR></BR>[Informace o obrazu](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) se přidá do objekt konfigurace, který jste dříve vytvořili pomocí New-AzureRmVMConfig. Objekt vrácený tento příkaz slouží pouze při nastavení disk operačního systému použít image platformy. |
| Disk operačního systému sadu používat imagi platformy |$vm = [Set-AzureRmVMOSDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmosdisk) -VM $vm -Name "myOSDisk" -VhdUri "http://mystore1.blob.core.windows.net/vhds/myOSDisk.vhd" -CreateOption FromImage<BR></BR><BR></BR>Název disku operačního systému a jeho umístění v [úložiště](../../storage/common/storage-powershell-guide-full.md) se přidá do objekt konfigurace, kterou jste vytvořili. |
| Nastavit disk operačního systému použít zobecněný image |$vm = Set-AzureRmVMOSDisk -VM $vm -Name "myOSDisk" -SourceImageUri "https://mystore1.blob.core.windows.net/system/Microsoft.Compute/Images/myimages/myprefix-osDisk.{guid}.vhd" -VhdUri "https://mystore1.blob.core.windows.net/vhds/disk_name.vhd" -CreateOption FromImage -Windows<BR></BR><BR></BR>Název disku operačního systému, umístění zdrojové bitové kopie a umístění disku v [úložiště](../../storage/common/storage-powershell-guide-full.md) se přidá k objektu konfigurace. |
| Nastavit disk operačního systému použít speciální image |$vm = Set-AzureRmVMOSDisk -VM $vm -Name "myOSDisk" -VhdUri "http://mystore1.blob.core.windows.net/vhds/" -CreateOption Attach -Windows |
| Vytvoření virtuálního počítače |[Nový-AzureRmVM]() - ResourceGroupName $myResourceGroup-umístění $location - VM $vm<BR></BR><BR></BR>Všechny prostředky, které jsou vytvořené v [skupiny prostředků](../../azure-resource-manager/powershell-azure-resource-manager.md). Před spuštěním tohoto příkazu, spusťte nový AzureRmVMConfig, Set-AzureRmVMOperatingSystem, Set-AzureRmVMSourceImage, AzureRmVMNetworkInterface přidat a Set-AzureRmVMOSDisk. |

## <a name="get-information-about-vms"></a>Získat informace o virtuální počítače

| Úkol | Příkaz |
| ---- | ------- |
| Seznam virtuálních počítačů v předplatném. |[Get-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvm) |
| Seznam virtuálních počítačů ve skupině prostředků |Get-AzureRmVM -ResourceGroupName $myResourceGroup<BR></BR><BR></BR>Chcete-li získat seznam skupin prostředků v rámci vašeho předplatného, použijte [Get-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermresourcegroup). |
| Získání informací o virtuálním počítači |Get-AzureRmVM -ResourceGroupName $myResourceGroup -Name $myVM |

## <a name="manage-vms"></a>Správa virtuálních počítačů
| Úkol | Příkaz |
| --- | --- |
| Spuštění virtuálního počítače |[Start-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/start-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Zastavení virtuálního počítače |[Stop-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/stop-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Restartujte spuštění virtuálního počítače |[Restart-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/restart-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Odstranění virtuálního počítače |[Remove-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Generalize virtuálního počítače |[Set-AzureRmVm](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM -Generalized<BR></BR><BR></BR>Tento příkaz spustíte před spuštěním AzureRmVMImage uložit. |
| Zachycení virtuálního počítače |[Save-AzureRmVMImage](https://docs.microsoft.com/powershell/module/azurerm.compute/save-azurermvmimage) -ResourceGroupName $myResourceGroup -VMName $myVM -DestinationContainerName "myImageContainer" -VHDNamePrefix "myImagePrefix" -Path "C:\filepath\filename.json"<BR></BR><BR></BR>Virtuální počítač musí být [připravený, vypněte a zobecněn](prepare-for-upload-vhd-image.md) který se má použít k vytvoření image. Než spustíte tento příkaz, spusťte sadu-AzureRmVm. |
| Aktualizace virtuálního počítače |[Update-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvm) -ResourceGroupName $myResourceGroup -VM $vm<BR></BR><BR></BR>Získat aktuální konfigurace virtuálního počítače pomocí Get-AzureRmVM, změňte nastavení konfigurace na objekt virtuálního počítače a poté spusťte tento příkaz. |
| Přidání datového disku k virtuálnímu počítači |[Přidat AzureRmVMDataDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmdatadisk) - VM $vm-Name "myDataDisk" - VhdUri "https://mystore1.blob.core.windows.net/vhds/myDataDisk.vhd" - LUN #-ukládání do mezipaměti v režimu ReadWrite - DiskSizeinGB # - CreateOption prázdný<BR></BR><BR></BR>Pomocí Get-AzureRmVM získejte objekt virtuálního počítače. Zadejte číslo logické jednotky a velikost disku. Spuštění aktualizace-AzureRmVM změny konfigurace pro virtuální počítač. Disk, který přidáte není inicializován. |
| Odebrání datového disku z virtuálního počítače |[Remove-AzureRmVMDataDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvmdatadisk) -VM $vm -Name "myDataDisk"<BR></BR><BR></BR>Pomocí Get-AzureRmVM získejte objekt virtuálního počítače. Spuštění aktualizace-AzureRmVM změny konfigurace pro virtuální počítač. |
| Přidání rozšíření do virtuálního počítače |[Set-AzureRmVMExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmextension) - ResourceGroupName $myResourceGroup-umístění $location - VMName $myVM-název "Název_rozšíření"-vydavatele "publisherName"-typu "extensionType" - TypeHandlerVersion "#. #" – nastavení $Settings - ProtectedSettings $ProtectedSettings<BR></BR><BR></BR>Spusťte tento příkaz s odpovídající [informace o konfiguraci](template-description.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#extensions) pro rozšíření, které chcete nainstalovat. |
| Odebrání rozšíření virtuálního počítače |[Remove-AzureRmVMExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvmextension) -ResourceGroupName $myResourceGroup -Name "extensionName" -VMName $myVM |

## <a name="next-steps"></a>Další postup
* Informace o základní kroky pro vytvoření virtuálního počítače v [vytvořit virtuální počítač s Windows pomocí Resource Manageru a prostředí PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

