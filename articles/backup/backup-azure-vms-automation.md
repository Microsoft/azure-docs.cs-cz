---
title: Zálohování a obnovení virtuálních počítačů Azure pomocí Azure Backup pomocí prostředí PowerShell
description: Popisuje postup zálohování a obnovení virtuálních počítačů Azure pomocí Azure Backup pomocí prostředí PowerShell.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: dacurwin
ms.openlocfilehash: 1cbd0f649bd5e89c1ed424604697afa179964175
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68689007"
---
# <a name="back-up-and-restore-azure-vms-with-powershell"></a>Zálohování a obnovení virtuálních počítačů Azure pomocí PowerShellu

Tento článek vysvětluje, jak zálohovat a obnovit virtuální počítač Azure ve službě [Azure Backup](backup-overview.md) Recovery Services trezoru pomocí rutin PowerShellu.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořte Trezor Recovery Services a nastavte kontext trezoru.
> * Definice zásady zálohování
> * Použití zásady zálohování k ochraně několika virtuálních počítačů
> * Aktivujte úlohu zálohování na vyžádání pro chráněné virtuální počítače, abyste mohli zálohovat virtuální počítač (nebo ho chránit), musíte splnit [předpoklady](backup-azure-arm-vms-prepare.md) pro přípravu vašeho prostředí pro ochranu vašich virtuálních počítačů.

## <a name="before-you-start"></a>Než začnete

