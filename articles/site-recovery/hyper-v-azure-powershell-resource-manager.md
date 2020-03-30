---
title: Obnovení zotavení po havárii virtuálního počítače Hyper-V pomocí Azure Site Recovery a PowerShellu
description: Automatizujte zotavení po havárii virtuálních počítačích Hyper-V do Azure pomocí služby Azure Site Recovery pomocí PowerShellu a Azure Resource Manageru.
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 01/10/2020
ms.author: sutalasi
ms.openlocfilehash: 6499c986bef965848303ee9833fd59f5e3f0889c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257989"
---
# <a name="set-up-disaster-recovery-to-azure-for-hyper-v-vms-using-powershell-and-azure-resource-manager"></a>Nastavení zotavení po havárii na Azure for Hyper-V Virtuální počítače pomocí PowerShellu a Azure Resource Manageru

[Azure Site Recovery](site-recovery-overview.md) přispívá k vaší strategii kontinuity podnikání a zotavení po havárii (BCDR) tím, že organizuje replikaci, převzetí služeb při selhání a obnovení virtuálních počítačů (VM) a místních virtuálních počítačů a fyzických serverů.

Tento článek popisuje, jak používat Windows PowerShell, spolu s Azure Resource Manager, replikovat virtuální počítače Hyper-V do Azure. Příklad použitý v tomto článku ukazuje, jak replikovat jeden virtuální počítač spuštěný na hostiteli Hyper-V do Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell poskytuje rutiny pro správu Azure pomocí Windows PowerShellu. Rutiny PowerShellu pro obnovení lokality, které jsou dostupné v Azure PowerShellu pro Azure Resource Manager, vám pomůžou chránit a obnovit servery v Azure.

K použití tohoto článku nemusíte být odborníkem na prostředí PowerShell, ale musíte porozumět základním konceptům, jako jsou moduly, rutiny a relace. Další informace najdete v [tématu Dokumentace k Prostředí PowerShell](/powershell) a [použití Azure PowerShellu ve Správci prostředků Azure](../powershell-azure-resource-manager.md).

> [!NOTE]
> Partneři společnosti Microsoft v programu Zprostředkovatel cloudových řešení (CSP) mohou konfigurovat a spravovat ochranu zákaznických serverů podle příslušných předplatných CSP (předplatná tenanta).

## <a name="before-you-start"></a>Než začnete

Ujistěte se, že máte tyto předpoklady na místě:

