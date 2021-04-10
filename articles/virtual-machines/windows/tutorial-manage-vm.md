---
title: Kurz – vytváření a správa virtuálních počítačů s Windows pomocí Azure PowerShell
description: V tomto kurzu zjistíte, jak pomocí Azure PowerShellu vytvářet a spravovat virtuální počítače s Windows v Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 06/06/2019
ms.author: cynthn
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: ae298562b5d3306cf6c20b4c2fb7b378ba99c986
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102561615"
---
# <a name="tutorial-create-and-manage-windows-vms-with-azure-powershell"></a>Kurz: Vytváření a správa virtuálních počítačů s Windows pomocí Azure PowerShellu

Virtuální počítače Azure poskytují plně konfigurovatelné a flexibilní výpočetní prostředí. Tento kurz se zaměřuje na základní úlohy při nasazování virtuálních počítačů Azure, jako jsou výběr velikosti virtuálního počítače, výběr image virtuálního počítače a nasazení virtuálního počítače. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření a připojení virtuálního počítače
> * Výběr a použití imagí virtuálních počítačů
> * Zobrazení a použití specifických velikostí virtuálních počítačů
> * Změna velikosti virtuálního počítače
> * Zobrazení a pochopení stavu virtuálního počítače

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Pokud chcete otevřít Cloud Shell, vyberte položku **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče tak, že přejdete na [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) .

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Skupina prostředků musí být vytvořená už před vytvořením virtuálního počítače. V následujícím příkladu se vytvoří skupina prostředků s názvem *myResourceGroupVM* v oblasti *EastUS*:

```azurepowershell-interactive
New-AzResourceGroup `
   -ResourceGroupName "myResourceGroupVM" `
   -Location "EastUS"
```

Skupinu prostředků je třeba zadat při vytváření nebo úpravách virtuálního počítače, což uvidíte dále v tomto kurzu.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

Při vytváření virtuálního počítače je k dispozici několik možností, jako jsou image operačního systému, konfigurace sítě a přihlašovací údaje pro správu. Tento příklad vytvoří virtuální počítač *myVM* s výchozí verzí Windows Serveru 2016 Datacenter.

