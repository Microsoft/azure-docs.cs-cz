---
title: Zálohování a obnovení databází SQL ve virtuálních počítačích Azure pomocí PowerShellu – Azure Backup
description: Zálohujte a obnovte databáze SQL ve virtuálních počítačích Azure pomocí Azure Backup a PowerShellu.
ms.reviewer: pullabhk
author: dcurwin
manager: carmonm
keywords: Azure Backup; SQL
ms.service: backup
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: dacurwin
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.openlocfilehash: d5f3b98048cb04eab15479c3a9f5d27f16df1f3a
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309759"
---
# <a name="back-up-and-restore-sql-databases-in-azure--vms-with-powershell"></a>Zálohování a obnovení databází SQL ve virtuálních počítačích Azure pomocí PowerShellu

Tento článek popisuje, jak použít Azure PowerShell k zálohování a obnovení databáze SQL ve virtuálním počítači Azure pomocí služby [Azure Backup](backup-overview.md) Recovery Services trezoru.

Tento kurz vysvětluje následující postupy:

> [!div class="checklist"]
> * Nastavte PowerShell a zaregistrujte poskytovatele služby Azure Recovery Services.
> * Vytvořte trezor služby Recovery Services.
> * Konfigurace zálohování databáze SQL na virtuálním počítači Azure.
> * Spusťte úlohu zálohování.
> * Obnovte zálohovanou databázi SQL.
> * Monitorujte úlohy zálohování a obnovení.

## <a name="before-you-start"></a>Než začnete

