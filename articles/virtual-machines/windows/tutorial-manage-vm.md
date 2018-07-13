---
title: Kurz – Vytváření a správa virtuálních počítačů s Windows pomocí Azure PowerShellu | Microsoft Docs
description: V tomto kurzu zjistíte, jak pomocí Azure PowerShellu vytvářet a spravovat virtuální počítače s Windows v Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/23/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: d47981042fc13a96bdf5cb9690e4dc83a6aa0162
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37932547"
---
# <a name="tutorial-create-and-manage-windows-vms-with-azure-powershell"></a>Kurz: Vytváření a správa virtuálních počítačů s Windows pomocí Azure PowerShellu

Virtuální počítače Azure poskytují plně konfigurovatelné a flexibilní výpočetní prostředí. Tento kurz se zaměřuje na základní kroky při nasazování virtuálních počítačů, jako jsou výběr velikosti virtuálního počítače, výběr image virtuálního počítače a nasazení virtuálního počítače. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření a připojení virtuálního počítače
> * Výběr a použití imagí virtuálních počítačů
> * Zobrazení a použití specifických velikostí virtuálních počítačů
> * Změna velikosti virtuálního počítače
> * Zobrazení a pochopení stavu virtuálního počítače

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte v tomto kurzu použít modul Azure PowerShell verze 5.7.0 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzureRmAccount` pro vytvoření připojení k Azure.

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). 

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Skupina prostředků musí být vytvořená už před vytvořením virtuálního počítače. V následujícím příkladu se vytvoří skupina prostředků s názvem *myResourceGroupVM* v oblasti *EastUS*:

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName "myResourceGroupVM" -Location "EastUS"
```

Skupinu prostředků je třeba zadat při vytváření nebo úpravách virtuálního počítače, což uvidíte dále v tomto kurzu.

## <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

Při vytváření virtuálního počítače je k dispozici několik možností, jako jsou image operačního systému, konfigurace sítě a přihlašovací údaje pro správu. V tomto příkladu se vytvoří virtuální počítač s názvem *myVM*, na kterém poběží výchozí nejnovější verze systému Windows Server 2016 Datacenter.

