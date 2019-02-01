---
title: Monitorovat výstrahy zálohování pro virtuální počítače Azure
description: Monitorování událostí a upozornění z úlohy zálohování virtuálních počítačů Azure. Odeslání e-mailu na základě výstrah.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: raynew
ms.openlocfilehash: 886c41f669d0b0363f4484b24ba3b9975904d9f7
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55492211"
---
# <a name="monitor-alerts-for-azure-virtual-machine-backups"></a>Správa výstrah pro virtuální počítače Azure

Výstrahy jsou odpovědí ze služby, že má prahová hodnota události dosažená nebo překračovat. Znalost, při může být důležité nízkých nákladech obchodní problémy start. Výstrahy většinou nedochází podle plánu, a to je užitečné vědět, co nejdříve po výstrahy zobrazují tehdy. Například pokud se nezdaří úlohy zálohování nebo obnovení zobrazení výstrahy do pěti minut od selhání. V řídicím panelu trezoru na dlaždici výstrahy zálohování zobrazuje úrovni upozornění a kritické události. V nastavení výstrah zálohování můžete zobrazit všechny události. Ale co můžete dělat, když výstraha nastane, pokud pracujete na samostatný problém? Pokud si nejste jisti, když se stane, výstrahy, může to být dílčí potíže nebo ho by mohly ohrozit data. Pokud chcete mít jistotu, že správných lidí si vědomi výstrahy – když k ní dojde, nakonfigurujte službu pro odeslání oznámení o výstrahách e-mailem. Podrobnosti o nastavení e-mailových oznámení najdete v tématu [konfigurace oznámení](backup-azure-monitor-vms.md#configure-notifications).

## <a name="how-do-i-find-information-about-the-alerts"></a>Jak najdu informace o výstrahách

Chcete-li zobrazit informace o události, která vyvolala upozornění, je nutné otevřít část pojednávající o výstrahách zálohování. Existují dva způsoby, jak otevřete část pojednávající o výstrahách zálohování: buď z výstrahy zálohování dlaždici na řídicím panelu trezoru, nebo z část výstrahy a události.

Otevřete okno zálohování výstrahy z dlaždice výstrahy zálohování:

* Na **výstrahy zálohování** dlaždici na řídicím panelu trezoru, klikněte na tlačítko **kritický** nebo **upozornění** Chcete-li zobrazit provozní události pro tuto úroveň závažnosti.

    ![Dlaždice výstrahy zálohování](./media/backup-azure-monitor-vms/backup-alerts-tile.png)

Otevře se okno výstrahy zálohování v části Výstrahy a události:

1. Z řídicího panelu trezoru klikněte na tlačítko **všechna nastavení**. ![Tlačítko všechna nastavení](./media/backup-azure-monitor-vms/all-settings-button.png)
2. Na **nastavení** okna, klikněte na tlačítko **výstrahy a události**. ![Tlačítko výstrahy a události](./media/backup-azure-monitor-vms/alerts-and-events-button.png)
3. Na **výstrahy a události** okna, klikněte na tlačítko **výstrahy zálohování**. ![Zálohování tlačítko výstrahy](./media/backup-azure-monitor-vms/backup-alerts.png)

    **Výstrahy zálohování** oddílu se otevře a zobrazí filtrovaná výstrahy.

    ![Dlaždice výstrahy zálohování](./media/backup-azure-monitor-vms/backup-alerts-critical.png)
4. Chcete-li zobrazit podrobné informace o určité výstraze, ze seznamu událostí, klikněte na výstrahu a otevřete její **podrobnosti** oddílu.

    ![Podrobnosti události](./media/backup-azure-monitor-vms/audit-logs-event-detail.png)

    Přizpůsobit atributy zobrazí v seznamu, přečtěte si článek [zobrazit další událost atributy](backup-azure-monitor-vms.md#view-additional-event-attributes)

## <a name="configure-notifications"></a>Konfigurace oznámení

 Můžete nakonfigurovat službu pro odeslání e-mailová oznámení pro výstrahy, ke kterým došlo za poslední hodinu, nebo pokud dojde k určité typy událostí.

Nastavení e-mailová oznámení pro výstrahy

1. V nabídce výstrahy zálohování klikněte na tlačítko **konfigurace oznámení**

    ![Nabídka výstrahy zálohování](./media/backup-azure-monitor-vms/backup-alerts-menu.png)

    Otevře se v části Konfigurace oznámení.

    ![Nakonfigurovat okno oznámení](./media/backup-azure-monitor-vms/configure-notifications.png)
2. V části Konfigurace oznámení pro e-mailová oznámení, klikněte na **na**.

    Příjemci a závažnost dialogová okna mít hvězdičky vedle příslušné položky, protože tyto informace je povinný. Zadejte aspoň jednu e-mailovou adresu a vyberte aspoň jednu závažnost.
3. V **příjemci (E-mail)** dialogovém okně zadejte e-mailové adresy, pro který oznámení vůbec dostat. Použijte formát: username@domainname.com. Jednotlivé e-mailové adresy oddělujte středníkem (;).
4. V **upozornění** oblasti, zvolte **výstraze** k odesílání oznámení, pokud zadaný výstrahy, nebo **hodinový přehled** odeslat souhrn za poslední hodinu.
5. V **závažnost** dialogového okna, vyberte jednu nebo více úrovní, které chcete aktivovat e-mailové oznámení.
6. Klikněte na **Uložit**.

### <a name="what-alert-types-are-available-for-azure-iaas-vm-backup"></a>Jaké typy výstrah jsou k dispozici pro zálohování virtuálních počítačů Azure IaaS

   | Úroveň výstrahy | Zasílání upozornění |
   | --- | --- |
   | Kritická | selhání zálohování, obnovení selhalo. |
   | Upozornění | pro zálohování úloh bylo úspěšně dokončeno s upozorněními. (Příklad: některé zapisovače lokality nemohlo vytvřit snímek) |
   | Informační | v současné době nejsou k dispozici pro zálohování virtuálních počítačů Azure bez informační výstrahy |

### <a name="situations-where-email-isnt-sent-even-if-notifications-are-configured"></a>E-mail neodešle, i v případě, že jsou oznámení nakonfigurovaná situacích

Existují situace, ve kterém se neodešle výstrahu, i když byla správně nakonfigurovaná oznámení. V následujících situacích, e-mailu nedocházelo k jejich odesílání do vyhnutí se zbytečnému vytváření výstrah:

* Pokud jsou oznámení nakonfigurovaná na hodinový přehled a výstraha se vyvolá a vyřeší během hodiny.
* Úloha se zruší.
* Úloha zálohování se aktivuje a pak se nezdaří a probíhá jiná úloha zálohování.
* Spustí se naplánovaná úloha zálohování pro virtuální počítač s podporou Resource Manageru, ale virtuální počítač už neexistuje.

## <a name="using-activity-logs-to-get-notifications-for-successful-backups"></a>Dostávat oznámení o úspěšném provedení zálohy pomocí protokolů aktivit

> [!NOTE]
> Jsme přesunuli na nový model čerpání protokoly aktivit z Azure Backup na trezory služby Recovery Services. Bohužel to má vliv generování protokolů aktivit v suverénních Cloudech Azure. Pokud uživatelé suverénní Cloud Azure vytvořili/nakonfigurované všechny výstrahy na základě protokolů aktivit prostřednictvím služby Azure Monitor, jak je uvedeno tady, nebude spuštěna. V takovém případě by doporučujeme takové uživatelům používat nastavení diagnostiky a pracovního prostoru LA nebo [reporting řešení Power BI](backup-azure-configure-reports.md) zobrazíte příslušné informace. Ve všech veřejných oblastech Azure, pokud uživatel je shromažďování protokolů aktivit služby obnovení do pracovního prostoru analýzy protokolů jak už bylo zmíněno [tady](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity), neobjevila by se také tyto protokoly.

Pokud chcete, abyste dostávali oznámení po zálohování jsou úspěšné, můžete upozornění založená na [protokoly aktivit](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) trezoru.

### <a name="login-into-azure-portal"></a>Přihlaste se k webu Azure portal

Přihlaste se k webu Azure portal a přejít k příslušné trezor služeb zotavení Azure a klikněte na část "Protokol aktivit" ve vlastnostech.

### <a name="identify-appropriate-log"></a>Identifikujte příslušný protokol

Použijte filtry, které je znázorněno na následujícím obrázku ověřit, jestli jste obdrželi, protokoly aktivit pro úspěšné zálohování. Chcete-li zobrazit záznamy časový interval odpovídajícím způsobem měnit.

![Protokoly aktivit](./media/backup-azure-monitor-vms/activity-logs-identify.png)

Klikněte na tlačítko "JSON" segment, který má získat další podrobnosti a zobrazit tak kopie vložení ho do textového editoru. Zobrazit podrobnosti trezoru a položka, která aktivuje protokol aktivit například zálohované položky.

Pak klikněte na tlačítko "Přidat upozornění protokolu aktivit" Generovat výstrahy pro tyto protokoly.

### <a name="add-activity-log-alert"></a>Přidat upozornění protokolu aktivit

Klepnutím na tlačítko "Přidat upozornění protokolu aktivit" se zobrazí na obrazovce vidíte níže

![Upozornění protokolu aktivit](./media/backup-azure-monitor-vms/activity-logs-alerts-successful.png) předplatného a skupiny prostředků se používají k ukládání upozornění. Kritéria budou předem vyplněné. Zajistěte, aby že všechny hodnoty jsou relevantní pro váš požadavek.

Pro úspěšné zálohy na úrovni označen jako "Informační" a stav jako "ÚSPĚCH".

Pokud vyberete "prostředek" výše, bude výstraha vygeneruje, když protokoly aktivit jsou zaznamenány pro daný prostředek nebo trezoru. Pokud chcete pravidlo použít ke všem trezorům, ponechte "prostředek" prázdný.

### <a name="define-action-on-alert-firing"></a>Definujte akce v jeho spuštění výstrahy

Pomocí skupiny"akce" definujete akce, při generování výstrahy. Kliknutím na "Typ akce" Další informace o dostupných akcí takových e-mailu/SMS/integrace s ITSM atd.

![Skupina akcí protokolu aktivit](./media/backup-azure-monitor-vms/activity-logs-alerts-action-group.png)

Po kliknutí na OK, vygeneruje se upozornění protokolu aktivit a následné aktivity protokoly pro úspěšné zálohování se aktivuje akci podle skupiny akcí.

### <a name="limitations-on-alerts"></a>Omezení týkající se výstrah

Výstrahy založené na událostech se vztahují následující omezení:

1. Výstrahy se zobrazí u všech virtuálních počítačů v trezoru služby Recovery Services. Nelze nastavit upozornění pro celou dílčí sadu virtuálních počítačů v trezoru služby Recovery Services.
2. Výstrahy se odesílají z "alerts-noreply@mail.windowsazure.com". Momentálně nelze upravit odesílatelem e-mailu.

## <a name="next-steps"></a>Další postup

Informace o opětovné vytvoření virtuálního počítače z bodu obnovení, projděte si [obnovení virtuálních počítačů Azure](backup-azure-arm-restore-vms.md).

Pokud potřebujete informace o ochraně virtuálních počítačů, přečtěte si [stručně a přehledně: Zálohování virtuálních počítačů do trezoru služby Recovery Services](backup-azure-vms-first-look-arm.md).

Další informace o úlohách správy pro zálohy virtuálních počítačů v následujícím článku [záloh virtuálních počítačů Azure spravovat](backup-azure-manage-vms.md).
