---
title: Replikovat a převzít služby virtuálních počítačů VMware do Azure pomocí prostředí PowerShell ve službě Azure Site Recovery | Microsoft Docs
description: Zjistěte, jak nastavit replikaci a převzetí služeb při selhání do Azure pro virtuální počítače VMware pomocí prostředí PowerShell v Azure Site Recovery.
services: site-recovery
author: bsiva
manager: abhemraj
editor: raynew
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: bsiva
ms.openlocfilehash: 2a0f183770a07cd05963e88c672452cfeb80e567
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="replicate-and-fail-over-vmware-vms-to-azure-with-powershell"></a>Replikovat a převzít služby virtuálních počítačů VMware do Azure pomocí prostředí PowerShell

V tomto článku najdete postupy k replikaci a převzetí služeb při selhání virtuálních počítačů VMware do Azure pomocí Azure PowerShell. 

Získáte informace o těchto tématech:

> [!div class="checklist"]
> - Vytvořte trezor služby Recovery Services.
> - Nastavte kontext úložiště.
> - Ověřte, že je konfigurační Server a škálování proces servery registrovaný k úložišti.
> - Vytvořte zásadu replikace a namapovat je pro použití s konfiguračním serverem.
> - Přidání serveru vCenter a zjistit virtuální počítače VMware.
> - Vytvořte účty úložiště pro replikaci virtuálních počítačů.
> - Replikace virtuálních počítačů VMware do účtů úložiště Azure.
> - Nakonfigurujte nastavení převzetí služeb při selhání pro replikaci virtuálních počítačů.
> - Provedení testu převzetí služeb, ověřit a vyčistit testovací převzetí služeb při selhání.
> - Převzetí služeb při selhání do Azure.

## <a name="prerequisites"></a>Požadavky