Nastavte uživatelské jméno a heslo potřebné pro účet správce na virtuálním počítači pomocí rutiny [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Vytvořte virtuální počítač pomocí [New-AzVM](/powershell/module/az.compute/new-azvm).

```azurepowershell-interactive
New-AzVm `
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
Get-AzPublicIpAddress `
   -ResourceGroupName "myResourceGroupVM"  | Select IpAddress
```

Pomocí následujícího příkazu na svém místním počítači vytvořte s virtuálním počítačem relaci vzdálené plochy. Nahraďte IP adresu veřejnou IP adresou (*publicIPAddress*) vašeho virtuálního počítače. Po zobrazení výzvy zadejte přihlašovací údaje, které jste použili při vytváření virtuálního počítače.

```powershell
mstsc /v:<publicIpAddress>
```

V okně **Zabezpečení systému Windows** vyberte **Další možnosti** a pak **Použít jiný účet**. Zadejte uživatelské jméno a heslo, které jste vytvořili pro virtuální počítač, a pak klikněte na **OK**.

## <a name="understand-marketplace-images"></a>Principy imagí z Marketplace

Azure Marketplace obsahuje celou řadu imagí, které je možné použít k vytvoření virtuálního počítače. V předchozích krocích jsme vytvořili virtuální počítač pomocí image Windows Serveru 2016 Datacenter. V tomto kroku pomocí modulu PowerShell na webu Marketplace vyhledáme další image Windows, které je také možné použít jako základ pro nové virtuální počítače. Tento proces se skládá z vyhledání vydavatele, nabídky, skladové položky a volitelně čísla verze pro [identifikaci](cli-ps-findimage.md#terminology) image.

Pomocí příkazu [Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) vraťte seznam vydavatelů obrázků:

```azurepowershell-interactive
Get-AzVMImagePublisher -Location "EastUS"
```

Použijte [příkaz Get-AzVMImageOffer](/powershell/module/az.compute/get-azvmimageoffer) , který vrátí seznam nabídek obrázků. Při použití tohoto příkazu se ve vráceném seznamu vyfiltruje zadaný vydavatel `MicrosoftWindowsServer`:

```azurepowershell-interactive
Get-AzVMImageOffer `
   -Location "EastUS" `
   -PublisherName "MicrosoftWindowsServer"
```

Výsledky budou vypadat přibližně jako v tomto příkladu: 

```powershell
Offer             PublisherName          Location
-----             -------------          --------
Windows-HUB       MicrosoftWindowsServer EastUS
WindowsServer     MicrosoftWindowsServer EastUS
WindowsServer-HUB MicrosoftWindowsServer EastUS
```

Příkaz [Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku) pak vyfiltruje vydavatele a název nabídky a vrátí seznam názvů obrázků.

```azurepowershell-interactive
Get-AzVMImageSku `
   -Location "EastUS" `
   -PublisherName "MicrosoftWindowsServer" `
   -Offer "WindowsServer"
```

Výsledky budou vypadat přibližně jako v tomto příkladu: 

```powershell
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
New-AzVm `
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

Velikost virtuálního počítače určuje množství výpočetních prostředků, jako jsou CPU, GPU a paměť, které jsou k dispozici pro virtuální počítač. Virtuální počítače by měly být vytvořené pomocí velikosti virtuálního počítače vhodné pro zatížení. Pokud se pracovní zátěž zvýší, je také možné velikost existujícího virtuálního počítače změnit.

### <a name="vm-sizes"></a>Velikosti virtuálních počítačů

V následující tabulce jsou velikosti rozdělené podle způsobů použití.  

| Typ                     | Běžné velikosti           |    Popis       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Obecné účely](../sizes-general.md)         |B, Dsv3, Dv3, DSv2, Dv2, Av2, DC| Vyvážený poměr procesorů k paměti. Ideální pro vývoj nebo testování a pro malé až střední řešení aplikací a dat.  |
| [Optimalizované pro výpočty](../sizes-compute.md)   | Fsv2          | Vysoký poměr procesorů k paměti. Vhodné pro aplikace se středním provozem, síťová zařízení a dávkové procesy.        |
| [Optimalizované pro paměť](../sizes-memory.md)    | Esv3, Ev3, M, DSv2, Dv2  | Vysoký poměr paměti k jádrům. Velmi vhodné pro relační databáze, střední a velké mezipaměti a analýzu v paměti.                 |
| [Optimalizované pro úložiště](../sizes-storage.md)      | Lsv2, LS              | Vysoká propustnost disku a V/V. Ideální pro databáze NoSQL, SQL a velké objemy dat.                                                         |
| [GPU](../sizes-gpu.md)          | NV, NVv2, NC, NCv2, NCv3, ND            | Specializované virtuální počítače určené pro náročné vykreslování grafiky a úpravy videa.       |
| [Vysoký výkon](../sizes-hpc.md) | H        | Naše procesorově nejvýkonnější virtuální počítače s volitelnými síťovými rozhraními s vysokou propustností (RDMA). |

### <a name="find-available-vm-sizes"></a>Zjištění dostupných velikostí virtuálních počítačů

Pokud chcete zobrazit seznam velikostí virtuálních počítačů dostupných v konkrétní oblasti, použijte příkaz [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize) .

```azurepowershell-interactive
Get-AzVMSize -Location "EastUS"
```

## <a name="resize-a-vm"></a>Změna velikosti virtuálního počítače

Po nasazení virtuálního počítače můžete jeho velikost změnit, čímž se zvýší nebo sníží přidělení prostředků.

Před změnou velikosti virtuálního počítače ověřte, jestli je požadovaná velikost dostupná v aktuálním clusteru virtuálních počítačů. Příkaz [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize) vrátí seznam velikostí.

```azurepowershell-interactive
Get-AzVMSize -ResourceGroupName "myResourceGroupVM" -VMName "myVM"
```

Pokud je velikost k dispozici, je možné změnit velikost virtuálního počítače ze stavu zapnuto, ale během operace bude restartován.

```azurepowershell-interactive
$vm = Get-AzVM `
   -ResourceGroupName "myResourceGroupVM"  `
   -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_DS3_v2"
Update-AzVM `
   -VM $vm `
   -ResourceGroupName "myResourceGroupVM"
```

Pokud požadovaná velikost není v aktuálním clusteru k dispozici, je nutné uvolnit virtuální počítač, aby mohlo dojít k operaci změny velikosti. Uvolněním virtuálního počítače se odeberou všechna data na dočasném disku. Změní se také jeho veřejná IP adresa, pokud nepoužíváte statickou IP adresu.

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName "myResourceGroupVM" `
   -Name "myVM" -Force
$vm = Get-AzVM `
   -ResourceGroupName "myResourceGroupVM"  `
   -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_E2s_v3"
Update-AzVM -VM $vm `
   -ResourceGroupName "myResourceGroupVM"
Start-AzVM `
   -ResourceGroupName "myResourceGroupVM"  `
   -Name $vm.name
```

## <a name="vm-power-states"></a>Stavy napájení virtuálního počítače

Virtuální počítač Azure může mít jeden z mnoha stavů napájení. 


| Stav napájení | Description
|----|----|
| Spouštění | Virtuální počítač se právě spouští. |
| Spuštěno | Virtuální počítač je spuštěný. |
| Zastavování | Virtuální počítač se právě zastavuje. |
| Zastaveno | Virtuální počítač je zastavený. Poplatky za výpočetní výkon se účtují i za virtuální počítače v zastaveném stavu.  |
| Rušení přidělení | Probíhá navýšení virtuálního počítače. |
| Přidělení zrušeno | Označuje, že virtuální počítač je odebraný z hypervisoru, ale je stále k dispozici v rovině ovládacího prvku. Za virtuální počítače ve stavu `Deallocated` se neúčtují poplatky za výpočetní výkon. |
| - | Stav napájení virtuálního počítače je neznámý. |


Pokud chcete zjistit stav konkrétního virtuálního počítače, použijte příkaz [Get-AzVM](/powershell/module/az.compute/get-azvm) . Nezapomeňte zadat platný název virtuálního počítače a skupiny prostředků.

```azurepowershell-interactive
Get-AzVM `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Status | Select @{n="Status"; e={$_.Statuses[1].Code}}
```

Výstup bude vypadat přibližně jako v tomto příkladu:

```powershell
Status
------
PowerState/running
```

Pokud chcete načíst stav napájení všech virtuálních počítačů ve vašem předplatném, použijte [Virtual Machines – Vypíše všechna rozhraní API](/rest/api/compute/virtualmachines/listall) s parametrem **statusOnly** nastavenou na *hodnotu true*.

## <a name="management-tasks"></a>Úlohy správy

Během životního cyklu virtuálního počítače můžete chtít spustit úlohy správy, jako je spuštění, zastavení nebo odstranění virtuálního počítače. Možná také budete chtít vytvořit skripty pro automatizaci opakovaných nebo komplexních úloh. Pomocí Azure PowerShellu se dá mnoho běžných úloh správy spustit z příkazového řádku nebo ve skriptech.

### <a name="stop-a-vm"></a>Zastavení virtuálního počítače

Zastavení a zrušení přidělení virtuálního počítače pomocí [stop-AzVM](/powershell/module/az.compute/stop-azvm):

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName "myResourceGroupVM" `
   -Name "myVM" -Force
```

Pokud chcete zachovat virtuální počítač ve zřízeném stavu, použijte parametr -StayProvisioned.

### <a name="start-a-vm"></a>Spuštění virtuálního počítače

```azurepowershell-interactive
Start-AzVM `
   -ResourceGroupName "myResourceGroupVM" `
   -Name "myVM"
```

### <a name="delete-resource-group"></a>Odstranění skupiny prostředků

Při odstranění skupiny prostředků se odstraní všechno, co se nachází uvnitř skupiny prostředků.

```azurepowershell-interactive
Remove-AzResourceGroup `
   -Name "myResourceGroupVM" `
   -Force
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
> [Vytvoření a Správa disků virtuálních počítačů](./tutorial-manage-data-disk.md)