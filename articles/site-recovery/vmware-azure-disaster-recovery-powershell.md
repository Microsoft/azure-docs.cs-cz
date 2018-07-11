---
title: Replikace a převzetí služeb při selhání virtuálních počítačů VMware do Azure pomocí prostředí PowerShell ve službě Azure Site Recovery | Dokumentace Microsoftu
description: Zjistěte, jak nastavit replikaci a převzetí služeb při selhání do Azure pro virtuální počítače VMware pomocí prostředí PowerShell ve službě Azure Site Recovery.
services: site-recovery
author: bsiva
ms.service: site-recovery
ms.date: 07/06/2018
ms.topic: conceptual
ms.author: bsiva
ms.openlocfilehash: a826817b8f2b4ebff8442da1fbee79a95990a9e8
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2018
ms.locfileid: "37917808"
---
# <a name="replicate-and-fail-over-vmware-vms-to-azure-with-powershell"></a>Replikace a převzetí služeb při selhání virtuálních počítačů VMware do Azure pomocí Powershellu

V tomto článku uvidíte, jak provádět replikaci a převzetí služeb při selhání virtuálních počítačů VMware do Azure pomocí Azure Powershellu. 

Získáte informace o těchto tématech:

> [!div class="checklist"]
> - Vytvořte trezor služby Recovery Services a nastavte kontext trezoru.
> - Ověření registrace serveru v trezoru.
> - Nastavení replikace, včetně zásad replikace. Přidání serveru vCenter a zjištění virtuálních počítačů. > – Přidání serveru vCenter a zjišťování 
> - Vytvoření účtů úložiště pro uložení dat replikace a replikovat virtuální počítače.
> - Převzetí služeb. Konfigurace nastavení převzetí služeb při selhání, proveďte e nastavení pro replikaci virtuálních počítačů.

## <a name="prerequisites"></a>Požadavky

Než začnete, potřebujete:

