---
title: Běžné příkazy PowerShellu pro Azure Virtual Machines
description: Běžné příkazy PowerShellu, které vám pomohou začít vytvářet a spravovat virtuální počítače v Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: 8b99b6dd62920ed17d79281b448089754613d4e1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82098404"
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Běžné příkazy PowerShellu pro vytváření a správu virtuálních počítačů Azure

Tento článek se zabývá některými Azure PowerShell příkazy, které můžete použít k vytvoření a správě virtuálních počítačů ve vašem předplatném Azure.  Podrobnější nápovědu ke konkrétním přepínačům a možnostem příkazového řádku můžete získat pomocí příkazu **Get-Help** *command*.

 

Tyto proměnné mohou být užitečné při spuštění více než jednoho z příkazů v tomto článku:

- $location – umístění virtuálního počítače. Pomocí [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) můžete najít [geografickou oblast](https://azure.microsoft.com/regions/) , která vám bude vyhovovat.
- $myResourceGroup – název skupiny prostředků, která obsahuje virtuální počítač.
- $myVM – název virtuálního počítače.

## <a name="create-a-vm---simplified"></a>Vytvoření virtuálního počítače – zjednodušené

| Úkol | Příkaz |
| ---- | ------- |
| Vytvoření jednoduchého virtuálního počítače | [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) -Name $myVM <BR></BR><BR></BR> Příkaz New-AzVM má sadu *zjednodušených* parametrů, kde všechny požadované jsou jediný název. Hodnota pro – název se použije jako název pro všechny prostředky, které jsou potřebné pro vytvoření nového virtuálního počítače. Můžete zadat víc, ale to je všechno, co je potřeba.|
| Vytvoření virtuálního počítače z vlastní image | New-AzVm-ResourceGroupName $myResourceGroup-Name $myVM ImageName "myImage" – umístění $location  <BR></BR><BR></BR>Musíte již vytvořit vlastní [spravovanou bitovou kopii](capture-image-resource.md). Image můžete použít k vytvoření několika identických virtuálních počítačů. |



## <a name="create-a-vm-configuration"></a>Vytvořit konfiguraci virtuálního počítače

| Úkol | Příkaz |
| ---- | ------- |
| Vytvořit konfiguraci virtuálního počítače |$vm = [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) -VMName $MyVM-VMSize "Standard_D1_v1"<BR></BR><BR></BR>Konfigurace virtuálního počítače se používá k definování nebo aktualizaci nastavení virtuálního počítače. Konfigurace se inicializuje s názvem virtuálního počítače a jeho [velikosti](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Přidat nastavení konfigurace |$vm = [set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem) -VM $VM-Windows-ComputerName $MyVM-Credential $Cred-ProvisionVMAgent-EnableAutoUpdate<BR></BR><BR></BR>Do objektu konfigurace, který jste dříve vytvořili pomocí New-AzVMConfig, se přidají nastavení operačního systému včetně [přihlašovacích údajů](https://technet.microsoft.com/library/hh849815.aspx) . |
| Přidat síťové rozhraní |$vm = [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVMNetworkInterface) -VM $VM-ID $nic. Účet<BR></BR><BR></BR>Virtuální počítač musí mít [síťové rozhraní](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pro komunikaci ve virtuální síti. K načtení stávajícího objektu síťového rozhraní můžete také použít [příkaz Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) . |
| Zadat image platformy |$vm = [set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage) -VM $VM-Publisher "publisher_name"-nabídky "publisher_offer"-sku "product_sku"-Version "nejnovější"<BR></BR><BR></BR>Do objektu konfigurace, který jste dříve vytvořili pomocí New-AzVMConfig, se přidají [informace o imagi](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) . Objekt vrácený z tohoto příkazu se používá jenom v případě, že jste nastavili disk s operačním systémem pro použití image platformy. |
| Vytvoření virtuálního počítače |[New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) -ResourceGroupName $MyResourceGroup-Location $Location-VM $VM<BR></BR><BR></BR>Všechny prostředky se vytvoří ve [skupině prostředků](../../azure-resource-manager/management/manage-resource-groups-powershell.md). Před spuštěním tohoto příkazu spusťte rutinu New-AzVMConfig, set-AzVMOperatingSystem, set-AzVMSourceImage, Add-AzVMNetworkInterface a set-AzVMOSDisk. |
| Aktualizace virtuálního počítače |[Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) -ResourceGroupName $MYRESOURCEGROUP-VM $VM<BR></BR><BR></BR>Získejte aktuální konfiguraci virtuálních počítačů pomocí rutiny Get-AzVM, změňte nastavení konfigurace objektu virtuálního počítače a pak spusťte tento příkaz. |

## <a name="get-information-about-vms"></a>Získat informace o virtuálních počítačích

| Úkol | Příkaz |
| ---- | ------- |
| Výpis virtuálních počítačů v předplatném |[Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) |
| Vypsat virtuální počítače ve skupině prostředků |Get-AzVM-ResourceGroupName $myResourceGroup<BR></BR><BR></BR>Pokud chcete získat seznam skupin prostředků ve vašem předplatném, použijte [příkaz Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup). |
| Získání informací o virtuálním počítači |Get-AzVM-ResourceGroupName $myResourceGroup-Name $myVM |

## <a name="manage-vms"></a>Správa virtuálních počítačů
| Úkol | Příkaz |
| --- | --- |
| Spuštění virtuálního počítače |[Start-AzVM](https://docs.microsoft.com/powershell/module/az.compute/start-azvm) -ResourceGroupName $MyResourceGroup-Name $myVM |
| Zastavení virtuálního počítače |[Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) -ResourceGroupName $MyResourceGroup-Name $myVM |
| Restartujte běžící virtuální počítač. |[Restart-AzVM](https://docs.microsoft.com/powershell/module/az.compute/restart-azvm) -ResourceGroupName $MyResourceGroup-Name $myVM |
| Odstranění virtuálního počítače |[Remove-AzVM](https://docs.microsoft.com/powershell/module/az.compute/remove-azvm) -ResourceGroupName $MyResourceGroup-Name $myVM |


## <a name="next-steps"></a>Další kroky
* Projděte si základní kroky pro vytvoření virtuálního počítače v tématu Vytvoření virtuálního počítače s [Windows pomocí Správce prostředků a PowerShellu](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

