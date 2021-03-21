---
title: Ultra disky pro virtuální počítače – Azure Managed disks
description: Další informace o virtuálních počítačích s Ultra pro virtuální počítače Azure
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/16/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 43dac1692dd6ee4ed1ab67a9b18ca69738e0a0f0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104580485"
---
# <a name="using-azure-ultra-disks"></a>Použití disků Azure Ultra

Tento článek vysvětluje, jak nasadit a používat disk Ultra pro koncepční informace o Ultra discích, najdete v tématu [Jaké typy disků jsou k dispozici v Azure?](disks-types.md#ultra-disk).

Disky Azure Ultra nabízejí vysokou propustnost, vysoké IOPS a konzistentní diskové úložiště s nízkou latencí pro virtuální počítače Azure s IaaS. Tato nová nabídka poskytuje horní část výkonu linky na stejné úrovni dostupnosti jako naše nabídky stávajících disků. Jednou z hlavních výhod Ultra disks je schopnost dynamicky měnit výkon jednotky SSD společně s vašimi úlohami, aniž by bylo nutné restartovat virtuální počítače. Disky Ultra jsou vhodné pro úlohy náročné na data, jako jsou úlohy SAP HANA, databáze nejvyšší úrovně a úlohy s velkým počtem transakcí.

## <a name="ga-scope-and-limitations"></a>Rozsah a omezení GA

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](../../includes/managed-disks-ultra-disks-GA-scope-and-limitations.md)]

## <a name="determine-vm-size-and-region-availability"></a>Určení velikosti virtuálního počítače a dostupnosti oblasti

### <a name="vms-using-availability-zones"></a>Virtuální počítače používající zóny dostupnosti

K využití disků Ultra je potřeba určit, kterou zónu dostupnosti máte. Ne každá oblast podporuje všechny velikosti virtuálních počítačů pomocí Ultra disks. Pokud chcete zjistit, jestli vaše oblast, zóna a velikost virtuálního počítače podporují disky Ultra, spusťte některý z následujících příkazů, abyste nejdřív nahradili hodnoty **region**, **vmSize** a **Subscription** :

#### <a name="cli"></a>Rozhraní příkazového řádku

```azurecli
subscription="<yourSubID>"
# example value is southeastasia
region="<yourLocation>"
# example value is Standard_E64s_v3
vmSize="<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

#### <a name="powershell"></a>PowerShell

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
$sku = (Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})
if($sku){$sku[0].LocationInfo[0].ZoneDetails} Else {Write-host "$vmSize is not supported with Ultra Disk in $region region"}
```

Odpověď bude podobná následujícímu formuláři, kde X je zóna, která se má použít pro nasazení ve zvolené oblasti. X může být buď 1, 2 nebo 3.

Zachovat hodnotu **zón** , představuje vaši zónu dostupnosti a Vy ji budete potřebovat k nasazení Ultra disku.

|ResourceType  |Název  |Umístění  |Zóny  |Omezení  |Schopnost  |Hodnota  |
|---------|---------|---------|---------|---------|---------|---------|
|disků     |UltraSSD_LRS         |eastus2         |×         |         |         |         |

> [!NOTE]
> Pokud se z příkazu nepoužila žádná odpověď, vybraná velikost virtuálního počítače se pro disky s Ultra ve vybrané oblasti nepodporuje.

Teď, když víte, kterou zónu nasadit do, postupujte podle kroků nasazení v tomto článku a nasaďte virtuální počítač s připojeným Ultra diskem nebo připojte Ultra disk k existujícímu virtuálnímu počítači.

### <a name="vms-with-no-redundancy-options"></a>Virtuální počítače bez možností redundance

Disky Ultra nasazené ve vybraných oblastech se musí nasazovat bez možností redundance. V této oblasti ale nemusí být všechny velikosti disků podporující Ultra disks. Chcete-li zjistit, které velikosti disků podporují disky Ultra, můžete použít některý z následujících fragmentů kódu. Nejdřív nahraďte `vmSize` `subscription` hodnoty a:

```azurecli
subscription="<yourSubID>"
region="westus"
# example value is Standard_E64s_v3
vmSize="<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].capabilities" --subscription $subscription
```

```azurepowershell
$region = "westus"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) })[0].Capabilities
```

