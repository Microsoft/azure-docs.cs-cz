---
title: Vyloučení disků virtuálních počítačů Azure z replikace pomocí Azure Site Recovery a Azure PowerShellu
description: Zjistěte, jak vyloučit disky virtuálních počítačů Azure během Azure Site Recovery pomocí Azure PowerShellu.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 02/18/2019
ms.openlocfilehash: 7355233bb7241571e3f3820aafac6952af245654
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75973679"
---
# <a name="exclude-disks-from-powershell-replication-of-azure-vms"></a>Vyloučení disků z replikace virtuálních počítačů Azure v Prostředí PowerShell

Tento článek popisuje, jak vyloučit disky při replikaci virtuálních počítačů Azure. Můžete vyloučit disky pro optimalizaci spotřebované šířky pásma replikace nebo prostředky na straně cíl, které tyto disky používají. V současné době tato funkce je k dispozici pouze prostřednictvím Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Než začnete, potřebujete:

- Ujistěte se, že rozumíte [architektuře a součástem zotavení po havárii](azure-to-azure-architecture.md).
- Zkontrolujte [požadavky na podporu](azure-to-azure-support-matrix.md) pro všechny komponenty.
- Ujistěte se, že máte modul AzureRm PowerShell "Az". Informace o instalaci nebo aktualizaci PowerShellu najdete [v tématu Instalace modulu Azure PowerShellu](https://docs.microsoft.com/powershell/azure/install-az-ps).
- Ujistěte se, že jste alespoň jednou vytvořili trezor služeb pro obnovení a chráněné virtuální počítače. Pokud jste tyto věci neudělali, postupujte podle postupu na [webu Nastavení zotavení po havárii pro virtuální počítače Azure pomocí Azure PowerShellu](azure-to-azure-powershell.md).
- Pokud hledáte informace o přidávání disků do virtuálního počítače Azure povoleného pro replikaci, [přečtěte si tento článek](azure-to-azure-enable-replication-added-disk.md).

## <a name="why-exclude-disks-from-replication"></a>Proč vyloučit disky z replikace
Je možné, že bude nutné vyloučit disky z replikace, protože:

- Váš virtuální počítač dosáhl [limitů Azure Site Recovery, aby mohl replikovat rychlost výměny dat](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix).

- Data, která jsou na vyloučeném disku zachycena, nejsou důležitá nebo není nutné je replikovat.

- Chcete uložit úložiště a síťové prostředky tím, že nebudou replikovat data.

## <a name="how-to-exclude-disks-from-replication"></a>Jak vyloučit disky z replikace

V našem příkladu replikujeme virtuální počítač, který má jeden operační systém a tři datové disky, který je v oblasti USA – východní USA, do oblasti ZÁPADNÍ USA 2. Název virtuálního počítače je *AzureDemoVM*. Vylučujeme disk 1 a uchováváme disky 2 a 3.

## <a name="get-details-of-the-virtual-machines-to-replicate"></a>Získejte podrobnosti o virtuálních počítačích, které chcete replikovat

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM     
```

```
ResourceGroupName  : A2AdemoRG
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/A2AdemoRG/providers/Microsoft.Compute/virtualMachines/AzureDemoVM
VmId               : 1b864902-c7ea-499a-ad0f-65da2930b81b
Name               : AzureDemoVM
Type               : Microsoft.Compute/virtualMachines
Location           : eastus
Tags               : {}
DiagnosticsProfile : {BootDiagnostics}
HardwareProfile    : {VmSize}
NetworkProfile     : {NetworkInterfaces}
OSProfile          : {ComputerName, AdminUsername, WindowsConfiguration, Secrets}
ProvisioningState  : Succeeded
StorageProfile     : {ImageReference, OsDisk, DataDisks}
```

Získejte podrobnosti o discích virtuálního počítače. Tyto informace se použijí později při spuštění replikace virtuálního soudu.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-an-azure-virtual-machine"></a>Replikace virtuálního počítače Azure

V následujícím příkladu předpokládáme, že již máte účet úložiště mezipaměti, zásady replikace a mapování. Pokud tyto věci nemáte, postupujte podle postupu na [webu Nastavení zotavení po havárii pro virtuální počítače Azure pomocí Azure PowerShellu](azure-to-azure-powershell.md).

Replikujte virtuální počítač Azure se *spravovanými disky*.

```azurepowershell

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration).

#OsDisk
$OSdiskId =  $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data Disk 1 i.e StorageProfile.DataDisks[0] is excluded, so we will provide it during the time of replication.

# Data disk 2
$datadiskId2  = $vm.StorageProfile.DataDisks[1].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[1]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[1]. StorageAccountType

$DataDisk2ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId2 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

# Data Disk 3

$datadiskId3  = $vm.StorageProfile.DataDisks[2].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[2]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[2]. StorageAccountType

$DataDisk3ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId3 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk2ReplicationConfig, $DataDisk3ReplicationConfig


#Start replication by creating a replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

Když je operace start-replication úspěšná, data virtuálního aplikace se replikují do oblasti obnovení.

Můžete přejít na portál Azure a zobrazit replikované virtuální počítače v části "replikované položky".

Proces replikace začíná výsežení kopie replikující chrupu virtuálního počítače v oblasti obnovení. Tato fáze se nazývá fáze počáteční replikace.

Po dokončení počáteční replikace se replikace přesune do fáze synchronizace rozdílů. V tomto okamžiku je virtuální počítač chráněn. Vyberte chráněný virtuální počítač a zjistěte, jestli jsou vyloučeny nějaké disky.

## <a name="next-steps"></a>Další kroky

Další informace o [spuštění testu převzetí služeb při selhání](site-recovery-test-failover-to-azure.md).
