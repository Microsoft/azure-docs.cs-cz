---
title: Zotavení po havárii pro virtuální počítače Azure pomocí Azure PowerShellu a Azure Site Recovery
description: Zjistěte, jak nastavit zotavení po havárii pro virtuální počítače Azure pomocí Azure Site Recovery pomocí Azure PowerShellu.
services: site-recovery
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 3/29/2019
ms.author: sutalasi
ms.openlocfilehash: 583511194fb100add1d5fc4ea9c06a869cf652b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77212280"
---
# <a name="set-up-disaster-recovery-for-azure-virtual-machines-using-azure-powershell"></a>Nastavení zotavení po havárii pro virtuální počítače Azure pomocí Azure PowerShellu

V tomto článku uvidíte, jak nastavit a otestovat zotavení po havárii pro virtuální počítače Azure pomocí Azure PowerShellu.

Získáte informace o těchto tématech:

> [!div class="checklist"]
> - Vytvořte trezor služby Recovery Services.
> - Nastavte kontext trezoru pro relaci Prostředí PowerShell.
> - Připravte trezor a začněte replikovat virtuální počítače Azure.
> - Vytvořte síťová mapování.
> - Vytvořte účty úložiště pro replikaci virtuálních počítačů.
> - Replikujte virtuální počítače Azure do oblasti obnovení pro zotavení po havárii.
> - Proveďte test převzetí služeb při selhání, ověření a vyčištění test převzetí služeb při selhání.
> - Převzetí služeb při selhání do oblasti obnovení.

> [!NOTE]
> Ne všechny možnosti scénáře dostupné prostřednictvím portálu mohou být dostupné prostřednictvím Azure PowerShellu. Některé možnosti scénáře, které nejsou aktuálně podporované prostřednictvím Azure PowerShellu, jsou:
> - Možnost určit, že všechny disky ve virtuálním počítači by měly být replikovány, aniž by bylo nutné explicitně zadat každý disk virtuálního počítače.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Než začnete, potřebujete:
- Ujistěte se, že rozumíte [komponentám a architektuře řešení](azure-to-azure-architecture.md).
- Zkontrolujte [požadavky na podporu](azure-to-azure-support-matrix.md) pro všechny komponenty.
- Máte modul Azure `Az` PowerShell. Pokud potřebujete nainstalovat nebo upgradovat Azure PowerShell, postupujte podle tohoto [průvodce k instalaci a konfiguraci Azure PowerShellu](/powershell/azure/install-az-ps).

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Přihlášení k předplatnému Microsoft Azure

Přihlaste se k `Connect-AzAccount` předplatnému Azure pomocí rutiny.

```azurepowershell
Connect-AzAccount
```

Vyberte své předplatné Azure. Pomocí `Get-AzSubscription` rutiny získáte seznam předplatných Azure, ke které máte přístup. Vyberte předplatné Azure pro `Set-AzContext` práci s pomocí rutiny.

```azurepowershell
Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

## <a name="get-details-of-the-virtual-machine-to-be-replicated"></a>Získání podrobností o virtuálním počítači, který má být replikován

V tomto článku virtuální počítač v oblasti USA východ je replikována a obnovena v oblasti západní USA 2. Replikovaný virtuální počítač má disk operačního systému a jeden datový disk. Název virtuálního počítače použitého v `AzureDemoVM`příkladu je .

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM
```