- Účet [Microsoft Azure.](https://azure.microsoft.com/) Můžete začít s [bezplatnou zkušební verzí](https://azure.microsoft.com/pricing/free-trial/). Kromě toho si můžete přečíst o [cenách Azure Site Recovery Manager](https://azure.microsoft.com/pricing/details/site-recovery/).
- Azure Powershell Informace o této verzi a o tom, jak ji nainstalovat, najdete [v tématu Instalace Azure PowerShellu](/powershell/azure/install-az-ps).

Kromě toho konkrétní příklad popsaný v tomto článku má následující požadavky:

- Hostitel hyper-V se systémem Windows Server 2012 R2 nebo Microsoft Hyper-V Server 2012 R2 obsahující jeden nebo více virtuálních připojení. Servery Hyper-V by měly být připojeny k Internetu, a to buď přímo, nebo prostřednictvím proxy serveru.
- Virtuální chod, které chcete replikovat, by měly odpovídat [těmto požadavkům](hyper-v-azure-support-matrix.md#replicated-vms).

## <a name="step-1-sign-in-to-your-azure-account"></a>Krok 1: Přihlášení ke svému účtu Azure

1. Otevřete konzolu PowerShellu a spusťte tento příkaz pro přihlášení ke svému účtu Azure. Rutina zobrazí webovou stránku s výzvou k `Connect-AzAccount`zadání přihlašovacích údajů k účtu: .
   - Případně můžete zahrnout pověření účtu jako parametr v `Connect-AzAccount` rutině pomocí parametru **Credential.**
   - Pokud jste partner csp, který pracuje jménem tenanta, zadejte zákazníka jako tenanta pomocí jejich id tenanta nebo primárního názvu domény klienta. Příklad: `Connect-AzAccount -Tenant "fabrikam.com"`
1. Přidružte předplatné, které chcete použít, k účtu, protože účet může mít několik předplatných:

   ```azurepowershell
   Set-AzContext -Subscription $SubscriptionName
   ```

1. Ověřte, že vaše předplatné je registrované pro použití zprostředkovatelů Azure pro služby obnovení a obnovení webu, pomocí těchto příkazů:

   ```azurepowershell
   Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices
   ```

1. Ověřte, že ve výstupu příkazu **RegistrationState** je nastavena na **Registrováno**, můžete přejít ke kroku 2. Pokud ne, měli byste zaregistrovat chybějící zprostředkovatele v předplatném spuštěním těchto příkazů:

   ```azurepowershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.RecoveryServices
   ```

1. Ověřte, zda se zprostředkovatelé úspěšně zaregistrovali pomocí následujících příkazů:

   ```azurepowershell
   Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices
   ```

## <a name="step-2-set-up-the-vault"></a>Krok 2: Nastavení trezoru

1. Vytvořte skupinu prostředků Azure Resource Manager, ve které chcete vytvořit trezor nebo použít existující skupinu prostředků. Vytvořte novou skupinu prostředků následujícím způsobem. Proměnná `$ResourceGroupName` obsahuje název skupiny prostředků, které chcete vytvořit, a proměnná $Geo obsahuje oblast Azure, ve které chcete vytvořit skupinu prostředků (například "Brazílie – jih").

   ```azurepowershell
   New-AzResourceGroup -Name $ResourceGroupName -Location $Geo
   ```

1. Chcete-li získat seznam skupin prostředků v `Get-AzResourceGroup` rámci předplatného, spusťte rutinu.
1. Vytvořte nový trezor Služby azure recovery services takto:

   ```azurepowershell
   $vault = New-AzRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>
   ```

Pomocí `Get-AzRecoveryServicesVault` rutiny můžete načíst seznam existujících trezorů.

## <a name="step-3-set-the-recovery-services-vault-context"></a>Krok 3: Nastavení kontextu trezoru služby Recovery Services

Kontext úschovny nastavte takto:

```azurepowershell
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

## <a name="step-4-create-a-hyper-v-site"></a>Krok 4: Vytvoření webu Hyper-V

1. Vytvořte nový web Hyper-V takto:

   ```azurepowershell
   $sitename = "MySite"                #Specify site friendly name
   New-AzRecoveryServicesAsrFabric -Type HyperVSite -Name $sitename
   ```

1. Tato rutina spustí úlohu obnovení webu k vytvoření webu a vrátí objekt úlohy obnovení webu. Počkejte na dokončení úlohy a ověřte, zda byla úloha úspěšně dokončena.
1. Pomocí `Get-AzRecoveryServicesAsrJob` rutiny načtěte objekt úlohy a zkontrolujte aktuální stav úlohy.
1. Vygenerujte a stáhněte registrační klíč pro web takto:

   ```azurepowershell
   $SiteIdentifier = Get-AzRecoveryServicesAsrFabric -Name $sitename | Select-Object -ExpandProperty SiteIdentifier
   $path = Get-AzRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename
   ```

1. Zkopírujte stažený klíč do hostitele Hyper-V. Potřebujete klíč k registraci hostitele Hyper-V na webu.

## <a name="step-5-install-the-provider-and-agent"></a>Krok 5: Instalace zprostředkovatele a agenta

1. Stáhněte si instalační program pro nejnovější verzi poskytovatele od společnosti [Microsoft](https://aka.ms/downloaddra).
1. Spusťte instalační program na hostiteli Hyper-V.
1. Na konci instalace pokračujte krokem registrace.
1. Po zobrazení výzvy zadejte stažený klíč a úplnou registraci hostitele Hyper-V.
1. Ověřte, zda je hostitel Technologie Hyper-V registrován na webu následujícím způsobem:

   ```azurepowershell
   $server = Get-AzRecoveryServicesAsrFabric -Name $siteName | Get-AzRecoveryServicesAsrServicesProvider -FriendlyName $server-friendlyname
   ```

Pokud používáte základní server Hyper-V, stáhněte si instalační soubor a postupujte takto:

1. Extrahujte soubory z _azuresiterecoveryprovider.exe_ do místního adresáře spuštěním tohoto příkazu:

   ```console
   AzureSiteRecoveryProvider.exe /x:. /q
   ```

1. Spusťte následující příkaz:

   ```console
   .\setupdr.exe /i
   ```

   Výsledky jsou zaznamenány do _souboru %ProgramData%\ASRLogs\DRASetupWizard.log_.

1. Zaregistrujte server spuštěním tohoto příkazu:

   ```console
   cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
   ```

## <a name="step-6-create-a-replication-policy"></a>Krok 6: Vytvoření zásad replikace

Než začnete, zadaný účet úložiště by měl být ve stejné oblasti Azure jako trezor a měl by mít povolenou geografickou replikaci.

1. Vytvořte zásady replikace následujícím způsobem:

   ```azurepowershell
   $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
   $PolicyName = “replicapolicy”
   $Recoverypoints = 6                    #specify the number of recovery points
   $storageaccountID = Get-AzStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select-Object -ExpandProperty Id

   $PolicyResult = New-AzRecoveryServicesAsrPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds -NumberOfRecoveryPointsToRetain $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID
   ```

1. Zkontrolujte vrácenou úlohu a ujistěte se, že vytvoření zásad replikace proběhne úspěšně.

1. Načtěte kontejner ochrany, který odpovídá webu, následovně:

   ```azurepowershell
   $protectionContainer = Get-AzRecoveryServicesAsrProtectionContainer
   ```

1. Přidružte kontejner ochrany k zásadám replikace následujícím způsobem:

   ```azurepowershell
   $Policy = Get-AzRecoveryServicesAsrPolicy -FriendlyName $PolicyName
   $associationJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name $mappingName -Policy $Policy -PrimaryProtectionContainer $protectionContainer[0]
   ```

1. Počkejte na úspěšné dokončení úlohy přidružení.

1. Načíst mapování kontejneru ochrany.

   ```azurepowershell
   $ProtectionContainerMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $protectionContainer
   ```

## <a name="step-7-enable-vm-protection"></a>Krok 7: Povolení ochrany virtuálních mís

1. Načíst chráněnou položku, která odpovídá virtuálnímu virtuálnímu zařízení, které chcete chránit, takto:

   ```azurepowershell
   $VMFriendlyName = "Fabrikam-app"          #Name of the VM
   $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
   ```

1. Chraňte virtuální ho. Pokud je k virtuálnímu počítače, který chráníte, připojeno více než jeden disk, zadejte disk operačního systému pomocí parametru **OSDiskName.**

   ```azurepowershell
   $OSType = "Windows"          # "Windows" or "Linux"
   $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID
   ```

1. Počkejte, až virtuální počítače dosáhnou chráněného stavu po počáteční replikaci. To může chvíli trvat, v závislosti na faktorech, jako je množství dat, která mají být replikována, a dostupná šířka pásma pro Azure. Pokud je chráněný stav na místě, stav úlohy a StateDescription jsou aktualizovány takto:

   ```console
   PS C:\> $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob

   PS C:\> $DRjob | Select-Object -ExpandProperty State
   Succeeded

   PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
   Completed
   ```

1. Aktualizujte vlastnosti obnovení (například velikost role virtuálního počítače) a síť Azure, ke které chcete připojit nic virtuálního počítače po převzetí služeb při selhání.

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
> Pokud chcete replikovat na spravované disky s povolenou CMK v Azure, proveďte následující kroky pomocí prostředí Az PowerShell 3.3.0 a dále:
>
> 1. Povolení převzetí služeb při selhání na spravovaných discích aktualizací vlastností virtuálního počítače
> 1. Pomocí `Get-AzRecoveryServicesAsrReplicationProtectedItem` rutiny načíst ID disku pro každý disk chráněné položky
> 1. Vytvořte objekt slovníku pomocí `New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"` rutiny, která obsahuje mapování ID disku na sadu šifrování disku. Tyto sady šifrování disku mají být předem vytvořeny vámi v cílové oblasti.
> 1. Aktualizujte vlastnosti `Set-AzRecoveryServicesAsrReplicationProtectedItem` virtuálního počítače pomocí rutiny předáním objektu slovníku v parametru **DiskIdToDiskEncryptionSetMap.**

## <a name="step-8-run-a-test-failover"></a>Krok 8: Spuštění zkušebního převzetí služeb při selhání

1. Spuštění testu převzetí služeb při selhání takto:

   ```azurepowershell
   $nw = Get-AzVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

   $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

   $TFjob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $VM -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
   ```

1. Ověřte, že testovací virtuální počítač se vytvoří v Azure. Testovací úloha převzetí služeb při selhání je pozastavena po vytvoření testovacího virtuálního počítače v Azure.
1. Chcete-li vyčistit a dokončit zkušební převzetí služeb při selhání, spusťte:

   ```azurepowershell
   $TFjob = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $rpi -Comment "TFO done"
   ```

## <a name="next-steps"></a>Další kroky

[Další informace](/powershell/module/az.recoveryservices) o Azure Site Recovery s rutinami Azure Resource Manager PowerShell.
