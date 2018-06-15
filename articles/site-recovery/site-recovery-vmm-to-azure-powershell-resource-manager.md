---
title: Replikace virtuálních počítačů technologie Hyper-V v cloudech VMM pomocí Azure Site Recovery a prostředí PowerShell (Resource Manager) | Microsoft Docs
description: Replikace virtuálních počítačů technologie Hyper-V v cloudech VMM pomocí Azure Site Recovery a prostředí PowerShell
services: site-recovery
documentationcenter: ''
author: Rajani-Janaki-Ram
manager: rochakm
editor: raynew
ms.assetid: 6ac509ad-5024-43d8-b621-d8fec019b9a9
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: rajanaki
ms.openlocfilehash: 67d9164fcf8fb3731c4e3a9d30ac5c38da5908c7
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2018
ms.locfileid: "29677459"
---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-powershell-and-azure-resource-manager"></a>Replikace virtuálních počítačů technologie Hyper-V v cloudech VMM do Azure pomocí prostředí PowerShell a Azure Resource Manager
> [!div class="op_single_selector"]
> * [Azure Portal](site-recovery-vmm-to-azure.md)
> * [PowerShell – Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
> * [PowerShell – Classic](site-recovery-deploy-with-powershell.md)
>
>

## <a name="overview"></a>Přehled
Azure Site Recovery přispívá ke strategii obchodní kontinuitu a po havárii obnovení (BCDR) tím, že orchestruje replikaci, převzetí služeb při selhání a obnovení virtuálních počítačů v různých scénářích nasazení. Úplný seznam nasazení scénářů najdete v článku [přehled Azure Site Recovery](site-recovery-overview.md).

Tento článek ukazuje, jak pomocí prostředí PowerShell pro automatizaci běžných úkolů, které je třeba provést při nastavení Azure Site Recovery replikovat virtuální počítače Hyper-V v cloudech System Center VMM do úložiště Azure.

Článek obsahuje předpoklady pro scénář a ukazuje

* Jak nastavit trezor služeb zotavení
* Nainstalujte zprostředkovatele Azure Site Recovery na zdrojovém serveru VMM
* Zaregistrujte server v trezoru, přidat účet úložiště Azure
* Nainstalujte agenta služeb zotavení Azure na hostitelské servery technologie Hyper-V
* Konfigurovat nastavení ochrany pro cloudy VMM, které se použijí na všechny chráněné virtuální počítače
* Povolení ochrany pro tyto virtuální počítače.
* Testovací převzetí služeb při selhání a ujistěte se, zda že vše funguje podle očekávání.

Pokud narazíte na problémy nastavení tento scénář, zveřejněte svoje otázky na [fóru Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

> [!NOTE]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se zabývá pomocí modelu nasazení Resource Manager.
>
>

## <a name="before-you-start"></a>Než začnete
Ujistěte se, že máte zavedenou tyto požadavky:

### <a name="azure-prerequisites"></a>Požadavky Azure
* Budete potřebovat účet [Microsoft Azure](https://azure.microsoft.com/). Pokud nemáte, začínat [bezplatný účet](https://azure.microsoft.com/free). Kromě toho si můžete přečíst o [cenách Azure Site Recovery Manageru](https://azure.microsoft.com/pricing/details/site-recovery/).
* Pokud se pokoušíte se replikace pro určitý scénář předplatného poskytovatele CSP, budete potřebovat předplatné CSP. Další informace o programu CSP v [postup registrace do programu CSP](https://msdn.microsoft.com/library/partnercenter/mt156995.aspx).
* Budete potřebovat účet úložiště (Resource Manager) Azure v2 k ukládání dat replikovaných do Azure. Účet musí povolenou geografickou replikací. Musí být ve stejné oblasti jako služba Azure Site Recovery a být přidruženy ke stejnému předplatnému nebo předplatného poskytovatele CSP. Další informace o nastavení služby Azure storage, najdete v článku [Úvod do Microsoft Azure Storage](../storage/common/storage-introduction.md) pro referenci.
* Budete potřebovat, abyste měli jistotu, že virtuální počítače, které chcete chránit vyhovují [požadavky virtuálního počítače Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

> [!NOTE]
> V současné době pouze úrovně operace virtuálního počítače je možné pomocí prostředí Powershell. Podpora pro úrovně operace plánu obnovení, bude brzy se.  Zatím jste omezeni na provádění selhání Center pouze na rozlišením "chráněné virtuální počítač" a ne na úrovni plán obnovení.
>
>

### <a name="vmm-prerequisites"></a>Požadavky VMM
* Budete potřebovat VMM server běžící na System Center 2012 R2.
* Žádném serveru VMM obsahující virtuálních počítačů, které chcete chránit, musí být spuštěna zprostředkovatele Azure Site Recovery. To je nainstalován během nasazování Azure Site Recovery.
* Budete potřebovat alespoň jeden cloud na serveru VMM, který chcete chránit. Cloud by měl obsahovat:
  * Minimálně jednu skupinu hostitelů VMM.
  * Minimálně jeden hostitelský server nebo cluster Hyper-V v každé skupině hostitelů.
  * Jeden nebo více virtuálních počítačů na zdrojovém technologie Hyper-V serveru.
* Další informace o nastavení cloudů VMM:
  * Další informace o privátních cloudů VMM ve [co je nového v privátním cloudu s nástrojem System Center 2012 R2 VMM](http://go.microsoft.com/fwlink/?LinkId=324952) a v [VMM 2012 a cloudy](http://go.microsoft.com/fwlink/?LinkId=324956).
  * Další informace o [konfigurace VMM cloudových prostředků infrastruktury](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
  * Po vaší cloudové infrastruktury prvky jsou zavedené Další informace o vytváření privátních cloudů ve [vytvoření privátního cloudu v nástroji VMM](http://go.microsoft.com/fwlink/p/?LinkId=324953) a v [návod: vytvoření privátních cloudů pomocí System Center 2012 SP1 VMM](http://go.microsoft.com/fwlink/p/?LinkId=324954).

### <a name="hyper-v-prerequisites"></a>Požadavky technologie Hyper-V
* Servery hostitele technologie Hyper-V musí používat alespoň **systému Windows Server 2012** s rolí Hyper-V nebo **Microsoft Hyper-V Server 2012** a mít nainstalované nejnovější aktualizace.
* Pokud používáte technologii Hyper-V v clusteru, je důležité vědět, že zprostředkovatel clusteru se nevytvoří automaticky, pokud máte clustery založené na statických IP adresách. Budete ho muset nakonfigurovat ručně. Za
* Pokyny naleznete v části [postup konfigurace zprostředkovatele replik technologie Hyper-V](http://blogs.technet.com/b/haroldwong/archive/2013/03/27/server-virtualization-series-hyper-v-replica-broker-explained-part-15-of-20-by-yung-chou.aspx).
* Libovolný server hostitele technologie Hyper-V nebo clusteru, pro který chcete spravovat ochranu musí být součástí cloudu VMM.

### <a name="network-mapping-prerequisites"></a>Požadavky na mapování sítě
Při ochraně virtuálních počítačů v Azure mapování mapuje sítě, virtuální počítač sítě na zdrojovém serveru VMM a cílovými sítěmi Azure, aby bylo umožněno následující:

* Všechny počítače, které převzetí služeb při selhání ve stejné síti může připojit k sobě navzájem, bez ohledu na plánu obnovení jsou v.
* Pokud je v cílové síti Azure nastavená brána sítě, virtuální počítače se budou moct připojit k jiným místním virtuálním počítačům.
* Pokud nenakonfigurujete mapování sítě, bude moct po převzetí služeb při selhání do Azure připojit k sobě navzájem pouze virtuální počítače, které převzetí služeb při selhání v rámci stejného plánu obnovení.

Pokud chcete nasadit mapování sítě, budete potřebovat následující:

* Virtuální počítače, který chcete chránit na zdrojovém serveru VMM, musí být připojené k síti virtuálních počítačů. Tato síť musí být propojená na logickou síť, která je přidružená ke cloudu.
* Síť Azure ke kterému může po převzetí služeb při selhání připojit replikované virtuální počítače. Tuto síť vyberete v okamžiku převzetí služeb při selhání. Síť musí být ve stejné oblasti jako vaše předplatné Azure Site Recovery.

Další informace o mapování sítě v

* [Postup konfigurace logické sítě v nástroji VMM](http://go.microsoft.com/fwlink/p/?LinkId=386307)
* [Postup konfigurace sítě virtuálních počítačů a bran v nástroji VMM](http://go.microsoft.com/fwlink/p/?LinkId=386308)
* [Postup konfigurace a monitorování virtuálních sítí v Azure](https://azure.microsoft.com/documentation/services/virtual-network/)

### <a name="powershell-prerequisites"></a>Požadavky prostředí PowerShell
Ujistěte se, že máte Azure PowerShell připravené na vynucování. Pokud už používáte prostředí PowerShell, budete muset upgradovat na verzi 0.8.10 nebo novější. Informace o nastavení prostředí PowerShell najdete v tématu [Průvodce instalace a konfigurace prostředí Azure PowerShell](/powershell/azureps-cmdlets-docs). Po nastavení a nakonfigurovat prostředí PowerShell, můžete zobrazit všechny dostupné rutiny služby [zde](/powershell/azure/overview).

Další informace o tipy, které mohou pomoci pomocí rutin, jako je například jak hodnoty parametrů, vstupy a výstupy jsou obvykle zpracovávány v prostředí Azure PowerShell najdete v článku [Průvodce Začínáme s Azure rutiny](/powershell/azure/get-started-azureps).

## <a name="step-1-set-the-subscription"></a>Krok 1: Nastavte předplatné
1. Z prostředí Azure powershell, přihlášení k účtu Azure: pomocí následujících rutin

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Login-AzureRmAccount #-Credential $Cred
2. Získejte seznam předplatných. SubscriptionIDs to taky zobrazí seznam všech odběrů. Poznamenejte si ID předplatného předplatné, ve kterém chcete vytvořit trezor služeb zotavení

        Get-AzureRmSubscription
3. Nastavte předplatné, ve kterém má být vytvořen třeba uvést ID předplatného trezoru služeb zotavení

        Set-AzureRmContext –SubscriptionID <subscriptionId>

## <a name="step-2-create-a-recovery-services-vault"></a>Krok 2 – Vytvoření trezoru Služeb zotavení.
1. Vytvořte skupinu prostředků ve službě Správce prostředků Azure, pokud již nemáte

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location
2. Vytvořit nový trezor služeb zotavení a uložte vytvořený objekt trezoru automatické obnovení systému v proměnné (použijí později). Můžete také načíst pomocí rutiny Get-AzureRMRecoveryServicesVault vytvoření post objektu automatické obnovení systému trezoru:-

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location

## <a name="step-3-set-the-recovery-services-vault-context"></a>Krok 3: Nastavte kontext trezoru služeb zotavení

Nastavit kontext trezoru spuštěním následující příkaz.

       Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="step-4-install-the-azure-site-recovery-provider"></a>Krok 4: Instalace nástroje Azure Site Recovery Provider
1. Na počítači VMM vytvořte adresář tak, že spustíte následující příkaz:

       New-Item c:\ASR -type directory
2. Extrahujte soubory pomocí stažené poskytovatele tak, že spustíte následující příkaz

       pushd C:\ASR\
       .\AzureSiteRecoveryProvider.exe /x:. /q
3. Nainstalujte poskytovatele pomocí následujících příkazů:

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

   Počkejte na dokončení instalace.
4. Zaregistrujte server v trezoru, pomocí následujícího příkazu:

       $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
       pushd $BinPath
       $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

## <a name="step-5-create-an-azure-storage-account"></a>Krok 5: Vytvoření účtu úložiště Azure

Pokud nemáte účet úložiště Azure, vytvořte účet povolenou geografickou replikací ve stejném geograficky redundantním jako trezor spuštěním následujícího příkazu:

        $StorageAccountName = "teststorageacc1"    #StorageAccountname
        $StorageAccountGeo  = "Southeast Asia"     
        $ResourceGroupName =  “myRG”             #ResourceGroupName
        $RecoveryStorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Type “StandardGRS” -Location $StorageAccountGeo

Všimněte si, že účet úložiště musí být ve stejné oblasti jako služba Azure Site Recovery a být přidružený ke stejnému předplatnému.

## <a name="step-6-install-the-azure-recovery-services-agent"></a>Krok 6: Instalace agenta služeb zotavení Azure
1. Stáhnout agenta služeb zotavení Azure na [http://aka.ms/latestmarsagent](http://aka.ms/latestmarsagent) a nainstalujte ji na každém serveru hostitele technologie Hyper-V v cloudech VMM, které chcete chránit.
2. Na všech hostitelích VMM, spusťte následující příkaz:

       marsagentinstaller.exe /q /nu

## <a name="step-7-configure-cloud-protection-settings"></a>Krok 7: Konfigurace cloudu nastavení ochrany
1. Vytvořte zásadu replikace do Azure tak, že spustíte následující příkaz:

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                    #specify the number of recovery points

        $policryresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider HyperVReplicaAzure -ReplicationFrequencyInSeconds $replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId "/subscriptions/q1345667/resourceGroups/test/providers/Microsoft.Storage/storageAccounts/teststorageacc1"

1. Kontejner ochrany získáte spuštěním následujících příkazů:

       $PrimaryCloud = "testcloud"
       $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  
2. Získáte podrobnosti zásady pomocí úlohy, která byla vytvořena a zmínit zásad popisný název proměnné:

       $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname
3. Přidružení kontejner ochrany začněte zásady replikace:

       $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $protectionContainer  
4. Po dokončení úlohy spusťte následující příkaz:

       $job = Get-AzureRmSiteRecoveryJob -Job $associationJob

       if($job -eq $null -or $job.StateDescription -ne "Completed")
       {
         $isJobLeftForProcessing = $true;
       }
5. Po dokončení zpracování úlohy spusťte následující příkaz:

       if($isJobLeftForProcessing)
       {
         Start-Sleep -Seconds 60
       }
       }While($isJobLeftForProcessing)

Pokud chcete zkontrolovat na dokončení operace, postupujte podle kroků v [monitorování aktivity](#monitor).

## <a name="step-8-configure-network-mapping"></a>Krok 8: Nakonfigurování mapování sítě
Před zahájením mapování sítě ověřte, jestli jsou virtuální počítače na zdrojovém serveru VMM připojené k síti virtuálních počítačů. Kromě toho můžete vytvořte jeden nebo více virtuálních sítí Azure.

Další informace o tom, jak vytvořit virtuální síť pomocí Azure Resource Manageru a prostředí PowerShell v [vytvořit virtuální síť s připojením VPN typu site-to-site pomocí Azure Resource Manageru a prostředí PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

Všimněte si, že více sítí virtuálních počítačů lze mapovat na jednu síť Azure. Pokud má cílová síť více podsítí a jedna z těchto podsítí má stejný název jako podsíť, na kterém se nachází zdrojový virtuální počítač, pak virtuální počítač repliky se připojí k této cílové podsíti po převzetí služeb při selhání. Pokud není žádná cílová podsíť s odpovídajícím názvem, virtuální počítač se připojí k první podsíti v síti.

1. První příkaz získá servery pro aktuální trezoru Azure Site Recovery. Příkaz ukládá v proměnné pole $Servers servery Microsoft Azure Site Recovery.

        $Servers = Get-AzureRmSiteRecoveryServer
2. V druhém příkazu získá síť site recovery pro první server v poli $Servers. Příkaz ukládá v proměnné $Networks sítí.

        $Networks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]

1. V třetím příkazu je získán virtuálních sítí Azure a pak tuto hodnotu v proměnné $AzureVmNetworks.

        $AzureVmNetworks =  Get-AzureRmVirtualNetwork
2. Poslední rutina vytvoří mapování mezi primární síť a síť virtuálního počítače Azure. Rutina určuje primární síť jako první prvek $Networks. Rutina určuje jako první prvek $AzureVmNetworks síť virtuálních počítačů.

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $Networks[0] -AzureVMNetworkId $AzureVmNetworks[0]

## <a name="step-9-enable-protection-for-virtual-machines"></a>Krok 9: Povolení ochrany pro virtuální počítače
Po serverů, cloudů a sítí jsou správně nakonfigurovaný, můžete povolit ochranu pro virtuální počítače v cloudu.

 Je třeba počítat s následujícím:

* Virtuální počítače musí splňovat požadavky pro Azure. Zkontrolujte tyto v [požadavky a podpora](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) v příručce plánování.
* Povolit ochranu, operační systém a operačního systému disku vlastnosti musí být nastavené pro virtuální počítač. Vlastnost můžete nastavit při vytváření virtuálního počítače v nástroji VMM pomocí šablony virtuálního počítače. Tyto vlastnosti také můžete nastavit pro existující virtuální počítače, a to na kartách **Obecné** a **Konfigurace hardwaru** ve vlastnostech virtuálního počítače. Pokud tyto vlastnosti nenastavíte v nástroji VMM, budete je moct nakonfigurovat na portálu Azure Site Recovery.

1. Pokud chcete povolit ochranu, spusťte následující příkaz k získání kontejneru ochrany:

          $ProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $CloudName
2. Získáte entita ochrany (VM) tak, že spustíte následující příkaz:

           $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $protectionContainer
3. Povolte DR pro virtuální počítač tak, že spustíte následující příkaz:

          $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable –Force -Policy $policy -RecoveryAzureStorageAccountId  $storageID "/subscriptions/217653172865hcvkchgvd/resourceGroups/rajanirgps/providers/Microsoft.Storage/storageAccounts/teststorageacc1

## <a name="test-your-deployment"></a>Otestujte nasazení
Chcete nasazení otestovat můžete spustit test selhání pro jeden virtuální počítač, nebo vytvořit plán obnovení, který se skládá z více virtuálních počítačů a spustit test selhání pro plán. Testovací převzetí služeb při selhání simuluje váš mechanismus převzetí služeb při selhání a obnovení v izolované síti. Poznámky:

* Pokud se chcete připojit k virtuálnímu počítači v Azure pomocí vzdálené plochy po převzetí služeb při selhání, povolte připojení ke vzdálené ploše na virtuálním počítači před spuštěním testu převzetí služeb.
* Po převzetí služeb při selhání použijete veřejnou IP adresu pro připojení k virtuálnímu počítači v Azure pomocí vzdálené plochy. Pokud to chcete provést, zkontrolujte, že nemáte žádné zásady domény, které by vám bránily v připojení k virtuálnímu počítači pomocí veřejné adresy.

Pokud chcete zkontrolovat na dokončení operace, postupujte podle kroků v [monitorování aktivity](#monitor).

### <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání
- Spusťte testovací převzetí služeb tak, že spustíte následující příkaz:

       $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

       $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### <a name="run-a-planned-failover"></a>Spusťte plánované převzetí služeb při selhání
- Spusťte plánované převzetí služeb při selhání spuštěním následujícího příkazu:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### <a name="run-an-unplanned-failover"></a>Spustit neplánované převzetí služeb při selhání
- Spusťte neplánované převzetí služeb při selhání tak, že spustíte následující příkaz:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

## <a name=monitor></a> Sledování aktivity
Použijte následující příkazy k monitorování aktivit. Všimněte si, že máte čekat mezi úlohy pro zpracování ukončíte.

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
[Další informace](/powershell/module/azurerm.recoveryservices.backup/#recovery) o Azure Site Recovery pomocí rutin prostředí PowerShell Azure Resource Manager.
