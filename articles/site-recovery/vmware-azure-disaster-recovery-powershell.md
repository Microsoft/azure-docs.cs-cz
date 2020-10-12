---
title: Nastavení zotavení po havárii VMware pomocí prostředí PowerShell ve službě Azure Site Revoery
description: Naučte se, jak nastavit replikaci a převzetí služeb při selhání do Azure za účelem zotavení po havárii virtuálních počítačů VMware pomocí PowerShellu v Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.date: 01/10/2020
ms.topic: conceptual
ms.author: sutalasi
ms.openlocfilehash: de25a3f9df04b09a7337dc889a688a171d98db28
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86129913"
---
# <a name="set-up-disaster-recovery-of-vmware-vms-to-azure-with-powershell"></a>Nastavení zotavení po havárii virtuálních počítačů VMware do Azure s využitím PowerShellu

V tomto článku se můžete podívat, jak replikovat virtuální počítače VMware a převzít služby při selhání do Azure pomocí Azure PowerShell.

Získáte informace o těchto tématech:

> [!div class="checklist"]
> - Vytvořte Trezor Recovery Services a nastavte kontext trezoru.
> - Ověřte registraci serveru v trezoru.
> - Nastavte replikaci, včetně zásad replikace. Přidejte Server vCenter a vyhledejte virtuální počítače.
> - Přidání serveru vCenter a zjišťování
> - Vytvořte účty úložiště pro ukládání protokolů replikace nebo dat a replikujte virtuální počítače.
> - Převzetí služeb při selhání Nakonfigurujte nastavení převzetí služeb při selhání, proveďte nastavení pro replikaci virtuálních počítačů.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Než začnete, potřebujete:

