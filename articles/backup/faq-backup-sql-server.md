---
title: Nejčastější dotazy k zálohování databází systému SQL Server na virtuálních počítačích Azure s Azure Backup
description: Najděte odpovědi na běžné dotazy týkající se zálohování databází systému SQL Server na virtuálních počítačích Azure s Azure Backup.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 8/16/2018
ms.author: sogup
ms.openlocfilehash: 3b7649a029c6c44cd8a25ea553ff2091f816dd3c
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/01/2019
ms.locfileid: "57214822"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>Nejčastější dotazy týkající se databáze SQL serveru, na kterých běží zálohování virtuálních počítačů Azure

Tento článek odpovědi na běžné dotazy o zálohování databáze systému SQL Server, ve kterých běží na Azure virtual machines (VM) a které používají [Azure Backup](backup-overview.md) služby.

> [!NOTE]
> Tato funkce je aktuálně ve verzi public preview.

## <a name="can-i-throttle-the-backup-speed"></a>Je možné omezovat rychlost zálohování?

Ano. Můžete omezit rychlost, jakou zásadu zálohování, která spouští k minimalizaci vlivu na instanci systému SQL Server. Chcete-li změnit nastavení:
1. V instanci SQL serveru v *C:\Program Files\Azure úlohy Backup\bin* složky, vytvořit *ExtensionSettingsOverrides.json* souboru.
2. V *ExtensionSettingsOverrides.json* změňte **DefaultBackupTasksThreshold** nastavení na nižší hodnotu (například 5). <br>
  ` {"DefaultBackupTasksThreshold": 5}`

3. Uložte změny a zavřete soubor.
4. Na instanci serveru SQL Server otevřete **Správce úloh**. Restartujte **AzureWLBackupCoordinatorSvc** služby.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Ze sekundární repliky, spusťte úplné zálohování?
Ne. Tato funkce není podporována.

## <a name="do-successful-backup-jobs-create-alerts"></a>Úspěšné úlohy zálohování vytvářejí výstrahy?

Ne. Úspěšné úlohy zálohování není Generovat výstrahy. Oznámení se odešlou jenom pro úlohy zálohování, které selžou.

## <a name="can-i-see-scheduled-backup-jobs-in-the-jobs-menu"></a>Můžete zobrazit v nabídce úloh naplánovaných úloh zálohování?

Ne. **Úlohy zálohování** nabídce se zobrazí podrobnosti úlohy na vyžádání, ale ne naplánovaných úloh zálohování. Pokud selžou i všechny naplánované úlohy zálohování, zjistíte podrobnosti neúspěšné úlohy výstrah. Chcete-li monitorovat všechny plánované a neplánované úlohy zálohování, použijte [SQL Server Management Studio](manage-monitor-sql-database-backup.md).

## <a name="are-future-databases-automatically-added-for-backup"></a>Budoucí databáze automaticky přidávají pro zálohování?

Ne. Při nastavování ochrany pro instanci systému SQL Server, všechny databáze se přidají při výběru možnosti úroveň serveru. Po nastavení ochrany, je třeba ručně přidat nové databáze před nimi chránit. Nová databáze nejsou chráněné automaticky.

##  <a name="how-do-i-restart-protection-after-i-change-recovery-type"></a>Jak se po změně typu obnovení restartovat ochrany?

Aktivujte úplné zálohování. Protokol zálohování začne podle očekávání.

## <a name="can-i-protect-availability-groups-on-premises"></a>Můžete chránit skupiny dostupnosti na premises?

Ne. Azure Backup chrání databáze systému SQL Server běžící v Azure. Pokud skupinu dostupnosti (skupina dostupnosti) se pak rozdělí mezi Azure a v místním počítači, skupinu dostupnosti se dají chránit jenom v případě, že primární replikou je spuštěná v Azure. Kromě toho Azure Backup chrání pouze uzly, na kterých běží ve stejné oblasti Azure jako trezor služby Recovery Services.

## <a name="can-i-protect-availability-groups-across-regions"></a>Můžete chránit skupiny dostupnosti napříč oblastmi

Trezor služeb zotavení Azure Backup můžete zjišťovat a chránit všechny uzly, které jsou ve stejné oblasti jako trezor. V případě vaší skupiny dostupnosti AlwaysOn SQL serveru pokrývá více oblastí Azure, nastavení zálohování z oblasti, která má primárního uzlu. Azure Backup můžete zjišťovat a chránit všechny databáze ve skupině dostupnosti podle vašich potřeb zálohování. Pokud nejsou splněny vaše předvolby zálohování, zálohování se nezdaří a získáte výstrahy týkající se selhání.

## <a name="can-i-exclude-databases-with-autoprotection-enabled"></a>Můžete vyloučit databází s autoprotection, kterou povolené?

Ne. Autoprotection, kterou [platí pro celou instanci](backup-azure-sql-database.md#enable-auto-protection). Autoprotection, kterou nelze použít pro selektivní ochranu databáze v instanci.

## <a name="can-i-have-different-policies-in-an-autoprotected-instance"></a>Může mít různé zásady v instanci autoprotected?

Pokud vaše instance již obsahuje některé chráněné databáze, budou dál chránit podle jejich příslušných zásad i po [zapnout autoprotection, kterou](backup-azure-sql-database.md#enable-auto-protection). Nicméně nechráněných databází a databází, které přidáte později budou mít jenom jednu zásadu. Můžete definovat tyto zásady v rámci **konfigurace zálohování** po výběru databáze. Na rozdíl od jiných chráněných databází, nelze ve skutečnosti, dokonce i změnit zásady pro databázi, která je v instanci autoprotected.
Jediný způsob, jak změnit zásady této databáze je dočasně zakázat autoprotection, kterou pro instanci. Potom znovu povolit autoprotection, kterou pro instanci.

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-will-backups-stop"></a>Když odstraním databázi z autoprotected instance, přestane zálohování?

Ne. Ztracené databázi z autoprotected instance jsou stále pokusil zálohy databáze. Z toho vyplývá, že se zobrazí jako není v pořádku, v části začíná odstraněnou databázi **zálohování položek** a je pořád chráněný.

Jediný způsob, jak zastavit ochranu této databáze je dočasně [zakázat autoprotection, kterou](backup-azure-sql-database.md#enable-auto-protection) na instanci. Potom v části **zálohování položek** pro databázi, vyberte **Zastavit zálohování**. Potom znovu povolit autoprotection, kterou pro tuto instanci.

##  <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>Proč nevidím přidané database pro instanci autoprotected?

A databáze, kterou jste [přidat do autoprotected instance](backup-azure-sql-database.md#enable-auto-protection) nemusí zobrazit hned pod chráněných položek. Je to proto, že zjišťování spuštěno obvykle každých 8 hodin. Můžete však zjišťovat a okamžitě chránit nové databáze ručně při spuštění funkce zjišťování tak, že vyberete **obnovení databází**, jak je znázorněno na následujícím obrázku.

  ![Ručně zjistit nově přidaná databáze](./media/backup-azure-sql-database/view-newly-added-database.png)

## <a name="next-steps"></a>Další postup

Zjistěte, jak [zálohování databáze serveru SQL Server](backup-azure-sql-database.md) , na kterém běží na Virtuálním počítači Azure.
