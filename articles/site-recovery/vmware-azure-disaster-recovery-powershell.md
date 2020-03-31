---
title: Nastavení zotavení po havárii společnosti VMware pomocí prostředí PowerShell v revoery webu Azure
description: Zjistěte, jak nastavit replikaci a převzetí služeb při selhání do Azure pro zotavení po havárii virtuálních počítačů VMware pomocí Prostředí PowerShell v Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.date: 01/10/2020
ms.topic: conceptual
ms.author: sutalasi
ms.openlocfilehash: d2dfaab3d01ea29b0f9ecba1e9d748415bed2edc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257196"
---
# <a name="set-up-disaster-recovery-of-vmware-vms-to-azure-with-powershell"></a>Nastavení zotavení virtuálních počítačů VMware v oblasti havárií do Azure pomocí PowerShellu

V tomto článku uvidíte, jak replikovat a převzetí služeb při selhání virtuální počítače VMware do Azure pomocí Azure PowerShell.

Získáte informace o těchto tématech:

> [!div class="checklist"]
> - Vytvořte trezor služby Recovery Services a nastavte kontext úschovny.
> - Ověřte registraci serveru v úschovně.
> - Nastavte replikaci včetně zásad replikace. Přidejte server vCenter a objevte virtuální počítače.
> - Přidání serveru vCenter a zjišťování
> - Vytvořte účty úložiště pro uložení protokolů replikace nebo dat a replikujte virtuální chod.
> - Převzetí služeb při selhání Nakonfigurujte nastavení převzetí služeb při selhání, proveďte nastavení pro replikaci virtuálních počítačů.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Než začnete, potřebujete:

- Ujistěte se, že rozumíte [komponentám a architektuře řešení](vmware-azure-architecture.md).
- Zkontrolujte [požadavky na podporu](site-recovery-support-matrix-to-azure.md) pro všechny komponenty.
- Máte modul Azure `Az` PowerShell. Pokud potřebujete nainstalovat nebo upgradovat Azure PowerShell, postupujte podle tohoto [průvodce k instalaci a konfiguraci Azure PowerShellu](/powershell/azure/install-az-ps).

## <a name="log-into-azure"></a>Přihlášení k Azure

Přihlaste se k předplatnému Azure pomocí rutiny Connect-AzAccount:

```azurepowershell
Connect-AzAccount
```
Vyberte předplatné Azure, na které chcete replikovat virtuální počítače VMware. Pomocí rutiny Get-AzSubscription získáte seznam předplatných Azure, ke které máte přístup. Vyberte předplatné Azure pro práci s pomocí rutiny Select-AzSubscription.

```azurepowershell
Select-AzSubscription -SubscriptionName "ASR Test Subscription"
```
## <a name="set-up-a-recovery-services-vault"></a>Nastavte trezor služby Recovery Services.

1. Vytvořte skupinu prostředků, ve které chcete vytvořit trezor služby Recovery Services. V níže uvedeném příkladu se skupina prostředků nazývá VMwareDRtoAzurePS a je vytvořena v oblasti východní Asie.

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

2. Vytvořte trezor služeb obnovení. V níže uvedeném příkladu je trezor služeb obnovení s názvem VMwareDRToAzurePs a je vytvořen v oblasti východní Asie a ve skupině prostředků vytvořené v předchozím kroku.

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

3. Stáhněte si registrační klíč trezoru pro trezor. Registrační klíč úložiště se používá k registraci místního konfiguračního serveru do této úschovny. Registrace je součástí procesu instalace softwaru konfiguračního serveru.

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

