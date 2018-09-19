---
title: Azure Site Recovery – instalace a testování zotavení po havárii pro virtuální počítače Azure pomocí Azure Powershellu | Dokumentace Microsoftu
description: Zjistěte, jak nastavit zotavení po havárii pro virtuální počítače Azure pomocí Azure Site Recovery pomocí Azure Powershellu.
services: site-recovery
author: bsiva
manager: abhemraj
editor: raynew
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: bsiva
ms.openlocfilehash: dbc092a9a6984a74cb59f287f12b06892c68fe4a
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46296885"
---
# <a name="set-up-disaster-recovery-for-azure-virtual-machines-using-azure-powershell"></a>Nastavení zotavení po havárii pro virtuální počítače Azure pomocí Azure Powershellu

V tomto článku můžete zjistit, jak nastavit a testování zotavení po havárii pro virtuální počítače Azure pomocí Azure Powershellu.

Získáte informace o těchto tématech:

> [!div class="checklist"]
> - Vytvořte trezor služby Recovery Services.
> - Nastavte kontext trezoru pro relaci Powershellu.
> - Příprava úložiště ke spuštění replikace virtuálních počítačů Azure.
> - Vytvořte mapování sítě.
> - Vytvoření účtů úložiště pro replikaci virtuálních počítačů.
> - Replikace virtuálních počítačů Azure do oblasti pro zotavení pro zotavení po havárii.
> - Provést testovací převzetí služeb, ověření a vyčištění testovacího převzetí služeb při selhání.
> - Převzetí služeb při selhání na oblast pro obnovení.

> [!NOTE]
> Ne všechny scénáře možnosti jsou dostupné na portálu může být k dispozici prostřednictvím Azure Powershellu. Zde jsou některé možnosti scénář není aktuálně podporováno prostřednictvím Azure Powershellu:
> - Možnost replikace virtuálních počítačů s Azure, které používají spravované disky.
> - Možnost určit, že všechny disky na virtuálním počítači by měla být replikována bez nutnosti explicitně určovat jednotlivých disků virtuálního počítače.  

## <a name="prerequisites"></a>Požadavky

