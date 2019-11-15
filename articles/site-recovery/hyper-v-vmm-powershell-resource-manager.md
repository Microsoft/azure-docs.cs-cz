---
title: Nastavení technologie Hyper-V (s nástrojem VMM) zotavení po havárii do sekundární lokality pomocí Azure Site Recovery/PowerShellu
description: Popisuje, jak nastavit zotavení po havárii virtuálních počítačů Hyper-V v cloudech VMM do sekundární lokality VMM pomocí Azure Site Recovery a PowerShellu.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: sutalasi
ms.openlocfilehash: 2fc66514bdf33611f9e6266d35a2d537fe3b9261
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084907"
---
# <a name="set-up-disaster-recovery-of-hyper-v-vms-to-a-secondary-site-by-using-powershell-resource-manager"></a>Nastavení zotavení po havárii virtuálních počítačů Hyper-V do sekundární lokality pomocí prostředí PowerShell (Správce prostředků)

Tento článek popisuje, jak automatizovat kroky pro replikaci virtuálních počítačů Hyper-V v cloudu System Center Virtual Machine Manager do cloudu Virtual Machine Manager v sekundární místní lokalitě pomocí [Azure Site Recovery](site-recovery-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

- Prostudujte si [architekturu a komponenty scénáře](hyper-v-vmm-architecture.md).
- Zkontrolujte [požadavky na podporu](site-recovery-support-matrix-to-sec-site.md) pro všechny komponenty.
- Ujistěte se, že servery Virtual Machine Manager a hostitelé Hyper-V splňují [požadavky na podporu](site-recovery-support-matrix-to-sec-site.md).
- Ověřte, že virtuální počítače, které chcete replikovat, vyhovují [podpoře replikovaných počítačů](site-recovery-support-matrix-to-sec-site.md).


## <a name="prepare-for-network-mapping"></a>Příprava na mapování sítě

[Mapování sítě](hyper-v-vmm-network-mapping.md) mezi místními Virtual Machine Manager sítěmi virtuálních počítačů ve zdrojovém a cílovém cloudu. Mapování provádí následující:

- Po převzetí služeb při selhání připojí virtuální počítače k odpovídajícím cílovým sítím virtuálních počítačů. 
- Optimálně umístí repliky virtuálních počítačů na cílové hostitelské servery Hyper-V. 
- Pokud mapování sítě nenakonfigurujete, nebudou virtuální počítače repliky po převzetí služeb při selhání připojené k síti virtuálních počítačů.

Připravte Virtual Machine Manager následujícím způsobem:

* Ujistěte se, že na zdrojovém a cílovém Virtual Machine Managerm serveru máte [Virtual Machine Manager logické sítě](https://docs.microsoft.com/system-center/vmm/network-logical) :

    - Logická síť na zdrojovém serveru musí být přidružená ke zdrojovému cloudu, ve kterém se nacházejí hostitelé Hyper-V.
    - Logická síť na cílovém serveru musí být přidružená k cílovému cloudu.
* Ujistěte se, že máte [sítě virtuálních počítačů](https://docs.microsoft.com/system-center/vmm/network-virtual) na zdrojovém a cílovém Virtual Machine Managerch serverech. Sítě virtuálních počítačů musí být v obou umístěních propojené s logickou sítí.
* Připojte virtuální počítače na zdrojových hostitelích Hyper-V ke zdrojové síti virtuálních počítačů. 

## <a name="prepare-for-powershell"></a>Příprava pro PowerShell

Ujistěte se, že jste Azure PowerShell připraveni k tomu:

- Pokud už používáte PowerShell, upgradujte na verzi 0.8.10 nebo novější. [Přečtěte si další informace](/powershell/azureps-cmdlets-docs) o tom, jak nastavit PowerShell.
- Po nastavení a konfiguraci PowerShellu si Projděte [rutiny služby](/powershell/azure/overview).
- Další informace o tom, jak používat hodnoty parametrů, vstupy a výstupy v prostředí PowerShell, najdete [v příručce Začínáme](/powershell/azure/get-started-azureps) .

## <a name="set-up-a-subscription"></a>Nastavení předplatného
1. Z PowerShellu se přihlaste ke svému účtu Azure.

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Connect-AzAccount #-Credential $Cred
2. Načtěte seznam vašich předplatných s ID předplatného. Poznamenejte si ID předplatného, ve kterém chcete vytvořit trezor Recovery Services. 

        Get-AzSubscription
3. Nastavte předplatné pro trezor.

        Set-AzContext –SubscriptionID <subscriptionId>

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení
1. Vytvořte Azure Resource Manager skupinu prostředků, pokud ji nemáte.

        New-AzResourceGroup -Name #ResourceGroupName -Location #location
2. Vytvoří nový trezor Recovery Services. Uložte objekt trezoru do proměnné pro pozdější použití. 

        $vault = New-AzRecoveryServicesVault -Name #vaultname -ResourceGroupName #ResourceGroupName -Location #location
   
    Objekt trezoru můžete načíst po jeho vytvoření pomocí rutiny Get-AzRecoveryServicesVault.

## <a name="set-the-vault-context"></a>Nastavte kontext trezoru.
1. Načtěte existující trezor.

       $vault = Get-AzRecoveryServicesVault -Name #vaultname
2. Nastavte kontext trezoru.

       Set-AzSiteRecoveryVaultSettings -ARSVault $vault

## <a name="install-the-site-recovery-provider"></a>Instalace poskytovatele Site Recovery
1. Na Virtual Machine Managerm počítači vytvořte adresář spuštěním následujícího příkazu:

       New-Item c:\ASR -type directory
2. Extrahujte soubory pomocí staženého instalačního souboru zprostředkovatele.

       pushd C:\ASR\
       .\AzureSiteRecoveryProvider.exe /x:. /q
3. Nainstalujte poskytovatele a počkejte na dokončení instalace.

       .\SetupDr.exe /i
       $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
       do
       {
         $isNotInstalled = $true;
         if(Test-Path $installationRegPath)
         {
           $isNotInstalled = $false;
         }
       }While($isNotInstalled)

4. Zaregistrujte server v trezoru.

       $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
       pushd $BinPath
       $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

## <a name="create-and-associate-a-replication-policy"></a>Vytvoření a přidružení zásady replikace
1. Vytvořte zásadu replikace v tomto případě pro Hyper-V 2012 R2 následujícím způsobem:

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $RepProvider = HyperVReplica2012R2
        $Recoverypoints = 24                    #specify the number of hours to retain recovery pints
        $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
        $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
        $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
        $InitialRepMethod = "Online" #options are "Online" or "Offline"

        $policyresult = New-AzSiteRecoveryPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod

    > [!NOTE]
    > Virtual Machine Manager Cloud může obsahovat hostitele Hyper-V s různými verzemi Windows serveru, ale zásady replikace jsou určené pro konkrétní verzi operačního systému. Pokud máte různé hostitele spuštěné v různých operačních systémech, vytvořte pro každý systém samostatné zásady replikace. Pokud máte například pět hostitelů spuštěných v systému Windows Server 2012 a tři hostitele spuštěné v systému Windows Server 2012 R2, vytvořte dvě zásady replikace. Pro každý typ operačního systému ho vytvoříte.

2. Načte primární kontejner ochrany (primární Virtual Machine Manager Cloud) a kontejner ochrany pro obnovení (Cloud Virtual Machine Manager Recovery).

       $PrimaryCloud = "testprimarycloud"
       $primaryprotectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

       $RecoveryCloud = "testrecoverycloud"
       $recoveryprotectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
3. Načtěte zásadu replikace, kterou jste vytvořili pomocí popisného názvu.

       $policy = Get-AzSiteRecoveryPolicy -FriendlyName $policyname
4. Spusťte přidružení kontejneru ochrany (Virtual Machine Manager Cloud) se zásadami replikace.

       $associationJob  = Start-AzSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
5. Počkejte, než se dokončí úloha přidružení zásad. Chcete-li zjistit, zda byla úloha dokončena, použijte následující fragment kódu prostředí PowerShell:

       $job = Get-AzSiteRecoveryJob -Job $associationJob

       if($job -eq $null -or $job.StateDescription -ne "Completed")
       {
         $isJobLeftForProcessing = $true;
       }

6. Po dokončení zpracování úlohy spusťte následující příkaz:

       if($isJobLeftForProcessing)
       {
         Start-Sleep -Seconds 60
       }
       }While($isJobLeftForProcessing)

Chcete-li ověřit dokončení operace, postupujte podle kroků v části [monitorování aktivity](#monitor-activity).

##  <a name="configure-network-mapping"></a>Konfigurace mapování sítě
1. Tento příkaz slouží k načtení serverů pro aktuální trezor. Příkaz uloží Site Recovery servery do proměnné pole $Servers.

        $Servers = Get-AzSiteRecoveryServer
2. Spusťte tento příkaz, který načte sítě pro zdrojový Virtual Machine Manager Server a cílový Virtual Machine Manager Server.

        $PrimaryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[1]

    > [!NOTE]
    > Zdrojový Virtual Machine Manager Server může být první nebo druhý v poli serveru. Ověřte Virtual Machine Manager názvy serverů a odpovídajícím způsobem načtěte sítě.


3. Tato rutina vytvoří mapování mezi primární sítí a sítí pro obnovení. Určuje primární síť jako první prvek $PrimaryNetworks. Určuje síť pro obnovení jako první prvek $RecoveryNetworks.

        New-AzSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]


## <a name="enable-protection-for-vms"></a>Povolení ochrany pro virtuální počítače
Až budou servery, cloudy a sítě správně nakonfigurované, povolte ochranu pro virtuální počítače v cloudu.

1. Chcete-li povolit ochranu, spusťte následující příkaz, který načte kontejner ochrany:

          $PrimaryProtectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
2. Získejte entitu ochrany (VM) následujícím způsobem:

           $protectionEntity = Get-AzSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
3. Povolte replikaci pro virtuální počítač.

          $jobResult = Set-AzSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy

## <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání

Chcete-li otestovat nasazení, spusťte testovací převzetí služeb při selhání pro jeden virtuální počítač. Můžete také vytvořit plán obnovení, který obsahuje více virtuálních počítačů a spustit testovací převzetí služeb při selhání pro daný plán. Testovací převzetí služeb při selhání simuluje váš mechanismus převzetí služeb při selhání a zotavení v izolované síti.

1. Načtěte virtuální počítač, do kterého se virtuální počítače převezmou.

       $Servers = Get-AzSiteRecoveryServer
       $RecoveryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[1]

2. Proveďte test převzetí služeb při selhání.

   Pro jeden virtuální počítač:

        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1]
    
   Pro plán obnovení:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1]

Chcete-li ověřit dokončení operace, postupujte podle kroků v části [monitorování aktivity](#monitor-activity).

## <a name="run-planned-and-unplanned-failovers"></a>Spuštění plánovaných a neplánovaných převzetí služeb při selhání

1. Proveďte plánované převzetí služeb při selhání.

   Pro jeden virtuální počítač:

        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   Pro plán obnovení:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan

2. Proveďte neplánované převzetí služeb při selhání.

   Pro jeden virtuální počítač:
        
        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   Pro plán obnovení:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

## <a name="monitor-activity"></a>Aktivita monitorování
Pomocí následujících příkazů můžete monitorovat činnost převzetí služeb při selhání. Počkejte na dokončení zpracování mezi úlohami.

    Do
    {
        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
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



## <a name="next-steps"></a>Další kroky

[Přečtěte si další informace](/powershell/module/az.recoveryservices) o Site Recovery s rutinami prostředí PowerShell pro správce prostředků.