- Ujistěte se, že rozumíte [komponentám a architektuře řešení](vmware-azure-architecture.md).
- Zkontrolujte [požadavky na podporu](./vmware-physical-azure-support-matrix.md) pro všechny komponenty.
- Máte `Az`  modul Azure PowerShell. Pokud potřebujete nainstalovat nebo upgradovat Azure PowerShell, postupujte podle pokynů v tomto [Průvodci a nainstalujte a nakonfigurujte Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="log-into-azure"></a>Přihlášení k Azure

Přihlaste se k předplatnému Azure pomocí rutiny Connect-AzAccount:

```azurepowershell
Connect-AzAccount
```
Vyberte předplatné Azure, do kterého chcete replikovat virtuální počítače VMware. Pomocí rutiny Get-AzSubscription získáte seznam předplatných Azure, ke kterým máte přístup. Vyberte předplatné Azure, se kterým chcete pracovat pomocí rutiny Select-AzSubscription.

```azurepowershell
Select-AzSubscription -SubscriptionName "ASR Test Subscription"
```
## <a name="set-up-a-recovery-services-vault"></a>Nastavte trezor služby Recovery Services.

1. Vytvořte skupinu prostředků, ve které chcete vytvořit trezor Recovery Services. V následujícím příkladu má skupina prostředků název VMwareDRtoAzurePS a je vytvořená v oblasti Východní Asie.

   ```azurepowershell
   New-AzResourceGroup -Name "VMwareDRtoAzurePS" -Location "East Asia"
   ```
   ```
   ResourceGroupName : VMwareDRtoAzurePS
   Location          : eastasia
   ProvisioningState : Succeeded
   Tags              :
   ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRtoAzurePS
   ```

2. Vytvořte Trezor služby Recovery Services. V následujícím příkladu je trezor služby Recovery Services nazvaný VMwareDRToAzurePs a je vytvořen v oblasti Východní Asie a ve skupině prostředků vytvořené v předchozím kroku.

   ```azurepowershell
   New-AzRecoveryServicesVault -Name "VMwareDRToAzurePs" -Location "East Asia" -ResourceGroupName "VMwareDRToAzurePs"
   ```
   ```
   Name              : VMwareDRToAzurePs
   ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs
   Type              : Microsoft.RecoveryServices/vaults
   Location          : eastasia
   ResourceGroupName : VMwareDRToAzurePs
   SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
   Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
   ```

3. Stáhněte si registrační klíč trezoru pro trezor. Registrační klíč trezoru se používá k registraci místního konfiguračního serveru do tohoto trezoru. Registrace je součástí procesu instalace softwaru konfiguračního serveru.

   ```azurepowershell
   #Get the vault object by name and resource group and save it to the $vault PowerShell variable 
   $vault = Get-AzRecoveryServicesVault -Name "VMwareDRToAzurePS" -ResourceGroupName "VMwareDRToAzurePS"

   #Download vault registration key to the path C:\Work
   Get-AzRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   ```
   ```
   FilePath
   --------
   C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials
   ```

4. Použijte stažený registrační klíč trezoru a podle kroků uvedených v článcích dokončete instalaci a registraci konfiguračního serveru.
   - [Výběr cílů ochrany](vmware-azure-set-up-source.md#choose-your-protection-goals)
   - [Nastavení zdrojového prostředí](vmware-azure-set-up-source.md#set-up-the-configuration-server)

### <a name="set-the-vault-context"></a>Nastavte kontext trezoru.

Nastavte kontext trezoru pomocí rutiny Set-ASRVaultContext. Po nastavení se následné operace Azure Site Recovery v relaci PowerShellu provádějí v kontextu vybraného trezoru.

> [!TIP]
> Modul Azure Site Recovery PowerShellu (AZ. RecoveryServices Module) obsahuje snadné použití aliasů pro většinu rutin. Rutiny v modulu mají formu * \<Operation> - **AzRecoveryServicesAsr** \<Object> * a mají ekvivalentní aliasy, které mají formu * \<Operation> - **ASR** \<Object> *. Můžete nahradit aliasy rutiny pro snadné použití.

V následujícím příkladu se k určení kontextu trezoru pro relaci PowerShellu použijí podrobnosti trezoru z $vault proměnné.

   ```azurepowershell
   Set-ASRVaultContext -Vault $vault
   ```
   ```
   ResourceName      ResourceGroupName ResourceNamespace          ResourceType
   ------------      ----------------- -----------------          -----------
   VMwareDRToAzurePs VMwareDRToAzurePs Microsoft.RecoveryServices vaults
   ```

Jako alternativu k rutině Set-ASRVaultContext může k nastavení kontextu trezoru použít také rutinu Import-AzRecoveryServicesAsrVaultSettingsFile. Zadejte cestu, ve které je soubor registračního klíče trezoru umístěný jako parametr-Path pro rutinu Import-AzRecoveryServicesAsrVaultSettingsFile. Například:

   ```azurepowershell
   Get-AzRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   Import-AzRecoveryServicesAsrVaultSettingsFile -Path "C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials"
   ```
V dalších částech tohoto článku se předpokládá, že je nastavený kontext trezoru pro Azure Site Recovery operace.

## <a name="validate-vault-registration"></a>Ověřit registraci trezoru

V tomto příkladu máme následující:

- Do tohoto trezoru se zaregistroval konfigurační server (**ConfigurationServer**).
- Pro *ConfigurationServer* se zaregistroval další procesový Server (**škálování – ProcessServer**).
- Na konfiguračním serveru se nastavily účty (**vCenter_account**, **WindowsAccount**, **LinuxAccount**). Tyto účty slouží k přidání serveru vCenter, ke zjištění virtuálních počítačů a k instalaci softwaru služby mobility na servery se systémem Windows a Linux, které se mají replikovat.

1. Registrované konfigurační servery jsou reprezentovány objektem Fabric v Site Recovery. Získejte seznam objektů prostředků infrastruktury v trezoru a Identifikujte konfigurační server.

   ```azurepowershell
   # Verify that the Configuration server is successfully registered to the vault
   $ASRFabrics = Get-AzRecoveryServicesAsrFabric
   $ASRFabrics.count
   ```
   ```
   1
   ```
   ```azurepowershell
   #Print details of the Configuration Server
   $ASRFabrics[0]
   ```
   ```
   Name                  : 2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
   FriendlyName          : ConfigurationServer
   ID                    : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationFabrics
                           /2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
   Type                  : Microsoft.RecoveryServices/vaults/replicationFabrics
   FabricType            : VMware
   SiteIdentifier        : ef7a1580-f356-4a00-aa30-7bf80f952510
   FabricSpecificDetails : Microsoft.Azure.Commands.RecoveryServices.SiteRecovery.ASRVMWareSpecificDetails
   ```

2. Identifikujte procesní servery, které se dají použít k replikaci počítačů.

   ```azurepowershell
   $ProcessServers = $ASRFabrics[0].FabricSpecificDetails.ProcessServers
   for($i=0; $i -lt $ProcessServers.count; $i++) {
    "{0,-5} {1}" -f $i, $ProcessServers[$i].FriendlyName
   }
   ```
   ```
   0     ScaleOut-ProcessServer
   1     ConfigurationServer
   ```

   Z výstupu nad ***$ProcessServers [0]*** odpovídá *ProcessServer* a ***$ProcessServers [1]*** odpovídá roli procesového serveru v *ConfigurationServer* .

3. Identifikujte účty, které byly nastaveny na konfiguračním serveru.

   ```azurepowershell
   $AccountHandles = $ASRFabrics[0].FabricSpecificDetails.RunAsAccounts
   #Print the account details
   $AccountHandles
   ```
   ```
   AccountId AccountName
   --------- -----------
   1         vCenter_account
   2         WindowsAccount
   3         LinuxAccount
   ```

   Z výstupu výše ***$AccountHandles [0]*** odpovídá účtu *vCenter_account*, ***$AccountHandles [1]*** k účtu *WindowsAccount*a ***$AccountHandles [2]*** k účtu *LinuxAccount*

## <a name="create-a-replication-policy"></a>Vytvoření zásady replikace

V tomto kroku se vytvoří dvě zásady replikace. Jedna zásada pro replikaci virtuálních počítačů VMware do Azure a druhá pro replikaci virtuálních počítačů s podporou převzetí služeb při selhání v Azure zpátky na místní lokalitu VMware.

> [!NOTE]
> Většina operací Azure Site Recovery prováděna asynchronně. Když zahájíte operaci, odešle se Azure Site Recovery úloha a vrátí se objekt sledování úlohy. Tento objekt sledování úlohy lze použít k monitorování stavu operace.

1. Vytvořte zásadu replikace s názvem *ReplicationPolicy* pro replikaci virtuálních počítačů VMware do Azure se zadanými vlastnostmi.

   ```azurepowershell
   $Job_PolicyCreate = New-AzRecoveryServicesAsrPolicy -VMwareToAzure -Name "ReplicationPolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60

   # Track Job status to check for completion
   while (($Job_PolicyCreate.State -eq "InProgress") -or ($Job_PolicyCreate.State -eq "NotStarted")){
           sleep 10;
           $Job_PolicyCreate = Get-ASRJob -Job $Job_PolicyCreate
   }

   #Display job status
   $Job_PolicyCreate
   ```
   ```
   Name             : 8d18e2d9-479f-430d-b76b-6bc7eb2d0b3e
   ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/8d18e2d
                      9-479f-430d-b76b-6bc7eb2d0b3e
   Type             :
   JobType          : AddProtectionProfile
   DisplayName      : Create replication policy
   ClientRequestId  : a162b233-55d7-4852-abac-3d595a1faac2 ActivityId: 9895234a-90ea-4c1a-83b5-1f2c6586252a
   State            : Succeeded
   StateDescription : Completed
   StartTime        : 11/24/2017 2:49:24 AM
   EndTime          : 11/24/2017 2:49:23 AM
   TargetObjectId   : ab31026e-4866-5440-969a-8ebcb13a372f
   TargetObjectType : ProtectionProfile
   TargetObjectName : ReplicationPolicy
   AllowedActions   :
   Tasks            : {Prerequisites check for creating the replication policy, Creating the replication policy}
   Errors           : {}
   ```

2. Vytvořte zásadu replikace, která se použije pro navrácení služeb po obnovení z Azure do místní lokality VMware.

   ```azurepowershell
   $Job_FailbackPolicyCreate = New-AzRecoveryServicesAsrPolicy -AzureToVMware -Name "ReplicationPolicy-Failback" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60
   ```

   Pomocí podrobností o úloze v *$Job _FailbackPolicyCreate* můžete sledovat dokončení operace.

   * Vytvořte mapování kontejneru ochrany pro mapování zásad replikace ke konfiguračnímu serveru.

   ```azurepowershell
   #Get the protection container corresponding to the Configuration Server
   $ProtectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $ASRFabrics[0]

   #Get the replication policies to map by name.
   $ReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "ReplicationPolicy"
   $FailbackReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "ReplicationPolicy-Failback"

   # Associate the replication policies to the protection container corresponding to the Configuration Server.

   $Job_AssociatePolicy = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "PolicyAssociation1" -PrimaryProtectionContainer $ProtectionContainer -Policy $ReplicationPolicy

   # Check the job status
   while (($Job_AssociatePolicy.State -eq "InProgress") -or ($Job_AssociatePolicy.State -eq "NotStarted")){
           sleep 10;
           $Job_AssociatePolicy = Get-ASRJob -Job $Job_AssociatePolicy
   }
   $Job_AssociatePolicy.State

   <# In the protection container mapping used for failback (replicating failed over virtual machines
      running in Azure, to the primary VMware site.) the protection container corresponding to the
      Configuration server acts as both the Primary protection container and the recovery protection
      container
   #>
    $Job_AssociateFailbackPolicy = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "FailbackPolicyAssociation" -PrimaryProtectionContainer $ProtectionContainer -RecoveryProtectionContainer $ProtectionContainer -Policy $FailbackReplicationPolicy

   # Check the job status
   while (($Job_AssociateFailbackPolicy.State -eq "InProgress") -or ($Job_AssociateFailbackPolicy.State -eq "NotStarted")){
           sleep 10;
           $Job_AssociateFailbackPolicy = Get-ASRJob -Job $Job_AssociateFailbackPolicy
   }
   $Job_AssociateFailbackPolicy.State

   ```

## <a name="add-a-vcenter-server-and-discover-vms"></a>Přidání serveru vCenter a zjištění virtuálních počítačů

Přidejte vCenter Server podle IP adresy nebo názvu hostitele. Parametr **-port** Určuje port na serveru vCenter, který se má připojit k, parametr **-Name** Určuje popisný název, který se má použít pro Server vCenter, a parametr  **-account** Určuje popisovač účtu na konfiguračním serveru, který se má použít ke zjišťování virtuálních počítačů spravovaných serverem vCenter.

```azurepowershell
# The $AccountHandles[0] variable holds details of vCenter_account

$Job_AddvCenterServer = New-AzRecoveryServicesAsrvCenter -Fabric $ASRFabrics[0] -Name "MyvCenterServer" -IpOrHostName "10.150.24.63" -Account $AccountHandles[0] -Port 443

#Wait for the job to complete and ensure it completed successfully

while (($Job_AddvCenterServer.State -eq "InProgress") -or ($Job_AddvCenterServer.State -eq "NotStarted")) {
        sleep 30;
        $Job_AddvCenterServer = Get-ASRJob -Job $Job_AddvCenterServer
}
$Job_AddvCenterServer
```
```
Name             : 0f76f937-f9cf-4e0e-bf27-10c9d1c252a4
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/0f76f93
                   7-f9cf-4e0e-bf27-10c9d1c252a4
Type             :
JobType          : DiscoverVCenter
DisplayName      : Add vCenter server
ClientRequestId  : a2af8892-5686-4d64-a528-10445bc2f698 ActivityId: 7ec05aad-002e-4da0-991f-95d0de7a9f3a
State            : Succeeded
StateDescription : Completed
StartTime        : 11/24/2017 2:41:47 AM
EndTime          : 11/24/2017 2:44:37 AM
TargetObjectId   : 10.150.24.63
TargetObjectType : VCenter
TargetObjectName : MyvCenterServer
AllowedActions   :
Tasks            : {Adding vCenter server}
Errors           : {}
```

## <a name="create-storage-accounts-for-replication"></a>Vytvoření účtů úložiště pro replikaci

**K zápisu na spravovaný disk použijte [PowerShell AZ. RecoveryServices Module 2.0.0](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview) a vyšší.** Vyžaduje jenom vytvoření účtu úložiště protokolu. Doporučuje se použít standardní typ účtu a redundanci LRS, protože se používá k ukládání pouze dočasných protokolů. Ujistěte se, že je účet úložiště vytvořený ve stejné oblasti Azure jako trezor.

Pokud používáte verzi modulu AZ. RecoveryServices starší než 2.0.0, vytvořte pomocí následujících kroků účty úložiště. Tyto účty úložiště se používají později k replikaci virtuálních počítačů. Zajistěte, aby se účty úložiště vytvořily ve stejné oblasti Azure jako trezor. Pokud plánujete použít existující účet úložiště pro replikaci, můžete tento krok přeskočit.

> [!NOTE]
> Při replikaci místních virtuálních počítačů do účtu Premium Storage je nutné zadat další účet úložiště úrovně Standard (účet úložiště protokolu). Účet úložiště protokolu uchovává protokoly replikace jako zprostředkující úložiště, dokud je nebudete moct v cíli úložiště Premium použít.
>

```azurepowershell

$PremiumStorageAccount = New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "premiumstorageaccount1" -Location "East Asia" -SkuName Premium_LRS

$LogStorageAccount = New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "logstorageaccount1" -Location "East Asia" -SkuName Standard_LRS

$ReplicationStdStorageAccount= New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "replicationstdstorageaccount1" -Location "East Asia" -SkuName Standard_LRS
```

## <a name="replicate-vmware-vms"></a>Replikace virtuálních počítačů VMware

Zjištění virtuálních počítačů ze serveru vCenter trvá přibližně 15-20 minut. Po zjištění se v Azure Site Recovery vytvoří nechráněný objekt položky pro každý zjištěný virtuální počítač. V tomto kroku se tři ze zjištěných virtuálních počítačů replikují na účty Azure Storage vytvořené v předchozím kroku.

K ochraně zjištěného virtuálního počítače budete potřebovat následující informace:

* Chráněná položka, která má být replikována.
* Účet úložiště, do kterého se má virtuální počítač replikovat (jenom v případě, že se replikuje do účtu úložiště). 
* Úložiště protokolu je potřeba k ochraně virtuálních počítačů s účtem Premium Storage nebo se spravovaným diskem.
* Procesový Server, který se má použít pro replikaci. Seznam dostupných procesových serverů byl načten a uložen do ***$ProcessServers [0]***  *(Scale-ProcessServer)* a ***$ProcessServers [1]*** *(ConfigurationServer)* proměnných.
* Účet, který se má použít k instalaci softwaru služby mobility do počítačů. Seznam dostupných účtů byl načten a uložen v proměnné ***$AccountHandles*** .
* Mapování kontejneru ochrany pro zásady replikace, které se má použít pro replikaci.
* Skupina prostředků, ve které se musí vytvořit virtuální počítače při převzetí služeb při selhání.
* Volitelně by se měla připojit virtuální síť Azure a podsíť, ke které se virtuální počítač převezme služby při selhání.

Teď replikujte následující virtuální počítače pomocí nastavení zadaných v této tabulce.


|Virtuální počítač  |Procesový Server        |Účet úložiště              |Účet úložiště protokolů  |Zásady           |Účet pro instalaci služby mobility|Cílová skupina prostředků  | Cílová virtuální síť  |Cílová podsíť  |
|-----------------|----------------------|-----------------------------|---------------------|-----------------|-----------------------------------------|-----------------------|-------------------------|---------------|
|CentOSVM1       |ConfigurationServer   |Není k dispozici| logstorageaccount1                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR – VNet                 |Podsíť – 1       |
|Win2K12VM1       |ScaleOut-ProcessServer|premiumstorageaccount1       |logstorageaccount1   |ReplicationPolicy|WindowsAccount                           |VMwareDRToAzurePs      |ASR – VNet                 |Podsíť – 1       |   
|CentOSVM2       |ConfigurationServer   |replicationstdstorageaccount1| Není k dispozici                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR – VNet                 |Podsíť – 1       |   


```azurepowershell

#Get the target resource group to be used
$ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg" 

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap  = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM1 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1 using the Az.RecoveryServices module 2.0.0 onwards to replicate to managed disks
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

# Alternatively, if the virtual machine CentOSVM1 has CMK enabled disks, enable replication using Az module 3.3.0 onwards as below
# $diskID is the Disk Encryption Set ID to be used for all replica managed disks and target managed disks in the target region
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId -DiskEncryptionSetId $diskId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine Win2K12VM1
$VM2 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "Win2K12VM1"

# Enable replication for virtual machine Win2K12VM1
$Job_EnableReplication2 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM2 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $PremiumStorageAccount.Id -LogStorageAccountId $LogStorageAccount.Id -ProcessServer $ProcessServers[0] -Account $AccountHandles[1] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine CentOSVM2
$VM3 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM2"

# Enable replication for virtual machine CentOSVM2
$Job_EnableReplication3 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM3 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $ReplicationStdStorageAccount.Id  -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

```

Po úspěšném dokončení úlohy povolit replikaci se pro virtuální počítače spustí počáteční replikace. Počáteční replikace může chvíli trvat, v závislosti na množství dat, která se mají replikovat, a šířce pásma dostupnou pro replikaci. Po dokončení počáteční replikace se virtuální počítač přesune do chráněného stavu. Jakmile virtuální počítač dosáhne chráněného stavu, můžete provést testovací převzetí služeb při selhání pro virtuální počítač, přidat ho do plánů obnovení atd.

Stav replikace a stav replikace virtuálního počítače můžete zjistit pomocí rutiny Get-ASRReplicationProtectedItem.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState                 ReplicationHealth
------------ ---------------                 -----------------
CentOSVM1    Protected                       Normal
CentOSVM2    InitialReplicationInProgress    Normal
Win2K12VM1   Protected                       Normal
```

## <a name="configure-failover-settings"></a>Konfigurace nastavení převzetí služeb při selhání

Nastavení převzetí služeb při selhání pro chráněné počítače je možné aktualizovat pomocí rutiny Set-ASRReplicationProtectedItem. Některá nastavení, která je možné aktualizovat prostřednictvím této rutiny:
* Název virtuálního počítače, který se má vytvořit při převzetí služeb při selhání
* Velikost virtuálního počítače, který se má vytvořit při převzetí služeb při selhání
* Virtuální síť Azure a podsíť, ke kterým by se měly síťové karty virtuálního počítače připojit při převzetí služeb při selhání
* Převzetí služeb při selhání na spravované disky
* Použít zvýhodněné hybridní využití Azure
* Přiřadit statickou IP adresu z cílové virtuální sítě, která se má přiřadit k virtuálnímu počítači při převzetí služeb při selhání.

V tomto příkladu aktualizujeme velikost virtuálního počítače, který má být vytvořen při převzetí služeb při selhání pro virtuální počítač *Win2K12VM1* , a určíte, že virtuální počítač použije při převzetí služeb při selhání spravované disky.

```azurepowershell
$ReplicatedVM1 = Get-AzRecoveryServicesAsrReplicationProtectedItem -FriendlyName "Win2K12VM1" -ProtectionContainer $ProtectionContainer

Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $ReplicatedVM1 -Size "Standard_DS11" -UseManagedDisk True
```
```
Name             : cafa459c-44a7-45b0-9de9-3d925b0e7db9
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/cafa459
                   c-44a7-45b0-9de9-3d925b0e7db9
Type             :
JobType          : UpdateVmProperties
DisplayName      : Update the virtual machine
ClientRequestId  : b0b51b2a-f151-4e9a-a98e-064a5b5131f3 ActivityId: ac2ba316-be7b-4c94-a053-5363f683d38f
State            : InProgress
StateDescription : InProgress
StartTime        : 11/24/2017 2:04:26 PM
EndTime          :
TargetObjectId   : 88bc391e-d091-11e7-9484-000c2955bb50
TargetObjectType : ProtectionEntity
TargetObjectName : Win2K12VM1
AllowedActions   :
Tasks            : {Update the virtual machine properties}
Errors           : {}
```

## <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání

1. Spusťte postup zotavení po havárii (testovací převzetí služeb při selhání) následujícím způsobem:

   ```azurepowershell
   #Test failover of Win2K12VM1 to the test virtual network "V2TestNetwork"

   #Get details of the test failover virtual network to be used
   TestFailovervnet = Get-AzVirtualNetwork -Name "V2TestNetwork" -ResourceGroupName "asrrg" 

   #Start the test failover operation
   $TFOJob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -AzureVMNetworkId $TestFailovervnet.Id -Direction PrimaryToRecovery
   ```
2. Jakmile se úloha testovacího převzetí služeb při selhání úspěšně dokončí, všimnete si, že se v Azure vytvoří virtuální počítač s příponou *"-test"* (Win2K12VM1-test v tomto případě) na jeho název.
3. Nyní se můžete připojit k testu při selhání virtuálního počítače a ověřit testovací převzetí služeb při selhání.
4. Vyčistěte testovací převzetí služeb při selhání pomocí rutiny Start-ASRTestFailoverCleanupJob. Tato operace odstraní virtuální počítač vytvořený jako součást operace testovacího převzetí služeb při selhání.

   ```azurepowershell
   $Job_TFOCleanup = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $ReplicatedVM1
   ```

## <a name="fail-over-to-azure"></a>Převzetí služeb při selhání do Azure

V tomto kroku provedeme převzetí služeb při selhání virtuálního počítače Win2K12VM1 na určitý bod obnovení.

1. Získat seznam dostupných bodů obnovení, které se mají použít pro převzetí služeb při selhání:
   ```azurepowershell
   # Get the list of available recovery points for Win2K12VM1
   $RecoveryPoints = Get-AzRecoveryServicesAsrRecoveryPoint -ReplicationProtectedItem $ReplicatedVM1
   "{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[0].RecoveryPointTime
   ```
   ```
   CrashConsistent 11/24/2017 5:28:25 PM
   ```
   ```azurepowershell

   #Start the failover job
   $Job_Failover = Start-AzRecoveryServicesAsrUnplannedFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[0]
   do {
           $Job_Failover = Get-ASRJob -Job $Job_Failover;
           sleep 60;
   } while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))
   $Job_Failover.State
   ```
   ```
   Succeeded
   ```

2. Po úspěšném selhání můžete operaci převzetí služeb při selhání potvrdit a nastavit zpětnou replikaci z Azure zpátky na místní lokalitu VMware.

## <a name="next-steps"></a>Další kroky
Naučte se automatizovat další úlohy pomocí [Azure Site Recovery referenčních informací prostředí PowerShell](/powershell/module/Az.RecoveryServices).
