---
title: Výstrahy sledování zálohy pro virtuální počítače Azure
description: Monitorování události a výstrahy z úlohy zálohování virtuálního počítače Azure. Odeslání e-mailu na základě výstrah.
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: markgal
ms.openlocfilehash: 3783014738ec4e8f185531773b1259dc63e7f49f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606303"
---
# <a name="monitor-alerts-for-azure-virtual-machine-backups"></a>Správa výstrah pro virtuální počítače Azure
Výstrahy jsou odpovědi ze služby, aby byla splněny nebo překročení prahová hodnota události. Zároveň budete vědět, když může být kritické nízkými náklady obchodní problémy spuštění. Výstrahy obvykle nedojde k podle plánu, a proto je užitečné vědět, co nejdříve po generována výstraha. Například pokud se nezdaří úlohy zálohování nebo obnovení, zobrazení výstrahy do pěti minut selhání. Na řídicím panelu trezoru na dlaždici zálohování výstrahy zobrazuje kritická a úroveň pro upozornění události. V nastavení zálohování výstrah můžete zobrazit všechny události. Ale co dělat v případě výstrahu při práci na samostatné problém? Pokud si nejste jisti, když se stane, výstrahy, může to být méně závažné potíže, nebo ji mohl ohrozit zabezpečení dat. Pokud chcete mít jistotu, že oprávnění uživatelé by se měl dozvědět výstrahy – když dojde, nakonfigurujte službu pro odeslání oznámení o výstrahách e-mailem. Podrobnosti o nastavení e-mailová oznámení najdete v tématu [konfigurace oznámení](backup-azure-monitor-vms.md#configure-notifications).

## <a name="how-do-i-find-information-about-the-alerts"></a>Jak se najít informace o výstrahách?
Chcete-li zobrazit informace o události, která způsobila výstrahu, je nutné otevřít části výstrahy zálohování. Existují dva způsoby, jak otevřete část zálohování výstrahy: některý z výstrah zálohování dlaždici na řídicím panelu trezoru, nebo z části Výstrahy a události.

Otevřete okno zálohování výstrahy z dlaždice výstrahy zálohování:

* Na **zálohování výstrahy** dlaždici na řídicím panelu trezoru, klikněte na tlačítko **kritický** nebo **upozornění** Chcete-li zobrazit provozní události pro tuto úroveň závažnosti.

    ![Dlaždice výstrahy zálohy](./media/backup-azure-monitor-vms/backup-alerts-tile.png)

Otevřete okno zálohování výstrahy z části Výstrahy a události:

1. Na řídicím panelu trezoru, klikněte na tlačítko **všechna nastavení**. ![Tlačítko všechna nastavení](./media/backup-azure-monitor-vms/all-settings-button.png)
2. Na **nastavení** okně klikněte na tlačítko **výstrahy a události**. ![Tlačítko výstrahy a události](./media/backup-azure-monitor-vms/alerts-and-events-button.png)
3. Na **výstrahy a události** okně klikněte na tlačítko **zálohování výstrahy**. ![Zálohování tlačítko výstrahy](./media/backup-azure-monitor-vms/backup-alerts.png)

    **Zálohování výstrahy** části otevře a zobrazí filtrované výstrahy.

    ![Dlaždice výstrahy zálohy](./media/backup-azure-monitor-vms/backup-alerts-critical.png)
4. Chcete-li zobrazit podrobné informace o určité výstraze, ze seznamu události, klikněte na výstrahu otevřete jeho **podrobnosti** části.

    ![Podrobnosti události](./media/backup-azure-monitor-vms/audit-logs-event-detail.png)

    Přizpůsobení atributů zobrazit v seznamu, najdete v části [zobrazit další událost atributy](backup-azure-monitor-vms.md#view-additional-event-attributes)

## <a name="configure-notifications"></a>Konfigurace oznámení
 Můžete nakonfigurovat službu pro odeslání e-mailová oznámení pro výstrahy, které přes poslední hodinu, nebo pokud dojde k určité typy událostí došlo k chybě.

Nastavení e-mailová oznámení pro výstrahy

1. V nabídce výstrahy zálohování, klikněte na tlačítko **konfigurace oznámení**

    ![Zálohování nabídky výstrahy](./media/backup-azure-monitor-vms/backup-alerts-menu.png)

    Otevře se v části Konfigurace oznámení.

    ![Konfigurace oznámení okno](./media/backup-azure-monitor-vms/configure-notifications.png)
2. V části Konfigurace oznámení pro e-mailová oznámení, klikněte na tlačítko **na**.

    Příjemci a závažnost dialogová okna mají hvězdu vedle jejich, protože tyto informace je vyžadován. Zadejte aspoň jednu e-mailovou adresu a vyberte alespoň jeden závažnosti.
3. V **příjemce (e-mailu)** dialogové okno, zadejte e-mailové adresy, pro který dostávat oznámení. Použijte formát: username@domainname.com. Jednotlivé e-mailové adresy oddělte středníkem (;).
4. V **oznámení** oblasti, zvolte **za výstraha** k odeslání oznámení, když dojde k zadané výstrahy, nebo **hodinové Digest** k odeslání souhrn za poslední hodinu.
5. V **závažnost** dialogovém okně, vyberte jeden nebo více úrovní, které chcete aktivovat e-mailové oznámení.
6. Klikněte na **Uložit**.

   ### <a name="what-alert-types-are-available-for-azure-iaas-vm-backup"></a>Jaké typy výstrah jsou k dispozici pro zálohování virtuálních počítačů Azure IaaS?
   | Úroveň výstrahy | Zasílání upozornění |
   | --- | --- |
   | Důležité | selhání zálohování, obnovení selhání |
   | Upozornění | pro úlohy zálohování proběhla úspěšně. upozornění (například: některé zapisovače se nezdařila při vytváření snímku) |
   | Informační | v současné době jsou k dispozici pro zálohování virtuálních počítačů Azure informační výstrahu. |

### <a name="are-there-situations-where-email-isnt-sent-even-if-notifications-are-configured"></a>Dochází k situacím, že se e-mail neodešle, i když jsou oznámení nakonfigurovaná?
Existují situacích, kde se neposílají výstrahu, i když oznámení správně nakonfigurovaný. V následujících situacích e-mailu s oznámení neodešlou předejdete výstrahy nepůsobily:

* Pokud oznámení jsou nakonfigurovány pro hodinové Digest, a je výstraha vyvolána a vyřešit v rámci hodiny.
* Úloha je zrušena.
* Úloha zálohování se aktivuje a pak se nezdaří a probíhá další úloha zálohování.
* Spustí naplánované úlohy zálohování pro virtuální počítač povolena Resource Manager, ale virtuální počítač už existuje.

## <a name="using-activity-logs-to-get-notifications-for-successful-backups"></a>Pomocí protokolů z aktivity dostávat oznámení po úspěšném provedení zálohy

Pokud chcete po zálohování jsou úspěšné upozorněn, můžete výstrahy založený na [protokoly aktivity](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) trezoru.

### <a name="login-into-azure-portal"></a>Přihlásit se k portálu Azure
Přihlášení k portálu Azure a přejít k příslušné trezor služeb zotavení Azure a klikněte na část "Protokol aktivit" ve vlastnostech.

### <a name="identify-appropriate-log"></a>Identifikovat příslušný protokol

Použijte filtry znázorněno na následujícím obrázku chcete ověřit, zda jsou přijímá protokoly aktivity pro úspěšné zálohy. Změňte časový interval odpovídajícím způsobem zobrazit záznamy.

![Protokoly aktivit](./media/backup-azure-monitor-vms/activity-logs-identify.png)

Klikněte na tlačítko "JSON" segment, který má získat další podrobnosti a zobrazit podle kopie vložení ho do textového editoru. Mělo by se zobrazit podrobnosti trezoru a položku, která spustí protokol aktivit tedy zálohování položek.

Klikněte na tlačítko "Přidat aktivitu protokolu výstraha" generování výstrah pro všechny tyto protokoly.

### <a name="add-activity-log-alert"></a>Přidání oznámení protokolu aktivit

Kliknutím na tlačítko "Přidat aktivitu protokolu upozornění" vám ukáže obrazovky jak je uvedeno níže

![Upozornění protokolu aktivit](./media/backup-azure-monitor-vms/activity-logs-alerts-successful.png)
    
Předplatné a skupina prostředků se používají k ukládání upozornění. Kritéria předvyplní se. Zkontrolujte, zda že všechny hodnoty jsou relevantní pro váš požadavek.

Pro úspěšné zálohy na úrovni je označený jako "Informační" a stav jako "Succeeded".

Pokud zvolíte možnost "zdroj" výše, výstraha vygeneruje, když aktivita protokolů pro tento prostředek nebo trezoru. Pokud chcete pravidlo se vztahuje na všechny trezory, ponechte "prostředek" byly prázdné.

### <a name="define-action-on-alert-firing"></a>Definovat akci na výstrahy pálení

Pomocí skupiny"akce" k definování akce při generování výstrahy. Kliknutím na "Typ akce" Další informace o dostupné akce takových e-mailu nebo SMS nebo integrace s ITSM atd.

![Skupiny akci protokolu aktivit](./media/backup-azure-monitor-vms/activity-logs-alerts-action-group.png)


Po kliknutí na tlačítko OK, bude vygenerována výstraha protokolu aktivity a protokoly následné aktivity, které jsou zaznamenány pro úspěšné zálohy se aktivují akce, jak je definované ve skupině Akce.

### <a name="limitations-on-alerts"></a>Omezení výstrahy
Výstrahy na základě událostí se vztahují následující omezení:

1. Výstrahy se spouštějí na všechny virtuální počítače v trezoru služeb zotavení. Nelze nastavit upozornění pro podmnožiny virtuální počítače v trezoru služeb zotavení.
2. Výstrahy jsou odesílány z "alerts-noreply@mail.windowsazure.com". Momentálně nelze upravit odesílatelem e-mailu.

## <a name="next-steps"></a>Další postup
Informace o opětovné vytvoření virtuálního počítače z bodu obnovení, podívejte se na [obnovení virtuálních počítačů Azure](backup-azure-arm-restore-vms.md).

Pokud potřebujete informace o ochraně virtuálních počítačů, přečtěte si [první pohled: zálohování virtuálních počítačů do trezoru služeb zotavení](backup-azure-vms-first-look-arm.md). 

Další informace o úlohách správy pro zálohování virtuálních počítačů v článku, [záloh virtuálních počítačů Azure spravovat](backup-azure-manage-vms.md).