Než začnete, potřebujete:
- Ujistěte se, že rozumíte [komponentám a architektuře řešení](azure-to-azure-architecture.md).
- Zkontrolujte [požadavky na podporu](azure-to-azure-support-matrix.md) pro všechny komponenty.
- Máte verzi 5.7.0 nebo větší modul AzureRm Powershellu. Pokud potřebujete instalaci nebo upgrade prostředí Azure PowerShell, postupujte podle to [příručce k instalaci a konfiguraci prostředí Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Přihlaste se k předplatnému Microsoft Azure

Přihlaste se ke svému předplatnému Azure pomocí rutiny Connect-AzureRmAccount

```azurepowershell
Connect-AzureRmAccount
```
Vyberte své předplatné Azure. Použijte rutinu Get-AzureRmSubscription zobrazíte seznam předplatných Azure, ke kterým máte přístup k. Vyberte předplatné Azure, postup při použití rutiny Select-AzureRmSubscription.

```azurepowershell
Select-AzureRmSubscription -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

## <a name="get-details-of-the-virtual-machines-to-be-replicated"></a>Získat podrobnosti o virtuálních počítačů na replikaci

V příkladu v tomto článku se virtuální počítač v oblasti USA – východ replikují do a obnovit v oblasti západní USA 2. Virtuální počítač replikuje se virtuální počítače mají disk s operačním systémem a jeden datový disk. Název virtuálního počítače, v tomto příkladu je AzureDemoVM.

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzureRmVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

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

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Vytvořte skupinu prostředků, ve kterém chcete vytvořit trezor služby Recovery Services.

> [!IMPORTANT]
> * Trezor služby Recovery services a virtuální počítače, který je chráněn, musí být v různých oblastech Azure.
> * Skupina prostředků trezoru služby Recovery services a virtuální počítače, který je chráněn, musí být v různých oblastech Azure.
> * Trezor služby Recovery services a skupinu prostředků, do které patří, může být ve stejném umístění Azure.

V příkladu v tomto článku je chráněný virtuální počítač v oblasti USA – východ. Oblast pro obnovení vybraný pro zotavení po havárii je oblast západní USA 2. Trezor služby recovery services a skupinu prostředků, úložiště, jsou v oblasti pro zotavení (USA – západ 2)

```azurepowershell
#Create a resource group for the recovery services vault in the recovery Azure region
New-AzureRmResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"
```
```
ResourceGroupName : a2ademorecoveryrg
Location          : westus2
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg
```

Vytvoření trezoru služby Recovery services. V příkladu níže trezor služby Recovery Services s názvem a2aDemoRecoveryVault se vytvoří v oblasti západní USA 2.

```azurepowershell
#Create a new Recovery services vault in the recovery region
$vault = New-AzureRmRecoveryServicesVault -Name "a2aDemoRecoveryVault" -ResourceGroupName "a2ademorecoveryrg" -Location "West US 2"

Write-Output $vault
```
```
Name              : a2aDemoRecoveryVault
ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoRecoveryVault
Type              : Microsoft.RecoveryServices/vaults
Location          : westus2
ResourceGroupName : a2ademorecoveryrg
SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```
## <a name="set-the-vault-context"></a>Nastavte kontext trezoru

> [!TIP]
> Modul Powershellu pro Azure Site Recovery (AzureRm.RecoveryServices.SiteRecovery modul) obsahuje aliasy snadno se používá u většiny rutin. Rutiny v modulu podobu  *\<operace >-**na AzureRmRecoveryServicesAsr**\<objektu >* a mají ekvivalentní aliasy, které podobu  *\<Operace >-**Azure Site Recovery**\<objektu >*. Tento článek používá rutiny aliasy pro snadnější čtení.

Nastavte kontext trezoru pro použití v relaci Powershellu. Provedete to tak, stáhněte si soubor nastavení trezoru a importujte stažený soubor do relace prostředí PowerShell nastavte kontext trezoru.

Po nastavení, následné operace Azure Site Recovery v relaci Powershellu se provádějí v kontextu vybraného trezoru.

 ```azurepowershell
#Download the vault settings file for the vault.
$Vaultsettingsfile = Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteRecovery -Path C:\users\user\Documents\

#Import the downloaded vault settings file to set the vault context for the PowerShell session.
Import-AzureRmRecoveryServicesAsrVaultSettingsFile -Path $Vaultsettingsfile.FilePath

```
```
ResourceName         ResourceGroupName ResourceNamespace          ResouceType
------------         ----------------- -----------------          -----------
a2aDemoRecoveryVault a2ademorecoveryrg Microsoft.RecoveryServices Vaults     
```

```azurepowershell
#Delete the downloaded vault settings file
Remove-Item -Path $Vaultsettingsfile.FilePath
```
## <a name="prepare-the-vault-to-start-replicating-azure-virtual-machines"></a>Příprava úložiště ke spuštění replikace virtuálních počítačů Azure

####<a name="1-create-a-site-recovery-fabric-object-to-represent-the-primarysource-region"></a>1. Vytvoření objektu infrastruktury Site Recovery k reprezentaci primary(source) oblasti

Objekt prostředků infrastruktury v trezoru představuje oblasti Azure. Objekt primární prostředků infrastruktury je fabric objekt vytvořený představující oblast Azure, do které patří virtuální počítače chráněné v trezoru. V příkladu v tomto článku je chráněný virtuální počítač v oblasti USA – východ.

> [!NOTE]
> Operace Azure Site Recovery se provedl asynchronně. Při zahájení operace odeslání úlohy služby Azure Site Recovery a úlohy sledování objektu se vrátí. Použijte tuto úlohu sledování objektů, chcete-li získat nejnovější stav úlohy (Get-ASRJob) a monitorovat stav operace.

```azurepowershell
#Create Primary ASR fabric
$TempASRJob = New-ASRFabric -Azure -Location 'East US'  -Name "A2Ademo-EastUS"

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State

$PrimaryFabric = Get-AsrFabric -Name "A2Ademo-EastUS"
```
Pokud do stejného trezoru jsou chráněné virtuální počítače v několika oblastech Azure, vytvořte jeden objekt prostředků infrastruktury pro každý zdroj oblasti Azure.

####<a name="2-create-a-site-recovery-fabric-object-to-represent-the-recovery-region"></a>2. Vytvoření objektu infrastruktury Site Recovery představující oblast pro obnovení

Objekt prostředků infrastruktury pro zotavení představuje obnovení umístění Azure. Virtuální počítače bude replikován a obnovit (v případě selhání) reprezentována prostředků infrastruktury pro zotavení oblast pro obnovení. Obnovení Azure oblasti používané v tomto příkladu je západní USA 2.

```azurepowershell
#Create Recovery ASR fabric
$TempASRJob = New-ASRFabric -Azure -Location 'West US 2'  -Name "A2Ademo-WestUS"

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State

$RecoveryFabric = Get-AsrFabric -Name "A2Ademo-WestUS"

```

####<a name="3-create-a-site-recovery-protection-container-in-the-primary-fabric"></a>3. Vytvoření kontejneru ochrany Site Recovery v prostředcích infrastruktury primární

Kontejner ochrany je kontejner, umožňuje seskupení replikované položky v rámci prostředků infrastruktury.

```azurepowershell
#Create a Protection container in the primary Azure region (within the Primary fabric)
$TempASRJob = New-AzureRmRecoveryServicesAsrProtectionContainer -InputObject $PrimaryFabric -Name "A2AEastUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

Write-Output $TempASRJob.State

$PrimaryProtContainer = Get-ASRProtectionContainer -Fabric $PrimaryFabric -Name "A2AEastUSProtectionContainer"
```
####<a name="4-create-a-site-recovery-protection-container-in-the-recovery-fabric"></a>4. Vytvoření kontejneru ochrany Site Recovery v prostředcích infrastruktury pro zotavení

```azurepowershell
#Create a Protection container in the recovery Azure region (within the Recovery fabric)
$TempASRJob = New-AzureRmRecoveryServicesAsrProtectionContainer -InputObject $RecoveryFabric -Name "A2AWestUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"

Write-Output $TempASRJob.State

$RecoveryProtContainer = Get-ASRProtectionContainer -Fabric $RecoveryFabric -Name "A2AWestUSProtectionContainer"
```

####<a name="5-create-a-replication-policy"></a>5. Vytvoření zásady replikace

```azurepowershell
#Create replication policy
$TempASRJob = New-ASRPolicy -AzureToAzure -Name "A2APolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State

$ReplicationPolicy = Get-ASRPolicy -Name "A2APolicy"
```
####<a name="6-create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container"></a>6. Vytvoření mapování kontejnerů ochrany mezi primárními a obnovovacími kontejner ochrany

Mapování kontejnerů ochrany mapuje primární ochranu kontejneru se kontejner ochrany pro obnovení a zásady replikace. Vytvořte jedno mapování pro každou zásadu replikace, které budete používat pro virtuální počítače replikovat mezi dvěma kontejneru ochrany.

```azurepowershell
#Create Protection container mapping between the Primary and Recovery Protection Containers with the Replication policy
$TempASRJob = New-ASRProtectionContainerMapping -Name "A2APrimaryToRecovery" -Policy $ReplicationPolicy -PrimaryProtectionContainer $PrimaryProtContainer -RecoveryProtectionContainer $RecoveryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State

$EusToWusPCMapping = Get-ASRProtectionContainerMapping -ProtectionContainer $PrimaryProtContainer -Name "A2APrimaryToRecovery"
```

####<a name="7-create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover"></a>7. Vytvoření mapování kontejnerů ochrany pro navrácení služeb po obnovení (zpětná replikace po převzetí služeb)

Po selhání, když budete chtít převeďte se virtuální počítač zpět do původní oblasti Azure, které navrácení služeb po obnovení. K navrácení služeb po obnovení, se přes virtuální počítač je zpětně replikované z se v oblasti, aby se původní oblast. Pro zpětnou replikaci přepnout role původní oblasti a oblasti pro zotavení. Původní oblast bude nyní nové oblasti pro zotavení a co byl původně oblasti obnovení teď bude primární oblasti. Mapování kontejnerů ochrany na zpětnou replikaci představuje přepnulo role oblastech původní a obnovení.

```azurepowershell
#Create Protection container mapping (for failback) between the Recovery and Primary Protection Containers with the Replication policy
$TempASRJob = New-ASRProtectionContainerMapping -Name "A2ARecoveryToPrimary" -Policy $ReplicationPolicy -PrimaryProtectionContainer $RecoveryProtContainer -RecoveryProtectionContainer $PrimaryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State
```

## <a name="create-cache-storage-accounts-and-target-storage-accounts"></a>Vytvoření účtů úložiště mezipaměti a cílových účtů úložiště

Účet úložiště mezipaměti je účet úložiště úrovně standard ve stejné oblasti Azure jako virtuální počítač replikuje. Účet úložiště mezipaměti se používá k ukládání replikace změn dočasně, předtím, než se změny se přesouvají na obnovení oblasti Azure. Je možné (ale nemusí) specifikovat účty úložiště mezipaměti různé u různých disků virtuálního počítače.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$EastUSCacheStorageAccount = New-AzureRmStorageAccount -Name "a2acachestorage" -ResourceGroupName "A2AdemoRG" -Location 'East US' -SkuName Standard_LRS -Kind Storage
```

Pro virtuální počítače **nepoužívá spravované disky**, cílový účet úložiště se účty úložiště v oblasti obnovení, které jsou replikovat disky virtuálního počítače. Cílový účet úložiště může být účet standard storage nebo účtu služby premium storage. Vyberte druh je potřeba účet úložiště podle četnost změn dat (rychlosti vstupně-výstupních operací zápisu) pro disky a omezení četnosti změn podporované v Azure Site Recovery pro tento typ úložiště.

```azurepowershell
#Create Target storage account in the recovery region. In this case a Standard Storage account
$WestUSTargetStorageAccount = New-AzureRmStorageAccount -Name "a2atargetstorage" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -SkuName Standard_LRS -Kind Storage

```

## <a name="create-network-mappings"></a>Vytvořte mapování sítě

Mapování sítě zajišťuje mapování virtuálních sítí v primární oblasti do virtuálních sítí v oblasti obnovení. Mapování sítě určuje virtuální síť Azure v oblasti obnovení, kterou virtuální počítač v primární virtuální sítě by měl převzetí služeb při selhání. Jednu virtuální síť Azure lze mapovat na pouze jednu virtuální síť Azure v oblasti obnovení.

- Vytvořte virtuální síť Azure v oblasti obnovení pro převzetí služeb při selhání

   ```azurepowershell
    #Create a Recovery Network in the recovery region
    $WestUSRecoveryVnet = New-AzureRmVirtualNetwork -Name "a2arecoveryvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.0.0.0/16"

    Add-AzureRmVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $WestUSRecoveryVnet -AddressPrefix "10.0.0.0/20" | Set-AzureRmVirtualNetwork

    $WestUSRecoveryNetwork = $WestUSRecoveryVnet.Id
   ```
- Načíst primární virtuální sítě (virtuální sítě, virtuální počítač je připojený k)
   ```azurepowershell
    #Retrieve the virtual network that the virtual machine is connected to

    #Get first network interface card(nic) of the virtual machine
    $SplitNicArmId = $VM.NetworkProfile.NetworkInterfaces[0].Id.split("/")

    #Extract resource group name from the ResourceId of the nic
    $NICRG = $SplitNicArmId[4]

    #Extract resource name from the ResourceId of the nic
    $NICname = $SplitNicArmId[-1]

    #Get network interface details using the extracted resource group name and resourec name
    $NIC = Get-AzureRmNetworkInterface -ResourceGroupName $NICRG -Name $NICname

    #Get the subnet ID of the subnet that the nic is connected to
    $PrimarySubnet = $NIC.IpConfigurations[0].Subnet

    # Extract the resource ID of the Azure virtual network the nic is connected to from the subnet ID
    $EastUSPrimaryNetwork = (Split-Path(Split-Path($PrimarySubnet.Id))).Replace("\","/")  
   ```
- Vytvořit mapování sítě mezi primární virtuální sítí a virtuální síť pro obnovení
   ```azurepowershell
    #Create an ASR network mapping between the primary Azure virtual network and the recovery Azure virtual network
    $TempASRJob = New-ASRNetworkMapping -AzureToAzure -Name "A2AEusToWusNWMapping" -PrimaryFabric $PrimaryFabric -PrimaryAzureNetworkId $EastUSPrimaryNetwork -RecoveryFabric $RecoveryFabric -RecoveryAzureNetworkId $WestUSRecoveryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-ASRJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
    Write-Output $TempASRJob.State

   ```
- Vytvoření mapování sítí pro obrátit směr (navrácení služeb po obnovení)
    ```azurepowershell
    #Create an ASR network mapping for failback between the recovery Azure virtual network and the primary Azure virtual network
    $TempASRJob = New-ASRNetworkMapping -AzureToAzure -Name "A2AWusToEusNWMapping" -PrimaryFabric $RecoveryFabric -PrimaryAzureNetworkId $WestUSRecoveryNetwork -RecoveryFabric $PrimaryFabric -RecoveryAzureNetworkId $EastUSPrimaryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-ASRJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
    Write-Output $TempASRJob.State
    ```

## <a name="replicate-azure-virtual-machine"></a>Replikace virtuálních počítačů Azure

Replikovat virtuální počítač Azure s **spravované disky**.

```azurepowershell

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzureRmResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OsDisk
$OSdiskId =  $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -managed -LogStorageAccountId $storageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $ RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryOSDiskAccountType $RecoveryOSDiskAccountType

# Data disk
$datadiskId1  = $vm.StorageProfile.DataDisks[0].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[0]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[0]. StorageAccountType

$DataDisk1ReplicationConfig  = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -managed -LogStorageAccountId $storageAccount.Id `
         -DiskId $datadiskId1 -RecoveryResourceGroupId  $ RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig


#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId

```

Replikovat virtuální počítač Azure s **nespravované disky**.

```azurepowershell
#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#Disk replication configuration for the OS disk
$OSDiskReplicationConfig = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $OSDiskVhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Disk replication configuration for data disk
$DataDisk1ReplicationConfig = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $DataDisk1VhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzureRmResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.  
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}


