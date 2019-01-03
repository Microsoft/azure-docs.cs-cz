---
title: Správa virtuálních počítačů pomocí Azure Powershellu | Dokumentace Microsoftu
description: Další příkazy, které vám umožní automatizovat úkoly při správě virtuálních počítačů.
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
ms.openlocfilehash: b7fafa148417ba1667ec0277b414105f95e428ce
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53971780"
---
# <a name="manage-your-virtual-machines-by-using-azure-powershell"></a>Správa virtuálních počítačů pomocí Azure PowerShellu
> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a Classic](../../../resource-manager-deployment-model.md). Tento článek se věnuje modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Běžné příkazy prostředí PowerShell pomocí modelu Resource Manager, najdete v části [tady](../../virtual-machines-windows-ps-common-ref.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Řadu úloh, které děláte každý den museli spravovat virtuální počítače je možné automatizovat pomocí rutin prostředí Azure PowerShell. Tento článek obsahuje příklady příkazů pro jednodušší úkoly a odkazy na články, které ukazují příkazy pro složitější úlohy.

> [!NOTE]
> Pokud jste ještě nainstalovali a nakonfigurovali Azure PowerShell, ale pokyny, získáte v článku [instalace a konfigurace Azure Powershellu](/powershell/azure/overview).
> 
> 

## <a name="how-to-use-the-example-commands"></a>Jak používat příklady příkazů
Bude potřeba část textu v příkazech nahraďte text, který je vhodný pro vaše prostředí. < a > symboly označují text, je třeba nahradit. Po nahrazení textu odebrat symboly, ale ponechat uvozovek nahoře na místě.

## <a name="get-a-vm"></a>Získání virtuálního počítače
Jedná se o základní úlohu, kterou budete používat často. Použijte ho k získání informací o virtuálním počítači, provádět úlohy na virtuálním počítači nebo získat výstup pro uložení do proměnné.

Pokud chcete získat informace o virtuálním počítači, spusťte tento příkaz a nahraďte všechno, co v uvozovkách, včetně < a > znaků:

    Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

Pokud chcete ukládat výstup do proměnné $vm, spusťte:

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="log-on-to-a-windows-based-vm"></a>Přihlaste se k virtuální počítač založený na Windows
Spusťte tyto příkazy:

> [!NOTE]
> Název virtuálního počítače a cloudové služby můžete získat ze zobrazení **Get-AzureVM** příkazu.
> 
> $svcName = `"<cloud service name>"` $vmName = `"<virtual machine name>"` $localPath = `"<drive and folder location to store the downloaded RDP file, example: c:\temp >"` $localFile = $localPath + "\" $vmname +"RDP"get-AzureRemoteDesktopFile - ServiceName $svcName – název $vmName – LocalPath $localFile – spuštění
> 
> 

## <a name="stop-a-vm"></a>Zastavení virtuálního počítače
Spusťte tento příkaz:

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

> [!IMPORTANT]
> Tento parametr použijte v případě, že je poslední virtuální počítač v dané cloudové službě zachovat virtuální IP adresy (VIP) cloudové služby. <br><br> Pokud použijete parametr StayProvisioned, můžete stále účtovat virtuálního počítače.
> 
> 

## <a name="start-a-vm"></a>Spuštění virtuálního počítače
Spusťte tento příkaz:

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="attach-a-data-disk"></a>Připojení datového disku
Tato úloha vyžaduje několik kroků. Nejprve pomocí *** Add-AzureDataDisk *** rutiny disk přidat do objektu $vm. Potom použijte **Update-AzureVM** rutina pro aktualizaci konfigurace virtuálního počítače.

Musíte se také rozhodnout, jestli se má připojit nový disk, nebo disk, který obsahuje data. Pro nový disk příkaz vytvoří soubor .vhd a připojí ho.

Pokud chcete připojit nový disk, spusťte tento příkaz:

    Add-AzureDataDisk -CreateNew -DiskSizeInGB 128 -DiskLabel "<main>" -LUN <0> -VM $vm | Update-AzureVM

Pokud chcete připojit stávající datový disk, spusťte tento příkaz:

    Add-AzureDataDisk -Import -DiskName "<MyExistingDisk>" -LUN <0> | Update-AzureVM

Chcete-li připojit datové disky z existujícího souboru VHD v úložišti objektů blob, spusťte tento příkaz:

    Add-AzureDataDisk -ImportFrom -MediaLocation `
              "<https://mystorage.blob.core.windows.net/mycontainer/MyExistingDisk.vhd>" `
              -DiskLabel "<main>" -LUN <0> |
              Update-AzureVM

## <a name="create-a-windows-based-vm"></a>Vytvoření virtuálního počítače založené na Windows
K vytvoření nového virtuálního počítače založené na Windows v Azure, postupujte podle pokynů v [pomocí prostředí Azure PowerShell k vytvoření a předkonfigurace virtuálního počítače se systémem Windows](create-powershell.md). Toto téma kroky vás provedou vytvořením prostředí Azure PowerShell sady příkazů vytvoří virtuální počítač založený na Windows, která můžete předkonfigurované:

* S členství v doméně služby Active Directory.
* S další disky.
* Jako člen existující skupinu s vyrovnáváním zatížení nastavit.
* S použitím statické IP adresy.

