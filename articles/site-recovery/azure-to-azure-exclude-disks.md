---
title: Vyloučení disků virtuálních počítačů Azure z replikace pomocí Azure Site Recovery a Azure PowerShell
description: Přečtěte si, jak vyloučit disky virtuálních počítačů Azure během Azure Site Recovery pomocí Azure PowerShell.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 02/18/2019
ms.openlocfilehash: a21460279420c46b11c43615ae5ecc7bfa81de4d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86135811"
---
# <a name="exclude-disks-from-powershell-replication-of-azure-vms"></a>Vyloučení disků z replikace PowerShellu virtuálních počítačů Azure

Tento článek popisuje, jak vyloučit disky při replikaci virtuálních počítačů Azure. Disky můžete vyloučit pro optimalizaci spotřebované šířky pásma replikace nebo prostředků na cílové straně, které tyto disky používají. V současné době je tato funkce dostupná jenom prostřednictvím Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Než začnete, potřebujete:

- Ujistěte se, že rozumíte [architektuře a komponentám zotavení po havárii](azure-to-azure-architecture.md).
- Zkontrolujte [požadavky na podporu](azure-to-azure-support-matrix.md) pro všechny komponenty.
- Ujistěte se, že máte modul PowerShell AzureRm "AZ". Pokud chcete nainstalovat nebo aktualizovat PowerShell, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps).
- Ujistěte se, že jste aspoň jednou vytvořili trezor služby Recovery Services a chráněné virtuální počítače. Pokud jste tyto věci neudělali, postupujte podle pokynů v části [Nastavení zotavení po havárii pro virtuální počítače Azure pomocí Azure PowerShell](azure-to-azure-powershell.md).
- Pokud hledáte informace o přidávání disků do virtuálního počítače Azure, který je k dispozici pro replikaci, [Přečtěte si tento článek](azure-to-azure-enable-replication-added-disk.md).

## <a name="why-exclude-disks-from-replication"></a>Důvody vyloučení disků z replikace
Je možné, že budete chtít vyloučit disky z replikace z těchto důvodů:

- Virtuální počítač dosáhl [Azure Site Recovery omezení pro replikaci sazeb změny dat](./azure-to-azure-support-matrix.md).

- Data, která jsou stejná na vyloučeném disku, nejsou důležitá nebo nemusí být replikována.

- Chcete uložit úložiště a síťové prostředky tím, že se data nereplikují.

## <a name="how-to-exclude-disks-from-replication"></a>Vyloučení disků z replikace

V našem příkladu budeme replikovat virtuální počítač, který má jeden operační systém a tři datové disky, které jsou v Východní USA oblasti, do Západní USA 2 oblasti. Název virtuálního počítače je *AzureDemoVM*. Vyloučíme disk 1 a zachováte disky 2 a 3.

## <a name="get-details-of-the-virtual-machines-to-replicate"></a>Získat podrobnosti o virtuálních počítačích, které se mají replikovat

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

Získejte podrobnosti o discích virtuálního počítače. Tyto informace se použijí později při zahájení replikace virtuálního počítače.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-an-azure-virtual-machine"></a>Replikace virtuálního počítače Azure

V následujícím příkladu předpokládáme, že už máte účet úložiště mezipaměti, zásady replikace a mapování. Pokud tyto věci nemáte, postupujte podle pokynů v části [Nastavení zotavení po havárii pro virtuální počítače Azure pomocí Azure PowerShell](azure-to-azure-powershell.md).

Replikace virtuálního počítače Azure se *spravovanými disky*.

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

Po úspěšném provedení operace spuštění replikace se data virtuálního počítače replikují do oblasti obnovení.

Můžete přejít na Azure Portal a zobrazit replikované virtuální počítače v části replikované položky.

Proces replikace začíná vytvořením kopie replikačních disků virtuálního počítače v oblasti obnovení. Tato fáze se nazývá fáze prvotní replikace.

Po dokončení počáteční replikace se replikace přesune do fáze rozdílové synchronizace. V tuto chvíli je virtuální počítač chráněný. Vyberte chráněný virtuální počítač a zjistěte, jestli nejsou nějaké disky vyloučené.

## <a name="next-steps"></a>Další kroky

Přečtěte si o [spuštění testovacího převzetí služeb při selhání](site-recovery-test-failover-to-azure.md).