```Output
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

Získejte podrobnosti o disku pro disky virtuálního počítače. Podrobnosti o disku budou použity později při spuštění replikace pro virtuální počítač.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Vytvořte skupinu prostředků, ve které chcete vytvořit trezor služby Recovery Services.

> [!IMPORTANT]
> * Trezor služeb obnovení a chráněné virtuální počítače musí být v různých umístěních Azure.
> * Skupina prostředků trezoru služeb obnovení a virtuální počítače, které jsou chráněné, musí být v různých umístěních Azure.
> * Trezor služeb obnovení a skupina prostředků, do které patří, mohou být ve stejném umístění Azure.

V příkladu v tomto článku virtuální počítač, který je chráněn je v oblasti usa – východ. Oblast zotavení vybraná pro zotavení po havárii je oblast Západní USA 2. Trezor služeb obnovení a skupina prostředků trezoru jsou v oblasti obnovení, západní USA 2.

```azurepowershell
#Create a resource group for the recovery services vault in the recovery Azure region
New-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"
```

```Output
ResourceGroupName : a2ademorecoveryrg
Location          : westus2
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg
```

Vytvořte trezor služeb obnovení. V tomto příkladu je `a2aDemoRecoveryVault` v oblasti Západní USA 2 vytvořena úschovna služeb obnovení s názvem .

```azurepowershell
#Create a new Recovery services vault in the recovery region
$vault = New-AzRecoveryServicesVault -Name "a2aDemoRecoveryVault" -ResourceGroupName "a2ademorecoveryrg" -Location "West US 2"

Write-Output $vault
```

```Output
Name              : a2aDemoRecoveryVault
ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoRecoveryVault
Type              : Microsoft.RecoveryServices/vaults
Location          : westus2
ResourceGroupName : a2ademorecoveryrg
SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

## <a name="set-the-vault-context"></a>Nastavení kontextu úschovny

Nastavte kontext úložiště pro použití v relaci Prostředí PowerShell. Po nastavení kontextu úložiště se operace obnovení webu Azure v relaci prostředí PowerShell uvedou v kontextu vybraného trezoru.

```azurepowershell
#Setting the vault context.
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

```Output
ResourceName         ResourceGroupName ResourceNamespace          ResourceType
------------         ----------------- -----------------          -----------
a2aDemoRecoveryVault a2ademorecoveryrg Microsoft.RecoveryServices Vaults
```

```azurepowershell
#Delete the downloaded vault settings file
Remove-Item -Path $Vaultsettingsfile.FilePath
```

Pro migraci Azure do Azure můžete nastavit kontext trezoru na nově vytvořený trezor:

```azurepowershell
#Set the vault context for the PowerShell session.
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

## <a name="prepare-the-vault-to-start-replicating-azure-virtual-machines"></a>Příprava trezoru na zahájení replikace virtuálních počítačů Azure

### <a name="create-a-site-recovery-fabric-object-to-represent-the-primary-source-region"></a>Vytvoření objektu infrastruktury site recovery, který bude představovat primární (zdrojovou) oblast

Objekt infrastruktury v úschovně představuje oblast Azure. Primární objekt infrastruktury se vytvoří tak, aby reprezentoval oblast Azure, do které patří virtuální počítače chráněné do trezoru. V příkladu v tomto článku virtuální počítač, který je chráněn je v oblasti usa – východ.

- Pro každý region lze vytvořit pouze jeden objekt infrastruktury.
- Pokud jste dříve povolili replikaci site recovery pro virtuální počítač na portálu Azure, site recovery vytvoří objekt infrastruktury automaticky. Pokud objekt infrastruktury existuje pro oblast, nelze vytvořit nový.

Než začnete, uvědomte si, že operace obnovení lokality jsou prováděny asynchronně. Když zahájíte operaci, je odeslána úloha obnovení webu Azure a vrátí se objekt sledování úlohy. Pomocí objektu sledování úlohy získáte nejnovější`Get-AzRecoveryServicesAsrJob`stav úlohy ( ) a sledujte stav operace.

```azurepowershell
#Create Primary ASR fabric
$TempASRJob = New-AzRecoveryServicesAsrFabric -Azure -Location 'East US'  -Name "A2Ademo-EastUS"

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$PrimaryFabric = Get-AzRecoveryServicesAsrFabric -Name "A2Ademo-EastUS"
```

Pokud jsou virtuální počítače z více oblastí Azure chráněné do stejného trezoru, vytvořte jeden objekt infrastruktury pro každou zdrojovou oblast Azure.

### <a name="create-a-site-recovery-fabric-object-to-represent-the-recovery-region"></a>Vytvoření objektu infrastruktury obnovení webu představující oblast obnovení

