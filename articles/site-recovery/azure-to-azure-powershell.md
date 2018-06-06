---
title: Azure Site Recovery - instalační program a testování zotavení po havárii pro virtuální počítače Azure pomocí Azure PowerShell | Microsoft Docs
description: Zjistěte, jak nastavit zotavení po havárii pro virtuální počítače Azure s Azure Site Recovery pomocí Azure PowerShell.
services: site-recovery
author: bsiva
manager: abhemraj
editor: raynew
ms.service: site-recovery
ms.topic: article
ms.date: 05/31/2018
ms.author: bsiva
ms.openlocfilehash: 3fa9ee27a1b9717d8011b7b46a1116f1f1ac1df5
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34716325"
---
# <a name="set-up-disaster-recovery-for-azure-virtual-machines-using-azure-powershell"></a>Nastavit zotavení po havárii pro virtuální počítače Azure pomocí Azure PowerShell

V tomto článku, můžete zjistit, jak nastavit a testování zotavení po havárii pro virtuální počítače Azure pomocí Azure PowerShell. 

Získáte informace o těchto tématech:

> [!div class="checklist"]
> - Vytvořte trezor služby Recovery Services.
> - Nastavte kontext trezoru pro relace prostředí PowerShell.
> - Příprava na trezor zahájení replikace virtuálních počítačů Azure.
> - Vytvořte mapování sítě.
> - Vytvořte účty úložiště pro replikaci virtuálních počítačů.
> - Replikovat virtuální počítače Azure v oblasti obnovení pro zotavení po havárii.
> - Provedení testu převzetí služeb, ověřit a vyčistit testovací převzetí služeb při selhání.
> - Převzetí služeb při selhání pro oblast pro obnovení.

> [!NOTE]
> Ne všechny funkce scénář prostřednictvím portálu k dispozici může být k dispozici prostřednictvím Azure PowerShell. Některé z možností scénář není podporován pomocí prostředí Azure PowerShell jsou:
> - Možnost replikovat Azure virtuální počítače, které používají spravovaný disky.
> - Možnost určit, že všechny disky ve virtuálním počítači by měla být replikována bez nutnosti explicitně určovat každého disku virtuálního počítače.  

## <a name="prerequisites"></a>Požadavky

