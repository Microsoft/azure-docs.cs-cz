---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: dfb094bc9f84e7129a3e1c733a054c5f6cd96372
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81008622"
---
Disky Azure Ultra nabízejí vysokou propustnost, vysoké IOPS a konzistentní diskové úložiště s nízkou latencí pro virtuální počítače Azure s IaaS. Tato nová nabídka poskytuje horní část výkonu linky na stejné úrovni dostupnosti jako naše nabídky stávajících disků. Jednou z hlavních výhod Ultra disks je schopnost dynamicky měnit výkon jednotky SSD společně s vašimi úlohami, aniž by bylo nutné restartovat virtuální počítače. Disky Ultra jsou vhodné pro úlohy náročné na data, jako jsou úlohy SAP HANA, databáze nejvyšší úrovně a úlohy s velkým počtem transakcí.

## <a name="ga-scope-and-limitations"></a>Rozsah a omezení GA

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]

## <a name="determine-vm-size-and-region-availability"></a>Určení velikosti virtuálního počítače a dostupnosti oblasti

### <a name="vms-using-availability-zones"></a>Virtuální počítače používající zóny dostupnosti

K využití disků Ultra je potřeba určit, kterou zónu dostupnosti máte. Ne každá oblast podporuje všechny velikosti virtuálních počítačů pomocí Ultra disks. Pokud chcete zjistit, jestli vaše oblast, zóna a velikost virtuálního počítače podporují disky Ultra, spusťte některý z následujících příkazů, abyste nejdřív nahradili hodnoty **region**, **vmSize**a **Subscription** :

#### <a name="cli"></a>Rozhraní příkazového řádku

