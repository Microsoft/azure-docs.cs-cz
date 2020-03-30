---
title: Obnovení databází SAP HANA na virtuálních počítačích Azure
description: V tomto článku zjistěte, jak obnovit databáze SAP HANA, které běží na virtuálních počítačích Azure.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 999edba61177758ad9039e81e789efcef99ca1de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74287915"
---
# <a name="restore-sap-hana-databases-on-azure-vms"></a>Obnovení databází SAP HANA na virtuálních počítačích Azure

Tento článek popisuje, jak obnovit databáze SAP HANA, které běží na Virtuálním počítači (VM) Azure, které služba Azure Backup zálohovala do trezoru služby Obnovení zálohování Azure. Obnovení lze použít k vytvoření kopií dat pro scénáře pro vývoj nebo testování nebo k návratu do předchozího stavu.

Další informace o tom, jak zálohovat databáze SAP HANA, najdete v tématu [Zálohování databází SAP HANA na virtuálních počítačích Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database).

## <a name="restore-to-a-point-in-time-or-to-a-recovery-point"></a>Obnovení do bodu v čase nebo bodu obnovení

Azure Backup můžete obnovit databáze SAP HANA, které běží na virtuálních počítačích Azure takto:

* Obnovení na určité datum nebo čas (na druhý) pomocí záloh protokolu. Azure Backup automaticky určuje příslušné úplné, rozdílové zálohy a řetězec záloh protokolu, které jsou nutné k obnovení na základě vybraného času.

* Obnovení na konkrétní úplné nebo rozdílové zálohy obnovit do určitého bodu obnovení.

## <a name="prerequisites"></a>Požadavky

Před obnovením databáze si všimněte následujícího:

* Databázi můžete obnovit pouze do instance SAP HANA, která je ve stejné oblasti.

* Cílová instance musí být registrována ve stejném trezoru jako zdroj.

* Azure Backup nemůže identifikovat dvě různé instance SAP HANA na stejném virtuálním počítači. Proto obnovení dat z jedné instance do druhé na stejném virtuálním počítači není možné

* Chcete-li zajistit, aby cílová instance SAP HANA byla připravena k obnovení, zkontrolujte její stav **připravenosti zálohování:**

  * Otevření úschovny, ve které je registrována cílová instance SAP HANA

  * Na řídicím panelu trezoru v části **Začínáme**zvolte **Zálohování**

![Zálohování na řídicím panelu úschovny](media/sap-hana-db-restore/getting-started-backup.png)

* V **části Zálohování**v části Co **SAP HANA in Azure VM** **chcete zálohovat?**

![Volba SAP HANA ve virtuálním počítači Azure](media/sap-hana-db-restore/sap-hana-backup.png)

* V **části Objevit disky DB ve virtuálních počítačích** klikněte na **Zobrazit podrobnosti.**

![Zobrazení podrobností](media/sap-hana-db-restore/view-details.png)

* Kontrola **připravenosti zálohování** cílového virtuálního počítače

![Chráněné servery](media/sap-hana-db-restore/protected-servers.png)