Objekt prostředků pro obnovení představuje umístění Azure pro obnovení. Pokud je převzetí služeb při selhání, virtuální počítače jsou replikovány a obnoveny do oblasti obnovení reprezentované prostředků infrastruktury pro obnovení. Oblast Azure pro obnovení použitá v tomto příkladu je – západní USA 2.

```azurepowershell
#Create Recovery ASR fabric
$TempASRJob = New-AzRecoveryServicesAsrFabric -Azure -Location 'West US 2'  -Name "A2Ademo-WestUS"

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$RecoveryFabric = Get-AzRecoveryServicesAsrFabric -Name "A2Ademo-WestUS"
```

### <a name="create-a-site-recovery-protection-container-in-the-primary-fabric"></a>Vytvoření kontejneru ochrany site recovery v primární masce

Kontejner ochrany je kontejner používaný k seskupení replikovaných položek v rámci infrastruktury.

```azurepowershell
#Create a Protection container in the primary Azure region (within the Primary fabric)
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainer -InputObject $PrimaryFabric -Name "A2AEastUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

Write-Output $TempASRJob.State

$PrimaryProtContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $PrimaryFabric -Name "A2AEastUSProtectionContainer"
```

### <a name="create-a-site-recovery-protection-container-in-the-recovery-fabric"></a>Vytvoření kontejneru ochrany site recovery v prostředků pro obnovení

```azurepowershell
#Create a Protection container in the recovery Azure region (within the Recovery fabric)
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainer -InputObject $RecoveryFabric -Name "A2AWestUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"

Write-Output $TempASRJob.State

$RecoveryProtContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $RecoveryFabric -Name "A2AWestUSProtectionContainer"
```

### <a name="create-a-replication-policy"></a>Vytvoření zásady replikace

```azurepowershell
#Create replication policy
$TempASRJob = New-AzRecoveryServicesAsrPolicy -AzureToAzure -Name "A2APolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$ReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "A2APolicy"
```

### <a name="create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container"></a>Vytvoření mapování kontejneru ochrany mezi primárním kontejnerem ochrany a kontejnerem ochrany obnovení

Mapování kontejneru ochrany mapuje kontejner primární ochrany s kontejnerem ochrany obnovení a zásadami replikace. Vytvořte jedno mapování pro každou zásadu replikace, kterou použijete k replikaci virtuálních počítačů mezi dvojicí kontejnerů ochrany.

```azurepowershell
#Create Protection container mapping between the Primary and Recovery Protection Containers with the Replication policy
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "A2APrimaryToRecovery" -Policy $ReplicationPolicy -PrimaryProtectionContainer $PrimaryProtContainer -RecoveryProtectionContainer $RecoveryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$EusToWusPCMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $PrimaryProtContainer -Name "A2APrimaryToRecovery"
```

### <a name="create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover"></a>Vytvoření mapování kontejneru ochrany pro navrácení služeb po obnovení (reverzní replikace po převzetí služeb při selhání)

Po převzetí služeb při selhání, když jste připraveni přenést převzetí služeb při selhání přes virtuální počítač zpět do původní oblasti Azure, provedete navrácení služeb po obnovení. Chcete-li navrácení služeb po selhání, převzetí služeb při selhání virtuálního počítače je zpětně replikována z oblasti převzetí služeb při selhání do původní oblasti. Pro zpětnou replikaci role původní oblasti a přepínač oblasti obnovení. Původní oblast se nyní stane novou oblastí obnovení a původně oblast obnovení se nyní stane primární oblastí. Mapování kontejneru ochrany pro reverzní replikaci představuje přepínané role původní chod a oblasti obnovení.

```azurepowershell
#Create Protection container mapping (for fail back) between the Recovery and Primary Protection Containers with the Replication policy
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "A2ARecoveryToPrimary" -Policy $ReplicationPolicy -PrimaryProtectionContainer $RecoveryProtContainer -RecoveryProtectionContainer $PrimaryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$WusToEusPCMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $RecoveryProtContainer -Name "A2ARecoveryToPrimary"
```

## <a name="create-cache-storage-account-and-target-storage-account"></a>Vytvoření účtu úložiště mezipaměti a cílového účtu úložiště

Účet úložiště mezipaměti je standardní účet úložiště ve stejné oblasti Azure jako replikovaný virtuální počítač. Účet úložiště mezipaměti se používá k uložení změny replikace dočasně, před změny jsou přesunuty do oblasti obnovení Azure. Můžete se rozhodnout, ale není nutné, určit různé účty úložiště mezipaměti pro různé disky virtuálního počítače.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$EastUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestorage" -ResourceGroupName "A2AdemoRG" -Location 'East US' -SkuName Standard_LRS -Kind Storage
```

U virtuálních počítačů, které **nepoužívají spravované disky**, je cílovým účtem úložiště účet úložiště v oblasti obnovení, do kterých se replikují disky virtuálního počítače. Cílový účet úložiště může být buď účet standardního úložiště nebo účet úložiště premium. Vyberte typ účtu úložiště požadovaný na základě rychlosti změny dat (rychlost zápisu vi) pro disky a limity podporovaných změn azure site recovery pro typ úložiště.

```azurepowershell
#Create Target storage account in the recovery region. In this case a Standard Storage account
$WestUSTargetStorageAccount = New-AzStorageAccount -Name "a2atargetstorage" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -SkuName Standard_LRS -Kind Storage
```

## <a name="create-network-mappings"></a>Vytvoření síťových mapování

Mapování sítě mapuje virtuální sítě v primární oblasti na virtuální sítě v oblasti obnovení. Mapování sítě určuje virtuální síť Azure v oblasti obnovení, že virtuální počítač v primární virtuální síti by měl převzetí služeb při selhání. Jednu virtuální síť Azure lze namapovat jenom na jednu virtuální síť Azure v oblasti obnovení.

- Vytvořte virtuální síť Azure v oblasti obnovení, na kterou chcete přejít k selhání:

   ```azurepowershell
    #Create a Recovery Network in the recovery region
    $WestUSRecoveryVnet = New-AzVirtualNetwork -Name "a2arecoveryvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.0.0.0/16"

    Add-AzVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $WestUSRecoveryVnet -AddressPrefix "10.0.0.0/20" | Set-AzVirtualNetwork

    $WestUSRecoveryNetwork = $WestUSRecoveryVnet.Id
   ```

- Načíst primární virtuální síť. Virtuální síť, ke které je virtuální počítač připojený:

   ```azurepowershell
    #Retrieve the virtual network that the virtual machine is connected to

    #Get first network interface card(nic) of the virtual machine
    $SplitNicArmId = $VM.NetworkProfile.NetworkInterfaces[0].Id.split("/")

    #Extract resource group name from the ResourceId of the nic
    $NICRG = $SplitNicArmId[4]

    #Extract resource name from the ResourceId of the nic
    $NICname = $SplitNicArmId[-1]

    #Get network interface details using the extracted resource group name and resource name
    $NIC = Get-AzNetworkInterface -ResourceGroupName $NICRG -Name $NICname

    #Get the subnet ID of the subnet that the nic is connected to
    $PrimarySubnet = $NIC.IpConfigurations[0].Subnet

    # Extract the resource ID of the Azure virtual network the nic is connected to from the subnet ID
    $EastUSPrimaryNetwork = (Split-Path(Split-Path($PrimarySubnet.Id))).Replace("\","/")
   ```

- Vytvoření mapování sítě mezi primární virtuální sítí a virtuální sítí pro obnovení:

   ```azurepowershell
    #Create an ASR network mapping between the primary Azure virtual network and the recovery Azure virtual network
    $TempASRJob = New-AzRecoveryServicesAsrNetworkMapping -AzureToAzure -Name "A2AEusToWusNWMapping" -PrimaryFabric $PrimaryFabric -PrimaryAzureNetworkId $EastUSPrimaryNetwork -RecoveryFabric $RecoveryFabric -RecoveryAzureNetworkId $WestUSRecoveryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
    Write-Output $TempASRJob.State
   ```

- Vytvořit mapování sítě pro opačný směr (zpětný chod zezadu):

    ```azurepowershell
    #Create an ASR network mapping for fail back between the recovery Azure virtual network and the primary Azure virtual network
    $TempASRJob = New-AzRecoveryServicesAsrNetworkMapping -AzureToAzure -Name "A2AWusToEusNWMapping" -PrimaryFabric $RecoveryFabric -PrimaryAzureNetworkId $WestUSRecoveryNetwork -RecoveryFabric $PrimaryFabric -RecoveryAzureNetworkId $EastUSPrimaryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
    Write-Output $TempASRJob.State
    ```

## <a name="replicate-azure-virtual-machine"></a>Replikace virtuálního počítače Azure

Replikujte virtuální počítač Azure pomocí **spravovaných disků**.

```azurepowershell
#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OsDisk
$OSdiskId = $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data disk
$datadiskId1 = $vm.StorageProfile.DataDisks[0].ManagedDisk.Id
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType

$DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

Replikujte virtuální počítač Azure pomocí **nespravovaných disků**.

```azurepowershell
#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#Disk replication configuration for the OS disk
$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $OSDiskVhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Disk replication configuration for data disk
$DataDisk1ReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $DataDisk1VhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State
```

Jakmile bude operace počáteční replikace úspěšná, data virtuálního počítače se replikují do oblasti obnovení.

Proces replikace začíná počátečním nastavením kopie replikačních disků virtuálního počítače v oblasti obnovení. Tato fáze se nazývá počáteční fáze replikace.

Počáteční replikace služby AFter dokončena, replikace se přesune do fáze synchronizace rozdílů. V tomto okamžiku je virtuální počítač chráněn a lze na něm provést testovací operaci převzetí služeb při selhání. Stav replikace replikované položky představující virtuální počítač přejde do **chráněného** stavu po dokončení počáteční replikace.

Sledujte stav replikace a stav replikace pro virtuální počítač získáním podrobností o odpovídající položkě chráněné replikaci.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

```
FriendlyName ProtectionState ReplicationHealth
------------ --------------- -----------------
AzureDemoVM  Protected       Normal
```

## <a name="do-a-test-failover-validate-and-cleanup-test-failover"></a>Provést převzetí služeb při selhání, ověření a ověření testu selhání testu selhání testu

Po replikaci virtuálního počítače dosáhl chráněného stavu, test převzetí služeb při selhání operace lze provést na virtuálním počítači (na replikaci chráněné položky virtuálního počítače).

```azurepowershell
#Create a separate network for test failover (not connected to my DR network)
$TFOVnet = New-AzVirtualNetwork -Name "a2aTFOvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.3.0.0/16"

Add-AzVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $TFOVnet -AddressPrefix "10.3.0.0/20" | Set-AzVirtualNetwork

$TFONetwork= $TFOVnet.Id
```

Proveďte zkušební převzetí služeb při selhání.

```azurepowershell
$ReplicationProtectedItem = Get-AzRecoveryServicesAsrReplicationProtectedItem -FriendlyName "AzureDemoVM" -ProtectionContainer $PrimaryProtContainer

$TFOJob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -AzureVMNetworkId $TFONetwork -Direction PrimaryToRecovery
```

Počkejte na dokončení operace převzetí služeb při selhání testu.

```azurepowershell
Get-AzRecoveryServicesAsrJob -Job $TFOJob
```

```Output
Name             : 3dcb043e-3c6d-4e0e-a42e-8d4245668547
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/3dcb043e-3c6d-4e0e-a42e-8d4245668547
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : TestFailover
DisplayName      : Test failover
ClientRequestId  : 1ef8515b-b130-4452-a44d-91aaf071931c ActivityId: 907bb2bc-ebe6-4732-8b66-77d0546eaba8
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:29:43 AM
EndTime          : 4/25/2018 4:33:06 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   :
Tasks            : {Prerequisites check for test failover, Create test virtual machine, Preparing the virtual machine, Start the virtual machine}
Errors           : {}
```

Po úspěšném dokončení úlohy převzetí služeb při selhání testu se můžete připojit k testu se nezdařilo přes virtuální počítač a ověřit převzetí služeb při selhání testu.

Po dokončení testování v testu se nezdařilo přes virtuální počítač, vyčistit zkušební kopii spuštěním operace převzetí služeb při selhání testu vyčištění. Tato operace odstraní testovací kopii virtuálního počítače, který byl vytvořen zkušební převzetí služeb při selhání.

```azurepowershell
$Job_TFOCleanup = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-AzRecoveryServicesAsrJob -Job $Job_TFOCleanup | Select State
```

```Output
State
-----
Succeeded
```

## <a name="fail-over-to-azure"></a>Převzetí služeb při selhání do Azure

Převzetí služeb při selhání virtuálního počítače do určitého bodu obnovení.

```azurepowershell
$RecoveryPoints = Get-AzRecoveryServicesAsrRecoveryPoint -ReplicationProtectedItem $ReplicationProtectedItem

#The list of recovery points returned may not be sorted chronologically and will need to be sorted first, in order to be able to find the oldest or the latest recovery points for the virtual machine.
"{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[-1].RecoveryPointTime
```

```Output
CrashConsistent 4/24/2018 11:10:25 PM
```

```azurepowershell
#Start the fail over job
$Job_Failover = Start-AzRecoveryServicesAsrUnplannedFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[-1]

do {
        $Job_Failover = Get-AzRecoveryServicesAsrJob -Job $Job_Failover;
        sleep 30;
} while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))

$Job_Failover.State
```

```Output
Succeeded
```

Pokud je úloha převzetí služeb při selhání úspěšná, můžete operaci převzetí služeb při selhání potvrdit.

```azurepowershell
$CommitFailoverJOb = Start-AzRecoveryServicesAsrCommitFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-AzRecoveryServicesAsrJob -Job $CommitFailoverJOb
```

```Output
Name             : 58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : CommitFailover
DisplayName      : Commit
ClientRequestId  : 10a95d6c-359e-4603-b7d9-b7ee3317ce94 ActivityId: 8751ada4-fc42-4238-8de6-a82618408fcf
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:50:58 AM
EndTime          : 4/25/2018 4:51:01 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   :
Tasks            : {Prerequisite check, Commit}
Errors           : {}
```

## <a name="reprotect-and-fail-back-to-the-source-region"></a>Znovu chránit a obnovit jeho obnovení do zdrojové oblasti

Po převzetí služeb při selhání, když jste připraveni k návratu do původní oblasti, spusťte reverzní replikaci pro položku chráněnou replikací pomocí `Update-AzRecoveryServicesAsrProtectionDirection` rutiny.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage
```

```azurepowershell
#Use the recovery protection container, new cache storage account in West US and the source region VM resource group
Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure
-ProtectionContainerMapping $WusToEusPCMapping -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.ResourceId
```

Po dokončení opětovné ochrany můžete převzetí služeb při selhání v opačném směru, západní USA na východ USA a obnovit služby pro oblast zdroje.

## <a name="disable-replication"></a>Zákaz replikace

Replikaci můžete `Remove-AzRecoveryServicesAsrReplicationProtectedItem` zakázat pomocí rutiny.

```azurepowershell
Remove-AzRecoveryServicesAsrReplicationProtectedItem -ReplicationProtectedItem $ReplicatedItem
```

## <a name="next-steps"></a>Další kroky

Zobrazte [odkaz na Azure Site Recovery PowerShell a](/powershell/module/az.RecoveryServices) zjistěte, jak můžete dělat další úkoly, jako je vytváření plánů obnovení a testování převzetí služeb při selhání plánů obnovení pomocí prostředí PowerShell.
