---
title: Obnovení databází SAP HANA na virtuálních počítačích Azure
description: V tomto článku zjistíte, jak obnovit SAP HANA databáze, které běží na Azure Virtual Machines.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 999edba61177758ad9039e81e789efcef99ca1de
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74287915"
---
# <a name="restore-sap-hana-databases-on-azure-vms"></a>Obnovení databází SAP HANA na virtuálních počítačích Azure

Tento článek popisuje, jak obnovit SAP HANA databáze, které běží na virtuálním počítači Azure (VM), že služba Azure Backup se zálohuje do Azure Backup Recovery Services trezoru. Obnovení lze použít k vytvoření kopií dat pro scénáře vývoje a testování nebo k návratu do předchozího stavu.

Další informace o tom, jak zálohovat SAP HANA databáze, najdete v tématu [zálohování databází SAP HANA na virtuálních počítačích Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database).

## <a name="restore-to-a-point-in-time-or-to-a-recovery-point"></a>Obnovení k určitému bodu v čase nebo na bod obnovení

Azure Backup může obnovit databáze SAP HANA, které běží na virtuálních počítačích Azure, takto:

* Obnoví konkrétní datum nebo čas (za sekundu) pomocí záloh protokolu. Azure Backup automaticky určuje odpovídající úplné a rozdílové zálohy a řetěz záloh protokolů, které jsou nutné k obnovení na základě vybraného času.

* Obnovení do konkrétního úplného nebo rozdílového zálohování pro obnovení do konkrétního bodu obnovení.

## <a name="prerequisites"></a>Požadavky

Před obnovením databáze mějte na paměti následující:

* Databázi můžete obnovit pouze do instance SAP HANA, která je ve stejné oblasti.

* Cílová instance musí být zaregistrovaná ve stejném trezoru jako zdroj.

* Azure Backup nemůže na jednom virtuálním počítači identifikovat dvě různé instance SAP HANA. Proto není možné obnovit data z jedné instance do druhé na stejném virtuálním počítači.

* Pokud chcete mít jistotu, že je cílová instance SAP HANA připravená k obnovení, zkontrolujte její stav **připravenosti na zálohování** :

  * Otevřete trezor, ve kterém je instance cílového SAP HANA zaregistrovaná.

  * Na řídicím panelu trezoru v části **Začínáme**klikněte na **zálohování** .

![Zálohování na řídicím panelu trezoru](media/sap-hana-db-restore/getting-started-backup.png)

* V části **zálohování**v části **co chcete zálohovat?** vyberte **SAP HANA na virtuálním počítači Azure** .

![Výběr SAP HANA na virtuálním počítači Azure](media/sap-hana-db-restore/sap-hana-backup.png)

* V části **zjišťování databáze ve virtuálních počítačích** klikněte na **Zobrazit podrobnosti.**

![Zobrazit podrobnosti](media/sap-hana-db-restore/view-details.png)

* Kontrola **připravenosti na zálohování** cílového virtuálního počítače

![Chráněné servery](media/sap-hana-db-restore/protected-servers.png)

