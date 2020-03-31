---
title: Správa zálohovaných databází SAP HANA na virtuálních počítačích Azure
description: V tomto článku se dozvíte běžné úkoly pro správu a monitorování databází SAP HANA, které běží na virtuálních počítačích Azure.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 89fd7f23163d301817e767771257d9bc6f4ed526
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480058"
---
# <a name="manage-and-monitor-backed-up-sap-hana-databases"></a>Správa a monitorování zálohovaných databází SAP HANA

Tento článek popisuje běžné úlohy pro správu a monitorování databází SAP HANA, které běží na virtuálním počítači Azure (VM) a které jsou zálohovány do trezoru služby Azure Backup Recovery Services službou [Azure Backup.](https://docs.microsoft.com/azure/backup/backup-overview) Dozvíte se, jak monitorovat úlohy a výstrahy, aktivovat zálohování na vyžádání, upravovat zásady, zastavit a obnovit ochranu databáze a zrušit registraci virtuálního počítače ze záloh.

Pokud jste ještě nenakonfigurovali zálohy pro databáze SAP HANA, přečtěte si informace [o zálohování databází SAP HANA na virtuálních počítačích Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database).

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Sledování úloh ručního zálohování na portálu

Azure Backup zobrazuje všechny ručně aktivované úlohy v části **Úlohy zálohování** na webu Azure Portal.

![Oddíl Úlohy zálohování](./media/sap-hana-db-manage/backup-jobs.png)

Úlohy, které se zobrazí na tomto portálu, zahrnují zjišťování a registraci databáze a operace zálohování a obnovení. Naplánované úlohy, včetně záloh protokolu nejsou zobrazeny v této části. Ručně spuštěné zálohy od nativních klientů SAP HANA (Studio / Cockpit / DBA Cockpit) se zde také nezobrazují.

![Seznam úloh zálohování](./media/sap-hana-db-manage/backup-jobs-list.png)

Další informace o monitorování najdete [v části Monitorování na webu Azure Portal](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) a Monitorování pomocí Azure [Monitoru](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

## <a name="view-backup-alerts"></a>Zobrazit výstrahy zálohování

Výstrahy jsou snadným prostředkem sledování zálohování databází SAP HANA. Výstrahy vám pomohou zaměřit se na události, na kterých vám nejvíce záleží, aniž byste se ztratili v množství událostí, které záloha generuje. Azure Backup umožňuje nastavit výstrahy a lze je sledovat následujícím způsobem:

* Přihlaste se k [portálu Azure](https://portal.azure.com/).
* Na řídicím panelu trezoru vyberte **Možnost Zálohování výstrah**.

  ![Výstrahy zálohování na řídicím panelu trezoru](./media/sap-hana-db-manage/backup-alerts-dashboard.png)

* Budete mít možnost zobrazit výstrahy:

  ![Seznam výstrah zálohování](./media/sap-hana-db-manage/backup-alerts-list.png)

* Kliknutím na upozornění zobrazíte další podrobnosti:

  ![Podrobnosti upozornění](./media/sap-hana-db-manage/alert-details.png)

Služba Azure Backup dnes umožňuje odesílání výstrah prostřednictvím e-mailu. Jedná se o tyto výstrahy:

* Spuštěno pro všechny selhání zálohování.
* Konsolidováno na úrovni databáze kódem chyby.
* Odesláno pouze pro první selhání zálohování v databázi.

Další informace o monitorování najdete [v části Monitorování na webu Azure Portal](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) a Monitorování pomocí Azure [Monitoru](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

## <a name="management-operations"></a>Operace řízení

Azure Backup usnadňuje správu zálohované databáze SAP HANA s množstvím operací správy, které podporuje. Tyto operace jsou podrobněji popsány v následujících částech.

### <a name="run-an-on-demand-backup"></a>Spuštění zálohy na vyžádání

Zálohy spustit v souladu s plánem zásad. Zálohu můžete spustit na vyžádání následujícím způsobem:

1. V nabídce úschovny klikněte na **Zálohovat položky**.
2. V **části Položky zálohování**vyberte virtuální ms se spuštěnou databází SAP HANA a klikněte na **Zálohovat .**
3. V **aplikaci Backup Now**vyberte pomocí ovládacího prvku kalendář poslední den, kdy má být bod obnovení zachován. Pak klikněte na **OK**.
4. Sledujte oznámení portálu. Průběh úlohy můžete sledovat na řídicím panelu trezoru >**probíhající** **úlohy** > zálohování . V závislosti na velikosti databáze může chvíli trvat vytvoření počáteční zálohy.

### <a name="hana-native-client-integration"></a>Integrace nativního klienta HANA

Nyní na vyžádání úplné zálohy aktivované z některého z nativních klientů HANA se zobrazí jako úplná záloha na stránce **Položky zálohování.**

![Poslední spuštění záloh](./media/sap-hana-db-manage/last-backups.png)

Tyto ad-hoc úplné zálohy se také zobrazí v seznamu bodů obnovení pro obnovení.

![Seznam bodů obnovení](./media/sap-hana-db-manage/list-restore-points.png)

### <a name="run-sap-hana-native-client-backup-on-a-database-with-azure-backup-enabled"></a>Spuštění zálohování nativního klienta SAP HANA v databázi s povolenou zálohou Azure

Pokud chcete provést místní zálohu (pomocí HANA Studio / Cockpit) databáze, která je zálohována pomocí služby Azure Backup, postupujte takto:

1. Počkejte na všechny úplné nebo protokol zálohy pro dokončení databáze. Zkontrolujte stav v SAP HANA Studio/ Cockpit.
2. Zakažte zálohování protokolů a nastavte katalog záloh do systému souborů pro příslušnou databázi.
3. Chcete-li to provést, poklepejte na filtr**databáze** > **(Protokolu)** konfigurace **systemdb** > **.** > 
4. Nastavit **enable_auto_log_backup** na **ne**.
5. Nastavte **log_backup_using_backint** na **hodnotu False**.
6. Provést úplnou zálohu databáze na vyžádání.
7. Počkejte na dokončení úplné zálohy a zálohy katalogu.
8. Vrátit předchozí nastavení zpět na nastavení pro Azure:
   * Nastavte **enable_auto_log_backup** na **Ano**.
   * Nastavte **log_backup_using_backint** na **hodnotu True**.

### <a name="change-policy"></a>Změnit zásady

Můžete změnit základní zásady pro položku zálohování SAP HANA.

* Na řídicím panelu trezoru přejděte na **Položky zálohování**:

  ![Výběr položek zálohování](./media/sap-hana-db-manage/backup-items.png)

* Volba **SAP HANA ve virtuálním počítači Azure**

  ![Volba SAP HANA ve virtuálním počítači Azure](./media/sap-hana-db-manage/sap-hana-in-azure-vm.png)

* Vyberte položku zálohování, jejíž základní zásady chcete změnit.
* Klikněte na existující zásady zálohování

  ![Výběr existujících zásad zálohování](./media/sap-hana-db-manage/existing-backup-policy.png)

* Změňte zásadu a vyžádej si ze seznamu. V případě potřeby [vytvořte novou zásadu zálohování.](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database#create-a-backup-policy)

  ![Výběr zásad z rozevíracího seznamu](./media/sap-hana-db-manage/choose-backup-policy.png)

* Uložení změn

  ![Uložení změn](./media/sap-hana-db-manage/save-changes.png)

* Změna zásad ovlivní všechny přidružené položky zálohování a aktivuje odpovídající **úlohy ochrany konfigurace.**

>[!NOTE]
> Každá změna retenčního období bude použita zpětně na všechny starší body výtěžnosti kromě nových bodů.
>
> Zásady přírůstkového zálohování nelze použít pro databáze SAP HANA. Přírůstkové zálohování není aktuálně podporováno pro tyto databáze.

### <a name="modify-policy"></a>Změnit zásady

Upravte zásady a změňte typy zálohování, frekvence a rozsah uchování.

>[!NOTE]
>Každá změna retenčního období bude kromě nových použita zpětně na všechny starší body výtěžnosti.

1. Na řídicím panelu úschovny přejděte na **Spravovat > zásady zálohování** a vyberte zásady, které chcete upravit.

   ![Zvolte zásadu, kterou chcete upravit.](./media/sap-hana-db-manage/manage-backup-policies.png)

1. Vyberte **Změnit**.

   ![Vybrat změnit](./media/sap-hana-db-manage/modify-policy.png)

1. Zvolte frekvenci pro typy zálohování.

   ![Zvolte frekvenci zálohování](./media/sap-hana-db-manage/choose-frequency.png)

Změna zásad ovlivní všechny přidružené položky zálohování a aktivuje odpovídající **úlohy ochrany konfigurace.**

### <a name="inconsistent-policy"></a>Nekonzistentní zásady

V některých příležitostech může operace změnit zásady vést k **nekonzistentní** verzi zásad pro některé položky zálohování. K tomu dochází, když se po aktivaci operace zásad změny nezdaří odpovídající úloha **ochrany konfigurace** pro položku zálohování. V zobrazení položky zálohování se zobrazí následujícím způsobem:

![Nekonzistentní zásady](./media/sap-hana-db-manage/inconsistent-policy.png)

Verzi zásad můžete opravit pro všechny ovlivněné položky jedním kliknutím:

![Opravit verzi zásad](./media/sap-hana-db-manage/fix-policy-version.png)

### <a name="stop-protection-for-an-sap-hana-database"></a>Zastavení ochrany databáze SAP HANA

Ochranu databáze SAP HANA můžete ukončit několika způsoby:

* Zastavte všechny budoucí úlohy zálohování a odstraňte všechny body obnovení.
* Zastavte všechny budoucí úlohy zálohování a ponechte body obnovení beze změny.

Pokud se rozhodnete ponechat body obnovení, mějte na paměti tyto podrobnosti:

* Všechny body obnovení zůstanou neporušené navždy, veškeré prořezávání se zastaví při zastavení ochrany s uchováním dat.
* Bude se vám účtovat chráněná instance a spotřebované úložiště. Další informace najdete v tématu [Azure Backup pricing](https://azure.microsoft.com/pricing/details/backup/).
* Pokud odstraníte zdroj dat bez zastavení zálohování, nové zálohy se nezdaří.

Ukončení ochrany databáze:

* Na řídicím panelu úschovny vyberte **Zálohovat položky**.
* V části **Typ správy zálohování**vyberte SAP **HANA ve virtuálním počítači Azure.**

  ![Výběr SAP HANA v virtuálním počítači Azure](./media/sap-hana-db-manage/sap-hana-azure-vm.png)

* Vyberte databázi, pro kterou chcete ochranu zastavit:

  ![Vyberte databázi, která má být ochranou zastavit.](./media/sap-hana-db-manage/select-database.png)

* V nabídce databáze vyberte **Zastavit zálohování**.

  ![Vybrat zastavení zálohování](./media/sap-hana-db-manage/stop-backup.png)

* V nabídce **Zastavit zálohování** vyberte, zda chcete data uchovávat nebo odstraňovat. Pokud chcete, uveďte důvod a komentář.

  ![Vybrat zachovat nebo odstranit data](./media/sap-hana-db-manage/retain-backup-data.png)

* Vyberte **možnost Zastavit zálohování**.

### <a name="resume-protection-for-an-sap-hana-database"></a>Obnovení ochrany databáze SAP HANA

Když zastavíte ochranu databáze SAP HANA, pokud vyberete možnost **Zachovat záložní data,** můžete později obnovit ochranu. Pokud zálohovaná data neuchováte, nebudete moci ochranu obnovit.

Obnovení ochrany databáze SAP HANA:

* Otevřete položku zálohování a vyberte **Pokračovat v zálohování**.

   ![Vybrat obnovení zálohování](./media/sap-hana-db-manage/resume-backup.png)

* V nabídce **Zásady zálohování** vyberte zásadu a pak vyberte **Uložit**.

### <a name="upgrading-from-sap-hana-10-to-20"></a>Upgrade z SAP HANA 1.0 na 2.0

Naučte se pokračovat v zálohování databáze SAP HANA [po upgradu ze SAP HANA 1.0 na 2.0](backup-azure-sap-hana-database-troubleshoot.md#upgrading-from-sap-hana-10-to-20).

### <a name="upgrading-without-a-sid-change"></a>Inovace bez změny SID

Zjistěte, jak pokračovat v zálohování databáze SAP HANA, jehož [sid se po upgradu nezměnil](backup-azure-sap-hana-database-troubleshoot.md#upgrading-without-an-sid-change).

### <a name="unregister-an-sap-hana-instance"></a>Zrušení registrace instance SAP HANA

Zrušte registraci instance SAP HANA po zakázání ochrany, ale před odstraněním trezoru:

* Na řídicím panelu trezoru vyberte v části **Správa** **položku Zálohovací infrastruktura**.

   ![Vybrat infrastrukturu zálohování](./media/sap-hana-db-manage/backup-infrastructure.png)

* Vyberte **typ správy zálohování** jako **úlohu ve virtuálním počítači Azure**

   ![Vyberte typ správy zálohování jako úlohu ve virtuálním počítači Azure](./media/sap-hana-db-manage/backup-management-type.png)

* V **části Chráněné servery**vyberte instanci, kterou chcete zrušit. Chcete-li trezor odstranit, musíte zrušit registraci všech serverů/ instancí.

* Klepněte pravým tlačítkem myši na chráněnou instanci a vyberte **možnost Zrušit registraci**.

   ![Vybrat zrušit registraci](./media/sap-hana-db-manage/unregister.png)

### <a name="re-register-extension-on-the-sap-hana-server-vm"></a>Znovu zaregistrovat rozšíření na virtuálním počítači serveru SAP HANA

Někdy rozšíření zatížení na virtuálním počítači může získat vliv z toho či onoho důvodu. V takových případech všechny operace aktivované na virtuálním počítači začne selhat. Potom může být nutné znovu zaregistrovat rozšíření na virtuálním počítači. Operace opětovnéregistrace přeinstaluje rozšíření zálohování úloh y na virtuálním počítači, aby operace mohly pokračovat.

Tuto možnost použijte opatrně: při aktivaci na virtuálním počítači s rozšířením již v pořádku způsobí tato operace restartování rozšíření. To může způsobit selhání všech probíhajících úloh. Před spuštěním operace opětovné registrace zkontrolujte jeden nebo více [příznaků.](backup-azure-sap-hana-database-troubleshoot.md#re-registration-failures)

## <a name="next-steps"></a>Další kroky

* Zjistěte, jak [řešit běžné problémy při zálohování databází SAP HANA.](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)
