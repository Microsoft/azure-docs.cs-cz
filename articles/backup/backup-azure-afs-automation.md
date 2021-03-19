---
title: Zálohování sdílené složky Azure pomocí PowerShellu
description: V tomto článku se dozvíte, jak zálohovat sdílenou složku souborů Azure pomocí služby Azure Backup a PowerShellu.
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 948931764769bc967b88e7942b7e8384b0f93dff
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87077006"
---
# <a name="back-up-an-azure-file-share-by-using-powershell"></a>Zálohování sdílené složky Azure pomocí PowerShellu

Tento článek popisuje, jak použít Azure PowerShell k zálohování sdílené složky souborů Azure pomocí trezoru služby [Azure Backup](backup-overview.md) Recovery Services.

V tomto článku se dozvíte, jak:

> [!div class="checklist"]
>
> * Nastavte PowerShell a zaregistrujte poskytovatele Recovery Services.
> * Vytvořte trezor služby Recovery Services.
> * Nakonfigurujte zálohování sdílené složky Azure.
> * Spusťte úlohu zálohování.

## <a name="before-you-start"></a>Než začnete

* [Přečtěte si další informace](backup-azure-recovery-services-vault-overview.md) o úložištích Recovery Services.
* Přečtěte si referenční informace k [rutině](/powershell/module/az.recoveryservices) AZ. RecoveryServices v knihovně Azure.
* Zkontrolujte následující hierarchii objektů PowerShellu pro Recovery Services:

  ![Recovery Services hierarchie objektů](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

## <a name="set-up-powershell"></a>Nastavení PowerShellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Nastavte PowerShell následujícím způsobem:

1. [Stáhněte si nejnovější verzi Azure PowerShell](/powershell/azure/install-az-ps).

    > [!NOTE]
    > Minimální verze prostředí PowerShell požadovaná pro zálohování sdílených složek Azure je AZ. RecoveryServices 2.6.0. Pomocí nejnovější verze nebo alespoň minimální verze vám pomůže vyhnout se problémům se stávajícími skripty. Minimální verzi nainstalujete pomocí následujícího příkazu PowerShellu:
    >
    > ```powershell
    > Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
    > ```

2. Pomocí tohoto příkazu Najděte rutiny PowerShellu pro Azure Backup:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Zkontrolujte aliasy a rutiny pro Azure Backup, Azure Site Recovery a trezoru Recovery Services. Tady je příklad toho, co se vám může zobrazit. Nejedná se o úplný seznam rutin.

    ![Seznam rutin Recovery Services](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Přihlaste se ke svému účtu Azure pomocí **Connect-AzAccount**.
5. Na webové stránce, která se zobrazí, budete vyzváni k zadání přihlašovacích údajů k účtu.

    Alternativně můžete do rutiny **Connect-AzAccount** zahrnout přihlašovací údaje účtu pomocí parametr **-Credential**.

    Pokud jste partnerem CSP při práci jménem tenanta, zadejte zákazníka jako tenanta. Použijte své ID klienta nebo název primární domény tenanta. Příkladem je **Connect-AzAccount-tenant "fabrikam.com"**.

6. Přidružte předplatné, které chcete používat s účtem, protože účet může mít několik předplatných:

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Pokud používáte Azure Backup poprvé, zaregistrujte poskytovatele služby Azure Recovery Services s vaším předplatným pomocí rutiny **Register-AzResourceProvider** :

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Ověřte, zda poskytovatelé úspěšně zaregistrovali:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. Ve výstupu příkazu ověřte, že **RegistrationState** změny se **zaregistrují**. Pokud ne, spusťte znovu rutinu **Register-AzResourceProvider** .

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Recovery Services trezor je prostředek Správce prostředků, takže ho musíte umístit do skupiny prostředků. Můžete použít existující skupinu prostředků, nebo můžete vytvořit skupinu prostředků pomocí rutiny **New-AzResourceGroup** . Když vytváříte skupinu prostředků, zadejte její název a umístění.

Pomocí těchto kroků vytvořte Recovery Services trezor:

1. Pokud nemáte existující skupinu prostředků, vytvořte novou pomocí rutiny [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . V tomto příkladu vytvoříme skupinu prostředků v Západní USA oblasti:

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```

1. K vytvoření trezoru použijte rutinu [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) . Zadejte stejné umístění pro trezor, který jste použili pro skupinu prostředků.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

### <a name="view-the-vaults-in-a-subscription"></a>Zobrazení trezorů v předplatném

Pokud chcete zobrazit všechny trezory v rámci předplatného, použijte [příkaz Get-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault):

```powershell
Get-AzRecoveryServicesVault
```

Výstup je podobný následujícímu. Všimněte si, že výstup poskytuje přidruženou skupinu prostředků a umístění.

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

Mnoho rutin Azure Backup vyžaduje jako vstup objekt Recovery Services trezoru, takže je vhodné uložit objekt trezoru do proměnné.

Kontext trezoru představuje typ chráněných dat v trezoru. Nastavte ji pomocí [set-AzRecoveryServicesVaultContext](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext). Po nastavení je kontext použit pro všechny následné rutiny.

Následující příklad nastaví kontext trezoru pro **testvault**:

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Načtení ID trezoru

Plánujeme vyřadit nastavení kontextu trezoru v souladu s pokyny pro Azure PowerShell. Místo toho můžete uložit nebo načíst ID trezoru a předat ho relevantním příkazům. Pokud jste nenastavili kontext trezoru nebo chcete zadat příkaz, který se má spustit pro určitý trezor, předejte ID trezoru pro `-vaultID` všechny relevantní příkazy následujícím způsobem:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>Konfigurace zásady zálohování

Zásady zálohování určují plán zálohování a dobu, po kterou mají být udržovány body obnovení zálohy.

Zásada zálohování je přidružená minimálně k jedné zásadě uchovávání informací. Zásady uchovávání informací definují, jak dlouho je bod obnovení udržován před jeho odstraněním. Zálohy můžete nakonfigurovat na denní, týdenní, měsíční nebo roční uchování.

Tady jsou některé rutiny pro zásady zálohování:

* Prohlédněte si výchozí uchovávání zásad zálohování pomocí [Get-AzRecoveryServicesBackupRetentionPolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject).
* Pomocí [Get-AzRecoveryServicesBackupSchedulePolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject)si prohlédněte výchozí plán zásad zálohování.
* Vytvořte nové zásady zálohování pomocí [New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy). Jako vstup zadáte objekty zásad plánování a uchovávání.

Ve výchozím nastavení je v objektu zásad plánování definován počáteční čas. Pomocí následujícího příkladu změňte čas spuštění na požadovaný čas zahájení. Požadovaný čas spuštění by měl být v univerzálním koordinovaném čase (UTC). V příkladu se předpokládá, že požadovaný počáteční čas je 01:00 UTC pro denní zálohy.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Je nutné zadat počáteční čas pouze do 30 minut. V předchozím příkladu může být pouze "01:00:00" nebo "02:30:00". Počáteční čas nemůže být "01:15:00".

V následujícím příkladu jsou uloženy zásady plánu a zásady uchovávání informací v proměnných. Pak tyto proměnné používá jako parametry pro nové zásady (**NewAFSPolicy**). **NewAFSPolicy** provádí každodenní zálohování a uchovává ho po dobu 30 dnů.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Výstup je podobný tomuto:

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2019 1:30:00 AM
```

## <a name="enable-backup"></a>Povolit zálohování

Po definování zásad zálohování můžete povolit ochranu sdílené složky Azure pomocí zásad.

### <a name="retrieve-a-backup-policy"></a>Načtení zásady zálohování

Příslušný objekt zásad načtete pomocí [Get-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy). Pomocí této rutiny můžete zobrazit zásady spojené s typem úlohy nebo získat konkrétní zásadu.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>Načtěte zásadu pro typ úlohy.

Následující příklad načte zásady pro typ úlohy **AzureFiles**:

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

Výstup je podobný tomuto:

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> Časové pásmo pole **BackupTime** v PowerShellu je v čase UTC. Když se v Azure Portal zobrazí čas zálohování, upraví se čas na vaše místní časové pásmo.

#### <a name="retrieve-a-specific-policy"></a>Načíst konkrétní zásadu

Následující zásada načte zásady zálohování s názvem **dailyafs**:

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-protection-and-apply-the-policy"></a>Povolit ochranu a použít zásady

Ochranu zapněte pomocí [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection). Po přidružení zásady k trezoru se spustí zálohování v souladu s plánem zásad.

Následující příklad povolí ochranu sdílené složky Azure **testAzureFileShare** v účtu úložiště **testStorageAcct** s **dailyafs** zásad:

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

Příkaz čeká na dokončení úlohy konfigurace ochrany a poskytne výstup podobný následujícímu příkladu:

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

Další informace o tom, jak získat seznam sdílených složek pro účet úložiště, najdete v [tomto článku](/powershell/module/az.storage/get-azstorageshare).

## <a name="important-notice-backup-item-identification"></a>Důležité upozornění: identifikace zálohované položky

Tato část popisuje důležitou změnu v zálohování sdílených složek Azure v přípravě na obecnou dostupnost.

Když zapnete zálohování sdílených složek Azure, uživatel udělí zákazníkovi název sdílení souboru jako název entity a vytvoří se zálohovaná položka. Název zálohované položky je jedinečný identifikátor, který služba Azure Backup vytvoří. Identifikátor je obvykle uživatelsky přívětivý název. Pokud ale chcete zpracovat scénář obnovitelného odstranění, kde se dá odstranit sdílená složka, a může se vytvořit jiná sdílená složka se stejným názvem, jedinečná identita sdílené složky Azure je teď ID.

Chcete-li zjistit jedinečné ID každé položky, spusťte příkaz **Get-AzRecoveryServicesBackupItem** s příslušnými filtry pro **backupManagementType** a **WorkloadType** pro získání všech relevantních položek. Pak Sledujte pole název v vráceném objektu nebo odpovědi prostředí PowerShell.

Doporučujeme, abyste v odpovědi vypisovat položky a pak z pole název načetli jejich jedinečný název. Tuto hodnotu použijte k filtrování položek s parametrem *Name* . V opačném případě pomocí parametru *FriendlyName* Načtěte položku s jejím ID.

> [!IMPORTANT]
> Ujistěte se, že je PowerShell upgradovaný na minimální verzi (AZ. RecoveryServices 2.6.0) pro zálohování sdílených složek Azure. V této verzi je k dispozici filtr *FriendlyName* pro příkaz **Get-AzRecoveryServicesBackupItem** .
>
> Předejte název sdílené složky Azure do parametru *FriendlyName* . Pokud předáte název sdílené složky do parametru *Name* , tato verze vyvolá upozornění k předání názvu parametru *FriendlyName* .
>
> Pokud nenainstalujete minimální verzi, může dojít k selhání existujících skriptů. Nainstalujte minimální verzi PowerShellu pomocí následujícího příkazu:
>
>```powershell
>Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
>```

## <a name="trigger-an-on-demand-backup"></a>Aktivace zálohování na vyžádání

Pomocí rutiny [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) spusťte zálohování na vyžádání pro chráněnou sdílenou složku Azure:

1. Načtěte účet úložiště z kontejneru v trezoru, který obsahuje data záloh pomocí [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Pokud chcete spustit úlohu zálohování, Získejte informace o sdílené složce Azure pomocí [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Spusťte zálohování na vyžádání pomocí rutiny [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).

Zálohování na vyžádání spusťte následujícím způsobem:

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -FriendlyName "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

Příkaz vrátí úlohu s ID, které se má sledovat, jak je znázorněno v následujícím příkladu:

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Snímky sdílené složky Azure se používají v době, kdy se provádí zálohování. Úloha je obvykle dokončena podle času, kdy příkaz vrátí tento výstup.

## <a name="next-steps"></a>Další kroky

* Přečtěte si o [zálohování souborů Azure v Azure Portal](backup-afs.md).
* Pokud chcete naplánovat zálohování pomocí sady Runbook Azure Automation, přečtěte si [ukázkový skript na GitHubu](https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup) .
