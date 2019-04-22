---
title: Azure Site Recovery - vyloučení disků při replikaci virtuálních počítačů Azure pomocí Azure Powershellu | Dokumentace Microsoftu
description: Zjistěte, jak vyloučit disky virtuálních počítačů Azure během Azure Site Recovery s využitím Azure Powershellu.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/18/2019
ms.author: asgang
ms.openlocfilehash: 54a32d7f7aa4bcab73f5828da3e7eba9d25276be
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678271"
---
# <a name="exclude-disks-from-powershell-replication-of-azure-vms"></a>Vyloučení disků z replikace virtuálních počítačů Azure Powershellu

Tento článek popisuje, jak vyloučit disky při replikaci virtuálních počítačů Azure. Může vyloučit disky pro optimalizaci šířky pásma spotřebovaná replikace nebo cílové straně prostředky, které používají tyto disky. Tato funkce je aktuálně dostupná jenom prostřednictvím Azure Powershellu.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Než začnete, potřebujete:

- Ujistěte se, že rozumíte [komponent a architektury zotavení po havárii](azure-to-azure-architecture.md).
- Zkontrolujte [požadavky na podporu](azure-to-azure-support-matrix.md) pro všechny komponenty.
- Ujistěte se, že máte AzureRm Powershellu "Az" modulu. Pro instalaci nebo aktualizaci prostředí PowerShell, najdete v článku [instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).
- Ujistěte se, že jste vytvořili trezor služby recovery services a aspoň jednou chráněný virtuální počítač. Pokud jste tyto věci takto neučinili, pokračujte v procesu v [nastavit zotavení po havárii pro virtuální počítače Azure pomocí Azure Powershellu](azure-to-azure-powershell.md).

## <a name="why-exclude-disks-from-replication"></a>Proč vylučovat disky z replikace
Je třeba vyloučit disky z replikace, protože:

- Váš virtuální počítač byl dosažen [frekvence změny limity Azure Site Recovery k replikaci dat](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix).

- Měněná data uložená na vyloučených discích nejsou důležitá nebo nemusí být replikována.

- Chcete uložit úložné a síťové prostředky odstraněním potřeby replikace data.

## <a name="how-to-exclude-disks-from-replication"></a>Jak vyloučit disky z replikace

V našem příkladu jsme replikovat virtuální počítač, který má jeden operační systém a tři datové disky, které jsou na oblast východní USA pro oblast západní USA 2. Název virtuálního počítače je *AzureDemoVM*. Vyloučení disku se 1 jsme zachovat disky 2 a 3.

## <a name="get-details-of-the-virtual-machines-to-replicate"></a>Získat podrobnosti pro replikaci virtuálních počítačů

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

Získáte podrobné informace o discích virtuálních počítačů. Tyto informace se použije později při spuštění replikace virtuálního počítače.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-an-azure-virtual-machine"></a>Replikace virtuálního počítače Azure

V následujícím příkladu předpokládáme, že už máte účet úložiště mezipaměti, zásady replikace a mapování. Pokud nemáte k dispozici tyto věci, postupujte podle procesu na [nastavit zotavení po havárii pro virtuální počítače Azure pomocí Azure Powershellu](azure-to-azure-powershell.md).

Replikace virtuálního počítače Azure s *spravované disky*.

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

Když operace počáteční replikace skončí úspěšně, data virtuálního počítače se replikují do oblasti pro zotavení.

Můžete přejít na web Azure Portal a zobrazit replikované virtuální počítače v části "replikované položky."

Proces replikace začne synchronizace replik indexů kopii replikační disky virtuálního počítače v oblasti obnovení. Tato fáze se nazývá fáze počáteční replikace.

Po dokončení počáteční replikace, replikace přejde k fázi rozdílové synchronizace. V tomto okamžiku je chráněný virtuální počítač. Vyberte chráněný virtuální počítač, pokud chcete zobrazit, pokud jsou vyloučeny všechny disky.

## <a name="next-steps"></a>Další postup

Další informace o [spuštění testovacího převzetí služeb při](site-recovery-test-failover-to-azure.md).