```azurecli
$subscription = "<yourSubID>"
# example value is southeastasia
$region = "<yourLocation>"
# example value is Standard_E64s_v3
$vmSize = "<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

#### <a name="powershell"></a>PowerShell

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})[0].LocationInfo[0].ZoneDetails
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

Disky Ultra nasazené v Západní USA musí být nasazené bez možností redundance. V této oblasti ale nemusí být všechny velikosti disků podporující Ultra disks. Chcete-li zjistit, které z Západní USA podporují disky Ultra, můžete použít některý z následujících fragmentů kódu. Nejdřív nahraďte hodnoty `vmSize` a `subscription` :

```azurecli
$subscription = "<yourSubID>"
$region = "westus"
# example value is Standard_E64s_v3
$vmSize = "<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].capabilities" --subscription $subscription
```

```azurepowershell
$region = "westus"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) })[0].Capabilities
```

Odpověď bude podobná následujícímu formuláři, který označuje `UltraSSDAvailable   True` , jestli velikost virtuálního počítače podporuje Ultra disků v této oblasti.

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

Pokud máte v úmyslu použít vlastní šablonu, ujistěte se, **apiVersion** že apiVersion `Microsoft.Compute/virtualMachines` pro `Microsoft.Compute/Disks` a je nastavená jako `2018-06-01` (nebo novější).

Nastavte SKU disku na **UltraSSD_LRS**a pak nastavte kapacitu disku, IOPS, zónu dostupnosti a propustnost v MB/s, aby se vytvořil Ultra disk.

Po zřízení virtuálního počítače můžete rozdělit a naformátovat datové disky a nakonfigurovat je pro vaše úlohy.


## <a name="deploy-an-ultra-disk-using-the-azure-portal"></a>Nasazení Ultra disku pomocí Azure Portal

Tato část popisuje nasazení virtuálního počítače vybaveného diskem Ultra jako datový disk. Předpokládá se, že máte zkušenosti s nasazením virtuálního počítače, a pokud to neuděláte, přečtěte si náš [rychlý Start: Vytvoření virtuálního počítače s Windows v Azure Portal](../articles/virtual-machines/windows/quick-create-portal.md).

- Přihlaste se k [Azure Portal](https://portal.azure.com/) a přejděte k nasazení virtuálního počítače (VM).
- Ujistěte se, že jste zvolili [podporovanou velikost virtuálního počítače a oblast](#ga-scope-and-limitations).
- Vyberte **zónu dostupnosti** v **možnostech dostupnosti**.
- Vyplňte zbývající položky vybranými možnostmi.
- Vyberte **Disky**.

![Create-Ultra-disk-Enabled-VM. png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk-enabled-vm.png)

- V okně disky vyberte **Ano** , aby se **povolila kompatibilita s Ultra diskem**.
- Vyberte **vytvořit a připojit nový disk** a připojte Ultra disk nyní.

![Enable-and-Attach-Ultra-disk. png](media/virtual-machines-disks-getting-started-ultra-ssd/enable-and-attach-ultra-disk.png)

- V okně **vytvořit nový disk** zadejte název a pak zvolte **změnit velikost**.
- Změňte **typ účtu** na **Ultra disk**.
- Změňte hodnoty položky **velikost vlastního disku (GIB)**, **IOPS disku**a **propustnosti disku** na ty podle vašeho výběru.
- V obou oknech vyberte **OK** .
- Pokračujte s nasazením virtuálního počítače, stejně jako při nasazení jakýchkoli jiných virtuálních počítačů.

![Create-Ultra-disk. png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk.png)

## <a name="attach-an-ultra-disk-using-the-azure-portal"></a>Připojení disku Ultra pomocí Azure Portal

Případně, pokud je váš stávající virtuální počítač v oblasti oblast/dostupnosti, která je schopná používat disky Ultra, můžete využít Ultra disks bez nutnosti vytvářet nový virtuální počítač. Povolením disků Ultra na stávajícím VIRTUÁLNÍm počítači je připojte jako datové disky.

- Přejděte na virtuální počítač a vyberte **disky**.
- Vyberte **Upravit**.

![Options-Selector-Ultra-disks. png](media/virtual-machines-disks-getting-started-ultra-ssd/options-selector-ultra-disks.png)

- Vyberte **Ano** , pokud chcete **Povolit kompatibilitu s Ultra diskem**.

![Ultra-Options-Yes-Enable. png](media/virtual-machines-disks-getting-started-ultra-ssd/ultra-options-yes-enable.png)

- Vyberte **Uložit**.
- Vyberte **přidat datový disk** a potom v rozevíracím seznamu pro **název** vyberte **vytvořit disk**.

![Create-and-Attach-New-Ultra-disk. png](media/virtual-machines-disks-getting-started-ultra-ssd/create-and-attach-new-ultra-disk.png)

- Zadejte název nového disku a pak vyberte **změnit velikost**.
- Změňte **typ účtu** na **Ultra disk**.
- Změňte hodnoty položky **velikost vlastního disku (GIB)**, **IOPS disku**a **propustnosti disku** na ty podle vašeho výběru.
- Vyberte **OK** a pak vyberte **vytvořit**.

![Making-a-New-Ultra-disk. png](media/virtual-machines-disks-getting-started-ultra-ssd/making-a-new-ultra-disk.png)

- Až se vrátíte do okna disku, vyberte **Uložit**.

![Saving-and-Attaching-New-Ultra-disk. png](media/virtual-machines-disks-getting-started-ultra-ssd/saving-and-attaching-new-ultra-disk.png)

### <a name="adjust-the-performance-of-an-ultra-disk-using-the-azure-portal"></a>Upravte výkon disku Ultra pomocí Azure Portal

Disky Ultra nabízejí jedinečnou možnost, která umožňuje upravit jejich výkon. Tyto úpravy můžete provést z Azure Portal na samotných discích.

- Přejděte na virtuální počítač a vyberte **disky**.
- Vyberte Ultra disk, pro který chcete změnit výkon.

![Selecting-Ultra-disk-to-Modify. png](media/virtual-machines-disks-getting-started-ultra-ssd/selecting-ultra-disk-to-modify.png)

- Vyberte **Konfigurace** a potom proveďte úpravy.
- Vyberte **Uložit**.

![Configuring-Ultra-disk-Performance-and-Size. png](media/virtual-machines-disks-getting-started-ultra-ssd/configuring-ultra-disk-performance-and-size.png)

## <a name="deploy-an-ultra-disk-using-cli"></a>Nasazení Ultra disk s použitím rozhraní příkazového řádku

Nejprve určete velikost virtuálního počítače k nasazení. Seznam podporovaných velikostí virtuálních počítačů najdete v části [omezení a obory GA](#ga-scope-and-limitations) .

Aby bylo možné připojit disk Ultra, je nutné vytvořit virtuální počítač, který je schopný používat disky Ultra.

Nahraďte nebo nastavte **$VMName**, **$RgName**, **$DiskName**, **$Location**, **$Password**, **$User** proměnných s vašimi vlastními hodnotami. Nastavte **$Zone** na hodnotu vaší zóny dostupnosti, kterou jste získali od [začátku tohoto článku](#determine-vm-size-and-region-availability). Pak spuštěním následujícího příkazu rozhraní příkazového řádku vytvořte virtuální počítač s povolenou podporou Ultra:

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location
```

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm"></a>Povolení kompatibility s Ultra disks na stávajícím virtuálním počítači

