---
title: Nasazení a správa záloh sdílených složek Azure pomocí Powershellu
description: Nasazení a správa záloh sdílených složek Azure v Azure pomocí Powershellu
services: backup
author: pvrk
manager: shivamg
keywords: PowersShell; Zálohování souborů Azure; Obnovení se soubory Azure;
ms.service: backup
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: pullabhk
ms.assetid: 80da8ece-2cce-40dd-8dce-79960b6ae073
ms.openlocfilehash: 30fc36f29a7602e2bc3f192b445474bfc50e9434
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53632631"
---
# <a name="use-powershell-to-back-up-and-restore-azure-file-shares"></a>Použití Powershellu k zálohování a obnovení sdílených složek Azure

Tento článek popisuje, jak pomocí rutin Azure Powershellu k zálohování a obnovení sdílené složky Azure pomocí trezoru služby Recovery Services. Trezor služby Recovery Services je prostředek Azure Resource Manageru použít k ochraně dat a assetů ve službě Azure Backup a Azure Site Recovery services.

## <a name="concepts"></a>Koncepty

Pokud nejste obeznámeni se službou Azure Backup, získáte přehled o službě, přečtěte si článek, [co je Azure Backup?](backup-introduction-to-azure-backup.md). Než začnete, ujistěte se berou na vědomí možnosti ve verzi preview zálohování sdílených složek Azure zdokumentované [tady](backup-azure-files.md).

Jak efektivně pomocí prostředí PowerShell, je nezbytné pro zjištění hierarchie objektů a ze které se mají spustit.