#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State
```

Po úspěšném spuštění operace replikace dat virtuálního počítače se replikují do oblasti pro zotavení.

Proces replikace se spustí na počáteční synchronizace replik indexů kopii replikační disky virtuálního počítače v oblasti obnovení. Tato fáze se nazývá fáze počáteční replikace.

Po dokončení počáteční replikace, replikace přejde do fáze rozdílové synchronizace. V tomto okamžiku je chráněný virtuální počítač a operaci převzetí služeb při selhání testu lze provést v něm. Stav replikace replikovanou položku představující virtuální počítač přejde do stavu "Protected" po dokončení počáteční replikace.

Získávání podrobností chráněné položce replikace k němu odpovídající sledujte stav replikace a stav replikace pro virtuální počítač.
```azurepowershell
 Get-ASRReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState ReplicationHealth
------------ --------------- -----------------
AzureDemoVM  Protected       Normal           
```

## <a name="perform-a-test-failover-validate-and-cleanup-test-failover"></a>Provést testovací převzetí služeb, ověření a vyčištění testovacího převzetí služeb při selhání

Po dosažení chráněném stavu replikace pro virtuální počítač operaci převzetí služeb při selhání testu lze provést na virtuálním počítači (na chráněné položce replikace virtuálního počítače.)

```azurepowershell
#Create a seperate network for test failover (not connected to my DR network)
$TFOVnet = New-AzureRmVirtualNetwork -Name "a2aTFOvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.3.0.0/16"