* Další informace o typech obnovení, které podporuje SAP HANA, naleznete v poznámce SAP HANA [1642148](https://launchpad.support.sap.com/#/notes/1642148)

## <a name="restore-a-database"></a>Obnovení databáze

* Otevření úschovny, ve které je registrována databáze SAP HANA, která má být obnovena

* Na řídicím panelu trezoru v části **Chráněné položky**zvolte **Zálohovat položky.**

![Položky zálohování](media/sap-hana-db-restore/backup-items.png)

* V **části Položky zálohování**vyberte v části Typ správy **zálohování** **SAP HANA ve virtuálním počítači Azure.**

![Typ správy zálohování](media/sap-hana-db-restore/backup-management-type.png)

* Vyberte databázi, která má být obnovena.

 ![Databáze k obnovení](media/sap-hana-db-restore/database-to-restore.png)

* Zkontrolujte nabídku databáze. Poskytuje informace o zálohování databáze, včetně:

  * Nejstarší a nejnovější body obnovení

  * Stav zálohování protokolu za posledních 24 a 72 hodin pro databázi

![Nabídka Databáze](media/sap-hana-db-restore/database-menu.png)

* Vybrat **obnovit DB**

* V části **Obnovit konfiguraci**určete, kde (nebo jak) chcete obnovit data:

  * **Alternativní umístění**: Obnovení databáze do alternativního umístění a zachovat původní zdrojovou databázi.

  * **Přepsat DB**: Obnovení dat do stejné instance SAP HANA jako původní zdroj. Tato možnost přepíše původní databázi.

![Obnovit konfiguraci](media/sap-hana-db-restore/restore-configuration.png)

### <a name="restore-to-alternate-location"></a>Obnovit do alternativního umístění

* V nabídce **Obnovit konfiguraci** vyberte v části **Kde obnovit** **položku Alternativní umístění**.

![Obnovit do alternativního umístění](media/sap-hana-db-restore/restore-alternate-location.png)

* Vyberte název hostitele SAP HANA a název instance, do kterého chcete databázi obnovit.
* Zkontrolujte, zda je cílová instance SAP HANA připravena k obnovení tím, že zajistí tekví **připravenosti na zálohování.** Další podrobnosti naleznete v [části požadavky.](#prerequisites)
* Do pole **Obnovený název db** zadejte název cílové databáze.

> [!NOTE]
> Jeden kontejner databáze (SDC) obnovení musí následovat tyto [kontroly](backup-azure-sap-hana-database-troubleshoot.md#single-container-database-sdc-restore).

* Pokud je to možné, vyberte **Přepsat, pokud db se stejným názvem již existuje na vybrané instanci HANA**.
* Vyberte **OK**.

![Obnovit konfiguraci - závěrečná obrazovka](media/sap-hana-db-restore/restore-configuration-last.png)

* V **povolte Select Restore point** **vyberte Protokoly (Bod v čase),** chcete-li [obnovit určitý bod v čase](#restore-to-a-specific-point-in-time). Nebo vyberte **plný & rozdíl** obnovit do [určitého bodu obnovení](#restore-to-a-specific-recovery-point).

### <a name="restore-and-overwrite"></a>Obnovení a přepsání

* V nabídce **Obnovit konfiguraci** vyberte v části **Kde obnovit**možnost **Přepsat DB** > **OK**.

![Přepsání databáze](media/sap-hana-db-restore/overwrite-db.png)

* V **povolte Select Restore point** **vyberte Protokoly (Bod v čase),** chcete-li [obnovit určitý bod v čase](#restore-to-a-specific-point-in-time). Nebo vyberte **plný & rozdíl** obnovit do [určitého bodu obnovení](#restore-to-a-specific-recovery-point).

### <a name="restore-to-a-specific-point-in-time"></a>Obnovení do určitého bodu v čase

Pokud jste jako typ obnovení vybrali **protokoly (Bod v čase),** postupujte takto:

* Vyberte bod obnovení z grafu protokolu a vyberte **OK,** chcete-li zvolit bod obnovení.

![Bod obnovení](media/sap-hana-db-restore/restore-point.png)

* V nabídce **Restore** vyberte **Obnovit,** chcete-li spustit úlohu obnovení.

![Vybrat obnovení](media/sap-hana-db-restore/restore-restore.png)

* Sledujte průběh obnovení v oblasti **Oznámení** nebo jej sledujte výběrem **možnosti Obnovit úlohy** v nabídce databáze.

![Obnovení se úspěšně spustilo.](media/sap-hana-db-restore/restore-triggered.png)

### <a name="restore-to-a-specific-recovery-point"></a>Obnovení do určitého bodu obnovení

Pokud jste jako typ obnovení vybrali **možnost Full & Differential,** postupujte takto:

* Vyberte bod obnovení ze seznamu a vyberte **OK,** chcete-li vybrat bod obnovení.

![Obnovit konkrétní bod obnovení](media/sap-hana-db-restore/specific-recovery-point.png)

* V nabídce **Restore** vyberte **Obnovit,** chcete-li spustit úlohu obnovení.

![Zahájit úlohu obnovení](media/sap-hana-db-restore/restore-specific.png)

* Sledujte průběh obnovení v oblasti **Oznámení** nebo jej sledujte výběrem **možnosti Obnovit úlohy** v nabídce databáze.

![Obnovení průběhu](media/sap-hana-db-restore/restore-progress.png)

> [!NOTE]
> V kontejneru více databází (MDC) obnoví po obnovení databáze systému do cílové instance, je třeba znovu spustit skript předběžné registrace. Teprve potom následné obnovení db bude úspěšné. Další informace naleznete [v řešení potíží – obnovení mdc](backup-azure-sap-hana-database-troubleshoot.md#multiple-container-database-mdc-restore).

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak](sap-hana-db-manage.md) spravovat databáze SAP HANA zálohované pomocí azure backupu
