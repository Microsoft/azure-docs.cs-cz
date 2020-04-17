---
title: Běžné příkazy Prostředí PowerShell pro virtuální počítače Azure
description: Běžné příkazy PowerShellu, které vám pomohou začít vytvářet a spravovat virtuální počítače s Windows v Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ba3839a2-f3d5-4e19-a5de-95bfb1c0e61e
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: 3d08693b6b07b5a2f1fb40d0c4758ab43729bd76
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81456358"
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Běžné příkazy PowerShellu pro vytváření a správu virtuálních počítačů Azure

Tento článek popisuje některé příkazy Azure PowerShellu, které můžete použít k vytvoření a správě virtuálních počítačů ve vašem předplatném Azure.  Podrobnější nápovědu s konkrétními přepínači a možnostmi příkazového řádku získáte pomocí *příkazu* **Get-Help** .

 

Tyto proměnné mohou být užitečné, pokud spuštění více než jeden z příkazů v tomto článku:

- $location – umístění virtuálního počítače. [Pomocí funkce Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) můžete najít [geografickou oblast,](https://azure.microsoft.com/regions/) která vám vyhovuje.
- $myResourceGroup - Název skupiny prostředků, která obsahuje virtuální počítač.
- $myVM – název virtuálního počítače.

## <a name="create-a-vm---simplified"></a>Vytvoření virtuálního virtuálního virtuálního virtuálního <a) – zjednodušené

| Úkol | Příkaz |
| ---- | ------- |
| Vytvoření jednoduchého virtuálního virtuálního montova | [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) -Název $myVM <BR></BR><BR></BR> New-AzVM má sadu *zjednodušených* parametrů, kde vše, co je potřeba, je jeden název. Hodnota pro -Name se použije jako název pro všechny prostředky potřebné pro vytvoření nového virtuálního soudu. Můžete zadat více, ale to je vše, co je požadováno.|
| Vytvoření virtuálního počítače z vlastní image | New-AzVm -ResourceGroupName $myResourceGroup -Name $myVM ImageName "myImage" -Umístění $location  <BR></BR><BR></BR>Musíte již vytvořit vlastní [spravovanou bitové kopii](capture-image-resource.md). Pomocí obrázku můžete vytvořit více identických virtuálních počítačích. |



## <a name="create-a-vm-configuration"></a>Vytvoření konfigurace virtuálního počítače

| Úkol | Příkaz |
| ---- | ------- |
| Vytvoření konfigurace virtuálního počítače |$vm = [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) -VMName $myVM -VMSize "Standard_D1_v1"<BR></BR><BR></BR>Konfigurace virtuálního počítače se používá k definování nebo aktualizaci nastavení pro virtuální počítače. Konfigurace je inicializována s názvem virtuálního počítače a jeho [velikostí](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Přidání nastavení konfigurace |$vm = [Set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem) -VM $vm -Windows -ComputerName $myVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>Nastavení operačního systému včetně pověření jsou [přidána](https://technet.microsoft.com/library/hh849815.aspx) do konfiguračního objektu, který jste dříve vytvořili pomocí funkce New-AzVMConfig. |
| Přidání síťového rozhraní |$vm = [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVMNetworkInterface) -VM $vm -Id $nic. Id<BR></BR><BR></BR>Virtuální počítač musí mít [síťové rozhraní](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pro komunikaci ve virtuální síti. Pomocí rozhraní [Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) můžete také načíst existující objekt síťového rozhraní. |
| Určení obrazu platformy |$vm = [Set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage) -VM $vm -PublisherName "publisher_name" -Nabídka "publisher_offer" -Skus "product_sku" -Verze "nejnovější"<BR></BR><BR></BR>[Informace o obrázku](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) jsou přidány do konfiguračního objektu, který jste dříve vytvořili pomocí funkce New-AzVMConfig. Objekt vrácený z tohoto příkazu se používá pouze v případě, že nastavíte disk operačního systému tak, aby používal bitovou kopii platformy. |
| Vytvoření virtuálního počítače |[New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) -ResourceGroupName $myResourceGroup -Umístění $location -VM $vm<BR></BR><BR></BR>Všechny prostředky jsou vytvořeny ve [skupině prostředků](../../azure-resource-manager/management/manage-resource-groups-powershell.md). Před spuštěním tohoto příkazu spusťte příkazy New-AzVMConfig, Set-AzVMOperatingSystem, Set-AzVMSourceImage, Add-AzVMNetworkInterface a Set-AzVMOSDisk. |
| Aktualizace virtuálního virtuálního mísa |[Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) -ResourceGroupName $myResourceGroup -VM $vm<BR></BR><BR></BR>Získejte aktuální konfiguraci virtuálního počítače pomocí Get-AzVM, změňte nastavení konfigurace na objektu virtuálního počítače a spusťte tento příkaz. |

## <a name="get-information-about-vms"></a>Získání informací o virtuálních discích

| Úkol | Příkaz |
| ---- | ------- |
| Seznam virtuálních mitů v předplatném |[Získat-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) |
| Seznam virtuálních společností ve skupině prostředků |Get-AzVM -ResourceGroupName $myResourceGroup<BR></BR><BR></BR>Chcete-li získat seznam skupin prostředků v rámci předplatného, použijte [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup). |
| Získání informací o virtuálním počítači |Get-AzVM -ResourceGroupName $myResourceGroup -Name $myVM |

## <a name="manage-vms"></a>Správa virtuálních počítačů
| Úkol | Příkaz |
| --- | --- |
| Spuštění virtuálního počítače |[Start-AzVM](https://docs.microsoft.com/powershell/module/az.compute/start-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Zastavení virtuálního počítače |[Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Restartování spuštěného virtuálního počítače |[Restart-AzVM](https://docs.microsoft.com/powershell/module/az.compute/restart-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Odstranění virtuálního počítače |[Remove-AzVM](https://docs.microsoft.com/powershell/module/az.compute/remove-azvm) -ResourceGroupName $myResourceGroup -$myVM Name |


## <a name="next-steps"></a>Další kroky
* Podívejte se na základní kroky pro vytvoření virtuálního počítače v [tématu Vytvoření virtuálního počítače s Windows pomocí Správce prostředků a Prostředí PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