Add-AzureRmVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $TFOVnet -AddressPrefix "10.3.0.0/20" | Set-AzureRmVirtualNetwork

$TFONetwork= $TFOVnet.Id
```

Provedení testu převzetí služeb při selhání.
```azurepowershell
$ReplicationProtectedItem = Get-ASRReplicationProtectedItem -FriendlyName "AzureDemoVM" -ProtectionContainer $PrimaryProtContainer

$TFOJob = Start-ASRTestFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -AzureVMNetworkId $TFONetwork -Direction PrimaryToRecovery


```

Počkejte na dokončení operace převzetí služeb při selhání testu.
```azurepowershell
Get-ASRJob -Job $TFOJob
```

```
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
Po úspěšném dokončení úlohy převzetí služeb při selhání testu můžete připojit k testovací převzetí služeb virtuálního počítače při selhání a ověření testu převzetí služeb.

Po dokončení testovacího převzetí služeb virtuálního počítače při selhání testování vyčistit testovací kopie spuštěním čištění testovací převzetí služeb při selhání. Tato operace odstraní zkušební kopii virtuálního počítače, který byl vytvořen převzetí služeb při selhání testu.

```azurepowershell
$Job_TFOCleanup = Start-ASRTestFailoverCleanupJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-ASRJob -Job $Job_TFOCleanup | Select State
```
```
State    
-----    
Succeeded
```