![Hierarchie objektů Recovery Services](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Reference k rutinám Powershellu AzureRm.RecoveryServices.Backup, naleznete v tématu [Azure Backup – rutiny služby zotavení](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup) v knihovně Azure.

## <a name="setup-and-registration"></a>Instalace a registrace

> [!NOTE]
> Jak je uvedeno [tady](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.13.0), podporu pro nové funkce v modulu AzureRM endové systémy v listopadu 2018. Proto poskytujeme podporu pro zálohování sdílených složek Azure pomocí nové "Az" PS modulu nyní v obecné dostupnosti.

Chcete-li začít:

1. [Stáhněte si nejnovější verzi prostředí PowerShell "Az"](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azurermps-6.13.0) (minimální požadovaná verze je: 1.0.0)

2. Vyhledání dostupných rutin Powershellu pro zálohování Azure tak, že zadáte následující příkaz:

    ```powershell
    Get-Command *azrecoveryservices*
    ```
    Zobrazí aliasů a rutin pro trezor služby Recovery Services, Azure Backup a Azure Site Recovery. Na následujícím obrázku je příklad takhle. Není úplný seznam rutin.

    ![seznam služby Recovery Services](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

3. Účet přihlášení pro aplikaci Azure pomocí **připojit AzAccount**. Tato rutina se vyvolá na webové stránce vás vyzve k zadání přihlašovacích údajů k účtu:

    * Alternativně můžete zahrnout přihlašovacích údajů k účtu jako parametr v **připojit AzAccount** rutiny pomocí **– přihlašovací údaje** parametr.
    * Pokud jste partner CSP, který spolupracuje jménem klienta, určení zákazníka jako tenant, pomocí názvu primární doména tenanta nebo ID Tenanta. Příklad: **Připojit AzAccount-Tenant "fabrikam.com"**

4. Přidružte předplatné, které chcete používat s účtem, protože účet může mít několik předplatných:

    ```powershell
    Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

5. Pokud používáte Azure Backup poprvé, je nutné použít **Register-AzResourceProvider** rutiny ve vašem předplatném zaregistrovat poskytovatele služby Azure Recovery.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Můžete ověřit, že zprostředkovatele úspěšně zaregistrován, pomocí následujících příkazů:
    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
    Ve výstupu tohoto příkazu **RegistrationState** měli změnit na **registrované**. Pokud ne, stačí spustit **Register-AzResourceProvider** rutinu znovu.

Tyto úlohy je možné automatizovat pomocí prostředí PowerShell:

* Vytvoření trezoru Služeb zotavení
* Konfigurace zálohování sdílených složek Azure
* Aktivace úlohy zálohování
* Monitorování úlohy zálohování
* Obnovení sdílené složky Azure
* Obnovení jednotlivých souborů Azure ze sdílené složky Azure

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Následující kroky vás provedou vytvořením trezor služby Recovery Services.

1. Trezor služby Recovery Services je prostředek Resource Manageru, proto musíte umístit do skupiny prostředků. Můžete použít existující skupinu prostředků nebo vytvořte skupinu prostředků pomocí **New-AzResourceGroup** rutiny. Při vytváření skupiny prostředků, zadejte název a umístění pro skupinu prostředků.  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```
2. Použití **New-AzRecoveryServicesVault** rutina pro vytvoření trezoru služby Recovery Services. Ujistěte se, k určení stejného umístění trezoru, protože byl použit pro skupinu prostředků.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Zadejte typ redundance úložiště se použije. můžete použít [místně redundantní úložiště (LRS)](../storage/common/storage-redundancy-lrs.md) nebo [geograficky redundantního úložiště (GRS)](../storage/common/storage-redundancy-grs.md). Následující příklad ukazuje, že možnost - BackupStorageRedundancy pro testvault nastavená na GeoRedundant.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Zobrazit tyto trezory v rámci předplatného

Chcete-li zobrazit všechny trezorů v předplatném, použijte **Get-AzRecoveryServicesVault**:

```powershell
Get-AzRecoveryServicesVault
```

Výstup se podobá následujícímu příkladu si všimněte, že jsou k dispozici přidružený název skupiny prostředků a umístění.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

Řada rutin služby Azure Backup vyžaduje jako vstup objekt trezoru služby Recovery Services.

Použití **Set-AzRecoveryServicesVaultContext** nastavit kontext trezoru. Po nastavení se kontext trezoru použije pro všechny další rutiny. Následující příklad nastaví kontext trezoru pro trezor, *testvault*.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

> [!NOTE]
> Plánujeme přestat používat nastavení kontextu trezoru podle pokynů prostředí Azure PowerShell. Místo toho doporučujeme uživatelům předat ID trezoru, jak je uvedeno níže

Alternativně je můžete úložiště/načítání ID trezoru, ke které chcete provést operaci prostředí PowerShell a předat ho na příslušný příkaz.

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-backup-for-an-azure-file-share"></a>Konfigurace zálohování pro sdílené složky Azure

### <a name="create-protection-policy"></a>Vytvoření zásady ochrany

Zásady zálohování ochrany je přidružená aspoň jednu zásadu uchovávání informací. Zásady uchovávání informací Určuje, jak dlouho bod obnovení je zachována před odstraněním. Použití **Get-AzRecoveryServicesBackupRetentionPolicyObject** zobrazení výchozí zásady uchovávání informací.  Podobně můžete použít **Get-AzRecoveryServicesBackupSchedulePolicyObject** získat výchozí plán zásady. **New-AzRecoveryServicesBackupProtectionPolicy** rutina vytvoří objekt prostředí PowerShell, který obsahuje informace o zásadách zálohování. Objekty zásad plán a uchovávání se používají jako vstupy **New-AzRecoveryServicesBackupProtectionPolicy** rutiny. Následující příklad ukládá v proměnné plán zásady a zásady uchovávání informací. V příkladu se používá k definici parametrů při vytváření zásad ochrany těchto proměnných *NewPolicy*.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Výstup se podobá následujícímu příkladu:

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2017 1:30:00 AM
```

"NewAFSPolicy" trvá denní zálohování a uchovávají po dobu 30 dnů.

### <a name="enable-protection"></a>Povolení ochrany

Jakmile se zásady ochrany, které jste definovali, můžete povolit ochranu pro sdílenou složku Azure s touto zásadou.

Nejdřív načíst objekt příslušné zásady s **Get-AzRecoveryServicesBackupProtectionPolicy** rutiny. Načte konkrétní zásady, nebo pro zobrazení zásad přidružený k typu úlohy, můžete použít tuto rutinu.

Následující příklad získá zásady pro typ úlohy, AzureFiles.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

Výstup se podobá následujícímu příkladu:

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> Časové pásmo BackupTime pole v prostředí PowerShell je čas UTC. Když čas zálohování se zobrazí na webu Azure Portal, čas upraveny na místní časové pásmo.
>
>

Tyto zásady načte zásady zálohování s názvem jako "dailyafs"

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

Použití **povolit AzRecoveryServicesBackupProtection** k povolení ochrany položka s daným zásadám. Jakmile zásadu přidružená k trezoru, pracovního postupu zálohování se aktivuje v době definovaný v plánu zásady.

Následující příklad povolí ochranu pro sdílenou složku Azure, "testAzureFileShare", v části úložiště účtu "testStorageAcct", s dailyafs zásad.

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

Příkaz počká, až se dokončí úloha Konfigurace ochrany a nabízí podobný výstup, jak je znázorněno níže.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

### <a name="trigger-an-on-demand-backup"></a>Spustit zálohu na vyžádání

Použití **zálohování AzRecoveryServicesBackupItem** k aktivaci úlohy zálohování pro chráněný sdílenou složku Azure. Načíst úložiště souborů a sdílené složky v rámci ho pomocí následujících příkazů a spustit zálohu na vyžádání.

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -Name "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

Příkaz vrátí úlohu, která bude sledovat pomocí ID jako v následujícím příkladu.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Jsme snímky sdílené složky Azure využívají přitom zálohy a proto obvykle dokončení úlohy v době, kdy příkaz vrátí tento výstup

### <a name="modify-protection-policy"></a>Upravit zásady ochrany

Pokud chcete změnit zásady s, který je chráněn sdílenou složku Azure, použijte **povolit AzRecoveryServicesBackupProtection** relevantní zálohovaná položka a nové zásady ochrany.

Následující příklady změní zásady ochrany "testAzureFS" z "dailyafs" k "monthlyafs"

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="restore-azure-file-sharesazure-files"></a>Obnovení sdílené položky souboru Azure / Azure soubory

Můžete obnovit celou sdílenou do jeho původního nebo alternativního umístění. Jednotlivé soubory ze sdílené souborové podobně je možné obnovit také.

### <a name="fetching-recovery-points"></a>Načítají se body obnovení

Použití **Get-AzRecoveryServicesBackupRecoveryPoint** rutiny pro zobrazení seznamu všech bodů obnovení zálohované položky. V následujícím skriptu, proměnné, **$rp**, je pole bodů obnovení pro vybrané záložní položky z posledních sedmi dnů. Pole je v obráceném pořadí řazení čas nabízí nejnovější bod obnovení na pozici 0. Použijte standardní indexování pole prostředí PowerShell a vyberte bod obnovení. V tomto příkladu $rp [0] Vybere poslední bod obnovení.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

Výstup se podobá následujícímu příkladu:

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

Po výběru bodu obnovení odpovídající pokračujte obnovit sdílenou složku nebo soubor do umístění alternativní/původní, jak je popsáno níže.

### <a name="restore-azure-file-shares-to-an-alternate-location"></a>Obnovení do alternativního umístění sdílené složky Azure

#### <a name="restoring-an-azure-file-share"></a>Obnovení sdílené složky Azure

Určete alternativní umístění tím, že poskytuje následující informace:

* ***TargetStorageAccountName***: Účet úložiště, do kterého se obnoví zálohovaný obsah. Cílový účet úložiště by měl být ve stejném umístění jako trezor.
* ***TargetFileShareName***: Sdílené složky v účtu cílového úložiště, do kterého se obnoví zálohovaný obsah
* ***TargetFolder***: Složka, v rámci sdílené složky, do kterého se data obnovit. Když zálohovaný obsah by měl obnovit do kořenové složky, zadejte hodnoty cílové složky jako prázdný řetězec
* ***ResolveConflict***: Instrukce v případě konfliktu s obnovená data. Přijímá "Přepsat" nebo "Přeskočit"

Poskytuje tyto parametry pro příkaz restore Obnovení zálohovaného sdílené složky do alternativního umístění.

````powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
````

Příkaz vrátí úlohu s ID, které má sledovat, jak je znázorněno v následujícím příkladu.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
````

#### <a name="restoring-an-azure-file"></a>Obnovení Azure file

V případě budete chtít obnovení jednotlivých souborů místo celou sdílenou složku, by měl jednotlivých souborů jednoznačně identifikují tím, že poskytuje následující parametry.

* ***TargetStorageAccountName***: Účet úložiště, do kterého se obnoví zálohovaný obsah. Cílový účet úložiště by měl být ve stejném umístění jako trezor.
* ***TargetFileShareName***: Sdílené složky v účtu cílového úložiště, do kterého se obnoví zálohovaný obsah
* ***TargetFolder***: Složka, v rámci sdílené složky, do kterého se data obnovit. Když zálohovaný obsah by měl obnovit do kořenové složky, zadejte hodnoty cílové složky jako prázdný řetězec
* ***Cestakezdrojovemusouboru***: Absolutní cesta souboru pro obnovení ve sdílené složce, jako řetězec. Jedná se o stejnou cestu používané ```Get-AzStorageFile``` PS rutiny.
* ***SourceFileType***: Určuje, zda je vybrána adresář nebo soubor. Přijímá "Directory" nebo "File"
* ***ResolveConflict***: Instrukce v případě konfliktu s obnovená data. Přijímá "Přepsat" nebo "Přeskočit"

Jak je vidět další parametry se vztahují pouze k jednotlivých souborů pro obnovení.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Také vrátí úlohu, která bude sledovat pomocí ID, jak je znázorněno výše.

### <a name="restore-azure-file-shares-to-original-location"></a>Obnovit do původního umístění sdílené složky Azure

V případě obnovení původního umístění všechny cílového související s parametry není nutné zadávat. Pouze ```ResolveConflict``` musí být k dispozici

#### <a name="overwrite-an-azure-file-share"></a>Přepsat sdílené složky Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Přepsat soubor v Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="track-backup-and-restore-jobs"></a>Přehled zálohování a obnovení úloh

Operace zálohování a obnovení na vyžádání vrací úlohu spolu s ID, jak je znázorněno [nad](#trigger-an-on-demand-backup). Použití ```Get-AzRecoveryServicesBackupJobDetails``` rutiny sledovat průběh úlohy a načíst další podrobnosti.

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
1073871825 Microsoft Azure Backup encountered an internal error. Wait for a few minutes and then try the operation again. If the issue persists, please contact Microsoft support
```
