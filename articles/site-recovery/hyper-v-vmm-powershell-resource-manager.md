---
title: Replikace virtuálních počítačů technologie Hyper-V v cloudech nástroje Virtual Machine Manager sekundární lokality pomocí prostředí PowerShell (Azure Resource Manager) | Microsoft Docs
description: Popisuje, jak replikovat virtuální počítače Hyper-V v cloudech nástroje Virtual Machine Manager do sekundární lokality nástroje Virtual Machine Manager pomocí prostředí PowerShell (Resource Manager)
services: site-recovery
author: sujaytalasila
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/12/2018
ms.author: sutalasi
ms.openlocfilehash: 7c6af1b63d9e7904f5a397200c6950c62df08832
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="replicate-hyper-v-vms-to-a-secondary-site-by-using-powershell-resource-manager"></a>Replikace virtuálních počítačů technologie Hyper-V do sekundární lokality pomocí prostředí PowerShell (Resource Manager)

Tento článek ukazuje, jak automatizovat kroky pro replikaci virtuálních počítačů technologie Hyper-V v cloudech System Center Virtual Machine Manager do nástroje Virtual Machine Manager cloudu na sekundární místní lokalitu pomocí [Azure Site Recovery](site-recovery-overview.md).

## <a name="prerequisites"></a>Požadavky

- Zkontrolujte [scénář architektura a komponenty](hyper-v-vmm-architecture.md).
- Zkontrolujte [požadavky na podporu](site-recovery-support-matrix-to-sec-site.md) pro všechny komponenty.
- Ujistěte se, že servery nástroje Virtual Machine Manager a hostitelů Hyper-V souladu s [podporu požadavků](site-recovery-support-matrix-to-sec-site.md).
- Zkontrolujte, zda chcete replikovat virtuální počítače v souladu s [replikovat podpora počítačů](site-recovery-support-matrix-to-sec-site.md).


## <a name="prepare-for-network-mapping"></a>Příprava mapování sítě

[Mapování sítě](hyper-v-vmm-network-mapping.md) mapy mezi místní sítí virtuálních počítačů nástroje Virtual Machine Manager ve zdrojové a cílové cloudy. Mapování provede následující akce:

- Virtuální počítače se připojí k příslušné cílové sítě virtuálních počítačů po převzetí služeb při selhání. 
- Optimálně umístí virtuální počítače repliky na cílové servery hostitele technologie Hyper-V. 
- Pokud nenakonfigurujete mapování sítě, virtuální počítače replik nebude připojen k síti virtuálních počítačů po převzetí služeb při selhání.

Příprava nástroje Virtual Machine Manager následujícím způsobem:

