---
title: Replikace virtuálních počítačů Hyper-V v cloudech Virtual Machine Manager do sekundární lokality pomocí Powershellu (Azure Resource Manageru) | Dokumentace Microsoftu
description: Popisuje, jak replikovat virtuální počítače Hyper-V v cloudech Virtual Machine Manager do sekundární lokality Virtual Machine Manager pomocí prostředí PowerShell (Resource Manager)
services: site-recovery
author: sujaytalasila
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: sutalasi
ms.openlocfilehash: 0fecc7ba48daf396c3d25969cdda5891bdf08232
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2018
ms.locfileid: "37917961"
---
# <a name="replicate-hyper-v-vms-to-a-secondary-site-by-using-powershell-resource-manager"></a>Replikace virtuálních počítačů Hyper-V do sekundární lokality pomocí prostředí PowerShell (Resource Manager)

Tento článek popisuje, jak automatizovat kroky pro replikaci virtuálních počítačů Hyper-V v cloudech System Center Virtual Machine Manager cloud Virtual Machine Manager v sekundární místní lokality pomocí [Azure Site Recovery](site-recovery-overview.md).

## <a name="prerequisites"></a>Požadavky

- Zkontrolujte [komponent a architektury scénáře](hyper-v-vmm-architecture.md).
- Zkontrolujte [požadavky na podporu](site-recovery-support-matrix-to-sec-site.md) pro všechny komponenty.
- Ujistěte se, že servery Virtual Machine Manager a hostitelů Hyper-V souladu s [požadavky na podporu](site-recovery-support-matrix-to-sec-site.md).
- Zkontrolujte, jestli virtuální počítače, které chcete replikovat v souladu s [počítač podporu replikovaných](site-recovery-support-matrix-to-sec-site.md).


## <a name="prepare-for-network-mapping"></a>Příprava mapování sítě

[Mapování sítě](hyper-v-vmm-network-mapping.md) mapování mezi místní sítí virtuálních počítačů Virtual Machine Manager v zdrojovým a cílovým cloudem. Mapování provede následující akce:

- Připojí virtuální počítače do příslušné cílové sítě virtuálních počítačů po převzetí služeb při selhání. 
- Virtuální počítače repliky optimálně umístí na hostitelské servery Hyper-V cílové. 
- Pokud nenakonfigurujete mapování sítě, virtuální počítače replik nebude připojen k síti virtuálních počítačů po převzetí služeb při selhání.

Příprava Virtual Machine Manager následujícím způsobem:

