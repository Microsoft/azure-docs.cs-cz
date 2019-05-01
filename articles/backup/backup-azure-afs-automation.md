---
title: Zálohovat a obnovovat soubory Azure pomocí Powershellu a Azure Backup
description: Zálohovat a obnovovat soubory Azure pomocí Powershellu a Azure Backup.
author: pvrk
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: pullabhk
ms.openlocfilehash: 46719d3f72c57a44fb48489891e2348bb418da1e
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64918953"
---
# <a name="back-up-and-restore-azure-files-with-powershell"></a>Zálohovat a obnovovat soubory Azure pomocí Powershellu

Tento článek popisuje, jak pomocí Azure Powershellu k zálohování a obnovení sdílené složky souboru soubory Azure pomocí [Azure Backup](backup-overview.md) trezor služby Recovery Services. 

Tento kurz vysvětluje následující postupy:

> [!div class="checklist"]
> * Nastavení prostředí PowerShell a zaregistrujte zprostředkovatele služby Azure Recovery Services.
> * Vytvořte trezor služby Recovery Services.
> * Konfigurace zálohování pro sdílenou složku Azure.
> * Spuštění úlohy zálohování.
> * Obnovení zálohovaného až sdílené složky Azure nebo jednotlivých souborů ze sdílené složky.
> * Monitorovat zálohování a obnovení úloh.


## <a name="before-you-start"></a>Než začnete

- [Další informace](backup-azure-recovery-services-vault-overview.md) o trezory služby Recovery Services.
- Přečtěte si informace o možnosti ve verzi preview pro [zálohování sdílených složek Azure](backup-azure-files.md).
- Projděte si hierarchie objektů prostředí PowerShell pro služby Recovery Services.


## <a name="recovery-services-object-hierarchy"></a>Hierarchie objektů Recovery Services

V následujícím diagramu je automaticky shrnutý hierarchie objektů.

