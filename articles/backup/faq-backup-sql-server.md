---
title: Nejčastější dotazy – zálohování databází SQL Serveru na virtuálních počítačích Azure
description: Najděte odpovědi na běžné otázky týkající se zálohování databází SQL Serveru na virtuálních počítačích Azure pomocí Azure Backup.
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: a973761bf16e2d271d718e4a8b29e08624276987
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247706"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>Nejčastější dotazy k databázím SQL Serveru spuštěným na zálohě virtuálního počítače Azure

Tento článek odpovídá na běžné otázky týkající se zálohování databází SQL Serveru, které běží na virtuálních počítačích Azure a používají službu [Azure Backup.](backup-overview.md)

## <a name="can-i-use-azure-backup-for-iaas-vm-as-well-as-sql-server-on-the-same-machine"></a>Můžu použít zálohu Azure pro virtuální počítač IaaS i SQL Server na stejném počítači?

Ano, můžete mít zálohování virtuálních počítačových a SQL zálohování na stejném virtuálním počítači. V takovém případě interně aktivujeme úplnou zálohu pouze pro kopírování na virtuálním počítači, abychom nezkrátižovali protokoly.

## <a name="does-the-solution-retry-or-auto-heal-the-backups"></a>Má řešení opakovat nebo auto-léčit zálohy?

Za určitých okolností služba Azure Backup aktivuje nápravné zálohy. Auto-léčit se může stát pro některou ze šesti podmínek uvedených níže:

- Pokud protokol nebo rozdílové zálohování selže z důvodu chyby ověření LSN, další protokol nebo rozdílové zálohy je místo toho převedena na úplnou zálohu.
- Pokud před protokolem nebo rozdílovou zálohou nedošlo k žádné úplné záloze, bude tato záloha protokolu nebo rozdílové zálohy převedena na úplnou zálohu.
- Pokud je poslední úplné zálohy point-in-time je starší než 15 dní, další protokol nebo rozdílové zálohy je místo toho převedena na úplnou zálohu.
- Všechny úlohy zálohování, které se zruší z důvodu upgradu rozšíření, se po dokončení upgradu a spuštění rozšíření znovu aktivují.
- Pokud se rozhodnete přepsat databázi během obnovení, další protokol/rozdílové zálohování se nezdaří a místo toho se aktivuje úplná záloha.
- V případech, kdy je vyžadována úplná záloha k obnovení řetězů protokolů z důvodu změny v modelu obnovení databáze, se automaticky spustí úplné v dalším plánu.

Auto-heal jako schopnost je povolena pro všechny uživatele ve výchozím nastavení; Nicméně v případě, že se rozhodnete odhlásit se z něj, proveďte následující:

- V instanci serveru SQL Server ve složce *C:\Program Files\Azure Workload Backup\bin* vytvořte nebo upravte soubor **ExtensionSettingsOverrides.json.**
- V **souboru ExtensionSettingsOverrides.json**nastavte *{"EnableAutoHealer": false}*.
- Uložte změny a zavřete soubor.
- V instanci serveru SQL Server otevřete **službu Správa úloh** a restartujte službu **AzureWLBackupCoordinatorSvc.**

## <a name="can-i-control-how-many-concurrent-backups-run-on-the-sql-server"></a>Lze určit, kolik souběžných záloh běží na serveru SQL?

Ano. Můžete omezit rychlost, jakou se spustí zásady zálohování, abyste minimalizovali dopad na instanci serveru SQL Server. Změna nastavení:

1. V instanci serveru SQL Server vytvořte ve složce *C:\Program Files\Azure Workload Backup\bin* soubor *ExtensionSettingsOverrides.json.*
2. V souboru *ExtensionSettingsOverrides.json* změňte nastavení **DefaultBackupTasksThreshold** na nižší hodnotu (například 5). <br>
  `{"DefaultBackupTasksThreshold": 5}`
<br>
Výchozí hodnota DefaultBackupTasksThreshold je **20**.

3. Uložte změny a zavřete soubor.
4. V instanci serveru SQL Server otevřete **Správce úloh**. Restartujte službu **AzureWLBackupCoordinatorSvc.**<br/> <br/>
 Zatímco tato metoda pomáhá, pokud zálohovací aplikace spotřebovává velké množství prostředků, SQL Server [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor?view=sql-server-2017) je obecnější způsob, jak určit omezení na množství procesoru, fyzické vi a paměti, které příchozí požadavky aplikací lze použít.

> [!NOTE]
> V UX můžete stále pokračovat a naplánovat tolik záloh v daném okamžiku, nicméně budou zpracovány v posuvném okně řekněme 5, podle výše uvedeného příkladu.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Mohu spustit úplnou zálohu ze sekundární repliky?

Podle omezení SQL můžete spustit možnost Kopírovat pouze úplné zálohování na sekundární replice; Úplné zálohování však není povoleno.

## <a name="can-i-protect-availability-groups-on-premises"></a>Mohu chránit místní skupiny dostupnosti?

