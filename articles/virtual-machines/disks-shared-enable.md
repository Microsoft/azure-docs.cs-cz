---
title: Povolení sdílených disků pro Azure Managed disks
description: Nakonfigurujte Azure Managed disk se sdílenými disky, abyste ho mohli sdílet napříč několika virtuálními počítači.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/30/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: e8bb97196fcceea0c86f58fe4a63146e681c427e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96500729"
---
# <a name="enable-shared-disk"></a>Povolení sdíleného disku

Tento článek popisuje, jak povolit funkci sdílené disky pro službu Azure Managed disks. Sdílené disky Azure jsou novou funkcí pro služby Azure Managed disks, která umožňuje současně připojit spravovaný disk k několika virtuálním počítačům (VM). Připojení spravovaného disku k několika virtuálním počítačům vám umožní nasadit do Azure nové nebo migrovat existující clusterové aplikace. 

Pokud hledáte koncepční informace o spravovaných discích, které mají povolené sdílené disky, přečtěte si téma [sdílené disky Azure](disks-shared.md).

## <a name="limitations"></a>Omezení

[!INCLUDE [virtual-machines-disks-shared-limitations](../../includes/virtual-machines-disks-shared-limitations.md)]

## <a name="supported-operating-systems"></a>Podporované operační systémy

