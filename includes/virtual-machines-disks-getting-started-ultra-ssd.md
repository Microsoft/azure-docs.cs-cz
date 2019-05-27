---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/10/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 326382339e2b4aeaa488d3d7f76b7ff35f9bc620
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66147777"
---
# <a name="enable-and-deploy-azure-ultra-ssds-preview"></a>Povolit a nasadit Azure ultra SSD disky (preview)

Azure ultra SSD disky (SSD) (preview) nabídka vysoké propustnosti, vysoké IOPS a stálá nízká latence diskové úložiště pro virtuální počítače Azure IaaS (virtuální počítače). Tato nová nabídka poskytuje horní řádek výkon ve stejné úrovně dostupnosti jako naše stávající nabídky disky. Hlavní výhodou ultra SSD disků je schopnost dynamicky měnit výkon SSD společně s vašimi úlohami, aniž byste museli restartovat své virtuální počítače. Ultra SSD disky jsou vhodné pro úlohy náročné na data, jako je SAP HANA, databáze na nejvyšší úrovni a transakce náročné úlohy.

V současné době ve verzi preview jsou ultra disků SSD a vy musíte [zaregistrovat](https://aka.ms/UltraSSDPreviewSignUp) ve verzi preview, aby bylo možné přistupovat k nim.

## <a name="determine-your-availability-zone"></a>Určení zóny dostupnosti

Po schválení, budete muset určit, které zóny dostupnosti jsou in, aby bylo možné používat ultra SSD. Spusťte některý z následujících příkazů určit, které zóna v oblasti východní USA 2, ultra disku pro nasazení:

PowerShell: `Get-AzComputeResourceSku | where {$_.ResourceType -eq "disks" -and $_.Name -eq "UltraSSD_LRS" }`

CLI: `az vm list-skus --resource-type disks --query "[?name=='UltraSSD_LRS'].locationInfo"`

Odpověď bude podobná formuláři níže, kde X je pásmo má být použito pro nasazení v oblasti východní USA 2. X může být buď 1, 2 nebo 3.

Zachovat **zóny** hodnota představuje zónu dostupnosti, a budete ho potřebovat za účelem nasazení ultra SSD.

|ResourceType  |Název  |Location  |Zóny  |Omezení  |Schopnost  |Hodnota  |
|---------|---------|---------|---------|---------|---------|---------|
|disks     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

Pokud neodpověděl z příkazu, je registrace k funkci buď stále čekající na vyřízení nebo není schváleno ještě.

Teď, když znáte zóně, ve které chcete nasadit, postupujte podle kroků nasazení v tomto článku se získat první virtuální počítače nasazené pomocí ultra SSD.

## <a name="deploy-an-ultra-ssd-using-azure-resource-manager"></a>Nasazení ultra SSD pomocí Azure Resource Manageru

Nejprve určete velikost virtuálního počítače k nasazení. V rámci této verze Preview jsou podporovány pouze řady DsV3 a EsV3 virtuálního počítače. Naleznete v druhé tabulce v tomto [blogu](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) další podrobné informace o těchto velikostí virtuálních počítačů.

Pokud chcete vytvořit virtuální počítač s více ultra disků SSD, podívejte se na ukázku [vytvoření Virtuálního počítače s více ultra SSD](https://aka.ms/UltraSSDTemplate).

Pokud máte v úmyslu použít vlastní šablonu, ujistěte se, že **apiVersion** pro `Microsoft.Compute/virtualMachines` a `Microsoft.Compute/Disks` je nastaven jako `2018-06-01` (nebo novější).

Nastavte skladovou položku disku **UltraSSD_LRS**, potom nastavení kapacity disku, vstupně-výstupních operací, zóna dostupnosti a propustnost v MB/s pro vytvoření ultra disku.

Po zřízení virtuálního počítače můžete oddílu a formátování datových disků a nakonfigurovat je pro vaše úlohy.

## <a name="deploy-an-ultra-ssd-using-cli"></a>Nasazení ultra SSD pomocí rozhraní příkazového řádku

Nejprve určete velikost virtuálního počítače k nasazení. V rámci této verze Preview jsou podporovány pouze řady DsV3 a EsV3 virtuálního počítače. Naleznete v druhé tabulce v tomto [blogu](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) další podrobné informace o těchto velikostí virtuálních počítačů.

Pokud chcete použít ultra disků SSD, musíte vytvořit virtuální počítač, který podporuje použití ultra disků SSD.

Nahraďte nebo nastavit **$vmname**, **$rgname**, **$diskname**, **$location**, **$password**, **$user** proměnné s vlastními hodnotami. Nastavte **$zone** hodnotu zónu dostupnosti, které jste získali z [spuštění tohoto článku](#determine-your-availability-zone). Spusťte následující příkaz rozhraní příkazového řádku k vytvoření ultra povoleno virtuálního počítače:

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled --zone $zone --authentication-type password --admin-password $password --admin-username $user --attach-data-disks $diskname --size Standard_D4s_v3 --location $location
```

### <a name="create-an-ultra-ssd-using-cli"></a>Vytvoření ultra SSD pomocí rozhraní příkazového řádku

Teď, když máte virtuální počítač, který podporuje použití ultra disků SSD, můžete vytvořit a připojit se k němu ultra SSD.

```azurecli-interactive
location="eastus2"
subscription="xxx"
rgname="ultraRG"
diskname="ssd1"
vmname="ultravm1"
zone=123

#create an Ultra SSD disk
az disk create `
--subscription $subscription `
-n $diskname `
-g $rgname `
--size-gb 4 `
--location $location `
--zone $zone `
--sku UltraSSD_LRS `
--disk-iops-read-write 1000 `
--disk-mbps-read-write 50
```

### <a name="adjust-the-performance-of-an-ultra-ssd-using-cli"></a>Upravit úroveň výkonu ultra SSD pomocí rozhraní příkazového řádku

Ultra SSD disky nabízejí jedinečné funkce, která umožňuje upravit jejich výkon, jak používat tuto funkci znázorňuje následující příkaz:

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-ssd-using-powershell"></a>Nasazení ultra SSD pomocí prostředí PowerShell

Nejprve určete velikost virtuálního počítače k nasazení. V rámci této verze Preview jsou podporovány pouze řady DsV3 a EsV3 virtuálního počítače. Naleznete v druhé tabulce v tomto [blogu](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) další podrobné informace o těchto velikostí virtuálních počítačů.

Pokud chcete použít ultra disků SSD, musíte vytvořit virtuální počítač, který podporuje použití ultra disků SSD. Nahraďte nebo nastavit **$resourcegroup** a **$vmName** proměnné s vlastními hodnotami. Nastavte **$zone** hodnotu zónu dostupnosti, které jste získali z [spuštění tohoto článku](#determine-your-availability-zone). Potom spusťte následující příkaz [rutiny New-AzVm](/powershell/module/az.compute/new-azvm) příkazu vytvořte ultra povolené virtuálního počítače:

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD `
    -size "Standard_D4s_v3" `
    -zone $zone
```

### <a name="create-an-ultra-ssd-using-powershell"></a>Vytvoření ultra SSD pomocí prostředí PowerShell

Teď, když máte virtuální počítač, který podporuje použití ultra disků SSD, můžete vytvořit a připojit se k němu ultra SSD:

```powershell
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName 'Disk02' `
-Disk $diskconfig;
```

### <a name="adjust-the-performance-of-an-ultra-ssd-using-powershell"></a>Upravit úroveň výkonu ultra SSD pomocí prostředí PowerShell

Ultra SSD jedinečná funkce, která umožňuje upravit jejich výkon, příkaz je příklad, který přizpůsobí výkonu bez nutnosti se odpojíte příslušný disk:

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```

## <a name="next-steps"></a>Další postup

Pokud jste chtěli vyzkoušet nový typ disku [žádat o přístup k verzi preview se tohoto průzkumu](https://aka.ms/UltraSSDPreviewSignUp).