Odpověď bude podobná následujícímu formuláři, který `UltraSSDAvailable   True` označuje, jestli velikost virtuálního počítače podporuje Ultra disků v této oblasti.

```
Name                                         Value
----                                         -----
MaxResourceVolumeMB                          884736
OSVhdSizeMB                                  1047552
vCPUs                                        64
HyperVGenerations                            V1,V2
MemoryGB                                     432
MaxDataDiskCount                             32
LowPriorityCapable                           True
PremiumIO                                    True
VMDeploymentTypes                            IaaS
vCPUsAvailable                               64
ACUs                                         160
vCPUsPerCore                                 2
CombinedTempDiskAndCachedIOPS                128000
CombinedTempDiskAndCachedReadBytesPerSecond  1073741824
CombinedTempDiskAndCachedWriteBytesPerSecond 1073741824
CachedDiskBytes                              1717986918400
UncachedDiskIOPS                             80000
UncachedDiskBytesPerSecond                   1258291200
EphemeralOSDiskSupported                     True
AcceleratedNetworkingEnabled                 True
RdmaEnabled                                  False
MaxNetworkInterfaces                         8
UltraSSDAvailable                            True
```

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>Nasazení extrémně disku pomocí Azure Resource Manager

Nejprve určete velikost virtuálního počítače k nasazení. Seznam podporovaných velikostí virtuálních počítačů najdete v části [Rozsah a omezení GA](#ga-scope-and-limitations) .

Pokud chcete vytvořit virtuální počítač s více disky Ultra, přečtěte si v ukázce [Vytvoření virtuálního počítače s několika disky Ultra](https://aka.ms/ultradiskArmTemplate).

Pokud máte v úmyslu použít vlastní šablonu, ujistěte se, že **apiVersion** pro `Microsoft.Compute/virtualMachines` a `Microsoft.Compute/Disks` je nastavená jako `2018-06-01` (nebo novější).

Nastavte SKU disku na **UltraSSD_LRS** a pak nastavte kapacitu disku, IOPS, zónu dostupnosti a propustnost v MB/s, aby se vytvořil Ultra disk.

Po zřízení virtuálního počítače můžete rozdělit a naformátovat datové disky a nakonfigurovat je pro vaše úlohy.


## <a name="deploy-an-ultra-disk"></a>Nasazení Ultra disku

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Tato část popisuje nasazení virtuálního počítače vybaveného diskem Ultra jako datový disk. Předpokládá se, že máte zkušenosti s nasazením virtuálního počítače, a pokud to neuděláte, přečtěte si náš [rychlý Start: Vytvoření virtuálního počítače s Windows v Azure Portal](./windows/quick-create-portal.md).

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) a přejděte k nasazení virtuálního počítače (VM).
1. Ujistěte se, že jste zvolili [podporovanou velikost virtuálního počítače a oblast](#ga-scope-and-limitations).
1. Vyberte **zónu dostupnosti** v **možnostech dostupnosti**.
1. Vyplňte zbývající položky vybranými možnostmi.
1. Vyberte **Disky**.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/new-ultra-vm-create.png" alt-text="Snímek obrazovky s tokem vytváření virtuálních počítačů, okno základy" lightbox="media/virtual-machines-disks-getting-started-ultra-ssd/new-ultra-vm-create.png":::

1. V okně disky vyberte **Ano** , aby se **povolila kompatibilita s Ultra diskem**.
1. Vyberte **vytvořit a připojit nový disk** a připojte Ultra disk nyní.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/new-ultra-vm-disk-enable.png" alt-text="Snímek obrazovky toku vytváření virtuálních počítačů, okna disku, Ultra je povolený a je zvýrazněný možnost vytvořit a připojit nový disk." :::

1. V okně **vytvořit nový disk** zadejte název a pak zvolte **změnit velikost**.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/new-ultra-create-disk.png" alt-text="Snímek obrazovky s oknem vytvořit nový disk se zvýrazněnou změnou velikosti":::


1. Změňte položku **SKU disku** na **Ultra disk**.
1. Změňte hodnoty položky **velikost vlastního disku (GIB)**, **IOPS disku** a **propustnosti disku** na ty podle vašeho výběru.
1. V obou oknech vyberte **OK** .

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/new-select-ultra-disk-size.png" alt-text="Snímek obrazovky okna vyberte velikost disku, Ultra disk vybraný pro typ úložiště, další hodnoty zvýrazněné.":::

1. Pokračujte s nasazením virtuálního počítače, stejně jako při nasazení jakýchkoli jiných virtuálních počítačů.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Nejprve určete velikost virtuálního počítače k nasazení. Seznam podporovaných velikostí virtuálních počítačů najdete v části [omezení a obory GA](#ga-scope-and-limitations) .

Aby bylo možné připojit disk Ultra, je nutné vytvořit virtuální počítač, který je schopný používat disky Ultra.

Nahraďte nebo nastavte **$VMName**, **$RgName**, **$DiskName**, **$Location**, **$Password**, **$User** proměnných s vašimi vlastními hodnotami. Nastavte **$Zone**  na hodnotu vaší zóny dostupnosti, kterou jste získali od [začátku tohoto článku](#determine-vm-size-and-region-availability). Pak spuštěním následujícího příkazu rozhraní příkazového řádku vytvořte virtuální počítač s povolenou podporou Ultra:

```azurecli-interactive
az disk create --subscription $subscription -n $diskname -g $rgname --size-gb 1024 --location $location --sku UltraSSD_LRS --disk-iops-read-write 8192 --disk-mbps-read-write 400
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location --attach-data-disks $diskname
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Nejprve určete velikost virtuálního počítače k nasazení. Seznam podporovaných velikostí virtuálních počítačů najdete v části [omezení a obory GA](#ga-scope-and-limitations) .

Chcete-li použít disky Ultra, je nutné vytvořit virtuální počítač schopný používat disky Ultra. Nahraďte nebo nastavte **$resourcegroup** a **$vmName** proměnných vlastními hodnotami. Nastavte **$Zone** na hodnotu vaší zóny dostupnosti, kterou jste získali od [začátku tohoto článku](#determine-vm-size-and-region-availability). Pak spuštěním následujícího příkazu [New-AzVm](/powershell/module/az.compute/new-azvm) vytvořte virtuální počítač s povolenou podporou Ultra:

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

### <a name="create-and-attach-the-disk"></a>Vytvoření a připojení disku

Po nasazení virtuálního počítače můžete k němu vytvořit a připojit disk Ultra pomocí následujícího skriptu:

```powershell
# Set parameters and select subscription
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Connect-AzAccount -SubscriptionId $subscription

# Create the disk
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
-DiskName $diskName `
-Disk $diskconfig;

# add disk to VM
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

---

## <a name="deploy-an-ultra-disk---512-byte-sector-size"></a>Nasazení velikosti sektoru bajtů Ultra disk-512

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Přihlaste se k [Azure Portal](https://portal.azure.com/)a pak vyhledejte a vyberte **disky**.
1. Vyberte **+ Nová** a vytvořte nový disk.
1. Vyberte oblast, která podporuje Ultra disky a vyberte zónu dostupnosti, vyplňte zbývající hodnoty podle vašich přání.
1. Vyberte **Změnit velikost**.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/create-managed-disk-basics-workflow.png" alt-text="Snímek obrazovky s oknem pro vytvoření disku, oblast, zónu dostupnosti a zvýrazněnou změnou velikosti":::

1. V poli **SKU disku** vyberte **Ultra disk**, vyplňte hodnoty pro požadovaný výkon a vyberte **OK**.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/select-disk-size-ultra.png" alt-text="Snímek obrazovky s vytvářením Ultra disk.":::

1. V okně **základy** vyberte kartu **Upřesnit** .
1. Vyberte **512** pro **velikost logického sektoru** a pak vyberte **zkontrolovat + vytvořit**.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/select-different-sector-size-ultra.png" alt-text="Snímek obrazovky s selektorem pro změnu velikosti logického sektoru Ultra disk na 512.":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Nejprve určete velikost virtuálního počítače k nasazení. Seznam podporovaných velikostí virtuálních počítačů najdete v části [omezení a obory GA](#ga-scope-and-limitations) .

Aby bylo možné připojit disk Ultra, je nutné vytvořit virtuální počítač, který je schopný používat disky Ultra.

Nahraďte nebo nastavte **$VMName**, **$RgName**, **$DiskName**, **$Location**, **$Password**, **$User** proměnných s vašimi vlastními hodnotami. Nastavte **$Zone**  na hodnotu vaší zóny dostupnosti, kterou jste získali od [začátku tohoto článku](#determine-vm-size-and-region-availability). Pak spuštěním následujícího příkazu rozhraní příkazového řádku vytvořte virtuální počítač s Ultra diskem, který má velikost sektoru 512 bajtů:

```azurecli
#create an ultra disk with 512 sector size
az disk create --subscription $subscription -n $diskname -g $rgname --size-gb 1024 --location $location --sku UltraSSD_LRS --disk-iops-read-write 8192 --disk-mbps-read-write 400 --logical-sector-size 512
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location --attach-data-disks $diskname
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Nejprve určete velikost virtuálního počítače k nasazení. Seznam podporovaných velikostí virtuálních počítačů najdete v části [omezení a obory GA](#ga-scope-and-limitations) .

Chcete-li použít disky Ultra, je nutné vytvořit virtuální počítač schopný používat disky Ultra. Nahraďte nebo nastavte **$resourcegroup** a **$vmName** proměnných vlastními hodnotami. Nastavte **$Zone** na hodnotu vaší zóny dostupnosti, kterou jste získali od [začátku tohoto článku](#determine-vm-size-and-region-availability). Pak spuštěním následujícího příkazu [New-AzVm](/powershell/module/az.compute/new-azvm) vytvořte virtuální počítač s povolenou podporou Ultra:

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

Pokud chcete vytvořit a připojit Ultra disk s velikostí sektoru 512 bajtů, můžete použít následující skript:

```powershell
# Set parameters and select subscription
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Connect-AzAccount -SubscriptionId $subscription

# Create the disk
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-LogicalSectorSize 512 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName $diskName `
-Disk $diskconfig;

# add disk to VM
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```
---
## <a name="attach-an-ultra-disk"></a>Připojit Ultra disk

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Případně, pokud je váš stávající virtuální počítač v oblasti oblast/dostupnosti, která je schopná používat disky Ultra, můžete využít Ultra disks bez nutnosti vytvářet nový virtuální počítač. Povolením disků Ultra na stávajícím VIRTUÁLNÍm počítači je připojte jako datové disky. Pokud chcete povolit kompatibilitu s Ultra diskem, musíte virtuální počítač zastavit. Po zastavení virtuálního počítače můžete povolit kompatibilitu a pak restartovat virtuální počítač. Po povolení kompatibility můžete připojit Ultra disk:

1. Přejděte k VIRTUÁLNÍmu počítači a Zastavte ho, počkejte, než se uvolní.
1. Až se váš virtuální počítač oddělí, vyberte **disky**.
1. Vyberte **Další nastavení**.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/new-ultra-disk-additional-settings.png" alt-text="Snímek obrazovky okna disku, zvýrazní se další nastavení.":::

1. Vyberte **Ano** , pokud chcete **Povolit kompatibilitu s Ultra diskem**.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/enable-ultra-disks-existing-vm.png" alt-text="Snímek obrazovky s povolením kompatibility s Ultra diskem":::

1. Vyberte **Uložit**.
1. Vyberte **vytvořit a připojit nový disk** a vyplňte název nového disku.
1. Jako **Typ úložiště** vyberte **Ultra disk**.
1. Změňte hodnoty **Size (GIB)**, **Max IOPS** a **Max propustnost** na ty podle vašeho výběru.
1. Až se vrátíte do okna disku, vyberte **Uložit**.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/new-create-ultra-disk-existing-vm.png" alt-text="Snímek obrazovky s oknem disku a přidáním nového disku Ultra disk.":::

1. Spusťte znovu svůj virtuální počítač.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Případně, pokud je váš stávající virtuální počítač v oblasti oblast/dostupnosti, která je schopná používat disky Ultra, můžete využít Ultra disks bez nutnosti vytvářet nový virtuální počítač.

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm---cli"></a>Povolení kompatibility Ultra disk na stávajícím virtuálním počítači – CLI

Pokud váš virtuální počítač splňuje požadavky uvedené v [oboru GA a omezeních](#ga-scope-and-limitations) a je v [příslušné zóně pro váš účet](#determine-vm-size-and-region-availability), můžete na svém virtuálním počítači povolit kompatibilitu s Ultra disks.

Pokud chcete povolit kompatibilitu s Ultra diskem, musíte virtuální počítač zastavit. Po zastavení virtuálního počítače můžete povolit kompatibilitu a pak restartovat virtuální počítač. Po povolení kompatibility můžete připojit Ultra disk:

```azurecli
az vm deallocate -n $vmName -g $rgName
az vm update -n $vmName -g $rgName --ultra-ssd-enabled true
az vm start -n $vmName -g $rgName
```

### <a name="create-an-ultra-disk---cli"></a>Vytvoření rozhraní příkazového řádku Ultra disk

Teď, když máte virtuální počítač schopný připojit disky Ultra disks, můžete k němu vytvořit a připojit disk Ultra.

```azurecli-interactive
location="eastus2"
subscription="xxx"
rgname="ultraRG"
diskname="ssd1"
vmname="ultravm1"
zone=123

#create an ultra disk
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

### <a name="attach-the-disk---cli"></a>Připojení disku – CLI

```azurecli
rgName="<yourResourceGroupName>"
vmName="<yourVMName>"
diskName="<yourDiskName>"
subscriptionId="<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Případně, pokud je váš stávající virtuální počítač v oblasti oblast/dostupnosti, která je schopná používat disky Ultra, můžete využít Ultra disks bez nutnosti vytvářet nový virtuální počítač.

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm---powershell"></a>Povolení kompatibility s Ultra disks na stávajícím virtuálním počítači – PowerShell

Pokud váš virtuální počítač splňuje požadavky uvedené v [oboru GA a omezeních](#ga-scope-and-limitations) a je v [příslušné zóně pro váš účet](#determine-vm-size-and-region-availability), můžete na svém virtuálním počítači povolit kompatibilitu s Ultra disks.

Pokud chcete povolit kompatibilitu s Ultra diskem, musíte virtuální počítač zastavit. Po zastavení virtuálního počítače můžete povolit kompatibilitu a pak restartovat virtuální počítač. Po povolení kompatibility můžete připojit Ultra disk:

```azurepowershell
#Stop the VM
Stop-AzVM -Name $vmName -ResourceGroupName $rgName
#Enable ultra disk compatibility
$vm1 = Get-AzVM -name $vmName -ResourceGroupName $rgName
Update-AzVM -ResourceGroupName $rgName -VM $vm1 -UltraSSDEnabled $True
#Start the VM
Start-AzVM -Name $vmName -ResourceGroupName $rgName
```

### <a name="create-and-attach-an-ultra-disk---powershell"></a>Vytvoření a připojení Ultra disk – PowerShell

Když teď máte virtuální počítač, který dokáže používat disky Ultra, můžete k němu vytvořit a připojit disk Ultra:

```powershell
# Set parameters and select subscription
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Connect-AzAccount -SubscriptionId $subscription

# Create the disk
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
-DiskName $diskName `
-Disk $diskconfig;

# add disk to VM
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

---
## <a name="adjust-the-performance-of-an-ultra-disk"></a>Úprava výkonu Ultra disk

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Disky Ultra nabízejí jedinečnou možnost, která umožňuje upravit jejich výkon. Tyto úpravy můžete provést z Azure Portal na samotných discích.

1. Přejděte na virtuální počítač a vyberte **disky**.
1. Vyberte Ultra disk, pro který chcete změnit výkon.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/select-ultra-disk-to-modify.png" alt-text="Snímek obrazovky s oknem disků na vašem virtuálním počítači se zvýrazní Ultra disk.":::

1. Vyberte **Velikost + výkon** a pak proveďte úpravy.
1. Vyberte **Uložit**.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/modify-ultra-disk-performance.png" alt-text="Snímek obrazovky s oknem konfigurace na disku Ultra disk, velikost disku, IOPS a propustnost jsou zvýrazněny, je zvýrazněna položka Uložit.":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Disky Ultra nabízejí jedinečnou možnost, která umožňuje upravit jejich výkon. následující příkaz znázorňuje, jak tuto funkci používat:

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>Úprava výkonu Ultra disku pomocí prostředí PowerShell

Disky Ultra mají jedinečnou schopnost, která umožňuje upravit jejich výkon. Tento příkaz je příkladem, který upravuje výkon bez nutnosti odpojení disku:

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```
---

## <a name="next-steps"></a>Další kroky

- [Použijte Azure Ultra disks ve službě Azure Kubernetes Service (Preview)](../aks/use-ultra-disks.md).
- [Migrujte disk protokolu na disk Ultra](../azure-sql/virtual-machines/windows/storage-migrate-to-ultradisk.md).
