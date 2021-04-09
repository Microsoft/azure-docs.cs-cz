---
title: Monitorování Azure Automation runbooků s upozorněními na metriky
description: Tento článek popisuje, jak nastavit výstrahu metriky na základě stavu dokončení sady Runbook.
services: automation
ms.date: 08/10/2020
ms.topic: article
ms.openlocfilehash: 3a52824a7030d78647d8a664819f439d92d7296d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100593943"
---
# <a name="monitor-runbooks-with-metric-alerts"></a>Monitorování runbooků s upozorněními metrik

V tomto článku se dozvíte, jak vytvořit [výstrahu metriky](../azure-monitor/alerts/alerts-metric-overview.md) na základě stavu dokončování sady Runbook.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k portálu [Azure Portal](https://portal.azure.com).

## <a name="create-alert"></a>Vytvoření upozornění

Výstrahy umožňují definovat podmínku, kterou chcete monitorovat, a akci, která se má provést při splnění této podmínky.

1. Spusťte službu Azure Automation v Azure Portal tak, že kliknete na **všechny služby** a pak vyhledáte a vyberete **účty Automation**.

2. V seznamu účtů Automation vyberte účet, pro který chcete vytvořit výstrahu. 

3. V části **monitorování** vyberte **výstrahy** a pak vyberte **+ nové pravidlo výstrahy**. Obor pro cíl je již definován a přidružen k vašemu účtu Automation.

### <a name="configure-alert-criteria"></a>Konfigurace kritérií výstrahy

1. Klikněte na **vybrat podmínku**. Vyberte **metriky** pro **typ signálu** a v seznamu zvolte **Celkový počet úloh** .

2. Na stránce **Konfigurovat logiku signálu** je místo, kde můžete definovat logiku, která aktivuje výstrahu. V rámci historického grafu se zobrazí dvě dimenze, název a **stav** **Runbooku** . Dimenze jsou různé vlastnosti metriky, které lze použít k filtrování výsledků. V části **název Runbooku** vyberte sadu Runbook, na které chcete upozornit, nebo ponechte pole prázdné, aby se zobrazila výstraha pro všechny Runbooky. V rozevíracím seznamu **stav** vyberte stav, pro který chcete monitorovat. Název sady Runbook a hodnoty stavu, které se zobrazí v rozevíracím seznamu, jsou pouze pro úlohy, které byly spuštěny v minulém týdnu.

   Pokud chcete upozornit na stav nebo sadu Runbook, které nejsou zobrazeny v rozevíracím seznamu, klikněte na možnost **Přidat vlastní hodnotu** vedle dimenze. Tato akce otevře dialogové okno, které vám umožní zadat vlastní hodnotu, která se v poslední době negenerovala pro tuto dimenzi. Pokud zadáte hodnotu, která pro vlastnost neexistuje, vaše výstraha se neaktivuje.

   > [!NOTE]
   > Pokud neurčíte název pro dimenzi **název Runbooku** , pokud existují nějaké Runbooky, které splňují kritéria stavu, což zahrnuje skryté systémové Runbooky, zobrazí se upozornění.

    Například pro upozornění, když sada Runbook vrátí stav _selhání_ , kromě určení názvu Runbooku **se nepovedlo** zadat název sady Runbook pro dimenzi **stav** přidat vlastní hodnotu dimenze.

    :::image type="content" source="./media/automation-alert-metric/specify-dimension-custom-value.png" alt-text="Zadat vlastní hodnotu dimenze" border="false":::

3. V části **logika výstrahy** Definujte podmínku a prahovou hodnotu pro výstrahu. V části se zobrazí náhled definované podmínky.

4. Pod položkou **vyhodnoceno na základě** vyberte časové rozpětí pro dotaz a jak často se má dotaz spouštět. Pokud například zvolíte **dobu** v intervalu **5 minut** za období a **každou 1 minutu** pro **frekvenci**, výstraha vyhledá počet sad Runbook, které splnily kritéria za posledních 5 minut. Tento dotaz se spouští každou minutu a jakmile se kritéria výstrahy, která jste definovali, už v okně 5 minut nenaleznou, výstraha se vyřeší sám. Po dokončení klikněte na **Hotovo**.

   ![Vyberte prostředek pro výstrahu.](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-the-action-to-take"></a>Definujte akci, která se má provést.

1. V části **Skupina akcí** vyberte **zadat skupinu akcí**. Skupina akcí je skupina akcí, které lze použít v rámci více než jedné výstrahy. Ty můžou zahrnovat, ale nejsou omezené, e-mailová oznámení, Runbooky, Webhooky a spousta dalších. Další informace o skupinách akcí a postupu při jejich vytvoření, které odesílají e-mailové oznámení, najdete v tématu [Vytvoření a Správa skupin akcí](../azure-monitor/alerts/action-groups.md).

### <a name="define-alert-details"></a>Definice podrobností o upozornění

1. V části **Podrobnosti pravidla výstrahy** zadejte popisný název a popis výstrahy. Nastavte **závažnost** tak, aby odpovídala vaší podmínce upozornění. Existuje pět závažností od 0 do 5. Výstrahy se považují za nezávisle na závažnosti, ale můžete se shodovat se závažností, aby odpovídala vaší obchodní logice.

1. Ve výchozím nastavení jsou pravidla povolena při vytváření, pokud nevyberete možnost **ne** pro možnost **Povolit pravidlo upozornění při vytvoření**. Pro výstrahy vytvořené v zakázaném stavu je můžete v budoucnu povolit, až budete připraveni. Vyberte **vytvořit pravidlo upozornění** a uložte provedené změny.

## <a name="receive-notification"></a>Doručení oznámení

Když je splněno kritérium výstrahy, Skupina akcí spustí definovanou akci. V tomto článku je třeba poslat e-mail. Následující obrázek je příkladem e-mailu, který obdržíte po aktivaci výstrahy:

![E-mailové upozornění](./media/automation-alert-activity-log/alert-email.png)

Jakmile metrika přestane být mimo stanovenou prahové hodnoty, výstraha se deaktivuje a skupina akcí spustí definovanou akci. Pokud je vybraný typ akce e-mail, pošle se e-mail s řešením oznamující, že je vyřešený.

## <a name="next-steps"></a>Další kroky

* Další informace najdete v tématu [použití výstrahy ke spuštění sady runbook Azure Automation](automation-create-alert-triggered-runbook.md).
