---
title: SQL DB v & zálohování virtuálních počítačích Azure přes PowerShell
description: Zálohování a obnovení databází SQL ve virtuálních počítačích Azure pomocí Azure Backup a PowerShellu.
ms.topic: conceptual
ms.date: 03/15/2019
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.openlocfilehash: 9608b02869b1d41d901ec77a42cfaa6d882040e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131815"
---
# <a name="back-up-and-restore-sql-databases-in-azure-vms-with-powershell"></a>Zálohování a obnovení databází SQL ve virtuálních počítačích Azure pomocí PowerShellu

Tento článek popisuje, jak pomocí Azure PowerShell zálohovat a obnovit SQL DB v rámci virtuálního počítače Azure pomocí trezoru [služby Azure Backup](backup-overview.md) Recovery Services.

Tento článek vysvětluje, jak:

> [!div class="checklist"]
>
> * Nastavte PowerShell a zaregistrujte zprostředkovatele služeb azure recovery services.
> * Vytvořte trezor služby Recovery Services.
> * Konfigurace zálohování pro SQL DB v rámci virtuálního počítače Azure.
> * Spusťte úlohu zálohování.
> * Obnovení zálohované databáze SQL.
> * Sledujte úlohy zálohování a obnovení.

## <a name="before-you-start"></a>Než začnete

