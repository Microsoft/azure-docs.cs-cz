---
title: Zálohování a obnovení virtuálních počítačů Azure pomocí služby Azure Backup s využitím Powershellu
description: Popisuje, jak zálohovat a obnovovat virtuální počítače Azure pomocí služby Azure Backup s využitím Powershellu
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: raynew
ms.openlocfilehash: 85e7b40778305395bb0f4a9403b4aeafc4607654
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565701"
---
# <a name="back-up-and-restore-azure-vms-with-powershell"></a>Zálohování a obnovení virtuálních počítačů Azure pomocí Powershellu

Tento článek vysvětluje, jak zálohovat a obnovit virtuální počítač v Azure [Azure Backup](backup-overview.md) trezor služby Recovery Services pomocí rutin prostředí PowerShell.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořte trezor služby Recovery Services a nastavte kontext trezoru.
> * Definice zásady zálohování
> * Použití zásady zálohování k ochraně několika virtuálních počítačů
> * Aktivační událost úlohu zálohování na vyžádání pro chráněné virtuální počítače před můžete zálohovat (nebo chránit) virtuální počítač, je třeba provést [požadavky](backup-azure-arm-vms-prepare.md) Příprava prostředí pro ochranu virtuálních počítačů.

## <a name="before-you-start"></a>Než začnete