* [Přečtěte si další informace](backup-azure-recovery-services-vault-overview.md) o úložištích Recovery Services.
* Přečtěte si o možnostech funkcí pro [zálohování SQL databáze v rámci virtuálních počítačů Azure](backup-azure-sql-database.md#before-you-start).
* Zkontrolujte hierarchii objektů PowerShell pro Recovery Services.

### <a name="recovery-services-object-hierarchy"></a>Recovery Services hierarchie objektů

Hierarchie objektů je shrnuta v následujícím diagramu.

![Recovery Services hierarchie objektů](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Přečtěte si referenční informace k rutině **AZ. RecoveryServices** [cmdlet reference](/powershell/module/az.recoveryservices) v knihovně Azure.

### <a name="set-up-and-install"></a>Nastavení a instalace

Nastavte PowerShell následujícím způsobem:

1. [Stáhněte si nejnovější verzi programu AZ PowerShell](/powershell/azure/install-az-ps). Minimální požadovaná verze je 1.5.0.

2. Pomocí tohoto příkazu Najděte rutiny Azure Backup PowerShellu:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Zkontrolujte aliasy a rutiny pro Azure Backup a trezor Recovery Services. Tady je příklad toho, co se vám může zobrazit. Nejedná se o úplný seznam rutin.

    ![Seznam rutin Recovery Services](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Přihlaste se ke svému účtu Azure pomocí **Connect-AzAccount**.
5. Na zobrazené webové stránce budete vyzváni k zadání přihlašovacích údajů k účtu.

    * Alternativně můžete zahrnout přihlašovací údaje účtu jako parametr v rutině **Connect-AzAccount** s parametrem **-Credential**.
    * Pokud jste partner CSP pro tenanta, zadejte zákazníka jako tenanta pomocí názvu primární domény tenantID nebo tenanta. Příkladem je **Connect-AzAccount-Tenant** Fabrikam.com.

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

9. Ve výstupu příkazu ověřte, že **RegistrationState** změny se zaregistrují. Pokud ne, spusťte znovu rutinu **Register-AzResourceProvider** .

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Pomocí těchto kroků můžete vytvořit trezor Recovery Services.

Recovery Services trezor je prostředek Správce prostředků, takže ho musíte umístit do skupiny prostředků. Můžete použít existující skupinu prostředků, nebo můžete vytvořit skupinu prostředků pomocí rutiny **New-AzResourceGroup** . Při vytváření skupiny prostředků zadejte název a umístění skupiny prostředků.

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
    * Následující příklad nastaví možnost **-BackupStorageRedundancy** pro příkaz[set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd pro **testvault** nastavenou na geograficky **redundantní**.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>Zobrazení trezorů v předplatném

Pokud chcete zobrazit všechny trezory v rámci předplatného, použijte [příkaz Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

Výstup je podobný následujícímu. K dispozici je přidružená skupina prostředků a umístění.

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

V souladu s pokyny pro Azure PowerShell plánujeme vyřadit nastavení kontextu trezoru. Místo toho můžete uložit nebo načíst ID trezoru a předat příslušné příkazy následujícím způsobem:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>Konfigurace zásady zálohování

Zásady zálohování určují plán zálohování a dobu, po kterou mají být udržovány body obnovení zálohy:

* Zásada zálohování je přidružená minimálně k jedné zásadě uchovávání informací. Zásady uchovávání informací definují, jak dlouho je bod obnovení udržován před jeho odstraněním.
* Podívejte se na výchozí uchování zásad zálohování pomocí [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
* Seznamte se s výchozím plánem zásad zálohování pomocí [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
* Pomocí rutiny [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) vytvoříte nové zásady zálohování. Zadáváte objekty zásad plánování a uchovávání dat.

Ve výchozím nastavení je v objektu zásad plánování definován počáteční čas. Pomocí následujícího příkladu změňte čas spuštění na požadovaný čas zahájení. Požadovaný čas spuštění by měl být také ve formátu UTC. Následující příklad předpokládá, že požadovaný počáteční čas je 01:00 UTC pro denní zálohy.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Je nutné zadat čas spuštění pouze v 30 minutách pouze násobcích. V tomto příkladu může být pouze "01:00:00" nebo "02:30:00". Počáteční čas nemůže být "01:15:00"

V následujícím příkladu jsou uloženy zásady plánu a zásady uchovávání informací v proměnných. Pak tyto proměnné používá jako parametry pro nové zásady (**NewSQLPolicy**). **NewSQLPolicy** přijímá každodenní "úplnou" zálohu, uchovává ji po dobu 180 dnů a při každém 2 hodinách provede zálohování protokolu.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "MSSQL"
$NewSQLPolicy = New-AzRecoveryServicesBackupProtectionPolicy -Name "NewSQLPolicy" -WorkloadType "MSSQL" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Výstup je podobný následujícímu.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                Frequency                                IsDifferentialBackup IsLogBackupEnabled
                                                                                                                                Enabled
----                 ------------       -------------------- ----------                ---------                                -------------------- ------------------
NewSQLPolicy         MSSQL              AzureWorkload        3/15/2019 01:30:00 AM      Daily                                    False                True
```

## <a name="enable-backup"></a>Povolit zálohování

### <a name="registering-the-sql-vm"></a>Registrace virtuálního počítače SQL

Pro zálohování virtuálních počítačů Azure a sdílené složky Azure se služba Backup může připojit k těmto Azure Resource Manager prostředkům a načíst příslušné podrobnosti. Vzhledem k tomu, že SQL je aplikace na virtuálním počítači Azure, služba zálohování potřebuje oprávnění pro přístup k aplikaci a načtení potřebných podrobností. Abyste to mohli udělat, musíte *zaregistrovat* virtuální počítač Azure, který obsahuje aplikaci SQL, s trezorem služby Recovery Services. Jakmile zaregistrujete virtuální počítač SQL pomocí trezoru, můžete SQL databáze chránit jenom k tomuto trezoru. K registraci virtuálního počítače použijte rutinu [Register-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS.

````powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
````

Příkaz vrátí kontejner záloh tohoto prostředku a stav bude zaregistrováno.

> [!NOTE]
> Pokud parametr Force není zadaný, zobrazí se uživateli výzva k potvrzení pomocí textu. Chcete zakázat ochranu tohoto kontejneru. Tento text prosím ignorujte a potvrďte ho tak, že zaznamenáte "Y". Jedná se o známý problém a pracujeme na odebrání textu a požadavku parametru Force.

### <a name="fetching-sql-dbs"></a>Načítá se SQL databáze.

Po dokončení registrace bude služba zálohování moct zobrazit seznam všech dostupných součástí SQL v rámci virtuálního počítače. Pokud chcete zobrazit všechny součásti SQL, které se ještě zálohují do tohoto trezoru, použijte rutinu [Get-AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS.

````powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $targetVault.ID
````

Ve výstupu se zobrazí všechny nechráněné součásti SQL napříč všemi virtuálními počítači SQL zaregistrovanými do tohoto trezoru s typem položky a servername. Můžete dál filtrovat na konkrétní virtuální počítač SQL předáním parametru-Container nebo použitím kombinace názvu a názvu serveru společně s příznakem ItemType k doručení do jedinečné položky SQL.

````powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
````

### <a name="configuring-backup"></a>Konfigurace zálohování

Teď, když máme požadovanou databázi SQL a zásadu, se kterou je potřeba zálohovat, můžeme ke konfiguraci zálohování pro tuto databázi SQL použít rutinu [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) .

````powershell
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
````

Příkaz čeká na dokončení zálohování konfigurace a vrátí následující výstup.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>Načítají se nové databáze SQL.

Jakmile je počítač zaregistrován, Služba Backup načte podrobnosti o databáze, které jsou k dispozici. Pokud uživatel přidá instance SQL databáze/SQL do registrovaného počítače později, musí ručně aktivovat službu zálohování, aby provedla nové "dotazování", aby bylo možné znovu získat všechny nechráněné databáze (včetně nově přidaných). K provedení nového dotazu použijte rutinu [Initialize-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Initialize-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS na virtuálním počítači SQL. Příkaz čeká na dokončení operace. Později pomocí rutiny [Get-AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS Získejte seznam nejnovějších nechráněných komponent SQL.

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $targetvault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID
````

Po načtení příslušných chráněných položek povolte zálohy podle pokynů v [předchozí části](#configuring-backup).
Pokud některý z nich nechce ručně zjišťovat nové databáze, můžou se rozhodnout pro automatické ochrany, jak je vysvětleno [níže](#enable-autoprotection).

## <a name="enable-autoprotection"></a>Povolit AutoProtection

Uživatel může nakonfigurovat zálohování tak, aby se všechny databáze přidané v budoucnu automaticky chránily pomocí určitých zásad. Pokud chcete povolit AutoProtection, použijte rutinu [Enable-AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS.

Vzhledem k tomu, že pokyn slouží k zálohování všech budoucích databáze, operace se provádí na úrovni SQLInstance.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $targetPolicy -VaultId $targetvault.ID
```

Po uvedení záměru AutoProtection se dotaz do počítače pro načtení nově přidaných databáze provede jako naplánovaná úloha na pozadí každých 8 hodin.

## <a name="restore-sql-dbs"></a>Obnovit databáze SQL

Azure Backup může obnovit databáze SQL Server, které běží na virtuálních počítačích Azure, takto:

1. Obnovení na konkrétní datum nebo čas (do druhé) pomocí záloh protokolu transakcí. Azure Backup automaticky určí odpovídající úplné rozdílové zálohování a řetěz záloh protokolů, které jsou nutné k obnovení na základě vybraného času.
2. Obnovení konkrétního úplného nebo rozdílového zálohování pro obnovení do konkrétního bodu obnovení.

Před obnovením SQL databáze ověřte zmíněné požadavky. [](restore-sql-database-azure-vm.md#prerequisites)

Nejdřív načtěte relevantní zálohovanou databázi SQL pomocí rutiny [Get-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
````

### <a name="fetch-the-relevant-restore-time"></a>Načíst relevantní čas obnovení

Jak je uvedeno výše, uživatel může obnovit zálohovanou databázi SQL do úplné/rozdílové kopie **nebo** do časového bodu v čase.

#### <a name="fetch-distinct-recovery-points"></a>Načíst samostatné body obnovení

Pomocí [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryPoint?view=azps-1.5.0) můžete načíst samostatné (úplné/rozdílové) body obnovení pro ZÁLOHOVANOU databázi SQL.

````powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = (Get-Date).ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -StartDate $startdate -EndDate $endDate
````

Výstup se podobá následujícímu příkladu

````powershell
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
````

K načtení relevantního bodu obnovení použijte filtr RecoveryPointId nebo filtr pole.

````powershell
$FullRP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -RecoveryPointId "6660368097802"
````

#### <a name="fetch-point-in-time-recovery-point"></a>Načíst bod obnovení v čase v čase

Pokud chce uživatel obnovit databázi k určitému časovému okamžiku, použijte rutinu [Get-AzRecoveryServicesBackupRecoveryLogChain](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryLogChain?view=azps-1.5.0) PS. Rutina vrátí seznam kalendářních dat, který představuje počáteční a koncové časy nepřerušeného a průběžného řetězce protokolu pro danou položku zálohy SQL. Požadovaný časový okamžik by měl být v tomto rozsahu.

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -Item -VaultId $targetVault.ID
```

Výstup bude vypadat podobně jako v následujícím příkladu.

````powershell
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
````

Výše uvedený výstup znamená, že uživatel může obnovit nějaký časový okamžik mezi zobrazeným časem zahájení a časem ukončení. Časy jsou v čase UTC. Vytvořte libovolný časový okamžik v čase PS, který je v rozsahu zobrazeném výše.

> [!NOTE]
> Když je pro obnovení zvolený časový okamžik v protokolu, uživatel nemusí určit výchozí bod, tj. úplné zálohování, ze kterého se databáze obnovila. Služba Azure Backup se postará o celý plán obnovení, tj., která úplná záloha má zvolit, jaké zálohy protokolů se mají použít atd.

### <a name="determine-recovery-configuration"></a>Určení konfigurace obnovení

V případě obnovení databáze SQL se podporují následující scénáře obnovení.

1. Přepsání zálohované databáze SQL pomocí dat z jiného bodu obnovení – OriginalWorkloadRestore
2. Obnova databáze SQL DB jako nové databáze ve stejné instanci SQL – AlternateWorkloadRestore
3. Obnovení databáze SQL Database jako nové databáze v jiné instanci SQL v jiném virtuálním počítači SQL – AlternateWorkloadRestore

Po načtení relevantního bodu obnovení (DISTINCT nebo log-in-time) pomocí rutiny [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS načtěte objekt konfigurace obnovení podle požadovaného plánu obnovení.

#### <a name="original-workload-restore"></a>Obnovení původního zatížení

Chcete-li přepsat zálohovanou databázi daty z bodu obnovení, stačí zadat správný příznak a příslušný bod obnovení, jak je znázorněno v následujících příkladech.

##### <a name="original-restore-with-distinct-recovery-point"></a>Původní obnovení s odlišným bodem obnovení

````powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $targetVault.ID
````

##### <a name="original-restore-with-log-point-in-time"></a>Původní obnovení s časovým bodem přihlášení

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $targetVault.ID
```

#### <a name="alternate-workload-restore"></a>Obnovení alternativní úlohy

> [!IMPORTANT]
> Zálohovaná databáze SQL Database se dá obnovit jako nová databáze jenom pro jiný SQLInstance, a to v zaregistrovaném virtuálním počítači Azure do tohoto trezoru.

Jak je uvedeno výše, pokud cílový SQLInstance leží v jiném virtuálním počítači Azure, ujistěte se, že je [zaregistrovaný do tohoto trezoru](#registering-the-sql-vm) a že se příslušné SQLInstance jeví jako chráněná položka.

````powershell
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "<SQLInstance Name>" -ServerName "<SQL VM name>" -VaultId $targetVault.ID
````

Pak stačí předávat příslušný bod obnovení a cílovou instanci SQL pomocí správného příznaku, jak je znázorněno níže.

##### <a name="alternate-restore-with-distinct-recovery-point"></a>Alternativní obnovení s odlišným bodem obnovení

````powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $targetVault.ID
````

##### <a name="alternate-restore-with-log-point-in-time"></a>Alternativní obnovení s časovým okamžikem přihlášení

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -AlternateWorkloadRestore -VaultId $targetVault.ID
```

Konečný objekt konfigurace bodu obnovení získaný z rutiny [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS obsahuje všechny relevantní informace pro obnovení a je jak vidíte níže.

````powershell
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : No
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
````

Můžete upravit pole Název obnovené databáze, OverwriteWLIfpresent, NoRecoveryMode a targetPhysicalPath. Získejte další podrobnosti o cílových cestách k souborům, jak je znázorněno níže.

````powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath

MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
````

Nastavte odpovídající vlastnosti PS jako řetězcové hodnoty, jak je znázorněno níže.

````powershell
$AnotherInstanceWithFullConfig.OverwriteWLIfpresent = "Yes"
$AnotherInstanceWithFullConfig | fl

TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : Yes
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
````

> [!IMPORTANT]
> Ujistěte se, že poslední konfigurační objekt obnovení obsahuje všechny nezbytné a správné hodnoty, protože operace obnovení bude založena na objektu konfigurace.

### <a name="restore-with-relevant-configuration"></a>Obnovit s příslušnou konfigurací

Po získání a ověření relevantního konfiguračního objektu obnovení spusťte proces obnovení pomocí rutiny [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Restore-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS.

````powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $targetVault.ID
````

Operace obnovení vrátí úlohu, která se má sledovat.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
````

## <a name="manage-sql-backups"></a>Správa záloh SQL

### <a name="on-demand-backup"></a>Zálohování na vyžádání

Po povolení zálohování databáze může uživatel také aktivovat zálohování na vyžádání pro databázi pomocí rutiny [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Backup-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS. Následující příklad spustí úplnou zálohu v databázi SQL s povolenou kompresí a úplná záloha by měla být zachována po dobu 60 dnů.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
````

Příkaz zálohování v režimu ad hoc vrátí úlohu, která se má sledovat.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
````

Pokud se výstup ztratí nebo pokud chcete získat příslušné ID úlohy, [Získejte seznam úloh](#track-azure-backup-jobs) od služby Azure Backup a potom ho sledujte a jeho podrobnosti.

### <a name="change-policy-for-backup-items"></a>Změnit zásady pro zálohované položky

Uživatel může buď upravit existující zásady, nebo změnit zásadu zálohované položky z Policy1 na Policy2. Chcete-li přepnout zásady pro zálohovanou položku, jednoduše načtěte příslušné zásady a zálohujte položku a použijte příkaz [Enable-AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) s položkou Backup jako parametr.

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType MSSQL -BackupManagementType AzureWorkload -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
````

Příkaz čeká na dokončení zálohování konfigurace a vrátí následující výstup.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="re-register-sql-vms"></a>Znovu zaregistrovat virtuální počítače SQL

> [!WARNING]
> Nezapomeňte si přečíst tento [dokument](backup-sql-server-azure-troubleshoot.md#re-registration-failures) , abyste porozuměli symptomům selhání a vyvolali před pokusem o opakovanou registraci.

Pokud chcete aktivovat opětovnou registraci virtuálního počítače SQL, načtěte příslušný kontejner zálohování a předejte ho do rutiny Register.

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Register-AzRecoveryServicesBackupContainer -Container $SQLContainer -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID
````

### <a name="stop-protection"></a>Zastavení ochrany

#### <a name="retain-data"></a>Zachovat data

Pokud si uživatel přeje zastavit ochranu, může použít rutinu [Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS. Tím se zastaví naplánovaná zálohování, ale data zálohovaná, dokud se teď neuchovávají trvale.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>Odstranit data zálohy

Pokud chcete uložená zálohovaná data z trezoru úplně odebrat, stačí přidat příznak/RemoveRecoveryPoints nebo přepnout na [příkaz "Zakázat" ochranu](#retain-data).

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

#### <a name="disable-auto-protection"></a>Zakázat automatickou ochranu

Pokud byla na SQLInstance nakonfigurována AutoProtection, uživatel ji může zakázat pomocí rutiny [Disable-AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS.

````powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetvault.ID
````

#### <a name="unregister-sql-vm"></a>Zrušení registrace virtuálního počítače SQL

Pokud [už nejsou chráněné žádné databáze SQL serveru a neexistují žádná zálohovaná data](#delete-backup-data), může uživatel z tohoto trezoru zrušit registraci virtuálního počítače SQL. Jenom pak uživatel může chránit databáze do jiného trezoru. Pomocí rutiny [Unregister-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Unregister-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS odregistrujte virtuální počítač SQL.

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $targetvault.ID
````

### <a name="track-azure-backup-jobs"></a>Sledování úloh Azure Backup

Je důležité si uvědomit, že Azure Backup jenom sleduje úlohy aktivované uživatelem v zálohování SQL. Plánované zálohy (včetně záloh protokolu) nejsou na portálu nebo PowerShellu viditelné. Pokud ale některé naplánované úlohy selžou, vygeneruje se [Výstraha zálohování](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) , která se zobrazí na portálu. Ke sledování všech naplánovaných úloh a dalších relevantních informací [použijte Azure monitor](backup-azure-monitoring-use-azuremonitor.md) .

Uživatelé mohou sledovat operace aktivované službou ad hoc/uživatelem s ID úlohy, která je vrácena ve [výstupu](#on-demand-backup) asynchronních úloh, jako je například zálohování. Pomocí rutiny [Get-AzRecoveryServicesBackupJobDetail](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJobDetail) PS můžete sledovat úlohu a její podrobnosti.

````powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $targetVault.ID
````

Pokud chcete získat seznam úloh ad hoc a jejich stavů z Azure Backup služby, použijte rutinu [Get-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS. Následující příklad vrátí všechny probíhající úlohy SQL.

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

Chcete-li zrušit probíhající úlohu, použijte rutinu [stop-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Stop-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS.

## <a name="managing-sql-always-on-availability-groups"></a>Správa skupin dostupnosti Always On SQL serveru

V případě skupin dostupnosti Always On SQL nezapomeňte [zaregistrovat všechny uzly](#registering-the-sql-vm) skupiny dostupnosti (AG). Po dokončení registrace u všech uzlů je objekt skupiny dostupnosti SQL logicky vytvořený v rámci chráněných položek. Databáze v rámci SQL AG budou uvedené jako "SQLDatabase". Uzly se zobrazí jako samostatné instance a výchozí databáze SQL, které jsou pod nimi, budou uvedeny také jako databáze SQL.

Předpokládejme například, že SQL AG má dva uzly: SQL-Server-0 a SQL-Server-1 a 1 SQL AG DB. Jakmile jsou oba tyto uzly registrovány, zobrazí uživatel [seznam chráněných položek](#fetching-sql-dbs)pomocí následujících součástí

1. Typ položky, která je chráněna objektem SQL AG jako SQLAvailabilityGroup
2. Typ položky, která je chráněná databází SQL AG DB jako SQLDatabase
3. SQL-Server-0 – chráněná typ položky jako SQLInstance
4. SQL-Server-1-chráněná typ položky jako SQLInstance
5. Všechny výchozí SQL databáze (Master, model, msdb) v rámci SQL-Server-0-Protected typu položky jako SQLDatabase
6. Všechny výchozí SQL databáze (Master, model, msdb) v části SQL-Server-Protected typ položky jako SQLDatabase

SQL-Server-0, SQL Server-1 bude při výpisu zálohovacích kontejnerů v seznamu [](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupContainer?view=azps-1.5.0)obsahovat také "AzureVMAppContainer".

Stačí načíst příslušnou databázi SQL pro [Povolení zálohování](#configuring-backup) a rutiny zálohování a [obnovení](#restore-sql-dbs) [ad hoc](#on-demand-backup) jsou identické.