* [Přečtěte si další informace](backup-azure-recovery-services-vault-overview.md) o trezorech služby Recovery Services.
* Přečtěte si o funkcích funkcí funkcí pro [zálohování SQL DBs v rámci virtuálních počítačích Azure](backup-azure-sql-database.md#before-you-start).
* Zkontrolujte hierarchii objektů prostředí PowerShell pro služby recovery Services.

### <a name="recovery-services-object-hierarchy"></a>Hierarchie objektů služby Recovery Services

Hierarchie objektů je shrnuta v následujícím diagramu.

![Hierarchie objektů služby Recovery Services](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Zkontrolujte odkaz [na rutinu rutiny](/powershell/module/az.recoveryservices) **Az.RecoveryServices** v knihovně Azure.

### <a name="set-up-and-install"></a>Nastavení a instalace

Nastavení prostředí PowerShell takto:

1. [Stáhněte si nejnovější verzi prostředí Az PowerShell](/powershell/azure/install-az-ps). Minimální požadovaná verze je 1.5.0.

2. Najděte rutiny Azure Backup PowerShell pomocí tohoto příkazu:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Zkontrolujte aliasy a rutiny pro Azure Backup a trezor služby Recovery Services. Zde je příklad toho, co můžete vidět. Není to úplný seznam rutin.

    ![Seznam rutin služby Obnovení](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Přihlaste se ke svému účtu Azure pomocí **služby Connect-AzAccount**.
5. Na zobrazené webové stránce budete vyzváni k zadání přihlašovacích údajů k účtu.

    * Případně můžete zahrnout pověření účtu jako parametr v rutině **Connect-AzAccount** s **-Credential**.
    * Pokud jste partner csp pracující pro tenanta, zadejte zákazníka jako tenanta pomocí jejich tenantID nebo název primární domény klienta. Příkladem je **Connect-AzAccount -Tenant** fabrikam.com.

6. Přidružte předplatné, které chcete použít, k účtu, protože účet může mít několik předplatných.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Pokud používáte Azure Backup poprvé, použijte rutinu **Register-AzResourceProvider** k registraci poskytovatele služby Azure Recovery Services s vaším předplatným.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Ověřte, zda se poskytovatelé úspěšně zaregistrovali:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. Ve výstupu příkazu ověřte, že **RegistrationState** změny **Registered**. Pokud tomu tak není, spusťte rutinu **Register-AzResourceProvider** znovu.

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Podle těchto kroků vytvořte trezor služby Recovery Services.

Trezor služby Recovery Services je prostředek Správce prostředků, takže jej musíte umístit do skupiny prostředků. Můžete použít existující skupinu prostředků nebo můžete vytvořit skupinu prostředků s rutinou **New-AzResourceGroup.** Při vytváření skupiny prostředků zadejte název a umístění skupiny prostředků.

1. Úschovna je umístěna ve skupině prostředků. Pokud nemáte existující skupinu prostředků, vytvořte novou skupinu s [new-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). V tomto příkladu vytvoříme novou skupinu prostředků v oblasti Západní USA.

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. K vytvoření úschovny použijte rutinu [New-AzRecoveryServicesVault.](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) Zadejte stejné umístění pro úschovnu, jaké bylo použito pro skupinu prostředků.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Zadejte typ redundance, který se má použít pro úložiště trezoru.

    * Můžete použít [místně redundantní úložiště](../storage/common/storage-redundancy-lrs.md) nebo [geograficky redundantní úložiště](../storage/common/storage-redundancy-grs.md).
    * Následující příklad nastaví možnost **-BackupStorageRedundancy** pro cmd[Set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) pro **testvault** nastavenou na **GeoRedundant**.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>Zobrazení trezorů v předplatném

Chcete-li zobrazit všechny trezory v předplatném, použijte [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

Výstup je podobný následujícímu. Přidružená skupina prostředků a umístění jsou k dispozici.

```output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>Nastavení kontextu úschovny

Uložte objekt úschovny do proměnné a nastavte kontext úschovny.

* Mnoho rutin Azure Backup vyžaduje jako vstup objekt trezoru služby Recovery Services, takže je vhodné uložit objekt trezoru v proměnné.
* Kontext trezoru představuje typ chráněných dat v trezoru. Nastavte ji pomocí [set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). Po nastavení kontextu se vztahuje na všechny následné rutiny.

Následující příklad nastaví kontext úložiště pro **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Načtení ID trezoru

Plánujeme zanesit nastavení kontextu trezoru v souladu s pokyny Azure PowerShellu. Místo toho můžete ID úschovny uložit nebo načíst a předat ho příslušným příkazům takto:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>Konfigurace zásad zálohování

Zásada zálohování určuje plán zálohování a jak dlouhé mají být zachovány body obnovení zálohy:

* Zásady zálohování jsou přidruženy alespoň k jedné zásadě uchovávání informací. Zásady uchovávání informací definují, jak dlouho je bod obnovení uchováván před jeho odstraněním.
* Zobrazení výchozího uchovávání zásad zálohování pomocí [objektu Get-AzRecoveryServicesBackupBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
* Zobrazení výchozího plánu zásad zálohování pomocí objektu [Get-AzRecoveryServicesBackupBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
* Rutina [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) slouží k vytvoření nové zásady zálohování. Zadejte objekty zásad plánu a uchovávání informací.

Ve výchozím nastavení je čas zahájení definován v objektu zásad plánu. Pomocí následujícího příkladu můžete změnit čas zahájení na požadovaný čas zahájení. Požadovaný čas zahájení by měl být také v UTC. Níže uvedený příklad předpokládá, že požadovaný čas zahájení je 01:00 AM UTC pro denní zálohování.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Je třeba zadat čas zahájení pouze v násobcích 30 minut. Ve výše uvedeném příkladu může být pouze "01:00:00" nebo "02:30:00". Čas zahájení nemůže být "01:15:00"

Následující příklad ukládá zásady plánu a zásady uchovávání informací v proměnných. Potom používá tyto proměnné jako parametry pro novou zásadu (**NewSQLPolicy**). **NewSQLPolicy** trvá denní "Full" zálohování, uchovává ji po dobu 180 dnů a trvá log zálohování každé 2 hodiny

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

### <a name="registering-the-sql-vm"></a>Registrace virtuálního virtuálního soudu SQL

Pro zálohování virtuálních počítačových služeb Azure a sdílené složky Azure se služba Backup může připojit k těmto prostředkům Azure Resource Manageru a načíst příslušné podrobnosti. Vzhledem k tomu, že SQL je aplikace v rámci virtuálního počítače Azure, služba Backup potřebuje oprávnění pro přístup k aplikaci a načtení potřebných podrobností. K tomu je potřeba *"zaregistrovat"* virtuální počítač Azure, který obsahuje aplikaci SQL s trezorem služeb obnovení. Jakmile zaregistrujete virtuální ho dohledu SQL s trezorem, můžete chránit SQL DBs pouze do tohoto trezoru. Pomocí rutiny [Register-AzRecoveryServicesBackupContainerContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS zaregistrujte virtuální ho.

```powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

Příkaz vrátí "záložní kontejner" tohoto prostředku a stav bude "registrován"

> [!NOTE]
> Pokud není uveden parametr force, je uživatel vyzván k potvrzení textem "Chcete zakázat ochranu tohoto kontejneru". Prosím, ignorujte tento text a řekněte "Y" pro potvrzení. Jedná se o známý problém a pracujeme na odstranění textu a požadavku na parametr síly.

### <a name="fetching-sql-dbs"></a>Načítání SQL DBs

Po dokončení registrace bude služba Backup schopna vypsat všechny dostupné součásti SQL v rámci virtuálního počítače. Chcete-li zobrazit všechny součásti SQL, které ještě chcete zálohovat do tohoto trezoru, použijte rutinu [Rutina PS Get-AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0)

```powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $targetVault.ID
```

Výstup zobrazí všechny nechráněné součásti SQL ve všech virtuálních počítačích SQL registrovaných v tomto trezoru s typem položky a názvem_serveru. Můžete dále filtrovat na konkrétní virtuální hod SQL předáním '-Container' parametr nebo použít kombinaci 'Name' a 'ServerName' spolu s ItemType příznak dorazí na jedinečnou položku SQL.

```powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
```

### <a name="configuring-backup"></a>Konfigurace zálohování

Nyní, když máme požadovanou databázi SQL DB a zásady, se kterými je třeba zálohovat, můžeme použít rutinu [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) pro konfiguraci zálohování pro tuto databázi SQL DB.

```output
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
```

Příkaz čeká na dokončení konfigurace zálohy a vrátí následující výstup.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>Načítání nových SQL DBs

Jakmile je počítač zaregistrován, služba Backup načte podrobnosti o dbs k dispozici pak. Pokud uživatel přidá SQL DBs/SQL instance do registrovaného počítače později, je třeba ručně spustit službu zálohování provést nový 'dotaz' získat všechny nechráněné DBs (včetně nově přidané) znovu. Pomocí rutiny [Inicializovat-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Initialize-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS na virtuálním počítači SQL proveďte nový dotaz. Příkaz čeká na dokončení operace. Později použijte rutinu [Ps Get-AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) k získání seznamu nejnovějších nechráněných součástí SQL

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $targetvault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID
```

Po načtení příslušných chránitelných položek povolte zálohy podle pokynů ve [výše uvedené části](#configuring-backup).
Pokud jeden nechce ručně detekovat nové DBs, mohou se rozhodnout pro automatickou ochranu, jak je vysvětleno [níže](#enable-autoprotection).

## <a name="enable-autoprotection"></a>Povolit automatickou ochranu

Uživatel může nakonfigurovat zálohování tak, aby všechny centrální hodnoty přidané v budoucnu byly automaticky chráněny určitou zásadou. Chcete-li povolit automatickou ochranu, použijte rutinu [Enable-AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS.

Vzhledem k tomu, že instrukce je zálohovat všechny budoucí DBs, operace se provádí na úrovni SQLInstance.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $NewSQLPolicy -VaultId $targetvault.ID
```

Jakmile je udělena záměr automatické ochrany, šetření do počítače načíst nově přidané DBs probíhá jako naplánované úlohy na pozadí každých 8 hodin.

## <a name="restore-sql-dbs"></a>Obnovení sql dbs

Azure Backup můžete obnovit SQL Server databází, které běží na virtuálních počítačích Azure takto:

* Obnovení na určité datum nebo čas (na druhý) pomocí záloh protokolu transakcí. Azure Backup automaticky určuje příslušné úplné rozdílové zálohy a řetězce záloh protokolu, které jsou nutné k obnovení na základě vybraného času.
* Obnovení konkrétní úplné nebo rozdílové zálohy obnovit do určitého bodu obnovení.

Zkontrolujte [požadavky](restore-sql-database-azure-vm.md#prerequisites) zde uvedené před obnovením SQL DBs.

Nejprve načíst příslušné zálohované SQL DB pomocí [Rutina Get-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
```

### <a name="fetch-the-relevant-restore-time"></a>Načtení příslušnédoby obnovení

Jak je uvedeno výše, uživatel můžete obnovit zálohované SQL DB na úplnou/rozdílovou kopii **NEBO** do bodu protokolu v čase.

#### <a name="fetch-distinct-recovery-points"></a>Načtení různých bodů obnovení

Pomocí [příkazu Get-AzRecoveryServicesRecoveryRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryPoint?view=azps-1.5.0) načíst odlišné (Full/differential) body obnovení pro zálohovanou databázi SQL DB.

```powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = (Get-Date).ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -StartDate $startdate -EndDate $endDate
```

Výstup je podobný následujícímu příkladu

```output
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
```

Použijte filtr RecoveryPointId nebo maticový filtr k načtení příslušného bodu obnovení.

```powershell
$FullRP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -RecoveryPointId "6660368097802"
```

#### <a name="fetch-point-in-time-recovery-point"></a>Bod obnovení načtení bodu v čase

Pokud chce uživatel obnovit db na určitý bod v čase, použijte rutinu [Get-AzRecoveryServicesRecoveryRecoveryLogChain PS.](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryLogChain?view=azps-1.5.0) Rutina vrátí seznam dat, která představují počáteční a koncový čas nepřerušeného souvislého řetězce protokolů pro tuto položku zálohování SQL. Požadovaný bod v čase by měl být v tomto rozsahu.

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -Item -VaultId $targetVault.ID
```

Výstup bude podobný následujícímu příkladu.

```output
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
```

Výše uvedený výstup znamená, že uživatel může obnovit libovolný bod v čase mezi zobrazeným počátečním časem a časem ukončení. Časy jsou v UTC. Vytvořte libovolný bod v čase v ps, který je v rozsahu uvedeném výše.

> [!NOTE]
> Pokud je pro obnovení vybrán bod v protokolu, uživatel nemusí zadávat počáteční bod, tj. Služba Azure Backup se postará o celý plán obnovení, tj., které úplné zálohování zvolit, jaké zálohy protokolu použít atd.

### <a name="determine-recovery-configuration"></a>Určit konfiguraci obnovení

V případě obnovení SQL DB jsou podporovány následující scénáře obnovení.

* Přepsání zálohované databáze SQL db s daty z jiného bodu obnovení – OriginalWorkloadRestore
* Obnovení databáze SQL jako nové databáze ve stejné instanci SQL – AlternateWorkloadRestore
* Obnovení DATABÁZE SQL jako nové databáze v jiné instanci SQL v jiném virtuálním virtuálním montovně SQL – alternateworkloadrestore
* Obnovení SQL DB jako soubory .bak -RestoreAsFiles

Po načtení příslušného bodu obnovení (odlišný nebo log point-in-time) použijte rutinu [Get-AzRecoveryServicesBackupRecoveryRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS pro načtení objektu konfigurace obnovení podle požadovaného plánu obnovení.

#### <a name="original-workload-restore"></a>Původní obnovení pracovního vytížení

Chcete-li přepsat zálohovanou databázi daty z bodu obnovení, zadejte správný příznak a příslušný bod obnovení, jak je znázorněno v následujícím příkladu( bodů).

##### <a name="original-restore-with-distinct-recovery-point"></a>Původní obnovení s odlišným bodem obnovení

```powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="original-restore-with-log-point-in-time"></a>Původní obnovení s bodem protokolu v čase

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $targetVault.ID
```

#### <a name="alternate-workload-restore"></a>Alternativní obnovení pracovního vytížení

> [!IMPORTANT]
> Zálohovaná DATABÁZE SQL může být obnovena jako nová DB pouze do jiné instance SQLInstance, v virtuálním počítači Azure "registrovaných" do tohoto trezoru.

Jak je uvedeno výše, pokud cílová SQLInstance leží v jiném virtuálním počítači Azure, ujistěte se, že je [registrována do tohoto trezoru](#registering-the-sql-vm) a příslušné SQLInstance se zobrazí jako chránitelné položky.

```powershell
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "<SQLInstance Name>" -ServerName "<SQL VM name>" -VaultId $targetVault.ID
```

Pak stačí předat příslušný bod obnovení, cílovou instanci SQL s pravým příznakem, jak je znázorněno níže.

##### <a name="alternate-restore-with-distinct-recovery-point"></a>Alternativní obnovení s odlišným bodem obnovení

```powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="alternate-restore-with-log-point-in-time"></a>Alternativní obnovení s bodem v protokolu

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="restore-as-files"></a>Obnovit jako soubory

Chcete-li obnovit záložní data jako soubory .bak místo databáze, zvolte možnost **Obnovit jako soubory.** Zálohovaný SQL DB lze obnovit na libovolný cílový virtuální virtuální modul, který je registrován do tohoto trezoru.

```powershell
$TargetContainer= Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName "VM name" -VaultId $vaultID
```

##### <a name="restore-as-files-with-distinct-recovery-point"></a>Obnovit jako soubory s odlišným bodem obnovení

```powershell
$FileRestoreWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-latest-full"></a>Obnovení jako soubory s bodem protokolu v čase z nejnovějšího úplného

```powershell
$FileRestoreWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-a-specified-full"></a>Obnovení jako soubory s bodem protokolu v čase ze zadaného úplného

Chcete-li poskytnout konkrétní úplný, který by měl být použit pro obnovení, použijte následující příkaz:

```powershell
$FileRestoreWithLogAndSpecificFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -FromFull $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

Konečný bod obnovení konfigurační objekt získaný z [Rutina Get-AzRecoveryServicesBackupRecoveryRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS má všechny relevantní informace pro obnovení a je znázorněno níže.

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

Můžete upravit obnovený název DB, OverwriteWLIfpresent, NoRecoveryMode a targetPhysicalPath pole. Získejte další podrobnosti o cílových cestách souborů, jak je znázorněno níže.

```powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath
```

```output
MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
```

Nastavte příslušné vlastnosti PS jako hodnoty řetězců, jak je znázorněno níže.

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
> Ujistěte se, že konečný recovery config objekt má všechny potřebné a správné hodnoty, protože operace obnovení bude založena na konfigurační objekt.

### <a name="restore-with-relevant-configuration"></a>Obnovení s příslušnou konfigurací

Po získání a ověření příslušného objektu Config pro obnovení spusťte proces obnovení pomocí rutiny [Obnovení-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Restore-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS.

```powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $targetVault.ID
```

Operace obnovení vrátí úlohu, která má být sledována.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
```

## <a name="manage-sql-backups"></a>Správa záloh SQL

### <a name="on-demand-backup"></a>Zálohování na vyžádání

Jakmile je pro db povoleno zálohování, může uživatel také aktivovat zálohu na vyžádání pro db pomocí rutiny [PS Backup-AzRecoveryServicesBackupItem.](https://docs.microsoft.com/powershell/module/az.recoveryservices/Backup-AzRecoveryServicesBackupItem?view=azps-1.5.0) Následující příklad spustí úplnou zálohu v databázi SQL DB s povolenou kompresí a úplné zálohování by mělo být zachováno po dobu 60 dnů.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

Příkaz zálohování na vyžádání vrátí úlohu, která má být sledována.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
```

Pokud dojde ke ztrátě výstupu nebo pokud chcete získat příslušné ID úlohy, [získejte seznam úloh](#track-azure-backup-jobs) ze služby Azure Backup a pak jej sledujte a jeho podrobnosti.

### <a name="change-policy-for-backup-items"></a>Změnit zásady pro položky zálohování

Uživatel může změnit existující zásady nebo zásady zálohované položky ze zásad1 na Policy2. Chcete-li přepnout zásady pro zálohovanou položku, načíst příslušné zásady a zálohovat položku a použít [příkaz Enable-AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) s položkou zálohování jako parametr.

```powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType MSSQL -BackupManagementType AzureWorkload -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
```

Příkaz čeká na dokončení konfigurace zálohy a vrátí následující výstup.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="re-register-sql-vms"></a>Opětovná registrace virtuálních virtuálních měn SQL

> [!WARNING]
> Ujistěte se, že jste si přečetli tento [dokument,](backup-sql-server-azure-troubleshoot.md#re-registration-failures) abyste pochopili příznaky selhání a příčiny před pokusem o opětovnou registraci

Chcete-li aktivovat opětovnou registraci virtuálního počítače SQL, načíst příslušný kontejner zálohování a předat jej do rutiny registru.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Register-AzRecoveryServicesBackupContainer -Container $SQLContainer -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID
```

### <a name="stop-protection"></a>Zastavení ochrany

#### <a name="retain-data"></a>Zachování dat

Pokud si uživatel přeje ochranu zastavit, může použít rutinu [PS Disable-AzRecoveryServicesBackupProtection.](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) Tím se zastaví plánované zálohování, ale data zálohovaná až dosud jsou zachována navždy.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
```

#### <a name="delete-backup-data"></a>Odstranění zálohovaných dat

Chcete-li zcela odstranit uložená záložní data v trezoru, stačí přidat příznak /přepnout na příkaz "zakázat" příznak /přepnout do [příkazu 'zakázat' ochranu](#retain-data).

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
```

#### <a name="disable-auto-protection"></a>Zakázat automatickou ochranu

Pokud byla v instanci SQLInstance nakonfigurována automatická ochrana, může ji uživatel zakázat pomocí rutiny [PS disable-AzRecoveryServicesBackupAutoProtection.](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0)

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetvault.ID
```

#### <a name="unregister-sql-vm"></a>Zrušení registrace virtuálního virtuálního montovace SQL

Pokud všechny centrální katalogy SERVERU SQL [již nejsou chráněny a neexistují žádná záložní data](#delete-backup-data), může uživatel zrušit registraci virtuálního počítače SQL z tohoto trezoru. Teprve pak může uživatel chránit centrální debs do jiného trezoru. Pomocí rutiny [Unregister-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Unregister-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS odregistrujte virtuální ho hlavního virtuálního počítače SQL.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $targetvault.ID
```

### <a name="track-azure-backup-jobs"></a>Sledování úloh Azure Backup

Je důležité si uvědomit, že Azure Backup pouze sleduje uživatele matné úlohy v zálohování SQL. Naplánované zálohy (včetně záloh protokolu) nejsou viditelné v portálu/powershellu. Pokud však všechny naplánované úlohy selžou, je generována a zobrazena na portálu [výstraha zálohování.](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) [Azure Monitor slouží](backup-azure-monitoring-use-azuremonitor.md) ke sledování všech naplánovaných úloh a dalších relevantních informací.

Uživatelé mohou sledovat operace spuštěné na vyžádání/uživatelem pomocí jobid, který je vrácen ve [výstupu](#on-demand-backup) asynchronních úloh, jako je například zálohování. Ke sledování úlohy a jejích podrobností použijte rutinu [Get-AzRecoveryServicesBackupJobDetail](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJobDetail) PS.

```powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $targetVault.ID
```

Chcete-li získat seznam úloh na vyžádání a jejich stavy ze služby Azure Backup, použijte rutinu [Rutina PS Get-AzRecoveryServicesBackupJob.](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJob?view=azps-1.5.0) Následující příklad vrátí všechny probíhající úlohy SQL.

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

Chcete-li probíhající úlohu zrušit, použijte rutinu [Ps Stop-AzRecoveryServicesBackupJob.](https://docs.microsoft.com/powershell/module/az.recoveryservices/Stop-AzRecoveryServicesBackupJob?view=azps-1.5.0)

## <a name="managing-sql-always-on-availability-groups"></a>Správa skupin sql always on dostupnost

Pro skupiny dostupnosti SQL Always On nezapomeňte [zaregistrovat všechny uzly](#registering-the-sql-vm) skupiny dostupnosti (AG). Po dokončení registrace pro všechny uzly, objekt skupiny dostupnosti SQL je logicky vytvořen pod chránitelné položky. Databáze pod SQL AG budou uvedeny jako SQLDatabase. Uzly se zobrazí jako samostatné instance a výchozí databáze SQL pod nimi budou uvedeny také jako databáze SQL.

Předpokládejme například, že SQL AG má dva uzly: SQL-server-0' a SQL-server-1 a 1 SQL AG DB. Jakmile jsou oba tyto uzly registrovány, pokud uživatel [uvádí chránitelné položky](#fetching-sql-dbs), uvádí následující součásti

* Objekt SQL AG - chránitelný typ položky jako SQLAvailabilityGroup
* SQL AG DB - chránitelný typ položky jako SQLDatabase
* sql-server-0 - chránitelný typ položky jako SQLInstance
* sql-server-1 - chránitelný typ položky jako SQLInstance
* Všechny výchozí sql dbs (master, model, msdb) pod SQL-server-0 - chránitelný typ položky jako SQLDatabase
* Všechny výchozí sql dbs (master, model, msdb) pod SQL-server-1 - chránitelný typ položky jako SQLDatabase

sql-server-0, sql-server-1 bude také uveden jako "AzureVMAppContainer" při [zálohování kontejnerů jsou uvedeny](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupContainer?view=azps-1.5.0).

Stačí načíst příslušnou databázi SQL, která [umožní zálohování](#configuring-backup) a [rutiny PS](#restore-sql-dbs) [na vyžádání](#on-demand-backup) jsou identické.