- [Další informace](backup-azure-recovery-services-vault-overview.md) o trezory služby Recovery Services.
- [Zkontrolujte](backup-architecture.md#architecture-direct-backup-of-azure-vms) architektury pro zálohování virtuálních počítačů Azure [Další informace o](backup-azure-vms-introduction.md) procesu zálohování a [zkontrolujte](backup-support-matrix-iaas.md) podporu, omezení a požadavky.
- Projděte si hierarchie objektů prostředí PowerShell pro služby Recovery Services.

## <a name="recovery-services-object-hierarchy"></a>Hierarchie objektů Recovery Services

V následujícím diagramu je automaticky shrnutý hierarchie objektů.

![Hierarchie objektů Recovery Services](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Zkontrolujte **Az.RecoveryServices** [Reference k rutinám](https://docs.microsoft.com/powershell/module/Az.RecoveryServices/?view=azps-1.4.0) odkaz v knihovně Azure.

## <a name="set-up-and-register"></a>Nastavení a registrace

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Chcete-li začít:

1. [Stáhněte si nejnovější verzi prostředí PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)

2. Vyhledání dostupných rutin Powershellu pro zálohování Azure tak, že zadáte následující příkaz:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

    Zobrazí aliasů a rutin pro trezor služby Recovery Services, Azure Backup a Azure Site Recovery. Na následujícím obrázku je příklad takhle. Není úplný seznam rutin.

    ![seznam služby Recovery Services](./media/backup-azure-vms-automation/list-of-recoveryservices-ps.png)

3. Účet přihlášení pro aplikaci Azure pomocí **připojit AzAccount**. Tato rutina se vyvolá na webové stránce vás vyzve k zadání přihlašovacích údajů k účtu:

    * Alternativně můžete zahrnout přihlašovacích údajů k účtu jako parametr v **připojit AzAccount** rutiny pomocí **– přihlašovací údaje** parametr.
    * Pokud jste partner CSP, který spolupracuje jménem klienta, určení zákazníka jako tenant, pomocí názvu primární doména tenanta nebo ID Tenanta. Příklad: **Připojit AzAccount-Tenant "fabrikam.com"**

4. Přidružte předplatné, které chcete používat s účtem, protože účet může mít několik předplatných:

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. Pokud používáte Azure Backup poprvé, je nutné použít **[Register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)** rutiny ve vašem předplatném zaregistrovat poskytovatele služby Azure Recovery.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Můžete ověřit, že zprostředkovatele úspěšně zaregistrován, pomocí následujících příkazů:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

    Ve výstupu tohoto příkazu **RegistrationState** měli změnit na **registrované**. Pokud ne, stačí spustit **[Register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)** rutinu znovu.


## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Následující kroky vás provedou vytvořením trezor služby Recovery Services. Trezor služby Recovery Services se liší od trezoru služby Backup.

1. Trezor služby Recovery Services je prostředek Resource Manageru, proto musíte umístit do skupiny prostředků. Můžete použít existující skupinu prostředků nebo vytvořte skupinu prostředků pomocí **[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)** rutiny. Při vytváření skupiny prostředků, zadejte název a umístění pro skupinu prostředků.  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```
2. Použití [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/new-azrecoveryservicesvault?view=azps-1.4.0) rutina pro vytvoření trezoru služby Recovery Services. Ujistěte se, k určení stejného umístění trezoru, protože byl použit pro skupinu prostředků.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Zadejte typ redundance úložiště se použije. můžete použít [místně redundantní úložiště (LRS)](../storage/common/storage-redundancy-lrs.md) nebo [geograficky redundantního úložiště (GRS)](../storage/common/storage-redundancy-grs.md). Následující příklad ukazuje, že možnost - BackupStorageRedundancy pro testvault nastavená na GeoRedundant.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperty  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Řada rutin služby Azure Backup vyžaduje jako vstup objekt trezoru služby Recovery Services. Z tohoto důvodu je vhodné uložit objekt trezoru služby Recovery Services do proměnné.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>Zobrazit tyto trezory v rámci předplatného

Chcete-li zobrazit všechny trezorů v předplatném, použijte [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0):

```powershell
Get-AzRecoveryServicesVault
```

Výstup se podobá následujícímu příkladu si všimněte, že jsou k dispozici přidružený název skupiny prostředků a umístění.

```
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```


## <a name="back-up-azure-vms"></a>Zálohování virtuálních počítačů Azure

Ochrana virtuálních počítačů pomocí trezoru služby Recovery Services. Před použitím ochrany nastavte kontext trezoru (typ chráněných dat v trezoru) a ověřte zásady ochrany. Plán spouštění úloh zálohování a jak dlouho se uchovávají každý snímek zálohy, jsou zásady ochrany.

### <a name="set-vault-context"></a>Kontext trezoru set

Než povolíte ochranu virtuálního počítače, použijte [Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0) nastavit kontext trezoru. Po nastavení se kontext trezoru použije pro všechny další rutiny. Následující příklad nastaví kontext trezoru pro trezor, *testvault*.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="modifying-storage-replication-settings"></a>Úprava nastavení replikace úložiště

Použití [Set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/Set-AzRecoveryServicesBackupProperty) příkaz pro nastavení konfigurace úložiště replikace úložiště LRS nebo GRS

```powershell
$vault= Get-AzRecoveryServicesVault -name "testvault"
Set-AzRecoveryServicesBackupProperty -Vault $vault -BackupStorageRedundancy GeoRedundant/LocallyRedundant
```

> [!NOTE]
> Redundance úložiště je možné upravit pouze v případě, že neexistují žádné zálohované položky chráněné do trezoru.

### <a name="create-a-protection-policy"></a>Vytvořit zásady ochrany.

Při vytváření trezoru služby Recovery Services se vytvoří i výchozí zásady ochrany a uchovávání informací. Výchozí zásady ochrany aktivují úlohu zálohování každý den v určenou dobu. Výchozí zásady uchovávání informací uchovávají denní bod obnovení po dobu 30 dnů. Výchozí zásady můžete použít k rychlému zajištění ochrany vašeho virtuálního počítače a upravovat zásady později pomocí různých údajů.

Použití **[Get-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy)** zobrazení zásad ochrany k dispozici v trezoru. Načte konkrétní zásady, nebo pro zobrazení zásad přidružený k typu úlohy, můžete použít tuto rutinu. Následující příklad získá zásady pro typ úlohy, AzureVM.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
```

Výstup se podobá následujícímu příkladu:

```
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> Časové pásmo BackupTime pole v prostředí PowerShell je čas UTC. Když čas zálohování se zobrazí na webu Azure Portal, čas upraveny na místní časové pásmo.
>
>

Zásady zálohování ochrany je přidružená aspoň jednu zásadu uchovávání informací. Zásady uchovávání informací Určuje, jak dlouho bod obnovení je zachována před odstraněním.

- Použití [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject) zobrazení výchozí zásady uchovávání informací.
- Podobně můžete použít [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject) získat výchozí plán zásady.
- [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy) rutina vytvoří objekt prostředí PowerShell, který obsahuje informace o zásadách zálohování.
- Objekty zásad plán a uchovávání se používají jako vstupy do rutiny New-AzRecoveryServicesBackupProtectionPolicy.

Ve výchozím nastavení počáteční čas je definován v objektu zásad plán. Chcete-li změnit čas zahájení pro požadované počáteční čas použijte následující příklad. Požadovaný počáteční čas by měl být ve standardu UTC, také. Následujícím příkladu se předpokládá požadovanou počáteční čas je čas UTC 01:00 AM pro denní zálohy.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

Následující příklad ukládá v proměnné plán zásady a zásady uchovávání informací. V příkladu se používá k definici parametrů při vytváření zásad ochrany těchto proměnných *NewPolicy*.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Výstup se podobá následujícímu příkladu:

```
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### <a name="enable-protection"></a>Povolení ochrany

Jakmile se zásady ochrany, které jste definovali, stále musíte povolit zásady pro položku. Použití [povolit AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) povolit ochranu. Povolení ochrany vyžaduje dva objekty - položka a zásady. Jakmile zásadu přidružená k trezoru, pracovního postupu zálohování se aktivuje v době definovaný v plánu zásady.

Následující příklady zapnout ochranu položky V2VM, pomocí zásad NewPolicy. V příkladech se liší podle toho, jestli je virtuální počítač zašifrovaný a jaký typ šifrování.

Povolení ochrany na **nešifrované virtuální počítače Resource Manageru**:

```powershell
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Pokud chcete povolit ochranu šifrovaných virtuálních počítačů (šifrované pomocí klíče BEK a KEK), je třeba zadat oprávnění služby Azure Backup ke čtení klíče a tajné kódy z trezoru klíčů.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Povolení ochrany na **šifrovaných virtuálních počítačů (šifrované pomocí klíče BEK pouze)** , musí udělit oprávnění služby Azure Backup pro čtení tajných klíčů z trezoru klíčů.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

> [!NOTE]
> Pokud používáte cloud Azure Government, použijte pro parametr ServicePrincipalName ff281ffe-705c-4f53-9f37-a40e6f2c68f3 hodnotu v [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) rutiny.
>

## <a name="monitoring-a-backup-job"></a>Monitorování úlohy zálohování

Dlouhotrvající operace, jako je například úlohy zálohování, můžete sledovat bez použití webu Azure portal. Chcete-li zjistit stav probíhající úlohy, použijte [Get-AzRecoveryservicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) rutiny. Tato rutina načte úlohy zálohování pro konkrétní trezor a tento trezor je zadán v kontext trezoru. Následující příklad získá stav probíhající úlohy jako pole a uloží stav $joblist proměnné.

```powershell
$joblist = Get-AzRecoveryservicesBackupJob –Status "InProgress"
$joblist[0]
```

Výstup se podobá následujícímu příkladu:

```
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016                5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Namísto dotazování těchto úloh pro dokončení – což je zbytečné další kód – použít [čekání AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) rutiny. Tato rutina pozastaví provádění až do dokončení úlohy nebo nebude dosaženo hodnoty zadaný časový limit.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## <a name="manage-azure-vm-backups"></a>Správa záloh virtuálních počítačů Azure

### <a name="modify-a-protection-policy"></a>Upravit zásady ochrany.

Chcete-li upravit zásady ochrany, použijte [Set-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) upravit objekty SchedulePolicy nebo parametru RetentionPolicy.

#### <a name="modifying-scheduled-time"></a>Úprava naplánovaném čase

Když vytvoříte zásady ochrany, je přiřazena čas spuštění ve výchozím nastavení. Následující příklady ukazují, jak změnit čas zahájení zásady ochrany.

````powershell
$SchPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z" (This is the time that the customer wants to start the backup)
$UtcTime = $UtcTime.ToUniversalTime()
$SchPol.ScheduleRunTimes[0] = $UtcTime
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -SchedulePolicy $SchPol
````

#### <a name="modifying-retention"></a>Změna uchovávání

Následující příklad změní až 365 dnů uchování bodu obnovení.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
$retPol.DailySchedule.DurationCountInDays = 365
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol
```

#### <a name="configuring-instant-restore-snapshot-retention"></a>Konfigurace uchovávání rychlé obnovení snímku

> [!NOTE]
> Z PS Az a vyšší verze 1.6.0 jeden můžete aktualizovat doba uchování snímků okamžitá obnova ve zásady pomocí Powershellu

````powershell
PS C:\> $bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
$bkpPol.SnapshotRetentionInDays=7
PS C:\> Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
````

Výchozí hodnota bude 2, může uživatel nastavit hodnotu s minimální hodnotu 1 a maximálně 5. Pro týdenní zálohování zásady, období je nastavena na 5 a nedá se změnit.

### <a name="trigger-a-backup"></a>Aktivujte zálohování

Použití [zálohování AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) k aktivaci úlohy zálohování. Pokud se jedná o prvotní zálohování, je úplná záloha. Následné zálohy trvat, než přírůstková kopie. V následujícím příkladu má virtuální počítač zálohy se uchovávají po dobu 60 dnů.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
$job = Backup-AzRecoveryServicesBackupItem -Item $item -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

Výstup se podobá následujícímu příkladu:

```
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup              InProgress          4/23/2016                  5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> Časové pásmo polí StartTime a EndTime v prostředí PowerShell je čas UTC. Když čas se zobrazí na webu Azure Portal, čas upraveny na místní časové pásmo.
>
>

### <a name="change-policy-for-backup-items"></a>Změna zásad pro zálohování položek

Uživatele můžete upravit existující zásadu nebo změnit zásady zálohovanou položku z Zásada1 na Policy2. Přepnout zásady pro zálohovanou položku, jednoduše načíst příslušné zásady zálohování položek a použít [povolit AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) příkaz zálohovaná položka jako parametr.

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType AzureVM -BackupManagementType AzureVM -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
````

Příkaz počká, až se dokončí konfigurace zálohování a vrátí následující výstup.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
TestVM           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="stop-protection"></a>Zastavení ochrany

#### <a name="retain-data"></a>Zachovat data

Pokud si uživatel přeje ukončit ochranu, můžete použít [zakázat AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS rutiny. Tento kód přestane plánovaného zálohování, ale data zálohována nahoru, až nyní se uchovávají navždy.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>Odstranění zálohovaných dat

Pokud chcete úplně odebrat uložených dat záloh v trezoru, stačí přidat elementy "-RemoveRecoveryPoints' příznak/přepnout na [zakázat ochranu příkaz](#retain-data).

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

## <a name="restore-an-azure-vm"></a>Obnovení virtuálního počítače Azure

Je důležité rozdíl mezi obnovení virtuálního počítače pomocí webu Azure portal a obnovení virtuálního počítače pomocí Powershellu. Pomocí Powershellu operace obnovení byla dokončena, jakmile disky a informace o konfiguraci z bodu obnovení se vytvoří. Operace obnovení není vytvoření virtuálního počítače. K vytvoření virtuálního počítače z disku, naleznete v části [vytvořit virtuální počítač z obnovených disků](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). Pokud nechcete obnovit celý virtuální počítač, ale chcete obnovit nebo obnovení několika souborů ze zálohy virtuálního počítače Azure, přečtěte si [souboru sekce obnovení](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup).

> [!Tip]
> Operace obnovení není vytvoření virtuálního počítače.
>
>

Následující obrázek znázorňuje hierarchii objektů z RecoveryServicesVault dolů BackupRecoveryPoint.

![Hierarchie objektů služby zotavení zobrazující BackupContainer](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Chcete-li obnovit zálohovaná data, identifikujte zálohovaná položka a bod obnovení, který obsahuje data bodu v čase. Použití [obnovení AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) k obnovení dat z trezoru ke svému účtu.

Toto jsou základní kroky k obnovení virtuálního počítače Azure:

* Vyberte virtuální počítač.
* Zvolte bod obnovení.
* Obnovení disků.
* Vytvoření virtuálního počítače z uložené disků.

### <a name="select-the-vm"></a>Vyberte virtuální počítač

Chcete-li získat objekt prostředí PowerShell, který identifikuje přímo zálohovaná položka, spusťte z kontejneru v trezoru a nahlíželi hierarchií objektu. Chcete-li vybrat kontejner, který představuje virtuální počítač, použijte [Get-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) rutiny a kanálem, který se [Get-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) rutiny.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Zvolte bod obnovení

Použití [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) rutiny pro zobrazení seznamu všech bodů obnovení zálohované položky. Zvolte bod obnovení pro obnovení. Pokud si nejste jistí, který bod obnovení používat, je vhodné zvolte nejnovější RecoveryPointType = AppConsistent bod v seznamu.

V následujícím skriptu, proměnné, **$rp**, je pole bodů obnovení pro vybrané záložní položky z posledních sedmi dnů. Pole je v obráceném pořadí řazení čas nabízí nejnovější bod obnovení na pozici 0. Použijte standardní indexování pole prostředí PowerShell a vyberte bod obnovení. V tomto příkladu $rp [0] Vybere poslední bod obnovení.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0]
```

Výstup se podobá následujícímu příkladu:

```powershell
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="restore-the-disks"></a>Obnovit disky

Použití [obnovení AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) rutiny obnovit data zálohovaná položka a konfigurace bodu obnovení. Jakmile identifikujete bod obnovení, použijte jako hodnotu **- RecoveryPoint** parametru. V ukázce výše **$rp [0]** byl bod obnovení použít. V následujícím ukázkovém kódu **$rp [0]** je bod obnovení pro obnovení disku.

Chcete-li obnovit disky a konfigurační informace:

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG"
$restorejob
```

#### <a name="restore-managed-disks"></a>Obnovení spravovaných disků

> [!NOTE]
> Pokud chcete obnovit jako spravovaných disků zálohovaného virtuálního počítače má spravované disky, zavedli jsme možnost z modulu Azure RM Powershellu v 6.7.0. a vyšší
>
>

Zadejte další parametr **TargetResourceGroupName** k určení RG, ke kterému se obnovit spravované disky.

> [!NOTE]
> Důrazně doporučujeme použít **TargetResourceGroupName** parametr pro obnovení spravovaných disků, protože to má za následek výrazné zlepšení výkonu. Navíc z prostředí Azure Powershell Az modulu 1.0 a vyšší, tento parametr je povinný v případě obnovení se spravovanými disky
>
>


```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks"
```

**VMConfig.JSON** soubor se obnoví do účtu úložiště a spravované disky se obnoví do zadané cílové RG.

Výstup se podobá následujícímu příkladu:

```powershell
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Použití [čekání AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) rutiny čekat na dokončení úlohy obnovení.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

Po dokončení úlohy obnovení, použijte [Get-AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) rutiny zobrazíte podrobnosti o operaci obnovení. Vlastnost JobDetails obsahuje informace potřebné k opětovnému sestavení virtuálního počítače.

```powershell
$restorejob = Get-AzRecoveryServicesBackupJob -Job $restorejob
$details = Get-AzRecoveryServicesBackupJobDetails -Job $restorejob
```

Po obnovení disků, přejděte k další části, chcete-li vytvořit virtuální počítač.

## <a name="replace-disks-in-azure-vm"></a>Nahraďte disků ve virtuálním počítači Azure

Pokud chcete nahradit disky a informace o konfiguraci, proveďte následujících kroků:

- Krok 1: [Obnovit disky](backup-azure-vms-automation.md#restore-the-disks)
- Krok 2: [Odpojení datového disku pomocí prostředí PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk#detach-a-data-disk-using-powershell)
- Krok 3: [Připojení datového disku k virtuálnímu počítači Windows pomocí Powershellu](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps)


## <a name="create-a-vm-from-restored-disks"></a>Vytvořte virtuální počítač z obnovených disků

Po obnovení disků použijte následující kroky k vytvoření a konfigurace virtuálního počítače z disku.

> [!NOTE]
> Vytvořit z obnovených disků šifrovaných virtuálních počítačů, Azure role musí mít oprávnění k provedení akce, **Microsoft.KeyVault/vaults/deploy/action**. Pokud vaše role toto oprávnění nemá, vytvořte vlastní roli s touto akcí. Další informace najdete v tématu [vlastní role Azure RBAC](../role-based-access-control/custom-roles.md).
>
>

> [!NOTE]
> Po obnovení disků, teď můžete získat šablonu nasazení, který můžete přímo použít k vytvoření nového virtuálního počítače. Žádné další různé rutiny PS k vytvoření virtuálních počítačů spravované nebo nespravované, které jsou šifrované a nešifrované.

Podrobnosti o výsledné úloze poskytuje šablona identifikátoru URI, které jde dotazovat a nasadit.

```powershell
   $properties = $details.properties
   $templateBlobURI = $properties["Template Blob Uri"]
```

Nasadit šablonu, kterou chcete vytvořit nový virtuální počítač, jak je vysvětleno [tady](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy).

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment ResourceGroupName ExampleResourceGroup -TemplateUri $templateBlobURI -storageAccountType Standard_GRS
```

V následující části jsou uvedené kroky potřebné k vytvoření virtuálního počítače pomocí souboru "Funkce".

> [!NOTE]
> Důrazně doporučujeme použít šablonu nasazení, které jsou podrobně popsané výše vytvořte virtuální počítač. V této části (body 1 až 6) bude brzy přestanou používat.

1. Dotaz na vlastnosti obnoveného disku pro podrobnosti o úloze.

   ```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $configBlobName = $properties["Config Blob Name"]
   ```

2. Nastavit kontext úložiště Azure a obnovení konfiguračního souboru JSON.

   ```powershell
   Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
   $destination_path = "C:\vmconfig.json"
   Get-AzStorageBlobContent -Container $containerName -Blob $configBlobName -Destination $destination_path
   $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
   ```

3. Použití konfiguračního souboru JSON pro vytvoření konfigurace virtuálního počítače.

   ```powershell
   $vm = New-AzVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
   ```

4. Připojte disk s operačním systémem a datové disky. Tento krok obsahuje příklady různých spravované a zašifrovaný konfigurací virtuálních počítačů. Použijte tento příklad, který vyhovuje vaší konfigurace virtuálního počítače.

   * **Virtuální počítače spravované bez a nešifrované** – použijte následující ukázku pro nespravované, bez šifrování virtuálních počítačů.

       ```powershell
       Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
       $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
       foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
       {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
       }
       ```

   * **Nespravovaná a šifrovaných virtuálních počítačů pomocí Azure AD (pouze klíče BEK)** – nespravované, šifrovaných virtuálních počítačů s Azure AD (šifrované pomocí klíče BEK pouze), budete muset obnovit tajný kód k trezoru klíčů, než disky můžete připojit. Další informace najdete v tématu [obnovení šifrovaných virtuálních počítačů z bodu obnovení Azure Backup](backup-azure-restore-key-secret.md). Následující příklad ukazuje, jak připojit operačního systému a datové disky pro šifrované virtuální počítače. Při nastavování disk s operačním systémem, ujistěte se, že jste zmínili odpovídající typ operačního systému.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $dekUrl = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows/Linux
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
      {
       $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

   * **Nespravovaná a šifrovaných virtuálních počítačů pomocí Azure AD (klíče BEK a KEK)** – nespravované, šifrovaných virtuálních počítačů pomocí Azure AD (šifrované pomocí klíče BEK a KEK), obnovit klíč a tajný kód trezoru klíčů před připojením disků. Další informace najdete v tématu [obnovení šifrovaných virtuálních počítačů z bodu obnovení Azure Backup](backup-azure-restore-key-secret.md). Následující příklad ukazuje, jak připojit operačního systému a datové disky pro šifrované virtuální počítače.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
     }
      ```

   * **Nespravovaná a šifrovaných virtuálních počítačů bez Azure AD (pouze klíče BEK)** – nespravované, šifrovaných virtuálních počítačů bez Azure AD (šifrované pomocí klíče BEK pouze), pokud zdroj **keyVault/tajný klíč nejsou k dispozici** obnovit tajné klíče do služby key vault pomocí postupu v [obnovení virtuálního počítače bez šifrování z bodu obnovení Azure Backup](backup-azure-restore-key-secret.md). Potom spusťte tyto skripty pro nastavení šifrování podrobnosti u obnoveného objektu blob operačního systému (Tento krok není povinný pro datový objekt blob). $Dekurl můžete načíst z obnovené trezor klíčů.<br>

   Níže uvedený skript je třeba provést pouze v případě, že trezor klíčů/tajný klíč zdroj není k dispozici.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""}}]}"
      $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
      $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
      $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
      $osBlob.ICloudBlob.SetMetadata()
      ```

    Po **tajné klíče jsou k dispozici** a podrobnosti o šifrování jsou také nastavit u objektu Blob operačního systému, připojení disků pomocí skriptu uvedena níže.<br>

    Pokud se trezor klíčů/tajných klíčů zdroje jsou už k dispozici, nemusí výše skript spustí.

      ```powershell
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

   * **Nespravovaná a šifrovaných virtuálních počítačů bez Azure AD (klíče BEK a KEK)** – nespravované, šifrovaných virtuálních počítačů bez Azure AD (šifrované pomocí klíče BEK a KEK), pokud zdroj **trezor klíčů/klíč/tajný kód nejsou k dispozici** obnovení klíče a tajné klíče trezoru klíčů pomocí postupu v [obnovení virtuálního počítače bez šifrování z bodu obnovení Azure Backup](backup-azure-restore-key-secret.md). Potom spusťte tyto skripty pro nastavení šifrování podrobnosti u obnoveného objektu blob operačního systému (Tento krok není povinný pro datový objekt blob). $Dekurl a $kekurl můžete načíst z obnovené trezor klíčů.

   Níže uvedený skript je třeba provést pouze v případě, že zdrojový trezor klíčů/klíč/tajný klíč není k dispozici.

    ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""},""keyEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""keyUrl"":""$kekUrl""}}]}"
      $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
      $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
      $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
      $osBlob.ICloudBlob.SetMetadata()
      ```
   Po **klíče/tajné klíče jsou k dispozici** a podrobnosti o šifrování jsou nastaveny na objekt Blob operačního systému, připojte disky pomocí skriptu uvedena níže.

    Pokud zdrojový trezor klíčů/klíče/tajné klíče jsou k dispozici, nemusí provést skript výše.

    ```powershell
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

   * **Spravovaný a bez šifrování virtuálního počítače** – spravované nešifrované virtuální počítače, připojit obnovenou spravované disky. Podrobné informace najdete v tématu [připojení datového disku k virtuálnímu počítači s Windows pomocí Powershellu](../virtual-machines/windows/attach-disk-ps.md).

   * **Spravovat a šifrovaných virtuálních počítačů s Azure AD (pouze klíče BEK)** – spravovaných šifrovaných virtuálních počítačů s Azure AD (šifrované pomocí klíče BEK pouze) a připojit obnovenou spravované disky. Podrobné informace najdete v tématu [připojení datového disku k virtuálnímu počítači s Windows pomocí Powershellu](../virtual-machines/windows/attach-disk-ps.md).

   * **Spravovat a šifrovaných virtuálních počítačů s Azure AD (klíče BEK a KEK)** – spravovaných šifrovaných virtuálních počítačů s Azure AD (šifrované pomocí klíče BEK a KEK), připojit obnovenou spravované disky. Podrobné informace najdete v tématu [připojení datového disku k virtuálnímu počítači s Windows pomocí Powershellu](../virtual-machines/windows/attach-disk-ps.md).

   * **Spravovat a šifrovaných virtuálních počítačů bez Azure AD (pouze klíče BEK)** -pro spravované, šifrované virtuální počítače bez Azure AD (šifrované pomocí klíče BEK pouze), pokud zdroj **keyVault/tajný klíč nejsou k dispozici** obnovit tajné klíče pomocí služby key vault Postup v [obnovení virtuálního počítače bez šifrování z bodu obnovení Azure Backup](backup-azure-restore-key-secret.md). Potom spusťte tyto skripty pro nastavení šifrování podrobnosti na obnovený disk s operačním systémem (Tento krok není povinný pro datový disk). $Dekurl můžete načíst z obnovené trezor klíčů.

     Níže uvedený skript je třeba provést pouze v případě, že trezor klíčů/tajný klíč zdroj není k dispozici.  

     ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
      $diskupdateconfig = Set-AzDiskUpdateDiskEncryptionKey -DiskUpdate $diskupdateconfig -SecretUrl $dekUrl -SourceVaultId $keyVaultId  
      Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
      ```

     Poté, co tajné klíče jsou k dispozici a podrobnosti o šifrování jsou nastaveny na disk s operačním systémem, připojit obnovenou spravované disky, najdete v článku [připojení datového disku k virtuálnímu počítači s Windows pomocí Powershellu](../virtual-machines/windows/attach-disk-ps.md).

   * **Spravovat a šifrovaných virtuálních počítačů bez Azure AD (klíče BEK a KEK)** – pro spravované, šifrované virtuální počítače bez Azure AD (šifrované pomocí klíče BEK a KEK), pokud zdroj **trezor klíčů/klíč/tajný kód nejsou k dispozici** obnovení klíče a tajné klíče na klíč pomocí postupu v trezoru [obnovení virtuálního počítače bez šifrování z bodu obnovení Azure Backup](backup-azure-restore-key-secret.md). Potom spusťte tyto skripty pro nastavení šifrování podrobnosti na obnovený disk s operačním systémem (Tento krok není povinný pro datový disk). $Dekurl a $kekurl můžete načíst z obnovené trezor klíčů.

   Níže uvedený skript je třeba provést pouze v případě, že zdrojový trezor klíčů/klíč/tajný klíč není k dispozici.

   ```powershell
     $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
     $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
     $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
     $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
     $diskupdateconfig = Set-AzDiskUpdateDiskEncryptionKey -DiskUpdate $diskupdateconfig -SecretUrl $dekUrl -SourceVaultId $keyVaultId  
     $diskupdateconfig = Set-AzDiskUpdateKeyEncryptionKey -DiskUpdate $diskupdateconfig -KeyUrl $kekUrl -SourceVaultId $keyVaultId  
     Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
    ```

    Poté, co klíče/tajné klíče jsou k dispozici a podrobnosti o šifrování jsou nastaveny na disk s operačním systémem, připojit obnovenou spravované disky, najdete v článku [připojení datového disku k virtuálnímu počítači s Windows pomocí Powershellu](../virtual-machines/windows/attach-disk-ps.md).

5. Nastavení sítě.

    ```powershell
    $nicName="p1234"
    $pip = New-AzPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName "test" -Location "WestUS" -Name "testvNET" -AddressPrefix 10.0.0.0/16
    $virtualNetwork | Set-AzVirtualNetwork
    $vnet = Get-AzVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    $subnetindex=0
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    $vm=Add-AzVMNetworkInterface -VM $vm -Id $nic.Id
    ```

6. Vytvořte virtuální počítač.

    ```powershell  
    New-AzVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

