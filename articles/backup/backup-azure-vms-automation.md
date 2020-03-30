---
title: Zálohování a obnovení virtuálních počítačů Azure pomocí PowerShellu
description: Popisuje, jak zálohovat a obnovovat virtuální počítače Azure pomocí Azure Backup s PowerShellem.
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 733a06a84aa170f1361ea74d126ec9752586fce2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247979"
---
# <a name="back-up-and-restore-azure-vms-with-powershell"></a>Zálohování a obnovení virtuálních počítačů Azure pomocí PowerShellu

Tento článek vysvětluje, jak zálohovat a obnovovat virtuální počítač Azure v trezoru [služby Azure Backup](backup-overview.md) Recovery Services pomocí rutin PowerShell.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Vytvořte trezor služby Recovery Services a nastavte kontext úschovny.
> * Definice zásady zálohování
> * Použití zásady zálohování k ochraně několika virtuálních počítačů
> * Aktivace úlohy zálohování na vyžádání pro chráněné virtuální počítače Než budete moct zálohovat (nebo chránit) virtuální počítač, musíte dokončit [předpoklady](backup-azure-arm-vms-prepare.md) pro přípravu prostředí pro ochranu virtuálních počítačů.

## <a name="before-you-start"></a>Než začnete

* [Přečtěte si další informace](backup-azure-recovery-services-vault-overview.md) o trezorech služby Recovery Services.
* [Zkontrolujte](backup-architecture.md#architecture-built-in-azure-vm-backup) architekturu pro zálohování virtuálních počítačích Azure, [získejte informace o](backup-azure-vms-introduction.md) procesu zálohování a [zkontrolujte](backup-support-matrix-iaas.md) podporu, omezení a požadavky.
* Zkontrolujte hierarchii objektů prostředí PowerShell pro služby recovery Services.

## <a name="recovery-services-object-hierarchy"></a>Hierarchie objektů služby Recovery Services

Hierarchie objektů je shrnuta v následujícím diagramu.

![Hierarchie objektů služby Recovery Services](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Zkontrolujte odkaz [na rutinu rutiny](https://docs.microsoft.com/powershell/module/Az.RecoveryServices/?view=azps-1.4.0) **Az.RecoveryServices** v knihovně Azure.

## <a name="set-up-and-register"></a>Nastavení a registrace

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Začněte následovně:

1. [Stažení nejnovější verze PowerShellu](https://docs.microsoft.com/powershell/azure/install-az-ps)

2. Najděte rutiny Azure Backup PowerShell, které jsou k dispozici, zadáním následujícího příkazu:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

    Zobrazí se aliasy a rutiny pro Azure Backup, Azure Site Recovery a trezor služby Recovery Services. Následující obrázek je příkladem toho, co uvidíte. Nejedná se o úplný seznam rutin.

    ![seznam služeb pro obnovení](./media/backup-azure-vms-automation/list-of-recoveryservices-ps.png)

3. Přihlaste se ke svému účtu Azure pomocí **connect-azaccount**. Tato rutina zobrazí webovou stránku s výzvou k zadání přihlašovacích údajů k účtu:

    * Případně můžete zahrnout pověření účtu jako parametr v rutině **Connect-AzAccount** pomocí parametru **-Credential.**
    * Pokud jste partnerem CSP pracujícím jménem tenanta, zadejte zákazníka jako tenanta pomocí jejich id tenanta nebo primárního názvu domény klienta. Příklad: **Connect-AzAccount -Tenant "fabrikam.com"**

4. Přidružte předplatné, které chcete použít, k účtu, protože účet může mít několik předplatných:

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. Pokud používáte Azure Backup poprvé, musíte použít rutinu **[Register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)** k registraci poskytovatele služby Azure Recovery Service s vaším předplatným.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Můžete ověřit, zda se poskytovatelé úspěšně zaregistrovali pomocí následujících příkazů:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

    Ve výstupu příkazu **RegistrationState** by měl změnit na **Registered**. Pokud ne, stačí znovu spustit rutinu **[Register-AzResourceProvider.](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)**

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Následující kroky vás provedou vytvořením trezoru služby Recovery Services. Trezor služby Recovery Services se liší od trezoru zálohování.

1. Trezor služby Recovery Services je prostředek Správce prostředků, takže jej musíte umístit do skupiny prostředků. Můžete použít existující skupinu prostředků nebo vytvořit skupinu prostředků s rutinou **[New-AzResourceGroup.](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)** Při vytváření skupiny prostředků zadejte název a umístění skupiny prostředků.  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. K vytvoření trezoru služby Recovery Services použijte rutinu [New-AzRecoveryServicesVault.](https://docs.microsoft.com/powershell/module/az.recoveryservices/new-azrecoveryservicesvault?view=azps-1.4.0) Nezapomeňte zadat stejné umístění pro úschovnu, jaké bylo použito pro skupinu prostředků.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Zadejte typ redundance úložiště, která se má použít. můžete použít [místně redundantní úložiště (LRS)](../storage/common/storage-redundancy-lrs.md) nebo [geograficky redundantní úložiště (GRS).](../storage/common/storage-redundancy-grs.md) Následující příklad ukazuje -BackupStorageRedundancy možnost testvault je nastavena na GeoRedundant.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperty  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Řada rutin služby Azure Backup vyžaduje jako vstup objekt trezoru služby Recovery Services. Z tohoto důvodu je vhodné uložit objekt trezoru služby Recovery Services do proměnné.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>Zobrazení trezorů v předplatném

Chcete-li zobrazit všechny trezory v předplatném, použijte [get-azrecoveryservicesvault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0):

```powershell
Get-AzRecoveryServicesVault
```

Výstup je podobný následujícímu příkladu, všimněte si, že jsou k dispozici přidružené ResourceGroupName a Location.

```output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

## <a name="back-up-azure-vms"></a>Zálohování virtuálních počítačů Azure

K ochraně virtuálních počítačů použijte trezor služby Recovery Services. Před použitím ochrany nastavte kontext úschovny (typ dat chráněných v úschovně) a ověřte zásady ochrany. Zásady ochrany je plán při spuštění úlohy zálohování a jak dlouho každý snímek zálohy je zachována.

### <a name="set-vault-context"></a>Nastavení kontextu úschovny

Před povolením ochrany na virtuálním počítači nastavte kontext úschovny pomocí [set-AzRecoveryServicesVaultContext.](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0) Po nastavení se kontext trezoru použije pro všechny další rutiny. Následující příklad nastaví kontext úložiště pro trezor *testvault*.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "Contoso-docs-rg" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Načtení ID trezoru

Plánujeme zanesit nastavení kontextu trezoru v souladu s pokyny Azure PowerShellu. Místo toho můžete ID úschovny uložit nebo načíst a předat ho příslušným příkazům. Pokud jste tedy nenastavili kontext úložiště nebo chcete zadat příkaz ke spuštění určitého trezoru, předejte ID úschovny jako "-vaultID" všem relevantním příkazům takto:

```powershell
$targetVault = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault"
$targetVault.ID
```

Nebo

```powershell
$targetVaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

### <a name="modifying-storage-replication-settings"></a>Změna nastavení replikace úložiště

Použití [příkazu Set-AzRecoveryServicesBackupProperty k](https://docs.microsoft.com/powershell/module/az.recoveryservices/Set-AzRecoveryServicesBackupProperty) nastavení konfigurace replikace úložiště úložiště v úložišti na LRS/GRS

```powershell
Set-AzRecoveryServicesBackupProperty -Vault $targetVault -BackupStorageRedundancy GeoRedundant/LocallyRedundant
```

> [!NOTE]
> Redundanci úložiště lze upravit pouze v případě, že neexistují žádné položky zálohování chráněné do trezoru.

### <a name="create-a-protection-policy"></a>Vytvoření zásad ochrany

Při vytváření trezoru služby Recovery Services se vytvoří i výchozí zásady ochrany a uchovávání informací. Výchozí zásady ochrany aktivují úlohu zálohování každý den v určenou dobu. Výchozí zásady uchovávání informací uchovávají denní bod obnovení po dobu 30 dnů. Pomocí výchozích zásad můžete rychle chránit virtuální počítač a upravit zásady později s různými podrobnostmi.

Pomocí **[zásad ochrany Get-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy)** zobrazíte zásady ochrany dostupné v úschovně. Pomocí této rutiny můžete získat konkrétní zásady nebo zobrazit zásady přidružené k typu pracovního vytížení. Následující příklad získá zásady pro typ pracovního vytížení AzureVM.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

Výstup se podobá následujícímu příkladu:

```output
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> Časové pásmo pole BackupTime v prostředí PowerShell je UTC. Pokud se však čas zálohování zobrazí na webu Azure Portal, čas se upraví na místní časové pásmo.
>
>

Zásady ochrany zálohování jsou přidruženy alespoň k jedné zásadě uchovávání informací. Zásady uchovávání informací definují, jak dlouho je bod obnovení uchováván před jeho odstraněním.

* Pomocí [objektu Get-AzRecoveryServicesBackupBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject) zobrazte výchozí zásady uchovávání informací.
* Podobně můžete použít [Get-AzRecoveryServicesBackupBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject) získat výchozí zásady plánu.
* Rutina [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy) vytvoří objekt prostředí PowerShell, který obsahuje informace o zásadách zálohování.
* Objekty zásad plánování a uchovávání informací se používají jako vstupy do rutiny New-AzRecoveryServicesBackupProtectionPolicy.

Ve výchozím nastavení je čas zahájení definován v objektu zásad plánu. Pomocí následujícího příkladu můžete změnit čas zahájení na požadovaný čas zahájení. Požadovaný čas zahájení by měl být také v UTC. Níže uvedený příklad předpokládá, že požadovaný čas zahájení je 01:00 AM UTC pro denní zálohování.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM" -VaultId $targetVault.ID
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Je třeba zadat čas zahájení pouze v násobcích 30 minut. Ve výše uvedeném příkladu může být pouze "01:00:00" nebo "02:30:00". Čas zahájení nemůže být "01:15:00"

Následující příklad ukládá zásady plánu a zásady uchovávání informací v proměnných. Příklad používá tyto proměnné k definování parametrů při vytváření zásad ochrany *NewPolicy*.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM" -VaultId $targetVault.ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultId $targetVault.ID
```

Výstup se podobá následujícímu příkladu:

```output
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### <a name="enable-protection"></a>Povolení ochrany

Po definování zásad ochrany je stále nutné povolit zásady pro položku. Chcete-li povolit ochranu, použijte příkaz [Enable-AzRecoveryServicesBackupProtection.](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) Povolení ochrany vyžaduje dva objekty - položku a zásadu. Jakmile je zásada přidružena k úschovně, pracovní postup zálohování se aktivuje v době definované v plánu zásad.

> [!IMPORTANT]
> Při použití PS povolit zálohování pro více virtuálních počítačů najednou, ujistěte se, že jedna zásada nemá více než 100 virtuálních počítačů s ním spojené. Toto je [doporučený osvědčený postup](https://docs.microsoft.com/azure/backup/backup-azure-vm-backup-faq#is-there-a-limit-on-number-of-vms-that-can-beassociated-with-a-same-backup-policy). V současné době klient PS explicitně neblokuje, pokud existuje více než 100 virtuálních připojení, ale kontrola je plánována na přidání v budoucnu.

Následující příklady povolit ochranu pro položku, V2VM, pomocí zásady NewPolicy. Příklady se liší v závislosti na tom, zda je virtuální počítač šifrovaný a jaký typ šifrování.

Povolení ochrany na **nešifrovaných virtuálních počítačích Správce prostředků**:

```powershell
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

Chcete-li povolit ochranu na šifrovaných virtuálních počítačích (šifrované pomocí BEK a KEK), musíte službě Azure Backup udělit oprávnění ke čtení klíčů a tajných kódů z trezoru klíčů.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

Chcete-li povolit ochranu na **šifrovaných virtuálních počítačích (šifrované pouze pomocí BEK)**, musíte službě Azure Backup udělit oprávnění ke čtení tajných kódů z trezoru klíčů.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

> [!NOTE]
> Pokud používáte cloud Azure Government, použijte pro parametr ServicePrincipalName v rutině [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) hodnotu ff281ffe-705c-4f53-9f37-a40e6f2c68f3.
>

## <a name="monitoring-a-backup-job"></a>Sledování úlohy zálohování

Můžete sledovat dlouhotrvající operace, jako jsou úlohy zálohování, bez použití portálu Azure. Chcete-li získat stav probíhající úlohy, použijte rutinu [Get-AzRecoveryservicesBackupJob.](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) Tato rutina získá úlohy zálohování pro konkrétní úschovnu a tento trezor je určen v kontextu úschovny. Následující příklad získá stav probíhající úlohy jako pole a uloží stav do proměnné $joblist.

```powershell
$joblist = Get-AzRecoveryservicesBackupJob –Status "InProgress" -VaultId $targetVault.ID
$joblist[0]
```

Výstup se podobá následujícímu příkladu:

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016                5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Namísto dotazování těchto úloh pro dokončení - což je zbytečné další kód - použijte [Rutina Wait-AzRecoveryServicesBackupJob.](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) Tato rutina pozastaví provádění, dokud nebude dokončena úloha nebo dokud nebude dosaženo zadané hodnoty časového času.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200 -VaultId $targetVault.ID
```

## <a name="manage-azure-vm-backups"></a>Správa záloh virtuálních počítačů Azure

### <a name="modify-a-protection-policy"></a>Změna zásad ochrany

Chcete-li změnit zásady ochrany, použijte [set-AzRecoveryServicesBackupBackupProtectionProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) k úpravě objektů SchedulePolicy nebo RetentionPolicy.

#### <a name="modifying-scheduled-time"></a>Úprava naplánovaného času

Když vytvoříte zásadu ochrany, je ve výchozím nastavení přiřazen čas zahájení. Následující příklady ukazují, jak změnit čas zahájení zásad ochrany.

````powershell
$SchPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z" (This is the time that the customer wants to start the backup)
$UtcTime = $UtcTime.ToUniversalTime()
$SchPol.ScheduleRunTimes[0] = $UtcTime
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -SchedulePolicy $SchPol -VaultId $targetVault.ID
````

#### <a name="modifying-retention"></a>Změna uchovávání informací

Následující příklad změní bod obnovení uchovávání na 365 dní.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
$retPol.DailySchedule.DurationCountInDays = 365
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol -VaultId $targetVault.ID
```

#### <a name="configuring-instant-restore-snapshot-retention"></a>Konfigurace uchovávání snímků okamžitého obnovení

> [!NOTE]
> Od Az PS verze 1.6.0 dále, jeden může aktualizovat okamžité obnovení snímku retenční období v zásadách pomocí Powershellu

````powershell
$bkpPol = Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM" -VaultId $targetVault.ID
$bkpPol.SnapshotRetentionInDays=7
Set-AzRecoveryServicesBackupProtectionPolicy -policy $bkpPol -VaultId $targetVault.ID
````

Výchozí hodnota bude 2, uživatel může nastavit hodnotu s min 1 a max 5. Pro zásady týdenní zálohování období je nastavena na 5 a nelze změnit.

### <a name="trigger-a-backup"></a>Spuštění zálohy

Pomocí [položky Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) aktivujte úlohu zálohování. Pokud se jedná o počáteční zálohu, je to úplná záloha. Následné zálohy trvat přírůstkové kopie. Následující příklad trvá záloha virtuálních počítače, které mají být zachovány po dobu 60 dnů.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
$job = Backup-AzRecoveryServicesBackupItem -Item $item -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

Výstup se podobá následujícímu příkladu:

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup              InProgress          4/23/2016                  5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> Časové pásmo polí StartTime a EndTime v prostředí PowerShell je UTC. Když se však čas zobrazí na portálu Azure, čas se upraví na místní časové pásmo.
>
>

### <a name="change-policy-for-backup-items"></a>Změnit zásady pro položky zálohování

Uživatel může změnit existující zásady nebo zásady zálohované položky ze zásad1 na Policy2. Chcete-li přepnout zásady pro zálohovanou položku, načíst příslušné zásady a zálohovat položku a použít [příkaz Enable-AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) s položkou zálohování jako parametr.

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName> -VaultId $targetVault.ID
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType AzureVM -BackupManagementType AzureVM -Name "<BackupItemName>" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1 -VaultId $targetVault.ID
````

Příkaz čeká na dokončení konfigurace zálohy a vrátí následující výstup.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
TestVM           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="stop-protection"></a>Zastavení ochrany

#### <a name="retain-data"></a>Zachování dat

Pokud si uživatel přeje ochranu zastavit, může použít rutinu [PS Disable-AzRecoveryServicesBackupProtection.](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) Tím se zastaví plánované zálohování, ale data zálohovaná až dosud jsou zachována navždy.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>Odstranění zálohovaných dat

Chcete-li zcela odstranit uložená záložní data v trezoru, stačí přidat příznak /přepnout na příkaz "zakázat" příznak /přepnout do [příkazu 'zakázat' ochranu](#retain-data).

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

## <a name="restore-an-azure-vm"></a>Obnovení virtuálního počítače Azure

Je důležitý rozdíl mezi obnovení virtuálního počítače pomocí portálu Azure a obnovení virtuálního počítače pomocí Prostředí PowerShell. S Prostředím PowerShell je operace obnovení dokončena po vytvoření disků a informací o konfiguraci z bodu obnovení. Operace obnovení nevytvoří virtuální počítač. Pokud chcete vytvořit virtuální počítač z disku, přečtěte si část [Vytvoření virtuálního počítače z obnovených disků](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). Pokud nechcete obnovit celý virtuální počítač, ale chcete obnovit nebo obnovit několik souborů ze zálohy virtuálního počítače Azure, přečtěte si [část obnovení souboru](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup).

> [!Tip]
> Operace obnovení nevytvoří virtuální počítač.
>
>

Následující obrázek znázorňuje hierarchii objektů od recoveryservicesvault až po BackupRecoveryPoint.

![Hierarchie objektů služby Recovery Services zobrazující kontejner BackupContainer](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Chcete-li obnovit záložní data, identifikujte zálohovanou položku a bod obnovení, který obsahuje data bodu v čase. Pomocí [nástroje Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) obnovte data z úložiště do svého účtu.

Základní kroky k obnovení virtuálního počítače Azure jsou:

* Vyberte virtuální počítač.
* Zvolte bod obnovení.
* Obnovte disky.
* Vytvořte virtuální ho svitek z uložených disků.

### <a name="select-the-vm"></a>Výběr virtuálního virtuálního mísy

Chcete-li získat objekt Prostředí PowerShell, který identifikuje správnou položku zálohování, začněte z kontejneru v úschovně a propracujte se dolů hierarchií objektů. Chcete-li vybrat kontejner, který představuje virtuální hod, použijte rutinu [Get-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) a kanál, který do rutiny [Get-AzRecoveryServicesBackupItem.](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem)

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

### <a name="choose-a-recovery-point"></a>Zvolte bod obnovení

Pomocí rutiny [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) vypsat všechny body obnovení pro položku zálohování. Pak zvolte bod obnovení, který chcete obnovit. Pokud si nejste jisti, který bod obnovení použít, je vhodné zvolit nejnovější RecoveryPointType = AppConsistent bod v seznamu.

V následujícím skriptu je proměnná **$rp**, pole bodů obnovení pro vybranou položku zálohování za posledních sedm dní. Pole je seřazeno v obráceném pořadí času s nejnovějším bodem obnovení v indexu 0. K výběru bodu obnovení použijte standardní indexování pole PowerShell. V příkladu $rp[0] vybere nejnovější bod obnovení.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $targetVault.ID
$rp[0]
```

Výstup se podobá následujícímu příkladu:

```output
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

Pomocí rutiny [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) obnovte data a konfiguraci položky zálohy do bodu obnovení. Jakmile identifikujete bod obnovení, použijte jej jako hodnotu parametru **-RecoveryPoint.** Ve výše uvedeném vzorku byl **$rp[0]** bodem obnovení, který se měl použít. V následujícím ukázkovém kódu je **$rp[0]** bod obnovení, který se má použít pro obnovení disku.

Obnovení disků a informací o konfiguraci:

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -VaultId $targetVault.ID
$restorejob
```

#### <a name="restore-managed-disks"></a>Obnovení spravovaných disků

> [!NOTE]
> Pokud podporovaný virtuální počítač spravuje disky a chcete je obnovit jako spravované disky, zavedli jsme funkce z modulu Azure PowerShell RM v 6.7.0. a dále
>
>

Zadejte další parametr **TargetResourceGroupName** pro určení RG, na které budou obnoveny spravované disky.

> [!NOTE]
> Důrazně doporučujeme použít parametr **TargetResourceGroupName** pro obnovení spravovaných disků, protože výsledkem jsou významná vylepšení výkonu. Také z modulu Azure Powershell Az 1.0 dále je tento parametr povinný v případě obnovení se spravovanými disky
>
>

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID
```

Soubor **VMConfig.JSON** bude obnoven do účtu úložiště a spravované disky budou obnoveny na zadaný cílový RG.

Výstup se podobá následujícímu příkladu:

```output
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Pomocí rutiny [Wait-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) počkejte na dokončení úlohy obnovení.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

Po dokončení úlohy obnovení použijte rutinu [Get-AzRecoveryServicesBackupJobDetails,](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) abyste získali podrobnosti o operaci obnovení. Vlastnost JobDetails má informace potřebné k opětovnému sestavení virtuálního účtu.

```powershell
$restorejob = Get-AzRecoveryServicesBackupJob -Job $restorejob -VaultId $targetVault.ID
$details = Get-AzRecoveryServicesBackupJobDetails -Job $restorejob -VaultId $targetVault.ID
```

Po obnovení disků přejděte k další části a vytvořte virtuální počítače.

## <a name="replace-disks-in-azure-vm"></a>Nahrazení disků ve virtuálním počítači Azure

Chcete-li nahradit disky a informace o konfiguraci, proveďte následující kroky:

* Krok 1: [Obnovení disků](backup-azure-vms-automation.md#restore-the-disks)
* Krok 2: [Odpojení datového disku pomocí prostředí PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk#detach-a-data-disk-using-powershell)
* Krok 3: [Připojení datového disku k virtuálnímu počítače s Windows pomocí Prostředí PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps)

## <a name="create-a-vm-from-restored-disks"></a>Vytvoření virtuálního počítače z obnovených disků

Po obnovení disků vytvořte a nakonfigurujte virtuální počítač z disku pomocí následujících kroků.

> [!NOTE]
>
> 1. Je vyžadován modul AzureAz 3.0.0 nebo vyšší. <br>
> 2. Chcete-li vytvořit šifrované virtuální počítače z obnovených disků, musí mít vaše role Azure oprávnění k provedení akce **Microsoft.KeyVault/vaults/deploy/action**. Pokud vaše role nemá toto oprávnění, vytvořte vlastní roli s touto akcí. Další informace najdete [v tématu vlastní role v Azure RBAC](../role-based-access-control/custom-roles.md). <br>
> 3. Po obnovení disků teď můžete získat šablonu nasazení, kterou můžete přímo použít k vytvoření nového virtuálního počítače. Žádné další různé rutiny PS k vytvoření spravovaných/nespravovaných virtuálních mandů, které jsou šifrované nebo nezašifrované.<br>
> <br>

### <a name="create-a-vm-using-the-deployment-template"></a>Vytvoření virtuálního počítače pomocí šablony nasazení

Podrobnosti výsledné úlohy poskytují identifikátor URI šablony, který lze dotazovat a nasazovat.

```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $templateBlobURI = $properties["Template Blob Uri"]
```

Šablona není přímo přístupná, protože je pod účtem úložiště zákazníka a daným kontejnerem. Potřebujeme úplnou adresu URL (spolu s dočasným tokenem SAS) pro přístup k této šabloně.

1. Nejprve extrahujte název šablony ze šablonyBlobURI. Formát je uveden níže. Pomocí operace rozdělení v prostředí Powershell můžete extrahovat konečný název šablony z této adresy URL.

```http
https://<storageAccountName.blob.core.windows.net>/<containerName>/<templateName>
```

2. Pak může být vygenerována úplná adresa URL, jak je vysvětleno [zde](https://docs.microsoft.com/azure/azure-resource-manager/templates/secure-template-with-sas-token?tabs=azure-powershell#provide-sas-token-during-deployment).

```powershell
Set-AzCurrentStorageAccount -Name $storageAccountName -ResourceGroupName <StorageAccount RG name>
$templateBlobFullURI = New-AzStorageBlobSASToken -Container $containerName -Blob <templateName> -Permission r -FullUri
```

3. Nasazení šablony k vytvoření nového virtuálního počítače, jak je vysvětleno [zde](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy).

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment ResourceGroupName ExampleResourceGroup -TemplateUri $templateBlobFullURI -storageAccountType Standard_GRS
```

### <a name="create-a-vm-using-the-config-file"></a>Vytvoření virtuálního virtuálního mísa pomocí konfiguračního souboru

V následující části jsou uvedeny kroky nezbytné k vytvoření virtuálního virtuálního mísy pomocí souboru VMConfig.

> [!NOTE]
> Důrazně doporučujeme použít šablonu nasazení podrobně uvedenou výše k vytvoření virtuálního počítače. Tato sekce (body 1-6) bude brzy zastaralá.

1. Dotaz na obnovené vlastnosti disku pro podrobnosti o úloze.

   ```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $configBlobName = $properties["Config Blob Name"]
   ```

2. Nastavte kontext úložiště Azure a obnovte konfigurační soubor JSON.

   ```powershell
   Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
   $destination_path = "C:\vmconfig.json"
   Get-AzStorageBlobContent -Container $containerName -Blob $configBlobName -Destination $destination_path
   $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
   ```

3. K vytvoření konfigurace virtuálního počítače použijte konfigurační soubor JSON.

   ```powershell
   $vm = New-AzVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
   ```

4. Připojte disk operačního systému a datové disky. Tento krok obsahuje příklady pro různé spravované a šifrované konfigurace virtuálních počítačů. Použijte příklad, který vyhovuje konfiguraci virtuálního počítače.

* **Nespravované a nešifrované virtuální aplikace** – následující ukázka se použije pro nespravované nešifrované virtuální viny.

```powershell
       Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
       $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
       foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
       {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
       }
```

* **Nespravované a šifrované virtuální počítače s Azure AD (pouze BEK)** – pro nespravované, šifrované virtuální počítače s Azure AD (šifrované pouze pomocí BEK), je třeba obnovit tajný klíč do trezoru klíčů, než budete moct připojit disky. Další informace najdete v tématu [Obnovení šifrovaného virtuálního počítače z bodu obnovení zálohování Azure](backup-azure-restore-key-secret.md). Následující ukázka ukazuje, jak připojit operační systém a datové disky pro šifrované virtuální počítače. Při nastavování disku operačního systému nezapomeňte uvést příslušný typ operačního systému.

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

* **Nespravované a šifrované virtuální počítače s Azure AD (BEK a KEK)** – pro nespravované, šifrované virtuální počítače s Azure AD (šifrované pomocí BEK a KEK), obnovit klíč a tajný klíč do trezoru klíčů před připojením disků. Další informace najdete [v tématu Obnovení šifrovaného virtuálního počítače z bodu obnovení zálohování Azure](backup-azure-restore-key-secret.md). Následující ukázka ukazuje, jak připojit operační systém a datové disky pro šifrované virtuální počítače.

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

* **Nespravované a šifrované virtuální počítače bez Azure AD (pouze BEK)** – pro nespravované, šifrované virtuální počítače bez Azure AD (šifrované pouze pomocí BEK), pokud zdroj **keyVault/secret nejsou k dispozici** obnovit tajné klíče do trezoru klíčů pomocí postupu v [obnovení nešifrovaného virtuálního počítače z bodu obnovení zálohování Azure](backup-azure-restore-key-secret.md). Pak spusťte následující skripty pro nastavení podrobností šifrování na obnoveném objektu blob operačního spoje (tento krok není vyžadován pro objekt blob dat). $dekurl lze načíst z obnovenékeyVault.

Níže uvedený skript musí být proveden pouze v případě, že zdrojový klíčVault/tajný klíč není k dispozici.

```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""}}]}"
      $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
      $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
      $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
      $osBlob.ICloudBlob.SetMetadata()
```

Poté, co **jsou k dispozici tajné klíče** a podrobnosti šifrování jsou také nastaveny na objekt blob operačního systému, připojte disky pomocí skriptu uvedeného níže.

Pokud zdrojový klíčVault/secrets jsou již k dispozici, pak výše uvedený skript nemusí být provedeny.

```powershell
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
```

* **Nespravované a šifrované virtuální počítače bez Azure AD (BEK a KEK)** – pro nespravované, šifrované virtuální počítače bez Azure AD (šifrované pomocí BEK & KEK), pokud zdroj **keyVault/key/secret nejsou k dispozici** obnovit klíč a tajné klíče do trezoru klíčů pomocí postupu v [obnovení nešifrovaného virtuálního počítače z bodu obnovení Azure](backup-azure-restore-key-secret.md). Pak spusťte následující skripty pro nastavení podrobností šifrování na obnoveném objektu blob operačního spoje (tento krok není vyžadován pro objekt blob dat). $dekurl a $kekurl lze načíst z obnovenékeyVault.

Níže uvedený skript musí být proveden pouze v případě, že zdrojový klíčVault/key/secret není k dispozici.

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

Po **klíč/tajné klíče jsou k dispozici** a podrobnosti šifrování jsou nastaveny na objekt blob operačního systému, připojte disky pomocí skriptu uvedeného níže.

Pokud zdrojový klíčVault/key/secrets jsou k dispozici, pak výše uvedený skript nemusí být provedeny.

```powershell
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
```

* **Spravované a nešifrované virtuální počítače** – pro spravované nešifrované virtuální počítače připojte obnovené spravované disky. Podrobné informace najdete v [tématu Připojení datového disku k virtuálnímu virtuálnímu počítače se systémem Windows pomocí prostředí PowerShell](../virtual-machines/windows/attach-disk-ps.md).

* **Spravované a šifrované virtuální počítače s Azure AD (pouze BEK)** – pro spravované šifrované virtuální počítače s Azure AD (šifrované pouze pomocí BEK) připojte obnovené spravované disky. Podrobné informace najdete v [tématu Připojení datového disku k virtuálnímu virtuálnímu počítače se systémem Windows pomocí prostředí PowerShell](../virtual-machines/windows/attach-disk-ps.md).

* **Spravované a šifrované virtuální počítače s Azure AD (BEK a KEK)** – pro spravované šifrované virtuální počítače s Azure AD (šifrované pomocí BEK a KEK) připojte obnovené spravované disky. Podrobné informace najdete v [tématu Připojení datového disku k virtuálnímu virtuálnímu počítače se systémem Windows pomocí prostředí PowerShell](../virtual-machines/windows/attach-disk-ps.md).

* **Spravované a šifrované virtuální počítače bez Azure AD (pouze BEK)** -Pro spravované, šifrované virtuální počítače bez Azure AD (šifrované pouze pomocí BEK), pokud zdroj **keyVault/tajný klíč nejsou k dispozici** obnovit tajné klíče do trezoru klíčů pomocí postupu v obnovení [nešifrovaného virtuálního počítače z bodu obnovení zálohování Azure](backup-azure-restore-key-secret.md). Poté spusťte následující skripty pro nastavení podrobností šifrování na obnoveném disku operačního systému (tento krok není vyžadován pro datový disk). $dekurl lze načíst z obnovenékeyVault.

Níže uvedený skript musí být proveden pouze v případě, že zdrojový klíčVault/tajný klíč není k dispozici.  

```powershell
$dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
$keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
$diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
$encryptionSettingsElement = New-Object Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement
$encryptionSettingsElement.DiskEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndSecretReference
$encryptionSettingsElement.DiskEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
$encryptionSettingsElement.DiskEncryptionKey.SourceVault.Id = $keyVaultId
$encryptionSettingsElement.DiskEncryptionKey.SecretUrl = $dekUrl
$diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings = New-Object System.Collections.Generic.List[Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement]
$diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings.Add($encryptionSettingsElement)
$diskupdateconfig.EncryptionSettingsCollection.EncryptionSettingsVersion = "1.1"
Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
```

Po tajné klíče jsou k dispozici a podrobnosti šifrování jsou nastaveny na disku operačního systému připojit obnovené spravované disky, najdete v [tématu připojení datového disku k virtuálnímu počítači systému Windows pomocí prostředí PowerShell](../virtual-machines/windows/attach-disk-ps.md).

* **Spravované a šifrované virtuální počítače bez Azure AD (BEK a KEK)** – pro spravované, šifrované virtuální počítače bez Azure AD (šifrované pomocí BEK & KEK), pokud zdroj **keyVault/key/secret nejsou k dispozici** obnovit klíč a tajné klíče do trezoru klíčů pomocí postupu v [obnovení nešifrovaného virtuálního počítače z bodu obnovení Azure](backup-azure-restore-key-secret.md). Poté spusťte následující skripty pro nastavení podrobností šifrování na obnoveném disku operačního systému (tento krok není vyžadován pro datové disky). $dekurl a $kekurl lze načíst z obnovenékeyVault.

Níže uvedený skript musí být proveden pouze v případě, že zdrojový klíčVault/key/secret není k dispozici.

```powershell
$dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
$kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
$keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
$diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
$encryptionSettingsElement = New-Object Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement
$encryptionSettingsElement.DiskEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndSecretReference
$encryptionSettingsElement.DiskEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
$encryptionSettingsElement.DiskEncryptionKey.SourceVault.Id = $keyVaultId
$encryptionSettingsElement.DiskEncryptionKey.SecretUrl = $dekUrl
$encryptionSettingsElement.KeyEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndKeyReference
$encryptionSettingsElement.KeyEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
$encryptionSettingsElement.KeyEncryptionKey.SourceVault.Id = $keyVaultId
$encryptionSettingsElement.KeyEncryptionKey.KeyUrl = $kekUrl
$diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings = New-Object System.Collections.Generic.List[Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement]
$diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings.Add($encryptionSettingsElement)
$diskupdateconfig.EncryptionSettingsCollection.EncryptionSettingsVersion = "1.1"
Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
```

Po klíč/tajné klíče jsou k dispozici a podrobnosti šifrování jsou nastaveny na disku operačního systému připojit obnovené spravované disky, najdete v [tématu připojení datového disku k virtuálnímu počítači systému Windows pomocí prostředí PowerShell](../virtual-machines/windows/attach-disk-ps.md).

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

7. Push ADE rozšíření.
   Pokud rozšíření ADE nejsou nabízeny, pak datové disky budou označeny jako nešifrované, takže je povinné pro níže uvedené kroky, které mají být provedeny:

   * **Pro virtuální počítač s Azure AD** – pomocí následujícího příkazu ručně povolit šifrování pro datové disky  

     **Pouze BEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

     **BEK a KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId  -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

   * **Pro virtuální počítač bez Azure AD** – pomocí následujícího příkazu ručně povolit šifrování pro datové disky.

     Pokud během spuštění příkazu požádá o AADClientID, pak je potřeba aktualizovat Azure PowerShell.

     **Pouze BEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

      **BEK a KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

> [!NOTE]
> Ujistěte se, že ručně odstranit soubory JASON vytvořené jako součást procesu obnovení šifrovaného virtuálního počítače disku.

## <a name="restore-files-from-an-azure-vm-backup"></a>Obnovení souborů ze zálohy virtuálního počítače Azure

Kromě obnovení disků můžete také obnovit jednotlivé soubory ze zálohy virtuálního počítače Azure. Funkce obnovení souborů poskytuje přístup ke všem souborům v bodě obnovení. Spravujte soubory pomocí Průzkumníka souborů stejně jako u běžných souborů.

Základní kroky k obnovení souboru ze zálohy virtuálního počítače Azure jsou:

* Výběr virtuálního virtuálního mísy
* Zvolte bod obnovení
* Připojení disků bodu obnovení
* Kopírování požadovaných souborů
* Odpojit disk

### <a name="select-the-vm"></a>Výběr virtuálního virtuálního mísy

Chcete-li získat objekt Prostředí PowerShell, který identifikuje správnou položku zálohování, začněte z kontejneru v úschovně a propracujte se dolů hierarchií objektů. Chcete-li vybrat kontejner, který představuje virtuální hod, použijte rutinu [Get-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) a kanál, který do rutiny [Get-AzRecoveryServicesBackupItem.](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem)

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

### <a name="choose-a-recovery-point"></a>Zvolte bod obnovení

Pomocí rutiny [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) vypsat všechny body obnovení pro položku zálohování. Pak zvolte bod obnovení, který chcete obnovit. Pokud si nejste jisti, který bod obnovení použít, je vhodné zvolit nejnovější RecoveryPointType = AppConsistent bod v seznamu.

V následujícím skriptu je proměnná **$rp**, pole bodů obnovení pro vybranou položku zálohování za posledních sedm dní. Pole je seřazeno v obráceném pořadí času s nejnovějším bodem obnovení v indexu 0. K výběru bodu obnovení použijte standardní indexování pole PowerShell. V příkladu $rp[0] vybere nejnovější bod obnovení.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $targetVault.ID
$rp[0]
```

Výstup se podobá následujícímu příkladu:

```output
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

### <a name="mount-the-disks-of-recovery-point"></a>Připojení disků bodu obnovení

Pomocí rutiny [Get-AzRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprpmountscript) získáte skript pro připojení všech disků bodu obnovení.

> [!NOTE]
> Disky jsou připojeny jako disky iSCSI k počítači, ve kterém je skript spuštěn. Montáž nastane okamžitě a neúčtuje vám žádné poplatky.
>
>

```powershell
Get-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0] -VaultId $targetVault.ID
```

Výstup se podobá následujícímu příkladu:

```output
OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```

Spusťte skript v počítači, kde chcete obnovit soubory. Chcete-li spustit skript, musíte zadat zadaný hesel. Po připojení disků můžete pomocí Průzkumníka souborů systému Windows procházet nové svazky a soubory. Další informace najdete v článku Zálohování, [obnovení souborů ze zálohy virtuálního počítače Azure](backup-azure-restore-files-from-vm.md).

### <a name="unmount-the-disks"></a>Odpojit disky

Po zkopírování požadovaných souborů odpojte disky pomocí [příkazu Disable-AzRecoveryServicesBackupRPMountScript.](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackuprpmountscript) Nezapomeňte odpojit disky, aby byl odebrán přístup k souborům bodu obnovení.

```powershell
Disable-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0] -VaultId $targetVault.ID
```

## <a name="next-steps"></a>Další kroky

Pokud dáváte přednost použití PowerShellu k interakci s prostředky Azure, přečtěte si článek o PowerShellu [Nasazení a správa zálohování pro Windows Server](backup-client-automation.md). Pokud spravujete zálohy Aplikace DPM, přečtěte si článek [Nasazení a správa zálohování pro aplikace DPM](backup-dpm-automation.md).
