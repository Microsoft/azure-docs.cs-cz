---
title: Kurz – Použití vlastní image virtuálního počítače ve škálovací sadě pomocí Azure PowerShellu | Microsoft Docs
description: Zjistěte, jak pomocí Azure PowerShellu vytvořit vlastní image virtuálního počítače, kterou můžete použít k nasazení škálovací sady virtuálních počítačů.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: a3b0f9b2b158bd36259ee96633682e1777333499
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55981039"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-azure-powershell"></a>Kurz: Vytvoření a použití vlastní image pro škálovací sady virtuálních počítačů pomocí Azure Powershellu

Při vytváření škálovací sady zadáte image, která se použije při nasazení instancí virtuálních počítačů. Pokud chcete snížit počet úloh po nasazení instancí virtuálních počítačů, můžete použít vlastní image virtuálního počítače. Tato vlastní image virtuálního počítače obsahuje instalace a konfigurace všech požadovaných aplikací. Všechny instance virtuálních počítačů vytvořené ve škálovací sadě používají vlastní image virtuálního počítače a jsou připravené k obsluze provozu aplikace. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření a přizpůsobení virtuálního počítače
> * Zrušení zřízení a generalizace virtuálního počítače
> * Vytvoření vlastní image virtuálního počítače ze zdrojového virtuálního počítače
> * Nasazení škálovací sady využívající vlastní image virtuálního počítače

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [updated-for-az-vm.md](../../includes/updated-for-az-vm.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]


## <a name="create-and-configure-a-source-vm"></a>Vytvoření a konfigurace zdrojového virtuálního počítače

>[!NOTE]
> Tento kurz vás provede procesem vytvoření a použití image generalizovaného virtuálního počítače. Vytvoření škálovací sady ze specializovaného virtuálního pevného disku se nepodporuje.

Nejprve vytvořte skupinu prostředků pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup), pak vytvořte virtuální počítač s [rutiny New-AzVM](/powershell/module/az.compute/new-azvm). Tento virtuální počítač se použije jako zdroj pro vlastní image virtuálního počítače. Následující příklad vytvoří virtuální počítač *myCustomVM* ve skupině prostředků *myResourceGroup*. Po zobrazení výzvy zadejte uživatelské jméno a heslo, které se použijí jako přihlašovací údaje pro virtuální počítač:

```azurepowershell-interactive
# Create a resource a group
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"

# Create a Windows Server 2016 Datacenter VM
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Name "myCustomVM" `
  -ImageName "Win2016Datacenter" `
  -OpenPorts 3389
```

Pro připojení k vašemu virtuálnímu počítači, seznam veřejnou IP adresu pomocí [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) následujícím způsobem:

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Vytvořte vzdálené připojení k virtuálnímu počítači. Pokud používáte Azure Cloud Shell, proveďte tento krok z příkazového řádku místního PowerShellu nebo klienta Vzdálené plochy. Zadejte vlastní IP adresu z předchozího příkazu. Po zobrazení výzvy zadejte přihlašovací údaje, které jste použili při vytváření virtuálního počítače v prvním kroku:

```powershell
mstsc /v:<IpAddress>
```