7. Nabízená oznámení ADE rozšíření.

   * **Pro virtuální počítač s Azure AD** – použijte následující příkaz ručně povolit šifrování pro datové disky  

     **Klíč BEK pouze**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

     **Klíč BEK a KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId  -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

   * **Pro virtuální počítač bez Azure AD** – použijte následující příkaz ručně povolit šifrování pro datové disky.

     Pokud incase během provádění příkazu požádá o AADClientID, pak je potřeba aktualizovat prostředí Azure PowerShell.

     **Klíč BEK pouze**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

      **Klíč BEK a KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```


## <a name="restore-files-from-an-azure-vm-backup"></a>Obnovení souborů ze zálohy virtuálního počítače Azure

Kromě obnovení disků, můžete také obnovit jednotlivé soubory ze zálohy virtuálního počítače Azure. Funkce obnovení souborů poskytuje přístup ke všem souborům v bodu obnovení. Správa souborů pomocí Průzkumníka souborů, stejně jako u běžných souborů.

Toto jsou základní kroky obnovení souboru ze zálohy virtuálního počítače Azure:

* Vyberte virtuální počítač
* Zvolte bod obnovení
* Připojte disky bod obnovení
* Zkopírujte požadované soubory
* Odpojení disku

### <a name="select-the-vm"></a>Vyberte virtuální počítač

Chcete-li získat objekt prostředí PowerShell, který identifikuje přímo zálohovaná položka, spusťte z kontejneru v trezoru a nahlíželi hierarchií objektu. Chcete-li vybrat kontejner, který představuje virtuální počítač, použijte [Get-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) rutiny a kanálem, který se [Get-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) rutiny.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Zvolte bod obnovení

Použití [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) rutiny pro zobrazení seznamu všech bodů obnovení zálohované položky. Zvolte bod obnovení pro obnovení. Pokud si nejste jistí, který bod obnovení používat, je vhodné zvolte nejnovější RecoveryPointType = AppConsistent bod v seznamu.

V následujícím skriptu, proměnné, **$rp**, je pole bodů obnovení pro vybrané záložní položky z posledních sedmi dnů. Pole je v obráceném pořadí řazení čas nabízí nejnovější bod obnovení na pozici 0. Použijte standardní indexování pole prostředí PowerShell a vyberte bod obnovení. V tomto příkladu $rp [0] Vybere poslední bod obnovení.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0]
```

