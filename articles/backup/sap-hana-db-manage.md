---
title: Správa zálohovaných SAP HANA databází na virtuálních počítačích Azure
description: V tomto článku se seznámíte s běžnými úlohami při správě a monitorování SAP HANAch databází, které běží na virtuálních počítačích Azure.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: a9462f8608fc5ae35255ac321a0742b3f1834fde
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79252399"
---
# <a name="manage-and-monitor-backed-up-sap-hana-databases"></a>Správa a monitorování zálohovaných databází SAP HANA

Tento článek popisuje běžné úlohy pro správu a monitorování SAP HANAch databází, které běží na virtuálním počítači Azure a které se zálohují do Azure Backup Recovery Services trezoru služby [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) . Naučíte se monitorovat úlohy a výstrahy, aktivovat zálohování na vyžádání, upravovat zásady, zastavovat a obnovovat ochranu databáze a rušit registraci virtuálního počítače ze záloh.

Pokud jste ještě nenakonfigurovali zálohy pro databáze SAP HANA, přečtěte si téma [zálohování databází SAP HANA na virtuálních počítačích Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database).

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Monitorování úloh ručního zálohování na portálu

Azure Backup zobrazí všechny ručně aktivované úlohy v části **úlohy zálohování** na Azure Portal.

![Oddíl úlohy zálohování](./media/sap-hana-db-manage/backup-jobs.png)

Úlohy, které vidíte na tomto portálu, zahrnují zjišťování a registraci databází a operace zálohování a obnovení. Naplánované úlohy, včetně záloh protokolu, nejsou v této části uvedené. Nezobrazuje se zde také ručně aktivované zálohy z SAP HANA nativních klientů (Studio/řídicí panel/DBA).

![Seznam úloh zálohování](./media/sap-hana-db-manage/backup-jobs-list.png)