Pokud váš virtuální počítač splňuje požadavky uvedené v [oboru GA a omezeních](#ga-scope-and-limitations) a je v [příslušné zóně pro váš účet](#determine-vm-size-and-region-availability), můžete na svém virtuálním počítači povolit kompatibilitu s Ultra disks.

Pokud chcete povolit kompatibilitu s Ultra diskem, musíte virtuální počítač zastavit. Po zastavení virtuálního počítače můžete povolit kompatibilitu, připojit extrémně disk a pak restartovat virtuální počítač:

```azurecli
az vm deallocate -n $vmName -g $rgName
az vm update -n $vmName -g $rgName --ultra-ssd-enabled true
az vm start -n $vmName -g $rgName
```

### <a name="create-an-ultra-disk-using-cli"></a>Vytvoření Ultra disku pomocí rozhraní příkazového řádku

Teď, když máte virtuální počítač schopný připojit disky Ultra disks, můžete k němu vytvořit a připojit disk Ultra.

```azurecli-interactive
$location="eastus2"
$subscription="xxx"
$rgname="ultraRG"
$diskname="ssd1"
$vmname="ultravm1"
$zone=123

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

## <a name="attach-an-ultra-disk-to-a-vm-using-cli"></a>Připojení disku Ultra k virtuálnímu počítači pomocí rozhraní příkazového řádku

Případně, pokud je váš stávající virtuální počítač v oblasti oblast/dostupnosti, která je schopná používat disky Ultra, můžete využít Ultra disks bez nutnosti vytvářet nový virtuální počítač.

```azurecli
$rgName = "<yourResourceGroupName>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$subscriptionId = "<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-cli"></a>Úprava výkonu Ultra disku pomocí rozhraní příkazového řádku

Disky Ultra nabízejí jedinečnou možnost, která umožňuje upravit jejich výkon. následující příkaz znázorňuje, jak tuto funkci používat:

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-disk-using-powershell"></a>Nasazení Ultra disk s využitím PowerShellu

Nejprve určete velikost virtuálního počítače k nasazení. Seznam podporovaných velikostí virtuálních počítačů najdete v části [omezení a obory GA](#ga-scope-and-limitations) .

Chcete-li použít disky Ultra, je nutné vytvořit virtuální počítač schopný používat disky Ultra. Nahraďte nebo nastavte **$resourcegroup** a **$vmName** proměnných vlastními hodnotami. Nastavte **$Zone** na hodnotu vaší zóny dostupnosti, kterou jste získali od [začátku tohoto článku](#determine-vm-size-and-region-availability). Pak spuštěním následujícího příkazu [New-AzVm](/powershell/module/az.compute/new-azvm) vytvořte virtuální počítač s povolenou podporou Ultra:

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD $true `
    -size "Standard_D4s_v3" `
    -zone $zone
```

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm"></a>Povolení kompatibility s Ultra disks na stávajícím virtuálním počítači

Pokud váš virtuální počítač splňuje požadavky uvedené v [oboru GA a omezeních](#ga-scope-and-limitations) a je v [příslušné zóně pro váš účet](#determine-vm-size-and-region-availability), můžete na svém virtuálním počítači povolit kompatibilitu s Ultra disks.

Pokud chcete povolit kompatibilitu s Ultra diskem, musíte virtuální počítač zastavit. Po zastavení virtuálního počítače můžete povolit kompatibilitu, připojit extrémně disk a pak restartovat virtuální počítač:

```azurepowershell
#stop the VM
$vm1 = Get-AzureRMVM -name $vmName -ResourceGroupName $rgName
Update-AzureRmVM -ResourceGroupName $rgName -VM $vm1 -UltraSSDEnabled 1
#start the VM
```

### <a name="create-an-ultra-disk-using-powershell"></a>Vytvoření Ultra disku pomocí prostředí PowerShell

Když teď máte virtuální počítač, který dokáže používat disky Ultra, můžete k němu vytvořit a připojit disk Ultra:

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

## <a name="attach-an-ultra-disk-to-a-vm-using-powershell"></a>Připojení disku Ultra k virtuálnímu počítači pomocí PowerShellu

Případně, pokud je váš stávající virtuální počítač v oblasti oblast/dostupnosti, která je schopná používat disky Ultra, můžete využít Ultra disks bez nutnosti vytvářet nový virtuální počítač.

```powershell
# add disk to VM
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Login-AzureRMAccount -SubscriptionId $subscription
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>Úprava výkonu Ultra disku pomocí prostředí PowerShell

Disky Ultra mají jedinečnou schopnost, která umožňuje upravit jejich výkon. Tento příkaz je příkladem, který upravuje výkon bez nutnosti odpojení disku:

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```