* Ujistěte se, že máte [logických sítí Virtual Machine Manager](https://docs.microsoft.com/system-center/vmm/network-logical) ve zdrojové i cílové servery Virtual Machine Manager:

    - Přidružený zdrojový cloud, ve kterém se nachází hostitelé Hyper-V by měl být logická síť na zdrojovém serveru.
    - Přidružený cílový cloud by měl být logická síť na cílovém serveru.
* Ujistěte se, že máte [sítě virtuálních počítačů](https://docs.microsoft.com/system-center/vmm/network-virtual) ve zdrojové i cílové servery Virtual Machine Manager. Sítě virtuálních počítačů musí být propojena na logickou síť v jednotlivých oblastech.
* Propojení virtuálních počítačů na hostitelích Hyper-V zdrojového ke zdrojové síti virtuálních počítačů. 

## <a name="prepare-for-powershell"></a>Příprava pro prostředí PowerShell

Ujistěte se, že máte Azure PowerShell, jste připravení začít:

- Pokud už používáte PowerShell, upgradujte na verzi 0.8.10 nebo novější. [Další informace](/powershell/azureps-cmdlets-docs) o tom, jak nastavit prostředí PowerShell.
- Po nastavení a konfigurace prostředí PowerShell, zkontrolujte [služby rutiny](/powershell/azure/overview).
- Další informace o tom, jak používat hodnoty parametrů, vstupy a výstupy v prostředí PowerShell najdete v článku [Začínáme](/powershell/azure/get-started-azureps) průvodce.

## <a name="set-up-a-subscription"></a>Nastavení předplatného
1. Z prostředí PowerShell Přihlaste se ke svému účtu Azure.

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Connect-AzureRmAccount #-Credential $Cred
2. Načte seznam vašich předplatných, s ID předplatného. Poznamenejte si ID předplatného, ve kterém chcete vytvořit trezor služby Recovery Services. 

        Get-AzureRmSubscription
3. Nastavte předplatné pro trezor.

        Set-AzureRmContext –SubscriptionID <subscriptionId>

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení
1. Pokud ho nemáte, vytvořte skupinu prostředků Azure Resource Manageru.

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location
2. Vytvořte nový trezor služby Recovery Services. Uložte objekt trezoru v proměnné pro pozdější použití. 

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location
   
    Po vytvoření pomocí rutiny Get-AzureRMRecoveryServicesVault můžete pak získat objekt trezoru.

## <a name="set-the-vault-context"></a>Nastavte kontext trezoru
1. Načte existující trezor.

       $vault = Get-AzureRmRecoveryServicesVault -Name #vaultname
2. Nastavte kontext trezoru.

       Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="install-the-site-recovery-provider"></a>Instalace zprostředkovatele služby Site Recovery
1. Na počítači Virtual Machine Manager vytvořte adresář spuštěním následujícího příkazu:

       New-Item c:\ASR -type directory
2. Extrahujte soubory s použitím instalační soubor stažený zprostředkovatele.

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
1. Vytvoření zásady replikace, v tomto případě pro Hyper-V 2012 R2, následujícím způsobem:

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
    > Virtual Machine Manager cloud obsahovat hostitele Hyper-V s různými verzemi Windows serveru, ale je zásada replikace pro konkrétní verze operačního systému. Pokud máte na různých hostitelích, které běží na různých operačních systémů, vytvořte zásady samostatné replikace u každého systému. Například pokud máte pět hostitelů, které běží na Windows Server 2012 a tři hostitele se systémem Windows Server 2012 R2 vytvoříte dvě zásady replikace. Můžete vytvořit pro každý typ operačního systému.

2. Načíst primární ochranu kontejneru (primární Virtual Machine Manager cloud) a obnovení kontejneru ochrany (obnovení Virtual Machine Manager cloud).

       $PrimaryCloud = "testprimarycloud"
       $primaryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

       $RecoveryCloud = "testrecoverycloud"
       $recoveryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
3. Načtěte zásadu replikace, kterou jste vytvořili pomocí popisný název.

       $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname
4. Začněte přidružení kontejneru ochrany (Virtual Machine Manager cloud) k zásadě replikace.

       $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
5. Počkejte na dokončení úlohy přidružení zásad. Pokud chcete zkontrolovat, pokud je úloha dokončena, použijte následující fragment kódu Powershellu:

       $job = Get-AzureRmSiteRecoveryJob -Job $associationJob

       if($job -eq $null -or $job.StateDescription -ne "Completed")
       {
         $isJobLeftForProcessing = $true;
       }

6. Jakmile se úlohy dokončí zpracování, spusťte následující příkaz:

       if($isJobLeftForProcessing)
       {
         Start-Sleep -Seconds 60
       }
       }While($isJobLeftForProcessing)

Pokud chcete zkontrolovat dokončení operace, postupujte podle kroků v [sledovat činnost](#monitor-activity).

##  <a name="configure-network-mapping"></a>Konfigurace mapování sítě
1. Tento příkaz k načtení serverů pro aktuální úložiště. Tento příkaz uloží do proměnné pole $Servers servery Site Recovery.

        $Servers = Get-AzureRmSiteRecoveryServer
2. Spusťte tento příkaz k načtení sítě pro Virtual Machine Manager na zdrojovém a cílovém serveru Virtual Machine Manager.

        $PrimaryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

    > [!NOTE]
    > Na zdrojovém serveru Virtual Machine Manager může být ta první nebo druhé pole serveru. Zkontrolovat názvy serveru Virtual Machine Manager a načíst sítě odpovídajícím způsobem.


3. Tato rutina vytvoří mapování mezi primární síť a síť pro obnovení. Určuje primární síť jako prvního prvku $PrimaryNetworks. Jako první prvek $RecoveryNetworks určuje síť pro obnovení.

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]


## <a name="enable-protection-for-vms"></a>Povolit ochranu pro virtuální počítače
Po serverů, cloudů a sítí jsou nakonfigurovány správně, povolte ochranu pro virtuální počítače v cloudu.

1. Pokud chcete povolit ochranu, spusťte následující příkaz, který načte kontejner ochrany:

          $PrimaryProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
2. Entita ochrany (VM), získáte takto:

           $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
3. Povolení replikace pro virtuální počítač.

          $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy

## <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání

K otestování nasazení, spuštění testovací převzetí služeb při selhání pro jeden virtuální počítač. Také můžete vytvořit plán obnovení, který obsahuje několik virtuálních počítačů a testovací převzetí služeb při selhání pro plán. Testovací převzetí služeb při selhání simuluje váš mechanismus převzetí služeb při selhání a zotavení v izolované síti.

1. Načte virtuální počítač, do kterého virtuální počítače se převzetí služeb při selhání.

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

Pokud chcete zkontrolovat dokončení operace, postupujte podle kroků v [sledovat činnost](#monitor-activity).

## <a name="run-planned-and-unplanned-failovers"></a>Spusťte plánované a neplánované převzetí služeb při selhání

1. Proveďte plánované převzetí služeb při selhání.

   Pro jeden virtuální počítač:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   Pro plán obnovení:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan

2. Provedení neplánovaného převzetí služeb při selhání.

   Pro jeden virtuální počítač:
        
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   Pro plán obnovení:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

## <a name="monitor-activity"></a>Monitorování aktivity
Monitorovat aktivitu převzetí služeb při selhání, použijte následující příkazy. Počkejte na dokončení mezi úloh zpracování.

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

[Další informace](/powershell/module/azurerm.recoveryservices.backup/#recovery) o službě Site Recovery pomocí rutin Powershellu pro Resource Manager.