Než začnete, potřebujete:
- Ujistěte se, že rozumíte [komponentám a architektuře řešení](vmware-azure-architecture.md).
- Zkontrolujte [požadavky na podporu](site-recovery-support-matrix-to-azure.md) pro všechny komponenty.
- Máte verzi – 5.0.1 nebo větší modulu AzureRm prostředí PowerShell. Pokud potřebujete nainstalovat nebo upgradovat prostředí Azure PowerShell, postupujte podle to [Průvodce instalace a konfigurace prostředí Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Přihlaste se k předplatnému Microsoft Azure

Přihlaste se k předplatnému Azure, pomocí rutiny Connect-AzureRmAccount

```azurepowershell
Connect-AzureRmAccount
```
Vyberte předplatné Azure, které chcete replikovat virtuální počítače VMware. Použijte rutinu Get-AzureRmSubscription získat přístup k seznamu předplatných Azure, které máte. Vyberte předplatné Azure, postup při použití rutiny Select-AzureRmSubscription.

```azurepowershell
Select-AzureRmSubscription -SubscriptionName "ASR Test Subscription"
```
## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

* Vytvořte skupinu prostředků, ve které chcete vytvořit trezor služeb zotavení. V následujícím příkladu skupina prostředků je s názvem VMwareDRtoAzurePS a je vytvořen v oblasti východní Asie.

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
   
* Vytvoření trezoru služeb zotavení. V příkladu níže trezor služeb zotavení jmenuje VMwareDRToAzurePs a je vytvořen v oblasti, východní Asie a ve skupině prostředků vytvořili v předchozím kroku.

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

* Stáhněte si registrační klíč trezoru trezoru. Registrační klíč trezoru se používá k registraci místní konfigurační Server k tomuto úložišti. Registrace je součástí procesu instalace softwarové konfigurační Server.

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

Pomocí registračního klíče stažený trezoru a postupujte podle kroků v článcích níže uvedené na úplnou instalaci a registraci konfigurační Server.
* [Volba cílů ochrany](vmware-azure-set-up-source.md#choose-your-protection-goals)
* [Nastavení zdrojového prostředí](vmware-azure-set-up-source.md#set-up-the-configuration-server) 

## <a name="set-the-vault-context"></a>Nastavit kontext trezoru

> [!TIP]
> Modul Azure PowerShell obnovení lokality (AzureRm.RecoveryServices.SiteRecovery modul) obsahuje snadno použitelný aliasy pro většinu rutin. Rutiny v modulu ve formě  *\<operaci >-**AzureRmRecoveryServicesAsr**\<objekt >* a mít ekvivalentní aliasů, které mají formát  *\<Operaci >-**automatické obnovení systému**\<objekt >*. Tento článek používá rutinu aliasy pro snadnější čtení.

Nastavte pomocí rutiny Set-ASRVaultContext kontext úložiště. Po nastavení, následných operací Azure Site Recovery v relaci prostředí PowerShell se provádějí v kontextu vybraného trezoru. V následujícím příkladu podrobnosti trezoru z $vault proměnná slouží k určení kontextu trezoru pro relace prostředí PowerShell.
 ```azurepowershell
Set-ASRVaultContext -Vault $vault
```
```
ResourceName      ResourceGroupName ResourceNamespace          ResouceType
------------      ----------------- -----------------          -----------
VMwareDRToAzurePs VMwareDRToAzurePs Microsoft.RecoveryServices vaults
```

> [!TIP]
> Jako alternativu k rutinu Set-ASRVaultContext jeden můžete také použít rutinu Import AzureRmRecoveryServicesAsrVaultSettingsFile nastavit kontext úložiště. Zadejte cestu, kde je umístěn jako parametr - path do rutiny Import-AzureRmRecoveryServicesAsrVaultSettingsFile soubor registračního klíče trezoru.
>
>Příklad:
>
>```azurepowershell
>Get-AzureRmRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
>
>Import-AzureRmRecoveryServicesAsrVaultSettingsFile -Path "C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials"
>```
>

Následující části tohoto článku předpokládá, že byl nastaven kontext trezoru Azure Site Recovery operations.

## <a name="validate-that-your-configuration-server-and-scale-out-process-servers-are-registered-to-the-vault"></a>Ověřte, že je konfigurační Server a škálování proces servery registrovaný k úložišti

 Předpokládejme:
- Konfigurace serveru s názvem *ConfigurationServer* je zaregistrován k tomuto úložišti
- Dalšího procesového serveru s názvem *škálování server* registrován tak, aby *ConfigurationServer*
- Účty s názvem *vCenter_account*, *WindowsAccount*, a *LinuxAccount* již byly vytvořeny na konfiguračním serveru. Tyto účty se používají k přidání serveru vCenter se zjistit virtuální počítače a vynucené instalace softwaru služby mobility na serverech Windows a Linux, které se mají replikovat.

Registrované servery konfigurace jsou reprezentované objektem prostředků infrastruktury v Azure Site Recovery. V tomto kroku získat seznam prostředků infrastruktury objekty v trezoru a určete konfigurační Server.

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

* Identifikujte servery procesu, které je možné replikovat počítače.

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

Ve výstupu výš ***$ProcessServers [0]*** odpovídá *škálování server* a ***$ProcessServers [1]*** odpovídá roli procesový Server na *ConfigurationServer*

* Identifikujte účty, které již byly vytvořeny na konfiguračním serveru.

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

Ve výstupu výš ***$AccountHandles [0]*** odpovídá účtu *vCenter_account*, ***$AccountHandles [1]*** k účtu *WindowsAccount*, a ***$AccountHandles [2]*** k účtu *LinuxAccount*

## <a name="create-a-replication-policy-and-map-it-for-use-with-the-configuration-server"></a>Vytvořte zásadu replikace a namapovat je pro použití s konfiguračním serverem

V tomto kroku jsou vytvořeny dvě zásady replikace. Převzetí služeb při selhání jedné zásadě replikace virtuálních počítačů VMware do Azure a druhá replikovat virtuální počítače běžící v Azure zpět na místní lokalita VMware.

> [!NOTE]
> Většinu operací Azure Site Recovery se spustí asynchronně. Při zahájení operace odeslání úlohu služby Azure Site Recovery a úlohy sledování objekt je vrácen. Tato úloha sledování objektu slouží k monitorování stavu operace.

* Vytvořte zásadu replikace s názvem *ReplicationPolicy* k replikaci virtuálních počítačů VMware do Azure pomocí zadané vlastnosti.

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

* Vytvořte zásadu replikace pro navrácení služeb po obnovení z Azure do místní lokalita VMware.

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

$Job_AssociatePolicy = New-ASRProtectionContainerMapping -Name "PolicyAssociation1" -PrimaryProtectionContainer $ProtectionContainer -Policy $Policy[0]

# Check the job status
while (($Job_AssociatePolicy.State -eq "InProgress") -or ($Job_AssociatePolicy.State -eq "NotStarted")){ 
        sleep 10; 
        $Job_AssociatePolicy = Get-ASRJob -Job $Job_AssociatePolicy
}
$Job_AssociatePolicy.State

$Job_AssociateFailbackPolicy = New-ASRProtectionContainerMapping -Name "FailbackPolicyAssociation" -PrimaryProtectionContainer $ProtectionContainer -Policy $Policy[0]

# Check the job status
while (($Job_AssociateFailbackPolicy.State -eq "InProgress") -or ($Job_AssociateFailbackPolicy.State -eq "NotStarted")){ 
        sleep 10; 
        $Job_AssociateFailbackPolicy = Get-ASRJob -Job $Job_AssociateFailbackPolicy
}
$Job_AssociateFailbackPolicy.State

```

## <a name="add-a-vcenter-server-and-discover-vms"></a>Přidání serveru vCenter a zjistit virtuální počítače

Přidání systému vCenter Server pomocí IP adresy nebo názvu hostitele. **-Port** parametr určuje port, na serveru vCenter server připojit, **-název** parametr určuje popisný název pro vCenter server a **-účet** parametr určuje popisovač účet na konfiguračním serveru má použít pro vyhledávání virtuálních počítačů spravovaných pomocí serveru vCenter server.

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

## <a name="create-storage-accounts"></a>Vytvoření účtů úložiště

V tomto kroku jsou vytvořeny účty úložiště, který se má použít pro replikaci. Tyto účty úložiště jsou později používanou k replikaci virtuálních počítačů. Ujistěte se, že vytvoření účtů úložiště ve stejné oblasti Azure jako trezor. Pokud budete chtít použít stávající účet úložiště pro replikaci, můžete tento krok přeskočit.

> [!NOTE]
> Při replikaci lokálních virtuálních počítačů na účet úložiště premium, budete muset zadat účet další standardní úložiště (účet úložiště protokolu). Účet úložiště protokolu obsahuje protokoly replikace jako zprostředkující úložiště, dokud protokoly lze použít na cíli úložiště premium.
>

```azurepowershell

$PremiumStorageAccount = New-AzureRmStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "premiumstorageaccount1" -Location "East Asia" -SkuName Premium_LRS

$LogStorageAccount = New-AzureRmStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "logstorageaccount1" -Location "East Asia" -SkuName Standard_LRS

$ReplicationStdStorageAccount= New-AzureRmStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "replicationstdstorageaccount1" -Location "East Asia" -SkuName Standard_LRS
```

## <a name="replicate-vmware-virtual-machines"></a>Replikace virtuálních počítačů VMware

Jak dlouho trvá asi 15-20 minut pro virtuální počítače, které mají být zjišťované ze serveru vCenter. Po zjištění objekt chránitelná položka se vytvoří v Azure Site Recovery pro každý zjištěný virtuální počítač. V tomto kroku tři zjištěné virtuální počítače jsou replikovány do účty Azure Storage vytvořené v předchozím kroku.

Budete potřebovat následující podrobnosti k ochraně zjištěných virtuálního počítače:
* Chránitelná položka replikovat.
* Účet úložiště k replikaci virtuálního počítače. Kromě toho úložiště protokolů je potřeba k ochraně virtuálních počítačů na účet úložiště premium.
* Procesový Server, který se má použít pro replikaci. Načíst a uložit v seznamu serverů procesu k dispozici ***$ProcessServers [0]****(škálování-Server)* a ***$ProcessServers [1]*** *(ConfigurationServer)* proměnné.
* Účet, který se má použít pro vynucené instalace softwaru služby Mobility na počítačích. Načíst a uložené v seznamu dostupných účtů ***$AccountHandles*** proměnné.
* Mapování kontejnerů ochrany pro zásady replikace, který se má použít pro replikaci.
* Skupinu prostředků, ve kterém musí být vytvořen virtuální počítače na převzetí služeb při selhání.
* Volitelně můžete virtuální síť Azure a podsíť, ke které neúspěšný přes virtuální počítač by měly být připojené.

Nyní replikovat následující virtuální počítače pomocí nastavení zadané v této tabulce


|Virtuální počítač  |Procesový Server        |Účet úložiště              |Účet úložiště protokolu  |Zásada           |Účet pro instalaci služby Mobility|Cílová skupina prostředků  | Cílová virtuální síť  |Cílové podsíti  |
|-----------------|----------------------|-----------------------------|---------------------|-----------------|-----------------------------------------|-----------------------|-------------------------|---------------|
|Win2K12VM1       |ScaleOut-ProcessServer|premiumstorageaccount1       |logstorageaccount1   |ReplicationPolicy|WindowsAccount                           |VMwareDRToAzurePs      |Automatické obnovení systému vnet                 |Podsíť 1       |
|CentOSVM1       |ConfigurationServer   |replicationstdstorageaccount1| neuvedeno                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |Automatické obnovení systému vnet                 |Podsíť 1       |   
|CentOSVM2       |ConfigurationServer   |replicationstdstorageaccount1| neuvedeno                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |Automatické obnovení systému vnet                 |Podsíť 1       |   

 
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

Po úspěšném dokončení úlohy replikace povolit spuštění počáteční replikace pro virtuální počítače. Počáteční replikace může trvat v závislosti na množství dat, které se budou replikovat a dostupné šířky pásma pro replikaci. Po dokončení počáteční replikace virtuálního počítače se přesune na chráněném stavu. Jakmile je virtuální počítač dosáhne chráněném stavu, které můžete provádět testovací převzetí služeb při selhání pro virtuální počítač, přidejte ji do plánů obnovení atd.

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

## <a name="configure-failover-settings-for-replicating-virtual-machines"></a>Konfigurace nastavení převzetí služeb při selhání pro replikaci virtuálních počítačů

Pomocí rutiny Set-ASRReplicationProtectedItem můžete aktualizovat nastavení převzetí služeb při selhání pro chráněné počítače. Některá nastavení, která se dá aktualizovat přes tato rutina jsou:
* Název virtuálního počítače, který má být vytvořena na převzetí služeb při selhání
* Velikost virtuálního počítače z virtuálního počítače, který má být vytvořena na převzetí služeb při selhání
* Virtuální síť Azure a podsíť, síťové adaptéry virtuálního počítače musí být připojené k na převzetí služeb při selhání
* Převzetí služeb při selhání na spravované disky
* Použití Azure hybridní použití zvýhodnění
* Přiřadíte statickou IP adresu z cílové virtuální sítě pro přiřazení k virtuálnímu počítači na převzetí služeb při selhání.

V tomto příkladu budeme aktualizovat velikost virtuálního počítače z virtuálního počítače, který má být vytvořena na převzetí služeb při selhání pro virtuální počítač *Win2K12VM1* a určit, že virtuální počítač použijte spravovat disky na převzetí služeb při selhání.

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

## <a name="perform-a-test-failover-validate-and-cleanup-test-failover"></a>Provedení testu převzetí služeb, ověřit a vyčistit testovací převzetí služeb při selhání

```azurepowershell
#Test failover of Win2K12VM1 to the test virtual network "V2TestNetwork"

#Get details of the test failover virtual network to be used
TestFailovervnet = Get-AzureRmVirtualNetwork -Name "V2TestNetwork" -ResourceGroupName "asrrg" 

#Start the test failover operation
$TFOJob = Start-ASRTestFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -AzureVMNetworkId $TestFailovervnet.Id -Direction PrimaryToRecovery
```
Po úspěšném dokončení testovací převzetí služeb při selhání úlohy si všimnete, že virtuální počítač na konci *"-Test"* (Win2K12VM1-Test v tomto případě) na jeho název je vytvořena v Azure. 

Teď můžete připojit k testu při selhání virtuálního počítače a ověření převzetí služeb při selhání.

Čištění testovacího převzetí služeb při selhání pomocí rutiny Start-ASRTestFailoverCleanupJob. Tato operace odstraní virtuální počítač vytvořen jako součást operace testovacího převzetí služeb při selhání.

```azurepowershell
$Job_TFOCleanup = Start-ASRTestFailoverCleanupJob -ReplicationProtectedItem $ReplicatedVM1
```

## <a name="failover-to-azure"></a>Převzetí služeb při selhání do Azure

V tomto kroku jsme převzetí služeb při selhání virtuálního počítače Win2K12VM1 konkrétní bod obnovení.

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

Po převzetí služeb při selhání úspěšně, můžete provést převzetí služeb při selhání operace a instalační program zpětná replikace z Azure zpět na místní lokalita VMware.

## <a name="next-steps"></a>Další postup
Zobrazení [Azure Site Recovery PowerShell odkaz ](https://docs.microsoft.com/powershell/module/AzureRM.RecoveryServices.SiteRecovery) se dozvíte, jak můžete provádět další úlohy, jako je například vytváření plánů obnovení a testování převzetí služeb při selhání plánů obnovení pomocí prostředí PowerShell.
