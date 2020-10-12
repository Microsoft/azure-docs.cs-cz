---
title: Nejčastější dotazy k zálohování SQL Server databází na virtuálních počítačích Azure
description: Získejte odpovědi na běžné dotazy týkající se zálohování SQL Server databází na virtuálních počítačích Azure pomocí Azure Backup.
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: 6abfdb09fe16272e870fff517359759968417f79
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91461219"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>Nejčastější dotazy týkající se SQL Server databází, které běží na zálohování virtuálních počítačů Azure

Tento článek obsahuje odpovědi na běžné dotazy týkající se zálohování SQL Server databází, které běží na virtuálních počítačích Azure a používají službu [Azure Backup](backup-overview.md) .

## <a name="can-i-use-azure-backup-for-iaas-vm-as-well-as-sql-server-on-the-same-machine"></a>Můžu použít Azure Backup pro virtuální počítač s IaaS a taky SQL Server na stejném počítači?

Ano, na jednom virtuálním počítači můžete mít zálohování virtuálních počítačů i zálohování SQL. V tomto případě interně na virtuálním počítači spustíme úplnou zálohu jenom pro kopírování, aby se protokoly nezkrátily.

## <a name="does-the-solution-retry-or-auto-heal-the-backups"></a>Opakuje řešení opakování nebo automaticky zaretušuje zálohy?

Za určitých okolností služba Azure Backup spustí znovu média zálohování. Automatické vyretušování může nastat pro některé ze šesti podmínek uvedených níže:

- Pokud se protokol nebo rozdílová záloha nezdařila z důvodu chyby ověření LSN, je místo toho převeden další protokol nebo rozdílové zálohování na úplnou zálohu.
- Pokud před zálohováním protokolu nebo rozdílového zálohování neproběhla žádná úplná záloha, protokol nebo rozdílové zálohování se místo toho převede na úplnou zálohu.
- Pokud je nejnovější úplné zálohování v čase starší než 15 dní, převede se další protokol nebo rozdílové zálohování na úplnou zálohu.
- Všechny úlohy zálohování, které se zruší kvůli upgradu rozšíření, se po dokončení upgradu znovu aktivují a spustí se rozšíření.
- Pokud se rozhodnete přepsat databázi během obnovení, další zálohování protokolu nebo rozdílové zálohy se nezdařila a místo toho se spustí úplné zálohování.
- V případech, kdy je potřeba k resetování řetězů protokolů z důvodu změny v modelu obnovení databáze, se úplná aktivace automaticky aktivuje v dalším plánu.

Automatické zaretušování je ve výchozím nastavení povolené pro všechny uživatele. Pokud se ale rozhodnete, že ho chcete odhlásit, proveďte následující kroky:

- V SQL Server instanci ve složce *C:\Program Files\Azure úlohy Backup\bin* vytvořte nebo upravte **ExtensionSettingsOverrides.jsv** souboru.
- V **ExtensionSettingsOverrides.jsna**, nastavte *{"EnableAutoHealer": false}*.
- Uložte změny a zavřete soubor.
- V SQL Server instanci otevřete **úlohu spravovat** a restartujte službu **AzureWLBackupCoordinatorSvc** .

## <a name="can-i-control-how-many-concurrent-backups-run-on-the-sql-server"></a>Můžu řídit, kolik souběžných záloh běží na SQL serveru?

Ano. Rychlost, s jakou se zásady zálohování spouštějí, můžete omezit tak, aby se minimalizoval dopad na instanci SQL Server. Postup změny nastavení:

1. V SQL Server instanci v adresáři *C:\Program Files\Azure úlohy Backup\bin* vytvořte *ExtensionSettingsOverrides.jsv* souboru.
2. V *ExtensionSettingsOverrides.jsv* souboru změňte nastavení **DefaultBackupTasksThreshold** na nižší hodnotu (například 5). <br>
  `{"DefaultBackupTasksThreshold": 5}`
<br>
Výchozí hodnota DefaultBackupTasksThreshold je **20**.

3. Uložte změny a zavřete soubor.
4. V SQL Server instance otevřete **Správce úloh**. Restartujte službu **AzureWLBackupCoordinatorSvc** .<br/> <br/>
 I když tato metoda pomáhá, pokud zálohovací aplikace spotřebovává velké množství prostředků, je SQL Server [Správce zdrojů](/sql/relational-databases/resource-governor/resource-governor) obecnější způsob, jak určit omezení pro procesor, fyzickou/v/a paměť, které mohou používat příchozí žádosti o aplikace.

> [!NOTE]
> V uživatelském prostředí můžete pořád pokračovat a naplánovat tolik záloh v daném okamžiku. Budou se ale zpracovávat v posuvných intervalech, tedy 5, podle výše uvedeného příkladu.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Můžu spustit úplnou zálohu ze sekundární repliky?

Podle omezení SQL můžete spustit příkaz Kopírovat pouze úplné zálohování na sekundární replice. Úplná záloha však není povolena.

## <a name="can-i-protect-availability-groups-on-premises"></a>Můžu chránit skupiny dostupnosti místně?

Ne. Azure Backup chrání SQL Server databáze běžící v Azure. Pokud je skupina dostupnosti (AG) rozložená mezi Azure a místními počítači, může být AG chráněná jenom v případě, že je primární replika spuštěná v Azure. Azure Backup také chrání pouze uzly, které jsou spuštěny ve stejné oblasti Azure jako trezor Recovery Services.