- [Přečtěte si další informace](backup-azure-recovery-services-vault-overview.md) o úložištích Recovery Services.
- [Projděte si](backup-architecture.md#architecture-direct-backup-of-azure-vms) architekturu zálohování virtuálních počítačů Azure, [Zjistěte informace o](backup-azure-vms-introduction.md) procesu zálohování a Projděte [si](backup-support-matrix-iaas.md) podporu, omezení a požadavky.
- Zkontrolujte hierarchii objektů PowerShell pro Recovery Services.

## <a name="recovery-services-object-hierarchy"></a>Recovery Services hierarchie objektů

Hierarchie objektů je shrnuta v následujícím diagramu.

![Recovery Services hierarchie objektů](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Přečtěte si referenční informace k rutině **AZ. RecoveryServices** [](https://docs.microsoft.com/powershell/module/Az.RecoveryServices/?view=azps-1.4.0) v knihovně Azure.

## <a name="set-up-and-register"></a>Nastavení a registrace

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Zahajte:

1. [Stáhnout nejnovější verzi PowerShellu](https://docs.microsoft.com/powershell/azure/install-az-ps)

2. Pomocí následujícího příkazu Najděte dostupné rutiny Azure Backup PowerShellu:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

    Zobrazí se aliasy a rutiny pro Azure Backup, Azure Site Recovery a Recovery Services trezor. Následující obrázek je příkladem toho, co se vám zobrazí. Nejedná se o úplný seznam rutin.

    ![seznam Recovery Services](./media/backup-azure-vms-automation/list-of-recoveryservices-ps.png)

3. Přihlaste se k účtu Azure pomocí **Connect-AzAccount**. Tato rutina zobrazí webovou stránku s výzvou k zadání přihlašovacích údajů k účtu:

    * Alternativně můžete do rutiny **Connect-AzAccount** zahrnout přihlašovací údaje účtu a použít parametr **-Credential** .
    * Pokud jste partnerem CSP při práci jménem tenanta, zadejte zákazníka jako tenanta pomocí názvu primární domény tenantID nebo tenanta. Příklad: **Connect-AzAccount-tenant "fabrikam.com"**

4. Přidružte předplatné, které chcete používat s účtem, protože účet může mít několik předplatných:

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. Pokud používáte Azure Backup poprvé, musíte pomocí rutiny **[Register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)** zaregistrovat poskytovatele služby Azure Recovery Services s vaším předplatným.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Pomocí následujících příkazů můžete ověřit, jestli se poskytovatelé úspěšně zaregistrovali:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

    Ve výstupu příkazu by se **RegistrationState** měl změnit na **registrováno**. Pokud ne, stačí znovu spustit rutinu **[Register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)** .


## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Následující kroky vás provedou vytvořením trezoru Recovery Services. Recovery Services trezor se liší od trezoru záloh.

1. Recovery Services trezor je prostředek Správce prostředků, takže ho musíte umístit do skupiny prostředků. Můžete použít existující skupinu prostředků nebo vytvořit skupinu prostředků pomocí rutiny **[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)** . Při vytváření skupiny prostředků zadejte název a umístění skupiny prostředků.  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```
2. K vytvoření trezoru Recovery Services použijte rutinu [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/new-azrecoveryservicesvault?view=azps-1.4.0) . Nezapomeňte zadat stejné umístění úložiště, jaké bylo použito pro skupinu prostředků.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Zadejte typ redundance úložiště, který se má použít. můžete použít [místně redundantní úložiště (LRS)](../storage/common/storage-redundancy-lrs.md) nebo [geograficky redundantní úložiště (GRS)](../storage/common/storage-redundancy-grs.md). Následující příklad ukazuje možnost-BackupStorageRedundancy pro testvault je nastavená na geograficky redundantní.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperty  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Řada rutin služby Azure Backup vyžaduje jako vstup objekt trezoru služby Recovery Services. Z tohoto důvodu je vhodné uložit objekt trezoru služby Recovery Services do proměnné.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>Zobrazení trezorů v předplatném

Pokud chcete zobrazit všechny trezory v rámci předplatného, použijte [příkaz Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0):

```powershell
Get-AzRecoveryServicesVault
```

Výstup je podobný následujícímu příkladu, Všimněte si, že je k dispozici přidružený ResourceGroupName a umístění.

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

K ochraně virtuálních počítačů použijte Recovery Services trezor. Před použitím ochrany nastavte kontext trezoru (typ chráněných dat v trezoru) a ověřte zásady ochrany. Zásada ochrany je plán, kdy se spouštějí úlohy zálohování a jak dlouho se mají uchovávat snímky záloh.

### <a name="set-vault-context"></a>Nastavit kontext trezoru

Než povolíte ochranu na virtuálním počítači, nastavte kontext trezoru pomocí [set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0) . Po nastavení se kontext trezoru použije pro všechny další rutiny. Následující příklad nastaví kontext trezoru pro trezor *testvault*.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="modifying-storage-replication-settings"></a>Mění se nastavení replikace úložiště.

Pomocí příkazu [set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/Set-AzRecoveryServicesBackupProperty) nastavte konfiguraci replikace úložiště pro trezor na LRS/GRS.

```powershell
$vault= Get-AzRecoveryServicesVault -name "testvault"
Set-AzRecoveryServicesBackupProperty -Vault $vault -BackupStorageRedundancy GeoRedundant/LocallyRedundant
```

> [!NOTE]
> Redundanci úložiště lze upravit pouze v případě, že nejsou k tomuto trezoru chráněny žádné zálohované položky.

### <a name="create-a-protection-policy"></a>Vytvoření zásady ochrany

Při vytváření trezoru služby Recovery Services se vytvoří i výchozí zásady ochrany a uchovávání informací. Výchozí zásady ochrany aktivují úlohu zálohování každý den v určenou dobu. Výchozí zásady uchovávání informací uchovávají denní bod obnovení po dobu 30 dnů. Pomocí výchozích zásad můžete rychle chránit svůj virtuální počítač a upravit zásady později pomocí různých podrobností.

K zobrazení zásad ochrany dostupných v trezoru použijte **[Get-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy)** . Pomocí této rutiny můžete získat konkrétní zásadu nebo zobrazit zásady spojené s typem úlohy. Následující příklad získá zásady pro typ úlohy AzureVM.

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
> Časové pásmo pole BackupTime v PowerShellu je UTC. Pokud se ale čas zálohování zobrazuje v Azure Portal, upraví se čas na své místní časové pásmo.
>
>

Zásada ochrany zálohování je přidružená minimálně k jedné zásadě uchovávání informací. Zásady uchovávání informací definují, jak dlouho je bod obnovení udržován před jeho odstraněním.

- K zobrazení výchozích zásad uchovávání informací použijte [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject) .
- Podobně můžete použít [příkaz Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject) k získání výchozích zásad plánování.
- Rutina [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy) vytvoří objekt prostředí PowerShell, který obsahuje informace o zásadách zálohování.
- Objekty zásad plánování a uchovávání se používají jako vstupy rutiny New-AzRecoveryServicesBackupProtectionPolicy.

Ve výchozím nastavení je v objektu zásad plánování definován počáteční čas. Pomocí následujícího příkladu změňte čas spuštění na požadovaný čas zahájení. Požadovaný čas spuštění by měl být také ve formátu UTC. Následující příklad předpokládá, že požadovaný počáteční čas je 01:00 UTC pro denní zálohy.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Je nutné zadat čas spuštění pouze v 30 minutách pouze násobcích. V tomto příkladu může být pouze "01:00:00" nebo "02:30:00". Počáteční čas nemůže být "01:15:00"

V následujícím příkladu jsou uloženy zásady plánu a zásady uchovávání informací v proměnných. V příkladu se tyto proměnné používají k definování parametrů při vytváření zásad ochrany, *NewPolicy*.

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

Po definování zásady ochrany je stále nutné povolit zásadu pro položku. K povolení ochrany použijte [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) . Povolení ochrany vyžaduje dva objekty – položku a zásadu. Po přidružení zásady k trezoru se spustí pracovní postup zálohování v čase definovaném v plánu zásad.

> [!IMPORTANT]
> Když použijete PS k povolení zálohování pro víc virtuálních počítačů najednou, ujistěte se, že k jedné zásadě nemáte k dispozici víc než 100 virtuálních počítačů. Toto je [doporučený postup](https://docs.microsoft.com/azure/backup/backup-azure-vm-backup-faq#is-there-a-limit-on-number-of-vms-that-can-beassociated-with-a-same-backup-policy). V současné době klient PS explicitně neblokuje, pokud je k dispozici více než 100 virtuálních počítačů, ale tato kontrolní služba bude plánována do budoucna.

Následující příklady umožňují ochranu položky V2VM pomocí zásad NewPolicy. Příklady se liší v závislosti na tom, jestli je virtuální počítač zašifrovaný a jaký typ šifrování.

Povolení ochrany u nešifrovaných **Správce prostředků virtuálních počítačů**:

```powershell
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Pokud chcete povolit ochranu šifrovaných virtuálních počítačů (šifrovaných pomocí klíče bek a KEK), musíte Azure Backup službě udělit oprávnění ke čtení klíčů a tajných kódů z trezoru klíčů.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Pokud chcete povolit ochranu **šifrovaných virtuálních počítačů (jenom zašifrovaných pomocí klíče bek)** , musíte dát službě Azure Backup oprávnění ke čtení tajných kódů z trezoru klíčů.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

> [!NOTE]
> Pokud používáte cloud Azure Government, použijte hodnotu ff281ffe-705c-4F53-9f37-a40e6f2c68f3 pro parametr ServicePrincipalName v rutině [set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) .
>

## <a name="monitoring-a-backup-job"></a>Monitorování úlohy zálohování

Dlouho běžící operace, jako jsou úlohy zálohování, můžete monitorovat bez použití Azure Portal. Chcete-li získat stav probíhající úlohy, použijte rutinu [Get-AzRecoveryservicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) . Tato rutina načte úlohy zálohování pro určitý trezor a tento trezor je zadaný v kontextu trezoru. Následující příklad získá stav probíhající úlohy jako pole a uloží stav do proměnné $joblist.

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

Místo cyklického dotazování těchto úloh na dokončení – což je zbytečné další použití rutiny [Wait-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) . Tato rutina pozastaví provádění, dokud se úloha nedokončí nebo nedosáhne zadané hodnoty časového limitu.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## <a name="manage-azure-vm-backups"></a>Správa záloh virtuálních počítačů Azure

### <a name="modify-a-protection-policy"></a>Úprava zásady ochrany

Chcete-li upravit zásady ochrany, použijte [příkaz set-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) pro úpravu objektů SchedulePolicy nebo RetentionPolicy.

#### <a name="modifying-scheduled-time"></a>Změna naplánovaného času

Když vytvoříte zásady ochrany, ve výchozím nastavení se mu přiřadí čas spuštění. Následující příklady ukazují, jak upravit čas zahájení zásady ochrany.

````powershell
$SchPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z" (This is the time that the customer wants to start the backup)
$UtcTime = $UtcTime.ToUniversalTime()
$SchPol.ScheduleRunTimes[0] = $UtcTime
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -SchedulePolicy $SchPol
````

#### <a name="modifying-retention"></a>Úprava uchovávání informací

Následující příklad změní dobu uchování bodu obnovení na 365 dní.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
$retPol.DailySchedule.DurationCountInDays = 365
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol
```

#### <a name="configuring-instant-restore-snapshot-retention"></a>Konfigurace uchování snímku okamžitého obnovení

> [!NOTE]
> Z AZ PS Version 1.6.0 a vyšší můžete aktualizovat dobu uchování snímku okamžitého obnovení v zásadách pomocí PowerShellu.

````powershell
PS C:\> $bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
$bkpPol.SnapshotRetentionInDays=7
PS C:\> Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
````

Výchozí hodnota bude 2, uživatel může nastavit hodnotu s minimálním počtem 1 a Max z 5. Pro týdenní zásady zálohování je Tato perioda nastavená na 5 a nedá se změnit.

### <a name="trigger-a-backup"></a>Aktivace zálohování

K aktivaci úlohy zálohování použijte [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) . Pokud se jedná o počáteční zálohu, jedná se o úplnou zálohu. Následné zálohy přebírají přírůstkovou kopii. V následujícím příkladu se zachová záloha virtuálního počítače po dobu 60 dnů.

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
> Časové pásmo polí Čas_spuštění a čas_ukončení v PowerShellu je UTC. Pokud je však čas zobrazen v Azure Portal, čas se upraví na vaše místní časové pásmo.
>
>

### <a name="change-policy-for-backup-items"></a>Změnit zásady pro zálohované položky

Uživatel může buď upravit existující zásady, nebo změnit zásadu zálohované položky z Policy1 na Policy2. Chcete-li přepnout zásady pro zálohovanou položku, jednoduše načtěte příslušné zásady a zálohujte položku a použijte příkaz [Enable-AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) s položkou Backup jako parametr.

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType AzureVM -BackupManagementType AzureVM -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
````

Příkaz čeká na dokončení zálohování konfigurace a vrátí následující výstup.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
TestVM           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="stop-protection"></a>Zastavení ochrany

#### <a name="retain-data"></a>Zachovat data

Pokud si uživatel přeje zastavit ochranu, může použít rutinu [Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS. Tím se zastaví naplánovaná zálohování, ale data zálohovaná, dokud se teď neuchovávají trvale.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>Odstranění zálohovaných dat

Pokud chcete uložená zálohovaná data z trezoru úplně odebrat, stačí přidat příznak/RemoveRecoveryPoints nebo přepnout na [příkaz "Zakázat" ochranu](#retain-data).

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

## <a name="restore-an-azure-vm"></a>Obnovení virtuálního počítače Azure

Je důležitý rozdíl mezi obnovením virtuálního počítače pomocí Azure Portal a obnovením virtuálního počítače pomocí PowerShellu. V prostředí PowerShell je operace obnovení dokončena až po vytvoření disků a informací o konfiguraci z bodu obnovení. Operace obnovení nevytvoří virtuální počítač. Postup vytvoření virtuálního počítače z disku najdete v části [Vytvoření virtuálního počítače z obnovených disků](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). Pokud nechcete obnovit celý virtuální počítač, ale chcete obnovit nebo obnovit několik souborů ze zálohy virtuálního počítače Azure, přečtěte si [část obnovení souborů](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup).

> [!Tip]
> Operace obnovení nevytváří virtuální počítač.
>
>

Následující obrázek znázorňuje hierarchii objektů z RecoveryServicesVault dolů do BackupRecoveryPoint.

![Recovery Services hierarchie objektů zobrazující BackupContainer](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Chcete-li obnovit zálohovaná data, identifikujte zálohovanou položku a bod obnovení, který obsahuje data k určitému bodu v čase. K obnovení dat z trezoru na svůj účet použijte [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) .

Základní kroky pro obnovení virtuálního počítače Azure jsou:

* Vyberte virtuální počítač.
* Vyberte bod obnovení.
* Obnovte disky.
* Vytvořte virtuální počítač z uložených disků.

### <a name="select-the-vm"></a>Vyberte virtuální počítač.

Chcete-li získat objekt prostředí PowerShell, který identifikuje správnou zálohovanou položku, začněte z kontejneru v trezoru a Pracujte způsobem v hierarchii objektů. Pokud chcete vybrat kontejner, který představuje virtuální počítač, použijte rutinu [Get-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) a kanál, který rutinu [Get-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) .

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Zvolte bod obnovení.

Pomocí rutiny [Get-AzRecoveryServicesBackupRecoveryPoint Zobrazte](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) seznam všech bodů obnovení pro zálohovanou položku. Pak zvolte bod obnovení, který chcete obnovit. Pokud si nejste jistí, který bod obnovení chcete použít, je dobrým zvykem zvolit nejnovější RecoveryPointType = AppConsistent bod v seznamu.

V následujícím skriptu je proměnná, **$RP**pole bodů obnovení pro vybranou zálohovanou položku, z posledních sedmi dnů. Pole je seřazené v opačném pořadí s nejnovějším bodem obnovení na indexu 0. Pro výběr bodu obnovení použijte standardní indexování pole v PowerShellu. V příkladu $rp [0] vybere nejnovější bod obnovení.

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

### <a name="restore-the-disks"></a>Obnovení disků

Pomocí rutiny [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) obnovte data a konfiguraci zálohované položky do bodu obnovení. Jakmile identifikujete bod obnovení, použijte jej jako hodnotu parametru **-RecoveryPoint** . Ve výše uvedeném příkladu byl **$RP [0]** bod obnovení, který se má použít. V následujícím ukázkovém kódu **$RP [0]** bod obnovení, který se má použít pro obnovení disku.

Postup obnovení disků a informací o konfiguraci:

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG"
$restorejob
```

#### <a name="restore-managed-disks"></a>Obnovení spravovaných disků

> [!NOTE]
> Pokud má zálohovaný virtuální počítač spravované disky a chcete je obnovit jako spravované disky, zavedli jsme možnost Azure PowerShell RM Module v 6.7.0. a vyšší
>
>

Zadejte další parametr **TargetResourceGroupName** a určete tak RG, na které se budou spravované disky obnovovat.

> [!NOTE]
> Pro obnovení spravovaných disků se důrazně doporučuje použít parametr **TargetResourceGroupName** , protože výsledkem je výrazné zlepšení výkonu. Kromě toho z Azure PowerShellu AZ Module 1,0 a vyšší, je tento parametr pro případ obnovení se spravovanými disky povinný.
>
>


```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks"
```

Soubor **VMConfig. JSON** se obnoví do účtu úložiště a spravované disky se obnoví do zadaného cílového RG.

Výstup se podobá následujícímu příkladu:

```powershell
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Počkejte na dokončení úlohy obnovení pomocí rutiny [Wait-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) .

```powershell
Wait-AzRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

Po dokončení úlohy obnovení použijte k získání podrobností o operaci obnovení rutinu [Get-AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) . Vlastnost JobDetails obsahuje informace potřebné k opětovnému sestavení virtuálního počítače.

```powershell
$restorejob = Get-AzRecoveryServicesBackupJob -Job $restorejob
$details = Get-AzRecoveryServicesBackupJobDetails -Job $restorejob
```

Po obnovení disků použijte k vytvoření virtuálního počítače v další části.

## <a name="replace-disks-in-azure-vm"></a>Výměna disků ve virtuálním počítači Azure

Chcete-li nahradit informace o discích a konfiguraci, proveďte následující kroky:

- Krok 1: [Obnovení disků](backup-azure-vms-automation.md#restore-the-disks)
- Krok 2: [Odpojení datového disku pomocí PowerShellu](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk#detach-a-data-disk-using-powershell)
- Krok 3: [Připojení datového disku k virtuálnímu počítači s Windows pomocí PowerShellu](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps)


## <a name="create-a-vm-from-restored-disks"></a>Vytvoření virtuálního počítače z obnovených disků

Po obnovení disků pomocí následujících kroků vytvořte a nakonfigurujte virtuální počítač z disku.

> [!NOTE]
> Aby bylo možné vytvořit šifrované virtuální počítače z obnovených disků, musí mít vaše role Azure oprávnění k provedení této akce, **trezoru Microsoft. a trezorů/nasazení/akce**. Pokud vaše role nemá toto oprávnění, vytvořte pomocí této akce vlastní roli. Další informace najdete v tématu [vlastní role v Azure RBAC](../role-based-access-control/custom-roles.md).
>
>

> [!NOTE]
> Po obnovení disků teď můžete získat šablonu nasazení, kterou můžete použít přímo k vytvoření nového virtuálního počítače. Žádné další rutiny PS pro vytváření spravovaných a nespravovaných virtuálních počítačů, které jsou šifrované/nešifrované.

Podrobnosti výsledné úlohy poskytují identifikátor URI šablony, který lze dotazovat a nasadit.

```powershell
   $properties = $details.properties
   $templateBlobURI = $properties["Template Blob Uri"]
```

Stačí nasadit šablonu k vytvoření nového virtuálního počítače, jak je vysvětleno [zde](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy).

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment ResourceGroupName ExampleResourceGroup -TemplateUri $templateBlobURI -storageAccountType Standard_GRS
```

V následující části jsou uvedené kroky potřebné k vytvoření virtuálního počítače pomocí souboru "VMConfig".

> [!NOTE]
> Pro vytvoření virtuálního počítače se důrazně doporučuje použít šablonu nasazení podrobnou výše. Tato část (body 1-6) bude brzy zastaralá.

1. Dotaz na vlastnosti obnoveného disku pro podrobnosti úlohy.

   ```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $configBlobName = $properties["Config Blob Name"]
   ```

2. Nastavte kontext služby Azure Storage a obnovte konfigurační soubor JSON.

   ```powershell
   Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
   $destination_path = "C:\vmconfig.json"
   Get-AzStorageBlobContent -Container $containerName -Blob $configBlobName -Destination $destination_path
   $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
   ```

3. Vytvořte konfiguraci virtuálního počítače pomocí konfiguračního souboru JSON.

   ```powershell
   $vm = New-AzVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
   ```

4. Připojte disk s operačním systémem a datové disky. Tento krok popisuje příklady různých spravovaných a šifrovaných konfigurací virtuálních počítačů. Použijte příklad, který odpovídá konfiguraci virtuálního počítače.

   * **Nespravované a nešifrované virtuální počítače** – použijte následující ukázku pro nespravované, nešifrované virtuální počítače.

       ```powershell
       Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
       $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
       foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
       {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
       }
       ```

   * **Nespravované a šifrované virtuální počítače s Azure AD (jenom klíče bek)** – pro nespravované a šifrované virtuální počítače se službou Azure AD (ŠIFROVANÉ pomocí klíče bek) je potřeba před připojením disků obnovit tajný klíč do trezoru klíčů. Další informace najdete v tématu [obnovení šifrovaného virtuálního počítače z Azure Backup bodu obnovení](backup-azure-restore-key-secret.md). Následující příklad ukazuje, jak připojit operační systém a datové disky pro šifrované virtuální počítače. Při nastavování disku s operačním systémem nezapomeňte uvést příslušný typ operačního systému.

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

   * **Nespravované a šifrované virtuální počítače s Azure AD (klíče bek a KEK)** – pro nespravované a šifrované virtuální počítače se službou Azure AD (ŠIFROVANÉ pomocí klíče bek a KEK) před připojením disků obnovte klíč a tajný klíč do trezoru klíčů. Další informace najdete v tématu [obnovení šifrovaného virtuálního počítače z Azure Backup bodu obnovení](backup-azure-restore-key-secret.md). Následující příklad ukazuje, jak připojit operační systém a datové disky pro šifrované virtuální počítače.

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

   * **Nespravované a šifrované virtuální počítače bez Azure AD (jenom klíče bek)** – pro nespravované a šifrované virtuální počítače bez služby Azure AD (ŠIFROVANÉ pomocí klíče bek), pokud **není k dispozici zdrojový Trezor klíčů nebo tajný klíč** , obnovujte tajné klíče do trezoru klíčů pomocí postupu v [ Obnoví nešifrovaný virtuální počítač z Azure Backup bodu obnovení](backup-azure-restore-key-secret.md). Pak spusťte následující skripty a nastavte podrobnosti o šifrování obnoveného objektu BLOB operačního systému (Tento krok se nevyžaduje pro datový objekt BLOB). $Dekurl lze načíst z obnoveného trezoru klíčů.<br>

   Níže uvedený skript je nutné provést pouze v případě, že není k dispozici zdrojový trezor a tajný klíč.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""}}]}"
      $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
      $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
      $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
      $osBlob.ICloudBlob.SetMetadata()
      ```

    Po **zpřístupnění tajných** kódů a zadání podrobností o šifrování také v objektu BLOB operačního systému připojte disky pomocí skriptu uvedeného níže.<br>

    Pokud je zdrojový Trezor klíčů nebo tajné kódy již k dispozici, není nutné provést tento skript.

      ```powershell
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

   * **Nespravované a šifrované virtuální počítače bez služby Azure AD (klíče bek a KEK)** – pro nespravované a šifrované virtuální počítače bez služby Azure AD (ŠIFROVANÉ pomocí klíče bek & KEK), pokud **není k dispozici zdrojový trezor** klíčů, klíč a tajné klíče pro Trezor klíčů pomocí nástroje Postup [obnovení nešifrovaného virtuálního počítače z Azure Backup bodu obnovení](backup-azure-restore-key-secret.md). Pak spusťte následující skripty a nastavte podrobnosti o šifrování obnoveného objektu BLOB operačního systému (Tento krok se nevyžaduje pro datový objekt BLOB). $Dekurl a $kekurl je možné načíst z obnoveného trezoru klíčů.

   Níže uvedený skript je nutné provést pouze v případě, že není k dispozici zdrojový Trezor klíčů, klíč nebo tajný klíč.

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
   Až **budou klíče a tajné klíče k dispozici** a podrobnosti o šifrování jsou nastaveny v objektu BLOB operačního systému, připojte disky pomocí skriptu uvedeného níže.

    Pokud jsou k dispozici zdrojový Trezor klíčů/tajné klíče, není nutné spustit výše uvedený skript.

    ```powershell
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

   * **Spravované a nešifrované virtuální počítače** – pro spravované nešifrované virtuální počítače připojte obnovené spravované disky. Podrobné informace najdete v tématu [připojení datového disku k virtuálnímu počítači s Windows pomocí PowerShellu](../virtual-machines/windows/attach-disk-ps.md).

   * **Spravované a šifrované virtuální počítače s Azure AD (jenom klíče bek)** – pro spravované šifrované virtuální počítače s Azure AD (šifrované jenom pomocí klíče bek) připojte obnovené spravované disky. Podrobné informace najdete v tématu [připojení datového disku k virtuálnímu počítači s Windows pomocí PowerShellu](../virtual-machines/windows/attach-disk-ps.md).

   * **Spravované a šifrované virtuální počítače s Azure AD (klíče bek a KEK)** – pro spravované šifrované virtuální počítače s Azure AD (ŠIFROVANÉ pomocí klíče bek a KEK) připojte obnovené spravované disky. Podrobné informace najdete v tématu [připojení datového disku k virtuálnímu počítači s Windows pomocí PowerShellu](../virtual-machines/windows/attach-disk-ps.md).

   * **Spravované a šifrované virtuální počítače bez Azure AD (jenom klíče bek)** – pro spravované a šifrované virtuální počítače bez služby Azure AD (šifrované jenom pomocí klíče bek), pokud **není k dispozici zdrojový Trezor klíčů nebo tajný klíč** , obnovujte tajné klíče do trezoru klíčů pomocí postupu v části [obnovení nešifrovaný virtuální počítač z Azure Backup bodu obnovení](backup-azure-restore-key-secret.md). Pak spusťte následující skripty a nastavte podrobnosti o šifrování obnoveného disku s operačním systémem (Tento krok se nevyžaduje pro datový disk). $Dekurl lze načíst z obnoveného trezoru klíčů.

     Níže uvedený skript je nutné provést pouze v případě, že není k dispozici zdrojový trezor a tajný klíč.  

     ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
      $diskupdateconfig = Set-AzDiskUpdateDiskEncryptionKey -DiskUpdate $diskupdateconfig -SecretUrl $dekUrl -SourceVaultId $keyVaultId  
      Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
      ```

     Po dostupnosti tajných kódů a zadání podrobností o šifrování na disku s operačním systémem, které se mají připojit k obnoveným spravovaným diskům, najdete v tématu [připojení datového disku k virtuálnímu počítači s Windows pomocí PowerShellu](../virtual-machines/windows/attach-disk-ps.md).

   * **Spravované a šifrované virtuální počítače bez služby Azure AD (klíče bek a KEK)** – pro spravované a šifrované virtuální počítače bez služby Azure AD (ŠIFROVANÉ pomocí klíče bek & KEK), pokud **není k dispozici zdrojový trezor** klíčů, klíč a tajné klíče pro Trezor klíčů pomocí postupu v [Obnoví nešifrovaný virtuální počítač z Azure Backup bodu obnovení](backup-azure-restore-key-secret.md). Pak spusťte následující skripty a nastavte podrobnosti o šifrování obnoveného disku s operačním systémem (Tento krok se nevyžaduje pro datový disk). $Dekurl a $kekurl je možné načíst z obnoveného trezoru klíčů.

   Níže uvedený skript je nutné provést pouze v případě, že není k dispozici zdrojový Trezor klíčů, klíč nebo tajný klíč.

   ```powershell
     $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
     $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
     $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
     $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
     $diskupdateconfig = Set-AzDiskUpdateDiskEncryptionKey -DiskUpdate $diskupdateconfig -SecretUrl $dekUrl -SourceVaultId $keyVaultId  
     $diskupdateconfig = Set-AzDiskUpdateKeyEncryptionKey -DiskUpdate $diskupdateconfig -KeyUrl $kekUrl -SourceVaultId $keyVaultId  
     Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
    ```

    Až budou klíče a tajné kódy k dispozici a na disku s operačním systémem jsou nastavené podrobnosti o šifrování, aby bylo možné připojit obnovené spravované disky, přečtěte si téma [připojení datového disku k virtuálnímu počítači s Windows pomocí PowerShellu](../virtual-machines/windows/attach-disk-ps.md).

5. Nastavte nastavení sítě.

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

7. Vložení rozšíření ADE

   * **Pro virtuální počítač s Azure AD** – k ručnímu povolení šifrování pro datové disky použijte následující příkaz.  

     **Jenom klíče bek**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

     **KLÍČE bek a KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId  -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

   * **Pro virtuální počítač bez Azure AD** – pomocí následujícího příkazu ručně povolte šifrování datových disků.

     Pokud v případě, že se při provádění příkazu požádá o AADClientID, bude potřeba aktualizovat Azure PowerShell.

     **Jenom klíče bek**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

      **KLÍČE bek a KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```


## <a name="restore-files-from-an-azure-vm-backup"></a>Obnovení souborů ze zálohy virtuálního počítače Azure

Kromě obnovování disků můžete také obnovit jednotlivé soubory ze zálohy virtuálního počítače Azure. Funkce obnovit soubory poskytuje přístup ke všem souborům v bodu obnovení. Soubory můžete spravovat prostřednictvím Průzkumníka souborů, stejně jako u běžných souborů.

Základní kroky pro obnovení souboru ze zálohy virtuálního počítače Azure jsou:

* Vyberte virtuální počítač.
* Zvolte bod obnovení.
* Připojit disky bodu obnovení
* Zkopírujte požadované soubory.
* Odpojení disku

### <a name="select-the-vm"></a>Vyberte virtuální počítač.

Chcete-li získat objekt prostředí PowerShell, který identifikuje správnou zálohovanou položku, začněte z kontejneru v trezoru a Pracujte způsobem v hierarchii objektů. Pokud chcete vybrat kontejner, který představuje virtuální počítač, použijte rutinu [Get-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) a kanál, který rutinu [Get-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) .

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Zvolte bod obnovení.

Pomocí rutiny [Get-AzRecoveryServicesBackupRecoveryPoint Zobrazte](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) seznam všech bodů obnovení pro zálohovanou položku. Pak zvolte bod obnovení, který chcete obnovit. Pokud si nejste jistí, který bod obnovení chcete použít, je dobrým zvykem zvolit nejnovější RecoveryPointType = AppConsistent bod v seznamu.

V následujícím skriptu je proměnná, **$RP**pole bodů obnovení pro vybranou zálohovanou položku, z posledních sedmi dnů. Pole je seřazené v opačném pořadí s nejnovějším bodem obnovení na indexu 0. Pro výběr bodu obnovení použijte standardní indexování pole v PowerShellu. V příkladu $rp [0] vybere nejnovější bod obnovení.

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

### <a name="mount-the-disks-of-recovery-point"></a>Připojit disky bodu obnovení

Pomocí rutiny [Get-AzRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprpmountscript) Získejte skript pro připojení všech disků bodu obnovení.

> [!NOTE]
> Disky jsou připojené jako připojené disky iSCSI k počítači, na kterém se skript spouští. K okamžitému připojení dochází a neúčtují se žádné poplatky.
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

Spusťte skript na počítači, kde chcete soubory obnovit. Chcete-li spustit skript, je nutné zadat zadané heslo. Po připojení disků použijte Průzkumníka souborů Windows a procházejte nové svazky a soubory. Další informace najdete v článku zálohování, [obnovení souborů ze zálohy virtuálního počítače Azure](backup-azure-restore-files-from-vm.md).

### <a name="unmount-the-disks"></a>Odpojení disků

Po zkopírování požadovaných souborů použijte [příkaz disable-AzRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackuprpmountscript) k odpojení disků. Nezapomeňte odpojit disky, aby byl odebraný přístup k souborům bodu obnovení.

```powershell
Disable-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```

## <a name="next-steps"></a>Další postup

Pokud upřednostňujete použití PowerShellu k zapojení prostředků Azure, přečtěte si článek o PowerShellu, [nasazení a správu zálohování pro Windows Server](backup-client-automation.md). Pokud spravujete zálohy DPM, přečtěte si článek [nasazení a Správa zálohování pro DPM](backup-dpm-automation.md).