* Zajistěte, aby byla [logické sítě nástroje Virtual Machine Manager](https://docs.microsoft.com/system-center/vmm/network-logical) na zdrojové a cílové servery nástroje Virtual Machine Manager:

    - Logické sítě na zdrojovém serveru by měly být přidružené zdrojový cloud, ve které se nacházejí hostitelů Hyper-V.
    - Logické sítě na cílovém serveru by měly být přidružené cílový cloud.
* Zajistěte, aby byla [sítě virtuálních počítačů](https://docs.microsoft.com/system-center/vmm/network-virtual) na zdrojové a cílové servery nástroje Virtual Machine Manager. Sítě virtuálních počítačů musí být propojena na logickou síť v každém umístění.
* Připojte virtuální počítače na hostitelích technologie Hyper-V zdroj ke zdrojové síti virtuálních počítačů. 

## <a name="prepare-for-powershell"></a>Příprava pro prostředí PowerShell

Ujistěte se, že máte Azure PowerShell připravené na vynucování:

- Pokud už používáte prostředí PowerShell, upgradujte na verzi 0.8.10 nebo novější. [Další informace](/powershell/azureps-cmdlets-docs) o tom, jak nastavit prostředí PowerShell.
- Po nastavení a konfigurace prostředí PowerShell, zkontrolujte [služby rutiny](/powershell/azure/overview).
- Další informace o tom, jak pomocí hodnoty parametrů, vstupů a výstupů v prostředí PowerShell, najdete [Začínáme](/powershell/azure/get-started-azureps) průvodce.

## <a name="set-up-a-subscription"></a>Nastavení odběru
1. Z prostředí PowerShell Přihlaste se k účtu Azure.

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Connect-AzureRmAccount #-Credential $Cred
2. Načtení seznamu předplatné s ID předplatného. Poznamenejte si ID předplatného, ve kterém chcete vytvořit trezor služeb zotavení. 

        Get-AzureRmSubscription
3. Nastavte předplatné trezoru.

        Set-AzureRmContext –SubscriptionID <subscriptionId>

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení
1. Pokud nemáte, vytvořte skupinu prostředků Azure Resource Manager.

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location
2. Vytvořte nový trezor služeb zotavení. Uložte tento trezor objekt v proměnné pro pozdější použití. 

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location
   
    Po vytvoření pomocí rutiny Get-AzureRMRecoveryServicesVault můžete načíst objekt úložiště.

## <a name="set-the-vault-context"></a>Nastavit kontext trezoru
1. Načtěte existující trezor.

       $vault = Get-AzureRmRecoveryServicesVault -Name #vaultname
2. Nastavte kontext úložiště.

       Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="install-the-site-recovery-provider"></a>Instalace zprostředkovatele služby Site Recovery
1. Na počítači nástroje Virtual Machine Manager vytvořte adresář tak, že spustíte následující příkaz:

       New-Item c:\ASR -type directory
2. Extrahujte soubory pomocí zprostředkovatele stažený instalační soubor.

       pushd C:\ASR\
       .\AzureSiteRecoveryProvider.exe /x:. /q
3. Nainstalujte zprostředkovatele a počkejte na dokončení instalace.

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

## <a name="create-and-associate-a-replication-policy"></a>Vytvořit a přidružit zásady replikace
1. Vytvořte zásadu replikace, v takovém případě pro 2012 R2 Hyper-V, následujícím způsobem:

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $RepProvider = HyperVReplica2012R2
        $Recoverypoints = 24                    #specify the number of hours to retain recovery pints
        $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
        $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
        $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
        $InitialRepMethod = "Online" #options are "Online" or "Offline"

        $policyresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod

    > [!NOTE]
    > Cloudové nástroje Virtual Machine Manager může obsahovat hostitelů Hyper-V s různými verzemi systému Windows Server, ale zásady replikace je pro konkrétní verzi operačního systému. Pokud máte různých hostitelích, které jsou spuštěné v různých operačních systémech, vytvořte samostatné replikace zásady u každého systému. Například pokud máte pět hostitelů, které se spuštěným operačním systémem Windows Server 2012 a tři hostitele se systémem Windows Server 2012 R2, vytvořte dvě zásady replikace. Můžete vytvořit pro každý typ operačního systému.

2. Načíst primární ochranu kontejneru (primární cloud nástroje Virtual Machine Manager) a obnovení kontejneru ochrany (obnovení cloudu nástroje Virtual Machine Manager).

       $PrimaryCloud = "testprimarycloud"
       $primaryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

       $RecoveryCloud = "testrecoverycloud"
       $recoveryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
3. Načtěte zásady replikace, kterou jste vytvořili pomocí popisný název.

       $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname
4. Přidružení kontejneru ochrany (cloud nástroje Virtual Machine Manager) začněte zásady replikace.

       $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
5. Počkejte na dokončení úlohy přidružení zásad. Pokud chcete zkontrolovat, pokud je úloha dokončena, použijte následující fragment kódu prostředí PowerShell:

       $job = Get-AzureRmSiteRecoveryJob -Job $associationJob

       if($job -eq $null -or $job.StateDescription -ne "Completed")
       {
         $isJobLeftForProcessing = $true;
       }

6. Po dokončení úlohy zpracování, spusťte následující příkaz:

       if($isJobLeftForProcessing)
       {
         Start-Sleep -Seconds 60
       }
       }While($isJobLeftForProcessing)

Pokud chcete zkontrolovat na dokončení operace, postupujte podle kroků v [sledovat činnost](#monitor-activity).

##  <a name="configure-network-mapping"></a>Konfigurace mapování sítě
1. Použijte tento příkaz k načtení servery pro aktuální trezoru. Příkaz ukládá v proměnné pole $Servers servery Site Recovery.

        $Servers = Get-AzureRmSiteRecoveryServer
2. Tento příkaz k načtení sítě pro server Virtual Machine Manager zdrojový a cílový server nástroje Virtual Machine Manager spusťte.

        $PrimaryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

    > [!NOTE]
    > Zdrojový server nástroje Virtual Machine Manager, může se jednat o první nebo druhé v poli serveru. Zkontrolujte název serveru nástroje Virtual Machine Manager a správně načíst sítí.


3. Tato rutina vytvoří mapování mezi primární síť a síti pro obnovení. Určuje primární síť jako první prvek $PrimaryNetworks. Jako první prvek $RecoveryNetworks určuje síti pro obnovení.

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]


## <a name="enable-protection-for-vms"></a>Povolit ochranu pro virtuální počítače
Po serverů, cloudů a sítí jsou správně nakonfigurovaný, povolte ochranu pro virtuální počítače v cloudu.

1. Pokud chcete povolit ochranu, spusťte následující příkaz pro načtení kontejner ochrany:

          $PrimaryProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
2. Entita ochrany (VM), získáte takto:

           $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
3. Povolení replikace pro virtuální počítač.

          $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy

## <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání

Pokud chcete nasazení otestovat, spusťte testovací převzetí služeb při selhání pro jeden virtuální počítač. Také můžete vytvořit plán obnovení, který obsahuje víc virtuálních počítačů a spustit testovací převzetí služeb pro plán. Testovací převzetí služeb při selhání simuluje váš mechanismus převzetí služeb při selhání a zotavení v izolované síti.

1. Načtěte virtuální počítač, do kterého virtuální počítače budou převzetí služeb při selhání.

       $Servers = Get-AzureRmSiteRecoveryServer
       $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

2. Proveďte testovací převzetí služeb.

   Pro jeden virtuální počítač:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1]
    
   Pro plán obnovení:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1]

Pokud chcete zkontrolovat na dokončení operace, postupujte podle kroků v [sledovat činnost](#monitor-activity).

## <a name="run-planned-and-unplanned-failovers"></a>Spusťte plánované a neplánované převzetí služeb při selhání

1. Proveďte plánované převzetí služeb při selhání.

   Pro jeden virtuální počítač:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   Pro plán obnovení:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan

2. Proveďte neplánované převzetí služeb při selhání.

   Pro jeden virtuální počítač:
        
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   Pro plán obnovení:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

## <a name="monitor-activity"></a>Sledování aktivity
Použijte následující příkazy monitorování převzetí služeb při selhání. Počkejte na dokončení mezi úlohy zpracování.

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



## <a name="next-steps"></a>Další postup

[Další informace](/powershell/module/azurerm.recoveryservices.backup/#recovery) o Site Recovery pomocí rutin prostředí PowerShell Resource Manager.
