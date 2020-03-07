---
title: Zálohování souborů Azure pomocí PowerShellu
description: V tomto článku se dozvíte, jak zálohovat soubory Azure pomocí služby Azure Backup a PowerShellu.
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: f85451e0da6458de34aea936836b46781f4c4a21
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78382515"
---
# <a name="back-up-azure-files-with-powershell"></a>Zálohování souborů Azure pomocí PowerShellu

Tento článek popisuje, jak použít Azure PowerShell k zálohování sdílené složky souborů Azure pomocí trezoru služby [Azure Backup](backup-overview.md) Recovery Services.

V tomto článku se dozvíte, jak:

> [!div class="checklist"]
>
> * Nastavte PowerShell a zaregistrujte poskytovatele služby Azure Recovery Services.
> * Vytvořte trezor služby Recovery Services.
> * Nakonfigurujte zálohování sdílené složky Azure.
> * Spusťte úlohu zálohování.

## <a name="before-you-start"></a>Než začnete

* [Přečtěte si další informace](backup-azure-recovery-services-vault-overview.md) o úložištích Recovery Services.
* Přečtěte si o možnostech verze Preview pro [Zálohování sdílených složek Azure](backup-afs.md).
* Zkontrolujte hierarchii objektů PowerShell pro Recovery Services.

## <a name="recovery-services-object-hierarchy"></a>Recovery Services hierarchie objektů

Hierarchie objektů je shrnuta v následujícím diagramu.