- Ujistěte se, že rozumíte [komponentám a architektuře řešení](vmware-azure-architecture.md).
- Zkontrolujte [požadavky na podporu](site-recovery-support-matrix-to-azure.md) pro všechny komponenty.
- Máte – verze 5.0.1 nebo větší modul AzureRm Powershellu. Pokud potřebujete instalaci nebo upgrade prostředí Azure PowerShell, postupujte podle to [příručce k instalaci a konfiguraci prostředí Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="log-into-azure"></a>Přihlášení k Azure

Přihlaste se pomocí rutiny Connect-AzureRmAccount vašeho předplatného Azure:

```azurepowershell
Connect-AzureRmAccount
```
Vyberte předplatné Azure, které chcete replikovat virtuální počítače VMware na. Použijte rutinu Get-AzureRmSubscription zobrazíte seznam předplatných Azure, ke kterým máte přístup k. Vyberte předplatné Azure, postup při použití rutiny Select-AzureRmSubscription.

```azurepowershell
Select-AzureRmSubscription -SubscriptionName "ASR Test Subscription"
```
## <a name="set-up-a-recovery-services-vault"></a>Nastavte trezor služby Recovery Services.

1. Vytvořte skupinu prostředků, ve kterém chcete vytvořit trezor služby Recovery Services. V následujícím příkladu skupina prostředků má název VMwareDRtoAzurePS a je vytvořené v oblasti východní Asie.

   ```azurepowershell
   New-AzureRmResourceGroup -Name "VMwareDRtoAzurePS" -Location "East Asia"
   ```
   ```
   ResourceGroupName : VMwareDRtoAzurePS
   Location          : eastasia
   ProvisioningState : Succeeded
   Tags              :
   ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRtoAzurePS
```
   
2. Vytvoření trezoru služby Recovery services. V následujícím příkladu trezor služby Recovery services je s názvem VMwareDRToAzurePs a je vytvořen v oblasti východní Asie a ve skupině prostředků vytvořili v předchozím kroku.

   ```azurepowershell
   New-AzureRmRecoveryServicesVault -Name "VMwareDRToAzurePs" -Location "East Asia" -ResourceGroupName "VMwareDRToAzurePs"
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

3. Stáhněte si registrační klíč trezoru pro trezor. Registrační klíč trezoru se používá k registraci místní konfigurační Server do trezoru. Registrace je součástí procesu instalace softwaru konfiguračního serveru.

   ```azurepowershell
   #Get the vault object by name and resource group and save it to the $vault PowerShell variable 
   $vault = Get-AzureRmRecoveryServicesVault -Name "VMwareDRToAzurePS" -ResourceGroupName "VMwareDRToAzurePS"

   #Download vault registration key to the path C:\Work
   Get-AzureRmRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   ```
   ```
   FilePath
   --------
   C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials
   ```

4. Pomocí registračního klíče stažený trezor a postupujte podle pokynů v článcích uvedených níže pro dokončení instalace a registrace konfiguračního serveru.
   - [Volba cílů ochrany](vmware-azure-set-up-source.md#choose-your-protection-goals)
   - [Nastavení zdrojového prostředí](vmware-azure-set-up-source.md#set-up-the-configuration-server) 

### <a name="set-the-vault-context"></a>Nastavte kontext trezoru

Nastavte kontext trezoru pomocí rutiny Set-ASRVaultContext. Po nastavení, následné operace Azure Site Recovery v relaci Powershellu se provádějí v kontextu vybraného trezoru.

> [!TIP]
> Modul Powershellu pro Azure Site Recovery (AzureRm.RecoveryServices.SiteRecovery modul) obsahuje aliasy snadno se používá u většiny rutin. Rutiny v modulu podobu  *\<operace >-**na AzureRmRecoveryServicesAsr**\<objektu >* a mají ekvivalentní aliasy, které podobu  *\<Operace >-**Azure Site Recovery**\<objektu >*. Tento článek používá rutiny aliasy pro snadnější čtení.

V následujícím příkladu si podrobnosti o trezoru $vault proměnná slouží k určení kontext trezoru pro relaci Powershellu.

   ```azurepowershell
   Set-ASRVaultContext -Vault $vault
   ```
   ```
   ResourceName      ResourceGroupName ResourceNamespace          ResouceType
   ------------      ----------------- -----------------          -----------
   VMwareDRToAzurePs VMwareDRToAzurePs Microsoft.RecoveryServices vaults
   ```

Jako alternativu k rutině Set-ASRVaultContext jeden můžete také použít rutinu Import AzureRmRecoveryServicesAsrVaultSettingsFile nastavit kontext trezoru. Zadejte cestu, ve kterém se nachází jako parametr - path do rutiny Import-AzureRmRecoveryServicesAsrVaultSettingsFile soubor registračního klíče trezoru. Příklad:

   ```azurepowershell
   Get-AzureRmRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   Import-AzureRmRecoveryServicesAsrVaultSettingsFile -Path "C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials"
   ```
Dalších částech tohoto článku se předpokládá, že byl nastaven kontext trezoru pro operace Azure Site Recovery.

## <a name="validate-vault-registration"></a>Ověření registrace trezoru

V tomto příkladu jsme měli tyto položky:

- Konfigurační server (**ConfigurationServer**) je zaregistrován k tomuto trezoru.
- Další procesový server (**horizontálním navýšením kapacity server**) po registraci do *ConfigurationServer*
- Účty (**vCenter_account**, **WindowsAccount**, **LinuxAccount**) nastavený na konfiguračním serveru. Tyto účty se používají k přidání serveru vCenter, ke zjištění virtuálních počítačů a vynucené instalace software služby mobility na Windows a Linuxem serverech, které chcete replikovat.

1. Registrované konfigurační servery jsou reprezentované pomocí objektu prostředků infrastruktury ve službě Site Recovery. Získat seznam prostředků infrastruktury objekty v trezoru a určete konfigurační server.

   ```azurepowershell
   # Verify that the Configuration server is successfully registered to the vault
   $ASRFabrics = Get-ASRFabric
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

2. Identifikujte procesových serverů, které je možné replikovat počítače.

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

   Ve výstupu výš ***$ProcessServers [0]*** odpovídá *horizontálním navýšením kapacity server* a ***$ProcessServers [1]*** odpovídá roli procesový Server v *ConfigurationServer*

3. Určete účty, které byly vytvořeny na konfiguračním serveru.

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

   Ve výstupu výš ***$AccountHandles [0]*** odpovídá účtu *vCenter_account*, ***$AccountHandles [1]*** účet *WindowsAccount*, a ***$AccountHandles [2]*** účet *LinuxAccount*

## <a name="create-a-replication-policy"></a>Vytvoření zásady replikace

V tomto kroku se vytvoří dvě zásady replikace. Převzetí služeb při selhání jednu zásadu pro replikaci virtuálních počítačů VMware do Azure a druhý k replikaci virtuálních počítačů spuštěných v Azure zpět do místní lokality VMware.

> [!NOTE]
> Většinu operací Azure Site Recovery se provedl asynchronně. Při zahájení operace odeslání úlohy služby Azure Site Recovery a úlohy sledování objektu se vrátí. Tato úloha sledování objektů lze použít ke sledování stavu operace.

1. Vytvoření zásady replikace s názvem *ReplicationPolicy* pro replikaci virtuálních počítačů VMware do Azure pomocí zadané vlastnosti.

   ```azurepowershell
   $Job_PolicyCreate = New-ASRPolicy -VMwareToAzure -Name "ReplicationPolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60

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

2. Vytvoření zásady replikace pro navrácení služeb po obnovení z Azure do místní lokality VMware.

   ```azurepowershell
   $Job_FailbackPolicyCreate = New-ASRPolicy -AzureToVMware -Name "ReplicationPolicy-Failback" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60
   ```

   Podrobnosti úlohy v *$Job_FailbackPolicyCreate* ke sledování dokončení operace.

   * Vytvořte mapování kontejnerů ochrany mapovat zásady replikace s konfiguračním serverem.

   ```azurepowershell
   #Get the protection container corresponding to the Configuration Server
   $ProtectionContainer = Get-ASRProtectionContainer -Fabric $ASRFabrics[0]

   #Get the replication policies to map by name.
   $ReplicationPolicy = Get-ASRPolicy -Name "ReplicationPolicy"
   $FailbackReplicationPolicy = Get-ASRPolicy -Name "ReplicationPolicy-Failback"

   # Associate the replication policies to the protection container corresponding to the Configuration Server. 

   $Job_AssociatePolicy = New-ASRProtectionContainerMapping -Name "PolicyAssociation1" -PrimaryProtectionContainer $ProtectionContainer -Policy $ReplicationPolicy

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
    $Job_AssociateFailbackPolicy = New-ASRProtectionContainerMapping -Name "FailbackPolicyAssociation" -PrimaryProtectionContainer $ProtectionContainer -RecoveryProtectionContainer $ProtectionContainer -Policy $FailbackReplicationPolicy

   # Check the job status
   while (($Job_AssociateFailbackPolicy.State -eq "InProgress") -or ($Job_AssociateFailbackPolicy.State -eq "NotStarted")){ 
           sleep 10; 
           $Job_AssociateFailbackPolicy = Get-ASRJob -Job $Job_AssociateFailbackPolicy
   }
   $Job_AssociateFailbackPolicy.State

   ```

## <a name="add-a-vcenter-server-and-discover-vms"></a>Přidání serveru vCenter a zjištění virtuálních počítačů

Přidání serveru vCenter pomocí IP adresy nebo názvu hostitele. **-Port** parametr určuje port, který na serveru vCenter připojit, **– název** parametr určuje popisný název pro vCenter server a **– účet** parametr určuje popisovač účtu na konfiguračním serveru pro virtuální počítače spravované pomocí vCenter serveru.

```azurepowershell
# The $AccountHandles[0] variable holds details of vCenter_account

$Job_AddvCenterServer = New-ASRvCenter -Fabric $ASRFabrics[0] -Name "MyvCenterServer" -IpOrHostName "10.150.24.63" -Account $AccountHandles[0] -Port 443

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

V tomto kroku se vytvoří účty úložiště pro replikaci. Tyto účty úložiště jsou později použít k replikaci virtuálních počítačů. Ujistěte se, že jsou ve stejné oblasti Azure jako trezor vytvoří účty storage. Pokud chcete použít existující účet úložiště pro replikaci, můžete tento krok přeskočit.

> [!NOTE]
> Při replikaci místních virtuálních počítačů na účet úložiště úrovně premium, budete muset zadat další standard storage účtu (účet úložiště protokolů). Účet úložiště protokolů obsahuje protokoly replikace jako zprostředkující úložiště, dokud se dají použít protokoly na cíli úložiště úrovně premium.
>

```azurepowershell

$PremiumStorageAccount = New-AzureRmStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "premiumstorageaccount1" -Location "East Asia" -SkuName Premium_LRS

$LogStorageAccount = New-AzureRmStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "logstorageaccount1" -Location "East Asia" -SkuName Standard_LRS

$ReplicationStdStorageAccount= New-AzureRmStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "replicationstdstorageaccount1" -Location "East Asia" -SkuName Standard_LRS
```

## <a name="replicate-vmware-vms"></a>Replikace virtuálních počítačů VMware

Trvá asi 15 až 20 minut, než virtuální počítače mají být zjišťované ze serveru vCenter. Po zjištění, je vytvořen objekt chránitelnou položku ve službě Azure Site Recovery pro každé zjištěné virtuální počítač. V tomto kroku tři zjištěné virtuální počítače se replikují do Azure Storage účty vytvořené v předchozím kroku.

Budete potřebovat následující podrobnosti, které chcete chránit zjištěné virtuální počítač:

* Chránitelnou položku se musí replikovat.
* Účet úložiště pro replikaci virtuálního počítače. Kromě toho je potřeba úložiště protokolů k ochraně virtuálních počítačů na účet úložiště úrovně premium.
* Procesový Server má být použit pro replikaci. Načíst a uloží do seznamu k dispozici procesových serverů ***$ProcessServers [0]****(škálování aplikace server)* a ***$ProcessServers [1]*** *(ConfigurationServer)* proměnné.  
* Účet, který se má použít pro vynucené instalace software služby Mobility na počítače. Byla načten a uložené v seznamu dostupných účtů ***$AccountHandles*** proměnné.
* Mapování kontejnerů ochrany pro zásady replikace pro replikaci.
* Skupina prostředků, ve kterém musí být vytvořen virtuální počítače na převzetí služeb při selhání.
* Volitelně můžete virtuální síť Azure a podsíť, ke které se přes virtuální počítač by měl být připojen.

Teď replikujte pomocí nastavení uvedená v této tabulce následujících virtuálních počítačů


|Virtuální počítač  |Procesový Server        |Účet úložiště              |Účet úložiště protokolů  |Zásada           |Účet pro instalaci služby Mobility|Cílová skupina prostředků  | Cílová virtuální síť  |Cílová podsíť  |
|-----------------|----------------------|-----------------------------|---------------------|-----------------|-----------------------------------------|-----------------------|-------------------------|---------------|
|Win2K12VM1       |ScaleOut-ProcessServer|premiumstorageaccount1       |logstorageaccount1   |ReplicationPolicy|WindowsAccount                           |VMwareDRToAzurePs      |Azure Site Recovery vnet                 |Subnet-1       |
|CentOSVM1       |ConfigurationServer   |replicationstdstorageaccount1| neuvedeno                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |Azure Site Recovery vnet                 |Subnet-1       |   
|CentOSVM2       |ConfigurationServer   |replicationstdstorageaccount1| neuvedeno                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |Azure Site Recovery vnet                 |Subnet-1       |   

 
```azurepowershell

#Get the target resource group to be used
$ResourceGroup = Get-AzureRmResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzureRmVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg" 

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap  = Get-ASRProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine Win2K12VM1
$VM1 = Get-ASRProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "Win2K12VM1"

# Enable replication for virtual machine Win2K12VM1
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableRepication1 = New-ASRReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $PremiumStorageAccount.Id -LogStorageAccountId $LogStorageAccount.Id -ProcessServer $ProcessServers[0] -Account $AccountHandles[1] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" 

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM2 = Get-ASRProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1
$Job_EnableRepication2 = New-ASRReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM2 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $ReplicationStdStorageAccount.Id  -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine CentOSVM2
$VM3 = Get-ASRProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM2"

# Enable replication for virtual machine CentOSVM2
$Job_EnableRepication3 = New-ASRReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM3 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $ReplicationStdStorageAccount.Id  -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" 

```

Po úspěšném dokončení úlohy replikace povolit spuštění počáteční replikace pro virtuální počítače. Počáteční replikace může chvíli trvat v závislosti na množství dat se musí replikovat a dostupné šířky pásma pro replikaci. Po dokončení počáteční replikace virtuálního počítače přejde do chráněném stavu. Jakmile se virtuální počítač dosáhne chráněném stavu, které můžete provést testovací převzetí služeb virtuálního počítače, přidejte jej do plánů obnovení atd.

Můžete zkontrolovat stav replikace a stav replikace virtuálního počítače pomocí rutiny Get-ASRReplicationProtectedItem.

```azurepowershell
Get-ASRReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState                 ReplicationHealth
------------ ---------------                 -----------------
CentOSVM1    Protected                       Normal
CentOSVM2    InitialReplicationInProgress    Normal
Win2K12VM1   Protected                       Normal
```

## <a name="configure-failover-settings"></a>Konfigurace nastavení převzetí služeb při selhání

Nastavení převzetí služeb při selhání u chráněných počítačů můžete aktualizovat pomocí rutiny Set-ASRReplicationProtectedItem. Zde jsou některé z nastavení, která jde aktualizovat prostřednictvím této rutiny:
* Název virtuálního počítače, který má být vytvořena na převzetí služeb při selhání
* Velikost virtuálního počítače pro virtuální počítač má být vytvořena na převzetí služeb při selhání
* Virtuální síť Azure a podsíť, která by měla připojené síťové karty virtuálního počítače na převzetí služeb při selhání
* Převzetí služeb při selhání na spravované disky
* Použít program Azure Hybrid Use Benefit
* Přiřadíte statickou IP adresu z cílová virtuální síť má být přiřazena k virtuálnímu počítači na převzetí služeb při selhání.

V tomto příkladu aktualizujeme velikost virtuálního počítače z virtuálního počítače na převzetí služeb při selhání pro virtuální počítač vytvořit *Win2K12VM1* a určit, že virtuální počítač použít spravované disky na převzetí služeb při selhání.

```azurepowershell
$ReplicatedVM1 = Get-ASRReplicationProtectedItem -FriendlyName "Win2K12VM1" -ProtectionContainer $ProtectionContainer

Set-ASRReplicationProtectedItem -InputObject $ReplicatedVM1 -Size "Standard_DS11" -UseManagedDisk True
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

1. Spusťte postup zotavení po Havárii (testovací převzetí služeb) následujícím způsobem:

   ```azurepowershell
   #Test failover of Win2K12VM1 to the test virtual network "V2TestNetwork"

   #Get details of the test failover virtual network to be used
   TestFailovervnet = Get-AzureRmVirtualNetwork -Name "V2TestNetwork" -ResourceGroupName "asrrg" 

   #Start the test failover operation
   $TFOJob = Start-ASRTestFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -AzureVMNetworkId $TestFailovervnet.Id -Direction PrimaryToRecovery
   ```
2. Po úspěšném dokončení úlohy převzetí služeb při selhání testu, všimnete si, že virtuální počítač doplněny *"-Test"* (Win2K12VM1 a testování v tomto případě) k názvu se vytvoří v Azure.
3. Teď můžete připojit k testovací převzetí služeb virtuálního počítače při selhání a ověření testu převzetí služeb.
4. Vyčistěte testovací převzetí služeb pomocí rutiny Start-ASRTestFailoverCleanupJob. Tato operace odstraní virtuální počítač vytvořený jako součást operace převzetí služeb při selhání testu.

   ```azurepowershell
   $Job_TFOCleanup = Start-ASRTestFailoverCleanupJob -ReplicationProtectedItem $ReplicatedVM1
   ```

## <a name="fail-over-to-azure"></a>Převzetí služeb při selhání do Azure

V tomto kroku jsme převzetí služeb při selhání virtuálního počítače Win2K12VM1 konkrétní bod obnovení.

1. Získáte seznam dostupných bodů obnovení pro převzetí služeb:
   ```azurepowershell
   # Get the list of available recovery points for Win2K12VM1
   $RecoveryPoints = Get-ASRRecoveryPoint -ReplicationProtectedItem $ReplicatedVM1
   "{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[0].RecoveryPointTime
   ```
   ```
   CrashConsistent 11/24/2017 5:28:25 PM
   ```
   ```azurepowershell

   #Start the failover job
   $Job_Failover = Start-ASRUnplannedFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[0]
   do {
           $Job_Failover = Get-ASRJob -Job $Job_Failover;
           sleep 60;
   } while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))
   $Job_Failover.State
   ```
   ```
   Succeeded
   ```

2. Po selhání, úspěšně jste můžete potvrzení operace převzetí služeb při selhání a zadejte zpětné replikace z Azure zpět do místní lokality VMware.

## <a name="next-steps"></a>Další postup
Zjistěte, jak automatizovat pomocí další úlohy [Powershellu pro Azure Site Recovery odkaz ](https://docs.microsoft.com/powershell/module/AzureRM.RecoveryServices.SiteRecovery).