## <a name="failover-to-azure"></a>Převzetí služeb při selhání do Azure

Převzetí služeb při selhání virtuálního počítače na konkrétní bod obnovení.

```azurepowershell
$RecoveryPoints = Get-ASRRecoveryPoint -ReplicationProtectedItem $ReplicationProtectedItem

#The list of recovery points returned may not be sorted chronologically and will need to be sorted first, in order to be able to find the oldest or the latest recovery points for the virtual machine.
"{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[-1].RecoveryPointTime
```
```
CrashConsistent 4/24/2018 11:10:25 PM
```


```azurepowershell
#Start the failover job
$Job_Failover = Start-ASRUnplannedFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[-1]

do {
        $Job_Failover = Get-ASRJob -Job $Job_Failover;
        sleep 30;
} while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))

$Job_Failover.State
```
```
Succeeded
```

Po převzetí služeb při selhání úspěšně, můžete potvrdit operaci převzetí služeb při selhání.
```azurepowershell
$CommitFailoverJOb = Start-ASRCommitFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-ASRJOb -Job $CommitFailoverJOb
```

```
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

Po selhání až budete připravení přejít zpět na původní oblast, spusťte reverzní replikaci pro chráněné položce replikace pomocí rutiny Update-AzureRmRecoveryServicesAsrProtectionDirection.

## <a name="next-steps"></a>Další postup
Zobrazení [Powershellu pro Azure Site Recovery odkaz ](https://docs.microsoft.com/powershell/module/AzureRM.RecoveryServices.SiteRecovery) se dozvíte, jak lze provádět další úlohy, jako je například vytváření plánů pro zotavení a testování převzetí služeb při selhání plánů obnovení pomocí prostředí PowerShell.
