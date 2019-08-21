---
title: Zálohování a obnovení souborů Azure pomocí Azure Backup a PowerShellu
description: Zálohujte a obnovte soubory Azure pomocí Azure Backup a PowerShellu.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: dacurwin
ms.reviewer: pullabhk
ms.openlocfilehash: f736d7f1dde8f268033d7c80322b91543672e68f
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69638533"
---
# <a name="back-up-and-restore-azure-files-with-powershell"></a>Zálohování a obnovení souborů Azure pomocí PowerShellu

Tento článek popisuje, jak pomocí Azure PowerShell zálohovat a obnovovat sdílenou složku souborů Azure pomocí trezoru služby [Azure Backup](backup-overview.md) Recovery Services. 

Tento kurz vysvětluje následující postupy:

> [!div class="checklist"]
> * Nastavte PowerShell a zaregistrujte poskytovatele služby Azure Recovery Services.
> * Vytvořte trezor služby Recovery Services.
> * Nakonfigurujte zálohování sdílené složky Azure.
> * Spusťte úlohu zálohování.
> * Obnovte zálohovanou sdílenou složku Azure nebo jednotlivé soubory ze sdílené složky.
> * Monitorujte úlohy zálohování a obnovení.


## <a name="before-you-start"></a>Než začnete

- [Přečtěte si další informace](backup-azure-recovery-services-vault-overview.md) o úložištích Recovery Services.
- Přečtěte si o možnostech verze Preview pro [Zálohování sdílených složek Azure](backup-azure-files.md).
- Zkontrolujte hierarchii objektů PowerShell pro Recovery Services.


## <a name="recovery-services-object-hierarchy"></a>Recovery Services hierarchie objektů

Hierarchie objektů je shrnuta v následujícím diagramu.