Teď vlastní virtuální počítač přizpůsobte nainstalováním základního webového serveru. Instance virtuálního počítače po nasazení ve škálovací sadě bude obsahovat všechny požadované balíčky ke spuštění webové aplikace. Otevřete na virtuálním počítači příkazový řádek místního PowerShellu a pomocí rutiny [Install-WindowsFeature](/powershell/module/servermanager/install-windowsfeature) nainstalujte webový server služby IIS následujícím způsobem:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Posledním krokem přípravy virtuálního počítače pro použití jako vlastní image je jeho generalizace. Sysprep odebere všechny informace o vašem osobním účtu a konfigurace a resetuje virtuální počítač do čistého stavu pro budoucí nasazení. Další informace najdete v tématu [použití nástroje Sysprep: Úvod](https://technet.microsoft.com/library/bb457073.aspx).

Pokud chcete virtuální počítač generalizovat, spusťte Sysprep a nastavte virtuální počítač do počátečního nastavení. Po dokončení dejte nástroji Sysprep pokyn k vypnutí virtuálního počítače:

```powershell
C:\Windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown
```

Až Sysprep tento proces dokončí a virtuální počítač se vypne, vzdálené připojení k virtuálnímu počítači se automaticky ukončí.


## <a name="create-a-custom-vm-image-from-the-source-vm"></a>Vytvoření vlastní image virtuálního počítače ze zdrojového virtuálního počítače
Zdrojový virtuální počítač je teď přizpůsobený a obsahuje nainstalovaný webový server služby IIS. Teď vytvoříme vlastní image virtuálního počítače pro použití se škálovací sadou.

Abyste mohli vytvořit image, virtuální počítač musí být uvolněný. Uvolněte virtuální počítač s [Stop-AzVm](/powershell/module/az.compute/stop-azvm). Potom nastavte stav virtuálního počítače za generalizovaný s [Set-AzVm](/powershell/module/az.compute/set-azvm) tak, aby Platforma Azure věděla, virtuální počítač je připravený k použití vlastní image. Image můžete vytvořit pouze z generalizovaného počítače:

```azurepowershell-interactive
Stop-AzVM -ResourceGroupName "myResourceGroup" -Name "myCustomVM" -Force
Set-AzVM -ResourceGroupName "myResourceGroup" -Name "myCustomVM" -Generalized
```

Uvolnění a generalizace virtuálního počítače může několik minut trvat.

Teď vytvořte image virtuálního počítače pomocí [New-AzImageConfig](/powershell/module/az.compute/new-azimageconfig) a [New-AzImage](/powershell/module/az.compute/new-azimage). Následující příklad z vašeho virtuálního počítače vytvoří image *myImage*:

```azurepowershell-interactive
# Get VM object
$vm = Get-AzVM -Name "myCustomVM" -ResourceGroupName "myResourceGroup"

# Create the VM image configuration based on the source VM
$image = New-AzImageConfig -Location "EastUS" -SourceVirtualMachineId $vm.ID 

# Create the custom VM image
New-AzImage -Image $image -ImageName "myImage" -ResourceGroupName "myResourceGroup"
```


## <a name="create-a-scale-set-from-the-custom-vm-image"></a>Vytvoření škálovací sady z vlastní image virtuálního počítače
Teď vytvořte škálovací sadu s [New-AzVmss](/powershell/module/az.compute/new-azvmss) , která používá `-ImageName` parametr k definování vlastní image virtuálního počítače vytvořené v předchozím kroku. Za účelem distribuce provozu do jednotlivých instancí virtuálních počítačů se vytvoří také nástroj pro vyrovnávání zatížení. Nástroj pro vyrovnávání zatížení obsahuje pravidla pro distribuci provozu na portu TCP 80, stejně jako provozu vzdálené plochy na portu TCP 3389 a vzdálené komunikace PowerShellu na portu TCP 5985. Po zobrazení výzvy zadejte požadované přihlašovací údaje pro správu instancí virtuálních počítačů ve škálovací sadě:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -ImageName "myImage"
```

Vytvoření a konfigurace všech prostředků škálovací sady a virtuálních počítačů trvá několik minut.


## <a name="test-your-scale-set"></a>Test škálovací sady
Chcete vidět svou škálovací sadu v akci, získejte veřejnou IP adresu vašeho nástroje pro vyrovnávání zatížení s [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress) následujícím způsobem:


```azurepowershell-interactive
Get-AzPublicIpAddress `
  -ResourceGroupName "myResourceGroup" `
  -Name "myPublicIPAddress" | Select IpAddress
```

Zadejte veřejnou IP adresu do svého webového prohlížeče. Zobrazí se výchozí webová stránka služby IIS, jak je znázorněno v následujícím příkladu:

![Služba IIS spuštěná z vlastní image virtuálního počítače](media/tutorial-use-custom-image-powershell/default-iis-website.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud chcete odebrat škálovací sadu a další prostředky, odstraňte skupinu prostředků a všechny její prostředky pomocí [odebrat AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). Parametr `-Force` potvrdí, že chcete prostředky odstranit, aniž by se na to zobrazoval další dotaz. Parametr `-AsJob` vrátí řízení na příkazový řádek bez čekání na dokončení operace.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Další postup
V tomto kurzu jste zjistili, jak vytvořit a použít vlastní image virtuálního počítače pro škálovací sady pomocí Azure PowerShellu:

> [!div class="checklist"]
> * Vytvoření a přizpůsobení virtuálního počítače
> * Zrušení zřízení a generalizace virtuálního počítače
> * Vytvoření vlastní image virtuálního počítače
> * Nasazení škálovací sady využívající vlastní image virtuálního počítače

V dalším kurzu se dozvíte, jak do škálovací sady nasadit aplikace.

> [!div class="nextstepaction"]
> [Nasazování aplikací do škálovacích sad](tutorial-install-apps-powershell.md)
