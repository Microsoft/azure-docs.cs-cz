---
title: Správa zálohovaných SAP HANA databází na virtuálních počítačích Azure
description: V tomto článku se seznámíte s běžnými úlohami při správě a monitorování SAP HANAch databází, které běží na virtuálních počítačích Azure.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 54d3341a83873ad3cc50815f04a0b252bb44438e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101703762"
---
# <a name="manage-and-monitor-backed-up-sap-hana-databases"></a>Správa a monitorování zálohovaných databází SAP HANA

Tento článek popisuje běžné úlohy pro správu a monitorování SAP HANAch databází, které běží na virtuálním počítači Azure a které se zálohují do Azure Backup Recovery Services trezoru služby [Azure Backup](./backup-overview.md) . Naučíte se monitorovat úlohy a výstrahy, aktivovat zálohování na vyžádání, upravovat zásady, zastavovat a obnovovat ochranu databáze a rušit registraci virtuálního počítače ze záloh.

Pokud jste ještě nenakonfigurovali zálohy pro databáze SAP HANA, přečtěte si téma [zálohování databází SAP HANA na virtuálních počítačích Azure](./backup-azure-sap-hana-database.md).

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Monitorování úloh ručního zálohování na portálu

Azure Backup zobrazí všechny ručně aktivované úlohy v části **úlohy zálohování** na Azure Portal.

![Oddíl úlohy zálohování](./media/sap-hana-db-manage/backup-jobs.png)

Úlohy, které vidíte na tomto portálu, zahrnují zjišťování a registraci databází a operace zálohování a obnovení. Naplánované úlohy, včetně záloh protokolu, nejsou v této části uvedené. Nezobrazuje se zde také ručně aktivované zálohy z SAP HANA nativních klientů (Studio/řídicí panel/DBA).

![Seznam úloh zálohování](./media/sap-hana-db-manage/backup-jobs-list.png)

Další informace o monitorování najdete [v části monitorování v Azure Portal](./backup-azure-monitoring-built-in-monitor.md) a [monitorování pomocí Azure monitor](./backup-azure-monitoring-use-azuremonitor.md).

## <a name="view-backup-alerts"></a>Zobrazit výstrahy zálohování

Výstrahy představují snadný způsob, jak monitorovat zálohy databází SAP HANA. Výstrahy vám pomůžou soustředit se na události, které vás zajímají, aniž byste se museli ztratit v mnoha událostech, které zálohování vygeneruje. Azure Backup vám umožní nastavit upozornění a můžou se monitorovat takto:

