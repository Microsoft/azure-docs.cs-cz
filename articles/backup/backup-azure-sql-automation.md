---
title: 'Azure Backup: Zálohování a obnovení databází SQL na virtuálních počítačích Azure pomocí Powershellu a Azure Backup'
description: Zálohování a obnovení databází SQL na virtuálních počítačích Azure pomocí Powershellu a Azure Backup.
services: backup
author: pvrk
manager: vijayts
keywords: Azure Backup; SQL;
ms.service: backup
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pullabhk
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.openlocfilehash: 6a2e065466ab4426a6472b64fae19d264ff8dd81
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66734228"
---
# <a name="back-up-and-restore-sql-databases-in-azure--vms-with-powershell"></a>Zálohování a obnovení databází SQL na virtuálních počítačích Azure s využitím Powershellu

Tento článek popisuje, jak pomocí Azure Powershellu k zálohování a obnovení databáze SQL v rámci virtuálního počítače Azure pomocí [Azure Backup](backup-overview.md) trezor služby Recovery Services.

Tento kurz vysvětluje následující postupy:

> [!div class="checklist"]
> * Nastavení prostředí PowerShell a zaregistrujte zprostředkovatele služby Azure Recovery Services.
> * Vytvořte trezor služby Recovery Services.
> * Konfigurace zálohování pro databázi SQL ve Virtuálním počítači Azure.
> * Spuštění úlohy zálohování.
> * Obnovte zálohované databáze SQL.
> * Monitorovat zálohování a obnovení úloh.

## <a name="before-you-start"></a>Než začnete