* Další informace o typech obnovení, které SAP HANA podporuje, najdete v SAP HANA Note [1642148](https://launchpad.support.sap.com/#/notes/1642148) .

## <a name="restore-a-database"></a>Obnovení databáze

* Otevřete trezor, ve kterém se má zaregistrovat SAP HANA databáze, která se má obnovit.

* Na řídicím panelu trezoru v části **chráněné položky**vyberte **zálohované položky** .

![Zálohované položky](media/sap-hana-db-restore/backup-items.png)

* V části **zálohované položky**v části **typ správy zálohování** vyberte **SAP HANA na virtuálním počítači Azure** .

![Typ správy zálohování](media/sap-hana-db-restore/backup-management-type.png)

* Vyberte databázi, která se má obnovit.

 ![Databáze k obnovení](media/sap-hana-db-restore/database-to-restore.png)

* Projděte si nabídku databáze. Poskytuje informace o zálohování databáze, včetně:

  * Nejstarší a nejnovější body obnovení

  * Stav zálohování protokolu za posledních 24 a 72 hodin pro databázi

![Nabídka databáze](media/sap-hana-db-restore/database-menu.png)

* Vybrat **obnovit databázi**

* V části **obnovení konfigurace**určete, kam (nebo jak) se mají obnovit data:

  * **Alternativní umístění**: Obnovte databázi do alternativního umístění a zachovejte původní zdrojovou databázi.

  * **Přepsat databázi**: Obnovte data do stejné instance SAP HANA jako původní zdroj. Tato možnost přepíše původní databázi.

![Obnovit konfiguraci](media/sap-hana-db-restore/restore-configuration.png)

### <a name="restore-to-alternate-location"></a>Obnovit do alternativního umístění

* V nabídce **obnovit konfiguraci** v části **kde se má obnovit**vyberte **alternativní umístění**.

![Obnovit do alternativního umístění](media/sap-hana-db-restore/restore-alternate-location.png)

* Vyberte SAP HANA název hostitele a název instance, pro které chcete obnovit databázi.
* Ujistěte se, jestli je cílová instance SAP HANA připravená k obnovení, tím, že zajistíte její **připravenost na zálohování.** Další podrobnosti najdete v [části požadavky](#prerequisites) .
* Do pole **Název obnovené** databáze zadejte název cílové databáze.

> [!NOTE]
> Obnovení kontejneru Izolovaná databáze (SDC) musí splňovat tyto [kontroly](backup-azure-sap-hana-database-troubleshoot.md#single-container-database-sdc-restore).

* Pokud je to možné, vyberte **přepsat, pokud databáze se stejným názvem už ve vybrané instanci Hana existuje**.
* Vyberte **OK**.

![Obnovit konfiguraci – finální obrazovka](media/sap-hana-db-restore/restore-configuration-last.png)

* V **části vybrat bod obnovení**vyberte **protokoly (časové** okamžiky), které chcete [obnovit do konkrétního bodu v čase](#restore-to-a-specific-point-in-time). Případně můžete vybrat možnost **úplného rozdílu &** pro [obnovení do konkrétního bodu obnovení](#restore-to-a-specific-recovery-point).

### <a name="restore-and-overwrite"></a>Obnovit a přepsat

* V nabídce **obnovit konfiguraci** v části **kde se má obnovení**vyberte **přepsat databázi** > **OK**.

![Přepsání databáze](media/sap-hana-db-restore/overwrite-db.png)

* V **části vybrat bod obnovení**vyberte **protokoly (časové** okamžiky), které chcete [obnovit do konkrétního bodu v čase](#restore-to-a-specific-point-in-time). Případně můžete vybrat možnost **úplného rozdílu &** pro [obnovení do konkrétního bodu obnovení](#restore-to-a-specific-recovery-point).

### <a name="restore-to-a-specific-point-in-time"></a>Obnovení k určitému bodu v čase

Pokud jste jako typ obnovení vybrali možnost **protokoly (časový okamžik)** , udělejte toto:

* Vyberte bod obnovení z grafu protokolu a výběrem možnosti **OK** zvolte bod obnovení.

![Bod obnovení](media/sap-hana-db-restore/restore-point.png)

* V nabídce **obnovit** vyberte **obnovit** a spusťte úlohu obnovení.

![Výběr obnovení](media/sap-hana-db-restore/restore-restore.png)

* Sledujte průběh obnovy v **oznamovací** oblasti nebo ji Sledujte výběrem možnosti **obnovit úlohy** v nabídce databáze.

![Obnovení se úspěšně aktivovalo.](media/sap-hana-db-restore/restore-triggered.png)

### <a name="restore-to-a-specific-recovery-point"></a>Obnovit na určitý bod obnovení

Pokud jste jako typ obnovení vybrali možnost **úplný & rozdíl** , udělejte toto:

* Vyberte bod obnovení ze seznamu a vyberte možnost **OK** a zvolte bod obnovení.

![Obnovit určitý bod obnovení](media/sap-hana-db-restore/specific-recovery-point.png)

* V nabídce **obnovit** vyberte **obnovit** a spusťte úlohu obnovení.

![Spustit úlohu obnovení](media/sap-hana-db-restore/restore-specific.png)

* Sledujte průběh obnovy v **oznamovací** oblasti nebo ji Sledujte výběrem možnosti **obnovit úlohy** v nabídce databáze.

![Průběh obnovování](media/sap-hana-db-restore/restore-progress.png)

> [!NOTE]
> V rámci více databázových kontejnerů (MDC) se obnoví po obnovení systémové databáze do cílové instance jedna musí znovu spustit skript před registrací. Jenom potom se obnoví i následné obnovení databáze tenanta. Další informace najdete v tématu [řešení potíží – MDC Restore](backup-azure-sap-hana-database-troubleshoot.md#multiple-container-database-mdc-restore).

## <a name="next-steps"></a>Další kroky

* [Naučte](sap-hana-db-manage.md) se spravovat SAP HANA databází zálohovaných pomocí Azure Backup
