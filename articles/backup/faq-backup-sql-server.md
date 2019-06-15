---
title: Nejčastější dotazy k zálohování databází systému SQL Server na virtuálních počítačích Azure s Azure Backup
description: Najděte odpovědi na běžné dotazy týkající se zálohování databází systému SQL Server na virtuálních počítačích Azure s Azure Backup.
services: backup
author: sachdevaswati
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: sachdevaswati
ms.openlocfilehash: 649e50634d901ab48f1cb36c39d7331401c0cc51
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64700176"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>Nejčastější dotazy týkající se databáze SQL serveru, na kterých běží zálohování virtuálních počítačů Azure

Tento článek odpovědi na běžné dotazy o zálohování databáze systému SQL Server, ve kterých běží na Azure virtual machines (VM) a které používají [Azure Backup](backup-overview.md) služby.

## <a name="can-i-use-azure-backup-for-iaas-vm-as-well-as-sql-server-on-the-same-machine"></a>Můžu používat Azure backup pro virtuální počítač IaaS, stejně jako SQL Server na stejném počítači?
Ano, může mít zálohování virtuálních počítačů a zálohování SQL na jednom virtuálním počítači. V takovém případě aktivujeme interně úplné zálohy na virtuálním počítači není zkrátit protokoly.


## <a name="does-the-solution-retry-or-auto-heal-the-backups"></a>Řešení znovu nebo automatické opravy zálohy?

Služba Azure Backup za určitých okolností spustí nápravné zálohování. Automatické opravy může být kterákoli z šesti podmínek uvedených níže:

  - Pokud se nezdaří z důvodu chyby ověření pořadové číslo položky protokol nebo rozdílovou zálohu, další protokol nebo rozdílovou zálohu je místo toho převést na úplné zálohy.
  - Pokud se nevytvořila žádná úplná záloha před protokol nebo rozdílovou zálohu, protokolu nebo rozdílové zálohy je místo toho převést na úplné zálohy.
  - Pokud poslední úplné zálohy v daném okamžiku je starší než 15 dnů, další protokol nebo rozdílovou zálohu je místo toho převést na úplné zálohy.
  - Po dokončení upgradu a rozšíření se spustí všechny úlohy zálohování, které zrušena z důvodu upgrade rozšíření znovu spuštěná.
  - Pokud budete chtít přepsat databázi během obnovení, další protokolu nebo rozdílové zálohování se nezdaří a úplné zálohování se aktivuje místo.
  - V případech, kdy je nutné obnovit protokol řetězců z důvodu změny v modelu obnovení databáze úplné zálohy získá automaticky spustí úplné v dalším plánu.

Automatické opravy jako funkce ve výchozím nastavení; zapnutá pro všechny uživatele Ale pak provést v případě, že vyberete možnost odhlásit se, níže:

  * V instanci SQL serveru v *C:\Program Files\Azure úlohy Backup\bin* složku, vytvořte nebo upravte **ExtensionSettingsOverrides.json** souboru.
  * V **ExtensionSettingsOverrides.json**, nastavte *{"EnableAutoHealer": false}* .
  * Uložte změny a zavřete soubor.
  * Na instanci serveru SQL Server otevřete **spravovat úlohy** a restartujte **AzureWLBackupCoordinatorSvc** služby.  

## <a name="can-i-control-as-to-how-many-concurrent-backups-run-on-the-sql-server"></a>Můžu rozhodnout o tom, kolik souběžných procesů zálohování spustit na serveru SQL server?

Ano. Můžete omezit rychlost, jakou zásadu zálohování, která spouští k minimalizaci vlivu na instanci systému SQL Server. Chcete-li změnit nastavení:
1. V instanci SQL serveru v *C:\Program Files\Azure úlohy Backup\bin* složky, vytvořit *ExtensionSettingsOverrides.json* souboru.
2. V *ExtensionSettingsOverrides.json* změňte **DefaultBackupTasksThreshold** nastavení na nižší hodnotu (například 5). <br>
  `{"DefaultBackupTasksThreshold": 5}`

3. Uložte změny a zavřete soubor.
4. Na instanci serveru SQL Server otevřete **Správce úloh**. Restartujte **AzureWLBackupCoordinatorSvc** služby.<br/> <br/>
 Zatímco tato metoda je výhodné, pokud aplikaci Zálohování, která spotřebovává velké množství prostředků, SQL Server [správce zdrojů](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor?view=sql-server-2017) je obecnějším způsobem, jak zadat omezení množství procesoru, vstupně-výstupních operací fyzické a paměti, která se dá příchozí žádosti o aplikace použití.

> [!NOTE]
> V uživatelském prostředí stále můžete pokračovat a naplánování záloh v každém okamžiku ale budou zpracovány v posuvné okno Řekněme, 5, podle výše uvedeném příkladu.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Ze sekundární repliky, spusťte úplné zálohování?
Podle omezení SQL lze kopírovat pouze úplné zálohování na sekundární replice; ale úplná záloha není povolena.

