---
title: Nastavení technologie Hyper-V (s nástrojem VMM) zotavení po havárii do sekundární lokality pomocí Azure Site Recovery/PowerShellu
description: Popisuje, jak nastavit zotavení po havárii virtuálních počítačů Hyper-V v cloudech VMM do sekundární lokality VMM pomocí Azure Site Recovery a PowerShellu.
services: site-recovery
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 1/10/2020
ms.author: sutalasi
ms.openlocfilehash: a4f7d330db9a4a0d9b435ebe7527e55e37c254e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87086213"
---
# <a name="set-up-disaster-recovery-of-hyper-v-vms-to-a-secondary-site-by-using-powershell-resource-manager"></a>Nastavení zotavení po havárii virtuálních počítačů Hyper-V do sekundární lokality pomocí prostředí PowerShell (Správce prostředků)

Tento článek popisuje, jak automatizovat kroky pro replikaci virtuálních počítačů Hyper-V v cloudu System Center Virtual Machine Manager do cloudu Virtual Machine Manager v sekundární místní lokalitě pomocí [Azure Site Recovery](site-recovery-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

- Prostudujte si [architekturu a komponenty scénáře](hyper-v-vmm-architecture.md).
- Zkontrolujte [požadavky na podporu](./vmware-physical-secondary-support-matrix.md) pro všechny komponenty.
- Ujistěte se, že servery Virtual Machine Manager a hostitelé Hyper-V splňují [požadavky na podporu](./vmware-physical-secondary-support-matrix.md).
- Ověřte, že virtuální počítače, které chcete replikovat, vyhovují [podpoře replikovaných počítačů](./vmware-physical-secondary-support-matrix.md).

## <a name="prepare-for-network-mapping"></a>Příprava mapování sítě

[Mapování sítě](hyper-v-vmm-network-mapping.md) mezi místními Virtual Machine Manager sítěmi virtuálních počítačů ve zdrojovém a cílovém cloudu. Mapování provádí následující:

- Po převzetí služeb při selhání připojí virtuální počítače k odpovídajícím cílovým sítím virtuálních počítačů.
- Optimálně umístí repliky virtuálních počítačů na cílové hostitelské servery Hyper-V.
- Pokud mapování sítě nenakonfigurujete, nebudou virtuální počítače repliky po převzetí služeb při selhání připojené k síti virtuálních počítačů.

Připravte Virtual Machine Manager následujícím způsobem:

- Ujistěte se, že na zdrojovém a cílovém Virtual Machine Managerm serveru máte [Virtual Machine Manager logické sítě](/system-center/vmm/network-logical) :
  - Logická síť na zdrojovém serveru musí být přidružená ke zdrojovému cloudu, ve kterém se nacházejí hostitelé Hyper-V.
  - Logická síť na cílovém serveru musí být přidružená k cílovému cloudu.
- Ujistěte se, že máte [sítě virtuálních počítačů](/system-center/vmm/network-virtual) na zdrojovém a cílovém Virtual Machine Managerch serverech. Sítě virtuálních počítačů musí být v obou umístěních propojené s logickou sítí.
- Připojte virtuální počítače na zdrojových hostitelích Hyper-V ke zdrojové síti virtuálních počítačů.

## <a name="prepare-for-powershell"></a>Příprava pro PowerShell

Ujistěte se, že jste Azure PowerShell připraveni k tomu:

- Pokud už používáte PowerShell, upgradujte na verzi 0.8.10 nebo novější. [Přečtěte si další informace](/powershell/azure/) o tom, jak nastavit PowerShell.
- Po nastavení a konfiguraci PowerShellu si Projděte [rutiny služby](/powershell/azure/).
- Další informace o tom, jak používat hodnoty parametrů, vstupy a výstupy v prostředí PowerShell, najdete [v příručce Začínáme](/powershell/azure/get-started-azureps) .

## <a name="set-up-a-subscription"></a>Nastavení předplatného

1. Z PowerShellu se přihlaste ke svému účtu Azure.

   ```azurepowershell
   $UserName = "<user@live.com>"
   $Password = "<password>"
   $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
   $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
   Connect-AzAccount #-Credential $Cred
   ```

1. Načtěte seznam vašich předplatných s ID předplatného. Poznamenejte si ID předplatného, ve kterém chcete vytvořit trezor Recovery Services.

   ```azurepowershell
   Get-AzSubscription
   ```

1. Nastavte předplatné pro trezor.

   ```azurepowershell
   Set-AzContext –SubscriptionID <subscriptionId>
   ```

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

1. Vytvořte Azure Resource Manager skupinu prostředků, pokud ji nemáte.

   ```azurepowershell
   New-AzResourceGroup -Name #ResourceGroupName -Location #location
   ```

1. Vytvoří nový trezor Recovery Services. Uložte objekt trezoru do proměnné pro pozdější použití.

   ```azurepowershell
   $vault = New-AzRecoveryServicesVault -Name #vaultname -ResourceGroupName #ResourceGroupName -Location #location
   ```

   Objekt trezoru můžete načíst po jeho vytvoření pomocí `Get-AzRecoveryServicesVault` rutiny.

## <a name="set-the-vault-context"></a>Nastavte kontext trezoru.

1. Načtěte existující trezor.

   ```azurepowershell
   $vault = Get-AzRecoveryServicesVault -Name #vaultname
   ```

1. Nastavte kontext trezoru.

   ```azurepowershell
   Set-AzRecoveryServicesAsrVaultContext -Vault $vault
   ```

## <a name="install-the-site-recovery-provider"></a>Instalace poskytovatele Site Recovery

1. Na Virtual Machine Managerm počítači vytvořte adresář spuštěním následujícího příkazu:

   ```azurepowershell
   New-Item -Path C:\ASR -ItemType Directory
   ```

1. Extrahujte soubory pomocí staženého instalačního souboru zprostředkovatele.

   ```console
   pushd C:\ASR\
   .\AzureSiteRecoveryProvider.exe /x:. /q
   ```

1. Nainstalujte poskytovatele a počkejte na dokončení instalace.

   ```console
   .\SetupDr.exe /i
   $installationRegPath = "HKLM:\Software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
   do
   {
     $isNotInstalled = $true;
     if(Test-Path $installationRegPath)
     {
       $isNotInstalled = $false;
     }
   }While($isNotInstalled)
   ```

1. Zaregistrujte server v trezoru.

   ```console
   $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
   pushd $BinPath
   $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice
   ```

## <a name="create-and-associate-a-replication-policy"></a>Vytvoření a přidružení zásady replikace

1. Vytvořte zásadu replikace v tomto případě pro Hyper-V 2012 R2 následujícím způsobem:

   ```azurepowershell
   $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
   $PolicyName = “replicapolicy”
   $RepProvider = HyperVReplica2012R2
   $Recoverypoints = 24                    #specify the number of hours to retain recovery points
   $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
   $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
   $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
   $InitialRepMethod = "Online" #options are "Online" or "Offline"

   $policyresult = New-AzRecoveryServicesAsrPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -NumberOfRecoveryPointsToRetain $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod
   ```

   > [!NOTE]
   > Virtual Machine Manager Cloud může obsahovat hostitele Hyper-V s různými verzemi Windows serveru, ale zásady replikace jsou určené pro konkrétní verzi operačního systému. Pokud máte různé hostitele spuštěné v různých operačních systémech, vytvořte pro každý systém samostatné zásady replikace. Pokud máte například pět hostitelů spuštěných v systému Windows Server 2012 a tři hostitele spuštěné v systému Windows Server 2012 R2, vytvořte dvě zásady replikace. Pro každý typ operačního systému ho vytvoříte.

1. Načte primární kontejner ochrany (primární Virtual Machine Manager Cloud) a kontejner ochrany pro obnovení (Cloud Virtual Machine Manager Recovery).

   ```azurepowershell
   $PrimaryCloud = "testprimarycloud"
   $primaryprotectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $PrimaryCloud;

   $RecoveryCloud = "testrecoverycloud"
   $recoveryprotectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $RecoveryCloud;
   ```

1. Načtěte zásadu replikace, kterou jste vytvořili pomocí popisného názvu.

   ```azurepowershell
   $policy = Get-AzRecoveryServicesAsrPolicy -FriendlyName $policyname
   ```

1. Spusťte přidružení kontejneru ochrany (Virtual Machine Manager Cloud) se zásadami replikace.

   ```azurepowershell
   $associationJob  = New-AzRecoveryServicesAsrProtectionContainerMapping -Policy $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
   ```

1. Počkejte, než se dokončí úloha přidružení zásad. Chcete-li zjistit, zda byla úloha dokončena, použijte následující fragment kódu prostředí PowerShell:

   ```azurepowershell
   $job = Get-AzRecoveryServicesAsrJob -Job $associationJob

   if($job -eq $null -or $job.StateDescription -ne "Completed")
   {
     $isJobLeftForProcessing = $true;
   }
   ```

1. Po dokončení zpracování úlohy spusťte následující příkaz:

   ```azurepowershell
   if($isJobLeftForProcessing)
   {
     Start-Sleep -Seconds 60
   }
   While($isJobLeftForProcessing)
   ```

Chcete-li ověřit dokončení operace, postupujte podle kroků v části [monitorování aktivity](#monitor-activity).

##  <a name="configure-network-mapping"></a>Konfigurace mapování sítě

1. Tento příkaz slouží k načtení serverů pro aktuální trezor. Příkaz uloží Site Recovery servery do `$Servers` proměnné array.

   ```azurepowershell
   $Servers = Get-AzRecoveryServicesAsrFabric
   ```

1. Spusťte tento příkaz, který načte sítě pro zdrojový Virtual Machine Manager Server a cílový Virtual Machine Manager Server.

   ```azurepowershell
   $PrimaryNetworks = Get-AzRecoveryServicesAsrNetwork -Fabric $Servers[0]

   $RecoveryNetworks = Get-AzRecoveryServicesAsrNetwork -Fabric $Servers[1]
   ```

   > [!NOTE]
   > Zdrojový Virtual Machine Manager Server může být první nebo druhý v poli serveru. Ověřte Virtual Machine Manager názvy serverů a odpovídajícím způsobem načtěte sítě.

1. Tato rutina vytvoří mapování mezi primární sítí a sítí pro obnovení. Určuje primární síť jako první prvek `$PrimaryNetworks` . Určuje síť pro obnovení jako první prvek `$RecoveryNetworks` .

   ```azurepowershell
   New-AzRecoveryServicesAsrNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]
   ```

## <a name="enable-protection-for-vms"></a>Povolení ochrany pro virtuální počítače

Až budou servery, cloudy a sítě správně nakonfigurované, povolte ochranu pro virtuální počítače v cloudu.

1. Chcete-li povolit ochranu, spusťte následující příkaz, který načte kontejner ochrany:

   ```azurepowershell
   $PrimaryProtectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $PrimaryCloudName
   ```

1. Získejte entitu ochrany (VM) následujícím způsobem:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -FriendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
   ```

1. Povolte replikaci pro virtuální počítač.

   ```azurepowershell
   $jobResult = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $protectionentity -ProtectionContainerMapping $policy -VmmToVmm
   ```

> [!NOTE]
> Pokud chcete replikovat na spravované disky s povoleným CMK v Azure, proveďte následující kroky pomocí AZ PowerShell 3.3.0 a další:
>
> 1. Povolení převzetí služeb při selhání u spravovaných disků aktualizací vlastností virtuálního počítače
> 1. Pomocí `Get-AzRecoveryServicesAsrReplicationProtectedItem` rutiny načtěte ID disku pro každý disk chráněné položky.
> 1. Vytvořte objekt Dictionary pomocí `New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"` rutiny, který bude obsahovat mapování ID disku na disk pro šifrování disku. Tyto sady diskových šifrovacích sad se v cílové oblasti předem vytvoří.
> 1. Aktualizujte vlastnosti virtuálního počítače pomocí `Set-AzRecoveryServicesAsrReplicationProtectedItem` rutiny předáním objektu Dictionary v parametru **DiskIdToDiskEncryptionSetMap** .

## <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání

Chcete-li otestovat nasazení, spusťte testovací převzetí služeb při selhání pro jeden virtuální počítač. Můžete také vytvořit plán obnovení, který obsahuje více virtuálních počítačů a spustit testovací převzetí služeb při selhání pro daný plán. Testovací převzetí služeb při selhání simuluje váš mechanismus převzetí služeb při selhání a zotavení v izolované síti.

1. Načtěte virtuální počítač, do kterého se virtuální počítače převezmou.

   ```azurepowershell
   $Servers = Get-AzRecoveryServicesASRFabric
   $RecoveryNetworks = Get-AzRecoveryServicesAsrNetwork -Name $Servers[1]
   ```

1. Proveďte test převzetí služeb při selhání.

   Pro jeden virtuální počítač:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrTestFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity -VMNetwork $RecoveryNetworks[1]
   ```

   Pro plán obnovení:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrTestFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan -VMNetwork $RecoveryNetworks[1]
   ```

Chcete-li ověřit dokončení operace, postupujte podle kroků v části [monitorování aktivity](#monitor-activity).

## <a name="run-planned-and-unplanned-failovers"></a>Spuštění plánovaných a neplánovaných převzetí služeb při selhání

1. Proveďte plánované převzetí služeb při selhání.

   Pro jeden virtuální počítač:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrPlannedFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity
   ```

   Pro plán obnovení:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrPlannedFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan
   ```

1. Proveďte neplánované převzetí služeb při selhání.

   Pro jeden virtuální počítač:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrUnplannedFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity
   ```

   Pro plán obnovení:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrUnplannedFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan
   ```

## <a name="monitor-activity"></a>Aktivita monitorování

Pomocí následujících příkazů můžete monitorovat činnost převzetí služeb při selhání. Počkejte na dokončení zpracování mezi úlohami.

```azurepowershell
Do
{
    $job = Get-AzRecoveryServicesAsrJob -TargetObjectId $associationJob.JobId;
    Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
    if($job -eq $null -or $job.StateDescription -ne "Completed")
    {
        $isJobLeftForProcessing = $true;
    }

if($isJobLeftForProcessing)
    {
        Start-Sleep -Seconds 60
    }
}While($isJobLeftForProcessing)
```

## <a name="next-steps"></a>Další kroky

[Přečtěte si další informace](/powershell/module/az.recoveryservices) o Site Recovery s rutinami prostředí PowerShell pro správce prostředků.