Než začnete, potřebujete:
- Ujistěte se, že rozumíte [komponentám a architektuře řešení](azure-to-azure-architecture.md).
- Zkontrolujte [požadavky na podporu](azure-to-azure-support-matrix.md) pro všechny komponenty.
- Máte verzi 5.7.0 nebo větší modulu AzureRm prostředí PowerShell. Pokud potřebujete nainstalovat nebo upgradovat prostředí Azure PowerShell, postupujte podle to [Průvodce instalace a konfigurace prostředí Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Přihlaste se k předplatnému Microsoft Azure

Přihlaste se k předplatnému Azure, pomocí rutiny Connect-AzureRmAccount

```azurepowershell
Connect-AzureRmAccount
```
Vyberte své předplatné Azure. Použijte rutinu Get-AzureRmSubscription získat přístup k seznamu předplatných Azure, které máte. Vyberte předplatné Azure, postup při použití rutiny Select-AzureRmSubscription.

```azurepowershell
Select-AzureRmSubscription -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

## <a name="get-details-of-the-virtual-machines-to-be-replicated"></a>Podrobnosti o virtuální počítače, které se budou replikovat

V příkladu v tomto článku virtuální počítač v oblasti Východ USA replikovaných a obnovit v oblasti západní USA 2. Virtuální počítač, který je právě replikován je virtuální počítač s diskem operačního systému a jeden datový disk. Název virtuálního počítače použitých v tomto příkladu je AzureDemoVM.

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

Získáte podrobnosti o disku pro disky virtuálního počítače. Podrobnosti o disku se použije při počáteční replikace pro virtuální počítač později.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Vytvořte skupinu prostředků, ve které chcete vytvořit trezor služeb zotavení. 

> [!IMPORTANT]
> * Trezor služeb zotavení a virtuální počítače, který je chráněn, musí být v různých umístěních Azure.
> * Skupiny prostředků trezoru služeb zotavení a virtuální počítače, který je chráněn, musí být v různých umístěních Azure.
> * Trezor služeb zotavení a skupině prostředků, do které patří, může být ve stejné oblasti Azure.
 
V příkladu v tomto článku je virtuální počítač, který je chráněn v oblasti Východ USA. Oblasti obnovení vybraných pro obnovení po havárii je oblast západní USA 2. Trezor služeb zotavení a skupině prostředků úložiště, se v oblasti obnovení (západní USA 2)

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
   
Vytvoření trezoru služeb zotavení. V příkladu níže trezor služeb zotavení s názvem a2aDemoRecoveryVault se vytvoří v oblasti západní USA 2.

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
## <a name="set-the-vault-context"></a>Nastavit kontext trezoru

> [!TIP]
> Modul Azure PowerShell obnovení lokality (AzureRm.RecoveryServices.SiteRecovery modul) obsahuje snadno použitelný aliasy pro většinu rutin. Rutiny v modulu ve formě  *\<operaci >-**AzureRmRecoveryServicesAsr**\<objekt >* a mít ekvivalentní aliasů, které mají formát  *\<Operaci >-**automatické obnovení systému**\<objekt >*. Tento článek používá rutinu aliasy pro snadnější čtení.

Nastavte kontext trezoru pro použití v relaci prostředí PowerShell. K tomuto účelu stáhněte soubor nastavení trezoru k a importovat stažený soubor v relaci prostředí PowerShell nastavit kontext úložiště. 

Po nastavení, následných operací Azure Site Recovery v relaci prostředí PowerShell se provádějí v kontextu vybraného trezoru. 

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
## <a name="prepare-the-vault-to-start-replicating-azure-virtual-machines"></a>Příprava na trezor zahájení replikace virtuálních počítačů Azure

####<a name="1-create-a-site-recovery-fabric-object-to-represent-the-primarysource-region"></a>1. Vytvoření objektu prostředku infrastruktury Site Recovery představují oblasti primary(source)

Objekt prostředků infrastruktury v trezoru představuje oblast Azure. Objekt primární prostředků infrastruktury, je objekt fabric zastupující oblasti Azure, které patří virtuální počítače chráněn do trezoru. V příkladu v tomto článku je virtuální počítač, který je chráněn v oblasti Východ USA.

> [!NOTE]
> Operace v Azure Site Recovery se spustí asynchronně. Při zahájení operace odeslání úlohu služby Azure Site Recovery a úlohy sledování objekt je vrácen. Úlohu sledování objekt použijte, a získat nejnovější stav úlohy (Get-ASRJob) a sledování stavu operace.

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
Pokud ke stejnému trezoru jsou chráněné virtuální počítače z několika oblastmi Azure, vytvořte jeden objekt prostředků infrastruktury pro každý zdroj oblast Azure.

####<a name="2-create-a-site-recovery-fabric-object-to-represent-the-recovery-region"></a>2. Vytvoření objektu prostředku infrastruktury Site Recovery představují oblasti obnovení

Objekt fabric obnovení představuje obnovení umístění Azure. Virtuální počítače bude replikován a obnovit (v případě selhání) oblasti obnovení reprezentována obnovení prostředků infrastruktury. Obnovení oblast Azure, které jsou použité v tomto příkladu je západní USA 2.

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

####<a name="3-create-a-site-recovery-protection-container-in-the-primary-fabric"></a>3. Vytvořte kontejner ochrany Site Recovery v primární prostředků infrastruktury

Kontejner ochrany je kontejner, používá se k seskupení replikované položky v rámci prostředků infrastruktury.

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
####<a name="4-create-a-site-recovery-protection-container-in-the-recovery-fabric"></a>4. Vytvořte kontejner ochrany Site Recovery v prostředcích infrastruktury obnovení

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
####<a name="6-create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container"></a>6. Vytvořit mapování kontejnerů ochrany mezi primárním serverem a obnovení kontejner ochrany

Mapování kontejnerů ochrany mapuje kontejneru primární ochranu s kontejneru ochrany obnovení a zásady replikace. Vytvořte jedno mapování pro každou zásadu replikace, který budete chtít použít replikovat virtuální počítače mezi dvěma kontejneru ochrany.

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

####<a name="7-create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover"></a>7. Vytvořit mapování kontejnerů ochrany pro navrácení služeb po obnovení (po převzetí služeb při selhání provádí zpětnou replikaci)

Po selhání, až budete připraveni k uvedení neúspěšný přes virtuální počítač zpět do původní oblast Azure, můžete navrácení služeb po obnovení. K navrácení služeb po obnovení, neúspěšný přes virtuální počítač je zpětně replikované z neúspěšný přes oblast původní oblasti. Role původní oblasti a oblasti obnovení přepnout na zpětnou replikaci. Původní oblast teď bude oblasti obnovení nového a co byl původně oblasti obnovení teď bude primární oblasti. Mapování kontejnerů ochrany zpětné replikace představuje vypnuté role oblastí původní a obnovení.

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

## <a name="create-cache-storage-accounts-and-target-storage-accounts"></a>Vytvoření mezipaměti úložiště účty a účty úložiště cíl

Účet úložiště mezipaměti je standardní účet úložiště ve stejné oblasti Azure jako virtuální počítač, který je právě replikován. Účet úložiště mezipaměti se používá k ukládání replikaci se změny dočasně, než změny přesunou do obnovení oblast Azure. Můžete se rozhodnout (ale nemusí) zadejte účty úložiště různé mezipaměti u různých disků virtuálního počítače.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$EastUSCacheStorageAccount = New-AzureRmStorageAccount -Name "a2acachestorage" -ResourceGroupName "A2AdemoRG" -Location 'East US' -SkuName Standard_LRS -Kind Storage
```

Účet cílového úložiště pro virtuální počítače bez použití spravovaných disků, je účty úložiště v oblasti obnovení, ke které se replikují disky virtuálního počítače. Cílový účet úložiště může být standardní účet úložiště nebo prémiový účet úložiště. Vyberte typ účtu úložiště vyžaduje podle míry změny dat (rychlosti vstupně-výstupní operace zápisu) pro disky a Azure Site Recovery podporována změn limity pro typ úložiště.

```azurepowershell
#Create Target storage account in the recovery region. In this case a Standard Storage account
$WestUSTargetStorageAccount = New-AzureRmStorageAccount -Name "a2atargetstorage" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -SkuName Standard_LRS -Kind Storage

```

## <a name="create-network-mappings"></a>Vytvoření mapování sítě

Mapování sítě mapuje virtuálních sítí v primární oblasti k virtuálním sítím v oblasti obnovení. Mapování sítě určuje virtuální síť Azure v oblasti obnovení, která se má virtuální počítač v primární virtuální síť převzetí služeb při selhání. Jednu virtuální síť Azure lze mapovat na pouze jednu virtuální síť Azure v oblasti obnovení.

- Vytvoření virtuální sítě Azure v oblasti obnovení pro převzetí služeb při selhání

   ```azurepowershell
    #Create a Recovery Network in the recovery region
    $WestUSRecoveryVnet = New-AzureRmVirtualNetwork -Name "a2arecoveryvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.0.0.0/16"

    Add-AzureRmVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $WestUSRecoveryVnet -AddressPrefix "10.0.0.0/20" | Set-AzureRmVirtualNetwork

    $WestUSRecoveryNetwork = $WestUSRecoveryVnet.Id
   ```
- Načtení primární virtuální síť (vnet, připojený k virtuálnímu počítači)
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
- Vytvořit mapování sítě mezi primární virtuální sítě a virtuální síti pro obnovení
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
- Vytvořit mapování sítě pro pozpátku (navrácení služeb po obnovení)
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

## <a name="replicate-azure-virtual-machine"></a>Replikaci virtuálního počítače Azure

Replikovat virtuální počítač Azure.

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

Po spuštění operace replikace úspěšné, data virtuálního počítače se replikují do oblasti pro obnovení. 

V procesu replikace spustí na začátku synchronizace replik indexů kopii replikačních discích v oblasti obnovení virtuálního počítače. Tato fáze se nazývá fáze počáteční replikace. 

Po dokončení počáteční replikace replikace se přesune do fáze rozdílové synchronizace. V tomto okamžiku je virtuální počítač je chráněný a testovací převzetí služeb při selhání operaci lze provést v něm. Stav replikace replikované položky představující virtuální počítač přejde do stavu "Chráněné" po dokončení počáteční replikace.

Získávání podrobností chráněné položce replikace odpovídající ji sledovat stav replikace a stav replikace pro virtuální počítač.
```azurepowershell
 Get-ASRReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState ReplicationHealth
------------ --------------- -----------------
AzureDemoVM  Protected       Normal           
```

## <a name="perform-a-test-failover-validate-and-cleanup-test-failover"></a>Provedení testu převzetí služeb, ověřit a vyčistit testovací převzetí služeb při selhání

Po dosažení chráněném stavu replikace pro virtuální počítač testovací převzetí služeb při selhání operaci lze provést na virtuálním počítači (na chráněné položce replikace virtuálního počítače.)

```azurepowershell
#Create a seperate network for test failover (not connected to my DR network)
$TFOVnet = New-AzureRmVirtualNetwork -Name "a2aTFOvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.3.0.0/16"

Add-AzureRmVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $TFOVnet -AddressPrefix "10.3.0.0/20" | Set-AzureRmVirtualNetwork

$TFONetwork= $TFOVnet.Id
```

Proveďte test převzetí služeb při selhání.
```azurepowershell
$ReplicationProtectedItem = Get-ASRReplicationProtectedItem -FriendlyName "AzureDemoVM" -ProtectionContainer $PrimaryProtContainer

$TFOJob = Start-ASRTestFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -AzureVMNetworkId $TFONetwork -Direction PrimaryToRecovery


```

Počkejte na dokončení operace testovací převzetí služeb při selhání.
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
Po úspěšném dokončení testovací převzetí služeb při selhání úlohy můžete připojit k testu při selhání virtuálního počítače a ověření testu převzetí služeb.

Po dokončení testovacího převzetí služeb virtuálního počítače při selhání testování vyčistěte testovací kopie spuštěním vyčištění testovací převzetí služeb při selhání. Tato operace odstraní kopii testovací virtuální počítač, který byl vytvořen převzetí služeb při selhání.

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

Převzetí služeb při selhání virtuálního počítače do konkrétní bod obnovení.

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

Po převzetí služeb při selhání úspěšně, můžete provést operaci převzetí služeb při selhání.
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

Po selhání Pokud jste připravení přejít zpět na původní oblast, spusťte reverzní replikaci pro chráněné položce replikace pomocí rutiny AzureRmRecoveryServicesAsrProtectionDirection aktualizace.

## <a name="next-steps"></a>Další postup
Zobrazení [Azure Site Recovery PowerShell odkaz ](https://docs.microsoft.com/powershell/module/AzureRM.RecoveryServices.SiteRecovery) se dozvíte, jak můžete provádět další úlohy, jako je například vytváření plánů obnovení a testování převzetí služeb při selhání plánů obnovení pomocí prostředí PowerShell.