![Recovery Services hierarchie objektů](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Přečtěte si referenční informace k rutině **AZ. RecoveryServices** [cmdlet reference](/powershell/module/az.recoveryservices) v knihovně Azure.


## <a name="set-up-and-install"></a>Nastavení a instalace

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Nastavte PowerShell následujícím způsobem:

1. [Stáhněte si nejnovější verzi programu AZ PowerShell](/powershell/azure/install-az-ps). Minimální požadovaná verze je 1.0.0.

2. Pomocí tohoto příkazu Najděte rutiny Azure Backup PowerShellu:

    ```powershell
    Get-Command *azrecoveryservices*
    ```
3. Zkontrolujte aliasy a rutiny pro Azure Backup, Azure Site Recovery a Recovery Services trezor. Tady je příklad toho, co se vám může zobrazit. Nejedná se o úplný seznam rutin.

    ![Seznam rutin Recovery Services](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

3. Přihlaste se ke svému účtu Azure pomocí **Connect-AzAccount**.
4. Na zobrazené webové stránce budete vyzváni k zadání přihlašovacích údajů k účtu.

    - Alternativně můžete zahrnout přihlašovací údaje účtu jako parametr v rutině **Connect-AzAccount** s parametrem **-Credential**.
    - Pokud jste partner CSP jménem tenanta, zadejte zákazníka jako tenanta pomocí názvu primární domény tenantID nebo tenanta. Příkladem je **Connect-AzAccount-Tenant** Fabrikam.com.

4. Přidružte předplatné, které chcete používat s účtem, protože účet může mít několik předplatných.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. Pokud používáte Azure Backup poprvé, zaregistrujte poskytovatele služby Azure Recovery Services s vaším předplatným pomocí rutiny **Register-AzResourceProvider** .

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Ověřte, zda poskytovatelé úspěšně zaregistrovali:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
7. Ve výstupu příkazu ověřte, že **RegistrationState** změny se zaregistrují. Pokud ne, spusťte znovu rutinu **Register-AzResourceProvider** .



## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Pomocí těchto kroků můžete vytvořit trezor Recovery Services.

- Recovery Services trezor je prostředek Správce prostředků, takže ho musíte umístit do skupiny prostředků. Můžete použít existující skupinu prostředků, nebo můžete vytvořit skupinu prostředků pomocí rutiny **New-AzResourceGroup** . Při vytváření skupiny prostředků zadejte název a umístění skupiny prostředků. 

1. Trezor se umístí do skupiny prostředků. Pokud nemáte existující skupinu prostředků, vytvořte novou pomocí [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). V tomto příkladu vytvoříme novou skupinu prostředků v Západní USA oblasti.

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```
2. K vytvoření trezoru použijte rutinu [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) . Zadejte stejné umístění pro trezor, které bylo použito pro skupinu prostředků.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Zadejte typ redundance, který se použije pro úložiště trezoru.

   - Můžete použít [místně redundantní úložiště](../storage/common/storage-redundancy-lrs.md) nebo [geograficky redundantní úložiště](../storage/common/storage-redundancy-grs.md).
   - Následující příklad nastaví možnost **-BackupStorageRedundancy** pro příkaz[set-AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd pro **testvault** nastavenou na geograficky **redundantní**.

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

- Mnoho rutin Azure Backup vyžaduje jako vstup objekt Recovery Services trezoru, takže je vhodné uložit objekt trezoru do proměnné.
- Kontext trezoru představuje typ chráněných dat v trezoru. Nastavte ji pomocí [set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). Po nastavení je kontext použit pro všechny následné rutiny.


Následující příklad nastaví kontext trezoru pro **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Načtení ID trezoru

V souladu s pokyny pro Azure PowerShell plánujeme vyřadit nastavení kontextu trezoru. Místo toho můžete uložit nebo načíst ID trezoru a předat příslušné příkazy následujícím způsobem:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>Konfigurace zásady zálohování

Zásady zálohování určují plán zálohování a dobu, po kterou mají být udržovány body obnovení zálohy:

- Zásada zálohování je přidružená minimálně k jedné zásadě uchovávání informací. Zásady uchovávání informací definují, jak dlouho je bod obnovení udržován před jeho odstraněním.
- Podívejte se na výchozí uchování zásad zálohování pomocí [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
- Seznamte se s výchozím plánem zásad zálohování pomocí [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
-  Pomocí rutiny [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) vytvoříte nové zásady zálohování. Zadáváte objekty zásad plánování a uchovávání dat.

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
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2017 1:30:00 AM
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

## <a name="trigger-an-on-demand-backup"></a>Aktivace zálohování na vyžádání

Pomocí rutiny [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0) spustíte zálohování na vyžádání pro chráněnou sdílenou složku Azure.

1. Načtěte účet úložiště a sdílenou složku z kontejneru v trezoru, který obsahuje data záloh pomocí [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Pokud chcete spustit úlohu zálohování, získáte informace o virtuálním počítači pomocí [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Spusťte zálohování na vyžádání pomocí rutiny[Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).

Zálohování na vyžádání spusťte následujícím způsobem:
    
```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -Name "testAzureFS"
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

Zálohy na vyžádání se dají použít k uchování snímků po dobu 10 let. Plánovače se dají použít ke spouštění skriptů PowerShellu na vyžádání s vybraným uchováváním, takže snímky se v pravidelných intervalech přijímají každý týden, měsíc nebo rok. Při vytváření běžných snímků se řiďte [omezeními zálohování na vyžádání](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#how-many-on-demand-backups-can-i-take-per-file-share-) pomocí služby Azure Backup.

Pokud hledáte ukázkové skripty, můžete se podívat na ukázkový skript na GitHubu (https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup) pomocí Azure Automation Runbooku, který vám umožní pravidelně plánovat zálohy a uchovávat je ještě po dobu 10 let.

### <a name="modify-the-protection-policy"></a>Upravit zásady ochrany

Pokud chcete změnit zásady používané pro zálohování sdílené složky Azure, použijte [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Zadejte příslušnou položku zálohy a novou zásadu zálohování.

Následující příklad změní zásadu ochrany **testAzureFS** z **dailyafs** na **monthlyafs**.

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="restore-azure-file-shares-and-files"></a>Obnovení sdílených složek a souborů Azure

Můžete obnovit celou sdílenou složku nebo konkrétní soubory ve sdílené složce. Můžete obnovit do původního umístění nebo do alternativního umístění. 

### <a name="fetch-recovery-points"></a>Načíst body obnovení

K vypsání všech bodů obnovení pro zálohovanou položku použijte [příkaz Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint?view=azps-1.4.0) .

V následujícím skriptu:

- Proměnná **$RP** je pole bodů obnovení pro vybranou zálohovanou položku z posledních sedmi dnů.
- Pole je seřazené v opačném pořadí s nejnovějším bodem obnovení na indexu **0**.
- Pro výběr bodu obnovení použijte standardní indexování pole v PowerShellu.
- V příkladu **$RP [0]** vybere nejnovější bod obnovení.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

Výstup je podobný následujícímu.

```powershell
FileShareSnapshotUri : https://testStorageAcct.file.core.windows.net/testAzureFS?sharesnapshot=2018-11-20T00:31:04.00000
                       00Z
RecoveryPointType    : FileSystemConsistent
RecoveryPointTime    : 11/20/2018 12:31:05 AM
RecoveryPointId      : 86593702401459
ItemName             : testAzureFS
Id                   : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Micros                      oft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;teststorageRG;testStorageAcct/protectedItems/AzureFileShare;testAzureFS/recoveryPoints/86593702401462
WorkloadType         : AzureFiles
ContainerName        : storage;teststorageRG;testStorageAcct
ContainerType        : AzureStorage
BackupManagementType : AzureStorage
```
Po výběru příslušného bodu obnovení obnovte sdílenou složku nebo soubor do původního umístění nebo do alternativního umístění.

### <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Obnovení sdílené složky Azure do alternativního umístění

K obnovení do vybraného bodu obnovení použijte [příkaz Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) . Zadejte tyto parametry pro identifikaci alternativního umístění: 

- **TargetStorageAccountName**: Účet úložiště, do kterého se obnovil zálohovaný obsah. Cílový účet úložiště musí být ve stejném umístění jako trezor.
- **TargetFileShareName**: Sdílené složky v rámci cílového účtu úložiště, do kterého se obnoví zálohovaný obsah.
- **TargetFolder**: Složka ve sdílené složce, do které se mají obnovit data Pokud bude zálohovaný obsah obnoven do kořenové složky, zadejte hodnoty cílové složky jako prázdný řetězec.
- **ResolveConflict selhalo**: Instrukce, pokud dojde ke konfliktu s obnovenými daty. Přijímá **přepis** nebo **Skip**.

Spusťte rutinu s parametry následujícím způsobem:

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

Příkaz vrátí úlohu s ID, které se má sledovat, jak je znázorněno v následujícím příkladu.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

### <a name="restore-an-azure-file-to-an-alternate-location"></a>Obnovení souboru Azure do alternativního umístění

K obnovení do vybraného bodu obnovení použijte [příkaz Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) . Určete tyto parametry k identifikaci alternativního umístění a k jedinečné identifikaci souboru, který chcete obnovit.

* **TargetStorageAccountName**: Účet úložiště, do kterého se obnovil zálohovaný obsah. Cílový účet úložiště musí být ve stejném umístění jako trezor.
* **TargetFileShareName**: Sdílené složky v rámci cílového účtu úložiště, do kterého se obnoví zálohovaný obsah.
* **TargetFolder**: Složka ve sdílené složce, do které se mají obnovit data Pokud bude zálohovaný obsah obnoven do kořenové složky, zadejte hodnoty cílové složky jako prázdný řetězec.
* **SourceFilePath**: Absolutní cesta k souboru, který má být obnoven ve sdílené složce, jako řetězec. Tato cesta je stejná jako cesta použitá v rutině PowerShellu **Get-AzStorageFile** .
* **SourceFileType**: Zda je vybrán adresář nebo soubor. Přijímá **adresář** nebo **soubor**.
* **ResolveConflict selhalo**: Instrukce, pokud dojde ke konfliktu s obnovenými daty. Přijímá **přepis** nebo **Skip**.

Další parametry (SourceFilePath a SourceFileType) se týkají pouze jednotlivých souborů, které chcete obnovit.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Tento příkaz vrátí úlohu s ID, které se má sledovat, jak je znázorněno v předchozí části.

### <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Obnovení sdílených složek a souborů Azure do původního umístění

Při obnovení do původního umístění nemusíte zadávat parametry související s cíli a cíli. Musí být zadáno pouze **ResolveConflict selhalo** .

#### <a name="overwrite-an-azure-file-share"></a>Přepsání sdílené složky Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Přepsat soubor Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="track-backup-and-restore-jobs"></a>Sledování úloh zálohování a obnovení

Operace zálohování a obnovení na vyžádání vrátí úlohu spolu s ID, jak je znázorněno v případě, že jste [spustili zálohování na vyžádání](#trigger-an-on-demand-backup). Pomocí rutiny [Get-AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-1.4.0) můžete sledovat průběh úloh a podrobnosti.

```powershell
$job = Get-AzRecoveryServicesBackupJob -JobId 00000000-6c46-496e-980a-3740ccb2ad75 -VaultId $vaultID

 $job | fl


IsCancellable        : False
IsRetriable          : False
ErrorDetails         : {Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureFileShareJobErrorInfo}
ActivityId           : 00000000-5b71-4d73-9465-8a4a91f13a36
JobId                : 00000000-6c46-496e-980a-3740ccb2ad75
Operation            : Restore
Status               : Failed
WorkloadName         : testAFS
StartTime            : 12/10/2018 9:56:38 AM
EndTime              : 12/10/2018 11:03:03 AM
Duration             : 01:06:24.4660027
BackupManagementType : AzureStorage

$job.ErrorDetails

 ErrorCode ErrorMessage                                          Recommendations
 --------- ------------                                          ---------------
1073871825 Microsoft Azure Backup encountered an internal error. Wait for a few minutes and then try the operation again. If the issue persists, please contact Microsoft support.
```
## <a name="next-steps"></a>Další kroky
[Přečtěte si o](backup-azure-files.md) zálohování souborů Azure v Azure Portal.
