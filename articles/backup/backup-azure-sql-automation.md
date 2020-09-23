---
title: DATABÁZE SQL ve službě Azure VM Backup & obnovení prostřednictvím PowerShellu
description: Zálohujte a obnovte databáze SQL ve virtuálních počítačích Azure pomocí Azure Backup a PowerShellu.
ms.topic: conceptual
ms.date: 03/15/2019
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.openlocfilehash: 37e2336b262311ea00e833ad91fe5e8c5c1ddf1e
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90975187"
---
# <a name="back-up-and-restore-sql-databases-in-azure-vms-with-powershell"></a>Zálohování a obnovení databází SQL ve virtuálních počítačích Azure pomocí PowerShellu

Tento článek popisuje, jak použít Azure PowerShell k zálohování a obnovení databáze SQL ve virtuálním počítači Azure pomocí služby [Azure Backup](backup-overview.md) Recovery Services trezoru.

V tomto článku se dozvíte, jak:

> [!div class="checklist"]
>
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

Přečtěte si referenční informace k [rutině](/powershell/module/az.recoveryservices) **AZ. RecoveryServices** v knihovně Azure.

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

9. Ve výstupu příkazu ověřte, že **RegistrationState** změny se **zaregistrují**. Pokud ne, spusťte znovu rutinu **Register-AzResourceProvider** .

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Pomocí těchto kroků můžete vytvořit trezor Recovery Services.

Recovery Services trezor je prostředek Správce prostředků, takže ho musíte umístit do skupiny prostředků. Můžete použít existující skupinu prostředků, nebo můžete vytvořit skupinu prostředků pomocí rutiny **New-AzResourceGroup** . Při vytváření skupiny prostředků zadejte název a umístění skupiny prostředků.

