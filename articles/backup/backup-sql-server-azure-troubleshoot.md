---
title: Azure Backup Průvodce odstraňováním potíží pro virtuální počítače s SQL serverem | Dokumentace Microsoftu
description: Informace o odstraňování potíží pro zálohování virtuálních počítačů s SQL serverem na Azure.
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
ms.topic: article
ms.date: 06/19/2018
ms.author: anuragm
ms.custom: ''
ms.openlocfilehash: 3ad4afc740be01644145704679ee2674ebde3d07
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52720724"
---
# <a name="troubleshoot-back-up-sql-server-on-azure"></a>Řešení potíží s zálohování SQL serveru v Azure

Tento článek obsahuje informace o odstraňování potíží pro ochranu virtuálních počítačů SQL serverem v Azure (Preview).

## <a name="public-preview-limitations"></a>Omezení veřejné verze Preview

Chcete-li zobrazit omezení veřejné verze Preview, najdete v článku, [zálohovat databázi systému SQL Server v Azure](backup-azure-sql-database.md#public-preview-limitations).

## <a name="sql-server-permissions"></a>Oprávnění SQL Serveru

Konfigurace ochrany pro databáze serveru SQL Server na virtuálním počítači, **AzureBackupWindowsWorkload** rozšíření musí být nainstalované na tomto virtuálním počítači. Pokud se zobrazí chyba, **UserErrorSQLNoSysadminMembership**, to znamená, že vaše Instance SQL nemá požadovaná oprávnění pro zálohování. Chcete-li tuto chybu opravit, postupujte podle kroků v [nastavit oprávnění pro virtuální počítače s SQL mimo marketplace](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms).

## <a name="troubleshooting-errors"></a>Řešení potíží s chybami

Použijte informace v následujících tabulkách k řešení problémů a chyb zjištěných při ochraně SQL serveru do Azure.

## <a name="backup-failures"></a>Selhání zálohování

V následujících tabulkách jsou uspořádané podle čísla chyby.

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Tato databáze SQL nepodporuje požadovaný typ zálohy. | Nastane, pokud je model obnovení databáze neumožňuje požadovaný typ zálohy. K chybě může dojít v následujících situacích: <br/><ul><li>Databázi pomocí jednoduchý model obnovení není povolena zálohy protokolu.</li><li>Rozdílové zálohy a zálohy protokolů nejsou povolené pro hlavní databázi.</li></ul>Další podrobnosti najdete [modelů obnovení SQL](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) dokumentaci. | Pokud se nezdaří zálohování protokolu databáze v jednoduchém modelu obnovení, zkuste použijte jeden z těchto možností:<ul><li>Pokud se databáze nachází v režimu jednoduchého obnovení, zakažte zálohy protokolu.</li><li>Použití [dokumentace ke službě SQL](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) změnit model obnovení databáze na úplný nebo hromadně protokolovaný. </li><li> Pokud nechcete změnit model obnovení, a vy musíte standardní zásady zálohování více databází, které nelze změnit, chybu ignorujte. Úplné a rozdílové zálohy budou fungovat podle plánu. Zálohy protokolů se přeskočí, který se očekává v tomto případě.</li></ul>Pokud je hlavní databázi a nakonfigurovali jste protokolu nebo rozdílové zálohy, použijte některý z následujících kroků:<ul><li>Použití portálu, chcete-li změnit plán zálohování zásad pro hlavní databázi k úplné.</li><li>Pokud máte standardní zásady zálohování více databází, které nelze změnit, chybu ignorujte. Úplné zálohování bude fungovat podle plánu. Protokolu nebo rozdílové zálohy se neprovede, který se očekává v tomto případě.</li></ul> |
| Operace zrušena, protože konfliktní operace již byla spuštěna ve stejné databázi. | Zobrazit [blogu o zálohování a obnovení omezení](https://blogs.msdn.microsoft.com/arvindsh/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database) , které běží souběžně.| [Pomocí SQL Server Management Studio (SSMS) můžete monitorovat úlohy zálohování.](backup-azure-sql-database.md#manage-azure-backup-operations-for-sql-on-azure-vms) Jakmile konfliktní operace selže, restartujte operaci.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Databáze SQL neexistuje. | Databáze byla odstraněna nebo přejmenována. | <ul><li>Zaškrtněte, pokud byla databáze omylem odstraněna nebo přejmenována.</li><li>Pokud byla databáze vymazána omylem, chcete-li pokračovat v zálohování, obnovení databáze do původního umístění.</li><li>Pokud jste odstranili databázi a potřebují budoucí zálohy v trezoru služby Recovery Services, neklikejte [zastavení zálohování s "Delete/zachovat data"](backup-azure-sql-database.md#manage-azure-backup-operations-for-sql-on-azure-vms).</li>|

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Je porušený řetězec protokolu. | Databáze nebo virtuální počítač zálohovaný pomocí jiné řešení zálohování, která ořízne řetězec protokolu.|<ul><li>Zkontrolujte, zda jiné řešení zálohování nebo skriptu se používá. Pokud ano, přestat jiné řešení zálohování. </li><li>Pokud zálohování ad-hoc záloha protokolu, aktivujte úplné zálohování spustit nový řetězec protokolu. Protokol naplánované zálohování není vyžadována žádná akce, jak služba Azure Backup automaticky spustí úplné zálohování, chcete-li vyřešit tento problém.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Azure Backup se nemůže připojit k instanci serveru SQL. | Azure Backup se nemůže připojit k instanci serveru SQL. | Použijte další podrobnosti v nabídce Azure portal chyba zúžit hlavní příčiny. Odkazovat na [SQL odstraňování potíží se zálohováním](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine) oprava chyby.<br/><ul><li>Pokud výchozí nastavení SQL nepovolují vzdálená připojení, změňte nastavení. Najdete následujících odkazech pro změnu nastavení.<ul><li>[https://msdn.microsoft.com/library/bb326495.aspx](https://msdn.microsoft.com/library/bb326495.aspx)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Pokud dojde k problémům přihlášení, podívejte se na následujících odkazech to opravit:<ul><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Pro tento zdroj dat chybí první úplná záloha. | Chybí úplné zálohování pro databázi. Protokol a rozdílové zálohy rodiče, aby úplné zálohy, proto musí být přijata před aktivací rozdílové úplné zálohy nebo zálohy protokolu. | Aktivujte ad hoc úplného zálohování.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Zálohu nejde vytvořit, protože transakční protokol pro zdroj dat je plný. | Místa transakčního protokolu databáze je plná. | Chcete-li opravit tento problém, přečtěte si [dokumentace ke službě SQL](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error). |
| Tato databáze SQL nepodporuje požadovaný typ zálohy. | Vždy v AG sekundárních replik nepodporují úplné a rozdílové zálohy. | <ul><li>Pokud spuštění ad-hoc zálohy aktivujte zálohování na primárním uzlu.</li><li>Pokud zálohování byla naplánována pomocí zásad, ujistěte se, že je zaregistrovaný primárního uzlu. K registraci uzlu, [postupujte podle pokynů ke zjišťování do databáze SQL serveru](backup-azure-sql-database.md#discover-sql-server-databases).</li></ul> | 

## <a name="restore-failures"></a>Selhání obnovení

Při obnovení úlohy se zobrazí následující kódy chyb.

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite 

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Databáze se stejným názvem již existuje v cílovém umístění | Cíl obnovení cíl už obsahuje databáze se stejným názvem.  | <ul><li>Změnit název cílové databáze</li><li>Nebo použít možnost vynucení přepsání na stránce obnovení</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Obnovení se nezdařilo, protože databáze se nepovedlo převést do režimu offline. | Během operace obnovení, cílová databáze je potřeba uvést do offline režimu. Azure Backup není schopen poskytnout tato data do offline režimu. | Použijte další podrobnosti v nabídce Azure portal chyba zúžit hlavní příčiny. Další informace najdete v tématu [dokumentace ke službě SQL](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms). |


###  <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Nejde najít server certifikát s kryptografickým otiskem na cíli. | Hlavní databáze v cílové instanci nemá platný šifrovací kryptografického otisku. | Importujte platný kryptografický otisk používá v instanci zdroje v cílové instanci. |

## <a name="registration-failures"></a>Selhání registrace

Následující kódy chyb jsou určené pro selhání registrace.

### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError 

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Předvolby zálohování pro SQL skupiny dostupnosti AlwaysOn nejde splnit, protože některé uzly ve skupině dostupnosti nejsou zaregistrované. | Uzly, které jsou potřebné k zálohování není zaregistrované, nebo nejsou dostupné. | <ul><li>Zajistěte, aby všechny uzly potřebné k zálohování této databáze jsou zaregistrovaná a v pořádku a pak zkuste operaci zopakovat.</li><li>Předvolby zálohování změnu SQL skupiny dostupnosti Always On.</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Virtuální počítač s SQL serverem je buď vypnutý a není k dispozici služba Azure Backup. | Virtuální počítač je vypnutý. | Ujistěte se, že systém SQL server běží. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Služba Azure Backup agent hosta virtuálního počítače Azure pomocí provádí zálohování, ale není k dispozici na cílovém serveru agenta hosta. | Agent hosta není povolena, nebo není v pořádku. | [Nainstalujte agenta hosta virtuálního počítače](../virtual-machines/extensions/agent-windows.md) ručně. |

## <a name="next-steps"></a>Další postup

Další informace o Azure Backup pro virtuální počítače s SQL serverem (public preview) najdete v tématu [Azure Backup pro virtuální počítače s SQL (Public Preview)](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup).