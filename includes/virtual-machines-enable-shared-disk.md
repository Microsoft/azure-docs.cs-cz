---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 26e76731f663ac9038bc87182d52c4bd245f1b6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77471674"
---
## <a name="limitations"></a>Omezení

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>Velikosti disků

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-an-azure-shared-disk"></a>Nasazení sdíleného disku Azure

Chcete-li nasadit spravovaný disk s `maxShares` povolenou funkcí `>1`sdíleného disku, použijte novou vlastnost a definujte hodnotu . Díky tomu je disk sdílet mezi více virtuálních počítačů.

> [!IMPORTANT]
> Hodnotu `maxShares` lze nastavit nebo změnit pouze v případě, že disk je odpojen ze všech virtuálních počítačů. V [části Velikosti disku](#disk-sizes) naleznete `maxShares`povolené hodnoty pro program .

Před použitím následující šablony `[parameters('dataDiskName')]` `[resourceGroup().location]`nahraďte , , `[parameters('dataDiskSizeGB')]`, a `[parameters('maxShares')]` s vlastními hodnotami.

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

### <a name="using-azure-shared-disks-with-your-vms"></a>Používání sdílených disků Azure s virtuálními počítači

Po nasazení sdíleného disku s `maxShares>1`programem můžete disk připojit k jednomu nebo více virtuálním počítačům.

> [!IMPORTANT]
> Všechny virtuální počítače sdílející disk musí být nasazeny ve stejné [skupině umístění bez kontaktních míst](../articles/virtual-machines/windows/proximity-placement-groups.md).

```azurepowershell-interactive

$resourceGroup = "myResourceGroup"
$location = "WestCentralUS"
$ppgName = "myPPG"
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard

$vm = New-AzVm -ResourceGroupName $resourceGroup -Name "myVM" -Location $location -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress" -ProximityPlacementGroup $ppg.Id

$dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup -DiskName "mySharedDisk"

$vm = Add-AzVMDataDisk -VM $vm -Name "mySharedDisk" -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0

update-AzVm -VM $vm -ResourceGroupName $resourceGroup
```

## <a name="supported-scsi-pr-commands"></a>Podporované příkazy SCSI PR

Po navázání sdíleného disku do virtuálních počítačů v clusteru můžete vytvořit kvorum a číst/zapisovat na disk pomocí SCSI PR. Při použití sdílených disků Azure jsou k dispozici následující příkazy PR:

Chcete-li pracovat s diskem, začněte se seznamem trvalých akcí rezervace:

```
PR_REGISTER_KEY 

PR_REGISTER_AND_IGNORE 

PR_GET_CONFIGURATION 

PR_RESERVE 

PR_PREEMPT_RESERVATION 

PR_CLEAR_RESERVATION 

PR_RELEASE_RESERVATION 
```

Při použití PR_RESERVE, PR_PREEMPT_RESERVATION nebo PR_RELEASE_RESERVATION zadejte jeden z následujících typů trvalé rezervace:

```
PR_NONE 

PR_WRITE_EXCLUSIVE 

PR_EXCLUSIVE_ACCESS 

PR_WRITE_EXCLUSIVE_REGISTRANTS_ONLY 

PR_EXCLUSIVE_ACCESS_REGISTRANTS_ONLY 

PR_WRITE_EXCLUSIVE_ALL_REGISTRANTS 

PR_EXCLUSIVE_ACCESS_ALL_REGISTRANTS 
```

Při použití PR_RESERVE, PR_REGISTER_AND_IGNORE, PR_REGISTER_KEY, PR_PREEMPT_RESERVATION, PR_CLEAR_RESERVATION nebo PR_RELEASE REZERVACE je také nutné zadat trvalý klíč rezervace.


## <a name="next-steps"></a>Další kroky

Pokud máte zájem o vyzkoušení sdílených disků, [zaregistrujte se do naší verze Preview](https://aka.ms/AzureSharedDiskPreviewSignUp).