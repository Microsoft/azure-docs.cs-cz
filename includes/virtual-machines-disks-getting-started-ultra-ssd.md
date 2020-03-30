---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/14/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0d081a8cec088f4743bd0dc7d3cc37a9fade61d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80117233"
---
Ultra disky Azure nabízejí vysokou propustnost, vysoké viopy a konzistentní diskové úložiště s nízkou latencí pro virtuální počítače Azure IaaS. Tato nová nabídka poskytuje špičkový výkon na stejné úrovni dostupnosti jako naše stávající nabídky disků. Jednou z hlavních výhod ultra disků je možnost dynamicky měnit výkon SSD spolu s vašimi úlohami bez nutnosti restartování virtuálních počítačů. Disky Ultra jsou vhodné pro úlohy náročné na data, jako jsou úlohy SAP HANA, databáze nejvyšší úrovně a úlohy s velkým počtem transakcí.

## <a name="ga-scope-and-limitations"></a>Rozsah a omezení GA

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]

## <a name="determine-vm-size-and-region-availability"></a>Určení dostupnosti velikosti virtuálního počítače a oblasti

Chcete-li využít ultra disky, musíte určit, ve které zóně dostupnosti se právě nacházejíte. Ne každá oblast podporuje každou velikost virtuálního počítače s ultra disky. Chcete-li zjistit, zda velikost oblasti, zóny a virtuálního počítače podporuje ultra disky, spusťte některý z následujících příkazů, nejprve vyměňte hodnoty **oblasti**, **vmSize**a **předplatného:**

Cli:

```azurecli
$subscription = "<yourSubID>"
# example value is southeastasia
$region = "<yourLocation>"
# example value is Standard_E64s_v3
$vmSize = "<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

PowerShell:

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})[0].LocationInfo[0].ZoneDetails
```

Odpověď bude podobná níže uvedenému formuláři, kde X je zóna, která se má použít pro nasazení ve zvolené oblasti. X může být buď 1, 2 nebo 3.

Zachovat hodnotu **Zóny,** představuje zónu dostupnosti a budete ji potřebovat k nasazení ultra disku.

|ResourceType  |Name (Název)  |Umístění  |Zóny  |Omezení  |Schopnost  |Hodnota  |
|---------|---------|---------|---------|---------|---------|---------|
|Disky     |UltraSSD_LRS         |eastus2         |×         |         |         |         |

> [!NOTE]
> Pokud z příkazu nevznikla žádná odpověď, vybraná velikost virtuálního počítače není podporována ultra disky ve vybrané oblasti.

Teď, když víte, do které zóny se nasadit, postupujte podle kroků nasazení v tomto článku a nasaďte virtuální počítač s připojeným ultra diskem nebo připojte ultra disk k existujícímu virtuálnímu počítači.

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>Nasazení ultradisku pomocí Azure Resource Manageru