Uživatelské jméno a heslo potřebné pro účet správce na virtuálním počítači můžete nastavit pomocí příkazu [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Vytvořte virtuální počítač pomocí příkazu [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Location "EastUS" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```

## <a name="connect-to-vm"></a>Připojení k virtuálnímu počítači

Po dokončení nasazení vytvořte připojení ke vzdálené ploše virtuálního počítače.

Spuštění následujících příkazů vrátí veřejnou IP adresu virtuálního počítače. Poznamenejte si tuto IP adresu, abyste se k ní v dalším kroku mohli pomocí prohlížeče připojit a otestovat připojení k webu.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroupVM"  | Select IpAddress
```

Pomocí následujícího příkazu na svém místním počítači vytvořte s virtuálním počítačem relaci vzdálené plochy. IP adresu nahraďte veřejnou IP adresou (*publicIPAddress*) vašeho virtuálního počítače. Po zobrazení výzvy zadejte přihlašovací údaje, které jste použili při vytváření virtuálního počítače.

```powershell
mstsc /v:<publicIpAddress>
```

V okně **Zabezpečení systému Windows** vyberte **Další možnosti** a pak **Použít jiný účet**. Zadejte uživatelské jméno a heslo, které jste vytvořili pro virtuální počítač, a pak klikněte na **OK**.

## <a name="understand-vm-images"></a>Vysvětlení imagí virtuálních počítačů

Azure Marketplace obsahuje celou řadu imagí virtuálních počítačů, které jde využít k vytvoření nového virtuálního počítače. V předchozích krocích jsme vytvořili virtuální počítač pomocí image Windows Serveru 2016 Datacenter. V tomto kroku pomocí modulu PowerShell na webu Marketplace vyhledáme další image Windows, které je také možné použít jako základ pro nové virtuální počítače. Tento proces se skládá z vyhledání vydavatele, nabídky, skladové položky a volitelně čísla verze pro [identifikaci](cli-ps-findimage.md#terminology) image. 

Seznam vydavatelů imagí můžete získat pomocí příkazu [Get-AzureRmVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher):

```powershell
Get-AzureRmVMImagePublisher -Location "EastUS"
```

Seznam nabídek imagí pak můžete získat pomocí příkazu [Get-AzureRmVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer). Při použití tohoto příkazu se ve vráceném seznamu vyfiltruje zadaný vydavatel:

```azurepowershell-interactive
Get-AzureRmVMImageOffer -Location "EastUS" -PublisherName "MicrosoftWindowsServer"
```

```azurepowershell-interactive
Offer             PublisherName          Location
-----             -------------          -------- 
Windows-HUB       MicrosoftWindowsServer EastUS 
WindowsServer     MicrosoftWindowsServer EastUS   
WindowsServer-HUB MicrosoftWindowsServer EastUS   
```

Příkaz [Get-AzureRmVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) potom vyfiltruje vydavatele a název nabídky a vrátí seznam názvů imagí.

```azurepowershell-interactive
Get-AzureRmVMImageSku -Location "EastUS" -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer"
```

```azurepowershell-interactive
Skus                                      Offer         PublisherName          Location
----                                      -----         -------------          --------
2008-R2-SP1                               WindowsServer MicrosoftWindowsServer EastUS  
2008-R2-SP1-smalldisk                     WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter                        WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter-smalldisk              WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core               WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core-smalldisk     WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers           WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers-smalldisk WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-RDSH                 WindowsServer MicrosoftWindowsServer EastUS
2016-Nano-Server                          WindowsServer MicrosoftWindowsServer EastUS
```

Pomocí těchto informací můžete nasadit virtuální počítač s konkrétní imagí. Tento příklad nasadí virtuální počítač s použitím nejnovější verze image Windows Serveru 2016 s kontejnery.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM2" `
    -Location "EastUS" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress2" `
    -ImageName "MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest" `
    -Credential $cred `
    -AsJob
```

Parametr `-AsJob` vytvoří virtuální počítač jako úlohu na pozadí, takže budete mít k dispozici příkazový řádek PowerShellu. Podrobnosti úloh na pozadí můžete zobrazit pomocí rutiny `Get-Job`.


## <a name="understand-vm-sizes"></a>Vysvětlení velikostí virtuálních počítačů

Velikost virtuálního počítače určuje množství výpočetních prostředků, jako jsou procesor, grafický procesor a paměť, které jsou pro virtuální počítač k dispozici. Virtuální počítače je potřeba vytvořit s velikostí odpovídající očekávané pracovní zátěži. Pokud se pracovní zátěž zvýší, je možné velikost existujícího virtuálního počítače změnit.

### <a name="vm-sizes"></a>Velikosti virtuálních počítačů

V následující tabulce jsou velikosti rozdělené podle způsobů použití.  
| Typ                     | Běžné velikosti           |    Popis       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Obecné účely](sizes-general.md)         |Dsv3, Dv3, DSv2, Dv2, DS, D, Av2, A0-7| Vyvážený poměr procesorů k paměti. Ideální pro vývoj nebo testování a pro malé až střední řešení aplikací a dat.  |
| [Optimalizované z hlediska výpočetních služeb](sizes-compute.md)   | Fs, F             | Vysoký poměr procesorů k paměti. Vhodné pro aplikace se středním provozem, síťová zařízení a dávkové procesy.        |
| [Optimalizované z hlediska paměti](sizes-memory.md)    | Esv3, Ev3, M, GS, G, DSv2, DS, Dv2, D   | Vysoký poměr paměti k jádrům. Velmi vhodné pro relační databáze, střední a velké mezipaměti a analýzu v paměti.                 |
| [Optimalizované z hlediska úložiště](sizes-storage.md)      | Ls                | Vysoká propustnost disku a V/V. Ideální pro databáze NoSQL, SQL a velké objemy dat.                                                         |
| [GPU](sizes-gpu.md)          | NV, NC            | Specializované virtuální počítače určené pro náročné vykreslování grafiky a úpravy videa.       |
| [Vysoký výkon](sizes-hpc.md) | H, A8-11          | Naše procesorově nejvýkonnější virtuální počítače s volitelnými síťovými rozhraními s vysokou propustností (RDMA). 


### <a name="find-available-vm-sizes"></a>Zjištění dostupných velikostí virtuálních počítačů

Pokud chcete zobrazit seznam velikostí virtuálních počítačů dostupných v konkrétní oblasti, použijte příkaz [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize).

```azurepowershell-interactive
Get-AzureRmVMSize -Location "EastUS"
```

## <a name="resize-a-vm"></a>Změna velikosti virtuálního počítače

Po nasazení virtuálního počítače můžete jeho velikost změnit, čímž se zvýší nebo sníží přidělení prostředků.

Před změnou velikosti virtuálního počítače zkontrolujte, jestli je požadovaná velikost dostupná v aktuálním clusteru virtuálních počítačů. Seznam velikostí můžete získat pomocí příkazu [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize). 

```azurepowershell-interactive
Get-AzureRmVMSize -ResourceGroupName "myResourceGroupVM" -VMName "myVM"
```

Pokud je požadovaná velikost dostupná, dá se velikost virtuálního počítače změnit i za provozu, ale během této operace se bude vyžadovat restartování.

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName "myResourceGroupVM"  -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_D4"
Update-AzureRmVM -VM $vm -ResourceGroupName "myResourceGroupVM"
```

Pokud požadovaná velikost není v aktuálním clusteru, je třeba napřed zrušit přidělení virtuálního počítače, aby mohla změna velikosti proběhnout. Upozorňujeme, že až se virtuální počítač zase zapne, budou z něj odebraná všechna data na dočasném disku. Změní se také jeho veřejná IP adresa, pokud nepoužíváte statickou IP adresu. 

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName "myResourceGroupVM" -Name "myVM" -Force
$vm = Get-AzureRmVM -ResourceGroupName "myResourceGroupVM"  -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_F4s"
Update-AzureRmVM -VM $vm -ResourceGroupName "myResourceGroupVM"
Start-AzureRmVM -ResourceGroupName "myResourceGroupVM"  -Name $vm.name
```

## <a name="vm-power-states"></a>Stavy napájení virtuálního počítače

Virtuální počítač Azure může mít jeden z mnoha stavů napájení. Tento stav představuje aktuální stav virtuálního počítače z pohledu hypervisoru. 

### <a name="power-states"></a>Stavy napájení

| Stav napájení | Popis
|----|----|
| Spouštění | Označuje, že virtuální počítač se právě spouští. |
| Spuštěno | Označuje, že virtuální počítač běží (je spuštěný). |
| Zastavování | Označuje, že virtuální počítač se právě zastavuje. | 
| Zastaveno | Označuje, že virtuální počítač je zastavený. Upozorňujeme, že poplatky za výpočetní výkon se účtují i za virtuální počítače v zastaveném stavu.  |
| Rušení přidělení | Označuje, že se právě ruší přidělení virtuálního počítače. |
| Přidělení zrušeno | Označuje, že virtuální počítač je úplně odebraný z hypervisoru, ale stále je dostupný v rovině řízení. Za virtuální počítače ve stavu zrušeného přidělení se poplatky za výpočetní výkon neúčtují. |
| - | Označuje, že stav napájení virtuálního počítače není známý. |

### <a name="find-power-state"></a>Zjištění stavu napájení

Pokud chcete zjistit stav konkrétního virtuálního počítače, použijte příkaz [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm). Nezapomeňte zadat platný název virtuálního počítače a skupiny prostředků. 

```azurepowershell-interactive
Get-AzureRmVM `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Status | Select @{n="Status"; e={$_.Statuses[1].Code}}
```

Výstup:

```azurepowershell-interactive
Status
------
PowerState/running
```

## <a name="management-tasks"></a>Úlohy správy

Během životního cyklu virtuálního počítače možná budete potřebovat provádět úlohy správy, jako jsou spuštění, zastavení nebo odstranění virtuálního počítače. Možná také budete chtít vytvořit skripty pro automatizaci opakovaných nebo komplexních úloh. Pomocí Azure PowerShellu se dá mnoho běžných úloh správy spustit z příkazového řádku nebo ve skriptech.

### <a name="stop-virtual-machine"></a>Zastavení virtuálního počítače

Pokud chcete virtuální počítač zastavit a zrušit jeho přidělení, použijte příkaz [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm):

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName "myResourceGroupVM" -Name "myVM" -Force
```

Pokud chcete zachovat virtuální počítač ve stavu Zřízeno, použijte parametr -StayProvisioned.

### <a name="start-virtual-machine"></a>Spuštění virtuálního počítače

```azurepowershell-interactive
Start-AzureRmVM -ResourceGroupName "myResourceGroupVM" -Name "myVM"
```

### <a name="delete-resource-group"></a>Odstranění skupiny prostředků

Odstraněním skupiny prostředků se odstraní také všechny prostředky v ní obsažené.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroupVM" -Force
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o základních úkolech při vytváření a správě virtuálních počítačů, jako jsou:

> [!div class="checklist"]
> * Vytvoření a připojení virtuálního počítače
> * Výběr a použití imagí virtuálních počítačů
> * Zobrazení a použití specifických velikostí virtuálních počítačů
> * Změna velikosti virtuálního počítače
> * Zobrazení a pochopení stavu virtuálního počítače

V dalším kurzu se dozvíte něco o discích virtuálních počítačů.  

> [!div class="nextstepaction"]
> [Vytvoření a správa disků virtuálního počítače](./tutorial-manage-data-disk.md)
