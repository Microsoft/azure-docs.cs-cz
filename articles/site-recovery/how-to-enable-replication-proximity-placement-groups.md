---
title: Replikace virtuálních počítačů Azure běžících ve skupinách umístění pro Proximity
description: Přečtěte si, jak replikovat virtuální počítače Azure se systémem do skupin umístění v blízkosti pomocí Azure Site Recovery.
author: Sharmistha-Rai
manager: gaggupta
ms.topic: how-to
ms.date: 05/25/2020
ms.openlocfilehash: f3abdad427e038bb4a853cb6222174dd090cb6b2
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348419"
---
# <a name="replicate-azure-virtual-machines-running-in-proximity-placement-groups-to-another-region"></a>Replikace virtuálních počítačů Azure spuštěných ve skupinách umístění bezkontaktní komunikace do jiné oblasti

Tento článek popisuje, jak replikovat, převzetí služeb při selhání a navrácení služeb po obnovení z provozu do skupiny umístění blízkosti do sekundární oblasti.

[Skupiny umístění pro Proximity](../virtual-machines/windows/proximity-placement-groups-portal.md) je logická schopnost skupin virtuálních počítačů Azure, kterou můžete použít ke snížení latence sítě virtuálních počítačů přidružených k vašim aplikacím. Pokud jsou virtuální počítače nasazené v rámci stejné skupiny umístění pro Proximity, jsou fyzicky umístěné co nejblíže sobě. Skupiny umístění blízkosti jsou zvláště užitečné pro řešení požadavků na úlohy citlivé na latenci.

## <a name="disaster-recovery-with-proximity-placement-groups"></a>Zotavení po havárii se skupinami umístění pro Proximity

V typickém scénáři můžete mít virtuální počítače běžící ve skupině umístění s blízkostí, abyste se vyhnuli latenci sítě mezi různými úrovněmi vaší aplikace. I když to může zajistit optimální latenci sítě, chcete tyto aplikace ochránit pomocí Site Recovery pro jakékoli selhání úrovně oblasti. Site Recovery replikuje data z jedné oblasti do jiné oblasti Azure a v případě převzetí služeb při selhání zobrazí počítače v oblasti zotavení po havárii.

## <a name="considerations"></a>Co je potřeba vzít v úvahu

- Nejlepším úsilím pro převzetí služeb při selhání a navrácení služeb po obnovení z provozu do skupiny umístění s blízkými akcemi. Pokud se ale virtuální počítač nemůže během převzetí služeb při selhání nebo navrácení služeb po obnovení přenést do umístění blízkosti, pak se virtuální počítače vytvoří mimo skupinu umístění blízkosti.
-  Pokud je skupina dostupnosti připnuté do skupiny umístění blízkosti a během virtuálních počítačů s podporou převzetí služeb při selhání/navrácení služeb po obnovení mají v sadě dostupnosti omezení přidělení, vytvoří se virtuální počítače mimo skupinu dostupnosti a umístění blízkosti.
-  U nespravovaných disků není podporováno Site Recovery pro skupiny umístění blízkosti.

> [!NOTE]
> Azure Site Recovery nepodporuje navrácení služeb po obnovení ze spravovaných disků pro scénáře Hyper-V do Azure. Z tohoto důvodu není podporováno navrácení služeb po obnovení ze skupiny umístění v Azure do technologie Hyper-V.

## <a name="prerequisites"></a>Požadavky

1. Ujistěte se, že máte Azure PowerShell AZ Module. Pokud potřebujete nainstalovat nebo upgradovat Azure PowerShell, postupujte podle pokynů v tomto [Průvodci a nainstalujte a nakonfigurujte Azure PowerShell](/powershell/azure/install-az-ps).
2. Minimální Azure PowerShell AZ verze by měl být 4.1.0. Chcete-li zjistit aktuální verzi, použijte následující příkaz –
    ```
    Get-InstalledModule -Name Az
    ```

## <a name="set-up-site-recovery-for-virtual-machines-in-proximity-placement-group"></a>Nastavit Site Recovery pro Virtual Machines ve skupině umístění blízkosti

