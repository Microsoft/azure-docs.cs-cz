---
title: Sledování runbooků Azure Automation se upozornění na metriku
description: Tento článek vás provede s runbooky Azure Automation na základě metrik monitorování
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 11/01/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 65de18445f114f468dd42c5a7e7128dd2f63d44c
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50959819"
---
# <a name="monitoring-runbooks-with-metric-alerts"></a>Monitorování sady runbook pomocí upozornění na metriku

V tomto článku se dozvíte, jak vytvářet upozornění na základě stavu dokončení sad runbook.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k Azure na adrese https://portal.azure.com.

## <a name="create-alert"></a>Vytvoření upozornění

Výstrahy umožňují definovat podmínky pro monitorování a akce při splnění této podmínky.

Na webu Azure Portal přejděte na svůj účet Automation. V části **monitorování**vyberte **výstrahy** a klikněte na tlačítko **+ nové pravidlo upozornění**. Do vašeho účtu Automation je již definován obor pro cíl.

### <a name="configure-alert-criteria"></a>Konfigurovat kritéria výstrahy

1. Klikněte na tlačítko **+ přidat kritéria**. Vyberte **metriky** pro **signalizuje, že typ**a zvolte **celkový počet úloh** z tabulky.

2. **Konfigurovat logiku signálů** stránka je tady můžete definovat logiku, která aktivuje výstrahu. V části Historický graf budou vám nabídnuty dvě dimenze **název sady Runbook** a **stav**. Dimenze jsou různé vlastnosti pro metriku, která slouží k filtrování výsledků. Pro **název sady Runbook**, vyberte sadu runbook, který chcete upozornit na nebo nechte prázdné, upozornění na všechny sady runbook. Pro **stav**, vyberte z rozevíracího seznamu chcete monitorovat stav. Sada runbook název a stav hodnoty, které se zobrazí v rozevírací nabídce jsou určeny pouze pro úlohy, které jste spustili v minulém týdnu.

   Pokud chcete upozornění na stav nebo sadu runbook, který není uveden v rozevíracím seznamu, klikněte na tlačítko **\+** vedle dimenze. Tato akce otevře dialogové okno, které můžete zadat vlastní hodnoty, který nebyl vygenerován pro tuto dimenzi nedávno. Pokud zadáte hodnotu pro vlastnost neexistuje upozornění nebudou aktivovány.

3. V části **upozornění logiky**, definujte podmínku a prahovou hodnotu pro upozornění. Vaše podmínka definované ve verzi preview se zobrazí pod.

4. V části **Evaluated na základě**, vyberte časový rozsah pro dotaz a jak často chcete tento dotaz byl spuštěn. Například, pokud se rozhodnete **za posledních 5 minut** pro **období** a **každou 1 minutu** pro **frekvence**, výstrahu vypadá po dobu sad runbook, která splní kritéria za posledních 5 minut. Tento dotaz je spouštět každou minutu a po kritéria výstrahy, které jste definovali se již nenachází v okně 5 minut, výstraha vyřeší sám. Jakmile budete hotovi, klikněte na **Hotovo**.

   ![Vyberte prostředek pro výstrahy](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-alert-details"></a>Definice podrobností o upozornění

1. V části **2. Definujte podrobnosti upozornění** zadejte popisný název a popis upozornění. Nastavte **závažnost** tak, aby odpovídaly podmínku upozornění. Existuje pět závažnosti od 0 do 5. Výstrahy jsou považovány stejné bez ohledu na závažnost, můžete porovnat závažnost tak, aby odpovídaly obchodní logiku.

1. V dolní části je tlačítko, které umožňuje povolit pravidlo po dokončení. Ve výchozím nastavení jsou povolená pravidla při vytvoření. Pokud vyberete možnost Ne, můžete vytvořit výstrahy a vytváření **zakázané** stavu. Z **pravidla** stránky ve službě Azure Monitor, vyberte ho a klikněte na tlačítko **povolit** povolit upozornění, jakmile budete připraveni.

### <a name="define-the-action-to-take"></a>Definovat akce má být provedena

1. V části **3. Definujte skupinu akcí** klikněte na **+ Nová skupina akcí**. Skupiny akcí je skupina akcí, které můžete použít napříč více než jedna výstraha. Můžete zahrnout, ale nejsou omezené na e-mailová oznámení, sady runbook, webhooky a mnoho dalších. Další informace o skupinách akcí najdete v tématu [Vytváření a správa skupin akcí](../monitoring-and-diagnostics/monitoring-action-groups.md).

1. Do pole **Název skupiny akcí** zadejte popisný a krátký název. Krátký název se použije místo úplného názvu skupiny akcí při odesílání oznámení pomocí této skupiny.

1. V **akce** části **typ akce**vyberte **e-mailu/SMS nebo nabízená/hlasové**.

1. Na stránce **E-mailové/SMS/nabízené/hlasové oznámení** zadejte název. Zaškrtněte políčko **E-mail** a zadejte platnou e-mailovou adresu, která se má použít.

   ![Konfigurace e-mailové skupiny akcí](./media/automation-alert-activity-log/add-action-group.png)

1. Kliknutím na **OK** zavřete stránku **E-mailové/SMS/nabízené/hlasové oznámení** a kliknutím na **OK** zavřete stránku **Přidat skupinu akcí**. Název zadaný na této stránce je uložen jako **název akce**.

1. Jakmile budete hotovi, klikněte na **Uložit**. Tato akce vytvoří pravidlo, které zobrazuje výstrahy, pokud sada runbook byla dokončena s určitým stavem.

> [!NOTE]
> Při přidávání e-mailovou adresu do skupiny akcí, je odeslán oznamovací e-mail s oznámením, že adresa byla přidána do skupiny akcí.

## <a name="notification"></a>Oznámení

Při splnění kritérií výstrah skupiny akcí spuštěna akce definovaná. V příkladu v tomto článku se odešle e-mail. Na následujícím obrázku je příklad, který se zobrazí po aktivaci tohoto upozornění e-mailu:

![E-mailové upozornění](./media/automation-alert-activity-log/alert-email.png)

Po metriky již není mimo prahovou hodnotu definované, deaktivovat výstrahu a skupinu akcí spuštěna akce definovaná. Pokud je vybraný typ akce e-mailu, se odešle e-mail s řešení s informacemi o tom, že co byl vyřešen.

## <a name="next-steps"></a>Další postup

Pokračujte v následujícím článku se dozvíte o jiných způsobech, alertings můžete integrovat do vašeho účtu Automation.

> [!div class="nextstepaction"]
> [Pomocí upozornění můžete aktivovat runbooku Azure Automation](automation-create-alert-triggered-runbook.md)
