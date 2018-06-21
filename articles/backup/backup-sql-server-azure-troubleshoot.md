---
title: Průvodce řešením potíží pro virtuální počítače serveru SQL Azure Backup | Microsoft Docs
description: Informace o řešení potíží pro zálohování virtuálních počítačů serveru SQL do Azure.
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2018
ms.author: markgal;anuragm
ms.custom: ''
ms.openlocfilehash: 1c87382c2aae70b022fb391f80f7c75b0a4e5fe6
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296208"
---
# <a name="troubleshoot-back-up-sql-server-on-azure"></a>Řešení potíží s zálohování systému SQL Server v Azure

Tento článek obsahuje informace o odstraňování potíží pro ochranu virtuálních počítačů serveru SQL v Azure (Preview).

## <a name="public-preview-limitations"></a>Veřejné omezení verze Preview

Chcete-li zobrazit omezení ve verzi Public Preview, najdete v článku [zálohovat databázi systému SQL Server v Azure](backup-azure-sql-database.md#public-preview-limitations).

## <a name="sql-server-permissions"></a>Oprávnění SQL Serveru

Konfigurace ochrany pro databáze serveru SQL na virtuálním počítači, **AzureBackupWindowsWorkload** rozšíření musí být nainstalované na tomto virtuálním počítači. Pokud se zobrazí chyba, **UserErrorSQLNoSysadminMembership**, znamená to vaše Instance SQL, nemá požadovaná oprávnění zálohování. Pokud chcete vyřešit tuto chybu, postupujte podle kroků v [nastavit oprávnění pro virtuální počítače bez marketplace SQL](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms).

## <a name="troubleshooting-errors"></a>Řešení potíží s chybami

Použijte informace v následujících tabulkách potíží a chyb zjištěných při ochraně SQL serveru do Azure.

## <a name="backup-failures"></a>Selhání zálohování

V následujících tabulkách jsou uspořádané podle kódu chyby.

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Tuto databázi SQL nepodporuje požadovaný typ zálohy. | Nastane, když je model obnovení databáze neumožňuje požadovaný typ zálohy. K chybě může dojít v následujících situacích: <br/><ul><li>Databáze používá jednoduchý model obnovení neumožňuje zálohy protokolu.</li><li>Rozdíl a protokoly a nejsou povoleny pro hlavní databázi.</li></ul>Další podrobnosti najdete [modelech obnovení SQL](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) dokumentaci. | Pokud se nezdaří zálohování protokolu pro databázi ve jednoduchý model obnovení, zkuste jednu z těchto možností:<ul><li>Pokud se databáze nachází v režimu jednoduchého obnovení, zakažte zálohy protokolu.</li><li>Použití [dokumentaci k systému SQL](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) změnit model obnovení databáze na úplný nebo hromadně přihlášení. </li><li> Pokud nechcete změňte model obnovení, a mít standardní zásady zálohování více databází, které nelze změnit, můžete chybu ignorujte. Úplné a rozdílové zálohy budou fungovat podle plánu. Zálohy protokolu bude přeskočen, což je očekáváno v tomto případě.</li></ul>Pokud je hlavní databáze a jste nakonfigurovali rozdíl nebo protokolu zálohování, použijte některý z následujících kroků:<ul><li>Použití portálu ke změnám plánů zásady zálohování pro hlavní databázi, úplné.</li><li>Pokud máte standardní zásady zálohování více databází, které nelze změnit, můžete chybu ignorujte. Úplné zálohování bude fungovat podle plánu. Rozdíl nebo protokolu zálohování se neprovede, což je očekáváno v tomto případě.</li></ul> |
| Operace zrušena, protože konfliktní operace již byla spuštěna na stejné databáze. | Najdete v článku [položku blogu o zálohování a obnovení omezení](https://blogs.msdn.microsoft.com/arvindsh/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database) které běží souběžně.| [Ke sledování úloh zálohování použijte SQL Server Management Studio (SSMS).](backup-azure-sql-database.md#manage-azure-backup-operations-for-sql-on-azure-vms) Jakmile konfliktní operace selže, restartujte operaci.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Databáze SQL neexistuje. | Databáze byla odstraněna nebo přejmenována. | <ul><li>Zkontrolujte, zda byla databáze omylem odstranit nebo přejmenovat.</li><li>Pokud databáze byla odstraněna omylem, chcete-li pokračovat v zálohování, obnovte databázi do původního umístění.</li><li>Pokud jste odstranili databázi a provést není nutné budoucích zálohování, pak v trezoru služeb zotavení klikněte na tlačítko [zastavení zálohování pomocí "Odstranit nebo ponechat data"](backup-azure-sql-database.md#manage-azure-backup-operations-for-sql-on-azure-vms).</li>|

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Je porušený řetězec protokolu. | Databáze nebo virtuální počítač je zálohovat pomocí jiná řešení zálohování, což zkrátí řetězec protokolu.|<ul><li>Zkontrolujte, jestli jiná řešení zálohování nebo skriptu je používán. Pokud ano, zastavte jiné řešení zálohování. </li><li>Pokud se záloha byla zálohu protokolu ad-hoc, spusťte úplné zálohování spustit nový řetězec protokolu. Pro zálohy plánované protokolu není vyžadována žádná akce, jak služba Azure Backup se automaticky spouštět úplné zálohování tento problém vyřešit.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Zálohování Azure není možné se připojit k instanci SQL. | Zálohování Azure se nemůže připojit k instanci serveru SQL. | Pomocí další podrobnosti v nabídce portálu Azure chyby můžete zúžit základní příčiny. Odkazovat na [zálohování SQL řešení potíží](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine) postup řešení chyby.<br/><ul><li>Pokud výchozí nastavení SQL není povolit vzdálená připojení, změňte nastavení. Najdete následujících odkazů pro změnu nastavení.<ul><li>[https://msdn.microsoft.com/library/bb326495.aspx](https://msdn.microsoft.com/library/bb326495.aspx)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Pokud jsou přihlášení problémy, podívejte se na následujících odkazů můžete opravit:<ul><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Pro tento zdroj dat chybí první úplné zálohování. | Pro databázi chybí úplné zálohování. Protokol a rozdíl nadřazené zálohy pro úplné zálohování, tak, aby úplné zálohování bude nutno před spuštěním rozdílovou nebo protokolu zálohování. | Aktivaci služby ad-hoc úplného zálohování.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Nelze provést zálohování jako transakční protokol pro zdroj dat je plný. | Místo transakčního protokolu databáze je plná. | Chcete-li tento problém vyřešit, najdete [dokumentaci k systému SQL](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error). |
| Tuto databázi SQL nepodporuje požadovaný typ zálohy. | Vždy na AG sekundární repliky nepodporují úplné a rozdílové zálohy. | <ul><li>Pokud se aktivuje zálohu ad-hoc spustíte zálohování na primárním uzlu.</li><li>Pokud záloha byla naplánována zásadami, zkontrolujte, zda že primárním uzlu, který je zaregistrován. K registraci uzlu [postupujte podle kroků pro zjišťování do databáze serveru SQL](backup-azure-sql-database.md#discover-sql-server-databases).</li></ul> | 

## <a name="restore-failures"></a>Obnovení selhání

Následující kódy chyb se zobrazí při obnovení úloh selže.

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite 

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Databáze se stejným názvem již existuje v cílovém umístění | Cíl obnovení cíl již databáze se stejným názvem.  | <ul><li>Změňte název cílové databáze</li><li>Nebo použijte možnost force přepsat na stránce obnovení</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Obnovení se nezdařilo, protože databáze nelze uvést do režimu offline. | Přitom obnovení cílová databáze je potřeba uvést do offline režimu. Zálohování Azure není možné převést do offline režimu tato data. | Pomocí další podrobnosti v nabídce portálu Azure chyby můžete zúžit základní příčiny. Další informace najdete v tématu [dokumentaci k systému SQL](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms). |


###  <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Nelze najít certifikát serveru s kryptografickým otiskem na cíli. | Hlavní databáze na instance cílové nemá kryptografický otisk platný šifrování. | Naimportujte platný certifikát kryptografický otisk používá v instanci zdroje, v cílové instanci. |

## <a name="registration-failures"></a>Selhání registrace

Následující kódy chyb jsou určené pro chyby registrace.

### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError 

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Zálohování předvolby pro vždy na skupina dostupnosti SQL nelze splnit, protože nejsou registrované některé uzly skupiny dostupnosti. | Uzly, které jsou potřebné k provedení zálohy nejsou registrované nebo nejsou dostupné. | <ul><li>Zajistěte, aby všechny uzly, které jsou potřebné k provedení zálohování této databáze jsou zaregistrovaná a v pořádku a poté operaci opakujte.</li><li>Zálohování předvoleb změnu vždy na skupiny dostupnosti systému SQL.</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| SQL server virtuálního počítače je buď vypnutí a není k dispozici do služby Azure Backup. | Virtuální počítač je vypnutý. | Ujistěte se, že je SQL server spuštěn. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Služba Azure Backup používá pro provádění zálohování agenta hosta virtuálního počítače Azure, ale agent hosta, který není k dispozici na cílovém serveru. | Agent hosta nejsou povoleny nebo není v pořádku | [Instalace agenta hosta virtuálního počítače](../virtual-machines/extensions/agent-windows.md) ručně. |

## <a name="next-steps"></a>Další postup

Další informace o zálohování Azure pro virtuální počítače serveru SQL (verze public preview) najdete v tématu [zálohování Azure pro virtuální počítače SQL (verze Public Preview)](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup).