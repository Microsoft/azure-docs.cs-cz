---
title: Zotavení po havárii pro virtuální počítače Azure pomocí Azure PowerShell a Azure Site Recovery
description: Přečtěte si, jak nastavit zotavení po havárii pro virtuální počítače Azure s Azure Site Recovery pomocí Azure PowerShell.
services: site-recovery
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 3/29/2019
ms.author: sutalasi
ms.openlocfilehash: 1570bd9dfa62caa749d5a3983b93c2555be058ec
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348725"
---
# <a name="set-up-disaster-recovery-for-azure-virtual-machines-using-azure-powershell"></a>Nastavení zotavení po havárii pro virtuální počítače Azure s využitím Azure PowerShellu

V tomto článku uvidíte, jak nastavit a otestovat zotavení po havárii pro virtuální počítače Azure pomocí Azure PowerShell.

Získáte informace o těchto tématech:

> [!div class="checklist"]
> - Vytvořte trezor služby Recovery Services.
> - Nastavte kontext trezoru pro relaci PowerShellu.
> - Připraví trezor pro zahájení replikace virtuálních počítačů Azure.
> - Vytvořte mapování sítě.
> - Vytvořte účty úložiště pro replikaci virtuálních počítačů do.
> - Replikace virtuálních počítačů Azure do oblasti obnovení pro zotavení po havárii.
> - Proveďte test převzetí služeb při selhání, ověření a vyčištění testovacího převzetí služeb při selhání.
> - Převzetí služeb při selhání v oblasti obnovení.

> [!NOTE]
> Ne všechny možnosti scénářů, které jsou k dispozici prostřednictvím portálu, mohou být k dispozici prostřednictvím Azure PowerShell. Některé možnosti scénáře nejsou aktuálně podporované prostřednictvím Azure PowerShell:
> - Možnost určit, že všechny disky ve virtuálním počítači by měly být replikovány bez explicitního určení všech disků virtuálního počítače.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Než začnete, potřebujete:
- Ujistěte se, že rozumíte [komponentám a architektuře řešení](azure-to-azure-architecture.md).
- Zkontrolujte [požadavky na podporu](azure-to-azure-support-matrix.md) pro všechny komponenty.
- Máte `Az` modul Azure PowerShell. Pokud potřebujete nainstalovat nebo upgradovat Azure PowerShell, postupujte podle pokynů v tomto [Průvodci a nainstalujte a nakonfigurujte Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Přihlaste se k předplatnému Microsoft Azure.

Přihlaste se k předplatnému Azure pomocí `Connect-AzAccount` rutiny.

```azurepowershell
Connect-AzAccount
```

Vyberte své předplatné Azure. Pomocí `Get-AzSubscription` rutiny Získejte seznam předplatných Azure, ke kterým máte přístup. Vyberte předplatné Azure, se kterým chcete pracovat pomocí `Set-AzContext` rutiny.

```azurepowershell
Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

## <a name="get-details-of-the-virtual-machine-to-be-replicated"></a>Získat podrobnosti o virtuálním počítači, který se má replikovat

V tomto článku se virtuální počítač v Východní USA oblasti replikuje do Západní USA 2 oblasti a obnoví se. Replikovaný virtuální počítač má disk s operačním systémem a jeden datový disk. Název virtuálního počítače použitý v příkladu je `AzureDemoVM` .

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

Získejte podrobnosti disku pro disky virtuálního počítače. Podrobnosti o disku se použijí později při spuštění replikace pro virtuální počítač.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Vytvořte skupinu prostředků, ve které chcete vytvořit trezor Recovery Services.

> [!IMPORTANT]
> * Trezor služby Recovery Services a virtuální počítače, které jsou chráněny, musí být v různých umístěních Azure.
> * Skupina prostředků trezoru služby Recovery Services a virtuální počítače, které jsou chráněny, musí být v různých umístěních Azure.
> * Trezor služby Recovery Services a skupina prostředků, do které patří, můžou být ve stejném umístění Azure.

V příkladu v tomto článku je virtuální počítač chráněný v oblasti Východní USA. Oblast obnovení vybraná pro zotavení po havárii je Západní USA 2 oblast. Trezor služby Recovery Services a skupina prostředků trezoru jsou v oblasti obnovení Západní USA 2.

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

Vytvořte Trezor služby Recovery Services. V tomto příkladu `a2aDemoRecoveryVault` je ve západní USA 2 oblasti vytvořený trezor Recovery Services s názvem.

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

## <a name="set-the-vault-context"></a>Nastavte kontext trezoru.

Nastavte kontext trezoru pro použití v relaci PowerShellu. Po nastavení kontextu trezoru se operace Azure Site Recovery v relaci PowerShellu provádějí v kontextu vybraného trezoru.

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

U migrace z Azure do Azure můžete nastavit kontext trezoru na nově vytvořený trezor:

```azurepowershell
#Set the vault context for the PowerShell session.
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

## <a name="prepare-the-vault-to-start-replicating-azure-virtual-machines"></a>Příprava trezoru pro zahájení replikace virtuálních počítačů Azure

### <a name="create-a-site-recovery-fabric-object-to-represent-the-primary-source-region"></a>Vytvoření objektu prostředků infrastruktury Site Recovery pro reprezentaci primární (zdrojové) oblasti

Objekt Fabric v trezoru představuje oblast Azure. Vytvoří se primární objekt Fabric, který bude představovat oblast Azure, do které virtuální počítače chráněné do trezoru patří. V příkladu v tomto článku je virtuální počítač chráněný v oblasti Východní USA.

- V jednotlivých oblastech se dá vytvořit jenom jeden objekt Fabric.
- Pokud jste dříve povolili Site Recovery replikaci pro virtuální počítač v Azure Portal, Site Recovery vytvoří objekt Fabric automaticky. Pokud objekt Fabric pro oblast existuje, nemůžete vytvořit nový objekt.

Než začnete, pochopte, že Site Recovery operace se provádějí asynchronně. Když zahájíte operaci, odešle se Azure Site Recovery úloha a vrátí se objekt sledování úlohy. Pomocí objektu sledování úlohy můžete získat nejnovější stav úlohy ( `Get-AzRecoveryServicesAsrJob` ) a monitorovat stav operace.

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

Pokud jsou virtuální počítače z více oblastí Azure chráněny do stejného trezoru, vytvořte jeden objekt Fabric pro každou zdrojovou oblast Azure.

### <a name="create-a-site-recovery-fabric-object-to-represent-the-recovery-region"></a>Vytvoření objektu Site Recovery Fabric, který bude představovat oblast obnovení

Objekt prostředků infrastruktury obnovení představuje umístění Azure pro obnovení. Pokud dojde k převzetí služeb při selhání, virtuální počítače se replikují a obnoví do oblasti obnovení reprezentované prostředky infrastruktury pro obnovení. Oblast Azure pro obnovení použitou v tomto příkladu je Západní USA 2.

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

### <a name="create-a-site-recovery-protection-container-in-the-primary-fabric"></a>Vytvoření kontejneru Site Recovery Protection v primárních prostředcích infrastruktury

Kontejner ochrany je kontejner používaný k seskupení replikovaných položek v rámci prostředků infrastruktury.

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

### <a name="create-a-site-recovery-protection-container-in-the-recovery-fabric"></a>Vytvoření kontejneru ochrany Site Recovery v prostředcích infrastruktury obnovení

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

#### <a name="fabric-and-container-creation-when-enabling-zone-to-zone-replication"></a>Prostředky infrastruktury a vytváření kontejnerů při povolování replikace zóny do zóny

Při povolování zóny na replikaci zóny se vytvoří jenom jeden prostředek infrastruktury. Ale budou existovat dva kontejnery. Za předpokladu, že je oblast Západní Evropa, použijte k získání primárních a ochranných kontejnerů následující příkazy:

```azurepowershell
$primaryProtectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $fabric -Name "asr-a2a-default-westeurope-container"
$recoveryPprotectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $fabric -Name "asr-a2a-default-westeurope-t-container"
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

### <a name="create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container"></a>Vytvoření mapování kontejneru ochrany mezi primárním kontejnerem a kontejnerem ochrany při obnovení

Mapování kontejneru ochrany mapuje primární kontejner ochrany pomocí kontejneru ochrany obnovení a zásad replikace. Vytvořte jedno mapování pro každou zásadu replikace, kterou použijete k replikaci virtuálních počítačů mezi dvojicí kontejneru ochrany.

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

#### <a name="protection-container-mapping-creation-when-enabling-zone-to-zone-replication"></a>Vytvoření mapování kontejneru ochrany při povolování replikace zóny do zóny

Při povolování zóny pro replikaci zóny použijte následující příkaz k vytvoření mapování kontejneru ochrany. Za předpokladu, že je oblast Západní Evropa, bude příkaz –

```azurepowershell
$protContainerMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $PrimprotectionContainer -Name "westeurope-westeurope-24-hour-retention-policy-s"
```

### <a name="create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover"></a>Vytvořit mapování kontejneru ochrany pro navrácení služeb po obnovení (reverzní replikace po převzetí služeb při selhání)

Po převzetí služeb při selhání budete moct po převzetí služeb při selhání virtuální počítač přenést zpátky do původní oblasti Azure a provést navrácení služeb po obnovení. Pro navrácení služeb po obnovení je virtuální počítač, který převezme služby při selhání, zpětně replikován z oblasti převzetí služeb při selhání do původní oblasti. Pro zpětnou replikaci role v části původní oblast a přepínač oblasti obnovení. Původní oblast se teď stala novou oblastí obnovení a původní oblast obnovení se teď stal primární oblastí. Mapování kontejneru ochrany pro zpětnou replikaci představuje přepínací role původních oblastí a oblasti obnovení.

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

Účet úložiště mezipaměti je účet standardního úložiště ve stejné oblasti Azure jako virtuální počítač, který se replikuje. Účet úložiště mezipaměti se používá k dočasnému uchovávání změn replikace, než se změny přesunou do oblasti obnovení Azure. Můžete zvolit, že není nutné, abyste pro různé disky virtuálního počítače určili různé účty úložiště mezipaměti.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$EastUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestorage" -ResourceGroupName "A2AdemoRG" -Location 'East US' -SkuName Standard_LRS -Kind Storage
```

U virtuálních počítačů, které **nepoužívají spravované disky** , je cílovým účtem úložiště účet úložiště v oblasti obnovení, na které se replikují disky virtuálního počítače. Cílový účet úložiště může být buď účet standardního úložiště, nebo účet Premium Storage. Vyberte druh účtu úložiště vyžadovaný v závislosti na rychlosti změny dat (rychlost zápisu v/v) pro disky a Azure Site Recovery podporovaná omezení změn pro typ úložiště.

```azurepowershell
#Create Target storage account in the recovery region. In this case a Standard Storage account
$WestUSTargetStorageAccount = New-AzStorageAccount -Name "a2atargetstorage" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -SkuName Standard_LRS -Kind Storage
```

## <a name="create-network-mappings"></a>Vytvořit mapování sítě

Mapování sítě mapuje virtuální sítě v primární oblasti na virtuální sítě v oblasti obnovení. Mapování sítě určuje virtuální síť Azure v oblasti obnovení, na kterou by měl virtuální počítač v primární virtuální síti převzít služby při selhání. Jedna virtuální síť Azure se dá namapovat jenom na jednu virtuální síť Azure v oblasti obnovení.

- Vytvořte virtuální síť Azure v oblasti obnovení, pro kterou převezmete služby při selhání:

   ```azurepowershell
    #Create a Recovery Network in the recovery region
    $WestUSRecoveryVnet = New-AzVirtualNetwork -Name "a2arecoveryvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.0.0.0/16"

    Add-AzVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $WestUSRecoveryVnet -AddressPrefix "10.0.0.0/20" | Set-AzVirtualNetwork

    $WestUSRecoveryNetwork = $WestUSRecoveryVnet.Id
   ```

- Načtěte primární virtuální síť. Virtuální síť, ke které je virtuální počítač připojený:

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

- Vytvořit mapování sítě mezi primární virtuální sítí a virtuální sítí pro obnovení:

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

- Vytvořit mapování sítě pro zpětný směr (navrácení služeb po obnovení):

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

Replikujte virtuální počítač Azure se **spravovanými disky**.

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

$DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

Replikujte virtuální počítač Azure s **nespravovanými disky**.

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

Po úspěšném dokončení operace spuštění replikace se data virtuálního počítače replikují do oblasti obnovení.

Proces replikace začíná prvním vynásobením kopie replikačních disků virtuálního počítače v oblasti obnovení. Tato fáze se nazývá fáze počáteční replikace.

Po dokončení počáteční replikace se replikace přesune do fáze rozdílové synchronizace. V tuto chvíli je virtuální počítač chráněný a na něm může být prováděna operace testovacího převzetí služeb při selhání. Stav replikace replikované položky představující virtuální počítač přejde do **chráněného** stavu po dokončení počáteční replikace.

Sledujte stav replikace a stav replikace pro virtuální počítač tím, že získáte podrobné informace o položce chráněné replikace, která je pro ni odpovídající.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

```
FriendlyName ProtectionState ReplicationHealth
------------ --------------- -----------------
AzureDemoVM  Protected       Normal
```

## <a name="do-a-test-failover-validate-and-cleanup-test-failover"></a>Proveďte test převzetí služeb při selhání, ověření a vyčištění testovacího převzetí služeb při selhání

Po dokončení replikace virtuálního počítače do chráněného stavu se může provést testovací operace převzetí služeb při selhání na virtuálním počítači (na chráněné položce replikace virtuálního počítače).

```azurepowershell
#Create a separate network for test failover (not connected to my DR network)
$TFOVnet = New-AzVirtualNetwork -Name "a2aTFOvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.3.0.0/16"

Add-AzVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $TFOVnet -AddressPrefix "10.3.0.0/20" | Set-AzVirtualNetwork

$TFONetwork= $TFOVnet.Id
```

Proveďte test převzetí služeb při selhání.

```azurepowershell
$ReplicationProtectedItem = Get-AzRecoveryServicesAsrReplicationProtectedItem -FriendlyName "AzureDemoVM" -ProtectionContainer $PrimaryProtContainer

$TFOJob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -AzureVMNetworkId $TFONetwork -Direction PrimaryToRecovery
```

Počkejte, než se dokončí operace testovacího převzetí služeb při selhání.

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

Po úspěšném dokončení úlohy testovacího převzetí služeb při selhání se můžete připojit k testu při selhání virtuálního počítače a ověřit testovací převzetí služeb při selhání.

Po dokončení testování u testu při selhání virtuálního počítače vyčistěte testovací kopii spuštěním operace vyčištění testovacího převzetí služeb při selhání. Tato operace odstraní testovací kopii virtuálního počítače, který byl vytvořen testovacím převzetím služeb při selhání.

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

Převzetí služeb při selhání virtuálního počítače na určitý bod obnovení.

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

Po úspěšném provedení úlohy převzetí služeb při selhání můžete operaci převzetí služeb při selhání potvrdit.

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

## <a name="reprotect-and-fail-back-to-the-source-region"></a>Opětovné zapnutí ochrany a navrácení služeb po obnovení do zdrojové oblasti

Až budete převzetí služeb při selhání připraveni zpátky do původní oblasti, spusťte zpětnou replikaci pro položku chráněnou replikace pomocí `Update-AzRecoveryServicesAsrProtectionDirection` rutiny.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage
```

```azurepowershell
#Use the recovery protection container, new cache storage account in West US and the source region VM resource group
Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure
-ProtectionContainerMapping $WusToEusPCMapping -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.ResourceId
```

Po dokončení ochrany můžete převzít služby při selhání v opačném směru, Západní USA Východní USA a navrácení služeb po obnovení do zdrojové oblasti.

## <a name="disable-replication"></a>Zákaz replikace

Replikaci můžete zakázat pomocí `Remove-AzRecoveryServicesAsrReplicationProtectedItem` rutiny.

```azurepowershell
Remove-AzRecoveryServicesAsrReplicationProtectedItem -ReplicationProtectedItem $ReplicationProtectedItem
```

## <a name="next-steps"></a>Další kroky

Podívejte se na [odkaz Azure Site Recovery PowerShell](/powershell/module/az.RecoveryServices) , kde se dozvíte, jak provádět další úkoly, jako je vytváření plánů obnovení a testování převzetí služeb při selhání v prostředí PowerShell.