Další informace o monitorování najdete [v části monitorování v Azure Portal](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) a [monitorování pomocí Azure monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

## <a name="view-backup-alerts"></a>Zobrazit výstrahy zálohování

Výstrahy představují snadný způsob, jak monitorovat zálohy databází SAP HANA. Výstrahy vám pomůžou soustředit se na události, které vás zajímají, aniž byste se museli ztratit v mnoha událostech, které zálohování vygeneruje. Azure Backup vám umožní nastavit upozornění a můžou se monitorovat takto:

* Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
* Na řídicím panelu trezoru vyberte **výstrahy zálohování**.

  ![Výstrahy zálohování na řídicím panelu trezoru](./media/sap-hana-db-manage/backup-alerts-dashboard.png)

* Výstrahy budete moct zobrazit:

  ![Seznam výstrah zálohování](./media/sap-hana-db-manage/backup-alerts-list.png)

* Kliknutím na výstrahy zobrazíte další podrobnosti:

  ![Podrobnosti upozornění](./media/sap-hana-db-manage/alert-details.png)

Dnes Azure Backup umožňuje odesílání výstrah e-mailem. Jsou to tyto výstrahy:

* Aktivované pro všechna selhání zálohování.
* Konsolidovat na úrovni databáze podle kódu chyby.
* Odesílá se jenom pro první selhání zálohování databáze.

ToTo Další informace o monitorování najdete [v části monitorování v Azure Portal](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) a [monitorování pomocí Azure monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

## <a name="management-operations"></a>Operace správy

Azure Backup usnadňuje správu zálohovaných SAP HANA databáze díky četnosti operací správy, které podporuje. Tyto operace jsou podrobněji popsány v následujících částech.

### <a name="run-an-ad-hoc-backup"></a>Spuštění zálohování ad hoc

Zálohy se spouštějí v souladu s plánem zásad. Zálohu můžete spustit na vyžádání následujícím způsobem:

1. V nabídce trezoru klikněte na položku **zálohované položky**.
2. V části **zálohované položky**vyberte virtuální počítač, na kterém běží databáze SAP HANA, a pak klikněte na **Zálohovat nyní**.
3. V části **Zálohovat nyní**pomocí ovládacího prvku kalendáře vyberte poslední den, kdy se má bod obnovení zachovat. Pak klikněte na **OK**.
4. Monitorujte oznámení na portálu. Průběh úlohy můžete monitorovat na řídicím panelu trezoru > **úlohy zálohování** ** > probíhá**. V závislosti na velikosti databáze může vytváření prvotní zálohy chvíli trvat.

### <a name="run-sap-hana-native-client-backup-on-a-database-with-azure-backup-enabled"></a>SAP HANA spuštění zálohování nativního klienta v databázi s povolenou službou Azure Backup

Pokud chcete použít místní zálohu (pomocí HANA Studio/řídicího panelu) databáze, která se zálohuje pomocí Azure Backup, udělejte toto:

1. Počkejte na dokončení všech úplných záloh nebo zálohování protokolů databáze. Podívejte se na stav v SAP HANA studiu nebo řídicím panelu.
2. Zakažte zálohování protokolů a nastavte Katalog zálohování na systém souborů pro příslušnou databázi.
3. Provedete to tak, že dvakrát kliknete na **systemdb** > **Konfigurace** > **Vybrat databázi** > **filtr (protokol)** .
4. Nastavte **enable_auto_log_backup** na **ne**.
5. Nastavte **log_backup_using_backint** na **false**.
6. Proveďte úplné zálohování databáze na vyžádání.
7. Počkejte na dokončení úplného zálohování a zálohování katalogu.
8. Vrátí předchozí nastavení zpátky do těch pro Azure:
   * Nastavte **enable_auto_log_backup** na **Ano**.
   * Nastavte **log_backup_using_backint** na **hodnotu true**.

### <a name="change-policy"></a>Změnit zásady

U SAP HANA zálohované položky můžete změnit základní zásady.

* Na řídicím panelu trezoru klikněte na **zálohované položky**:

  ![Vybrat zálohované položky](./media/sap-hana-db-manage/backup-items.png)

* Výběr **SAP HANA na virtuálním počítači Azure**

  ![Výběr SAP HANA na virtuálním počítači Azure](./media/sap-hana-db-manage/sap-hana-in-azure-vm.png)

* Vyberte zálohovanou položku, jejíž základní zásady chcete změnit.
* Klikněte na existující zásady zálohování.

  ![Vybrat existující zásady zálohování](./media/sap-hana-db-manage/existing-backup-policy.png)

* Změňte zásadu výběrem ze seznamu. [V případě potřeby vytvořte nové zásady zálohování](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database#create-a-backup-policy) .

  ![Zvolit zásadu z rozevíracího seznamu](./media/sap-hana-db-manage/choose-backup-policy.png)

* Uložit změny

  ![Uložit změny](./media/sap-hana-db-manage/save-changes.png)

* Změna zásad ovlivní všechny přidružené zálohované položky a spustí odpovídající úlohy **Konfigurace ochrany** .

>[!NOTE]
> Jakákoli změna v období uchování se použije zpět na všechny starší body obnovení kromě nových.
>
> Zásady přírůstkového zálohování nelze použít pro databáze SAP HANA. Přírůstkové zálohování není v současné době pro tyto databáze podporováno.

### <a name="stop-protection-for-an-sap-hana-database"></a>Zastavení ochrany pro databázi SAP HANA

Ochranu databáze SAP HANA můžete zastavit několika způsoby:

* Zastavte všechny budoucí úlohy zálohování a odstraňte všechny body obnovení.
* Zastavte všechny budoucí úlohy zálohování a ponechte body obnovení beze změn.

Pokud se rozhodnete zachovat body obnovení, mějte na paměti tyto informace:

* Všechny body obnovení zůstanou nedotčeny trvale, všechna vyřazení se zastaví při zastavení ochrany s uchováním dat.
* Bude se vám účtovat za chráněnou instanci a spotřebované úložiště. Další informace najdete v tématu [Azure Backup ceny](https://azure.microsoft.com/pricing/details/backup/).
* Pokud odstraníte zdroj dat bez zastavení zálohování, nové zálohování se nezdaří.

Zastavení ochrany databáze:

* Na řídicím panelu trezoru vyberte **zálohované položky**.
* V části **typ správy zálohování**vyberte **SAP HANA na virtuálním počítači Azure** .

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

### <a name="upgrading-from-sap-hana-10-to-20"></a>Upgrade z SAP HANA 1,0 na 2,0

Naučte se, jak pokračovat v zálohování databáze SAP HANA [po upgradu z SAP HANA 1,0 na 2,0](backup-azure-sap-hana-database-troubleshoot.md#upgrading-from-sap-hana-10-to-20).

### <a name="upgrading-without-a-sid-change"></a>Upgrade bez změny SID

Naučte se, jak pokračovat v zálohování databáze SAP HANA, jejíž [SID se po upgradu nezměnilo](backup-azure-sap-hana-database-troubleshoot.md#upgrading-without-an-sid-change).

### <a name="unregister-an-sap-hana-database"></a>Zrušení registrace databáze SAP HANA

Zrušení registrace instance SAP HANA po zakázání ochrany, ale před odstraněním trezoru:

* Na řídicím panelu trezoru v části **Spravovat**vyberte **infrastruktura zálohování**.

   ![Výběr infrastruktury zálohování](./media/sap-hana-db-manage/backup-infrastructure.png)

* Výběr **typu správy zálohování** jako **úlohy na virtuálním počítači Azure**

   ![Výběr typu správy zálohování jako úlohy na virtuálním počítači Azure](./media/sap-hana-db-manage/backup-management-type.png)

* V části **chráněné servery**vyberte instanci, jejíž registraci chcete zrušit. Pokud chcete trezor odstranit, musíte zrušit registraci všech serverů nebo instancí.

* Klikněte pravým tlačítkem na chráněnou instanci a vyberte zrušit **registraci**.

   ![Vybrat zrušit registraci](./media/sap-hana-db-manage/unregister.png)

## <a name="next-steps"></a>Další kroky

* Naučte se [řešit běžné problémy při zálohování SAP HANA databází.](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)
