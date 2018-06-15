---
title: Replikace virtuálních počítačů technologie Hyper-V přes PowerShell a Azure Resource Manager | Microsoft Docs
description: Automatizovat replikaci virtuálních počítačů Hyper-V do Azure s Azure Site Recovery pomocí Powershellu a Azure Resource Manager.
services: site-recovery
author: bsiva
manager: abhiag
ms.service: site-recovery
ms.topic: article
ms.date: 03/08/2018
ms.author: bsiva
ms.openlocfilehash: 18ed9566cd265ef851f914a59e10f6973bdc0d86
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
ms.locfileid: "31596700"
---
# <a name="set-up-disaster-recovery-to-azure-for-hyper-v-vms-using-powershell-and-azure-resource-manager"></a>Nastavení zotavení po havárii do Azure pro virtuální počítače Hyper-V pomocí prostředí PowerShell a Azure Resource Manager

[Azure Site Recovery](site-recovery-overview.md) přispívá k kontinuity podnikových procesů a strategie po havárii (BCDR) tím, že orchestruje replikaci, převzetí služeb při selhání a obnovení virtuálních počítačích Azure (VM) a místní virtuální počítače a fyzické servery.

Tento článek popisuje, jak pomocí prostředí Windows PowerShell, společně s Azure Resource Manager, k replikaci virtuálních počítačů Hyper-V do Azure. Příklad používané v tomto článku se dozvíte, jak replikovat jeden virtuální počítač spuštěn v hostiteli technologie Hyper-V do Azure.

## <a name="azure-powershell"></a>Azure PowerShell

Prostředí Azure PowerShell obsahuje rutiny pro správu Azure pomocí prostředí Windows PowerShell. Rutiny prostředí PowerShell obnovení lokality pomocí prostředí Azure PowerShell pro Azure Resource Manager, můžete chránit a obnovit vaše servery v Azure.

Nemusíte být odborné prostředí PowerShell použít v tomto článku, ale musíte pochopit základní koncepty, jako jsou moduly, rutiny a relace. Čtení [Začínáme pomocí prostředí Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx), a [použití Azure Powershellu s Azure Resource Manager](../powershell-azure-resource-manager.md).

> [!NOTE]
> Microsoft partneři v programu Cloud Solution Provider (CSP) můžete konfigurovat a spravovat ochranu systému serverů zákazníka předplatným jejich odpovídajících CSP (odběry klienta).
>
>

## <a name="before-you-start"></a>Než začnete
Ujistěte se, že máte zavedenou tyto požadavky:

