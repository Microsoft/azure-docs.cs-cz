---
title: Poradce při potížích se zálohováním databáze serveru SQL Server
description: Poradce při potížích s informacemi pro zálohování databází SQL Serveru spuštěných na virtuálních počítačích Azure pomocí Azure Backup.
ms.topic: troubleshooting
ms.date: 06/18/2019
ms.openlocfilehash: 8d49adb0ab741903ccb2989cfeb4ceaef2e8a38d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408612"
---
# <a name="troubleshoot-sql-server-database-backup-by-using-azure-backup"></a>Poradce při potížích se zálohováním databáze serveru SQL Server pomocí služby Azure Backup

Tento článek obsahuje informace o řešení potíží pro databáze SQL Serveru spuštěné na virtuálních počítačích Azure.

Další informace o procesu zálohování a omezeních najdete [v tématu Zálohování serveru SQL Server v virtuálních počítačích Azure](sql-support-matrix.md#feature-consideration-and-limitations).

## <a name="sql-server-permissions"></a>Oprávnění serveru SQL Server

Chcete-li nakonfigurovat ochranu databáze serveru SQL Server ve virtuálním počítači, musíte do tohoto virtuálního počítače nainstalovat rozšíření **AzureBackupWindowsWorkload.** Pokud se zobrazí chyba **UserErrorSQLNoSysadminMembership**, znamená to, že instance serveru SQL Server nemá požadovaná oprávnění k zálohování. Chcete-li tuto chybu opravit, postupujte podle pokynů v [nastavení oprávnění virtuálního virtuálního soudu](backup-azure-sql-database.md#set-vm-permissions).

## <a name="troubleshoot-discover-and-configure-issues"></a>Poradce při potížích se zjišťováním a konfigurací

Po vytvoření a konfiguraci trezoru služby Recovery Services je zjišťování databází a konfigurace zálohování dvoustupňovým procesem.<br>

![sql](./media/backup-azure-sql-database/sql.png)

Pokud během konfigurace zálohování není virtuální modul SQL a jeho instance viditelné v **dbs zjišťování ve virtuálních serverech** a **konfigurovat zálohování** (viz výše obrázku) zajistěte, že:

### <a name="step-1-discovery-dbs-in-vms"></a>Krok 1: Zjišťování dbve virtuálních vás ve virtuálních discích

- Pokud virtuální počítače není uveden v seznamu zjištěných virtuálních počítače a také není registrován pro zálohování SQL v jiném úložišti, postupujte podle kroků [zálohování Discovery SQL Server.](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#discover-sql-server-databases)

### <a name="step-2-configure-backup"></a>Krok 2: Konfigurace zálohování

- Pokud je úschovna, ve které je virtuální modul SQL registrován ve stejném trezoru, který slouží k ochraně databází, postupujte podle kroků [konfigurace zálohování.](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#configure-backup)

Pokud virtuální ho virtuálního připojení SQL musí být registrovány v novém trezoru, musí být odregistrovány ze starého trezoru.  Zrušení registrace virtuálního počítače SQL z úložiště vyžaduje, aby byly chráněny všechny chráněné zdroje dat, a potom můžete zálohovaná data odstranit. Odstranění zálohovaných dat je destruktivní operace.  Po kontrole a přijmout všechna opatření k zrušení registrace virtuálního počítače SQL, zaregistrujte stejný virtuální počítače s novým trezoru a opakujte operaci zálohování.

## <a name="troubleshoot-backup-and-recovery-issues"></a>Poradce při potížích se zálohováním a obnovením  

Někdy může dojít k náhodným selháním v operacích zálohování a obnovení nebo se tyto operace mohou zaseknout. To může být způsobeno antivirovými programy na vašem virtuálním počítači. Jako osvědčený postup doporučujeme následující kroky:

1. Vylučte z antivirového skenování následující složky:

    `C:\Program Files\Azure Workload Backup` `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.WorkloadBackup.Edp.AzureBackupWindowsWorkload`

    Nahraďte `C:\` písmenem *systemdrive*.

1. Vylučte následující tři procesy spuštěné v rámci virtuálního počítače z antivirového skenování:

    - IaasWLPluginSvc.exe
    - IaasWorkloadCoordinaorService.exe
    - TriggerExtensionJob.exe

1. SQL také nabízí některé pokyny pro práci s antivirovými programy. Podrobnosti najdete v [tomto článku.](https://support.microsoft.com/help/309422/choosing-antivirus-software-for-computers-that-run-sql-server)

## <a name="error-messages"></a>Chybové zprávy

### <a name="backup-type-unsupported"></a>Typ zálohy není podporován.

| Severity | Popis | Možné příčiny | Doporučená akce |
|---|---|---|---|
| Upozornění | Aktuální nastavení pro tuto databázi nepodporují určité typy zálohování, které jsou k dispozici v přidružené zásadě. | <li>V hlavní databázi lze provést pouze úplnou operaci zálohování databáze. Rozdílové zálohování ani zálohování protokolu transakcí není možné. </li> <li>Všechny databáze v modelu jednoduché obnovení neumožňuje zálohování protokolů transakcí.</li> | Upravte nastavení databáze tak, aby byly podporovány všechny typy záloh v zásadách. Nebo změňte aktuální zásady tak, aby zahrnovaly pouze podporované typy zálohování. V opačném případě budou nepodporované typy zálohování během plánovaného zálohování přeskočeny nebo se nezdaří úloha zálohování pro zálohování na vyžádání.

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Tato databáze SQL nepodporuje požadovaný typ zálohování. | Vyvolá se v případě, že model obnovení databáze neumožňuje požadovaný typ zálohování. K chybě může dojít v následujících situacích: <br/><ul><li>Databáze, která používá jednoduchý model obnovení neumožňuje zálohování protokolu.</li><li>Rozdílové a protokolové zálohy nejsou povoleny pro hlavní databázi.</li></ul>Další podrobnosti naleznete v dokumentaci [k modelům obnovení serveru SQL Server.](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) | Pokud se zálohování protokolu nezdaří pro databázi v jednoduchém modelu obnovení, vyzkoušejte jednu z těchto možností:<ul><li>Pokud je databáze v jednoduchém režimu obnovení, zakažte zálohy protokolů.</li><li>Pomocí [dokumentace serveru SQL Server](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) změňte model obnovení databáze na úplný nebo souhrnný. </li><li> Pokud nechcete změnit model obnovení a máte standardní zásady pro zálohování více databází, které nelze změnit, ignorujte chybu. Úplné a rozdílové zálohy budou fungovat podle plánu. Zálohy protokolu budou přeskočeny, což se očekává v tomto případě.</li></ul>Pokud se jedná o hlavní databázi a nakonfigurovali jste rozdílovou zálohu nebo zálohu protokolu, použijte některý z následujících kroků:<ul><li>Pomocí portálu můžete změnit plán zásad zálohování pro hlavní databázi na plnou.</li><li>Pokud máte standardní zásady pro zálohování více databází, které nelze změnit, ignorujte chybu. Úplné zálohování bude fungovat podle plánu. Rozdílové nebo protokol zálohy se nestane, což se očekává v tomto případě.</li></ul> |
| Operace byla zrušena, protože konfliktní operace byla již spuštěna ve stejné databázi. | Podívejte se na [položku blogu o omezenízálohování a obnovení,](https://deep.data.blog/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database/) která běží souběžně.| [Ke sledování úloh zálohování použijte sql server Management Studio (SSMS).](manage-monitor-sql-database-backup.md) Po selhání konfliktní operace restartujte operaci.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Databáze SQL neexistuje. | Databáze byla odstraněna nebo přejmenována. | Zkontrolujte, zda byla databáze omylem odstraněna nebo přejmenována.<br/><br/> Pokud byla databáze omylem odstraněna, pokračujte v zálohování, obnovte databázi do původního umístění.<br/><br/> Pokud jste databázi odstranili a nepotřebujete budoucí zálohy, vyberte v trezoru služby Recovery Services **možnost Zastavit zálohování** pomocí **možnosti Zachovat záložní data** nebo Odstranit záložní **data**. Další informace naleznete v [tématu Správa a sledování zálohovaných databází serveru SQL Server](manage-monitor-sql-database-backup.md).

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Řetěz protokolů je porušený. | Databáze nebo virtuální počítače je zálohována prostřednictvím jiného řešení zálohování, které zkrátí řetěz protokolu.|<ul><li>Zkontrolujte, zda se používá jiné řešení zálohování nebo skript. Pokud ano, zastavte jiné řešení zálohování. </li><li>Pokud záloha byla záloha protokolu na vyžádání, spusťte úplnou zálohu a spusťte nový řetězec protokolů. Pro naplánované zálohy protokolů není potřeba žádná akce, protože služba Azure Backup automaticky aktivuje úplnou zálohu, aby tento problém vyřešila.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Azure Backup se nemůže připojit k instanci SQL. | Azure Backup se nemůže připojit k instanci SERVERU SQL Server. | Další podrobnosti v nabídce chyb y portálu Azure slouží k zúžení hlavních příčin. Chcete-li chybu opravit, přečtěte si [článek Řešení potíží se zálohováním SQL.](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine)<br/><ul><li>Pokud výchozí nastavení SQL nepovoluje vzdálená připojení, změňte nastavení. Informace o změně nastavení naleznete v následujících článcích:<ul><li>[MSSQLSERVER_-1](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-1-database-engine-error?view=sql-server-ver15)</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Pokud se jedná o problémy s přihlášením, opravte je pomocí těchto odkazů:<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Pro tento zdroj dat chybí první úplná záloha. | Pro databázi chybí úplná záloha. Protokol a rozdílové zálohy jsou rodiče na úplnou zálohu, takže nezapomeňte provést úplné zálohy před aktivací rozdílové nebo protokol zálohy. | Aktivujte úplné zálohování na vyžádání.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Nelze provést zálohu, protože transakční protokol pro zdroj dat je plný. | Transakční prostor protokolu databáze je plný. | Chcete-li tento problém vyřešit, naleznete v [dokumentaci k serveru SQL Server](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error). |

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Databáze se stejným názvem již existuje v cílovém umístění. | Cíl cíl obnovení cíl již má databázi se stejným názvem.  | <ul><li>Změňte název cílové databáze.</li><li>Nebo použijte možnost přepsání síly na stránce obnovení.</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Obnovení selhalo, protože databázi nejde nastavit offline. | Během obnovení je třeba cílovou databázi převést do offline. Azure Backup nemůže přenést tato data do pouzdla. | Další podrobnosti v nabídce chyb y portálu Azure slouží k zúžení hlavních příčin. Další informace najdete v [dokumentaci k SQL Serveru](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms). |

### <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Nelze najít certifikát serveru s kryptografickým otiskem na cíli. | Hlavní databáze v cílové instanci nemá platný kryptografický otisk. | Importujte platný kryptografický otisk certifikátu použitý ve zdrojové instanci do cílové instance. |

### <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Záloha protokolů použitá k obnovení obsahuje hromadně protokolované změny. Podle pokynů pro SQL ji nejde použít k zastavení v libovolném bodu v čase. | Pokud je databáze v režimu hromadného protokolu obnovení, nelze obnovit data mezi hromadně protokolovnou transakcí a další transakcí protokolu. | Zvolte jiný bod v čase pro zotavení. [Další informace](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server?view=sql-server-ver15).

### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Předvolbu zálohování pro skupinu dostupnosti SQL AlwaysOn není možné zajistit, protože některé uzly ve skupině dostupnosti nejsou zaregistrované. | Uzly potřebné k provádění záloh nejsou registrovány nebo jsou nedostupné. | <ul><li>Ujistěte se, že všechny uzly potřebné k provádění záloh této databáze jsou registrovány a v pořádku a opakujte operaci.</li><li>Změňte předvolbu zálohování pro skupinu dostupnosti sql serveru always on.</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Virtuální počítač SQL server je buď vypnutí a není přístupný pro službu Azure Backup. | Virtuální měsíč je vypnutý. | Ujistěte se, že je spuštěna instance serveru SQL Server. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Služba Azure Backup používá agenta hosta virtuálního počítače Azure k zálohování, ale agent hosta není na cílovém serveru k dispozici. | Agent hosta není povolen nebo není v pořádku. | [Nainstalujte agenta hosta virtuálního](../virtual-machines/extensions/agent-windows.md) zařízení ručně. |

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Záměr automatické ochrany byl buď odebrán, nebo již není platný. | Pokud povolíte automatickou ochranu v instanci serveru SQL Server, **konfigurace úloh zálohování** spustit pro všechny databáze v této instanci. Pokud zakážete automatickou ochranu, když jsou spuštěny úlohy, budou **probíhající** úlohy zrušeny pomocí tohoto kódu chyby. | Povolte opět automatickou ochranu a pomozte chránit všechny zbývající databáze. |

### <a name="clouddosabsolutelimitreached"></a>CloudDosAbsoluteLimitReached

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
Provoz je blokován, protože jste dosáhli limitu počtu operací povolených za 24 hodin. | Pokud dosáhnete maximálního přípustného limitu pro operaci v rozpětí 24 hodin, dojde k této chybě. <br> Například: Pokud jste dosáhli limitu pro počet úloh konfigurace zálohování, které mohou být spuštěny za den, a pokusíte se nakonfigurovat zálohování na novou položku, zobrazí se tato chyba. | Obvykle opakování operace po 24 hodinách řeší tento problém. Pokud však problém přetrvává, můžete se obrátit na podporu společnosti Microsoft s žádostí o pomoc.

### <a name="clouddosabsolutelimitreachedwithretry"></a>CloudDosAbsoluteLimitReachedWithRetry

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
Provoz je blokován, protože trezor dosáhl maximálního limitu pro tyto operace povolené v rozpětí 24 hodin. | Pokud dosáhnete maximálního přípustného limitu pro operaci v rozpětí 24 hodin, dojde k této chybě. Tato chyba obvykle přichází, pokud existují operace ve velkém měřítku, jako je například upravit zásady nebo automatickou ochranu. Na rozdíl od cloudDosAbsoluteLimitReached není moc, co můžete udělat k vyřešení tohoto stavu, ve skutečnosti služba Azure Backup bude opakovat operace interně pro všechny položky, které se ozývá.<br> Například: Pokud máte velký počet zdrojů dat chráněných zásadou a pokusíte se tuto zásadu upravit, spustí se konfigurace úloh ochrany pro každou chráněnou položku a někdy může být dosaženo maximálního povoleného limitu pro takové operace za den.| Služba Azure Backup tuto operaci automaticky zopakují po 24 hodinách.

### <a name="usererrorvminternetconnectivityissue"></a>UserErrorVMInternetConnectivityIssue

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
Virtuální počítač není schopen kontaktovat službu Azure Backup kvůli problémům s připojením k internetu. | Virtuální počítač potřebuje odchozí připojení ke službě Azure Backup Service, Azure Storage nebo službám Azure Active Directory.| - Pokud používáte nsg k omezení připojení, pak byste měli použít značku služby AzureBackup k povolování odchozího přístupu k Azure Backup do Služby Zálohování Azure, Azure Storage nebo Služby Azure Active Directory. Chcete-li [udělit](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#allow-access-using-nsg-tags) přístup, postupujte takto.<br>- Ujistěte se, že DNS řeší koncové body Azure.<br>- Zkontrolujte, jestli je virtuální virtuální měna za vyrovnáváním zatížení, který blokuje přístup k internetu. Přiřazením veřejné IP adresy k virtuálním počítačům bude zjišťování fungovat.<br>- Ověřte, že neexistuje žádný firewall / antivirus / proxy server, který blokuje volání výše uvedených tří cílových služeb.

## <a name="re-registration-failures"></a>Selhání opětovné registrace

Před spuštěním operace opětovné registrace zkontrolujte jeden nebo více následujících příznaků:

* Všechny operace (například zálohování, obnovení a konfigurace zálohování) selhávají na virtuálním počítači s jedním z následujících kódů chyb: **WorkloadExtensionNotReachable**, **UserErrorWorkloadExtensionExtensionNotInstalled**, **WorkloadExtensionNotPresent**, **WorkloadExtensionDidntDequeueMsg**.
* Pokud se v oblasti **Stav zálohování** pro položku zálohy zobrazuje **nedosažitelná**, vylučujte všechny ostatní příčiny, které mohou mít za následek stejný stav:

  * Nedostatek oprávnění k provádění operací souvisejících se zálohováním na virtuálním počítači.
  * Vypnutí virtuálního počítače, takže zálohy nemůže proběhne.
  * Problémy se sítí.

   ![opětovná registrace virtuálního mísa](./media/backup-azure-sql-database/re-register-vm.png)



* V případě skupiny dostupnosti vždy na zálohování začaly selhávat po změně předvolby zálohování nebo po převzetí služeb při selhání.

Tyto příznaky mohou vzniknout z jednoho nebo více z následujících důvodů:

* Rozšíření bylo odstraněno nebo odinstalováno z portálu.
* Rozšíření bylo odinstalováno z **Ovládacích panelů** na virtuálním počítači v části **Odinstalovat nebo změnit program**.
* Virtuální modul byl obnoven zpět v čase prostřednictvím obnovení disku na místě.
* Virtuální počítač byl vypnut na delší dobu, takže platnost konfigurace rozšíření na něm vypršela.
* Virtuální hotel byl odstraněn a jiný virtuální byl vytvořen se stejným názvem a ve stejné skupině prostředků jako odstraněný virtuální ms.
* Jeden z uzlů skupiny dostupnosti neobdržel úplnou konfiguraci zálohování. K tomu může dojít, když je skupina dostupnosti zaregistrována do trezoru nebo když je přidán nový uzel.

V předchozích scénářích doporučujeme aktivovat operaci opětovnéregistrace na virtuálním počítači. Pokyny k provedení této úlohy v PowerShellu [najdete tady.](https://docs.microsoft.com/azure/backup/backup-azure-sql-automation#enable-backup)

## <a name="size-limit-for-files"></a>Omezení velikosti souborů

Celková velikost řetězce souborů závisí nejen na počtu souborů, ale také na jejich názvech a cestách. Pro každý soubor databáze získejte název logického souboru a fyzickou cestu. Tento dotaz SQL můžete použít:

```sql
SELECT mf.name AS LogicalName, Physical_Name AS Location FROM sys.master_files mf
               INNER JOIN sys.databases db ON db.database_id = mf.database_id
               WHERE db.name = N'<Database Name>'"
```

Nyní je uspořádejte v následujícím formátu:

```json
[{"path":"<Location>","logicalName":"<LogicalName>","isDir":false},{"path":"<Location>","logicalName":"<LogicalName>","isDir":false}]}
```

Tady je příklad:

```json
[{"path":"F:\\Data\\TestDB12.mdf","logicalName":"TestDB12","isDir":false},{"path":"F:\\Log\\TestDB12_log.ldf","logicalName":"TestDB12_log","isDir":false}]}
```

Pokud velikost řetězce obsahu překročí 20 000 bajtů, jsou soubory databáze uloženy odlišně. Během obnovení nebudete moci nastavit cílovou cestu k souboru pro obnovení. Soubory budou obnoveny na výchozí cestu SQL poskytovanou serverem SQL Server.

### <a name="override-the-default-target-restore-file-path"></a>Přepsat výchozí cílovou cestu k obnovení souboru

Cestu k obnovení cíle můžete přepsat během operace obnovení umístěním souboru JSON, který obsahuje mapování databázového souboru, na cílovou cestu obnovení. Vytvořte `database_name.json` soubor a umístěte jej do umístění *C:\Program Files\Azure Workload Backup\bin\plugins\SQL*.

Obsah souboru by měl být v tomto formátu:

```json
[
  {
    "Path": "<Restore_Path>",
    "LogicalName": "<LogicalName>",
    "IsDir": "false"
  },
  {
    "Path": "<Restore_Path>",
    "LogicalName": "LogicalName",
    "IsDir": "false"
  },  
]
```

Tady je příklad:

```json
[
  {
   "Path": "F:\\Data\\testdb2_1546408741449456.mdf",
   "LogicalName": "testdb7",
   "IsDir": "false"
  },
  {
    "Path": "F:\\Log\\testdb2_log_1546408741449456.ldf",
    "LogicalName": "testdb7_log",
    "IsDir": "false"
  },  
]
```

V předchozím obsahu můžete získat logický název souboru databáze pomocí následujícího dotazu SQL:

```sql
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
  ```

Tento soubor by měl být umístěn před spuštěním operace obnovení.

## <a name="next-steps"></a>Další kroky

Další informace o virtuálních počítačích Azure Backup for SQL Server (public preview) najdete v [tématu Azure Backup for SQL VM](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup).
