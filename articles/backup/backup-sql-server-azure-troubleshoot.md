---
title: Řešení potíží se zálohováním databáze SQL Server
description: Informace o řešení potíží při zálohování SQL Server databází běžících na virtuálních počítačích Azure s Azure Backup.
ms.topic: troubleshooting
ms.date: 06/18/2019
ms.openlocfilehash: 1e4ee2bdcd0826b655aa71d83674ff1e0c06a8cb
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99549894"
---
# <a name="troubleshoot-sql-server-database-backup-by-using-azure-backup"></a>Řešení potíží se zálohováním databáze SQL Server pomocí Azure Backup

Tento článek poskytuje informace o řešení potíží pro SQL Server databáze běžící na virtuálních počítačích Azure.

Další informace o procesu zálohování a omezeních najdete v tématu [informace o SQL Server Backup ve virtuálních počítačích Azure](sql-support-matrix.md#feature-considerations-and-limitations).

## <a name="sql-server-permissions"></a>SQL Server oprávnění

Pokud chcete nakonfigurovat ochranu pro SQL Server databázi na virtuálním počítači, musíte na tomto virtuálním počítači nainstalovat rozšíření **AzureBackupWindowsWorkload** . Pokud se zobrazí chyba **UserErrorSQLNoSysadminMembership**, znamená to, že vaše instance SQL Server nemá požadovaná oprávnění k zálohování. Pokud chcete tuto chybu opravit, postupujte podle kroků v části [Nastavení oprávnění virtuálních počítačů](backup-azure-sql-database.md#set-vm-permissions).

## <a name="troubleshoot-discover-and-configure-issues"></a>Řešení potíží se zjišťováním a konfigurací

Po vytvoření a konfiguraci trezoru Recovery Services, který zjišťuje databáze a konfiguruje zálohování, je proces se dvěma kroky.<br>

![Cíl zálohování – SQL Server na virtuálním počítači Azure](./media/backup-azure-sql-database/sql.png)

Pokud se v konfiguraci zálohování nezobrazuje virtuální počítač SQL a jeho instance ve **databáze zjišťování na virtuálních počítačích** a **Konfigurace zálohování** (viz výše uvedený obrázek), zajistěte, aby:

### <a name="step-1-discovery-dbs-in-vms"></a>Krok 1: zjištění databáze na virtuálních počítačích

- Pokud není virtuální počítač uvedený v seznamu zjištěných virtuálních počítačů a není zaregistrován pro zálohování SQL v jiném úložišti, postupujte podle kroků [SQL Server zálohování](./backup-sql-server-database-azure-vms.md#discover-sql-server-databases) .

### <a name="step-2-configure-backup"></a>Krok 2: Konfigurace zálohování

- Pokud je trezor, ve kterém je virtuální počítač SQL zaregistrovaný ve stejném trezoru, který se používá k ochraně databází, postupujte podle kroků [Konfigurace zálohování](./backup-sql-server-database-azure-vms.md#configure-backup) .

Pokud je potřeba virtuální počítač SQL zaregistrovat v novém trezoru, musíte ho odregistrovat ve starém trezoru.  Zrušení registrace virtuálního počítače s SQL z trezoru vyžaduje, aby všechny chráněné zdroje dat byly zastavené a potom můžete zálohovaná data odstranit. Odstraňování zálohovaných dat je destruktivní operace.  Po kontrole a přijetí všech preventivních opatření pro zrušení registrace virtuálního počítače SQL proveďte registraci stejného virtuálního počítače v novém trezoru a zkuste operaci zálohování zopakovat.

## <a name="troubleshoot-backup-and-recovery-issues"></a>Řešení potíží se zálohováním a obnovením  

V některých případech se může stát, že při operacích zálohování a obnovení dojde k náhodným selháním nebo se můžou tyto operace zablokovat. To může být způsobeno antivirovými programy na vašem VIRTUÁLNÍm počítači. V rámci osvědčeného postupu doporučujeme následující postup:

1. Vylučte z kontroly antivirového programu následující složky:

    `C:\Program Files\Azure Workload Backup` `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.WorkloadBackup.AzureBackupWindowsWorkload`

    Nahraďte `C:\` písmenem pro *systemdrive*.

1. Vylučte následující tři procesy běžící v rámci virtuálního počítače z kontroly antivirové ochrany:

    - IaasWLPluginSvc.exe
    - IaaSWorkloadCoordinatorService.exe
    - TriggerExtensionJob.exe

1. SQL také nabízí některé pokyny pro práci s antivirovými programy. Podrobnosti najdete v [tomto článku](https://support.microsoft.com/help/309422/choosing-antivirus-software-for-computers-that-run-sql-server) .

## <a name="faulty-instance-in-a-vm-with-multiple-sql-server-instances"></a>Poškozená instance virtuálního počítače s několika instancemi SQL Server

Na virtuální počítač SQL se můžete vrátit jenom v případě, že jsou všechny instance SQL běžící v rámci virtuálního počítače hlášené v pořádku. Pokud je jedna nebo víc instancí "vadný", virtuální počítač se nezobrazí jako cíl obnovení. To může být možný důvod, proč se virtuální počítač s více instancemi v rozevíracím seznamu Server během operace obnovení nemusí zobrazit.

V části **Konfigurace zálohování** můžete ověřit "připravenost na zálohování" všech instancí SQL na virtuálním počítači.

![Ověření připravenosti na zálohování](./media/backup-sql-server-azure-troubleshoot/backup-readiness.png)

Pokud chcete aktivovat obnovení v dobré instanci SQL, proveďte následující kroky:

1. Přihlaste se k virtuálnímu počítači SQL a pokračujte na `C:\Program Files\Azure Workload Backup\bin` .
1. Vytvořte soubor JSON s názvem `ExtensionSettingsOverrides.json` (Pokud ještě není přítomen). Pokud tento soubor již na virtuálním počítači existuje, pokračujte v jeho používání.
1. Do souboru JSON přidejte následující obsah a soubor uložte:

    ```json
    {
                  "<ExistingKey1>":"<ExistingValue1>",
                    …………………………………………………… ,
              "whitelistedInstancesForInquiry": "FaultyInstance_1,FaultyInstance_2"
            }
            
            Sample content:        
            { 
              "whitelistedInstancesForInquiry": "CRPPA,CRPPB "
            }

    ```

1. Aktivujte operaci znovu **zjistit databáze** na ovlivněném serveru z Azure Portal (stejné místo, kde se dá zobrazit připravenost k zálohování). Virtuální počítač se začne zobrazovat jako cíl pro operace obnovení.

    ![Znovu zjistit databáze](./media/backup-sql-server-azure-troubleshoot/rediscover-dbs.png)

1. Po dokončení operace obnovení odeberte položku *whitelistedInstancesForInquiry* z ExtensionSettingsOverrides.jsv souboru.

## <a name="error-messages"></a>Chybové zprávy

### <a name="backup-type-unsupported"></a>Typ zálohování se nepodporuje.

| Závažnost | Popis | Možné příčiny | Doporučená akce |
|---|---|---|---|
| Upozornění | Aktuální nastavení této databáze nepodporují určité typy zálohování přítomné v přidružených zásadách. | <li>V hlavní databázi lze provést pouze úplnou operaci zálohování databáze. Rozdílové zálohování a zálohování protokolu transakcí není možné. </li> <li>Žádná databáze v jednoduchém modelu obnovení nepovoluje zálohování protokolů transakcí.</li> | Upravte nastavení databáze tak, aby všechny typy zálohování v těchto zásadách byly podporovány. Nebo změňte aktuální zásady tak, aby zahrnovaly jenom podporované typy zálohování. V opačném případě se nepodporované typy zálohování při plánovaném Zálohování přeskočí, jinak se úloha zálohování na vyžádání nezdařila.

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Tato databáze SQL nepodporuje požadovaný typ zálohování. | Vyvolá se v případě, že model obnovení databáze nepovoluje požadovaný typ zálohování. K této chybě může dojít v následujících situacích: <br/><ul><li>Databáze, která používá jednoduchý model obnovení, neumožňuje zálohování protokolů.</li><li>Pro hlavní databázi nejsou povoleny rozdílové zálohy a protokoly.</li></ul>Další podrobnosti najdete v dokumentaci k [modelům obnovení SQL Server](/sql/relational-databases/backup-restore/recovery-models-sql-server) . | Pokud se zálohování protokolu pro databázi v jednoduchém modelu obnovení nepovede, zkuste jednu z těchto možností:<ul><li>Pokud se databáze nachází v režimu jednoduchého obnovení, zakažte zálohování protokolů.</li><li>Pomocí [dokumentace k SQL Server](/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) můžete změnit model obnovení databáze na úplný nebo Hromadně protokolované. </li><li> Pokud nechcete změnit model obnovení a máte standardní zásady pro zálohování více databází, které nelze změnit, chybu ignorujte. Vaše úplné a rozdílové zálohy budou fungovat podle plánu. Zálohy protokolu se přeskočí, což je v tomto případě očekávané.</li></ul>Pokud se jedná o hlavní databázi a nakonfigurovali jste rozdílové zálohování nebo zálohování protokolu, použijte některý z následujících kroků:<ul><li>Pomocí portálu můžete změnit plán zásad zálohování hlavní databáze na úplný.</li><li>Pokud máte standardní zásady pro zálohování více databází, které nelze změnit, chybu ignorujte. Vaše úplné zálohování bude fungovat podle plánu. V tomto případě se v tomto případě očekávají rozdílové zálohování nebo zálohy protokolů.</li></ul> |
| Operace se zrušila, protože ve stejné databázi už je spuštěná konfliktní operace. | Prohlédněte si [záznam blogu o omezeních zálohování a obnovení](https://deep.data.blog/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database/) , která se spouštějí souběžně.| [Pomocí SQL Server Management Studio (SSMS) můžete monitorovat úlohy zálohování](manage-monitor-sql-database-backup.md). Po neúspěšném konfliktu operace restartujte operaci.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Databáze SQL neexistuje. | Databáze byla buď odstraněna, nebo přejmenována. | Ověřte, zda byla databáze omylem odstraněna nebo přejmenována.<br/><br/> Pokud se databáze nedopatřením odstranila, pokud chcete pokračovat v zálohování, obnovte databázi do původního umístění.<br/><br/> Pokud jste databázi odstranili a nepotřebujete budoucí zálohy, pak v Recovery Services trezoru vyberte **Zastavit zálohování** pomocí **uchovávání** zálohovaných dat nebo **odstranění zálohovaných dat**. Další informace najdete v tématu [Správa a sledování zálohovaných SQL Server databází](manage-monitor-sql-database-backup.md).

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Řetěz protokolů je porušený. | Databáze nebo virtuální počítač se zálohují v jiném řešení zálohování, které zkrátí řetězec protokolu.|<ul><li>Ověřte, jestli se nepoužívá jiné řešení zálohování nebo skript. Pokud ano, zastavte další řešení zálohování. </li><li>Pokud byla záloha zálohování protokolu na vyžádání, spusťte pro spuštění nového řetězce protokolu úplnou zálohu. U plánovaných záloh protokolů není potřeba žádná akce, protože služba Azure Backup automaticky aktivuje úplnou zálohu pro vyřešení tohoto problému.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Azure Backup se nemůže připojit k instanci SQL. | Azure Backup se nemůže připojit k instanci SQL Server. | Pomocí dalších podrobností v nabídce Azure Portal chyby můžete zúžit hlavní příčiny. Chybu opravíte v tématu [řešení potíží se zálohováním SQL](/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine) .<br/><ul><li>Pokud výchozí nastavení SQL neumožňují vzdálená připojení, změňte nastavení. Informace o změně nastavení najdete v následujících článcích:<ul><li>[MSSQLSERVER_-1](/sql/relational-databases/errors-events/mssqlserver-1-database-engine-error)</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Pokud dojde k problémům s přihlášením, opravte je pomocí těchto odkazů:<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Pro tento zdroj dat chybí první úplná záloha. | Pro databázi chybí úplné zálohování. Protokoly a rozdílové zálohy jsou nadřazené k úplnému zálohování, proto nezapomeňte před aktivací rozdílových nebo protokolu zálohování provést úplné zálohování. | Aktivovat úplné zálohování na vyžádání.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Zálohování nelze provést, protože transakční protokol zdroje dat je plný. | Transakční místo transakčního protokolu databáze je zaplněno. | Chcete-li tento problém vyřešit, přečtěte si [dokumentaci k SQL Server](/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error). |

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Databáze se stejným názvem už v cílovém umístění existuje. | Cílový cíl obnovení již obsahuje databázi se stejným názvem.  | <ul><li>Změňte název cílové databáze.</li><li>Nebo použijte možnost Vynutit přepsání na stránce obnovit.</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Obnovení selhalo, protože databázi nejde nastavit offline. | I když provádíte obnovení, musí být cílová databáze přepnuta do režimu offline. Azure Backup nemůže převést tato data do režimu offline. | Pomocí dalších podrobností v nabídce Azure Portal chyby můžete zúžit hlavní příčiny. Další informace najdete v [dokumentaci k SQL Serveru](/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms). |

### <a name="wlextgenericiofaultusererror"></a>WlExtGenericIOFaultUserError

|Chybová zpráva |Možné příčiny  |Doporučená akce  |
|---------|---------|---------|
|Během operace došlo k chybě vstupu/výstupu. Zkontrolujte běžné vstupně-výstupní chyby na virtuálním počítači.   |   Přístupová oprávnění nebo omezení místa na cíli.       |  Vyhledejte běžné vstupně-výstupní chyby na virtuálním počítači. Zajistěte, aby cílová jednotka nebo síťová sdílená položka v počítači: <li> má oprávnění ke čtení a zápisu pro účet NT AUTHORITY\SYSTEM v počítači. <li> má dostatek místa pro úspěšné dokončení operace.<br> Další informace najdete v tématu [obnovení souborů](restore-sql-database-azure-vm.md#restore-as-files).
       |

### <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| V cíli nebyl nalezen certifikát serveru s kryptografickým otiskem. | Hlavní databáze v cílové instanci nemá platný šifrovací kryptografický otisk. | Importujte platný kryptografický otisk certifikátu použitý u zdrojové instance do cílové instance. |

### <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Záloha protokolů použitá k obnovení obsahuje hromadně protokolované změny. Nedá se použít k zastavení v libovolném bodě v čase podle pokynů SQL. | Když je databáze v režimu hromadného obnovení, data mezi hromadně protokolovanými transakcemi a další transakce protokolu se nedají obnovit. | Vyberte jiný bod v čase pro obnovení. [Další informace](/sql/relational-databases/backup-restore/recovery-models-sql-server).

### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Předvolbu zálohování pro skupinu dostupnosti SQL AlwaysOn není možné zajistit, protože některé uzly ve skupině dostupnosti nejsou zaregistrované. | Uzly, které jsou nutné k provedení zálohování, nejsou registrovány nebo jsou nedosažitelné. | <ul><li>Zajistěte, aby všechny uzly potřebné k provedení záloh této databáze byly registrovány a v pořádku, a potom operaci opakujte.</li><li>Změňte předvolby zálohování pro skupinu dostupnosti Always On SQL Server.</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Virtuální počítač se systémem SQL Server je buď vypnutý, a není přístupný pro Azure Backup službu. | Virtuální počítač je vypnutý. | Ujistěte se, že je spuštěná instance SQL Server. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Služba Azure Backup pro zálohování používá agenta hosta virtuálního počítače Azure, ale Agent hosta není na cílovém serveru k dispozici. | Agent hosta není povolený nebo není v pořádku. | [Nainstalujte agenta hosta virtuálního počítače](../virtual-machines/extensions/agent-windows.md) ručně. |

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Záměr automatické ochrany byl buď odebrán, nebo již není platný. | Pokud povolíte automatickou ochranu na instanci SQL Server, nakonfigurujte úlohy **zálohování** spuštěné pro všechny databáze v této instanci. Pokud při spuštění úloh zakážete automatickou ochranu, **probíhající úlohy se** zruší s tímto kódem chyby. | Znovu povolte automatickou ochranu, aby bylo možné lépe chránit všechny zbývající databáze. |

### <a name="clouddosabsolutelimitreached"></a>CloudDosAbsoluteLimitReached

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
Operace je blokovaná, protože jste dosáhli limitu počtu operací povolených během 24 hodin. | Když jste dosáhli maximálního povoleného limitu operace v rozmezí 24 hodin, zobrazí se tato chyba. <br> Příklad: Pokud jste dosáhli limitu pro počet úloh konfigurace zálohování, které se můžou aktivovat za den, a pokusíte se nakonfigurovat zálohování pro novou položku, zobrazí se tato chyba. | Obvykle se tento problém vyřeší opakováním operace po 24 hodinách. Pokud se ale problém nevyřeší, můžete požádat o pomoc podporu Microsoftu.

### <a name="clouddosabsolutelimitreachedwithretry"></a>CloudDosAbsoluteLimitReachedWithRetry

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
Operace je zablokovaná, protože trezor dosáhl maximálního limitu pro tyto operace povolené v rozmezí 24 hodin. | Když jste dosáhli maximálního povoleného limitu operace v rozmezí 24 hodin, zobrazí se tato chyba. Tato chyba se obvykle zobrazuje v případě, že dojde k operacím na škálování, jako je například změna zásad nebo Automatická ochrana. Na rozdíl od v případě CloudDosAbsoluteLimitReached není možné tento stav vyřešit. Služba Azure Backup služby ve skutečnosti zopakuje operace interně pro všechny příslušné položky.<br> Příklad: Pokud máte k zásadám chráněný velký počet zdrojů dat a pokusíte se ji změnit, spustí se pro každou chráněnou položku konfigurace úloh ochrany a někdy se může vysáhnout maximální povolený limit pro tyto operace za den.| Služba Azure Backup bude tuto operaci automaticky opakovat po 24 hodinách.

### <a name="workloadextensionnotreachable"></a>WorkloadExtensionNotReachable

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
Operace rozšíření úlohy AzureBackup se nezdařila. | VIRTUÁLNÍ počítač je vypnutý (nebo) virtuální počítač nemůže kontaktovat službu Azure Backup kvůli problémům s připojením k Internetu.| – Ujistěte se prosím, že je virtuální počítač spuštěný a má připojení k Internetu.<br>- [Znovu zaregistrujte rozšíření na SQL serverm virtuálním počítači](https://docs.microsoft.com/azure/backup/manage-monitor-sql-database-backup#re-register-extension-on-the-sql-server-vm).


### <a name="usererrorvminternetconnectivityissue"></a>UserErrorVMInternetConnectivityIssue

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
Virtuální počítač nemůže kontaktovat službu Azure Backup kvůli problémům s připojením k Internetu. | Virtuální počítač potřebuje odchozí připojení k Azure Backup službě, Azure Storage nebo službám Azure Active Directory.| – Pokud k omezení připojení používáte NSG, měli byste použít značku služby *AzureBackup* a povolit odchozí přístup k Azure Backup službě a podobně pro služby Azure AD (*azureactivedirectory selhala*) a Azure Storage (*úložiště*). Pomocí těchto [kroků](./backup-sql-server-database-azure-vms.md#nsg-tags) udělíte přístup.<br>– Zajistěte překlad koncových bodů Azure DNS.<br>– Ověřte, jestli je virtuální počítač za nástrojem pro vyrovnávání zatížení blokující přístup k Internetu. Po přiřazení veřejné IP adresy k virtuálním počítačům bude zjišťování fungovat.<br>– Ověřte, že není k dispozici brána firewall/antivirová ochrana nebo proxy servery blokující volání výše uvedených tří cílových služeb.

## <a name="re-registration-failures"></a>Selhání opětovné registrace

Než zahájíte operaci opětovného zápisu, proveďte kontrolu jednoho nebo více následujících příznaků:

- Všechny operace (například zálohování, obnovení a konfigurace zálohování) selžou na virtuálním počítači s jedním z následujících kódů chyb: **[WorkloadExtensionNotReachable](#workloadextensionnotreachable)**, **UserErrorWorkloadExtensionNotInstalled**, **WorkloadExtensionNotPresent**, **WorkloadExtensionDidntDequeueMsg**.
- Pokud je v oblasti **stavu zálohování** pro zálohovanou položku **nedostupná**, vyfiltrujte všechny ostatní příčiny, které by mohly mít za následek stejný stav:

  - Nemáte oprávnění k provádění operací souvisejících se zálohováním virtuálního počítače.
  - Vypnutí virtuálního počítače, takže zálohy nejdou uskutečnit.
  - [Problémy se sítí](#usererrorvminternetconnectivityissue)

   ![opětovné registrace virtuálního počítače](./media/backup-azure-sql-database/re-register-vm.png)

- V případě skupiny dostupnosti Always On se zálohování po změně předvolby zálohování nebo po převzetí služeb při selhání nedaří.

K těmto potížím může dojít z některého z následujících důvodů:

- Rozšíření bylo na portálu odstraněno nebo odinstalováno.
- Rozšíření bylo z **ovládacích panelů** na virtuálním počítači odinstalováno při **odinstalaci nebo změně programu**.
- Virtuální počítač se znovu obnovil v čase prostřednictvím místního obnovení disku.
- Po delší dobu byl virtuální počítač vypnutý, takže platnost konfigurace rozšíření na něm vypršela.
- Virtuální počítač se odstranil a vytvořil se jiný virtuální počítač se stejným názvem a ve stejné skupině prostředků jako odstraněný virtuální počítač.
- Jeden z uzlů skupiny dostupnosti neobdržel úplnou konfiguraci zálohování. K tomu může dojít, když je skupina dostupnosti zaregistrovaná do trezoru nebo když se přidá nový uzel.

V předchozích scénářích doporučujeme, abyste na virtuálním počítači aktivovali operaci opětovného zápisu. Pokyny k provedení této úlohy v PowerShellu najdete [tady](./backup-azure-sql-automation.md#enable-backup) .

## <a name="size-limit-for-files"></a>Omezení velikosti souborů

Celková velikost řetězce souborů závisí nejen na počtu souborů, ale také na jejich názvech a cestách. Pro každý soubor databáze Získejte logický název souboru a fyzickou cestu. Tento dotaz SQL můžete použít:

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

Pokud velikost řetězce obsahu překračuje 20 000 bajtů, soubory databáze budou uloženy jinak. Během obnovení nebudete moci nastavit cestu k cílovému souboru pro obnovení. Soubory budou obnoveny do výchozí cesty SQL, kterou poskytuje SQL Server.

### <a name="override-the-default-target-restore-file-path"></a>Přepsat výchozí cílovou cestu k souboru pro obnovení

Cílovou cestu k souboru obnovení můžete během operace obnovení přepsat umístěním souboru JSON, který obsahuje mapování souboru databáze na cílovou cestu pro obnovení. Vytvořte `database_name.json` soubor a umístěte ho do umístění `C:\Program Files\Azure Workload Backup\bin\plugins\SQL*` .

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

V předchozím obsahu můžete získat logický název databázového souboru pomocí následujícího dotazu SQL:

```sql
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
```

Tento soubor by měl být umístěn před aktivací operace obnovení.

## <a name="next-steps"></a>Další kroky

Další informace o Azure Backup pro virtuální počítače s SQL Server (Public Preview) najdete v tématu [Azure Backup pro virtuální počítače SQL](../azure-sql/virtual-machines/windows/backup-restore.md#azbackup).