* A [Microsoft Azure](https://azure.microsoft.com/) účtu. Můžete začít s [bezplatnou zkušební verzí](https://azure.microsoft.com/pricing/free-trial/). Kromě toho si můžete přečíst o [cenách Azure Site Recovery Manageru](https://azure.microsoft.com/pricing/details/site-recovery/).
* Azure PowerShell 1.0. Informace o této verzi a k jeho instalaci najdete v tématu [Azure PowerShell 1.0.](https://azure.microsoft.com/)
* [AzureRM.SiteRecovery](https://www.powershellgallery.com/packages/AzureRM.SiteRecovery/) a [AzureRM.RecoveryServices](https://www.powershellgallery.com/packages/AzureRM.RecoveryServices/) moduly. Můžete získat nejnovější verze tyto moduly z [Galerie prostředí PowerShell](https://www.powershellgallery.com/)

Kromě toho konkrétní příklad popsané v tomto článku má následující požadavky:

* Hostitele Hyper-V se systémem Windows Server 2012 R2 nebo Microsoft Hyper-V Server 2012 R2, který obsahuje jeden nebo více virtuálních počítačů. Servery Hyper-V by měl být připojen k Internetu, buď přímo nebo prostřednictvím proxy serveru.
* Virtuální počítače, které chcete replikovat musí být v souladu s [tyto požadavky](hyper-v-azure-support-matrix.md#replicated-vms).

## <a name="step-1-sign-in-to-your-azure-account"></a>Krok 1: Přihlaste se k účtu Azure

1. Otevřete konzolu prostředí PowerShell a spusťte tento příkaz k přihlášení k účtu Azure. Rutina otevře na webové stránce vás vyzve k zadání přihlašovacích údajů účtu: **Connect-AzureRmAccount**.
    - Alternativně můžete použít přihlašovací údaje účtu jako parametr v **Connect-AzureRmAccount** rutiny, pomocí **-přihlašovacích údajů** parametr.
    - Pokud jste poskytovatel CSP partnera práce jménem klienta, zadejte zákazníka jako klient, pomocí jejich název primární domény tenantID nebo klienta. Příklad: **Connect-AzureRmAccount-klienta "fabrikam.com"**
2. Přidružte odběr, který chcete použít s účet, protože účet může mít několik odběrů:

    `Select-AzureRmSubscription -SubscriptionName $SubscriptionName`

3. Ověřte, že vaše předplatné je registrovaný k použití Azure zprostředkovatele služeb zotavení a obnovení lokality pomocí těchto příkazů:

    `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices` `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`

4. Ověřte, že ve výstupu příkazu **RegistrationState** je nastaven na **registrovaná**, můžete přejít ke kroku 2. Pokud ne, byste měli zaregistrovat chybějící zprostředkovatele v rámci vašeho předplatného, tak, že spustíte tyto příkazy:

    `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SiteRecovery` `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices`

5. Ověřte, že registrované zprostředkovatele úspěšně, použijte následující příkazy:

    `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices` `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`.

## <a name="step-2-set-up-the-vault"></a>Krok 2: Nastavení trezoru

1. Vytvořte skupinu prostředků Azure Resource Manager, ve kterém k vytvoření trezoru, nebo použijte existující skupinu prostředků. Vytvořte novou skupinu prostředků takto. Proměnná $ResourceGroupName název skupiny prostředků, kterou chcete vytvořit, a proměnnou $Geo obsahuje oblast Azure, ve kterém chcete vytvořit skupinu prostředků (například "Brazílie – jih").

    `New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Geo` 

2. K získání seznamu skupin prostředků v rámci vašeho předplatného, spusťte **Get-AzureRmResourceGroup** rutiny.
2. Vytvořte nový trezor služeb zotavení Azure následujícím způsobem:

        $vault = New-AzureRmRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>

    Můžete načíst seznam trezorů existující s **Get-AzureRmRecoveryServicesVault** rutiny.


## <a name="step-3-set-the-recovery-services-vault-context"></a>Krok 3: Nastavte kontext trezoru služeb zotavení

Kontext úložiště nastavte takto:

`Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault`

## <a name="step-4-create-a-hyper-v-site"></a>Krok 4: Vytvoření web Hyper-V

1. Vytvoří nový web s technologií Hyper-V následujícím způsobem:

        $sitename = "MySite"                #Specify site friendly name
        New-AzureRmSiteRecoverySite -Name $sitename

2. Tato rutina se spustí úloha Site Recovery pro vytvoření webu a vrátí objekt úlohy Site Recovery. Počkejte na dokončení a ověřte, zda úlohy úloha byla úspěšně dokončena.
3. Použití **rutiny Get-AzureRmSiteRecoveryJob**, k načtení objektu úlohy a zkontrolujte aktuální stav úlohy.
4. Vygenerování a stažení registrační klíč pro lokalitu, následujícím způsobem:

    ```
    $SiteIdentifier = Get-AzureRmSiteRecoverySite -Name $sitename | Select -ExpandProperty SiteIdentifier
        Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename -Path $Path
    ```

5. Zkopírujte stažený klíč na hostitele Hyper-V. Je nutné klíč k registraci hostitele technologie Hyper-V do lokality.

## <a name="step-5-install-the-provider-and-agent"></a>Krok 5: Instalace zprostředkovatel a agent

1. Stažení instalačního programu pro nejnovější verzi zprostředkovatele ze [Microsoft](https://aka.ms/downloaddra).
2. Spusťte instalační program na hostiteli theHyper-V.
3. Na konci instalace pokračujte krokem registrace.
4. Po zobrazení výzvy zadejte stažené klíč a dokončit registraci hostitele technologie Hyper-V.
5. Ověří, zda hostitel Hyper-V následujícím způsobem registrován do lokality:

        $server =  Get-AzureRmSiteRecoveryServer -FriendlyName $server-friendlyname

## <a name="step-6-create-a-replication-policy"></a>Krok 6: Vytvoření zásad replikace

Než začnete, Upozorňujeme, že zadaný účet úložiště by měl být ve stejné oblasti Azure jako trezor a musí mít povolenou geografickou replikací.

1. Vytvořte zásadu replikace následujícím způsobem:

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                    #specify the number of recovery points
        $storageaccountID = Get-AzureRmStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id

        $PolicyResult = New-AzureRmSiteRecoveryPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

2. Zkontrolujte vrácený úlohu, která zajistí, že úspěšné vytvoření zásad replikace.

3. Načtení kontejneru ochrany, který odpovídá k lokalitě, následujícím způsobem:

        $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer
3. Kontejner ochrany přidružte k zásadě replikace, následujícím způsobem:

     $Policy = get-AzureRmSiteRecoveryPolicy - FriendlyName $PolicyName $associationJob = Start AzureRmSiteRecoveryPolicyAssociationJob-zásady $Policy - PrimaryProtectionContainer $protectionContainer

4. Počkejte na úlohu přidružení úspěšně dokončit.

## <a name="step-7-enable-vm-protection"></a>Krok 7: Povolit ochranu virtuálního počítače

1. Načtení entity ochrany, která odpovídá virtuálních počítačů, které chcete chránit, následujícím způsobem:

        $VMFriendlyName = "Fabrikam-app"                    #Name of the VM
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
2. Ochrana virtuálních počítačů. Pokud chráníte virtuální počítač má více než jeden disk připojený k němu, zadejte disku operačního systému pomocí *OSDiskName* parametr.

        $Ostype = "Windows"                                 # "Windows" or "Linux"
        $DRjob = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity -Policy $Policy -Protection Enable -RecoveryAzureStorageAccountId $storageaccountID  -OS $OStype -OSDiskName $protectionEntity.Disks[0].Name

3. Počkejte, než pro virtuální počítače dosáhne chráněném stavu po počáteční replikaci. To může nějakou dobu trvat, v závislosti na faktorech, jako je množství dat, které se budou replikovat a dostupnou šířku pásma nadřazeného do Azure. Pokud je chráněný stav v místě, stav úlohy a StateDescription jsou aktualizované následujícím způsobem: 

        PS C:\> $DRjob = Get-AzureRmSiteRecoveryJob -Job $DRjob

        PS C:\> $DRjob | Select-Object -ExpandProperty State
        Succeeded

        PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
        Completed
4. Aktualizovat vlastnosti obnovení (například velikost role virtuálního počítače) a síť Azure, ke kterému chcete připojit síťový adaptér virtuálního počítače po převzetí služeb při selhání.

        PS C:\> $nw1 = Get-AzureRmVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

        PS C:\> $VMFriendlyName = "Fabrikam-App"

        PS C:\> $VM = Get-AzureRmSiteRecoveryVM -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

        PS C:\> $UpdateJob = Set-AzureRmSiteRecoveryVM -VirtualMachine $VM -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

        PS C:\> $UpdateJob = Get-AzureRmSiteRecoveryJob -Job $UpdateJob

        PS C:\> $UpdateJob

        Name             : b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        ID               : /Subscriptions/a731825f-4bf2-4f81-a611-c331b272206e/resourceGroups/MyRG/providers/Microsoft.RecoveryServices/vault
                           s/MyVault/replicationJobs/b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        Type             : Microsoft.RecoveryServices/vaults/replicationJobs
        JobType          : UpdateVmProperties
        DisplayName      : Update the virtual machine
        ClientRequestId  : 805a22a3-be86-441c-9da8-f32685673112-2015-12-10 17:55:51Z-P
        State            : Succeeded
        StateDescription : Completed
        StartTime        : 10-12-2015 17:55:53 +00:00
        EndTime          : 10-12-2015 17:55:54 +00:00
        TargetObjectId   : 289682c6-c5e6-42dc-a1d2-5f9621f78ae6
        TargetObjectType : ProtectionEntity
        TargetObjectName : Fabrikam-App
        AllowedActions   : {Restart}
        Tasks            : {UpdateVmPropertiesTask}
        Errors           : {}



## <a name="step-8-run-a-test-failover"></a>Krok 8: Spustit testovací převzetí služeb
1. Spusťte testovací převzetí služeb takto:

        $nw = Get-AzureRmVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMFriendlyName -ProtectionContainer $protectionContainer

        $TFjob = Start-AzureRmSiteRecoveryTestFailoverJob -ProtectionEntity $protectionEntity -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
2. Ověřte, že testovací virtuální počítač je vytvořen v Azure. Testovací převzetí služeb při selhání úlohy je pozastaven po vytvoření testovacího virtuálního počítače v Azure.
3. Vyčištění a dokončete testovací převzetí služeb, spusťte příkaz:

        $TFjob = Resume-AzureRmSiteRecoveryJob -Job $TFjob

## <a name="next-steps"></a>Další postup
[Další informace](https://msdn.microsoft.com/library/azure/mt637930.aspx) o Azure Site Recovery pomocí rutin prostředí PowerShell Azure Resource Manager.
