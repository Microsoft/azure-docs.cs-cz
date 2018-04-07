---
title: Správa virtuálních počítačů pomocí Azure PowerShell | Microsoft Docs
description: Další příkazy, které můžete použít k automatizaci úloh v správu virtuálních počítačů.
services: virtual-machines-windows
documentationcenter: windows
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 7cdf9bd3-6578-4069-8a95-e8585f04a394
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/12/2016
ms.author: kasing
ms.openlocfilehash: 942141fad09e6233efc7f850212a73f8a39c163c
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="manage-your-virtual-machines-by-using-azure-powershell"></a>Správa virtuálních počítačů pomocí Azure PowerShellu
> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../../../resource-manager-deployment-model.md). Tento článek se zabývá pomocí modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Pro běžné příkazy prostředí PowerShell pomocí modelu Resource Manager, najdete v části [zde](../../virtual-machines-windows-ps-common-ref.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Mnoho úloh, které se každý den ke správě virtuálních počítačů je možné automatizovat pomocí rutin prostředí Azure PowerShell. Tento článek vám příklady příkazů pro jednodušší úlohy a odkazy na články, které se zobrazí příkazy pro složitější úlohy.

> [!NOTE]
> Pokud ještě nainstalován a nakonfigurován prostředí Azure PowerShell ještě můžete získat pokyny v článku [postup instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).
> 
> 

## <a name="how-to-use-the-example-commands"></a>Jak používat příkazy v příkladu
Budete muset část textu v příkazech nahraďte text, který je vhodný pro vaše prostředí. < a > symboly uvést, budete muset nahraďte text. Při nahrazení textu odebrat symboly, ale ponechte uvozovek nahoře na místě.

## <a name="get-a-vm"></a>Získat virtuální počítač
Toto je základní úlohy, které budete používat často. Ji použijte k získání informací o virtuální počítač, provádějí úlohy na virtuálním počítači nebo získat výstup pro uložit jako proměnnou.

Chcete-li získat informace o virtuálním počítači, spusťte tento příkaz, nahraďte vše v uvozovkách, včetně < a > znaky:

     Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

Chcete-li uložit výstup do proměnné $vm, spusťte:

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="log-on-to-a-windows-based-vm"></a>Přihlaste se k systému Windows virtuálního počítače
Spusťte tyto příkazy:

> [!NOTE]
> Název virtuálního počítače a cloudové služby můžete získat z výstupu **Get-AzureVM** příkaz.
> 
> $svcName = "<cloud service name>" $vmName = "<virtual machine name>" $localPath = "< jednotku a složku umístění a uložte stažený soubor RDP, například: c:\temp >" $localFile = $localPath + "\" + $vmname +".rdp"Get-AzureRemoteDesktopFile - ServiceName $svcName-název $vmName - Místnícesta $localFile – spuštění
> 
> 

## <a name="stop-a-vm"></a>Zastavení virtuálního počítače
Spusťte tento příkaz:

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

> [!IMPORTANT]
> Tento parametr použijte v případě, že je poslední virtuální počítač v rámci této cloudové služby zachovat virtuální IP (VIP) cloudové služby. <br><br> Pokud použijete parametr StayProvisioned, stále platit budete pro virtuální počítač.
> 
> 

## <a name="start-a-vm"></a>Spuštění virtuálního počítače
Spusťte tento příkaz:

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="attach-a-data-disk"></a>Připojení datového disku
Tato úloha vyžaduje několik kroků. Nejprve pomocí *** Add-AzureDataDisk *** rutiny disk přidat do objektu $vm. Potom použít **aktualizace-AzureVM** rutiny se aktualizovat konfiguraci virtuálního počítače.

Musíte se také rozhodnout, jestli se má připojit nový disk, nebo disk, který obsahuje data. Příkaz pro nový disk, vytvoří soubor VHD a připojí jej.

Pokud chcete připojit nový disk, spusťte tento příkaz:

    Add-AzureDataDisk -CreateNew -DiskSizeInGB 128 -DiskLabel "<main>" -LUN <0> -VM $vm | Update-AzureVM

Pokud chcete připojit stávající datový disk, spusťte tento příkaz:

    Add-AzureDataDisk -Import -DiskName "<MyExistingDisk>" -LUN <0> | Update-AzureVM

Chcete-li datových disků připojit z existujícího souboru VHD v úložišti objektů blob, spusťte tento příkaz:

    Add-AzureDataDisk -ImportFrom -MediaLocation `
              "<https://mystorage.blob.core.windows.net/mycontainer/MyExistingDisk.vhd>" `
              -DiskLabel "<main>" -LUN <0> |
              Update-AzureVM

## <a name="create-a-windows-based-vm"></a>Vytvoření virtuálního počítače založené na systému Windows
K vytvoření nového virtuálního počítače založené na systému Windows v Azure, postupujte podle pokynů v [pomocí prostředí Azure PowerShell k vytvoření a nastavení virtuálních počítačích se systémem Windows](create-powershell.md). Tento postup téma vám pomůže vytvořit prostředí Azure PowerShell sady příkazů, který vytvoří virtuální počítač systému Windows, který může být předkonfigurované:

* S členství v doméně služby Active Directory.
* S další disky.
* Jako člen existující Vyrovnávání zatížení sítě nastavit.
* Pomocí statické IP adresy.

