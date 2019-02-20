---
title: Nejčastější dotazy k zálohování databází systému SQL Server na virtuálních počítačích Azure s Azure Backup
description: Poskytuje odpovědi na běžné dotazy týkající se zálohování databází systému SQL Server na virtuálních počítačích Azure s Azure Backup.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 8/16/2018
ms.author: sogup
ms.openlocfilehash: a14406733ff60d53d4bf7792ff0c9a015c57d9b3
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430777"
---
# <a name="faq-on-sql-server-running-on-azure-vm-backup"></a>Nejčastější dotazy k SQL serveru běžícího na virtuálním počítači Azure backup

Tento článek obsahuje odpovědi na běžné dotazy týkající se zálohování databází systému SQL Server běžící na virtuálních počítačích Azure s [Azure Backup](backup-overview.md) služby.

> [!NOTE]
> Tato funkce je aktuálně ve verzi public preview.



## <a name="can-i-throttle-the-backup-speed"></a>Je možné omezovat rychlost zálohování?

Ano. Můžete omezit rychlost, jakou zásadu zálohování, která spouští k minimalizaci vlivu na instanci systému SQL Server. Chcete-li změnit nastavení:
1. V instanci SQL serveru v *složky C:\Program Files\Azure úlohy Backup\bin*, vytvořte **ExtensionSettingsOverrides.json** souboru.
2. V **ExtensionSettingsOverrides.json** změňte **DefaultBackupTasksThreshold** nastavení na nižší hodnotu (například 5) <br>
  ` {"DefaultBackupTasksThreshold": 5}`

3. Uložte provedené změny. Zavřete soubor.
4. Na instanci serveru SQL Server otevřete **Správce úloh**. Restartujte **AzureWLBackupCoordinatorSvc** služby.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Ze sekundární repliky, spusťte úplné zálohování?
Ne. Tato funkce není podporována.

## <a name="do-successful-backup-jobs-create-alerts"></a>Úspěšné úlohy zálohování vytvářejí výstrahy?

Ne. Úspěšné úlohy zálohování není Generovat výstrahy. Oznámení se odešlou jenom pro úlohy zálohování, které selžou.

## <a name="can-i-see-scheduled-backup-jobs-in-the-jobs-menu"></a>Můžete zobrazit v nabídce úloh naplánovaných úloh zálohování?

Ne. **Úlohy zálohování** nabídky zobrazí podrobnosti úlohy na vyžádání, ale ne naplánovaných úloh zálohování. Pokud selžou i všechny naplánované úlohy zálohování, podrobnosti jsou dostupné ve výstrahách neúspěšnou úlohu. Můžete sledovat všechny naplánované a pomocí úlohy zálohování ad hoc [SQL Server Management Studio](manage-monitor-sql-database-backup.md).

## <a name="are-future-databases-automatically-added-for-backup"></a>Budoucí databáze automaticky přidávají pro zálohování?

Ne. Při konfiguraci ochrany pro instanci systému SQL Server, pokud vyberete možnost úrovni serveru, se přidají všechny databáze. Pokud chcete přidat databáze na instanci systému SQL Server po dokončení konfigurace ochrany, je třeba ručně přidat nové databáze před nimi chránit. Databáze nejsou automaticky součástí nakonfigurovanou ochranu.

##  <a name="how-do-i-restart-protection-after-changing-recovery-type"></a>Jak můžu opětné spuštění ochrany po změně typu obnovení?

Aktivujte úplné zálohování. Protokol zálohování začne podle očekávání.

## <a name="can-i-protect-availability-groups-on-premises"></a>Můžete chránit skupiny dostupnosti v místním?

Ne. Azure Backup chrání servery SQL v Azure. Pokud se skupina dostupnosti (AG) se pak rozdělí mezi Azure a v místním počítači, skupinu dostupnosti se dají chránit jenom v případě, že primární replikou je spuštěná v Azure. Kromě toho Azure Backup chrání pouze uzly spuštěnými ve stejné oblasti Azure jako trezor služby Recovery Services.

## <a name="can-i-protect-availability-groups-across-regions"></a>Můžete chránit skupiny dostupnosti napříč oblastmi

Azure Backup trezoru Recovery Services můžete zjišťovat a chránit všechny uzly, které jsou ve stejné oblasti jako trezor služby Recovery Services. Pokud máte vždy ve skupině dostupnosti SQL pokrývající víc oblastí Azure, musíte nakonfigurovat zálohování z oblasti, která má primárního uzlu. Azure Backup bude moct zjišťuje a chrání všechny databáze ve skupině dostupnosti podle preference zálohování. Pokud není splněná předvolby zálohování, zálohování se nezdaří a zobrazí se výstrahy týkající se selhání.

## <a name="can-i-exclude-databases-with-auto-protection-enabled"></a>Můžete vyloučit databází se zapnutou automatickou ochranu

Ne, [automatickou ochranu](backup-azure-sql-database.md#enable-auto-protection) platí pro celou instanci. Nemůžete chránit selektivně databází pomocí automatické ochrany instancí.

## <a name="can-i-have-different-policies-in-an-auto-protected-instance"></a>Může mít různé zásady v automaticky chráněná instance?

Pokud už máte některé chráněné databáze v instanci, budou i nadále se dají chránit pomocí jejich příslušných zásad i po zapnutí **ON** [automatickou ochranu](backup-azure-sql-database.md#enable-auto-protection) možnost. Nicméně nechráněných databází spolu s těch, které by v budoucnu přidat budou mít pouze jednu zásadu, která je definovat v rámci **konfigurace zálohování** po databáze dwrepository. Na rozdíl od jiných chráněných databází, nelze ve skutečnosti, dokonce i změnit zásady pro databáze v rámci automaticky chráněná instance.
Pokud chcete udělat, je jediným způsobem zakázat automatickou ochranu pro instanci prozatím a potom změňte zásady pro tuto databázi. Teď můžete znovu povolit automatickou ochranu pro tuto instanci.

## <a name="if-i-delete-a-database-from-auto-protection-will-backups-stop"></a>Když odstraním z Automatická ochrana databáze přestane zálohování?

Ne, pokud z automaticky chráněná instance je vyřadit databázi, zálohy pro tuto databázi jsou stále k pokusu o. Z toho vyplývá, že se zobrazí jako není v pořádku, v části začíná odstraněnou databázi **zálohování položek** a je stále považovány za chráněné.

Jediný způsob, jak zastavit ochranu této databáze je zakázat [automatickou ochranu](backup-azure-sql-database.md#enable-auto-protection) na instanci prozatím a klikněte na tlačítko **Zastavit zálohování** v části **zálohování položek**pro tuto databázi. Teď můžete znovu povolit automatickou ochranu pro tuto instanci.

##  <a name="why-cant-i-see-an-added-database-for-an-auto-protected-instance"></a>Proč nevidím přidané databáze automaticky chráněná instance?

Nemusíte vidět nově přidaná databáze do [automaticky chráněny](backup-azure-sql-database.md#enable-auto-protection) okamžitě chráněný v rámci instance chráněné položky. Je to proto, že zjišťování spuštěno obvykle každých 8 hodin. Uživatel však můžete spustit ruční zjišťování pomocí **obnovení databází** možnost pro zjišťování a ochranu nové databáze hned, jak je znázorněno obrázku níže:

  ![Zobrazí nově přidaná databáze](./media/backup-azure-sql-database/view-newly-added-database.png)

## <a name="next-steps"></a>Další postup

[Zjistěte, jak](backup-azure-sql-database.md) zálohování databáze serveru SQL Server běžící na Virtuálním počítači Azure.