1. Trezor se umístí do skupiny prostředků. Pokud nemáte existující skupinu prostředků, vytvořte novou pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). V tomto příkladu vytvoříme novou skupinu prostředků v Západní USA oblasti.

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. K vytvoření trezoru použijte rutinu [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) . Zadejte stejné umístění pro trezor, které bylo použito pro skupinu prostředků.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Zadejte typ redundance, který se použije pro úložiště trezoru.

    * Můžete použít [místně redundantní](../storage/common/storage-redundancy.md#locally-redundant-storage)úložiště, [geograficky redundantní úložiště](../storage/common/storage-redundancy.md#geo-redundant-storage) nebo [redundantní úložiště zóny](../storage/common/storage-redundancy.md#zone-redundant-storage) .
    * Následující příklad nastaví možnost **-BackupStorageRedundancy** pro příkaz[set-AzRecoveryServicesBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd pro **testvault** nastavenou na geograficky **redundantní**.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>Zobrazení trezorů v předplatném

Pokud chcete zobrazit všechny trezory v rámci předplatného, použijte [příkaz Get-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault).

```powershell
Get-AzRecoveryServicesVault
```

Výstup je podobný následujícímu. K dispozici je přidružená skupina prostředků a umístění.

```output
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
* Kontext trezoru představuje typ chráněných dat v trezoru. Nastavte ji pomocí [set-AzRecoveryServicesVaultContext](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext). Po nastavení je kontext použit pro všechny následné rutiny.

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
* Podívejte se na výchozí uchování zásad zálohování pomocí [Get-AzRecoveryServicesBackupRetentionPolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject).
* Seznamte se s výchozím plánem zásad zálohování pomocí [Get-AzRecoveryServicesBackupSchedulePolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject).
* Pomocí rutiny [New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) vytvoříte nové zásady zálohování. Zadáváte objekty zásad plánování a uchovávání dat.

Ve výchozím nastavení je v objektu zásad plánování definován počáteční čas. Pomocí následujícího příkladu změňte čas spuštění na požadovaný čas zahájení. Požadovaný čas spuštění by měl být také ve formátu UTC. Následující příklad předpokládá, že požadovaný počáteční čas je 01:00 UTC pro denní zálohy.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Je nutné zadat čas spuštění pouze v 30 minutách pouze násobcích. V tomto příkladu může být pouze "01:00:00" nebo "02:30:00". Počáteční čas nemůže být "01:15:00".

V následujícím příkladu jsou uloženy zásady plánu a zásady uchovávání informací v proměnných. Pak tyto proměnné používá jako parametry pro nové zásady (**NewSQLPolicy**). **NewSQLPolicy** přijímá každodenní "úplnou" zálohu, uchovává ji po dobu 180 dnů a při každém 2 hodinách provede zálohování protokolu.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "MSSQL"
$NewSQLPolicy = New-AzRecoveryServicesBackupProtectionPolicy -Name "NewSQLPolicy" -WorkloadType "MSSQL" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Výstup je podobný následujícímu.

```output
Name                 WorkloadType       BackupManagementType BackupTime                Frequency                                IsDifferentialBackup IsLogBackupEnabled
                                                                                                                                Enabled
----                 ------------       -------------------- ----------                ---------                                -------------------- ------------------
NewSQLPolicy         MSSQL              AzureWorkload        3/15/2019 01:30:00 AM      Daily                                    False                True
```

## <a name="enable-backup"></a>Povolit zálohování

### <a name="registering-the-sql-vm"></a>Registrace virtuálního počítače SQL

Pro zálohování virtuálních počítačů Azure a sdílené složky Azure se služba Backup může připojit k těmto Azure Resource Manager prostředkům a načíst příslušné podrobnosti. Vzhledem k tomu, že SQL je aplikace na virtuálním počítači Azure, služba zálohování potřebuje oprávnění pro přístup k aplikaci a načtení potřebných podrobností. Abyste to mohli udělat, musíte *zaregistrovat* virtuální počítač Azure, který obsahuje aplikaci SQL, s úložištěm Recovery Services. Jakmile zaregistrujete virtuální počítač SQL pomocí trezoru, můžete SQL databáze chránit jenom k tomuto trezoru. K registraci virtuálního počítače použijte rutinu [Register-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/register-azrecoveryservicesbackupcontainer) prostředí PowerShell.

```powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

Příkaz vrátí kontejner záloh tohoto prostředku a stav bude zaregistrováno.

> [!NOTE]
> Pokud parametr Force není zadaný, budete vyzváni k potvrzení pomocí textu. Chcete zakázat ochranu tohoto kontejneru. Tento text prosím ignorujte a potvrďte ho tak, že zaznamenáte "Y". Jedná se o známý problém a pracujeme na odebrání textu a požadavku parametru Force.

### <a name="fetching-sql-dbs"></a>Načítá se SQL databáze.

Po dokončení registrace bude služba zálohování moct zobrazit seznam všech dostupných součástí SQL v rámci virtuálního počítače. Pokud chcete zobrazit všechny součásti SQL, které se ještě zálohují do tohoto trezoru, použijte rutinu PowerShellu [Get-AzRecoveryServicesBackupProtectableItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectableitem) .

```powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $targetVault.ID
```

Ve výstupu se zobrazí všechny nechráněné součásti SQL napříč všemi virtuálními počítači SQL zaregistrovanými do tohoto trezoru s typem položky a servername. Můžete dál filtrovat na konkrétní virtuální počítač SQL předáním parametru-Container nebo použitím kombinace názvu a názvu serveru společně s příznakem ItemType k doručení do jedinečné položky SQL.

```powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
```

### <a name="configuring-backup"></a>Konfigurace zálohování

Teď, když máme požadovanou databázi SQL a zásadu, se kterou je potřeba zálohovat, můžeme ke konfiguraci zálohování pro tuto databázi SQL použít rutinu [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) .

```output
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
```

Příkaz čeká na dokončení zálohování konfigurace a vrátí následující výstup.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>Načítají se nové databáze SQL.

Jakmile je počítač zaregistrován, Služba Backup načte podrobnosti o databáze, které jsou k dispozici. Pokud se instance SQL databáze nebo SQL přidaly do registrovaného počítače později, budete muset ručně aktivovat zálohovací službu, aby provedla nové "dotazování", aby se znovu získala **všechna** nechráněná databáze (včetně nově přidaných). K provedení nového dotazu použijte rutinu prostředí PowerShell [Initialize-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/initialize-azrecoveryservicesbackupprotectableitem) na virtuálním počítači SQL. Příkaz čeká na dokončení operace. Později pomocí rutiny PowerShellu [Get-AzRecoveryServicesBackupProtectableItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectableitem) Získejte seznam nejnovějších nechráněných komponent SQL.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $targetvault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID
```

Po načtení příslušných chráněných položek povolte zálohy podle pokynů v [předchozí části](#configuring-backup).
Pokud některý z nich nechce ručně zjišťovat nové databáze, můžou se rozhodnout pro automatické ochrany, jak je vysvětleno [níže](#enable-autoprotection).

## <a name="enable-autoprotection"></a>Povolit AutoProtection

Zálohování můžete nakonfigurovat tak, aby se všechny databáze přidané v budoucnu automaticky chránily pomocí určitých zásad. Pokud chcete povolit AutoProtection, použijte rutinu [Enable-AzRecoveryServicesBackupAutoProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupautoprotection) prostředí PowerShell.

Vzhledem k tomu, že pokyn slouží k zálohování všech budoucích databáze, operace se provádí na úrovni SQLInstance.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $NewSQLPolicy -VaultId $targetvault.ID
```

Po uvedení záměru AutoProtection se dotaz do počítače pro načtení nově přidaných databáze provede jako naplánovaná úloha na pozadí každých 8 hodin.

## <a name="restore-sql-dbs"></a>Obnovit databáze SQL

Azure Backup může obnovit databáze SQL Server, které běží na virtuálních počítačích Azure, takto:

* Obnovení na konkrétní datum nebo čas (do druhé) pomocí záloh protokolu transakcí. Azure Backup automaticky určí odpovídající úplné rozdílové zálohování a řetěz záloh protokolů, které jsou nutné k obnovení na základě vybraného času.
* Obnovení konkrétního úplného nebo rozdílového zálohování pro obnovení do konkrétního bodu obnovení.

Před obnovením SQL [here](restore-sql-database-azure-vm.md#prerequisites) databáze se podívejte na zmíněné požadavky.

Nejdřív načtěte relevantní zálohovanou databázi SQL pomocí rutiny [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) prostředí PowerShell.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
```

### <a name="fetch-the-relevant-restore-time"></a>Načíst relevantní čas obnovení

Jak je uvedeno výše, můžete obnovit zálohovanou databázi SQL do úplné/rozdílové kopie **nebo** do časového bodu v čase.

#### <a name="fetch-distinct-recovery-points"></a>Načíst samostatné body obnovení

Pomocí [Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) můžete načíst samostatné (úplné/rozdílové) body obnovení pro ZÁLOHOVANOU databázi SQL.

```powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = (Get-Date).ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -StartDate $startdate -EndDate $endDate
```

Výstup se podobá následujícímu příkladu

```output
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
```

K načtení relevantního bodu obnovení použijte filtr RecoveryPointId nebo filtr pole.

```powershell
$FullRP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -RecoveryPointId "6660368097802"
```

#### <a name="fetch-point-in-time-recovery-point"></a>Načíst bod obnovení v čase v čase

Pokud chcete obnovit databázi k určitému časovému okamžiku, použijte rutinu [Get-AzRecoveryServicesBackupRecoveryLogChain](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverylogchain) prostředí PowerShell. Rutina vrátí seznam kalendářních dat, který představuje počáteční a koncové časy nepřerušeného a průběžného řetězce protokolu pro danou položku zálohy SQL. Požadovaný časový okamžik by měl být v tomto rozsahu.

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -Item -VaultId $targetVault.ID
```

Výstup bude vypadat podobně jako v následujícím příkladu.

```output
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
```

Výše uvedený výstup znamená, že se můžete vrátit k jakémukoli bodu v čase mezi zobrazeným časem zahájení a časem ukončení. Časy jsou v čase UTC. Vytvořte všechny body v čase v prostředí PowerShell, které jsou v rozsahu uvedeném výše.

> [!NOTE]
> Když je vybraný bod protokolu pro obnovení, nemusíte zadávat výchozí bod, tedy úplnou zálohu, ze které se databáze obnovuje. Služba Azure Backup se postará o celý plán obnovení, tedy o tom, která úplná záloha má vybrat, jaké zálohy protokolů se mají použít, a tak dále.

### <a name="determine-recovery-configuration"></a>Určení konfigurace obnovení

V případě obnovení databáze SQL se podporují následující scénáře obnovení.

* Přepsání zálohované databáze SQL pomocí dat z jiného bodu obnovení – OriginalWorkloadRestore
* Obnova databáze SQL DB jako nové databáze ve stejné instanci SQL – AlternateWorkloadRestore
* Obnovení databáze SQL Database jako nové databáze v jiné instanci SQL v jiném virtuálním počítači SQL – AlternateWorkloadRestore
* Obnova databáze SQL DB jako soubory. bak – RestoreAsFiles

Po načtení relevantního bodu obnovení (DISTINCT nebo log-in-time) pomocí rutiny [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupworkloadrecoveryconfig) prostředí PowerShell načtěte objekt konfigurace obnovení podle požadovaného plánu obnovení.

#### <a name="original-workload-restore"></a>Obnovení původního zatížení

Chcete-li přepsat zálohovanou databázi daty z bodu obnovení, stačí zadat správný příznak a příslušný bod obnovení, jak je znázorněno v následujících příkladech.

##### <a name="original-restore-with-distinct-recovery-point"></a>Původní obnovení s odlišným bodem obnovení

```powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="original-restore-with-log-point-in-time"></a>Původní obnovení s časovým bodem přihlášení

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $targetVault.ID
```

#### <a name="alternate-workload-restore"></a>Obnovení alternativní úlohy

> [!IMPORTANT]
> Zálohovaná databáze SQL Database se dá obnovit jako nová databáze jenom pro jiný SQLInstance, a to v zaregistrovaném virtuálním počítači Azure do tohoto trezoru.

Jak je uvedeno výše, pokud cílové SQLInstance leží v jiném virtuálním počítači Azure, ujistěte se, že je [zaregistrované v tomto trezoru](#registering-the-sql-vm) a že se příslušné SQLInstance jeví jako chráněná položka.

```powershell
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "<SQLInstance Name>" -ServerName "<SQL VM name>" -VaultId $targetVault.ID
```

Pak stačí předávat příslušný bod obnovení a cílovou instanci SQL pomocí správného příznaku, jak je znázorněno níže.

##### <a name="alternate-restore-with-distinct-recovery-point"></a>Alternativní obnovení s odlišným bodem obnovení

```powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="alternate-restore-with-log-point-in-time"></a>Alternativní obnovení s časovým okamžikem přihlášení

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="restore-as-files"></a>Obnovit jako soubory

Chcete-li obnovit data zálohy jako soubory. bak místo databáze, vyberte možnost **Obnovit jako soubory** . Zálohovaná databáze SQL se dá obnovit na libovolný cílový virtuální počítač, který se zaregistruje do tohoto trezoru.

```powershell
$TargetContainer= Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName "VM name" -VaultId $vaultID
```

##### <a name="restore-as-files-with-distinct-recovery-point"></a>Obnovit jako soubory s odlišným bodem obnovení

```powershell
$FileRestoreWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-latest-full"></a>Obnovit jako soubory s časovým okamžikem protokolu z posledního celého

```powershell
$FileRestoreWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-a-specified-full"></a>Obnovit jako soubory s časovým okamžikem přihlášení ze zadaného rozsahu

Pokud chcete poskytnout konkrétní úplnou hodnotu, kterou byste měli použít k obnovení, použijte následující příkaz:

```powershell
$FileRestoreWithLogAndSpecificFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -FromFull $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

Konečný objekt konfigurace bodu obnovení získaný pomocí rutiny [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupworkloadrecoveryconfig) prostředí PowerShell obsahuje všechny relevantní informace pro obnovení a je znázorněno níže.

```output
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
```

Můžete upravit pole Název obnovené databáze, OverwriteWLIfpresent, NoRecoveryMode a targetPhysicalPath. Získejte další podrobnosti o cílových cestách k souborům, jak je znázorněno níže.

```powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath
```

```output
MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
```

Nastavte odpovídající vlastnosti PowerShellu jako řetězcové hodnoty, jak je znázorněno níže.

```powershell
$AnotherInstanceWithFullConfig.OverwriteWLIfpresent = "Yes"
$AnotherInstanceWithFullConfig | fl
```

```output
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
```

> [!IMPORTANT]
> Ujistěte se, že poslední konfigurační objekt obnovení obsahuje všechny nezbytné a správné hodnoty, protože operace obnovení bude založena na objektu konfigurace.

### <a name="restore-with-relevant-configuration"></a>Obnovit s příslušnou konfigurací

Po získání a ověření relevantního konfiguračního objektu obnovení spusťte proces obnovení pomocí rutiny [Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) prostředí PowerShell.

```powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $targetVault.ID
```

Operace obnovení vrátí úlohu, která se má sledovat.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
```

## <a name="manage-sql-backups"></a>Správa záloh SQL

### <a name="on-demand-backup"></a>Zálohování na vyžádání

Po povolení zálohování databáze můžete také aktivovat zálohování na vyžádání pro databázi pomocí rutiny prostředí PowerShell [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) . Následující příklad spustí úplnou zálohu v databázi SQL s povolenou kompresí a úplná záloha by měla být zachována po dobu 60 dnů.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

Příkaz zálohování na vyžádání vrátí úlohu, která se má sledovat.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
```

Pokud se výstup ztratí nebo pokud chcete získat příslušné ID úlohy, [Získejte seznam úloh](#track-azure-backup-jobs) od služby Azure Backup a potom ho sledujte a jeho podrobnosti.

### <a name="change-policy-for-backup-items"></a>Změnit zásady pro zálohované položky

Zásady zálohované položky můžete změnit z *Policy1* na *Policy2*. Chcete-li přepnout zásady pro zálohovanou položku, načtěte příslušné zásady a zálohujte položku a použijte příkaz [Enable-AzRecoveryServices](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) s položkou Backup jako parametr.

```powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType MSSQL -BackupManagementType AzureWorkload -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
```

Příkaz čeká na dokončení zálohování konfigurace a vrátí následující výstup.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="edit-an-existing-backup-policy"></a>Upravit existující zásady zálohování

Pokud chcete upravit existující zásady, použijte příkaz [set-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) .

```powershell
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $Pol -SchedulePolicy $SchPol -RetentionPolicy $RetPol
```

Po uplynutí určité doby Projděte úlohy zálohování a sledujte případné chyby. V takovém případě je potřeba problémy vyřešit. Pak znovu spusťte příkaz Upravit zásadu s parametrem **FixForInconsistentItems** a zkuste znovu upravit zásady u všech zálohovaných položek, pro které se operace dřív nezdařila.

```powershell
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $Pol -FixForInconsistentItems
```

### <a name="re-register-sql-vms"></a>Znovu zaregistrovat virtuální počítače SQL

> [!WARNING]
> Nezapomeňte si přečíst tento [dokument](backup-sql-server-azure-troubleshoot.md#re-registration-failures) , abyste porozuměli symptomům selhání a vyvolali před pokusem o opakovanou registraci.

Pokud chcete aktivovat opětovnou registraci virtuálního počítače SQL, načtěte příslušný kontejner zálohování a předejte ho do rutiny Register.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Register-AzRecoveryServicesBackupContainer -Container $SQLContainer -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID
```

### <a name="stop-protection"></a>Zastavení ochrany

#### <a name="retain-data"></a>Zachování dat

Pokud chcete zastavit ochranu, můžete použít rutinu PowerShellu [Disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) . Tím se zastaví naplánovaná zálohování, ale data zálohovaná, dokud se teď neuchovávají trvale.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
```

#### <a name="delete-backup-data"></a>Odstranění zálohovaných dat

Pokud chcete uložená zálohovaná data z trezoru úplně odebrat, stačí přidat příznak/RemoveRecoveryPoints nebo přepnout na [příkaz "Zakázat" ochranu](#retain-data).

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
```

#### <a name="disable-auto-protection"></a>Zakázat automatickou ochranu

Pokud byla na SQLInstance nakonfigurovaná AutoProtection, můžete ji zakázat pomocí rutiny [Disable-AzRecoveryServicesBackupAutoProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection) prostředí PowerShell.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetvault.ID
```

#### <a name="unregister-sql-vm"></a>Zrušení registrace virtuálního počítače SQL

Pokud už databáze SQL Server není [chráněný a žádná data zálohy neexistují](#delete-backup-data), můžete zrušit registraci virtuálního počítače SQL z tohoto trezoru. Databáze můžete chránit jenom v jiném trezoru. Pomocí rutiny [Unregister-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer) prostředí PowerShell zrušte registraci virtuálního počítače SQL.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $targetvault.ID
```

### <a name="track-azure-backup-jobs"></a>Sledování úloh Azure Backup

Je důležité si uvědomit, že Azure Backup jenom sleduje úlohy aktivované uživatelem v zálohování SQL. Plánované zálohy (včetně záloh protokolu) nejsou na portálu nebo v PowerShellu viditelné. Pokud ale některé naplánované úlohy selžou, vygeneruje se [Výstraha zálohování](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) , která se zobrazí na portálu. Ke sledování všech naplánovaných úloh a dalších relevantních informací [použijte Azure monitor](backup-azure-monitoring-use-azuremonitor.md) .

Uživatelé mohou sledovat operace aktivované na vyžádání/uživatelem s ID úlohy, která je vrácena ve [výstupu](#on-demand-backup) asynchronních úloh, jako je například zálohování. Pomocí rutiny [Get-AzRecoveryServicesBackupJobDetail](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjobdetail) prostředí PowerShell můžete sledovat úlohu a její podrobnosti.

```powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $targetVault.ID
```

Pokud chcete získat seznam úloh na vyžádání a jejich stavů z Azure Backup služby, použijte rutinu PowerShellu [Get-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) . Následující příklad vrátí všechny probíhající úlohy SQL.

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

Chcete-li zrušit probíhající úlohu, použijte rutinu prostředí PowerShell [stop-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob) .

## <a name="managing-sql-always-on-availability-groups"></a>Správa skupin dostupnosti Always On SQL serveru

V případě skupin dostupnosti Always On SQL nezapomeňte [zaregistrovat všechny uzly](#registering-the-sql-vm) skupiny dostupnosti (AG). Po dokončení registrace u všech uzlů je objekt skupiny dostupnosti SQL logicky vytvořený v rámci chráněných položek. Databáze v rámci SQL AG budou uvedené jako "SQLDatabase". Uzly se zobrazí jako samostatné instance a výchozí databáze SQL, které jsou pod nimi, budou uvedeny také jako databáze SQL.

Předpokládejme například, že SQL AG má dva uzly: *SQL-Server-0* a *SQL-Server-1* a 1 SQL AG DB. Pokud jsou oba tyto uzly registrovány, zobrazí se při [výpisu chráněných položek](#fetching-sql-dbs)následující komponenty.

* Typ položky, která je chráněna objektem SQL AG jako SQLAvailabilityGroup
* Typ položky, která je chráněná databází SQL AG DB jako SQLDatabase
* SQL-Server-0 – chráněná typ položky jako SQLInstance
* SQL-Server-1-chráněná typ položky jako SQLInstance
* Všechny výchozí SQL databáze (Master, model, msdb) v rámci SQL-Server-0-Protected typu položky jako SQLDatabase
* Všechny výchozí SQL databáze (Master, model, msdb) v části SQL-Server-Protected typ položky jako SQLDatabase

SQL-Server-0, SQL Server-1 bude při [výpisu zálohovacích kontejnerů](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer)v seznamu obsahovat také "AzureVMAppContainer".

Stačí načíst příslušnou databázi, abyste [mohli povolit zálohování](#configuring-backup) a [rutiny PowerShellu](#restore-sql-dbs) [pro zálohování a obnovení na vyžádání](#on-demand-backup) jsou identické.
