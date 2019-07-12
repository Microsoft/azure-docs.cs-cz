---
title: Řešení potíží se zálohováním databáze systému SQL Server s využitím Azure Backup | Dokumentace Microsoftu
description: Informace o odstraňování potíží pro zálohování databází systému SQL Server běžící na virtuálních počítačích Azure s Azure Backup.
services: backup
author: anuragm
manager: sivan
ms.service: backup
ms.topic: article
ms.date: 06/18/2019
ms.author: anuragm
ms.openlocfilehash: b2822a3c7dfa23065f2cbd35ef4e506efae026f2
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704845"
---
# <a name="troubleshoot-sql-server-database-backup-by-using-azure-backup"></a>Řešení potíží se zálohováním databáze systému SQL Server s využitím Azure Backup

Tento článek obsahuje informace o odstraňování potíží pro databáze systému SQL Server běžící na virtuálních počítačích Azure.

Další informace o procesu zálohování a omezení, najdete v části [zálohování serveru SQL Server na virtuálních počítačích Azure](backup-azure-sql-database.md#feature-consideration-and-limitations).

## <a name="sql-server-permissions"></a>Oprávnění SQL serveru

Chcete-li konfigurovat ochranu pro databázi serveru SQL Server na virtuálním počítači, musíte nainstalovat **AzureBackupWindowsWorkload** rozšíření na tomto virtuálním počítači. Pokud se zobrazí chyba **UserErrorSQLNoSysadminMembership**, to znamená, že vaše instance systému SQL Server nemá požadovaná oprávnění pro zálohování. Chcete-li tuto chybu opravit, postupujte podle kroků v [virtuálního počítače nastavte oprávnění](backup-azure-sql-database.md#set-vm-permissions).

## <a name="error-messages"></a>Chybové zprávy

### <a name="backup-type-unsupported"></a>Nepodporovaný typ zálohování

| severity | Popis | Možné příčiny | Doporučená akce |
|---|---|---|---|
| Upozornění | Aktuální nastavení pro tuto databázi nepodporují některé typy zálohování v přidružené zásady. | <li>Pouze úplné zálohování lze provést v hlavní databázi. Rozdílová záloha ani zálohování protokolu transakcí není možné. </li> <li>Všechny databáze v jednoduchém modelu obnovení není povoleno pro zálohování protokolů transakcí.</li> | Změňte nastavení databáze tak, aby se podporují všechny typy zálohování v zásadách. Nebo změňte aktuální zásady zahrnout pouze podporované typy zálohování. V opačném případě se přeskočí nepodporované typy zálohování během naplánovaného zálohování nebo se nezdaří úlohy zálohování ad hoc záloha.


### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Tato databáze SQL nepodporuje požadovaný typ zálohy. | Nastane, pokud je model obnovení databáze neumožňuje požadovaný typ zálohy. K chybě může dojít v následujících situacích: <br/><ul><li>Databázi, která se používá jednoduchý model obnovení není povolena zálohy protokolu.</li><li>Rozdílové zálohy a zálohy protokolů nejsou povolené pro hlavní databázi.</li></ul>Další podrobnosti najdete [modelů obnovení systému SQL Server](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) dokumentaci. | Pokud se nezdaří zálohování protokolu pro databázi v jednoduchém modelu obnovení, zkuste použijte jeden z těchto možností:<ul><li>Pokud se databáze nachází v režimu jednoduchého obnovení, zakažte zálohy protokolu.</li><li>Použití [dokumentaci k SQL serveru](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) změnit model obnovení databáze na úplný nebo hromadně protokolované. </li><li> Pokud nechcete změnit model obnovení, a vy musíte standardní zásady zálohování více databází, které nelze změnit, chybu ignorujte. Úplné a rozdílové zálohy budou fungovat podle plánu. Zálohy protokolů se přeskočí, který se očekává v tomto případě.</li></ul>Pokud je hlavní databázi a nakonfigurovali rozdílové nebo zálohování protokolů, použijte některou z následujících kroků:<ul><li>Chcete-li změnit plán zálohování zásad pro hlavní databázi, na hodnotu full pomocí portálu.</li><li>Pokud máte standardní zásady zálohování více databází, které nelze změnit, chybu ignorujte. Úplné zálohování bude fungovat podle plánu. Protokolu nebo rozdílové zálohy se neprovede, který se očekává v tomto případě.</li></ul> |
| Operace zrušena, protože konfliktní operace již byla spuštěna ve stejné databázi. | Zobrazit [blogu o omezení zálohování a obnovení](https://blogs.msdn.microsoft.com/arvindsh/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database) , které běží souběžně.| [Monitorování úlohy zálohování pomocí služby SQL Server Management Studio (SSMS)](manage-monitor-sql-database-backup.md). Jakmile se konfliktní operace nepovede, restartujte operaci.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Databáze SQL neexistuje. | Databáze byla odstraněna nebo přejmenována. | Zaškrtněte, pokud byla databáze omylem odstraněna nebo přejmenována.<br/><br/> Pokud byla databáze vymazána omylem, chcete-li pokračovat v zálohování, obnovení databáze do původního umístění.<br/><br/> Pokud jste odstranili databázi a není nutné budoucí zálohy v trezoru služby Recovery Services vyberte **Zastavit zálohování** s **zachovat zálohovaná Data** nebo **odstranit zálohovaná Data**. Další informace najdete v tématu [sledování a Správa databází SQL serveru zálohovaných](manage-monitor-sql-database-backup.md).

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Řetěz protokolů je porušený. | Databáze nebo virtuální počítač zálohovaný do jiné řešení zálohování, která ořízne řetězec protokolu.|<ul><li>Zkontrolujte, zda jiné řešení zálohování nebo skriptu se používá. Pokud ano, přestat jiné řešení zálohování. </li><li>Pokud bylo zálohování zálohování ad hoc protokolu, aktivujte úplné zálohování spustit nový řetězec protokolu. Protokol naplánované zálohování není vyžadována žádná akce, protože služba Azure Backup automaticky spustí úplné zálohování, chcete-li vyřešit tento problém.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Azure Backup se nemůže připojit k instanci serveru SQL. | Azure Backup se nemůže připojit k instanci systému SQL Server. | Pomocí další podrobnosti v nabídce Azure portal chyba zúžit hlavní příčiny. Odkazovat na [SQL odstraňování potíží se zálohováním](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine) oprava chyby.<br/><ul><li>Pokud výchozí nastavení SQL neumožňuje vzdálená připojení, změňte nastavení. Naleznete v následujících článcích pro informace o změně nastavení:<ul><li>[MSSQLSERVER_-1](/previous-versions/sql/sql-server-2016/bb326495(v=sql.130))</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Pokud dojde k problémům přihlášení, použijte tyto odkazy a opravte je:<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Pro tento zdroj dat chybí první úplná záloha. | Chybí úplné zálohování pro databázi. Protokol a rozdílové zálohy rodičům, aby úplné zálohy, takže je potřeba provést úplnou zálohu před aktivací rozdílové nebo zálohu protokolu. | Aktivujte ad hoc úplné zálohování.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Zálohu nejde vytvořit, protože transakční protokol pro zdroj dat je plný. | Místa transakčního protokolu databáze je plná. | Chcete-li opravit tento problém, přečtěte si [dokumentaci k SQL serveru](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error). |

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Databáze se stejným názvem již existuje v cílovém umístění | Cíl obnovení cíl už obsahuje databáze se stejným názvem.  | <ul><li>Změňte název cílové databáze.</li><li>Nebo můžete použít možnost vynucení přepsání na stránce obnovení.</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Obnovení selhalo, protože databázi nejde nastavit offline. | Když provádíte obnovení, cílová databáze je potřeba uvést do offline režimu. Azure Backup nelze uvést do režimu offline tato data. | Pomocí další podrobnosti v nabídce Azure portal chyba zúžit hlavní příčiny. Další informace najdete v tématu [dokumentaci k SQL serveru](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms). |

###  <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Nejde najít server certifikát s kryptografickým otiskem na cíli. | Hlavní databáze v cílové instanci nemá platný šifrovací kryptografického otisku. | Importujte platný kryptografický otisk používá v instanci zdroje v cílové instanci. |

### <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Záloha protokolů použitá k obnovení obsahuje hromadně protokolované změny. Podle pokynů pro SQL ji nejde použít k zastavení v libovolném bodu v čase. | Pokud je databáze v režimu hromadně protokolovaného obnovení, data mezi hromadně protokolovaného transakce a další protokolu transakcí nelze obnovit. | Vyberte jiný bod v čase pro obnovení. [Další informace](https://docs.microsoft.com/previous-versions/sql/sql-server-2008-r2/ms186229(v=sql.105)).


### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Předvolbu zálohování pro skupinu dostupnosti SQL AlwaysOn není možné zajistit, protože některé uzly ve skupině dostupnosti nejsou zaregistrované. | Uzly, které jsou potřebné k zálohování není zaregistrované, nebo nejsou dostupné. | <ul><li>Ujistěte se, že všechny uzly potřebné k zálohování této databáze jsou zaregistrovaná a v pořádku a pak zkuste operaci zopakovat.</li><li>Změňte předvolby zálohování pro skupiny dostupnosti AlwaysOn SQL serveru.</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Virtuální počítač s SQL serverem je buď vypnutý a není k dispozici služba Azure Backup. | Virtuální počítač je vypnutý. | Ujistěte se, že je spuštěna instance serveru SQL Server. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Služba Azure Backup agent hosta virtuálního počítače Azure pomocí provádí zálohování, ale není k dispozici na cílovém serveru agenta hosta. | Agent hosta není povolena nebo je v chybném stavu. | [Nainstalujte agenta hosta virtuálního počítače](../virtual-machines/extensions/agent-windows.md) ručně. |

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Záměr automatické ochrany byla buď odebrána nebo je více platný. | Když povolíte automatické ochrany na instanci systému SQL Server, **konfigurace zálohování** úloha pro všechny databáze v této instanci. Pokud zakážete automatickou ochranu jsou spuštěné úlohy, které pak bude **probíhá** zrušení úloh s tímto kódem chyby. | Povolte automatickou ochranu znovu k ochraně všech zbývajících databází. |

## <a name="re-registration-failures"></a>Opětovná registrace selhání

Před aktivací operace znovu zaregistrovat, zkontrolujte pro jeden nebo více z následujících příznaků:

* Všechny operace (jako je zálohování, obnovení a konfiguraci zálohování) se nedaří na virtuálním počítači s jedním z následující kódy chyb: **WorkloadExtensionNotReachable**, **UserErrorWorkloadExtensionNotInstalled**, **WorkloadExtensionNotPresent**, **WorkloadExtensionDidntDequeueMsg**.
* **Stav zálohování** zobrazující oblast zálohované položky **nedostupný**. Vyloučit všechny jiné příčiny, které mohou nastat ve stejném stavu:

  * Nedostatečná oprávnění k provedení operace související se zálohování na virtuálním počítači  
  * Vypnutí virtuálního počítače, aby zálohování nelze provést
  * Problémy se sítí  

  !["Není dosažitelný" stav při opětovné registraci virtuálního počítače](./media/backup-azure-sql-database/re-register-vm.png)

* V případě skupiny dostupnosti Always On spustit zálohování po změně předvolby zálohování nebo po převzetí služeb při selhání.

Tyto příznaky mohou vzniknout pro jeden nebo více z následujících důvodů:

* Rozšíření byla odstraněna nebo odinstalována z portálu. 
* Rozšíření byla odinstalována ze **ovládací panely** na virtuálním počítači v rámci **odinstalovat nebo změnit Program**.
* Virtuální počítač se obnovila zpět v čase prostřednictvím obnovení místní disk.
* Virtuální počítač byl vypnut delší dobu, proto konfiguraci rozšíření na jeho platnost.
* Virtuální počítač byl odstraněn a jiný virtuální počítač vytvořila se stejným názvem a ve stejné skupině prostředků jako odstraněného virtuálního počítače.
* Jeden z uzlů skupiny dostupnosti nedostali kompletní konfiguraci zálohování. To může nastat při skupina dostupnosti je zaregistrovaný do trezoru, nebo když je přidán nový uzel.

V předchozích případech doporučujeme vám, že spustíte operaci přeregistrovat na virtuálním počítači. Prozatím se tato možnost je dostupná jenom přes PowerShell.

## <a name="size-limit-for-files"></a>Omezení velikosti souborů

Celkový počet řetězec velikost souborů závisí nejen na počet souborů, ale také na jejich názvy a cesty. Pro každý databázový soubor získejte logický název souboru a fyzickou cestu. Můžete pomocí tohoto dotazu SQL:

```
SELECT mf.name AS LogicalName, Physical_Name AS Location FROM sys.master_files mf
               INNER JOIN sys.databases db ON db.database_id = mf.database_id
               WHERE db.name = N'<Database Name>'"
```

Nyní uspořádejte je v následujícím formátu:

```
[{"path":"<Location>","logicalName":"<LogicalName>","isDir":false},{"path":"<Location>","logicalName":"<LogicalName>","isDir":false}]}
```

Tady je příklad:

```
[{"path":"F:\\Data\\TestDB12.mdf","logicalName":"TestDB12","isDir":false},{"path":"F:\\Log\\TestDB12_log.ldf","logicalName":"TestDB12_log","isDir":false}]}
```

Pokud řetězec velikost obsahu je větší než 20 000 bajtů, databázové soubory jsou uložena odlišným způsobem. Během obnovení nebudete moct nastavit cestu k cílovému souboru pro obnovení. Soubory se obnoví na výchozí cestu SQL poskytuje server SQL Server.

### <a name="override-the-default-target-restore-file-path"></a>Přepsat výchozí cíl obnovení cestu k souboru

Cesta k cílovému obnovení souboru během operace obnovení můžete přepsat tak, že soubor JSON, který obsahuje mapování souboru databáze do cílové cesty obnovení. Vytvoření `database_name.json` souboru a jeho následné uložení umístění *C:\Program Files\Azure úlohy Backup\bin\plugins\SQL*.

Obsah souboru by měla být v tomto formátu:
```
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

```
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

```
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
  ```


Tento soubor by měl být umístěn před aktivací operace obnovení.

## <a name="next-steps"></a>Další postup

Další informace o Azure Backup pro virtuální počítače s SQL serverem (public preview) najdete v tématu [Azure Backup pro virtuální počítače s SQL](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup).
