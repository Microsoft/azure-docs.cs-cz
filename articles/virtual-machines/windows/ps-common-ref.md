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
ms.openlocfilehash: 7be31a9390dfb0d663b27979a42fffe6f7a0afca
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55977521"
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Běžné příkazy prostředí PowerShell pro vytváření a správa virtuálních počítačů Azure

Tento článek popisuje některé příkazy prostředí Azure PowerShell, které můžete použít k vytvoření a správa virtuálních počítačů ve vašem předplatném Azure.  Podrobnější nápovědu k určité přepínače příkazového řádku a možnosti, můžete použít **Get-Help** *příkaz*.

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

Tyto proměnné může být užitečné pro vás, pokud používá více než jeden z příkazů v tomto článku:

- $location – umístění virtuálního počítače. Můžete použít [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) k vyhledání [geografické oblasti](https://azure.microsoft.com/regions/) , který vám vyhovuje.
- $myResourceGroup – název skupiny prostředků, která obsahuje virtuální počítač.
- $myVM – název virtuálního počítače.

## <a name="create-a-vm---simplified"></a>Vytvoření virtuálního počítače – zjednodušená

| Úkol | Příkaz |
| ---- | ------- |
| Vytvoření jednoduchého virtuálního počítače | [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) -Name $myVM <BR></BR><BR></BR> Nový-AzVM obsahuje sadu *zjednodušené* parametry, kde všechny, které je nutné je jediný název. Hodnota pro – název se použije jako název pro všechny prostředky potřebné pro vytvoření nového virtuálního počítače. Můžete zadat informace, ale to je vše, co je povinný.|
| Vytvoření virtuálního počítače z vlastní image | New-AzVm -ResourceGroupName $myResourceGroup -Name $myVM ImageName "myImage" -Location $location  <BR></BR><BR></BR>Je potřeba jste již vytvořili vlastní [spravované image](capture-image-resource.md). Můžete použít obraz lze zviditelnit několik, identických virtuálních počítačů. |



## <a name="create-a-vm-configuration"></a>Vytvořte konfiguraci virtuálního počítače

| Úkol | Příkaz |
| ---- | ------- |
| Vytvořte konfiguraci virtuálního počítače |$vm = [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) -VMName $myVM -VMSize "Standard_D1_v1"<BR></BR><BR></BR>Konfigurace virtuálního počítače se používá k definování nebo aktualizovat nastavení virtuálního počítače. Konfigurace se inicializuje s názvem virtuálního počítače a jeho [velikost](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Přidat nastavení konfigurace |$vm = [Set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem) -VM $vm -Windows -ComputerName $myVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>Nastavení operačního systému, včetně [pověření](https://technet.microsoft.com/library/hh849815.aspx) jsou přidány do konfigurační objekt, který jste předtím vytvořili pomocí New-AzVMConfig. |
| Přidat síťové rozhraní |$vm = [přidat AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVMNetworkInterface) - VM $vm – Id $nic. ID<BR></BR><BR></BR>Virtuální počítač musí mít [síťové rozhraní](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pro komunikaci ve virtuální síti. Můžete také použít [Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) načíst existující objekt síťového rozhraní. |
| Zadat image platformy |$vm = [set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage) publisher_name"- VM $vm - Název_vydavatele"-nabízejí "publisher_offer" - Skus "product_sku"-"nejnovější" verze<BR></BR><BR></BR>[Informace o obrazu](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) se přidá do konfigurační objekt, který jste předtím vytvořili pomocí New-AzVMConfig. Objekt vrácený z tohoto příkazu se používá jenom při nastavení disku s operačním systémem používat image platformy. |
| Vytvoření virtuálního počítače |[New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) -ResourceGroupName $myResourceGroup -Location $location -VM $vm<BR></BR><BR></BR>Všechny prostředky vytvořené v [skupiny prostředků](../../azure-resource-manager/powershell-azure-resource-manager.md). Před spuštěním tohoto příkazu spusťte AzVMConfig nový, AzVMOperatingSystem sady, Set-AzVMSourceImage, AzVMNetworkInterface přidat a AzVMOSDisk sady. |
| Aktualizovat virtuální počítač |[Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) -ResourceGroupName $myResourceGroup -VM $vm<BR></BR><BR></BR>Získat aktuální konfiguraci virtuálního počítače pomocí rutiny Get-AzVM, změnit nastavení konfigurace v objektu VM a potom spusťte tento příkaz. |

## <a name="get-information-about-vms"></a>Získejte informace o virtuálních počítačích

| Úkol | Příkaz |
| ---- | ------- |
| Seznam virtuálních počítačů v rámci předplatného |[Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) |
| Seznam virtuálních počítačů ve skupině prostředků |Get-AzVM -ResourceGroupName $myResourceGroup<BR></BR><BR></BR>Chcete-li získat seznam skupin prostředků ve vašem předplatném, použijte [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup). |
| Získání informací o virtuálním počítači |Get-AzVM -ResourceGroupName $myResourceGroup -Name $myVM |

## <a name="manage-vms"></a>Správa virtuálních počítačů
| Úkol | Příkaz |
| --- | --- |
| Spuštění virtuálního počítače |[Start-AzVM](https://docs.microsoft.com/powershell/module/az.compute/start-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Zastavení virtuálního počítače |[Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Restartujte spuštěného virtuálního počítače |[Restart-AzVM](https://docs.microsoft.com/powershell/module/az.compute/restart-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Odstranění virtuálního počítače |[Remove-AzVM](https://docs.microsoft.com/powershell/module/az.compute/remove-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |


## <a name="next-steps"></a>Další postup
* Zobrazit základní kroky při vytváření virtuálního počítače v [vytvoření virtuálního počítače s Windows pomocí Resource Manageru a Powershellu](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