## <a name="can-i-protect-availability-groups-on-premises"></a>Můžete chránit skupiny dostupnosti na premises?
Ne. Azure Backup chrání databáze systému SQL Server běžící v Azure. Pokud skupinu dostupnosti (skupina dostupnosti) se pak rozdělí mezi Azure a v místním počítači, skupinu dostupnosti se dají chránit jenom v případě, že primární replikou je spuštěná v Azure. Kromě toho Azure Backup chrání pouze uzly, na kterých běží ve stejné oblasti Azure jako trezor služby Recovery Services.

## <a name="can-i-protect-availability-groups-across-regions"></a>Můžete chránit skupiny dostupnosti napříč oblastmi
Trezor služeb zotavení Azure Backup můžete zjišťovat a chránit všechny uzly, které jsou ve stejné oblasti jako trezor. V případě vaší skupiny dostupnosti AlwaysOn SQL serveru pokrývá více oblastí Azure, nastavení zálohování z oblasti, která má primárního uzlu. Azure Backup můžete zjišťovat a chránit všechny databáze ve skupině dostupnosti podle vašich potřeb zálohování. Pokud nejsou splněny vaše předvolby zálohování, zálohování se nezdaří a získáte výstrahy týkající se selhání.

## <a name="do-successful-backup-jobs-create-alerts"></a>Úspěšné úlohy zálohování vytvářejí výstrahy?
Ne. Úspěšné úlohy zálohování není Generovat výstrahy. Oznámení se odešlou jenom pro úlohy zálohování, které selžou. Jsou uvedené podrobné chování portálu výstrah [tady](backup-azure-monitoring-built-in-monitor.md). Ale výstrahy mají v případě, že vás zajímá i u úspěšných úloh, můžete použít [monitorováním s využitím Azure monitoru](backup-azure-monitoring-use-azuremonitor.md).

## <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Můžete zobrazit v nabídce úlohy zálohování naplánovaných úloh zálohování?
**Úlohu zálohování** nabídky zobrazí pouze ad-hoc úlohy zálohování. Naplánované úlohy použijte [monitorováním s využitím Azure monitoru](backup-azure-monitoring-use-azuremonitor.md).

## <a name="are-future-databases-automatically-added-for-backup"></a>Budoucí databáze automaticky přidávají pro zálohování?
Ano, můžete dosáhnout tuto funkci s [automatickou ochranu](backup-sql-server-database-azure-vms.md#enable-auto-protection).  

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups"></a>Když odstraním databázi z autoprotected instance, co se stane s zálohy?
Ztracené databázi z autoprotected instance jsou stále pokusil zálohy databáze. Z toho vyplývá, že se zobrazí jako není v pořádku, v části začíná odstraněnou databázi **zálohování položek** a je pořád chráněný.

Správný způsob zastavení ochrany tato databáze je provést **Zastavit zálohování** s **odstranit data** u této databáze.  

## <a name="if-i-do-stop-backup-operation-of-an-autoprotected-database-what-will-be-its-behavior"></a>Je-li zastavit zálohování databáze autoprotected jaká bude její chování?
Pokud tak učiníte **zastavení zálohování s zachovat data**, bude probíhat žádné budoucí zálohy a existující body obnovení zůstane zachován beze změny. Databáze, bude se mít nadále za jako chráněný a zobrazen pod **zálohování položek**.

Pokud tak učiníte **zastavení zálohování s odstraňováním dat**, bude probíhat žádné budoucí zálohy a existující body obnovení budou také odstraněny. Databáze se budou považovat za zrušení chráněné a zobrazen pod instance v konfiguraci zálohování. Na rozdíl od jiných nahoru chráněných databází, které můžete vybrat ručně nebo, který můžete získat autoprotected, ale tato databáze se zobrazí šedě a nejde ho vybrat. Jediný způsob, jak znovu nastavit ochranu databáze je zakázat automatickou ochranu na instanci. Teď můžete vybrat tuto databázi a konfigurace ochrany na něm nebo znovu povolit automatické ochrany na instanci znovu.

## <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior"></a>Pokud po byla nastavená ochrana se změnit název databáze, co bude chování?
Znovu s názvem databáze je považován za novou databázi. Proto služba bude zacházet s této situaci jako kdyby nebyla nalezena databáze a se nezdaří zálohování.

Můžete vybrat databáze, které přejmenovává a konfigurace ochrany na něj. V případě, že na instanci je povolena automatická ochrana, přejmenováno databáze bude automaticky zjistil a chráněné.

##  <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>Proč nevidím přidané database pro instanci autoprotected?
A databáze, kterou jste [přidat do autoprotected instance](backup-sql-server-database-azure-vms.md#enable-auto-protection) nemusí zobrazit hned pod chráněných položek. Je to proto, že zjišťování spuštěno obvykle každých 8 hodin. Můžete však zjišťovat a okamžitě chránit nové databáze ručně při spuštění funkce zjišťování tak, že vyberete **obnovení databází**, jak je znázorněno na následujícím obrázku.

  ![Ručně zjistit nově přidaná databáze](./media/backup-azure-sql-database/view-newly-added-database.png)


## <a name="next-steps"></a>Další postup

Zjistěte, jak [zálohování databáze serveru SQL Server](backup-azure-sql-database.md) , na kterém běží na Virtuálním počítači Azure.
