---
title: Nastavení zotavení po havárii technologie Hyper-V (s VMM) na sekundární lokalitu pomocí azure site recovery/PowerShell
description: Popisuje, jak nastavit zotavení po havárii virtuálních počítačích Hyper-V v cloudech v cloudech VMM na sekundární lokalitu VMM pomocí Azure Site Recovery a PowerShell.
services: site-recovery
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 1/10/2020
ms.author: sutalasi
ms.openlocfilehash: deef7bfdbc28d744cb81da59d3ffc13a1abee54d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77048604"
---
# <a name="set-up-disaster-recovery-of-hyper-v-vms-to-a-secondary-site-by-using-powershell-resource-manager"></a>Nastavení zotavení po havárii virtuálních virtuálních zařízení Hyper-V do sekundární lokality pomocí prostředí PowerShell (Resource Manager)

Tento článek ukazuje, jak automatizovat kroky pro replikaci virtuálních počítačů Hyper-V v cloudech Správce virtuálních strojů do cloudu Správce virtuálních strojů v sekundárním místním webu pomocí [Azure Site Recovery](site-recovery-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

- Prostudujte si [architekturu a komponenty scénáře](hyper-v-vmm-architecture.md).
- Zkontrolujte [požadavky na podporu](site-recovery-support-matrix-to-sec-site.md) pro všechny komponenty.
- Ujistěte se, že servery Virtual Machine Manager a hostitelé Technologie Hyper-V splňují [požadavky na podporu](site-recovery-support-matrix-to-sec-site.md).
- Zkontrolujte, zda virtuální počítače, které chcete replikovat, splňují [podporu replikovaného počítače](site-recovery-support-matrix-to-sec-site.md).

## <a name="prepare-for-network-mapping"></a>Příprava mapování sítě

[Mapy mapování sítě](hyper-v-vmm-network-mapping.md) mezi místními sítěmi virtuálních virtuálních zařízení Virtual Machine Manager ve zdrojových a cílových cloudech. Mapování provádí následující:

- Po převzetí služeb při selhání připojí virtuální počítače k odpovídajícím cílovým sítím virtuálních počítačů.
- Optimálně umístí repliky virtuálních počítačů na cílové hostitelské servery Hyper-V.
- Pokud nenakonfigurujete mapování sítě, nebudou virtuální počítače repliky po převzetí služeb při selhání připojeny k síti virtuálních zařízení.

Připravte správce virtuálních strojů takto:

- Ujistěte se, že máte na zdrojových a [cílových serverech](https://docs.microsoft.com/system-center/vmm/network-logical) Správce virtuálních strojů logické sítě Virtual Machine Manager:
  - Logická síť na zdrojovém serveru musí být přidružená ke zdrojovému cloudu, ve kterém se nacházejí hostitelé Hyper-V.
  - Logická síť na cílovém serveru musí být přidružená k cílovému cloudu.
- Ujistěte se, že máte [sítě virtuálních počítačů](https://docs.microsoft.com/system-center/vmm/network-virtual) na zdrojových a cílových serverech Virtual Machine Manager. Sítě virtuálních počítačů musí být v obou umístěních propojené s logickou sítí.
- Připojte virtuální počítače na zdrojových hostitelích Hyper-V ke zdrojové síti virtuálních počítačů.

## <a name="prepare-for-powershell"></a>Příprava na PowerShell

Ujistěte se, že máte Azure PowerShell připravený na další:

- Pokud už používáte PowerShell, upgradujte na verzi 0.8.10 nebo novější. [Přečtěte si další informace](/powershell/azureps-cmdlets-docs) o nastavení PowerShellu.
- Po nastavení a konfiguraci prostředí PowerShell zkontrolujte [rutiny služby](/powershell/azure/overview).
- Další informace o použití hodnot parametrů, vstupů a výstupů v Prostředí PowerShell najdete v průvodci [Začínáme.](/powershell/azure/get-started-azureps)

## <a name="set-up-a-subscription"></a>Nastavení předplatného

1. Z PowerShellu se přihlaste ke svému účtu Azure.

   ```azurepowershell
   $UserName = "<user@live.com>"
   $Password = "<password>"
   $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
   $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
   Connect-AzAccount #-Credential $Cred
   ```

1. Načtěte seznam předplatných s ID předplatného. Všimněte si ID předplatného, ve kterém chcete vytvořit trezor služby Recovery Services.

   ```azurepowershell
   Get-AzSubscription
   ```

1. Nastavte odběr pro trezor.

   ```azurepowershell
   Set-AzContext –SubscriptionID <subscriptionId>
   ```

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

1. Vytvořte skupinu prostředků Azure Resource Manager, pokud nemáte.

   ```azurepowershell
   New-AzResourceGroup -Name #ResourceGroupName -Location #location
   ```

1. Vytvořte nový trezor služby Recovery Services. Uložte objekt úschovny do proměnné, která bude použita později.

   ```azurepowershell
   $vault = New-AzRecoveryServicesVault -Name #vaultname -ResourceGroupName #ResourceGroupName -Location #location
   ```

   Objekt úschovny můžete načíst po jeho `Get-AzRecoveryServicesVault` vytvoření pomocí rutiny.

## <a name="set-the-vault-context"></a>Nastavení kontextu úschovny

1. Načíst existující trezor.

   ```azurepowershell
   $vault = Get-AzRecoveryServicesVault -Name #vaultname
   ```

1. Nastavte kontext úschovny.

   ```azurepowershell
   Set-AzRecoveryServicesAsrVaultContext -Vault $vault
   ```

## <a name="install-the-site-recovery-provider"></a>Instalace zprostředkovatele obnovení webu

1. V počítači Virtual Machine Manager vytvořte adresář spuštěním následujícího příkazu:

   ```azurepowershell
   New-Item -Path C:\ASR -ItemType Directory
   ```

1. Extrahujte soubory pomocí staženého instalačního souboru zprostředkovatele.

   ```console
   pushd C:\ASR\
   .\AzureSiteRecoveryProvider.exe /x:. /q
   ```

1. Nainstalujte zprostředkovatele a počkejte na dokončení instalace.

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

1. Zaregistrujte server v úschovně.

   ```console
   $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
   pushd $BinPath
   $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice
   ```

## <a name="create-and-associate-a-replication-policy"></a>Vytvoření a přidružení zásad replikace

1. Vytvořte zásady replikace, v tomto případě pro Hyper-V 2012 R2, takto:

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
   > Cloud správce virtuálních strojů může obsahovat hostitele Technologie Hyper-V s různými verzemi systému Windows Server, ale zásady replikace platí pro konkrétní verzi operačního systému. Pokud máte v různých operačních systémech spuštěné různé hostitele, vytvořte pro každý systém samostatné zásady replikace. Pokud máte například v systému Windows Server 2012 spuštěno pět hostitelů a tři hostitele spuštěné v systému Windows Server 2012 R2, vytvořte dvě zásady replikace. Vytvoříte jeden pro každý typ operačního systému.

1. Načtěte primární kontejner ochrany (primární cloud Správce virtuálních strojů) a kontejner ochrany obnovení (cloud pro obnovení Virtual Machine Manager).

   ```azurepowershell
   $PrimaryCloud = "testprimarycloud"
   $primaryprotectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $PrimaryCloud;

   $RecoveryCloud = "testrecoverycloud"
   $recoveryprotectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $RecoveryCloud;
   ```

1. Načtěte zásadu replikace, kterou jste vytvořili, pomocí popisný název.

   ```azurepowershell
   $policy = Get-AzRecoveryServicesAsrPolicy -FriendlyName $policyname
   ```

1. Spusťte přidružení kontejneru ochrany (cloud správce virtuálních strojů) se zásadami replikace.

   ```azurepowershell
   $associationJob  = New-AzRecoveryServicesAsrProtectionContainerMapping -Policy $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
   ```

1. Počkejte na dokončení úlohy přidružení zásad. Chcete-li zkontrolovat, zda je úloha dokončena, použijte následující fragment prostředí PowerShell:

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

Chcete-li zkontrolovat dokončení operace, postupujte podle pokynů v [části Sledování aktivity](#monitor-activity).

##  <a name="configure-network-mapping"></a>Konfigurace mapování sítě

1. Tento příkaz slouží k načtení serverů pro aktuální úschovnu. Příkaz ukládá servery site `$Servers` recovery v proměnné pole.

   ```azurepowershell
   $Servers = Get-AzRecoveryServicesAsrFabric
   ```

1. Spuštěním tohoto příkazu načtěte sítě pro zdrojový server Virtual Machine Manager a cílový server Správce virtuálních strojů.

   ```azurepowershell
   $PrimaryNetworks = Get-AzRecoveryServicesAsrNetwork -Fabric $Servers[0]

   $RecoveryNetworks = Get-AzRecoveryServicesAsrNetwork -Fabric $Servers[1]
   ```

   > [!NOTE]
   > Zdrojový server Virtual Machine Manager může být první nebo druhý v poli serveru. Zkontrolujte názvy serverů Správce virtuálních počítačů a vhodně načtěte sítě.

1. Tato rutina vytvoří mapování mezi primární sítí a sítí pro obnovení. Určuje primární síť jako první prvek `$PrimaryNetworks`programu . Určuje síť pro obnovení jako první `$RecoveryNetworks`prvek programu .

   ```azurepowershell
   New-AzRecoveryServicesAsrNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]
   ```

## <a name="enable-protection-for-vms"></a>Povolit ochranu virtuálních měn

Po správné konfiguraci serverů, cloudů a sítí povolte ochranu virtuálních počítače v cloudu.

1. Chcete-li povolit ochranu, načtěte kontejner ochrany následujícím příkazem:

   ```azurepowershell
   $PrimaryProtectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $PrimaryCloudName
   ```

1. Získejte entitu ochrany (VM), takto:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -FriendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
   ```

1. Povolte replikaci pro virtuální hod.

   ```azurepowershell
   $jobResult = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $protectionentity -ProtectionContainerMapping $policy -VmmToVmm
   ```

> [!NOTE]
> Pokud chcete replikovat na spravované disky s povolenou CMK v Azure, proveďte následující kroky pomocí prostředí Az PowerShell 3.3.0 a dále:
>
> 1. Povolení převzetí služeb při selhání na spravovaných discích aktualizací vlastností virtuálního počítače
> 1. Pomocí `Get-AzRecoveryServicesAsrReplicationProtectedItem` rutiny načíst ID disku pro každý disk chráněné položky
> 1. Vytvořte objekt slovníku pomocí `New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"` rutiny, která obsahuje mapování ID disku na sadu šifrování disku. Tyto sady šifrování disku mají být předem vytvořeny vámi v cílové oblasti.
> 1. Aktualizujte vlastnosti `Set-AzRecoveryServicesAsrReplicationProtectedItem` virtuálního počítače pomocí rutiny předáním objektu slovníku v parametru **DiskIdToDiskEncryptionSetMap.**

## <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání

Chcete-li otestovat nasazení, spusťte test převzetí služeb při selhání pro jeden virtuální počítač. Můžete také vytvořit plán obnovení, který obsahuje více virtuálních počítačů a spustit test převzetí služeb při selhání pro plán. Testovací převzetí služeb při selhání simuluje váš mechanismus převzetí služeb při selhání a zotavení v izolované síti.

1. Načíst virtuální ho virtuálního mě, do kterého virtuální chod y bude převzetí služeb při selhání.

   ```azurepowershell
   $Servers = Get-AzRecoveryServicesASRFabric
   $RecoveryNetworks = Get-AzRecoveryServicesAsrNetwork -Name $Servers[1]
   ```

1. Proveďte zkušební převzetí služeb při selhání.

   Pro jeden virtuální virtuální montovna:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrTestFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity -VMNetwork $RecoveryNetworks[1]
   ```

   Plán obnovy:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrTestFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan -VMNetwork $RecoveryNetworks[1]
   ```

Chcete-li zkontrolovat dokončení operace, postupujte podle pokynů v [části Sledování aktivity](#monitor-activity).

## <a name="run-planned-and-unplanned-failovers"></a>Spuštění plánovaných a neplánovaných převzetí služeb při selhání

1. Proveďte plánované převzetí služeb při selhání.

   Pro jeden virtuální virtuální montovna:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrPlannedFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity
   ```

   Plán obnovy:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrPlannedFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan
   ```

1. Proveďte neplánované převzetí služeb při selhání.

   Pro jeden virtuální virtuální montovna:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrUnplannedFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity
   ```

   Plán obnovy:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrUnplannedFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan
   ```

## <a name="monitor-activity"></a>Sledovat aktivitu

Ke sledování aktivity převzetí služeb při selhání použijte následující příkazy. Počkejte na dokončení zpracování mezi úlohami.

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

[Další informace](/powershell/module/az.recoveryservices) o obnovení webu pomocí rutin prostředí PowerShell správce prostředků.
