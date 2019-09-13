---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/15/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3f910a3d0466153bd60fe23ef2f9f656cac292ee
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70919661"
---
# <a name="using-azure-ultra-disks"></a>Použití disků Azure Ultra

Disky Azure Ultra nabízejí vysokou propustnost, vysoké IOPS a konzistentní diskové úložiště s nízkou latencí pro virtuální počítače Azure s IaaS. Tato nová nabídka poskytuje horní část výkonu linky na stejné úrovni dostupnosti jako naše nabídky stávajících disků. Jednou z hlavních výhod Ultra disks je schopnost dynamicky měnit výkon jednotky SSD společně s vašimi úlohami, aniž by bylo nutné restartovat virtuální počítače. Disky Ultra jsou vhodné pro úlohy náročné na data, jako jsou SAP HANA, databáze nejvyšší úrovně a zatížení náročné na transakce.

## <a name="check-if-your-subscription-has-access"></a>Ověření přístupu k předplatnému

Pokud jste se už zaregistrovali na disky Ultra a chcete zjistit, jestli je u vašeho předplatného povolené disky Ultra, použijte některý z následujících příkazů: 

CLI: `az feature show --namespace Microsoft.Compute --name UltraSSD`

PowerShell: `Get-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName UltraSSD`

Pokud je vaše předplatné povolené, váš výstup by měl vypadat přibližně takto:

```bash
{
  "id": "/subscriptions/<yoursubID>/providers/Microsoft.Features/providers/Microsoft.Compute/features/UltraSSD",
  "name": "Microsoft.Compute/UltraSSD",
  "properties": {
    "state": "Registered"
  },
  "type": "Microsoft.Features/providers/features"
}
```

## <a name="determine-your-availability-zone"></a>Určení zóny dostupnosti

Po schválení musíte určit, kterou zónu dostupnosti máte, aby bylo možné používat extrémně disky. Spusťte některý z následujících příkazů, abyste určili, na kterou zónu se má nasazovat Ultra disk, a nejdřív nahraďte hodnoty **region**, **vmSize**a **Subscription** :

CLI

```bash
$subscription = "<yourSubID>"
$region = "<yourLocation>, example value is southeastasia"
$vmSize = "<yourVMSize>, example value is Standard_E64s_v3"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

PowerShell:

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})[0].LocationInfo[0].ZoneDetails
```

Odpověď bude podobná následujícímu formuláři, kde X je zóna, která se má použít pro nasazení ve zvolené oblasti. X může být buď 1, 2 nebo 3. V současné době podporují jenom tři oblasti Ultra disks: Východní USA 2, jihovýchodní Asie a Severní Evropa.

Zachovat hodnotu **zón** , představuje vaši zónu dostupnosti a Vy ji budete potřebovat k nasazení Ultra disku.

|Typ prostředku  |Name  |Location  |Zóny  |Omezení  |Funkce  |Value  |
|---------|---------|---------|---------|---------|---------|---------|
|disks     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> Pokud nedošlo k žádné odezvě z příkazu, vaše registrace do této funkce je buď stále čeká, nebo používáte starou verzi rozhraní příkazového řádku nebo PowerShellu.

Teď, když víte, kterou zónu nasadit do, postupujte podle kroků nasazení v tomto článku a nasaďte virtuální počítač s připojeným Ultra diskem nebo připojte Ultra disk k existujícímu virtuálnímu počítači.

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>Nasazení extrémně disku pomocí Azure Resource Manager

Nejprve určete velikost virtuálního počítače k nasazení. V současné době podporují jenom rodiny virtuálních počítačů DsV3 a EsV3. Další podrobnosti o těchto velikostech virtuálních počítačů najdete v druhé tabulce na tomto [blogu](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) .

Pokud chcete vytvořit virtuální počítač s více disky Ultra, přečtěte si v ukázce [Vytvoření virtuálního počítače s několika disky Ultra](https://aka.ms/ultradiskArmTemplate).

Pokud máte v úmyslu použít vlastní šablonu, ujistěte se, že apiVersion `Microsoft.Compute/virtualMachines` pro `Microsoft.Compute/Disks` a je nastavená jako `2018-06-01` (nebo novější).

Nastavte SKU disku na **UltraSSD_LRS**a pak nastavte kapacitu disku, IOPS, zónu dostupnosti a propustnost v MB/s, aby se vytvořil Ultra disk.

Po zřízení virtuálního počítače můžete rozdělit a naformátovat datové disky a nakonfigurovat je pro vaše úlohy.

## <a name="deploy-an-ultra-disk-using-cli"></a>Nasazení Ultra disk s použitím rozhraní příkazového řádku

Nejprve určete velikost virtuálního počítače k nasazení. V současné době podporují jenom rodiny virtuálních počítačů DsV3 a EsV3. Další podrobnosti o těchto velikostech virtuálních počítačů najdete v druhé tabulce na tomto [blogu](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) .

Aby bylo možné připojit disk Ultra, je nutné vytvořit virtuální počítač, který je schopný používat disky Ultra.

Nahraďte nebo nastavte **$VMName**, **$RgName**, **$DiskName**, **$Location**, **$Password**, **$User** proměnných s vašimi vlastními hodnotami. Nastavte **$Zone** na hodnotu vaší zóny dostupnosti, kterou jste získali od [začátku tohoto článku](#determine-your-availability-zone). Pak spuštěním následujícího příkazu rozhraní příkazového řádku vytvořte virtuální počítač s povolenou podporou Ultra:

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location
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

```bash
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

Nejprve určete velikost virtuálního počítače k nasazení. V současné době podporují jenom rodiny virtuálních počítačů DsV3 a EsV3. Další podrobnosti o těchto velikostech virtuálních počítačů najdete v druhé tabulce na tomto [blogu](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) .

Chcete-li použít disky Ultra, je nutné vytvořit virtuální počítač schopný používat disky Ultra. Nahraďte nebo nastavte **$resourcegroup** a **$vmName** proměnných vlastními hodnotami. Nastavte **$Zone** na hodnotu vaší zóny dostupnosti, kterou jste získali od [začátku tohoto článku](#determine-your-availability-zone). Pak spuštěním následujícího příkazu [New-AzVm](/powershell/module/az.compute/new-azvm) vytvořte virtuální počítač s povolenou podporou Ultra:

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

## <a name="next-steps"></a>Další postup

Pokud se chcete pokusit vyzkoušet nový typ disku, který bude [vyžadovat přístup k tomuto průzkumu](https://aka.ms/UltraDiskSignup).