![Recovery Services hierarchie objektů](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Přečtěte si referenční informace k [rutině](/powershell/module/az.recoveryservices) **AZ. RecoveryServices** v knihovně Azure.

## <a name="set-up-and-install"></a>Nastavení a instalace

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Nastavte PowerShell následujícím způsobem:

1. [Stáhněte si nejnovější verzi programu AZ PowerShell](/powershell/azure/install-az-ps). Minimální požadovaná verze je 1.0.0.

> [!WARNING]
> Minimální verze PS požadovaná pro verzi Preview byla "AZ 1.0.0". Z důvodu nadcházejících změn pro GA bude minimální požadovaná verze PS "AZ. RecoveryServices 2.6.0". Je velmi důležité upgradovat všechny stávající verze PS na tuto verzi. Jinak existující skripty budou přerušeny po GA. Minimální verzi nainstalujte pomocí následujících příkazů PS.

```powershell
Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
```

2. Pomocí tohoto příkazu Najděte rutiny Azure Backup PowerShellu:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Zkontrolujte aliasy a rutiny pro Azure Backup, Azure Site Recovery a Recovery Services trezor. Tady je příklad toho, co se vám může zobrazit. Nejedná se o úplný seznam rutin.

    ![Seznam rutin Recovery Services](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Přihlaste se ke svému účtu Azure pomocí **Connect-AzAccount**.
5. Na zobrazené webové stránce budete vyzváni k zadání přihlašovacích údajů k účtu.

    * Alternativně můžete zahrnout přihlašovací údaje účtu jako parametr v rutině **Connect-AzAccount** s parametrem **-Credential**.
    * Pokud jste partner CSP jménem tenanta, zadejte zákazníka jako tenanta pomocí názvu primární domény tenantID nebo tenanta. Příkladem je **Connect-AzAccount-Tenant** Fabrikam.com.

6. Přidružte předplatné, které chcete používat s účtem, protože účet může mít několik předplatných.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Pokud používáte Azure Backup poprvé, zaregistrujte poskytovatele služby Azure Recovery Services s vaším předplatným pomocí rutiny **Register-AzResourceProvider** .

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Ověřte, zda poskytovatelé úspěšně zaregistrovali:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. Ve výstupu příkazu ověřte, že **RegistrationState** změny se **zaregistrují**. Pokud ne, spusťte znovu rutinu **Register-AzResourceProvider** .

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Recovery Services trezor je prostředek Správce prostředků, takže ho musíte umístit do skupiny prostředků. Můžete použít existující skupinu prostředků, nebo můžete vytvořit skupinu prostředků pomocí rutiny **New-AzResourceGroup** . Při vytváření skupiny prostředků zadejte název a umístění skupiny prostředků.

Pomocí těchto kroků můžete vytvořit trezor Recovery Services.

1. Trezor se umístí do skupiny prostředků. Pokud nemáte existující skupinu prostředků, vytvořte novou pomocí [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). V tomto příkladu vytvoříme novou skupinu prostředků v Západní USA oblasti.

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```

2. K vytvoření trezoru použijte rutinu [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) . Zadejte stejné umístění pro trezor, které bylo použito pro skupinu prostředků.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Zadejte typ redundance, který se použije pro úložiště trezoru.

   * Můžete použít [místně redundantní úložiště](../storage/common/storage-redundancy-lrs.md) nebo [geograficky redundantní úložiště](../storage/common/storage-redundancy-grs.md).
   * Následující příklad nastaví možnost **-BackupStorageRedundancy** pro příkaz[set-AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd pro **testvault** nastavenou na geograficky **redundantní**.

     ```powershell
     $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
     Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
     ```

### <a name="view-the-vaults-in-a-subscription"></a>Zobrazení trezorů v předplatném

Pokud chcete zobrazit všechny trezory v rámci předplatného, použijte [příkaz Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

Výstup je podobný následujícímu. Všimněte si, že je k dispozici přidružená skupina prostředků a umístění.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>Nastavte kontext trezoru.

Uložte objekt trezoru do proměnné a nastavte kontext trezoru.

* Mnoho rutin Azure Backup vyžaduje jako vstup objekt Recovery Services trezoru, takže je vhodné uložit objekt trezoru do proměnné.
* Kontext trezoru představuje typ chráněných dat v trezoru. Nastavte ji pomocí [set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). Po nastavení je kontext použit pro všechny následné rutiny.

Následující příklad nastaví kontext trezoru pro **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Načtení ID trezoru

V souladu s pokyny pro Azure PowerShell plánujeme vyřadit nastavení kontextu trezoru. Místo toho můžete uložit nebo načíst ID trezoru a předat ho relevantním příkazům. Takže pokud jste nastavili kontext trezoru nebo chcete zadat příkaz, který se má spustit pro určitý trezor, předejte ID trezoru jako "-vaultID" do všech relevantních příkazů následujícím způsobem:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>Konfigurace zásady zálohování

Zásady zálohování určují plán zálohování a dobu, po kterou mají být udržovány body obnovení zálohy:

* Zásada zálohování je přidružená minimálně k jedné zásadě uchovávání informací. Zásady uchovávání informací definují, jak dlouho je bod obnovení udržován před jeho odstraněním.
* Podívejte se na výchozí uchování zásad zálohování pomocí [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
* Seznamte se s výchozím plánem zásad zálohování pomocí [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
* Pomocí rutiny [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) vytvoříte nové zásady zálohování. Zadáváte objekty zásad plánování a uchovávání dat.

Ve výchozím nastavení je v objektu zásad plánování definován počáteční čas. Pomocí následujícího příkladu změňte čas spuštění na požadovaný čas zahájení. Požadovaný čas spuštění by měl být také ve formátu UTC. Následující příklad předpokládá, že požadovaný počáteční čas je 01:00 UTC pro denní zálohy.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Je nutné zadat čas spuštění pouze v 30 minutách pouze násobcích. V tomto příkladu může být pouze "01:00:00" nebo "02:30:00". Počáteční čas nemůže být "01:15:00"

V následujícím příkladu jsou uloženy zásady plánu a zásady uchovávání informací v proměnných. Pak tyto proměnné používá jako parametry pro nové zásady (**NewAFSPolicy**). **NewAFSPolicy** provádí každodenní zálohování a uchovává ho po dobu 30 dnů.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Výstup je podobný následujícímu.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2019 1:30:00 AM
```

## <a name="enable-backup"></a>Povolit zálohování

Po definování zásad zálohování můžete povolit ochranu sdílené složky Azure pomocí zásad.

### <a name="retrieve-a-backup-policy"></a>Načtení zásady zálohování

Příslušný objekt zásad načtete pomocí [Get-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0). Pomocí této rutiny můžete získat konkrétní zásady nebo zobrazit zásady spojené s typem úlohy.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>Načtěte zásadu pro typ úlohy.

Následující příklad načte zásady pro typ úlohy **AzureFiles**.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

Výstup je podobný následujícímu.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> Časové pásmo pole **BackupTime** v PowerShellu je univerzální koordinovaný čas (UTC). Když se v Azure Portal zobrazí čas zálohování, upraví se čas na vaše místní časové pásmo.

### <a name="retrieve-a-specific-policy"></a>Načíst konkrétní zásadu

Následující zásada načte zásady zálohování s názvem **dailyafs**.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-backup-and-apply-policy"></a>Povolit zálohování a použít zásady

Povolte ochranu pomocí [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Po přidružení zásady k trezoru se spustí zálohování v souladu s plánem zásad.

Následující příklad povolí ochranu sdílené složky Azure **testAzureFileShare** v účtu úložiště **testStorageAcct**s použitím zásad **dailyafs**.

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

Příkaz čeká na dokončení úlohy konfigurace ochrany a poskytne podobný výstup, jak je znázorněno na obrázku.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

## <a name="important-notice---backup-item-identification-for-afs-backups"></a>Důležité upozornění – identifikace zálohových položek pro zálohy AFS

Tato část popisuje důležitou změnu v zálohování AFS v článku Příprava pro GA.

Když zapnete zálohování pro AFS, zadá uživatel popisný název sdílené složky zákazníka jako název entity a vytvoří se zálohovaná položka. Název zálohované položky je jedinečný identifikátor vytvořený službou Azure Backup. Identifikátor obvykle zahrnuje popisný název uživatele. Pokud ale chcete zpracovat důležitý scénář obnovitelného odstranění, kde se dá odstranit sdílená složka, a můžete vytvořit jinou sdílenou složku se stejným názvem, jedinečná identita sdílené složky Azure teď bude ID místo popisného názvu zákazníka. Aby bylo možné znát jedinečnou identitu a název každé položky, stačí spustit příkaz ```Get-AzRecoveryServicesBackupItem``` s příslušnými filtry pro backupManagementType a WorkloadType pro získání všech relevantních položek a pak sledovat pole název v vráceném objektu nebo odpovědi PS. Vždycky se doporučuje vypisovat položky a potom v odpovědi načíst jejich jedinečný název z pole název. Tuto hodnotu použijte k filtrování položek s parametrem Name. Jinak pomocí parametru FriendlyName Načtěte položku s popisným názvem nebo identifikátorem zákazníka.

> [!WARNING]
> Ujistěte se, že je verze PS upgradována na minimální verzi příkazu AZ. RecoveryServices 2.6.0 pro zálohy na AFS. V této verzi je filtr ' friendlyName ' k dispozici pro ```Get-AzRecoveryServicesBackupItem``` příkaz. Předejte název sdílené složky Azure do parametru friendlyName. Pokud předáte název sdílené složky Azure do parametru název, tato verze vyvolá upozornění, aby tento popisný název předával do parametru popisného názvu. Pokud nenainstalujete tuto minimální verzi, může dojít k selhání existujících skriptů. Nainstalujte minimální verzi PS pomocí následujícího příkazu.

```powershell
Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
```

## <a name="trigger-an-on-demand-backup"></a>Aktivace zálohování na vyžádání

Pomocí rutiny [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0) spustíte zálohování na vyžádání pro chráněnou sdílenou složku Azure.

1. Načtěte účet úložiště z kontejneru v trezoru, který obsahuje data záloh pomocí [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Pokud chcete spustit úlohu zálohování, získáte informace o sdílené složce Azure pomocí [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Spusťte zálohování na vyžádání pomocí rutiny[Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).

Zálohování na vyžádání spusťte následujícím způsobem:

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -FriendlyName "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

Příkaz vrátí úlohu s ID, které se má sledovat, jak je znázorněno v následujícím příkladu.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Snímky sdílené složky Azure se používají v době, kdy se provádí zálohování, takže úloha se obvykle dokončí v čase, kdy příkaz vrátí tento výstup.

### <a name="using-on-demand-backups-to-extend-retention"></a>Použití záloh na vyžádání k rozšířenému uchovávání

Zálohy na vyžádání se dají použít k uchování snímků po dobu 10 let. Plánovače se dají použít ke spouštění skriptů PowerShellu na vyžádání s vybraným uchováváním, takže snímky se v pravidelných intervalech přijímají každý týden, měsíc nebo rok. Při pořizování běžných snímků se řiďte [omezeními zálohování na vyžádání](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#how-many-on-demand-backups-can-i-take-per-file-share) pomocí služby Azure Backup.

Pokud hledáte ukázkové skripty, můžete se podívat na ukázkový skript na GitHubu (<https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup>) pomocí sady Azure Automation Runbook, která umožňuje pravidelné plánování záloh a jejich uchování až po dobu 10 let.

> [!WARNING]
> Ujistěte se, že je verze PS upgradována na minimální verzi pro příkaz AZ. RecoveryServices 2.6.0 pro zálohy AFS v sadách Automation. Starý modul ' AzureRM ' bude nutné nahradit modulem ' AZ '. V této verzi je filtr ' friendlyName ' k dispozici pro ```Get-AzRecoveryServicesBackupItem``` příkaz. Předejte název sdílené složky Azure do parametru friendlyName. Pokud předáte název sdílené složky Azure do parametru název, tato verze vyvolá upozornění, aby tento popisný název předával do parametru popisného názvu.

## <a name="next-steps"></a>Další kroky

[Přečtěte si o](backup-afs.md) zálohování souborů Azure v Azure Portal.