Ne. Azure Backup chrání databáze SQL Serveru spuštěné v Azure. Pokud je skupina dostupnosti (AG) rozprostřená mezi Azure a místními počítači, AG může být chráněna jenom v případě, že primární replika běží v Azure. Azure Backup také chrání pouze uzly, které běží ve stejné oblasti Azure jako trezor služby Recovery Services.

## <a name="can-i-protect-availability-groups-across-regions"></a>Můžu chránit skupiny dostupnosti napříč oblastmi?

Trezor služby Azure Backup Recovery Services dokáže rozpoznat a chránit všechny uzly, které jsou ve stejné oblasti jako trezor. Pokud vaše SQL Server always on dostupnost skupiny zahrnuje více oblastí Azure, nastavte zálohu z oblasti, která má primární uzel. Azure Backup může rozpoznat a chránit všechny databáze ve skupině dostupnosti podle předvoleb zálohování. Pokud není splněna vaše předvolba zálohování, zálohy se nezdaří a zobrazí se upozornění na selhání.

## <a name="do-successful-backup-jobs-create-alerts"></a>Vytvářejí úspěšné úlohy zálohování upozornění?

Ne. Úspěšné úlohy zálohování negenerují výstrahy. Výstrahy jsou odesílány pouze pro úlohy zálohování, které se nezdaří. Podrobné chování pro portálové výstrahy je dokumentováno [zde](backup-azure-monitoring-built-in-monitor.md). V případě, že máte zájem, máte upozornění i pro úspěšné úlohy, můžete použít [monitorování pomocí Azure Monitor](backup-azure-monitoring-use-azuremonitor.md).

## <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Lze zobrazit naplánované úlohy zálohování v nabídce Úlohy zálohování?

V nabídce **Úloha zálohování** se zobrazí pouze úlohy zálohování na vyžádání. Pro naplánované monitorování úloh [pomocí Nástroje pro sledování Azure](backup-azure-monitoring-use-azuremonitor.md).

## <a name="are-future-databases-automatically-added-for-backup"></a>Budou se automaticky zálohovat i budoucí databáze?

Ano, této funkce můžete dosáhnout pomocí [automatické ochrany](backup-sql-server-database-azure-vms.md#enable-auto-protection).  

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups"></a>Pokud odstraním databázi z automaticky chráněné instance, co se stane se zálohami?

Pokud je databáze vynechána z autochráněné instance, jsou stále pokusy o zálohování databáze. To znamená, že odstraněná databáze se začne zobrazovat jako není v pořádku v části **Položky zálohování** a je stále chráněna.

Správný způsob, jak zastavit ochranu této databáze je provést **zastavit zálohování** s **odstranit data** v této databázi.  

## <a name="if-i-do-stop-backup-operation-of-an-autoprotected-database-what-will-be-its-behavior"></a>Pokud zastavím operaci zálohování automaticky chráněné databáze, jaké bude její chování?

Pokud **zálohování zastavíte s uchováním dat**, nebudou probíhat žádné budoucí zálohy a existující body obnovení zůstanou beze změny. Databáze bude stále považována za chráněnou a bude zobrazena v části **Položky zálohování**.

Pokud **zálohování zastavíte odstraněním dat**, nebudou probíhat žádné budoucí zálohy a budou odstraněny také stávající body obnovení. Databáze bude považována za nechráněnou a zobrazí se pod instancí v části Konfigurovat zálohování. Na rozdíl od jiných databází chráněných předem, které lze vybrat ručně nebo které mohou být automaticky chráněny, se však tato databáze zobrazí šedě a nelze ji vybrat. Jediný způsob, jak znovu chránit tuto databázi je zakázat automatickou ochranu na instanci. Nyní můžete vybrat tuto databázi a nakonfigurovat ochranu na ní nebo znovu povolit automatickou ochranu instance znovu.

## <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior"></a>Pokud změním název databáze poté, co byla chráněna, jaké bude chování?

Přejmenovaná databáze je považována za novou databázi. Proto služba bude považovat tuto situaci, jako by databáze nebyla nalezena a selhání zálohy.

Můžete vybrat databázi, která je nyní přejmenována, a nakonfigurovat ochranu. V případě, že je v instanci povolena automatická ochrana, přejmenovaná databáze bude automaticky rozpoznána a chráněna.

## <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>Proč nevidím přidanou databázi pro autochráněnou instanci?

Databáze, kterou [přidáte do autochráněné instance,](backup-sql-server-database-azure-vms.md#enable-auto-protection) se nemusí okamžitě zobrazit pod chráněnými položkami. Důvodem je, že zjišťování obvykle běží každých 8 hodin. Nové databáze však můžete okamžitě zjistit a chránit, pokud zjišťování spustíte ručně tak, že vyberete **znovu objevit centrální deb ,** jak je znázorněno na následujícím obrázku:

  ![Ruční zjištění nově přidané databáze](./media/backup-azure-sql-database/view-newly-added-database.png)

## <a name="next-steps"></a>Další kroky

Zjistěte, jak [zálohovat databázi SQL Serveru,](backup-azure-sql-database.md) která běží na virtuálním počítači Azure.