Nejprve určete velikost virtuálního počítače, který chcete nasadit. Seznam podporovaných velikostí virtuálních počítače najdete v tématu oddíl [oboru a omezení GA.](#ga-scope-and-limitations)

Pokud chcete vytvořit virtuální počítač s více ultra disky, podívejte se na [ukázku Vytvoření virtuálního počítače s více ultra disky](https://aka.ms/ultradiskArmTemplate).

Pokud máte v úmyslu použít vlastní šablonu, `Microsoft.Compute/Disks` ujistěte `2018-06-01` se, že **apiVersion** pro `Microsoft.Compute/virtualMachines` a je nastavena jako (nebo novější).

Nastavte sku disku na **UltraSSD_LRS**, nastavte kapacitu disku, IOPS, zónu dostupnosti a propustnost v mb/s a vytvořte ultra disk.

Po zřízení virtuálního počítače můžete rozdělit a formátovat datové disky a nakonfigurovat je pro vaše úlohy.


## <a name="deploy-an-ultra-disk-using-the-azure-portal"></a>Nasazení ultradisku pomocí portálu Azure

Tato část popisuje nasazení virtuálního počítače vybaveného ultra diskem jako datového disku. Předpokládá, že máte znalost i s nasazením virtuálního počítače, pokud tak neučiníte, podívejte se na náš [úvodní kvádžu: Vytvoření virtuálního počítače s Windows na webu Azure Portal](../articles/virtual-machines/windows/quick-create-portal.md).

- Přihlaste se k [portálu Azure](https://portal.azure.com/) a přejděte k nasazení virtuálního počítače (VM).
- Ujistěte se, že jste zvolili [podporovanou velikost virtuálního počítače a oblast](#ga-scope-and-limitations).
- Vyberte **zónu dostupnost** v **možnostech dostupnosti**.
- Vyplňte zbývající položky výběry dle vašeho výběru.
- Vyberte **Disky**.

![vytvořit-ultra-disk-enabled-vm.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk-enabled-vm.png)

- V okně Disky vyberte **možnost Ano** pro **povolení kompatibility s ultradiskem**.
- Vyberte **Vytvořit a připojte nový disk** a připojte ultra disk.

![povolit a připojit ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/enable-and-attach-ultra-disk.png)

- V okně **Vytvořit nový disk** zadejte název a vyberte Změnit **velikost**.
- Změňte **typ účtu** na Ultra **Disk**.
- Změňte hodnoty **vlastní velikostdisku (GiB),** **diskové viposlužby**a **propustnost disku** na hodnoty podle vašeho výběru.
- V obou nožích vyberte **OK.**
- Pokračujte v nasazení virtuálního počítače, bude to stejné jako u ostatních virtuálních počítače.

![vytvořit-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk.png)

## <a name="attach-an-ultra-disk-using-the-azure-portal"></a>Připojení ultradisku pomocí portálu Azure

Případně pokud váš stávající virtuální počítač je v oblasti nebo dostupnosti zóny, která je schopná používat ultra disky, můžete použít ultra disky bez nutnosti vytvářet nový virtuální počítač. Povolením ultra disků na stávajícím virtuálním počítači a jejich připojením jako datových disků.

- Přejděte na virtuální počítač a vyberte **Disky**.
- Vyberte **Upravit**.

![volby-volič-ultra-disks.png](media/virtual-machines-disks-getting-started-ultra-ssd/options-selector-ultra-disks.png)

- Chcete-li **povolit kompatibilitu s ultradiskem,** vyberte **možnost Ano** .

![ultra-možnosti-ano-enable.png](media/virtual-machines-disks-getting-started-ultra-ssd/ultra-options-yes-enable.png)

- Vyberte **Uložit**.
- Vyberte **Přidat datový disk** a v rozevíracím souboru pro **Název** vyberte **Vytvořit disk**.

![vytvořit a připojit-new-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-and-attach-new-ultra-disk.png)

- Vyplňte název nového disku a vyberte **Změnit velikost**.
- Změňte **typ účtu** na Ultra **Disk**.
- Změňte hodnoty **vlastní velikostdisku (GiB),** **diskové viposlužby**a **propustnost disku** na hodnoty podle vašeho výběru.
- Vyberte **OK** a pak vyberte **Vytvořit**.

![tvorba-a-nový-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/making-a-new-ultra-disk.png)

- Po návratu do okna disku vyberte **uložit**.

![ukládání a připojování-new-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/saving-and-attaching-new-ultra-disk.png)

### <a name="adjust-the-performance-of-an-ultra-disk-using-the-azure-portal"></a>Nastavení výkonu ultradisku pomocí portálu Azure

Ultra disky nabízejí jedinečnou schopnost, která vám umožní upravit jejich výkon. Tyto úpravy můžete provést z portálu Azure, na samotných discích.

- Přejděte na virtuální počítač a vyberte **Disky**.
- Vyberte ultra disk, který chcete změnit výkon.

![výběr-ultra-disk-to-modify.png](media/virtual-machines-disks-getting-started-ultra-ssd/selecting-ultra-disk-to-modify.png)

- Vyberte **Konfigurace** a proveďte úpravy.
- Vyberte **Uložit**.

![konfigurace souboru "ultra-disk-performance-and-size.png"](media/virtual-machines-disks-getting-started-ultra-ssd/configuring-ultra-disk-performance-and-size.png)

## <a name="deploy-an-ultra-disk-using-cli"></a>Nasazení ultradisku pomocí cli

Nejprve určete velikost virtuálního počítače, který chcete nasadit. Seznam podporovaných velikostí virtuálních počítače najdete v části [Rozsah a omezení GA.](#ga-scope-and-limitations)

Musíte vytvořit virtuální počítač, který je schopen používat ultra disky, aby bylo možné připojit ultra disk.

Nahraďte nebo nastavte proměnné **$vmname**, **$rgname**, **$diskname**, **$location**, **$password** **$user** vlastními hodnotami. Nastavte **$zone** na hodnotu zóny dostupnosti, kterou jste získali od [začátku tohoto článku](#determine-vm-size-and-region-availability). Pak spusťte následující příkaz příkazu příkazu příkazu příkazu k příkazu k vytvoření virtuálního počítače s ultra technologií:

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location
```

### <a name="create-an-ultra-disk-using-cli"></a>Vytvoření ultra disku pomocí cli

Teď, když máte virtuální počítač, který je schopný připojit ultra disky, můžete k němu vytvořit a připojit ultra disk.

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

## <a name="attach-an-ultra-disk-to-a-vm-using-cli"></a>Připojení ultra disku k virtuálnímu počítači pomocí funkce vytelatého

Případně pokud váš stávající virtuální počítač je v oblasti nebo dostupnosti zóny, která je schopná používat ultra disky, můžete použít ultra disky bez nutnosti vytvářet nový virtuální počítač.

```azurecli
$rgName = "<yourResourceGroupName>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$subscriptionId = "<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-cli"></a>Nastavení výkonu ultradisku pomocí funkce CLI

Ultra disky nabízejí jedinečnou schopnost, která vám umožní upravit jejich výkon, následující příkaz zobrazuje, jak používat tuto funkci:

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-disk-using-powershell"></a>Nasazení ultradisku pomocí PowerShellu

Nejprve určete velikost virtuálního počítače, který chcete nasadit. Seznam podporovaných velikostí virtuálních počítače najdete v části [Rozsah a omezení GA.](#ga-scope-and-limitations)

Chcete-li použít ultra disky, musíte vytvořit virtuální počítač, který je schopen používat ultra disky. Nahraďte nebo nastavte **proměnné $resourcegroup** a **$vmName** vlastními hodnotami. Nastavte **$zone** na hodnotu zóny dostupnosti, kterou jste získali od [začátku tohoto článku](#determine-vm-size-and-region-availability). Pak spusťte následující příkaz New-AzVm a vytvořte virtuální počítač s možností s možností ultra:Then run the [following New-AzVm](/powershell/module/az.compute/new-azvm) command to create an ultra enabled VM:

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

### <a name="create-an-ultra-disk-using-powershell"></a>Vytvoření ultradisku pomocí PowerShellu

Teď, když máte virtuální počítač, který je schopný používat ultra disky, můžete k němu vytvořit a připojit ultra disk:

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

## <a name="attach-an-ultra-disk-to-a-vm-using-powershell"></a>Připojení ultra disku k virtuálnímu počítači pomocí PowerShellu

Případně pokud váš stávající virtuální počítač je v oblasti nebo dostupnosti zóny, která je schopná používat ultra disky, můžete použít ultra disky bez nutnosti vytvářet nový virtuální počítač.

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

### <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>Nastavení výkonu ultradisku pomocí Prostředí PowerShell

Ultra disky mají jedinečnou schopnost, která vám umožní upravit jejich výkon, následující příkaz je příkladem, který upravuje výkon bez nutnosti odpojit disk:

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```

## <a name="next-steps"></a>Další kroky

Chcete-li vyzkoušet nový typ [disku, požádejte o přístup k tomuto průzkumu](https://aka.ms/UltraDiskSignup).