* [Další informace](backup-azure-recovery-services-vault-overview.md) o trezory služby Recovery Services.
* Přečtěte si informace o možnosti funkce pro [zálohování databází SQL v rámci virtuálních počítačů Azure](backup-azure-sql-database.md#before-you-start).
* Projděte si hierarchie objektů prostředí PowerShell pro služby Recovery Services.

### <a name="recovery-services-object-hierarchy"></a>Hierarchie objektů Recovery Services

V následujícím diagramu je automaticky shrnutý hierarchie objektů.

![Hierarchie objektů Recovery Services](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Zkontrolujte **Az.RecoveryServices** [Reference k rutinám](/powershell/module/az.recoveryservices) odkaz v knihovně Azure.

### <a name="set-up-and-install"></a>Nastavení a instalace

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Prostředí PowerShell nastavte následujícím způsobem:

1. [Stáhněte si nejnovější verzi prostředí PowerShell Az](/powershell/azure/install-az-ps). Minimální požadovaná verze je 1.5.0.

2. Vyhledání rutin Powershellu pro zálohování Azure pomocí tohoto příkazu:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Zkontrolujte aliasů a rutin Azure Backup a trezor služby Recovery Services. Tady je příklad co můžete se setkat. Není úplný seznam rutin.

    ![Seznam rutin služby Recovery Services](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Přihlaste se ke svému účtu Azure pomocí **připojit AzAccount**.
5. Na webové stránce, která se zobrazí budete vyzváni k zadání přihlašovacích údajů k účtu.

    * Alternativně můžete zahrnout přihlašovacích údajů k účtu jako parametr v **AzAccount připojit** rutinu s **-Credential**.
    * Pokud jste partner CSP, který spolupracuje pro tenanta, zadejte jako tenant, pomocí názvu primární doména tenanta nebo ID Tenanta zákazníka. Příkladem je **Connect AzAccount-Tenanta** fabrikam.com.

6. Přidružte předplatné, které chcete používat s účtem, protože účet může mít několik předplatných.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Pokud používáte Azure Backup poprvé, použijte **Register-AzResourceProvider** rutiny zaregistrujte zprostředkovatele služby Azure Recovery Services s vaším předplatným.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Ověření se úspěšně zaregistrovala zprostředkovatele:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. Ve výstupu příkazu ověřte, že **RegistrationState** změny **registrované**. Pokud ne, spusťte **Register-AzResourceProvider** rutinu znovu.

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Postupujte podle těchto kroků a vytvořte trezor služby Recovery Services.

Trezor služby Recovery Services je prostředek Resource Manageru, proto je nutné umístit v rámci skupiny prostředků. Můžete použít existující skupinu prostředků, nebo můžete vytvořit skupinu prostředků pomocí **New-AzResourceGroup** rutiny. Když vytvoříte skupinu prostředků, zadejte název a umístění pro skupinu prostředků.

1. Trezor je umístěn ve skupině prostředků. Pokud nemáte existující prostředek skupiny, vytvořte si novou [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). V tomto příkladu vytvoříme novou skupinu prostředků v oblasti západní USA.

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. Použití [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) rutina pro vytvoření trezoru. Určení stejného umístění trezoru, protože byl použit pro skupinu prostředků.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Zadejte typ redundance úložiště trezoru používat.

    * Můžete použít [místně redundantní úložiště](../storage/common/storage-redundancy-lrs.md) nebo [geograficky redundantní úložiště](../storage/common/storage-redundancy-grs.md).
    * Následující příklad nastaví **- BackupStorageRedundancy** možnost[Set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd pro **testvault** nastavena na  **GeoRedundant**.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>Zobrazit tyto trezory v rámci předplatného

Chcete-li zobrazit všechny trezorů v předplatném, použijte [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

Výstup je podobný následujícímu. Skupiny prostředků a umístění jsou k dispozici.

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

* Řada rutin služby Azure Backup vyžadují objekt trezoru služby Recovery Services jako vstup, proto je vhodné uložit objekt trezoru v proměnné.
* Kontext trezoru představuje typ chráněných dat v trezoru. Nastavte ho pomocí [Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). Po nastavení kontextu platí pro všechny další rutiny.

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

* Zásady zálohování je přidružená aspoň jednu zásadu uchovávání informací. Zásady uchovávání informací Určuje, jak dlouho bod obnovení je zachována před odstraněním.
* Zobrazení výchozí zásady zálohování uchovávání informací pomocí [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
* Zobrazení výchozí zásady zálohování plán pomocí [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
* Můžete použít [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) rutina pro vytvoření nové zásady zálohování. Můžete zadat plán a uchovávání objekty zásad.

Následující příklad ukládá v proměnné plán zásady a zásady uchovávání informací. Poté použije tyto proměnné jako parametry pro novou zásadu (**NewSQLPolicy**). **NewSQLPolicy** Pořídí zálohu denní "Full", umožňuje je uchovat po dobu 180 dnů a trvá zálohu protokolu každé 2 hodiny

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
NewSQLPolicy         MSSQL              AzureWorkload        3/15/2019 9:00:00 PM      Daily                                    False                True
```

## <a name="enable-backup"></a>Povolit zálohování

### <a name="registering-the-sql-vm"></a>Registrace virtuálních počítačů SQL

Služba Backup pro zálohy virtuálních počítačů Azure a sdílených složek Azure můžete připojit k tyto prostředky Azure Resource Manageru a načíst související podrobnosti o. Vzhledem k tomu, že SQL je aplikace v rámci virtuálního počítače Azure, služba Backup potřebuje oprávnění pro přístup k aplikaci a načíst potřebné podrobnosti. Abyste to mohli udělat, budete muset *"zaregistrovat"* virtuálního počítače Azure, která obsahuje aplikaci SQL s trezorem Recovery services. Po registraci virtuálního počítače s SQL s trezorem, budete moci chránit databáze SQL pro tento trezor pouze. Použití [Register-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS rutiny k registraci virtuálního počítače.

````powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
````

Příkaz vrátí "záložní kontejner" tohoto prostředku a stav bude "registrováno"

> [!NOTE]
> Pokud tento parametr není zadaný parametr force, uživateli se zobrazí výzva k potvrzení s textem "Opravdu chcete zakázat ochranu pro tento kontejner". Ignorovat tento text a říct "Y" k potvrzení. Jedná se o známý problém a pracujeme na odstranění textu a požadavek pro parametr force

### <a name="fetching-sql-dbs"></a>Načítají se databáze SQL

Po dokončení registrace služby Backup budou moci zobrazit všechny dostupné součásti SQL na virtuálním počítači. Chcete-li zobrazit všechny součásti SQL, ale mají být zazálohovány pro tento trezor použít [Get-AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) rutiny PS

````powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $targetVault.ID
````

Výstup bude zobrazovat všechny součásti nechráněné SQL ve všech virtuálních počítačích SQL zaregistrovali k tomuto trezoru s typem položky a ServerName. Můžete dále filtrovat na konkrétní virtuální počítač SQL předání "-kontejneru ' parametru nebo použijte kombinaci"Name"a"Název_serveru"spolu s typem ItemType příznak můžete přejít na položku jedinečný SQL.

````powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
````

### <a name="configuring-backup"></a>Konfigurace zálohování

Teď, když máme požadované SQL DB a zásady se kterému je možné zálohovat, můžeme použít [povolit AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) rutiny můžete nakonfigurovat zálohy pro tuto databázi SQL.

````powershell
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
````

Příkaz počká, až se dokončí konfigurace zálohování a vrátí následující výstup.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>Načítají se nová databáze SQL

Po registraci počítače služba Backup se načíst podrobnosti o databází, které jsou pak k dispozici. Pokud uživatel přidá instancí SQL databází/SQL registrované počítač později, třeba ručně aktivujete službu zálohování a proveďte čerstvé "dotaz" Chcete-li získat nechráněných databází (včetně nově přidané) znovu. Použití [inicializace AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Initialize-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS rutiny na virtuálním počítači SQL k provedení nový dotaz. Příkaz čeká na dokončení operace. Později použít [Get-AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS rutiny pro získání seznamu součástí nejnovější nechráněné SQL

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $targetvault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID
````

Po načtení relevantní chránitelné položky povolit zálohování podle pokynů v tématu [nad části](#configuring-backup).
Pokud jeden není zapotřebí ručně zjišťovat nové databáze, jak je vysvětleno, se můžou rozhodnout pro autoprotection, kterou [níže](#enable-autoprotection).

## <a name="enable-autoprotection"></a>Povolit autoprotection, kterou

Uživatele můžete nakonfigurovat zálohování, tak, že všechny databáze v budoucnu přidá automaticky chráněné pomocí určitých zásad. Chcete-li povolit autoprotection, použijte [povolit AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS rutiny.

Protože pokyn k zálohování všech budoucích databází, operace se provádí na SQLInstance úrovně.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $targetPolicy -VaultId $targetvault.ID
```

Po záměr autoprotection, kterou je uveden, dotaz k počítači, který chcete načíst nově přidaná databáze se provádí jako úlohy naplánované na pozadí každých 8 hodin.

## <a name="restore-sql-dbs"></a>Obnovení databáze SQL

Azure Backup můžete obnovit databáze systému SQL Server, na kterých běží na virtuálních počítačích Azure následujícím způsobem:

1. Obnovte na konkrétní datum nebo čas (sekundy) pomocí zálohování transakčního protokolu. Azure Backup automaticky určuje odpovídající úplné rozdílové zálohy a řetězec zálohy protokolu, které jsou nutné k obnovení na základě vybraného času.
2. Obnovte konkrétní úplné nebo rozdílové zálohy obnovit na konkrétní bod obnovení.

Zkontrolujte požadavky uvedené [tady](restore-sql-database-azure-vm.md#prerequisites) před obnovením databází SQL.

Nejprve načtou se příslušné zálohovány pomocí SQL DB [Get-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS rutiny.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
````

### <a name="fetch-the-relevant-restore-time"></a>Relevantní obnovení čas načtení

Jak je uvedeno výš, uživatel může obnovit zálohovanou databázi SQL úplné nebo rozdílové kopírování **nebo** k protokolu v daném okamžiku.

#### <a name="fetch-distinct-recovery-points"></a>Načíst body obnovení distinct

Použití [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryPoint?view=azps-1.5.0) načíst různých bodů obnovení (úplné nebo rozdílové) zálohování SQL Database.

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

Použijte filtr "RecoveryPointId" nebo filtr pole k načtení bod obnovení relevantní.

````powershell
$FullRP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -RecoveryPointId "6660368097802"
````

#### <a name="fetch-point-in-time-recovery-point"></a>Načtou se bod obnovení bodu v čase

Pokud uživatel chce obnovit databáze na určité v daném okamžiku, použijte [Get-AzRecoveryServicesBackupRecoveryLogChain](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryLogChain?view=azps-1.5.0) PS rutiny. Rutina vrátí seznam dat, které představují počátečním a koncovým časem řetězce nepřerušený, průběžné protokolu pro danou položku Zálohování SQL. Požadovanou v daném okamžiku by měla být v tomto rozsahu.

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -Item -VaultId $targetVault.ID
```

Výstup bude vypadat podobně jako v následujícím příkladu.

````powershell
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
````

Ve výstupu výše znamená, že tento uživatel můžete obnovit k jakékoli v daném okamžiku mezi zobrazené počáteční čas a koncový čas. Časy jsou ve formátu UTC. Vytvořte libovolný bod za běhu v PS, který je v rozsahu uvedené výše.

> [!NOTE]
> Protokolu v daném okamžiku vybrány pro obnovení, když uživatel nemusí určovat tj počáteční bod, úplná záloha, ze kterého se obnoví databáze. Služba Azure Backup se postará o plánu obnovení celého tj, které úplné zálohování zvolte, co protokolu zálohování použít atd.

### <a name="determine-recovery-configuration"></a>Určení konfigurace obnovení

V případě obnovení databáze SQL, které jsou podporovány následující scénáře obnovení.

1. Přepsání zálohovanou databázi SQL s daty z jiného bodu obnovení – OriginalWorkloadRestore
2. Obnovení databáze SQL jako nové databáze v instanci SQL - AlternateWorkloadRestore
3. Obnovení databáze SQL jako nové databáze v jiné instanci SQL, do jiného virtuálního počítače SQL – AlternateWorkloadRestore

Po načítání bod obnovení odpovídající (distinct nebo protokolování bodu v čase), použijte [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS rutina pro načtení konfiguračního objektu obnovení podle plánu obnovení požadované.

#### <a name="original-workload-restore"></a>Původní úlohy obnovení

Pokud chcete přepsat zálohovanou databázi s daty z bodu obnovení, stačí zadejte příznak správné a relevantní obnovení bodu jak je znázorněno v následující příklady () dotazů.

##### <a name="original-restore-with-distinct-recovery-point"></a>Původní obnovení provést pomocí různých bod obnovení

````powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $targetVault.ID
````

##### <a name="original-restore-with-log-point-in-time"></a>Původní obnovení protokolu v daném okamžiku

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $targetVault.ID
```

#### <a name="alternate-workload-restore"></a>Alternativní úlohy obnovení

> [!IMPORTANT]
> Zálohované databáze SQL můžete obnovit jako nové databáze do jiné SQLInstance pouze ve virtuálním počítači Azure zaregistrovali k tomuto trezoru.

Jak je uvedeno výš, pokud cíl SQLInstance spočívá v rámci jiného virtuálního počítače Azure, ujistěte se, že je [zaregistrovali k tomuto trezoru](#registering-the-sql-vm) a relevantní SQLInstance se zobrazí jako chránitelnou položku.

````powershell
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "<SQLInstance Name>" -ServerName "<SQL VM name>" -VaultId $targetVault.ID
````

Potom stačí pouze předáte bod obnovení relevantní, cílová instance SQL s příznakem správné jak je znázorněno níže.

##### <a name="alternate-restore-with-distinct-recovery-point"></a>Alternativní obnovení provést pomocí různých bod obnovení

````powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $targetVault.ID
````

##### <a name="alternate-restore-with-log-point-in-time"></a>Alternativní obnovení protokolu v daném okamžiku

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -AlternateWorkloadRestore -VaultId $targetVault.ID
```

Objekt konfigurace bodu konečného získané z [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS rutiny má všechny relevantní informace pro obnovení a jak je znázorněno níže.

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

Můžete upravit obnovenou pole názvu, OverwriteWLIfpresent, NoRecoveryMode a targetPhysicalPath DB. Získání dalších podrobností pro cesty cílových souborů, jak je znázorněno níže.

````powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath

MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
````

Relevantní vlastnosti PS nastavte jako řetězcové hodnoty, jak je znázorněno níže.

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
> Ujistěte se, že konečného konfiguračního objektu má všechny nezbytné správné hodnoty a vzhledem k tomu, že operaci obnovení bude vycházet z konfiguračního objektu.

### <a name="restore-with-relevant-configuration"></a>Obnovit pomocí položky konfigurace

Jakmile získat objekt relevantní obnovení konfigurace a ověření použijte [obnovení AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Restore-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS rutiny pro zahájení procesu obnovení.

````powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $targetVault.ID
````

Operace obnovení vrací úlohu pro sledování.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
````

## <a name="manage-sql-backups"></a>Správa záloh SQL

### <a name="on-demand-backup"></a>Zálohování na vyžádání

Po zálohování se povolila pro databázi, uživatele můžete také spustit zálohu na vyžádání s využitím databáze [zálohování AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Backup-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS rutiny. Následující příklad spustí úplné zálohy v databázi SQL s komprese zapnuta a úplné zálohy se uchovávají po dobu 60 dnů.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
````

Příkaz zálohování ad hoc vrací úlohu pro sledování.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
````

Pokud dojde ke ztrátě výstup, nebo pokud chcete získat příslušné ID úlohy [získat seznam úloh](#track-azure-backup-jobs) ze zálohy Azure služby a sledovat ho a její podrobnosti.

### <a name="change-policy-for-backup-items"></a>Změna zásad pro zálohování položek

Uživatele můžete upravit existující zásadu nebo změnit zásady zálohovanou položku z Zásada1 na Policy2. Přepnout zásady pro zálohovanou položku, jednoduše načíst příslušné zásady zálohování položek a použít [povolit AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) příkaz zálohovaná položka jako parametr.

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType MSSQL -BackupManagementType AzureWorkload -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
````

Příkaz počká, až se dokončí konfigurace zálohování a vrátí následující výstup.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="re-register-sql-vms"></a>Znovu zaregistrovat virtuální počítače s SQL

> [!WARNING]
> Nezapomeňte si přečíst toto [dokumentu](backup-sql-server-azure-troubleshoot.md#re-registration-failures) o příznacích a způsobí, že před pokusem o opětovnou registraci

K aktivaci opětovnou registraci virtuálního počítače SQL, načtení relevantní záložní kontejner a předejte jej do rutinu register.

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Register-AzRecoveryServicesBackupContainer -Container $SQLContainer -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID
````

### <a name="stop-protection"></a>Zastavení ochrany

#### <a name="retain-data"></a>Zachovat data

Pokud si uživatel přeje ukončit ochranu, můžete použít [zakázat AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS rutiny. Tento kód přestane plánovaného zálohování, ale data zálohována nahoru, až nyní se uchovávají navždy.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>Odstranění zálohovaných dat

Pokud chcete úplně odebrat uložených dat záloh v trezoru, stačí přidat elementy "-RemoveRecoveryPoints' příznak/přepnout na [zakázat ochranu příkaz](#retain-data).

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

#### <a name="disable-auto-protection"></a>Zakažte automatickou ochranu

Pokud autoprotection, kterou jste nakonfigurovali na SQLInstance, můžete se zakázání uživatele pomocí [zakázat AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS rutiny.

````powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetvault.ID
````

#### <a name="unregister-sql-vm"></a>Zrušit registraci virtuálního počítače SQL

Pokud všechny databáze systému SQL server [už jsou chráněné a ne zálohování dat existuje](#delete-backup-data), uživatel může zrušit registraci virtuálního počítače SQL v tomto trezoru. Teprve pak mohl uživatel chránit databáze u jiného trezoru. Použití [Unregister-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Unregister-AzRecoveryServicesBackupContainer?view=azps-1.5.0) rutiny PS se zrušit registraci virtuálního počítače SQL.

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $targetvault.ID
````

### <a name="track-azure-backup-jobs"></a>Sledování úloh Azure Backup

Je důležité si uvědomit, že Azure Backup pouze sleduje uživatel aktivuje úlohy v zálohování SQL. Naplánované zálohy (včetně záloh protokolu), se nezobrazí v portálu nebo powershellu. Nicméně pokud všechny naplánované úlohy nezdaří, [zálohování upozornění](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) vygeneruje a zobrazí na portálu. [Použití Azure monitoru](backup-azure-monitoring-use-azuremonitor.md) ke sledování všech plánovaných úloh a další relevantní informace.

Uživatelé mohou sledovat ad hoc/uživatel aktivuje operace s ID úlohy, která je vrácena v [výstup](#on-demand-backup) asynchronních úloh, jako je zálohování. Použití [Get-AzRecoveryServicesBackupJobDetail](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJobDetail) PS rutiny pro sledování úloh a její podrobnosti.

````powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $targetVault.ID
````

Pokud chcete získat seznam úlohy ad hoc a jejich stavy ze služby Azure Backup, použijte [Get-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS rutiny. Následující příklad vrátí všechny probíhající úlohy SQL.

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

Chcete-li zrušit probíhající úlohy, použijte [Stop-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Stop-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS rutiny.

## <a name="managing-sql-always-on-availability-groups"></a>Správa vždy na skupinu dostupnosti SQL

Pro SQL skupin dostupnosti Always On, ujistěte se, že k [zaregistrovat na všech uzlech](#registering-the-sql-vm) skupiny dostupnosti (skupina dostupnosti). Po dokončení registrace pro všechny uzly objektu skupiny dostupnosti SQL logicky vytvořené v rámci chránitelné položky. Zobrazí se jeho databáze skupiny dostupnosti SQL jako "SQLDatabase". Uzly se zobrazí jako samostatné instance a databáze SQL výchozí pod nimi bude uvedená jako i databází SQL.

Například předpokládejme skupiny dostupnosti SQL má dva uzly: "sql-server-0" a "sql-server-1" a 1 AG databáze SQL. Jakmile oba tyto uzly jsou registrovány, pokud uživatel [zobrazí seznam chránitelných položek](#fetching-sql-dbs), obsahuje následující součásti

1. Objekt skupiny dostupnosti SQL - chránitelné položky typu jako SQLAvailabilityGroup
2. Skupiny dostupnosti SQL DB – typ chránitelnou položku jako SQL Database
3. SQL-server-0 - chránitelnou položku zadejte jako SQLInstance
4. SQL-server-1 - chránitelnou položku zadejte jako SQLInstance
5. Žádné výchozí databáze SQL (master, model, msdb) v části sql-server-0 - chránitelnou položku zadejte jako SQL Database
6. Žádné výchozí databáze SQL (master, model, msdb) v části sql-server-1 - chránitelnou položku zadejte jako SQL Database

SQL-server-0, 1 server pro sql budou také uvedené jako "AzureVMAppContainer" při [zálohování kontejnery jsou uvedeny](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupContainer?view=azps-1.5.0).

Načíst jenom příslušnou databázi SQL do [povolit zálohování](#configuring-backup) a [zálohování ad hoc](#on-demand-backup) a [obnovení rutiny PS](#restore-sql-dbs) jsou identické.
