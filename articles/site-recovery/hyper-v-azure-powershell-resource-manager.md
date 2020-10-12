---
title: Zotavení po havárii virtuálního počítače Hyper-V pomocí Azure Site Recovery a PowerShellu
description: Automatizujte zotavení virtuálních počítačů Hyper-V po havárii do Azure pomocí služby Azure Site Recovery pomocí PowerShellu a Azure Resource Manager.
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 01/10/2020
ms.author: sutalasi
ms.openlocfilehash: a4140a0b22f7ca8164d50cf60fe57c861f826eb4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86132506"
---
# <a name="set-up-disaster-recovery-to-azure-for-hyper-v-vms-using-powershell-and-azure-resource-manager"></a>Nastavení zotavení po havárii do Azure pro virtuální počítače Hyper-V s využitím PowerShellu a Azure Resource Manageru

[Azure Site Recovery](site-recovery-overview.md) přispívá ke strategii provozní kontinuity a zotavení po havárii (BCDR) tím, že orchestruje replikaci, převzetí služeb při selhání a obnovení virtuálních počítačů Azure a místní virtuální počítače a fyzické servery.

Tento článek popisuje, jak pomocí prostředí Windows PowerShell spolu s Azure Resource Manager replikovat virtuální počítače Hyper-V do Azure. V příkladu použitém v tomto článku se dozvíte, jak replikovat jeden virtuální počítač běžící na hostiteli Hyper-V do Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell poskytuje rutiny pro správu Azure pomocí Windows PowerShellu. Site Recovery rutiny prostředí PowerShell, které jsou k dispozici v Azure PowerShell pro Azure Resource Manager, vám pomůžou chránit a obnovovat servery v Azure.

K použití tohoto článku nemusíte být odborník na PowerShell, ale musíte pochopit základní koncepty, jako jsou například moduly, rutiny a relace. Další informace najdete v [dokumentaci k PowerShellu](/powershell) a [používání Azure PowerShell s Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md).

> [!NOTE]
> Partneři Microsoftu v programu Cloud Solution Provider (CSP) můžou nakonfigurovat a spravovat ochranu zákaznických serverů na příslušné předplatné CSP (předplatná klientů).

## <a name="before-you-start"></a>Než začnete

Ujistěte se, že máte zavedené tyto požadavky:

