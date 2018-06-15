---
title: Změnit velikost virtuální počítač s Windows v modelu nasazení classic - Azure | Microsoft Docs
description: Změňte velikost virtuálního počítače s Windows vytvořené v modelu nasazení classic, pomocí Azure Powershell.
services: virtual-machines-windows
documentationcenter: ''
author: Drewm3
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: e3038215-001c-406e-904d-e0f4e326a4c7
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/19/2016
ms.author: drewm
ms.openlocfilehash: 6afc8125462a659d0892e7dbb53f1f71789815cc
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
ms.locfileid: "30913617"
---
# <a name="resize-a-windows-vm-created-in-the-classic-deployment-model"></a>Změnit velikost virtuálního počítače Windows vytvořené v modelu nasazení classic
Tento článek ukazuje, jak změnit velikost virtuálního počítače Windows, vytvořené v modelu nasazení classic pomocí Azure Powershell.

Při výběru možnosti měnit velikost virtuálního počítače, existují dvě koncepty, které řídí rozsah velikostí, které jsou k dispozici ke změně velikosti virtuálního počítače. První koncept je oblast, ve kterém je nasazený virtuální počítač. Seznam velikosti virtuálních počítačů v oblasti je na kartě služeb oblasti Azure webové stránky. Druhý koncept je fyzický hardware aktuálně hostuje virtuální počítač. Fyzické servery, které hostují virtuální počítače jsou seskupeny dohromady v clusterech běžné fyzický hardware. Pokud požadovaná velikost nového virtuálního počítače je podporováno hardwaru cluster aktuálně hostuje virtuální počítač, se liší v závislosti na metodě změna velikosti virtuálních počítačů.

> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../../../resource-manager-deployment-model.md). Tento článek se zabývá pomocí modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Můžete také [změnit velikost virtuálního počítače vytvořené v modelu nasazení Resource Manager](../resize-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="add-your-account"></a>Přidání účtu
Je nutné nakonfigurovat Azure PowerShell pro práci s klasické prostředky Azure. Postupujte podle následujících kroků a konfigurace prostředí Azure PowerShell ke správě klasické prostředky.

1. Zadejte v příkazovém prostředí PowerShell, `Add-AzureAccount` a klikněte na tlačítko **Enter**. 
2. Zadejte e-mailová adresa spojená s předplatným Azure a klikněte na tlačítko **pokračovat**. 
3. Zadejte heslo k vašemu účtu. 
4. Klikněte na tlačítko **přihlášení**. 

## <a name="resize-in-the-same-hardware-cluster"></a>Změnit velikost ve stejném clusteru hardwaru
Ke změně velikosti virtuálního počítače na velikost, která je k dispozici v hardwaru clusteru, který je hostitelem virtuálního počítače, proveďte následující kroky.

1. Spusťte následující příkaz prostředí PowerShell k zobrazení seznamu velikosti virtuálních počítačů v clusteru hardwaru hostování Cloudová služba, která obsahuje virtuální počítač k dispozici.
   
    ```powershell
    Get-AzureService | where {$_.ServiceName -eq "<cloudServiceName>"}
    ```
2. Spusťte následující příkazy ke změně velikosti virtuálního počítače.
   
    ```powershell
    Get-AzureVM -ServiceName <cloudServiceName> -Name <vmName> | Set-AzureVMSize -InstanceSize <newVMSize> | Update-AzureVM
    ```

## <a name="resize-on-a-new-hardware-cluster"></a>Změnit velikost na novém clusteru hardwaru
Ke změně velikosti virtuálního počítače na velikost není k dispozici v clusteru hardwaru hostování virtuálních počítačů cloudu služby a všechny virtuální počítače v rámci cloudové služby je nutné znovu vytvořit. Jednotlivých cloudových služeb je hostované na klastru jeden hardwaru, takže všechny virtuální počítače v rámci cloudové služby musí být velikost, která je podporována v clusteru s podporou hardwaru. Následující kroky se popisují, jak změnit velikost virtuálního počítače odstranit a potom znovu vytvořit cloudovou službu.

1. Spusťte následující příkaz prostředí PowerShell k zobrazení seznamu velikosti virtuálních počítačů k dispozici v oblasti. 
   
    ```powershell
    Get-AzureLocation | where {$_.Name -eq "<locationName>"}
    ```
2. Poznamenejte si všechna nastavení konfigurace pro každý virtuální počítač v rámci cloudové služby, který obsahuje virtuální počítač na změnu velikosti. 
3. Odstraňte všechny virtuální počítače v rámci cloudové služby, vyberte možnost zachovat disky pro každý virtuální počítač.
4. Znovu vytvořte virtuální počítač na změnu velikosti pomocí požadovaná velikost virtuálního počítače.
5. Znovu vytvořte všech ostatních virtuálních počítačů, které byly v rámci cloudové služby pomocí velikost virtuálního počítače, který je k dispozici v hardwaru clusteru nyní hostitelem cloudové služby.

Ukázkový skript pro odstranit a znovu vytvořit cloudovou službu pomocí nové velikost virtuálního počítače lze nalézt [zde](https://github.com/Azure/azure-vm-scripts). 

## <a name="next-steps"></a>Další postup
* [Změnit velikost virtuálního počítače vytvořené v modelu nasazení Resource Manager](../resize-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

