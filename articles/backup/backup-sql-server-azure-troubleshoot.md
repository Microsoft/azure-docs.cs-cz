---
title: Řešení potíží s zálohy databáze SQL serveru pomocí služby Azure Backup | Dokumentace Microsoftu
description: Informace o odstraňování potíží pro zálohování databází systému SQL Server běžící na virtuálních počítačích Azure s Azure Backup.
services: backup
author: anuragm
manager: shivamg
ms.service: backup
ms.topic: article
ms.date: 06/18/2019
ms.author: anuragm
ms.openlocfilehash: 9ed30a35f30d1b6b9fdcd43110ed93618a10dbc3
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204195"
---
# <a name="troubleshoot-back-up-sql-server-on-azure"></a>Řešení potíží s zálohování SQL serveru v Azure

Tento článek obsahuje informace o odstraňování potíží pro ochranu virtuálních počítačů SQL serverem v Azure.

## <a name="feature-consideration-and-limitations"></a>Funkce aspektů a omezení

Zobrazit funkce zvážit, najdete v článku, [zálohování serveru SQL Server na virtuálních počítačích Azure](backup-azure-sql-database.md#feature-consideration-and-limitations).

## <a name="sql-server-permissions"></a>Oprávnění SQL serveru

Konfigurace ochrany pro databáze serveru SQL Server na virtuálním počítači, **AzureBackupWindowsWorkload** rozšíření musí být nainstalované na tomto virtuálním počítači. Pokud se zobrazí chyba, **UserErrorSQLNoSysadminMembership**, to znamená, že vaše Instance SQL nemá požadovaná oprávnění pro zálohování. Chcete-li tuto chybu opravit, postupujte podle kroků v [nastavit oprávnění pro virtuální počítače s SQL mimo marketplace](backup-azure-sql-database.md#set-vm-permissions).

## <a name="backup-type-unsupported"></a>Nepodporovaný typ zálohování

| Severity | Popis | Možné příčiny | Doporučená akce |
|---|---|---|---|
| Upozornění | Aktuální nastavení pro tuto databázi nepodporují určitého typu typy v přidružené zásady zálohování. | <li>**Master DB**: Pouze úplné zálohování lze provést v hlavní databázi. ani **rozdílové** zálohování ani transakce **protokoly** zálohování. </li> <li>Všechny databáze v **jednoduchý model obnovení** neumožňuje transakce **protokoly** zálohování, které mají být provedeny.</li> | Změňte nastavení databáze tak, aby se podporují všechny typy zálohování v zásadách. Můžete také změňte aktuální zásady zahrnout pouze podporované typy zálohování. V opačném případě se přeskočí nepodporované typy zálohování během naplánovaného zálohování nebo se nezdaří úlohy zálohování ad hoc záloha.


## <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Tato databáze SQL nepodporuje požadovaný typ zálohy. | Nastane, pokud je model obnovení databáze neumožňuje požadovaný typ zálohy. K chybě může dojít v následujících situacích: <br/><ul><li>Databázi pomocí jednoduchý model obnovení není povolena zálohy protokolu.</li><li>Rozdílové zálohy a zálohy protokolů nejsou povolené pro hlavní databázi.</li></ul>Další podrobnosti najdete [modelů obnovení SQL](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) dokumentaci. | Pokud se nezdaří zálohování protokolu databáze v jednoduchém modelu obnovení, zkuste použijte jeden z těchto možností:<ul><li>Pokud se databáze nachází v režimu jednoduchého obnovení, zakažte zálohy protokolu.</li><li>Použití [dokumentace ke službě SQL](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) změnit model obnovení databáze na úplný nebo hromadně protokolovaný. </li><li> Pokud nechcete změnit model obnovení, a vy musíte standardní zásady zálohování více databází, které nelze změnit, chybu ignorujte. Úplné a rozdílové zálohy budou fungovat podle plánu. Zálohy protokolů se přeskočí, který se očekává v tomto případě.</li></ul>Pokud je hlavní databázi a nakonfigurovali jste protokolu nebo rozdílové zálohy, použijte některý z následujících kroků:<ul><li>Použití portálu, chcete-li změnit plán zálohování zásad pro hlavní databázi k úplné.</li><li>Pokud máte standardní zásady zálohování více databází, které nelze změnit, chybu ignorujte. Úplné zálohování bude fungovat podle plánu. Protokolu nebo rozdílové zálohy se neprovede, který se očekává v tomto případě.</li></ul> |
| Operace zrušena, protože konfliktní operace již byla spuštěna ve stejné databázi. | Zobrazit [blogu o zálohování a obnovení omezení](https://blogs.msdn.microsoft.com/arvindsh/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database) , které běží souběžně.| [Pomocí SQL Server Management Studio (SSMS) můžete monitorovat úlohy zálohování.](manage-monitor-sql-database-backup.md) Jakmile konfliktní operace selže, restartujte operaci.|

## <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Databáze SQL neexistuje. | Databáze byla odstraněna nebo přejmenována. | Zaškrtněte, pokud byla databáze omylem odstraněna nebo přejmenována.<br/><br/> Pokud byla databáze vymazána omylem, chcete-li pokračovat v zálohování, obnovení databáze do původního umístění.<br/><br/> Pokud jste odstranili databázi a potřebují budoucí zálohy v trezoru služby Recovery Services, neklikejte [zastavení zálohování s "Delete/zachovat data"](manage-monitor-sql-database-backup.md).

## <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Řetěz protokolů je porušený. | Databáze nebo virtuální počítač zálohovaný pomocí jiné řešení zálohování, která ořízne řetězec protokolu.|<ul><li>Zkontrolujte, zda jiné řešení zálohování nebo skriptu se používá. Pokud ano, přestat jiné řešení zálohování. </li><li>Pokud bylo zálohování zálohování ad hoc protokolu, aktivujte úplné zálohování spustit nový řetězec protokolu. Protokol naplánované zálohování není vyžadována žádná akce, jak služba Azure Backup automaticky spustí úplné zálohování, chcete-li vyřešit tento problém.</li>|

## <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Azure Backup se nemůže připojit k instanci serveru SQL. | Azure Backup se nemůže připojit k instanci serveru SQL. | Použijte další podrobnosti v nabídce Azure portal chyba zúžit hlavní příčiny. Odkazovat na [SQL odstraňování potíží se zálohováním](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine) oprava chyby.<br/><ul><li>Pokud výchozí nastavení SQL nepovolují vzdálená připojení, změňte nastavení. Naleznete v následujících článcích pro informace o změně nastavení.<ul><li>[MSSQLSERVER_-1](/previous-versions/sql/sql-server-2016/bb326495(v=sql.130))</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Pokud dojde k problémům přihlášení, podívejte se na následujících odkazech to opravit:<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

## <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Pro tento zdroj dat chybí první úplná záloha. | Chybí úplné zálohování pro databázi. Protokol a rozdílové zálohy rodiče, aby úplné zálohy, proto musí být přijata před aktivací rozdílové úplné zálohy nebo zálohy protokolu. | Aktivujte ad hoc úplné zálohování.   |

## <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Zálohu nejde vytvořit, protože transakční protokol pro zdroj dat je plný. | Místa transakčního protokolu databáze je plná. | Chcete-li opravit tento problém, přečtěte si [dokumentace ke službě SQL](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error). |

## <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Databáze se stejným názvem již existuje v cílovém umístění | Cíl obnovení cíl už obsahuje databáze se stejným názvem.  | <ul><li>Změnit název cílové databáze</li><li>Nebo použít možnost vynucení přepsání na stránce obnovení</li> |

## <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Obnovení selhalo, protože databázi nejde nastavit offline. | Během operace obnovení, cílová databáze je potřeba uvést do offline režimu. Azure Backup není schopen poskytnout tato data do offline režimu. | Použijte další podrobnosti v nabídce Azure portal chyba zúžit hlavní příčiny. Další informace najdete v tématu [dokumentace ke službě SQL](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms). |

##  <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Nejde najít server certifikát s kryptografickým otiskem na cíli. | Hlavní databáze v cílové instanci nemá platný šifrovací kryptografického otisku. | Importujte platný kryptografický otisk používá v instanci zdroje v cílové instanci. |

## <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Záloha protokolů použitá k obnovení obsahuje hromadně protokolované změny. Podle pokynů pro SQL ji nejde použít k zastavení v libovolném bodu v čase. | Pokud je databáze v režimu hromadně protokolovaný obnovení, data mezi hromadně protokolované transakce a další protokolu transakcí nelze obnovit. | Vyberte jiný bod v čase pro obnovení. [Další informace](https://docs.microsoft.com/previous-versions/sql/sql-server-2008-r2/ms186229(v=sql.105))


## <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Předvolbu zálohování pro skupinu dostupnosti SQL AlwaysOn není možné zajistit, protože některé uzly ve skupině dostupnosti nejsou zaregistrované. | Uzly, které jsou potřebné k zálohování není zaregistrované, nebo nejsou dostupné. | <ul><li>Zajistěte, aby všechny uzly potřebné k zálohování této databáze jsou zaregistrovaná a v pořádku a pak zkuste operaci zopakovat.</li><li>Předvolby zálohování změnu SQL skupiny dostupnosti Always On.</li></ul> |

## <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Virtuální počítač s SQL serverem je buď vypnutý a není k dispozici služba Azure Backup. | Virtuální počítač je vypnutý. | Ujistěte se, že systém SQL server běží. |

## <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Služba Azure Backup agent hosta virtuálního počítače Azure pomocí provádí zálohování, ale není k dispozici na cílovém serveru agenta hosta. | Agent hosta není povolena, nebo není v pořádku. | [Nainstalujte agenta hosta virtuálního počítače](../virtual-machines/extensions/agent-windows.md) ručně. |

## <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Záměr automatické ochrany byla buď odebrána nebo je více platný. | Když povolíte automatické ochrany na instanci SQL, **konfigurace zálohování** úloha pro všechny databáze v této instanci. Pokud zakážete automatickou ochranu jsou spuštěné úlohy, které pak bude **probíhá** zrušení úloh s tímto kódem chyby. | Povolte automatickou ochranu znovu k ochraně všech zbývajících databází. |

## <a name="re-registration-failures"></a>Opětovná registrace selhání

Vyhledat jeden nebo více [příznaky](#symptoms) před aktivací operace znovu zaregistrovat.

### <a name="symptoms"></a>Příznaky

* Všechny operace, jako je zálohování, obnovení a konfiguraci zálohování se nedaří na virtuálním počítači s jedním z následující kódy chyb: **WorkloadExtensionNotReachable**, **UserErrorWorkloadExtensionNotInstalled**, **WorkloadExtensionNotPresent**, **WorkloadExtensionDidntDequeueMsg**
* **Stav zálohování** záložní položky zobrazuje **nedostupný**. Přestože třeba vyloučit všechny jiné důvody, které může také způsobit ve stejném stavu:

  * Nedostatečná oprávnění k provedení zálohování souvisejících operací na virtuálním počítači  
  * Virtuální počítač se vypnul kvůli které zálohování nelze provést
  * Problémy se sítí  

    ![Zopakujte registraci virtuálního počítače](./media/backup-azure-sql-database/re-register-vm.png)

* V případě skupiny dostupnosti always on spustit zálohování po změně předvolby zálohování nebo při převzetí služeb při selhání

### <a name="causes"></a>Příčiny
Tyto příznaky mohou vzniknout z důvodu nejméně jeden z následujících důvodů:

  * Rozšíření byla odstraněna nebo odinstalována z portálu 
  * Rozšíření byla odinstalována ze **ovládací panely** virtuálního počítače v rámci **odinstalovat nebo změnit Program** uživatelského rozhraní
  * Virtuální počítač se obnovila zpět v čase s použitím místní disky obnovení
  * Virtuální počítač byl vypnut delší dobu kvůli které konfiguraci rozšíření na něm vypršela
  * Odstranění virtuálního počítače a další virtuální počítač vytvořila se stejným názvem a ve stejné skupině prostředků jako odstraněného virtuálního počítače
  * Jeden z uzlů AG nedostali kompletní konfiguraci zálohování, k tomu může dojít buď v době registrace skupiny dostupnosti v úložišti nebo když přidá nový uzel  <br>
    Ve výše uvedených scénářích se doporučuje spustit znovu zaregistrovat operace na virtuálním počítači. Tato možnost dostupná jenom přes PowerShell a brzy bude k dispozici na webu Azure Portal i.

## <a name="files-size-limit-beyond-which-restore-happens-to-default-path"></a>Limit velikosti souboru nad rámec obnovení, který se stane výchozí cestu

Velikost řetězce celkový počet souborů nejen závisí na počtu souborů, ale také na jejich názvy a cesty. Pro všechny soubory databáze získáte logický název souboru a fyzickou cestu.
Můžete použít příkaz jazyka SQL, které jsou uvedena níže:

  ```
  SELECT mf.name AS LogicalName, Physical_Name AS Location FROM sys.master_files mf
                 INNER JOIN sys.databases db ON db.database_id = mf.database_id
                 WHERE db.name = N'<Database Name>'"
 ```

Nyní uspořádejte je ve formátu uvedena níže:

  ```[{"path":"<Location>","logicalName":"<LogicalName>","isDir":false},{"path":"<Location>","logicalName":"<LogicalName>","isDir":false}]}
  ```

Příklad:

  ```[{"path":"F:\\Data\\TestDB12.mdf","logicalName":"TestDB12","isDir":false},{"path":"F:\\Log\\TestDB12_log.ldf","logicalName":"TestDB12_log","isDir":false}]}
  ```

Pokud velikost řetězce obsah uvedený výše je větší než 20 000 bajtů, pak databázové soubory jsou uložena odlišným způsobem a při obnovení není možné nastavit cestu k cílovému souboru pro obnovení. Soubory se obnoví na výchozí SQL cestu poskytované systémem SQL Server.

### <a name="override-the-default-target-restore-file-path"></a>Přepsat výchozí cíl obnovení cestu k souboru

Cesta k cílovému obnovení souboru během operace obnovení můžete přepsat tak, že soubor JSON, který obsahuje mapování databázový soubor, cílovou cestu obnovení. K tomu vytvořit soubor `database_name.json` a umístěte ho do umístění *C:\Program Files\Azure úlohy Backup\bin\plugins\SQL*.

Obsah souboru musí být ve formátu uvedena níže:
  ```[
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

Příklad:

  ```[
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

 
Ve výše uvedené obsahu můžete získat logický název souboru databáze pomocí dotazu SQL uvedena níže:

```
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
  ```


Tento soubor by měl být umístěn před aktivací operace obnovení.

## <a name="next-steps"></a>Další postup

Další informace o Azure Backup pro virtuální počítače s SQL serverem (public preview) najdete v tématu [Azure Backup pro virtuální počítače s SQL (Public Preview)](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup).