- Účet [Microsoft Azure](https://azure.microsoft.com/) . Můžete začít s [bezplatnou zkušební verzí](https://azure.microsoft.com/pricing/free-trial/). Navíc si můžete přečíst o [cenách Azure Site Recovery Manageru](https://azure.microsoft.com/pricing/details/site-recovery/).
- Azure Powershell Informace o této verzi a o tom, jak ji nainstalovat, najdete v tématu [install Azure PowerShell](/powershell/azure/install-az-ps).

Kromě toho konkrétní příklad, který je popsaný v tomto článku, má následující požadavky:

- Hostitel Hyper-V se systémem Windows Server 2012 R2 nebo Microsoft Hyper-V Server 2012 R2 obsahující jeden nebo více virtuálních počítačů. Servery Hyper-V by měly být připojené k Internetu, a to buď přímo, nebo prostřednictvím proxy serveru.
- Virtuální počítače, které chcete replikovat, by měly splňovat [tyto požadavky](hyper-v-azure-support-matrix.md#replicated-vms).

## <a name="step-1-sign-in-to-your-azure-account"></a>Krok 1: přihlášení k účtu Azure

1. Otevřete konzolu PowerShellu a spusťte tento příkaz, abyste se přihlásili ke svému účtu Azure. Rutina zobrazí webovou stránku s výzvou k zadání přihlašovacích údajů k účtu: `Connect-AzAccount` .
   - Alternativně můžete použít přihlašovací údaje účtu jako parametr v `Connect-AzAccount` rutině pomocí parametru **Credential** .
   - Pokud jste partnerem CSP při práci jménem tenanta, zadejte zákazníka jako tenanta pomocí názvu primární domény tenantID nebo tenanta. Příklad: `Connect-AzAccount -Tenant "fabrikam.com"`
1. Přidružte předplatné, které chcete používat s účtem, protože účet může mít několik předplatných:

   ```azurepowershell
   Set-AzContext -Subscription $SubscriptionName
   ```

1. Ověřte, jestli je vaše předplatné zaregistrované, aby používaly poskytovatele Azure pro Recovery Services a Site Recovery, a to pomocí těchto příkazů:

   ```azurepowershell
   Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices
   ```

1. Ověřte, že je ve výstupu příkazu nastavená možnost **RegistrationState** na **zaregistrované**, můžete přejít ke kroku 2. Pokud ne, měli byste ve svém předplatném zaregistrovat chybějícího poskytovatele spuštěním těchto příkazů:

   ```azurepowershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.RecoveryServices
   ```

1. Pomocí následujících příkazů ověřte, jestli se poskytovatelé úspěšně zaregistrovali:

   ```azurepowershell
   Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices
   ```

## <a name="step-2-set-up-the-vault"></a>Krok 2: nastavení trezoru

1. Vytvořte Azure Resource Manager skupinu prostředků, ve které chcete vytvořit trezor, nebo použijte existující skupinu prostředků. Následujícím způsobem vytvořte novou skupinu prostředků. `$ResourceGroupName`Proměnná obsahuje název skupiny prostředků, kterou chcete vytvořit, a proměnná $Geo obsahuje oblast Azure, ve které chcete vytvořit skupinu prostředků (například "Brazílie – jih").

   ```azurepowershell
   New-AzResourceGroup -Name $ResourceGroupName -Location $Geo
   ```

1. Pokud chcete získat seznam skupin prostředků ve vašem předplatném, spusťte `Get-AzResourceGroup` rutinu.
1. Vytvořte nový trezor služby Azure Recovery Services následujícím způsobem:

   ```azurepowershell
   $vault = New-AzRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>
   ```

Pomocí rutiny můžete načíst seznam existujících trezorů `Get-AzRecoveryServicesVault` .

## <a name="step-3-set-the-recovery-services-vault-context"></a>Krok 3: nastavte kontext trezoru Recovery Services.

Nastavte kontext trezoru následujícím způsobem:

```azurepowershell
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

## <a name="step-4-create-a-hyper-v-site"></a>Krok 4: vytvoření lokality Hyper-V

1. Vytvořte novou lokalitu Hyper-V následujícím způsobem:

   ```azurepowershell
   $sitename = "MySite"                #Specify site friendly name
   New-AzRecoveryServicesAsrFabric -Type HyperVSite -Name $sitename
   ```

1. Tato rutina spustí úlohu Site Recovery pro vytvoření lokality a vrátí objekt Site Recovery úlohy. Počkejte, než se úloha dokončí, a ověřte, že se úloha úspěšně dokončila.
1. Pomocí `Get-AzRecoveryServicesAsrJob` rutiny načtěte objekt úlohy a ověřte aktuální stav úlohy.
1. Vygenerujte a stáhněte registrační klíč pro lokalitu následujícím způsobem:

   ```azurepowershell
   $SiteIdentifier = Get-AzRecoveryServicesAsrFabric -Name $sitename | Select-Object -ExpandProperty SiteIdentifier
   $path = Get-AzRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename
   ```

1. Zkopírujte stažený klíč na hostitele Hyper-V. K registraci hostitele Hyper-V do lokality budete potřebovat klíč.

## <a name="step-5-install-the-provider-and-agent"></a>Krok 5: instalace poskytovatele a agenta

1. Stáhněte instalační program pro nejnovější verzi zprostředkovatele od [společnosti Microsoft](https://aka.ms/downloaddra).
1. Spusťte instalační program na hostiteli Hyper-V.
1. Na konci instalace pokračujte v kroku registrace.
1. Po zobrazení výzvy zadejte stažený klíč a dokončete registraci hostitele Hyper-V.
1. Ověřte, zda je Hostitel Hyper-V zaregistrován na webu následujícím způsobem:

   ```azurepowershell
   $server = Get-AzRecoveryServicesAsrFabric -Name $siteName | Get-AzRecoveryServicesAsrServicesProvider -FriendlyName $server-friendlyname
   ```

Pokud používáte server základní technologie Hyper-V, Stáhněte instalační soubor a proveďte následující kroky:

1. Extrahujte soubory z _AzureSiteRecoveryProvider.exe_ do místního adresáře spuštěním tohoto příkazu:

   ```console
   AzureSiteRecoveryProvider.exe /x:. /q
   ```

1. Spusťte následující příkaz:

   ```console
   .\setupdr.exe /i
   ```

   Výsledky jsou protokolovány do _%ProgramData%\ASRLogs\DRASetupWizard.log_.

1. Zaregistrujte server spuštěním tohoto příkazu:

   ```console
   cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
   ```

## <a name="step-6-create-a-replication-policy"></a>Krok 6: vytvoření zásady replikace

Než začnete, zadaný účet úložiště by měl být ve stejné oblasti Azure jako trezor a měla by mít povolenou geografickou replikaci.

1. Vytvořte zásadu replikace následujícím způsobem:

   ```azurepowershell
   $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
   $PolicyName = “replicapolicy”
   $Recoverypoints = 6                    #specify the number of recovery points
   $storageaccountID = Get-AzStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select-Object -ExpandProperty Id

   $PolicyResult = New-AzRecoveryServicesAsrPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds -NumberOfRecoveryPointsToRetain $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID
   ```

1. Zkontrolujte vrácenou úlohu a ujistěte se, že se vytváření zásad replikace zdaří.

1. Načtěte kontejner ochrany, který odpovídá webu, následovně:

   ```azurepowershell
   $protectionContainer = Get-AzRecoveryServicesAsrProtectionContainer
   ```

1. Přidružte kontejner ochrany k zásadě replikace následujícím způsobem:

   ```azurepowershell
   $Policy = Get-AzRecoveryServicesAsrPolicy -FriendlyName $PolicyName
   $associationJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name $mappingName -Policy $Policy -PrimaryProtectionContainer $protectionContainer[0]
   ```

1. Počkejte, než se úloha přidružení úspěšně dokončí.

1. Načtěte mapování kontejneru ochrany.

   ```azurepowershell
   $ProtectionContainerMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $protectionContainer
   ```

## <a name="step-7-enable-vm-protection"></a>Krok 7: povolení ochrany virtuálních počítačů

1. Načtěte chráněnou položku, která odpovídá virtuálnímu počítači, který chcete chránit, následovně:

   ```azurepowershell
   $VMFriendlyName = "Fabrikam-app"          #Name of the VM
   $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
   ```

1. Chraňte virtuální počítač. Pokud má virtuální počítač, který chráníte, více než jeden disk, který je k němu připojený, určete pomocí parametru **OSDiskName** disk s operačním systémem.

   ```azurepowershell
   $OSType = "Windows"          # "Windows" or "Linux"
   $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID
   ```

1. Počkejte, až virtuální počítače dostanou chráněný stav po počáteční replikaci. To může nějakou dobu trvat, v závislosti na faktorech, jako je třeba množství dat, která se mají replikovat, a dostupné nadřazené šířky pásma do Azure. Pokud je chráněný stav na místě, aktualizují se stav úlohy a StateDescription následujícím způsobem:

   ```console
   PS C:\> $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob

   PS C:\> $DRjob | Select-Object -ExpandProperty State
   Succeeded

   PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
   Completed
   ```

1. Aktualizujte vlastnosti obnovení (například velikost role virtuálního počítače) a síť Azure, ke které chcete připojit síťovou kartu virtuálního počítače po převzetí služeb při selhání.

   ```console
   PS C:\> $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

   PS C:\> $VMFriendlyName = "Fabrikam-App"

   PS C:\> $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

   PS C:\> $UpdateJob = Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

   PS C:\> $UpdateJob = Get-AzRecoveryServicesAsrJob -Job $UpdateJob

   PS C:\> $UpdateJob | Select-Object -ExpandProperty state

   PS C:\> Get-AzRecoveryServicesAsrJob -Job $job | Select-Object -ExpandProperty state

   Succeeded
   ```

> [!NOTE]
> Pokud chcete replikovat na spravované disky s povoleným CMK v Azure, proveďte následující kroky pomocí AZ PowerShell 3.3.0 a další:
>
> 1. Povolení převzetí služeb při selhání u spravovaných disků aktualizací vlastností virtuálního počítače
> 1. Pomocí `Get-AzRecoveryServicesAsrReplicationProtectedItem` rutiny načtěte ID disku pro každý disk chráněné položky.
> 1. Vytvořte objekt Dictionary pomocí `New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"` rutiny, který bude obsahovat mapování ID disku na disk pro šifrování disku. Tyto sady diskových šifrovacích sad se v cílové oblasti předem vytvoří.
> 1. Aktualizujte vlastnosti virtuálního počítače pomocí `Set-AzRecoveryServicesAsrReplicationProtectedItem` rutiny předáním objektu Dictionary v parametru **DiskIdToDiskEncryptionSetMap** .

## <a name="step-8-run-a-test-failover"></a>Krok 8: spuštění testovacího převzetí služeb při selhání

1. Spusťte testovací převzetí služeb při selhání následujícím způsobem:

   ```azurepowershell
   $nw = Get-AzVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

   $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

   $TFjob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $VM -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
   ```

1. Ověřte, že testovací virtuální počítač je vytvořený v Azure. Po vytvoření testovacího virtuálního počítače v Azure se úloha testovacího převzetí služeb při selhání pozastavila.
1. Chcete-li vyčistit a dokončit testovací převzetí služeb při selhání, spusťte příkaz:

   ```azurepowershell
   $TFjob = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $rpi -Comment "TFO done"
   ```

## <a name="next-steps"></a>Další kroky

[Přečtěte si další informace](/powershell/module/az.recoveryservices) o Azure Site Recovery s rutinami prostředí PowerShell pro Azure Resource Manager.