4. Použijte stažený registrační klíč trezoru a postupujte podle pokynů uvedených v následujících článcích k dokončení instalace a registrace konfiguračního serveru.
   - [Vyberte si cíle ochrany](vmware-azure-set-up-source.md#choose-your-protection-goals)
   - [Nastavení zdrojového prostředí](vmware-azure-set-up-source.md#set-up-the-configuration-server)

### <a name="set-the-vault-context"></a>Nastavení kontextu úschovny

Nastavte kontext úschovny pomocí rutiny Set-ASRVaultContext. Po nastavení se následné operace obnovení webu Azure v relaci Prostředí PowerShellu provedou v kontextu vybraného trezoru.

> [!TIP]
> Modul Azure Site Recovery PowerShell (modul Az.RecoveryServices) je dodáván se snadno použitelnými aliasy pro většinu rutin. Rutiny v modulu mají podobu * \<Operace>-**AzRecoveryServicesAsr**\<Objekt>* a mají ekvivalentní aliasy, které mají podobu * \<Operace>-**ASR**\<objekt>*. Aliasy rutiny můžete vyměnit pro snadné použití.

V níže uvedeném příkladu se podrobnosti o úschovně z proměnné $vault používají k určení kontextu úložiště pro relaci prostředí PowerShell.

   ```azurepowershell
   Set-ASRVaultContext -Vault $vault
   ```
   ```
   ResourceName      ResourceGroupName ResourceNamespace          ResourceType
   ------------      ----------------- -----------------          -----------
   VMwareDRToAzurePs VMwareDRToAzurePs Microsoft.RecoveryServices vaults
   ```

Jako alternativu k rutině Set-ASRVaultContext můžete také použít rutinu Import-AzRecoveryServicesAsrVaultSettingsFile k nastavení kontextu úschovny. Určete cestu, ve které je soubor registračního klíče úschovny umístěn jako parametr -path k rutině Import-AzRecoveryServicesAsrVaultSettingsFile. Například:

   ```azurepowershell
   Get-AzRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   Import-AzRecoveryServicesAsrVaultSettingsFile -Path "C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials"
   ```
Následující části tohoto článku předpokládají, že kontext úložiště pro operace obnovení webu Azure byla nastavena.

## <a name="validate-vault-registration"></a>Ověřit registraci trezoru

V tomto příkladu máme následující:

- Do tohoto trezoru byl zaregistrován konfigurační server (**ConfigurationServer).**
- Další procesní server **(ScaleOut-ProcessServer)** byl zaregistrován na *ConfigurationServer.*
- Účty (**vCenter_account**, **WindowsAccount**, **LinuxAccount**) byly nastaveny na konfiguračním serveru. Tyto účty se používají k přidání serveru vCenter, ke zjišťování virtuálních počítačů a k instalaci softwaru služby mobility na serverech Se systémem Windows a Linux, které mají být replikovány.

1. Registrované konfigurační servery jsou reprezentovány objektem infrastruktury v nástroji Site Recovery. Získejte seznam objektů infrastruktury v úschovně a identifikujte konfigurační server.

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

2. Identifikujte procesní servery, které lze použít k replikaci počítačů.

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

   Z výše uvedeného výstupu ***odpovídá $ProcessServers[0]*** *scaleOut-ProcessServer* a ***$ProcessServers[1]*** odpovídá roli procesního serveru na *configurationserveru*

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

   Z výše uvedeného výstupu ***$AccountHandles[0]*** odpovídá účtu *vCenter_account*, ***$AccountHandles[1]*** k účtu *WindowsAccount*a ***$AccountHandles[2]*** k účtu *LinuxAccount*

## <a name="create-a-replication-policy"></a>Vytvoření zásady replikace

V tomto kroku jsou vytvořeny dvě zásady replikace. Jedna zásada replikovat virtuální počítače VMware do Azure a druhá replikovat selhání přes virtuální počítače spuštěné v Azure zpět do místního webu VMware.

> [!NOTE]
> Většina operací obnovení webu Azure se spouští asynchronně. Když zahájíte operaci, je odeslána úloha obnovení webu Azure a vrátí se objekt sledování úlohy. Tento objekt sledování úlohlze sledovat stav operace.

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

2. Vytvořte zásady replikace, které se použijí pro navrácení služeb po obnovení z Azure do místní lokality VMware.

   ```azurepowershell
   $Job_FailbackPolicyCreate = New-AzRecoveryServicesAsrPolicy -AzureToVMware -Name "ReplicationPolicy-Failback" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60
   ```

   Podrobnosti o úloze v *$Job_FailbackPolicyCreate* ke sledování operace až do dokončení.

   * Vytvořte mapování kontejneru ochrany pro mapování zásad replikace pomocí konfiguračního serveru.

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

## <a name="add-a-vcenter-server-and-discover-vms"></a>Přidání serveru vCenter a zjišťování virtuálních připojení

Přidejte server vCenter podle IP adresy nebo názvu hostitele. Parametr **-port** určuje port na serveru vCenter, ke kterému se má připojit, parametr **-Name** určuje popisný název, který má být pro server vCenter použít, a parametr **-Account** určuje popisovač účtu na konfiguračním serveru, který se má použít ke zjišťování virtuálních počítačů spravovaných serverem vCenter.

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

**Chcete-li zapisovat na spravovaný disk, použijte [modul 2.0.0 služby Powershell Az.RecoveryServices.](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview)** Vyžaduje pouze vytvoření účtu úložiště protokolu. Doporučuje se použít standardní typ účtu a redundanci LRS, protože se používá k ukládání pouze dočasných protokolů. Ujistěte se, že účet úložiště se vytvoří ve stejné oblasti Azure jako trezor.

Pokud používáte verzi modulu Az.RecoveryServices starší než 2.0.0, vytvořte účty úložiště pomocí následujících kroků. Tyto účty úložiště se později používají k replikaci virtuálních počítačů. Ujistěte se, že účty úložiště jsou vytvořeny ve stejné oblasti Azure jako trezoru. Tento krok můžete přeskočit, pokud plánujete použít existující účet úložiště pro replikaci.

> [!NOTE]
> Při replikaci místních virtuálních počítačů do účtu úložiště premium, je třeba zadat další účet standardníúložiště (účet úložiště protokolu). Účet úložiště protokolu uchovává protokoly replikace jako zprostředkující úložiště, dokud protokoly lze použít na cíl úložiště premium.
>

```azurepowershell

$PremiumStorageAccount = New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "premiumstorageaccount1" -Location "East Asia" -SkuName Premium_LRS

$LogStorageAccount = New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "logstorageaccount1" -Location "East Asia" -SkuName Standard_LRS

$ReplicationStdStorageAccount= New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "replicationstdstorageaccount1" -Location "East Asia" -SkuName Standard_LRS
```

## <a name="replicate-vmware-vms"></a>Replikace virtuálních měn VMware

Trvá asi 15-20 minut pro virtuální počítače, které mají být objeveny ze serveru vCenter. Po zjištění se v Azure Site Recovery pro každý zjištěný virtuální počítač vytvoří objekt s ochrannou položkou. V tomto kroku se replikují tři zjištěné virtuální počítače do účtů Azure Storage vytvořených v předchozím kroku.

K ochraně zjištěného virtuálního počítače budete potřebovat následující podrobnosti:

* Chráněná položka, která má být replikována.
* Účet úložiště pro replikaci virtuálního počítače (pouze v případě, že replikujete účet úložiště). 
* Úložiště protokolu je potřeba k ochraně virtuálních počítačů k účtu úložiště premium nebo spravovanému disku.
* Procesový server, který má být použit pro replikaci. Seznam dostupných procesních serverů byl načten a uložen v proměnných ***$ProcessServers[0]****(ScaleOut-ProcessServer)* a ***$ProcessServers[1]*** *(ConfigurationServer).*  
* Účet, který se má použít k nabízení softwaru služby Mobility do počítačů. Seznam dostupných účtů byl načten a uložen v ***proměnné $AccountHandles.***
* Mapování kontejneru ochrany pro zásady replikace, které mají být použity pro replikaci.
* Skupina prostředků, ve které musí být virtuální počítače vytvořeny při převzetí služeb při selhání.
* Volitelně by měla být připojena virtuální síť Azure a podsíť, ke které by měl být virtuální počítač s poruše, který se nezdařil o virtuální počítač.

Nyní replikujte následující virtuální počítače pomocí nastavení zadaných v této tabulce


|Virtuální počítač  |Procesní server        |Účet úložiště              |Účet úložiště protokolu  |Zásada           |Účet pro instalaci služby Mobility|Cílová skupina prostředků  | Cílová virtuální síť  |Cílová podsíť  |
|-----------------|----------------------|-----------------------------|---------------------|-----------------|-----------------------------------------|-----------------------|-------------------------|---------------|
|CentoSVM1       |Konfigurační server   |Není dostupné.| logstorageaccount1                 |Zásady replikace|LinuxAccount                             |VMwareDRToAzurePs      |Asr-vnet                 |Podsíť-1       |
|Win2K12VM1       |ScaleOut-ProcessServer|premiumstorageaccount1       |logstorageaccount1   |Zásady replikace|Účet Windows                           |VMwareDRToAzurePs      |Asr-vnet                 |Podsíť-1       |   
|CentoSVM2       |Konfigurační server   |replikační účet úložiště1| Není dostupné.                 |Zásady replikace|LinuxAccount                             |VMwareDRToAzurePs      |Asr-vnet                 |Podsíť-1       |   


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

Po úspěšném dokončení úlohy povolit replikaci se spustí počáteční replikace pro virtuální počítače. Počáteční replikace může chvíli trvat v závislosti na množství dat, která mají být replikována, a na šířce pásma, která je k dispozici pro replikaci. Po dokončení počáteční replikace se virtuální počítač přesune do chráněného stavu. Jakmile virtuální počítač dosáhne chráněného stavu, můžete provést zkušební převzetí služeb při selhání pro virtuální počítač, přidat ho do plánů obnovení atd.

Můžete zkontrolovat stav replikace a stav replikace virtuálního počítače pomocí rutiny Get-ASRReplicationProtectedItem.

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

Nastavení převzetí služeb při selhání pro chráněné počítače lze aktualizovat pomocí rutiny Set-ASRReplicationProtectedItem. Některá nastavení, která lze aktualizovat prostřednictvím této rutiny, jsou:
* Název virtuálního počítače, který má být vytvořen při převzetí služeb při selhání
* Velikost virtuálního počítače virtuálního počítače, který se má vytvořit při převzetí služeb při selhání
* Virtuální síť Azure a podsíť, ke kterým by měly být síťové karty virtuálního počítače připojené při převzetí služeb při selhání
* Převzetí služeb při selhání na spravované disky
* Použití výhody hybridního využití Azure
* Přiřaďte statickou IP adresu z cílové virtuální sítě, která se přidá virtuálnímu počítači při převzetí služeb při selhání.

V tomto příkladu aktualizujeme velikost virtuálního počítače virtuálního počítače, který se má vytvořit při převzetí služeb při selhání pro virtuální počítač *Win2K12VM1,* a určíme, že virtuální počítač používá spravované disky při převzetí služeb při selhání.

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

1. Spusťte cvičení zotavení po havárii (zkušební převzetí služeb při selhání) následujícím způsobem:

   ```azurepowershell
   #Test failover of Win2K12VM1 to the test virtual network "V2TestNetwork"

   #Get details of the test failover virtual network to be used
   TestFailovervnet = Get-AzVirtualNetwork -Name "V2TestNetwork" -ResourceGroupName "asrrg" 

   #Start the test failover operation
   $TFOJob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -AzureVMNetworkId $TestFailovervnet.Id -Direction PrimaryToRecovery
   ```
2. Po úspěšném dokončení úlohy převzetí služeb při selhání testu si všimnete, že virtuální počítač s uznaný s *"-Test"* (Win2K12VM1-Test v tomto případě) na jeho název se vytvoří v Azure.
3. Nyní se můžete připojit k testu se nezdařilo přes virtuální počítač a ověřit převzetí služeb při selhání testu.
4. Vyčistěte test převzetí služeb při selhání pomocí rutiny Start-ASRTestFailoverCleanupJob. Tato operace odstraní virtuální počítač vytvořený jako součást operace převzetí služeb při selhání testu.

   ```azurepowershell
   $Job_TFOCleanup = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $ReplicatedVM1
   ```

## <a name="fail-over-to-azure"></a>Převzetí služeb při selhání do Azure

V tomto kroku jsme převzetí služeb při selhání virtuálního počítače Win2K12VM1 do konkrétního bodu obnovení.

1. Získejte seznam dostupných bodů obnovení, které chcete použít pro převzetí služeb při selhání:
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

2. Po úspěšném převzetí služeb při selhání můžete potvrdit operaci převzetí služeb při selhání a nastavit zpětnou replikaci z Azure zpět do místní lokality VMware.

## <a name="next-steps"></a>Další kroky
Zjistěte, jak automatizovat další úkoly pomocí [odkazu Azure Site Recovery PowerShell](https://docs.microsoft.com/powershell/module/Az.RecoveryServices).