Výstup se podobá následujícímu příkladu:

```powershell
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="mount-the-disks-of-recovery-point"></a>Připojte disky bod obnovení

Použití [Get-AzRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprpmountscript) rutiny pro získání skript, který připojí všechny disky bodu obnovení.

> [!NOTE]
> Disky jsou připojené jako disky připojené přes iSCSI k počítači, ve kterém se skript spouští. Připojení začne okamžitě a nebudou účtovat žádné poplatky.
>
>

```powershell
Get-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```

Výstup se podobá následujícímu příkladu:

```powershell
OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```

Spusťte skript na počítači, kam chcete soubory obnovit. Ke spuštění skriptu, je nutné zadat heslo k dispozici. Po disky jsou připojené, pomocí Průzkumníka souborů Windows k nalezení nové svazky a soubory. Další informace najdete v článku zálohování [obnovení souborů ze záloh virtuálních počítačů Azure](backup-azure-restore-files-from-vm.md).

### <a name="unmount-the-disks"></a>Odpojení disků

Po zkopírování požadovaných souborů použijte [zakázat AzRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackuprpmountscript) k odpojení disků. Ujistěte se, že odpojení disků tak přístup k souborům bodu obnovení se odebere.

```powershell
Disable-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```

## <a name="next-steps"></a>Další postup

Pokud chcete spolupracovat s prostředky Azure pomocí Powershellu, najdete v článku prostředí PowerShell, [nasazení a Správa zálohování pro Windows Server](backup-client-automation.md). Pokud budete spravovat zálohy aplikace DPM, najdete v článku, [nasazení a Správa zálohování aplikace DPM](backup-dpm-automation.md).
