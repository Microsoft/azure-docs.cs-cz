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
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471674"
---
## <a name="limitations"></a>Omezení

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>Velikosti disků

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-an-azure-shared-disk"></a>Nasazení sdíleného disku Azure

Pokud chcete nasadit spravovaný disk s povolenou funkcí sdíleného disku, použijte novou vlastnost `maxShares` a definujte hodnotu `>1`. To umožňuje sdílet disk napříč několika virtuálními počítači.

> [!IMPORTANT]
> Hodnotu `maxShares` lze nastavit nebo změnit pouze v případě, že je disk odpojen ze všech virtuálních počítačů. Povolené hodnoty pro `maxShares`najdete v části [velikosti disků](#disk-sizes) .

Než použijete následující šablonu, nahraďte `[parameters('dataDiskName')]`, `[resourceGroup().location]`, `[parameters('dataDiskSizeGB')]`a `[parameters('maxShares')]` vlastními hodnotami.

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

### <a name="using-azure-shared-disks-with-your-vms"></a>Použití sdílených disků Azure s vašimi virtuálními počítači

Po nasazení sdíleného disku s `maxShares>1`můžete disk připojit k jednomu nebo více virtuálním počítačům.

> [!IMPORTANT]
> Všechny virtuální počítače sdílející disk musí být nasazené ve stejné [skupině umístění blízkosti](../articles/virtual-machines/windows/proximity-placement-groups.md).

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

Pokud vás zajímá, jak sdílet sdílené disky, [Zaregistrujte si verzi Preview](https://aka.ms/AzureSharedDiskPreviewSignUp).