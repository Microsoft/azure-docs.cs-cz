---
title: Azure Site Recovery - vyloučení disku během replikace virtuálních počítačů Azure pomocí Azure Powershellu | Dokumentace Microsoftu
description: Zjistěte, jak vyloučit disk pro virtuální počítače Azure pomocí Azure Site Recovery pomocí Azure Powershellu.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/18/2019
ms.author: asgang
ms.openlocfilehash: 1c278d810df7e5ba8701529a59987c9bb16fa40c
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2019
ms.locfileid: "59044110"
---
# <a name="exclude-disks-from-replication-of-azure-vms-to-azure-using-azure-powershell"></a>Vyloučení disků z replikace virtuálních počítačů Azure do Azure pomocí Azure Powershellu

Tento článek popisuje, jak vyloučit disky při replikaci virtuálních počítačů Azure. Takové vyloučení může optimalizovat jak využití šířky pásma potřebné pro replikaci, tak i prostředků využívaných na cílové straně těmito disky. Tato funkce je aktuálně zobrazují jen prostřednictvím Azure Powershellu.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Než začnete, potřebujete:

- Ujistěte se, že rozumíte [komponentám a architektuře řešení](azure-to-azure-architecture.md).
- Zkontrolujte [požadavky na podporu](azure-to-azure-support-matrix.md) pro všechny komponenty.
- Máte Azure PowerShell `Az` modulu. Pokud potřebujete instalaci nebo upgrade prostředí Azure PowerShell, postupujte podle to [příručce k instalaci a konfiguraci prostředí Azure PowerShell](/powershell/azure/install-az-ps).
- Jste již vytvořili trezor služby Recovery services a provedli ochranu virtuálních počítačů aspoň jednou. Pokud není proveďte pomocí dokumentace ke službě uvedených [tady](azure-to-azure-powershell.md).

## <a name="why-exclude-disks-from-replication"></a>Proč vylučovat disky z replikace?
Vyloučení disků z replikace je často nutné z těchto důvodů:

- Váš virtuální počítač byl dosažen [frekvence změny limity Azure Site Recovery k replikaci dat.](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix)

- Často měněná data uložená na vyloučených discích nejsou důležitá nebo nemusí být replikována.

- Odstraněním potřeby replikace těchto častých změn dat chcete šetřit prostředky úložiště a síťové prostředky.


## <a name="how-to-exclude-disks-from-replication"></a>Jak vyloučit disky z replikace?

V příkladu v tomto článku se virtuální počítač, který má 1 operačního systému a 3 datové disky v oblasti USA – východ se replikují do oblasti západní USA 2. Název virtuálního počítače, v tomto příkladu je AzureDemoVM. a My se vyloučit Disk 1 a budete mít disku, 2 a 3

## <a name="get-details-of-the-virtual-machines-to-be-replicated"></a>Získat podrobnosti o virtuálních počítačů na replikaci

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


Získáte podrobnosti o disku pro disky virtuálního počítače. Podrobnosti o disku použijete později při spouštění replikace pro virtuální počítač.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-azure-virtual-machine"></a>Replikace virtuálních počítačů Azure

V následujícím příkladu budeme mít předpokládá, že už máte účet úložiště mezipaměti, zásady replikace a mapování. Pokud není proveďte pomocí dokumentace ke službě uvedených [zde](azure-to-azure-powershell.md) 


Replikovat virtuální počítač Azure s **spravované disky**.

```azurepowershell

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OsDisk
$OSdiskId =  $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data Disk 1 i.e StorageProfile.DataDisks[0] is excluded so we will provide it during the time of replication 

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


#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

Po úspěšném spuštění operace replikace dat virtuálního počítače se replikují do oblasti pro zotavení.

Můžete přejít na web Azure Portal a v části replikované položky se zobrazí získávání replikované virtuální počítače.
Proces replikace se spustí na počáteční synchronizace replik indexů kopii replikační disky virtuálního počítače v oblasti obnovení. Tato fáze se nazývá fáze počáteční replikace.

Po dokončení počáteční replikace, replikace přejde do fáze rozdílové synchronizace. V tomto okamžiku je chráněný virtuální počítač. Klikněte na chráněný virtuální počítač > disky, které ověří, zda disk není vyloučený, nebo ne.

## <a name="next-steps"></a>Další postup

[Další informace](site-recovery-test-failover-to-azure.md) o spuštění testovací převzetí služeb při selhání.