## <a name="can-i-protect-availability-groups-across-regions"></a>Můžu chránit skupiny dostupnosti napříč oblastmi?

Trezor Recovery Services Azure Backup může detekovat a chránit všechny uzly, které jsou ve stejné oblasti jako trezor. Pokud vaše skupina dostupnosti Always On SQL Server zahrnuje více oblastí Azure, nastavte zálohu z oblasti, která má primární uzel. Azure Backup může zjistit a chránit všechny databáze ve skupině dostupnosti podle předvolby zálohování. Pokud vaše předvolba zálohování není splněná, zálohování se nezdaří a zobrazí se výstraha o selhání.

## <a name="do-successful-backup-jobs-create-alerts"></a>Vytvářejí úspěšné úlohy zálohování upozornění?

Ne. Úspěšné úlohy zálohování negenerují výstrahy. Výstrahy se odesílají jenom pro úlohy zálohování, které selžou. Podrobné chování výstrah na portálu [najdete tady](backup-azure-monitoring-built-in-monitor.md). Pokud vás ale zajímá, že máte výstrahy i pro úspěšné úlohy, můžete použít [monitorování pomocí Azure monitor](backup-azure-monitoring-use-azuremonitor.md).

## <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Můžu v nabídce úlohy zálohování Zobrazit naplánované úlohy zálohování?

V nabídce **úloha zálohování** se zobrazují všechny plánované operace a operace na vyžádání s výjimkou naplánovaných záloh protokolů, protože můžou být velmi časté. V případě naplánovaných úloh protokolu použijte [monitorování pomocí Azure monitor](backup-azure-monitoring-use-azuremonitor.md).

## <a name="are-future-databases-automatically-added-for-backup"></a>Budou se automaticky zálohovat i budoucí databáze?

Ano, tuto možnost můžete dosáhnout [automatickou ochranou](backup-sql-server-database-azure-vms.md#enable-auto-protection).  

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups"></a>Když odstraním databázi z autoprotected instance, co se stane se zálohami?

Pokud je databáze vyřazena z autoprotected instance, zálohování databáze se stále pokouší. To znamená, že Odstraněná databáze začne v rámci **zálohované položky** zobrazovat stav není v pořádku a je pořád chráněná.

Správným způsobem zastavení ochrany této databáze je **zastavení zálohování** s **odstraňováním dat** v této databázi.  

## <a name="if-i-do-stop-backup-operation-of-an-autoprotected-database-what-will-be-its-behavior"></a>Když zabráním operaci zálohování funkce autoprotected Database, která bude její chování?

Pokud **zastavíte zálohování s uchováním dat**, nebudou probíhat žádná budoucí zálohování a stávající body obnovení zůstanou beze změny. Databáze bude i nadále považována za chráněnou a zobrazí se v části **zálohované položky**.

Pokud **zálohování ukončíte pomocí odstranit data**, nebudou probíhat žádná budoucí zálohování a existující body obnovení budou také odstraněny. Databáze bude považována za nechráněnou a zobrazí se v rámci instance v části Konfigurace zálohování. Na rozdíl od jiných chráněných databází, které je možné vybrat ručně nebo které můžou získat autoochranu, se ale tato databáze zobrazuje šedě a nedá se vybrat. Jediným způsobem, jak tuto databázi znovu chránit, je vypnout automatickou ochranu instance. Nyní můžete vybrat tuto databázi a nakonfigurovat na ni ochranu nebo znovu zapnout automatickou ochranu instance.

## <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior"></a>Když změním název databáze poté, co byla chráněna, jaký bude chování?

Přejmenovaná databáze je považována za novou databázi. Takže se služba bude považovat za tuto situaci, jako kdyby se databáze nenašla a selhala v zálohování.

Můžete vybrat databázi, která je teď přejmenovaná, a nakonfigurovat na ni ochranu. Je-li v instanci povolena Automatická ochrana, přejmenovaná databáze bude automaticky rozpoznána a chráněna.

## <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>Proč se mi nezobrazuje přidaná databáze pro autoprotected instance?

Databáze, kterou [přidáte do autoprotected instance](backup-sql-server-database-azure-vms.md#enable-auto-protection) , se nemusí hned zobrazit v části chráněné položky. Důvodem je to, že zjišťování se obvykle spouští každých 8 hodin. Můžete však okamžitě vyhledat a chránit nové databáze, pokud ručně spustíte zjišťování výběrem možnosti znovu **zjistit databáze**, jak je znázorněno na následujícím obrázku:

  ![Ruční zjištění nově přidané databáze](./media/backup-azure-sql-database/view-newly-added-database.png)
  
## <a name="can-i-protect-databases-that-have-tde-transparent-data-encryption-turned-on-and-will-the-database-stay-encrypted-through-the-entire-backup-process"></a>Můžu chránit databáze, které mají TDE (transparentní šifrování dat) zapnuté a že databáze zůstane zašifrovaná prostřednictvím celého procesu zálohování?

Ano, Azure Backup podporuje zálohování databází SQL Server nebo serveru s povoleným TDE. Zálohování podporuje [TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) pomocí klíčů spravovaných pomocí Azure nebo klíčů spravovaných zákazníkem (BYOK).  Zálohování neprovádí v rámci procesu zálohování žádné šifrování SQL, takže databáze zůstane při zálohování zašifrovaná.

## <a name="next-steps"></a>Další kroky

Naučte se, jak [zálohovat databázi SQL Server](backup-azure-sql-database.md) , která běží na virtuálním počítači Azure.
