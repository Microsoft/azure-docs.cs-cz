---
title: Běžné příkazy prostředí PowerShell pro Azure Virtual Machines | Dokumentace Microsoftu
description: Běžné příkazy Powershellu, které vám pomůžou začít vytvářet a spravovat vaše virtuální počítače s Windows v Azure.
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
ms.openlocfilehash: cb9f03ab87079ba33135840e3e7599d2e8cc95e9
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/11/2018
ms.locfileid: "42054009"
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Běžné příkazy prostředí PowerShell pro vytváření a správa virtuálních počítačů Azure

Tento článek popisuje některé příkazy prostředí Azure PowerShell, které můžete použít k vytvoření a správa virtuálních počítačů ve vašem předplatném Azure.  Podrobnější nápovědu k určité přepínače příkazového řádku a možnosti, můžete použít **Get-Help** *příkaz*.

Projděte si článek [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview), kde najdete informace o instalaci nejnovější verze prostředí Azure PowerShell, výběru předplatného a přihlášení k účtu.

Tyto proměnné může být užitečné pro vás, pokud používá více než jeden z příkazů v tomto článku:

- $location – umístění virtuálního počítače. Můžete použít [Get-AzureRmLocation](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermlocation) k vyhledání [geografické oblasti](https://azure.microsoft.com/regions/) , který vám vyhovuje.
- $myResourceGroup – název skupiny prostředků, která obsahuje virtuální počítač.
- $myVM – název virtuálního počítače.

## <a name="create-a-vm---simplified"></a>Vytvoření virtuálního počítače – zjednodušená

| Úkol | Příkaz |
| ---- | ------- |
| Vytvoření jednoduchého virtuálního počítače | [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) – název $myVM <BR></BR><BR></BR> New-AzureRMVM obsahuje sadu *zjednodušené* parametry, kde všechny, které je nutné je jediný název. Hodnota pro – název se použije jako název pro všechny prostředky potřebné pro vytvoření nového virtuálního počítače. Můžete zadat informace, ale to je vše, co je povinný.|
| Vytvoření virtuálního počítače z vlastní image | New-AzureRmVm - ResourceGroupName $myResourceGroup – název ImageName $myVM "myImage"-umístění $location  <BR></BR><BR></BR>Je potřeba jste již vytvořili vlastní [spravované image](capture-image-resource.md). Můžete použít obraz lze zviditelnit několik, identických virtuálních počítačů. |



## <a name="create-a-vm-configuration"></a>Vytvořte konfiguraci virtuálního počítače

| Úkol | Příkaz |
| ---- | ------- |
| Vytvořte konfiguraci virtuálního počítače |$vm = [New-AzureRmVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig) - VMName $myVM - VMSize "Standard_D1_v1"<BR></BR><BR></BR>Konfigurace virtuálního počítače se používá k definování nebo aktualizovat nastavení virtuálního počítače. Konfigurace se inicializuje s názvem virtuálního počítače a jeho [velikost](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Přidat nastavení konfigurace |$vm = [set-AzureRmVMOperatingSystem](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) - VM $vm-Windows - ComputerName $myVM-přihlašovacích údajů $cred - ProvisionVMAgent - EnableAutoUpdate<BR></BR><BR></BR>Nastavení operačního systému, včetně [pověření](https://technet.microsoft.com/library/hh849815.aspx) jsou přidány do konfigurační objekt, který jste předtím vytvořili pomocí New-AzureRmVMConfig. |
| Přidat síťové rozhraní |$vm = [Add-AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.compute/Add-AzureRmVMNetworkInterface) - VM $vm – Id $nic. ID<BR></BR><BR></BR>Virtuální počítač musí mít [síťové rozhraní](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pro komunikaci ve virtuální síti. Můžete také použít [Get-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) načíst existující objekt síťového rozhraní. |
| Zadat image platformy |$vm = [set-AzureRmVMSourceImage](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmsourceimage) publisher_name"- VM $vm - Název_vydavatele"-nabízejí "publisher_offer" - Skus "product_sku"-"nejnovější" verze<BR></BR><BR></BR>[Informace o obrazu](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) se přidá do konfigurační objekt, který jste předtím vytvořili pomocí New-AzureRmVMConfig. Objekt vrácený z tohoto příkazu se používá jenom při nastavení disku s operačním systémem používat image platformy. |
| Vytvoření virtuálního počítače |[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) - ResourceGroupName $myResourceGroup – umístění $location - VM $vm<BR></BR><BR></BR>Všechny prostředky vytvořené v [skupiny prostředků](../../azure-resource-manager/powershell-azure-resource-manager.md). Před spuštěním tohoto příkazu spusťte New-AzureRmVMConfig, Set-AzureRmVMOperatingSystem, Set-AzureRmVMSourceImage, Add-AzureRmVMNetworkInterface a Set-AzureRmVMOSDisk. |
| Aktualizovat virtuální počítač |[Update-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvm) - ResourceGroupName $myResourceGroup - VM $vm<BR></BR><BR></BR>Získat aktuální konfiguraci virtuálního počítače pomocí Get-AzureRmVM, změnit nastavení konfigurace v objektu VM a potom spusťte tento příkaz. |

## <a name="get-information-about-vms"></a>Získejte informace o virtuálních počítačích

| Úkol | Příkaz |
| ---- | ------- |
| Seznam virtuálních počítačů v rámci předplatného |[Get-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvm) |
| Seznam virtuálních počítačů ve skupině prostředků |Get-AzureRmVM -ResourceGroupName $myResourceGroup<BR></BR><BR></BR>Chcete-li získat seznam skupin prostředků ve vašem předplatném, použijte [Get-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermresourcegroup). |
| Získání informací o virtuálním počítači |Get-AzureRmVM - ResourceGroupName $myResourceGroup – název $myVM |

## <a name="manage-vms"></a>Správa virtuálních počítačů
| Úkol | Příkaz |
| --- | --- |
| Spuštění virtuálního počítače |[Start-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/start-azurermvm) - ResourceGroupName $myResourceGroup – název $myVM |
| Zastavení virtuálního počítače |[Stop-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/stop-azurermvm) - ResourceGroupName $myResourceGroup – název $myVM |
| Restartujte spuštěného virtuálního počítače |[Restart-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/restart-azurermvm) - ResourceGroupName $myResourceGroup – název $myVM |
| Odstranění virtuálního počítače |[Remove-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvm) - ResourceGroupName $myResourceGroup – název $myVM |


## <a name="next-steps"></a>Další postup
* Zobrazit základní kroky při vytváření virtuálního počítače v [vytvoření virtuálního počítače s Windows pomocí Resource Manageru a Powershellu](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