![Hierarchie objektů Recovery Services](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Zkontrolujte **Az.RecoveryServices** [Reference k rutinám](/powershell/module/az.recoveryservices) odkaz v knihovně Azure.


## <a name="set-up-and-install"></a>Nastavení a instalace

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Prostředí PowerShell nastavte následujícím způsobem:

1. [Stáhněte si nejnovější verzi prostředí PowerShell Az](/powershell/azure/install-az-ps). Minimální požadovaná verze je 1.0.0.

2. Vyhledání rutin Powershellu pro zálohování Azure pomocí tohoto příkazu:

    ```powershell
    Get-Command *azrecoveryservices*
    ```
3. Projděte si aliasů a rutin pro službu Azure Backup, Azure Site Recovery a trezor služby Recovery Services se zobrazí. Tady je příklad co můžete se setkat. Není úplný seznam rutin.

    ![Seznam rutin služby Recovery Services](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

3. Přihlaste se ke svému účtu Azure pomocí **připojit AzAccount**.
4. Na webové stránce, která se zobrazí budete vyzváni k zadání přihlašovacích údajů k účtu.

    - Alternativně můžete zahrnout přihlašovacích údajů k účtu jako parametr v **AzAccount připojit** rutinu s **-Credential**.
    - Pokud jste partner CSP, který spolupracuje jménem klienta, určení zákazníka jako tenant, pomocí názvu primární doména tenanta nebo ID Tenanta. Příkladem je **Connect AzAccount-Tenanta** fabrikam.com.

4. Přidružte předplatné, které chcete používat s účtem, protože účet může mít několik předplatných.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. Pokud používáte Azure Backup poprvé, použijte **Register-AzResourceProvider** rutiny zaregistrujte zprostředkovatele služby Azure Recovery Services s vaším předplatným.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Ověření se úspěšně zaregistrovala zprostředkovatele:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
7. Ve výstupu příkazu ověřte, že **RegistrationState** změny **registrované**. Pokud ne, spusťte **Register-AzResourceProvider** rutinu znovu.



## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Postupujte podle těchto kroků a vytvořte trezor služby Recovery Services.

- Trezor služby Recovery Services je prostředek Resource Manageru, proto je nutné umístit v rámci skupiny prostředků. Můžete použít existující skupinu prostředků, nebo můžete vytvořit skupinu prostředků pomocí **New-AzResourceGroup** rutiny. Když vytvoříte skupinu prostředků, zadejte název a umístění pro skupinu prostředků. 

1. Trezor je umístěn ve skupině prostředků. Pokud nemáte existující prostředek skupiny, vytvořte si novou [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). V tomto příkladu vytvoříme novou skupinu prostředků v oblasti západní USA.

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```
2. Použití [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) rutina pro vytvoření trezoru. Určení stejného umístění trezoru, protože byl použit pro skupinu prostředků.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Zadejte typ redundance úložiště trezoru používat.

   - Můžete použít [místně redundantní úložiště](../storage/common/storage-redundancy-lrs.md) nebo [geograficky redundantní úložiště](../storage/common/storage-redundancy-grs.md).
   - Následující příklad nastaví **- BackupStorageRedundancy** možnost[Set-AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperties?view=azps-1.4.0) cmd pro **testvault** nastavena na  **GeoRedundant**.

     ```powershell
     $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
     Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
     ```

### <a name="view-the-vaults-in-a-subscription"></a>Zobrazit tyto trezory v rámci předplatného

Chcete-li zobrazit všechny trezorů v předplatném, použijte [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

Výstup je podobný následujícímu. Všimněte si, že jsou k dispozici přidružené skupině prostředků a umístění.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>Nastavte kontext trezoru

Store objekt trezoru v proměnné a nastavte kontext trezoru.

- Řada rutin služby Azure Backup vyžadují objekt trezoru služby Recovery Services jako vstup, proto je vhodné uložit objekt trezoru v proměnné.
- Kontext trezoru představuje typ chráněných dat v trezoru. Nastavte ho pomocí [Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). Po nastavení kontextu platí pro všechny další rutiny.


Následující příklad nastaví kontext trezoru pro **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Načtení ID trezoru

Plánujeme ukončení podpory pro kontext trezoru nastavení v souladu s pokyny pro prostředí Azure PowerShell. Místo toho můžete ukládat nebo načíst ID trezoru a předejte jej do příslušné příkazy, následujícím způsobem:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>Nakonfigurujte zásady zálohování

Zásady zálohování určují plán zálohování a jak dlouho se body obnovení záloh se uchovávají:

- Zásady zálohování je přidružená aspoň jednu zásadu uchovávání informací. Zásady uchovávání informací Určuje, jak dlouho bod obnovení je zachována před odstraněním.
- Zobrazení výchozí zásady zálohování uchovávání informací pomocí [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
- Zobrazení výchozí zásady zálohování plán pomocí [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
-  Můžete použít [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) rutina pro vytvoření nové zásady zálohování. Můžete zadat plán a uchovávání objekty zásad.

Následující příklad ukládá v proměnné plán zásady a zásady uchovávání informací. Poté použije tyto proměnné jako parametry pro novou zásadu (**NewAFSPolicy**). **NewAFSPolicy** trvá denní zálohování a uchovávají po dobu 30 dnů.

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

Po definování zásad zálohování můžete povolit ochranu pro sdílenou složku Azure pomocí zásad.

### <a name="retrieve-a-backup-policy"></a>Načíst zásady zálohování

Načtení objektu příslušných zásad s [Get-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0). Načte konkrétní zásady, nebo pro zobrazení zásad přidružený k typu úlohy, použijte tuto rutinu.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>Načtení zásad pro typ úlohy

Následující příklad načte zásad pro typ úlohy **AzureFiles**.

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
> Časové pásmo **BackupTime** pole v prostředí PowerShell je koordinovaný světový čas (UTC). Když čas zálohování se zobrazí na webu Azure Portal, čas se upraví na místním časovém pásmu.

### <a name="retrieve-a-specific-policy"></a>Načtení určité zásady

Tyto zásady načte zásady zálohování s názvem **dailyafs**.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-backup-and-apply-policy"></a>Povolit zálohování a použít zásady

Povolit ochranu s [povolit AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Poté, co je zásada přidružená k trezoru, zálohování se spouštějí podle plánu zásad.

Následující příklad povolí ochranu pro sdílené složky Azure **testAzureFileShare** v účtu úložiště **testStorageAcct**, se zásadami **dailyafs**.

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

Příkaz počká, až úloha Konfigurace ochrany je dokončená a nabízí podobný výstup, jak je znázorněno.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

## <a name="trigger-an-on-demand-backup"></a>Spustit zálohu na vyžádání

Použití [zálohování AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0) spustit zálohu na vyžádání pro chráněné sdílené složky Azure.

1. Načíst účet úložiště a sdílených složek v kontejneru v trezoru, který uchovává vaše zálohovaná data s [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Spustit úlohu zálohování, získejte informace o virtuálním počítači s [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Spustit zálohu na vyžádání s[zálohování AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).

Následujícím způsobem spusťte zálohování na vyžádání:
    
```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -Name "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

Příkaz vrátí úlohu s ID, které má sledovat, jak je znázorněno v následujícím příkladu.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Snímky sdílené složky Azure se používají při zálohy jsou prováděny, takže obvykle dokončení úlohy podle času, příkaz vrátí tento výstup.

### <a name="modify-the-protection-policy"></a>Upravit zásady ochrany

Chcete-li změnit zásady používané pro zálohování sdílené složky Azure, použijte [povolit AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Zadejte relevantní zálohovaná položka a nové zásady zálohování.

Následující příklad změny **testAzureFS** zásady ochrany z **dailyafs** k **monthlyafs**.

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="restore-azure-file-shares-and-files"></a>Obnovení sdílené složky Azure a souborů

Můžete obnovit celou sdílenou nebo konkrétní soubory ve sdílené složce. Můžete obnovit do původního umístění nebo do alternativního umístění. 

### <a name="fetch-recovery-points"></a>Načíst body obnovení

Použití [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint?view=azps-1.4.0) k výpisu všech bodů obnovení pro zálohovaná položka.

V následujícím skriptu:

- Proměnná **$rp** je pole bodů obnovení pro vybrané záložní položky z posledních sedmi dnů.
- Pole má řazení proběhnout v obráceném pořadí času s nejnovějším dostupným bodem obnovení v indexu **0**.
- Použijte standardní indexování pole prostředí PowerShell a vyberte bod obnovení.
- V tomto příkladu **$rp [0]** Vybere poslední bod obnovení.

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
Po výběru příslušné obnovení bodu obnovení sdílené složky nebo souboru do původního umístění nebo do alternativního umístění.

### <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Obnovení do alternativního umístění sdílené složky Azure

Použití [obnovení AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) k obnovení vybraného bodu obnovení. Zadejte parametry k určení alternativního umístění: 

- **TargetStorageAccountName**: Účet úložiště, ke kterému je obnovit zálohovaná obsah. Cílový účet úložiště musí být ve stejném umístění jako trezor.
- **TargetFileShareName**: Sdílené složky v rámci cílového úložiště účtu zálohovanou obsah je obnovit.
- **TargetFolder**: Složka, v rámci sdílené složky, do kterého se data obnovit. Pokud se zálohovaná obsahu je možné obnovit do kořenové složky, poskytují cílové složky hodnoty jako prázdný řetězec.
- **ResolveConflict**: Instrukce, pokud dojde ke konfliktu s obnovená data. Přijímá **přepsat** nebo **přeskočit**.

Spusťte rutinu s parametry následujícím způsobem:

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

Příkaz vrátí úlohu s ID, které má sledovat, jak je znázorněno v následujícím příkladu.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

### <a name="restore-an-azure-file-to-an-alternate-location"></a>Obnovení do alternativního umístění Azure file

Použití [obnovení AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) k obnovení vybraného bodu obnovení. Zadejte parametry k určení alternativního umístění a k jednoznačné identifikaci souboru, který chcete provést obnovení.

* **TargetStorageAccountName**: Účet úložiště, ke kterému je obnovit zálohovaná obsah. Cílový účet úložiště musí být ve stejném umístění jako trezor.
* **TargetFileShareName**: Sdílené složky v rámci cílového úložiště účtu zálohovanou obsah je obnovit.
* **TargetFolder**: Složka, v rámci sdílené složky, do kterého se data obnovit. Pokud se zálohovaná obsahu je možné obnovit do kořenové složky, poskytují cílové složky hodnoty jako prázdný řetězec.
* **SourceFilePath**: Absolutní cesta souboru pro obnovení ve sdílené složce, jako řetězec. Tato cesta je stejnou cestu používané **Get-AzStorageFile** rutiny Powershellu.
* **SourceFileType**: Určuje, zda je vybrána adresář nebo soubor. Přijímá **Directory** nebo **souboru**.
* **ResolveConflict**: Instrukce, pokud dojde ke konfliktu s obnovená data. Přijímá **přepsat** nebo **přeskočit**.

Další parametry (cestakezdrojovemusouboru a SourceFileType) se vztahují pouze k jednotlivých souborů, které chcete obnovit.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Tento příkaz vrátí úlohu s ID, které má sledovat, jak je znázorněno v předchozí části.

### <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Obnovení sdílené složky Azure a soubory do původního umístění

Při obnovení do původního umístění není nutné zadat související cílové a cílové parametry. Pouze **ResolveConflict** musí být zadaná.

#### <a name="overwrite-an-azure-file-share"></a>Přepsat sdílené složky Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Přepsat soubor v Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="track-backup-and-restore-jobs"></a>Přehled zálohování a obnovení úloh

Operace zálohování a obnovení na vyžádání vrací úlohu spolu s ID, jak je znázorněno, kdy jste [spustili zálohu na vyžádání](#trigger-an-on-demand-backup). Použití [Get-AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-1.4.0) rutiny můžete sledovat průběh úlohy a podrobnosti.

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
## <a name="next-steps"></a>Další postup
[Další informace o](backup-azure-files.md) zálohování souborů Azure na webu Azure Portal.
