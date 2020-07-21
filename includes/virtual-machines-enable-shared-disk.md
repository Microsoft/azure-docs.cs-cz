---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/14/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 29a90b94db5e6e5791361bad004efcf649e1950b
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86500586"
---
## <a name="limitations"></a>Omezení

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="supported-operating-systems"></a>Podporované operační systémy

Sdílené disky podporují více operačních systémů. Podporované operační systémy najdete v částech [Windows](../articles/virtual-machines/windows/disks-shared.md#windows) a [Linux](../articles/virtual-machines/linux/disks-shared.md#linux) článku konceptu.

## <a name="disk-sizes"></a>Velikosti disků

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-shared-disks"></a>Nasazení sdílených disků

### <a name="deploy-a-premium-ssd-as-a-shared-disk"></a>Nasazení jednotky SSD úrovně Premium jako sdíleného disku

Pokud chcete nasadit spravovaný disk s povolenou funkcí sdíleného disku, použijte novou vlastnost `maxShares` a definujte hodnotu větší než 1. To umožňuje sdílet disk napříč několika virtuálními počítači.

> [!IMPORTANT]
> Hodnotu `maxShares` lze nastavit nebo změnit pouze v případě, že je disk odpojen ze všech virtuálních počítačů. Podívejte se na [velikosti disků](#disk-sizes) pro povolené hodnoty pro `maxShares` .

#### <a name="cli"></a>Rozhraní příkazového řádku
```azurecli

az disk create -g myResourceGroup -n mySharedDisk --size-gb 1024 -l westcentralus --sku PremiumSSD_LRS --max-shares 2

```

#### <a name="powershell"></a>PowerShell
```azurepowershell-interactive

$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType PremiumSSD_LRS -CreateOption Empty

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig

```

#### <a name="azure-resource-manager"></a>Azure Resource Manager
Než použijete následující šablonu, nahraďte `[parameters('dataDiskName')]` , `[resourceGroup().location]` , a `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]` vlastními hodnotami.

```json
{ 
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "dataDiskName": {
      "type": "string",
      "defaultValue": "mySharedDisk"
    },
    "dataDiskSizeGB": {
      "type": "int",
      "defaultValue": 1024
    },
    "maxShares": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/disks",
      "name": "[parameters('dataDiskName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-07-01",
      "sku": {
        "name": "Premium_LRS"
      },
      "properties": {
        "creationData": {
          "createOption": "Empty"
        },
        "diskSizeGB": "[parameters('dataDiskSizeGB')]",
        "maxShares": "[parameters('maxShares')]"
      }
    }
  ] 
}
```

### <a name="deploy-an-ultra-disk-as-a-shared-disk"></a>Nasazení Ultra disku jako sdíleného disku

Pokud chcete nasadit spravovaný disk s povolenou funkcí sdíleného disku, změňte `maxShares` parametr na hodnotu větší než 1. To umožňuje sdílet disk napříč několika virtuálními počítači.

> [!IMPORTANT]
> Hodnotu `maxShares` lze nastavit nebo změnit pouze v případě, že je disk odpojen ze všech virtuálních počítačů. Podívejte se na [velikosti disků](#disk-sizes) pro povolené hodnoty pro `maxShares` .

#### <a name="cli"></a>Rozhraní příkazového řádku
```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

#### <a name="powershell"></a>PowerShell
```azurepowershell-interactive

$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig

```

#### <a name="azure-resource-manager"></a>Azure Resource Manager

Pokud chcete nasadit spravovaný disk s povolenou funkcí sdíleného disku, použijte vlastnost `maxShares` a definujte hodnotu větší než 1. To umožňuje sdílet disk napříč několika virtuálními počítači.

> [!IMPORTANT]
> Hodnotu `maxShares` lze nastavit nebo změnit pouze v případě, že je disk odpojen ze všech virtuálních počítačů. Podívejte se na [velikosti disků](#disk-sizes) pro povolené hodnoty pro `maxShares` .

Než použijete následující šablonu, nahraďte `[parameters('dataDiskName')]` , `[resourceGroup().location]` , `[parameters('dataDiskSizeGB')]` , `[parameters('maxShares')]` , `[parameters('diskIOPSReadWrite')]` , `[parameters('diskMBpsReadWrite')]` , `[parameters('diskIOPSReadOnly')]` a `[parameters('diskMBpsReadOnly')]` vlastními hodnotami.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "uShared30"
    },
    "location": {
        "type": "string",
        "defaultValue": "westus",
        "metadata": {
                "description": "Location for all resources."
        }
    },
    "dataDiskSizeGB": {
      "type": "int",
      "defaultValue": 1024
    },
    "maxShares": {
      "type": "int",
      "defaultValue": 2
    },
    "diskIOPSReadWrite": {
      "type": "int",
      "defaultValue": 2048
    },
    "diskMBpsReadWrite": {
      "type": "int",
      "defaultValue": 20
    },    
    "diskIOPSReadOnly": {
      "type": "int",
      "defaultValue": 100
    },
    "diskMBpsReadOnly": {
      "type": "int",
      "defaultValue": 1
    }    
  }, 
  "resources": [
    {
        "type": "Microsoft.Compute/disks",
        "name": "[parameters('diskName')]",
        "location": "[parameters('location')]",
        "apiVersion": "2019-07-01",
        "sku": {
            "name": "UltraSSD_LRS"
        },
        "properties": {
            "creationData": {
                "createOption": "Empty"
            },
            "diskSizeGB": "[parameters('dataDiskSizeGB')]",
            "maxShares": "[parameters('maxShares')]",
            "diskIOPSReadWrite": "[parameters('diskIOPSReadWrite')]",
            "diskMBpsReadWrite": "[parameters('diskMBpsReadWrite')]",
            "diskIOPSReadOnly": "[parameters('diskIOPSReadOnly')]",
            "diskMBpsReadOnly": "[parameters('diskMBpsReadOnly')]"
        }
    }
  ]
}
```

### <a name="using-azure-shared-disks-with-your-vms"></a>Použití sdílených disků Azure s vašimi virtuálními počítači

Po nasazení sdíleného disku pomocí nástroje `maxShares>1` můžete disk připojit k jednomu nebo více virtuálním počítačům.

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