> [!NOTE]
> Ujistěte se, že máte jedinečné ID cílové skupiny umístění blízkosti. Pokud vytváříte novou skupinu umístění pro Proximity, zkontrolujte [následující](../virtual-machines/windows/proximity-placement-groups.md#create-a-proximity-placement-group) příkaz a pokud používáte existující skupinu umístění do blízkosti, použijte tento [příkaz.](../virtual-machines/windows/proximity-placement-groups.md#list-proximity-placement-groups)

### <a name="azure-to-azure"></a>Azure do Azure

1. [Přihlaste](./azure-to-azure-powershell.md#sign-in-to-your-microsoft-azure-subscription) se ke svému účtu a nastavte své předplatné.
2. Získejte podrobnosti o virtuálním počítači, který plánujete replikovat, jak je uvedeno [tady](./azure-to-azure-powershell.md#get-details-of-the-virtual-machine-to-be-replicated).
3. [Vytvořte](./azure-to-azure-powershell.md#create-a-recovery-services-vault) trezor služby Recovery Services a [nastavte](./azure-to-azure-powershell.md#set-the-vault-context) kontext trezoru.
4. Připraví Trezor k zahájení replikace virtuálního počítače. To zahrnuje vytvoření [objektu Service Fabric](./azure-to-azure-powershell.md#create-a-site-recovery-fabric-object-to-represent-the-primary-source-region) pro primární i oblasti obnovení.
5. [Vytvořte](./azure-to-azure-powershell.md#create-a-site-recovery-protection-container-in-the-primary-fabric) kontejner Site Recovery Protection pro primární i Recovery Fabric.
6. [Vytvořte](./azure-to-azure-powershell.md#create-a-replication-policy) zásadu replikace.
7. Pomocí [těchto](./azure-to-azure-powershell.md#create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container) kroků a mapování kontejneru ochrany pro navrácení služeb po obnovení vytvořte mapování kontejnerů ochrany mezi primárním kontejnerem a ochranou kontejneru ochrany, jak je uvedeno [zde](./azure-to-azure-powershell.md#create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover).
8. Pomocí [následujících kroků vytvořte](./azure-to-azure-powershell.md#create-cache-storage-account-and-target-storage-account) účet úložiště mezipaměti.
9. Vytvořte požadovaná mapování sítě, jak je uvedeno [zde](./azure-to-azure-powershell.md#create-network-mappings).
10. Pokud chcete replikovat virtuální počítač Azure se spravovanými disky, použijte níže uvedenou rutinu PowerShellu.

```azurepowershell
#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OS Disk
$OSdisk = Get-AzDisk -DiskName $OSdiskName -ResourceGroupName $OSdiskResourceGroup
$OSdiskId = $OSdisk.Id
$RecoveryOSDiskAccountType = $OSdisk.Sku.Name
$RecoveryReplicaDiskAccountType = $OSdisk.Sku.Name

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $OSdiskId -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

#Data disk
$datadisk = Get-AzDisk -DiskName $datadiskName -ResourceGroupName $datadiskResourceGroup
$datadiskId1 = $datadisk[0].Id
$RecoveryReplicaDiskAccountType = $datadisk[0].Sku.Name
$RecoveryTargetDiskAccountType = $datadisk[0].Sku.Name

$DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.

$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.

$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id
```

Při povolování zóny pro replikaci zóny pomocí PPG se příkaz ke spuštění replikace vymění pomocí rutiny PowerShellu.

```azurepowershell
$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id -RecoveryAvailabilityZone "2"
```

Po úspěšném dokončení operace spuštění replikace se data virtuálního počítače replikují do oblasti obnovení.

Proces replikace začíná prvním vynásobením kopie replikačních disků virtuálního počítače v oblasti obnovení. Tato fáze se nazývá fáze počáteční replikace.

Po dokončení počáteční replikace se replikace přesune do fáze rozdílové synchronizace. V tuto chvíli je virtuální počítač chráněný a na něm může být prováděna operace testovacího převzetí služeb při selhání. Stav replikace replikované položky představující virtuální počítač přejde do chráněného stavu po dokončení počáteční replikace.

Sledujte stav replikace a stav replikace pro virtuální počítač tím, že získáte podrobné informace o položce chráněné replikace, která je pro ni odpovídající.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

11. Chcete-li provést test převzetí služeb při selhání [, proveďte ověření](./azure-to-azure-powershell.md#do-a-test-failover-validate-and-cleanup-test-failover) a vyčištění testovacího převzetí služeb při selhání.
12. Převzetí služeb při selhání provedete podle kroků uvedených [tady](./azure-to-azure-powershell.md#fail-over-to-azure).
13. K opětovné ochraně a navrácení služeb po obnovení do zdrojové oblasti použijte níže uvedenou rutinu prostředí PowerShell –

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage


#Use the recovery protection container, new cache storage account in West US and the source region VM resource group 
Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure -ProtectionContainerMapping $WusToEusPCMapping -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.ResourceId -RecoveryProximityPlacementGroupId $vm.ProximityPlacementGroup.Id
```

14. Pokud chcete zakázat replikaci [, postupujte podle těchto kroků.](./azure-to-azure-powershell.md#disable-replication)

### <a name="vmware-to-azure"></a>Z VMware do Azure

1. Ujistěte se, že jste [připravili místní servery VMware](./vmware-azure-tutorial-prepare-on-premises.md) na zotavení po havárii do Azure.
2. Přihlaste se ke svému účtu a nastavte své předplatné tak, jak je uvedeno [tady](./vmware-azure-disaster-recovery-powershell.md#log-into-azure).
3. [Nastavte](./vmware-azure-disaster-recovery-powershell.md#set-up-a-recovery-services-vault) trezor Recovery Services a [nastavte kontext trezoru](./vmware-azure-disaster-recovery-powershell.md#set-the-vault-context).
4. [Ověřte](./vmware-azure-disaster-recovery-powershell.md#validate-vault-registration) registraci trezoru.
5. [Vytvořte](./vmware-azure-disaster-recovery-powershell.md#create-a-replication-policy) zásadu replikace.
6. [Přidejte](./vmware-azure-disaster-recovery-powershell.md#add-a-vcenter-server-and-discover-vms) Server vCenter a vyhledejte virtuální počítače a [vytvořte](./vmware-azure-disaster-recovery-powershell.md#create-storage-accounts-for-replication) účty úložiště pro replikaci.
7. Pokud chcete replikovat Virtual Machines VMware, podívejte se na podrobnosti a postupujte podle následující rutiny prostředí PowerShell –

```azurepowershell
#Get the target resource group to be used
$ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg"

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM1 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1 using the Az.RecoveryServices module 2.0.0 onwards to replicate to managed disks
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" -RecoveryProximityPlacementGroupId $targetPpg.Id
```

8. Stav replikace a stav replikace virtuálního počítače můžete zjistit pomocí rutiny Get-ASRReplicationProtectedItem.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

9. Nastavení převzetí služeb při selhání nakonfigurujte podle kroků uvedených [tady](./vmware-azure-disaster-recovery-powershell.md#configure-failover-settings).
10. [Spusťte](./vmware-azure-disaster-recovery-powershell.md#run-a-test-failover) testovací převzetí služeb při selhání.
11. Převzetí služeb při selhání do Azure pomocí [těchto](./vmware-azure-disaster-recovery-powershell.md#fail-over-to-azure) kroků.

### <a name="hyper-v-to-azure"></a>Z Hyper-V do Azure

1. Ujistěte se, že jste [připravili místní servery Hyper-V](./hyper-v-prepare-on-premises-tutorial.md) pro zotavení po havárii do Azure.
2. [Přihlaste](./hyper-v-azure-powershell-resource-manager.md#step-1-sign-in-to-your-azure-account) se k Azure.
3. [Nastavte](./hyper-v-azure-powershell-resource-manager.md#step-2-set-up-the-vault) svůj trezor a [nastavte](./hyper-v-azure-powershell-resource-manager.md#step-3-set-the-recovery-services-vault-context) kontext trezoru Recovery Services.
4. [Vytvořte](./hyper-v-azure-powershell-resource-manager.md#step-4-create-a-hyper-v-site) lokalitu Hyper-V.
5. [Nainstalujte](./hyper-v-azure-powershell-resource-manager.md#step-5-install-the-provider-and-agent) poskytovatele a agenta.
6. [Vytvořte](./hyper-v-azure-powershell-resource-manager.md#step-6-create-a-replication-policy) zásadu replikace.
7. Replikaci povolte pomocí následujících kroků – 
    
    a. Načtěte chráněnou položku, která odpovídá virtuálnímu počítači, který chcete chránit, následovně:

    ```azurepowershell
    $VMFriendlyName = "Fabrikam-app"          #Name of the VM
    $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
    ```
    b. Chraňte virtuální počítač. Pokud má virtuální počítač, který chráníte, více než jeden disk, který je k němu připojený, určete pomocí parametru OSDiskName disk s operačním systémem.
    
    ```azurepowershell
    $OSType = "Windows"          # "Windows" or "Linux"
    $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId   $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID -RecoveryProximityPlacementGroupId $targetPpg.Id
    ```
    c. Počkejte, až virtuální počítače dostanou chráněný stav po počáteční replikaci. To může nějakou dobu trvat, v závislosti na faktorech, jako je třeba množství dat, která se mají replikovat, a dostupné nadřazené šířky pásma do Azure. Pokud je chráněný stav na místě, aktualizují se stav úlohy a StateDescription následujícím způsobem: 
    
    ```azurepowershell
    $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob
    $DRjob | Select-Object -ExpandProperty State

    $DRjob | Select-Object -ExpandProperty StateDescription
    ```
    d. Aktualizujte vlastnosti obnovení (například velikost role virtuálního počítače) a síť Azure, ke které chcete připojit síťovou kartu virtuálního počítače po převzetí služeb při selhání.

    ```azurepowershell
    $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

    $VMFriendlyName = "Fabrikam-App"

    $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

    $UpdateJob = Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

    $UpdateJob = Get-AzRecoveryServicesAsrJob -Job $UpdateJob

    $UpdateJob | Select-Object -ExpandProperty state

    Get-AzRecoveryServicesAsrJob -Job $job | Select-Object -ExpandProperty state
    ```
8. Spusťte testovací [převzetí služeb při selhání](./hyper-v-azure-powershell-resource-manager.md#step-8-run-a-test-failover).


## <a name="next-steps"></a>Další kroky

Pokud chcete pro VMware do Azure provést znovu ochranu a navrácení služeb po obnovení, postupujte podle kroků popsaných [tady](./vmware-azure-prepare-failback.md).

Pokud chcete provést převzetí služeb při selhání pro Hyper-V do Azure, postupujte podle kroků uvedených [tady](./site-recovery-failover.md) a proveďte navrácení služeb po obnovení, postupujte podle kroků uvedených [tady](./hyper-v-azure-failback.md).

Další informace najdete v tématu [převzetí služeb při selhání v Site Recovery](site-recovery-failover.md).