* Přihlaste se na [Azure Portal](https://portal.azure.com/).
* Na řídicím panelu trezoru vyberte **výstrahy zálohování**.

  ![Výstrahy zálohování na řídicím panelu trezoru](./media/sap-hana-db-manage/backup-alerts-dashboard.png)

* Výstrahy budete moct zobrazit:

  ![Seznam výstrah zálohování](./media/sap-hana-db-manage/backup-alerts-list.png)

* Kliknutím na výstrahy zobrazíte další podrobnosti:

  ![Podrobnosti výstrahy](./media/sap-hana-db-manage/alert-details.png)

Dnes Azure Backup umožňuje odesílání výstrah e-mailem. Jsou to tyto výstrahy:

* Aktivované pro všechna selhání zálohování.
* Konsolidovat na úrovni databáze podle kódu chyby.
* Odesílá se jenom pro první selhání zálohování databáze.

Další informace o monitorování najdete [v části monitorování v Azure Portal](./backup-azure-monitoring-built-in-monitor.md) a [monitorování pomocí Azure monitor](./backup-azure-monitoring-use-azuremonitor.md).

## <a name="management-operations"></a>Operace správy

Azure Backup usnadňuje správu zálohovaných SAP HANA databáze díky četnosti operací správy, které podporuje. Tyto operace jsou podrobněji popsány v následujících částech.

### <a name="run-an-on-demand-backup"></a>Spuštění zálohování na vyžádání

Zálohy se spouštějí v souladu s plánem zásad. Zálohu můžete spustit na vyžádání následujícím způsobem:

1. V nabídce trezoru vyberte **zálohované položky**.
2. V části **zálohované položky** vyberte virtuální počítač, na kterém běží databáze SAP HANA, a pak vyberte **Zálohovat nyní**.
3. V části **Zálohovat nyní** vyberte typ zálohy, kterou chcete provést. Pak vyberte **OK**. Tato záloha se zachová v závislosti na zásadách přidružených k této zálohované položce.
4. Monitorujte oznámení na portálu. Průběh úlohy můžete monitorovat na řídicím panelu trezoru > probíhající **úlohy zálohování**  >  . V závislosti na velikosti databáze může vytváření prvotní zálohy chvíli trvat.

Ve výchozím nastavení je uchovávání záloh na vyžádání 45 dní.

### <a name="hana-native-client-integration"></a>Integrace nativního klienta HANA

#### <a name="backup"></a>Backup

Zálohy na vyžádání aktivované ze všech nativních klientů HANA (do **Backint**) se zobrazí v seznamu zálohování na stránce **zálohované položky** .

![Poslední spuštění zálohy](./media/sap-hana-db-manage/last-backups.png)

[Tyto zálohy můžete také monitorovat](#monitor-manual-backup-jobs-in-the-portal) na stránce **úlohy zálohování** .

Tyto zálohy na vyžádání se zobrazí také v seznamu bodů obnovení pro obnovení.

![Seznam bodů obnovení](./media/sap-hana-db-manage/list-restore-points.png)

#### <a name="restore"></a>Obnovení

Obnovení spouštěné z nativních klientů HANA (pomocí **Backint**) pro obnovení na stejný počítač se dá [monitorovat](#monitor-manual-backup-jobs-in-the-portal) na stránce **úlohy zálohování** .

### <a name="run-sap-hana-native-client-backup-to-local-disk-on-a-database-with-azure-backup-enabled"></a>Spustit SAP HANA zálohování nativního klienta na místní disk v databázi s povoleným Azure Backup

Pokud chcete použít místní zálohu (pomocí HANA Studio/řídicího panelu) databáze, která se zálohuje pomocí Azure Backup, udělejte toto:

1. Počkejte na dokončení všech úplných záloh nebo zálohování protokolů databáze. Podívejte se na stav v SAP HANA studiu nebo řídicím panelu.
2. pro příslušnou databázi
    1. Zrušte nastavení parametrů backint. Provedete to tak, že dvakrát kliknete na konfigurace **SystemDB**  >    >  **vybrat databázový**  >  **filtr (protokol)**.
        * enable_auto_log_backup: ne
        * log_backup_using_backint: false
        * catalog_backup_using_backint: false
3. Pořídit úplnou zálohu databáze na vyžádání
4. Pak převratte postup. Pro stejné relevantní databáze uvedené výše
    1. opětovné povolení parametrů backint
        1. catalog_backup_using_backint: true
        1. log_backup_using_backint: true
        1. enable_auto_log_backup: Ano

### <a name="manage-or-clean-up-the-hana-catalog-for-a-database-with-azure-backup-enabled"></a>Správa nebo vyčištění katalogu HANA pro databázi s povoleným Azure Backup

Pokud chcete katalog záloh upravit nebo vyčistit, postupujte následovně:

1. Počkejte na dokončení všech úplných záloh nebo zálohování protokolů databáze. Podívejte se na stav v SAP HANA studiu nebo řídicím panelu.
2. pro příslušnou databázi
    1. Zrušte nastavení parametrů backint. Provedete to tak, že dvakrát kliknete na konfigurace **SystemDB**  >    >  **vybrat databázový**  >  **filtr (protokol)**.
        * enable_auto_log_backup: ne
        * log_backup_using_backint: false
        * catalog_backup_using_backint: false
3. Upravit katalog a odebrat starší položky
4. Pak převratte postup. Pro stejné relevantní databáze uvedené výše
    1. opětovné povolení parametrů backint
        1. catalog_backup_using_backint: true
        1. log_backup_using_backint: true
        1. enable_auto_log_backup: Ano

### <a name="change-policy"></a>Změnit zásady

U SAP HANA zálohované položky můžete změnit základní zásady.

* Na řídicím panelu trezoru klikněte na **zálohované položky**:

  ![Vybrat zálohované položky](./media/sap-hana-db-manage/backup-items.png)

* Výběr **SAP HANA na virtuálním počítači Azure**

  ![Výběr SAP HANA na virtuálním počítači Azure](./media/sap-hana-db-manage/sap-hana-in-azure-vm.png)

* Vyberte zálohovanou položku, jejíž základní zásady chcete změnit.
* Vyberte existující zásady zálohování.

  ![Vybrat existující zásady zálohování](./media/sap-hana-db-manage/existing-backup-policy.png)

* Změňte zásadu výběrem ze seznamu. [V případě potřeby vytvořte nové zásady zálohování](./backup-azure-sap-hana-database.md#create-a-backup-policy) .

  ![Zvolit zásadu z rozevíracího seznamu](./media/sap-hana-db-manage/choose-backup-policy.png)

* Uložte změny.

  ![Uložte změny.](./media/sap-hana-db-manage/save-changes.png)

* Změna zásad ovlivní všechny přidružené zálohované položky a spustí odpovídající úlohy **Konfigurace ochrany** .

>[!NOTE]
> Jakákoli změna v období uchování se použije zpět na všechny starší body obnovení kromě nových.

### <a name="modify-policy"></a>Upravit zásadu

Úprava zásad pro změnu typů zálohování, frekvencí a rozsahu uchovávání.

>[!NOTE]
>Jakákoli změna v období uchování se použije zpět na všechny starší body obnovení, a to i na nové.

1. Na řídicím panelu trezoru klikněte na **spravovat > zásady zálohování** a vyberte zásadu, kterou chcete upravit.

   ![Vyberte zásadu, kterou chcete upravit.](./media/sap-hana-db-manage/manage-backup-policies.png)

1. Vyberte **Upravit**.

   ![Vyberte Upravit.](./media/sap-hana-db-manage/modify-policy.png)

1. Vyberte frekvenci pro typy zálohování.

   ![Výběr četnosti zálohování](./media/sap-hana-db-manage/choose-frequency.png)

Změna zásad ovlivní všechny přidružené zálohované položky a spustí odpovídající úlohy **Konfigurace ochrany** .

### <a name="inconsistent-policy"></a>Nekonzistentní zásady

V některých případech může operace upravit zásady vést k **nekonzistentní** verzi zásad pro některé zálohované položky. K tomu dojde v případě, že se po aktivaci operace Upravit zásadu pro zálohovanou položku nezdařila odpovídající úloha **Konfigurace ochrany** . V zobrazení zálohovaná položka se zobrazí takto:

![Nekonzistentní zásady](./media/sap-hana-db-manage/inconsistent-policy.png)

Verzi zásad můžete u všech ovlivněných položek opravit jedním kliknutím:

![Opravit verzi zásad](./media/sap-hana-db-manage/fix-policy-version.png)

### <a name="stop-protection-for-an-sap-hana-database"></a>Zastavení ochrany pro databázi SAP HANA

Ochranu databáze SAP HANA můžete zastavit několika způsoby:

* Zastavte všechny budoucí úlohy zálohování a odstraňte všechny body obnovení.
* Zastavte všechny budoucí úlohy zálohování a ponechte body obnovení beze změn.

Pokud se rozhodnete zachovat body obnovení, mějte na paměti tyto informace:

* Všechny body obnovení zůstanou nedotčené trvale a všechny vyřazení se zastaví při zastavení ochrany s uchováním dat.
* Bude se vám účtovat chráněná instance a spotřebované úložiště. Další informace najdete v tématu [Azure Backup ceny](https://azure.microsoft.com/pricing/details/backup/).
* Pokud odstraníte zdroj dat bez zastavení zálohování, nové zálohování se nezdaří.

Zastavení ochrany databáze:

* Na řídicím panelu trezoru vyberte **zálohované položky**.
* V části **typ správy zálohování** vyberte **SAP HANA na virtuálním počítači Azure** .

  ![Výběr SAP HANA na virtuálním počítači Azure](./media/sap-hana-db-manage/sap-hana-azure-vm.png)

* Vyberte databázi, pro kterou chcete zastavit ochranu:

  ![Vyberte databázi pro zastavení ochrany](./media/sap-hana-db-manage/select-database.png)

* V nabídce databáze vyberte **Zastavit zálohování**.

  ![Vyberte Zastavit zálohování.](./media/sap-hana-db-manage/stop-backup.png)

* V nabídce **Zastavit zálohování** vyberte, jestli se mají data zachovat nebo odstranit. Pokud chcete, zadejte důvod a komentář.

  ![Vybrat zachovat nebo odstranit data](./media/sap-hana-db-manage/retain-backup-data.png)

* Vyberte **Zastavit zálohování**.

### <a name="resume-protection-for-an-sap-hana-database"></a>Obnovení ochrany databáze SAP HANA

Když zastavíte ochranu pro databázi SAP HANA, pokud vyberete možnost **uchovat data záloh** , můžete později obnovit ochranu. Pokud nezachováte zálohovaná data, nebudete moci obnovit ochranu.

Obnovení ochrany databáze SAP HANA:

* Otevřete zálohovanou položku a vyberte **pokračovat v zálohování**.

   ![Vybrat pokračovat v zálohování](./media/sap-hana-db-manage/resume-backup.png)

* V nabídce **zásady zálohování** vyberte zásadu a pak vyberte **Uložit**.

### <a name="upgrading-from-sdc-to-mdc"></a>Upgrade z SDC na MDC

Naučte se, jak pokračovat v zálohování databáze SAP HANA [po upgradu z SDC na MDC](backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-a-change-in-sid).

### <a name="upgrading-from-sdc-to-mdc-without-a-sid-change"></a>Upgrade z SDC na MDC bez změny SID

Naučte se, jak pokračovat v zálohování databáze SAP HANA, jejíž [SID se po upgradu z SDC na MDC nezměnilo](backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-no-change-in-sid).

### <a name="upgrading-to-a-new-version-in-either-sdc-or-mdc"></a>Upgrade na novou verzi buď v SDC nebo MDC

Naučte se, jak pokračovat v zálohování databáze SAP HANA, [jejíž verze se upgraduje](backup-azure-sap-hana-database-troubleshoot.md#sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm).

### <a name="unregister-an-sap-hana-instance"></a>Zrušení registrace instance SAP HANA

Zrušení registrace instance SAP HANA po zakázání ochrany, ale před odstraněním trezoru:

* Na řídicím panelu trezoru v části **Spravovat** vyberte **infrastruktura zálohování**.

   ![Výběr infrastruktury zálohování](./media/sap-hana-db-manage/backup-infrastructure.png)

* Výběr **typu správy zálohování** jako **úlohy na virtuálním počítači Azure**

   ![Výběr typu správy zálohování jako úlohy na virtuálním počítači Azure](./media/sap-hana-db-manage/backup-management-type.png)

* V části **chráněné servery** vyberte instanci, jejíž registraci chcete zrušit. Pokud chcete trezor odstranit, musíte zrušit registraci všech serverů nebo instancí.

* Klikněte pravým tlačítkem na chráněnou instanci a vyberte zrušit **registraci**.

   ![Vybrat zrušit registraci](./media/sap-hana-db-manage/unregister.png)

### <a name="re-register-extension-on-the-sap-hana-server-vm"></a>Opětovné zaregistrování rozšíření na virtuálním počítači s SAP HANA serverem

V některých případech může mít rozšíření úlohy na virtuálním počítači vliv na jeden důvod nebo jiné. V takových případech se začnou zdařit všechny operace aktivované na virtuálním počítači. Pak možná budete muset znovu zaregistrovat rozšíření na virtuálním počítači. Operace opětovného registrace znovu nainstaluje rozšíření zálohování úlohy na virtuálním počítači, aby operace pokračovaly.

Tuto možnost použijte opatrně: když se aktivuje na virtuálním počítači s již nedobrým rozšířením, tato operace způsobí, že se rozšíření dokončí znovu. To může způsobit selhání všech probíhajících úloh. Před aktivací operace opětovného registrace se podívejte na jeden nebo více [příznaků](backup-azure-sap-hana-database-troubleshoot.md#re-registration-failures) .

## <a name="next-steps"></a>Další kroky

* Naučte se [řešit běžné problémy při zálohování SAP HANA databází.](./backup-azure-sap-hana-database-troubleshoot.md)