Sdílené disky podporují více operačních systémů. Podporované operační systémy najdete v částech [Windows](./disks-shared.md#windows) a [Linux](./disks-shared.md#linux) článku konceptu.

## <a name="disk-sizes"></a>Velikosti disků

[!INCLUDE [virtual-machines-disks-shared-sizes](../../includes/virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-shared-disks"></a>Nasazení sdílených disků

### <a name="deploy-a-premium-ssd-as-a-shared-disk"></a>Nasazení jednotky SSD úrovně Premium jako sdíleného disku

Pokud chcete nasadit spravovaný disk s povolenou funkcí sdíleného disku, použijte novou vlastnost `maxShares` a definujte hodnotu větší než 1. To umožňuje sdílet disk napříč několika virtuálními počítači.

> [!IMPORTANT]
> Hodnotu `maxShares` lze nastavit nebo změnit pouze v případě, že je disk odpojen ze všech virtuálních počítačů. Podívejte se na [velikosti disků](#disk-sizes) pro povolené hodnoty pro `maxShares` .

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az disk create -g myResourceGroup -n mySharedDisk --size-gb 1024 -l westcentralus --sku Premium_LRS --max-shares 2
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$dataDiskConfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType Premium_LRS -CreateOption Empty -MaxSharesCount 2

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $dataDiskConfig
```

# <a name="resource-manager-template"></a>[Šablona Správce prostředků](#tab/azure-resource-manager)

Než použijete následující šablonu, nahraďte `[parameters('dataDiskName')]` , `[resourceGroup().location]` , a `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]` vlastními hodnotami.

[Šablona sdíleného disku SSD úrovně Premium](https://aka.ms/SharedPremiumDiskARMtemplate)

---

### <a name="deploy-an-ultra-disk-as-a-shared-disk"></a>Nasazení Ultra disku jako sdíleného disku

Pokud chcete nasadit spravovaný disk s povolenou funkcí sdíleného disku, změňte `maxShares` parametr na hodnotu větší než 1. To umožňuje sdílet disk napříč několika virtuálními počítači.

> [!IMPORTANT]
> Hodnotu `maxShares` lze nastavit nebo změnit pouze v případě, že je disk odpojen ze všech virtuálních počítačů. Podívejte se na [velikosti disků](#disk-sizes) pro povolené hodnoty pro `maxShares` .


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

##### <a name="regional-disk-example"></a>Příklad místního disku

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

##### <a name="zonal-disk-example"></a>Příklad disku s oblastmi

Tento příklad je skoro stejný jako předchozí, s tím rozdílem, že vytvoří disk v zóně dostupnosti 1.

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1 --zone 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

##### <a name="regional-disk-example"></a>Příklad místního disku

```azurepowershell-interactive
$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1 -MaxSharesCount 5

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig
```

##### <a name="zonal-disk-example"></a>Příklad disku s oblastmi

Tento příklad je skoro stejný jako předchozí, s tím rozdílem, že vytvoří disk v zóně dostupnosti 1.

```azurepowershell-interactive
$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1 -MaxSharesCount 5 -Zone 1

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig
```

# <a name="resource-manager-template"></a>[Šablona Správce prostředků](#tab/azure-resource-manager)

##### <a name="regional-disk-example"></a>Příklad místního disku

Než použijete následující šablonu, nahraďte `[parameters('dataDiskName')]` , `[resourceGroup().location]` , `[parameters('dataDiskSizeGB')]` , `[parameters('maxShares')]` , `[parameters('diskIOPSReadWrite')]` , `[parameters('diskMBpsReadWrite')]` , `[parameters('diskIOPSReadOnly')]` a `[parameters('diskMBpsReadOnly')]` vlastními hodnotami.

[Šablona pro místní sdílený Ultra disks](https://aka.ms/SharedUltraDiskARMtemplateRegional)

##### <a name="zonal-disk-example"></a>Příklad disku s oblastmi

Než použijete následující šablonu, nahraďte `[parameters('dataDiskName')]` , `[resourceGroup().location]` , `[parameters('dataDiskSizeGB')]` , `[parameters('maxShares')]` , `[parameters('diskIOPSReadWrite')]` , `[parameters('diskMBpsReadWrite')]` , `[parameters('diskIOPSReadOnly')]` a `[parameters('diskMBpsReadOnly')]` vlastními hodnotami.

[Šablona pro sdílenou oblast Ultra disks](https://aka.ms/SharedUltraDiskARMtemplateZonal)

---

## <a name="using-azure-shared-disks-with-your-vms"></a>Použití sdílených disků Azure s vašimi virtuálními počítači

Po nasazení sdíleného disku pomocí nástroje `maxShares>1` můžete disk připojit k jednomu nebo více virtuálním počítačům.

> [!NOTE]
> Pokud nasazujete Ultra disk, ujistěte se, že splňuje požadavky nezbytné pro. Podrobnosti najdete v tématu [použití disků Azure Ultra](disks-enable-ultra-ssd.md) .

```azurepowershell-interactive

$resourceGroup = "myResourceGroup"
$location = "WestCentralUS"

$vm = New-AzVm -ResourceGroupName $resourceGroup -Name "myVM" -Location $location -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress"

$dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup -DiskName "mySharedDisk"

$vm = Add-AzVMDataDisk -VM $vm -Name "mySharedDisk" -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0

update-AzVm -VM $vm -ResourceGroupName $resourceGroup
```

## <a name="supported-scsi-pr-commands"></a>Podporované příkazy SCSI PR

Po připojení sdíleného disku k virtuálním počítačům ve vašem clusteru můžete na disk vytvořit kvorum a číst ho a zapisovat ho pomocí SCSI žádosti o přijetí změn. Při použití sdílených disků Azure jsou k dispozici následující příkazy žádosti o přijetí změn:

Pokud chcete s diskem pracovat, začněte se seznamem trvalých rezervací – akcí:

```
PR_REGISTER_KEY 

PR_REGISTER_AND_IGNORE 

PR_GET_CONFIGURATION 

PR_RESERVE 

PR_PREEMPT_RESERVATION 

PR_CLEAR_RESERVATION 

PR_RELEASE_RESERVATION 
```

Při použití PR_RESERVE, PR_PREEMPT_RESERVATION nebo PR_RELEASE_RESERVATION, zadejte jeden z následujících trvalých rezervací-Type:

```
PR_NONE 

PR_WRITE_EXCLUSIVE 

PR_EXCLUSIVE_ACCESS 

PR_WRITE_EXCLUSIVE_REGISTRANTS_ONLY 

PR_EXCLUSIVE_ACCESS_REGISTRANTS_ONLY 

PR_WRITE_EXCLUSIVE_ALL_REGISTRANTS 

PR_EXCLUSIVE_ACCESS_ALL_REGISTRANTS 
```

Při použití PR_RESERVE, PR_REGISTER_AND_IGNORE, PR_REGISTER_KEY, PR_PREEMPT_RESERVATION, PR_CLEAR_RESERVATION nebo PR_RELEASE-RESERVATION je také potřeba zadat trvalou rezervaci klíč.


## <a name="next-steps"></a>Další kroky

Pokud dáváte přednost použití šablon Azure Resource Manager k nasazení disku, jsou k dispozici následující ukázkové šablony:
- [SSD úrovně Premium](https://aka.ms/SharedPremiumDiskARMtemplate)
- [Místní disky Ultra](https://aka.ms/SharedUltraDiskARMtemplateRegional)
- [Oblast Ultra disků](https://aka.ms/SharedUltraDiskARMtemplateZonal)