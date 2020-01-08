---
title: Monitorování Azure Automation runbooků s upozorněními na metriky
description: Tento článek vás provede monitorováním Azure Automation sad Runbook založených na metrikách.
services: automation
ms.date: 11/01/2018
ms.topic: article
ms.openlocfilehash: 9bd028157b33817898ef69f9e47cb8b5d9b8f381
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75367089"
---
# <a name="monitoring-runbooks-with-metric-alerts"></a>Monitorování runbooků s upozorněními na metriky

V tomto článku se dozvíte, jak vytvořit výstrahy na základě stavu dokončení runbooků.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k Azure na adrese https://portal.azure.com.

## <a name="create-alert"></a>Vytvoření upozornění

Výstrahy umožňují definovat podmínku, kterou chcete monitorovat, a akci, která se má provést při splnění této podmínky.

V Azure Portal přejděte do svého účtu Automation. V části **monitorování**vyberte **výstrahy** a klikněte na **+ nové pravidlo výstrahy**. Obor pro cíl je již definován pro váš účet Automation.

### <a name="configure-alert-criteria"></a>Konfigurace kritérií výstrahy

1. Klikněte na **+ Přidat kritéria**. Vyberte **metriky** pro **typ signálu**a v tabulce zvolte **Celkový počet úloh** .

2. Na stránce **Konfigurovat logiku signálu** je místo, kde můžete definovat logiku, která aktivuje výstrahu. V rámci historického grafu se zobrazí dvě dimenze, název a **stav** **Runbooku** . Dimenze jsou různé vlastnosti metriky, které lze použít k filtrování výsledků. V části **název Runbooku**vyberte sadu Runbook, na které chcete upozornit, nebo ponechte pole prázdné, aby se zobrazila výstraha pro všechny Runbooky. V rozevíracím seznamu **stav**vyberte stav, pro který chcete monitorovat. Název sady Runbook a hodnoty stavu, které se zobrazí v rozevíracím seznamu, jsou pouze pro úlohy, které byly spuštěny v minulém týdnu.

   Pokud chcete upozornit na stav nebo sadu Runbook, které nejsou zobrazeny v rozevíracím seznamu, klikněte na **\+** vedle dimenze. Tato akce otevře dialogové okno, které vám umožní zadat vlastní hodnotu, která se v poslední době negenerovala pro tuto dimenzi. Pokud zadáte hodnotu, která pro vlastnost neexistuje, vaše výstraha se neaktivuje.

   > [!NOTE]
   > Pokud nepoužijete název pro dimenzi **RunbookName** , pokud existují Runbooky, které splňují kritéria stavu, což zahrnuje skryté systémové Runbooky, zobrazí se upozornění.

3. V části **logika výstrahy**Definujte podmínku a prahovou hodnotu pro výstrahu. V části se zobrazí náhled definované podmínky.

4. Pod položkou **vyhodnoceno na základě**vyberte časové rozpětí pro dotaz a jak často chcete dotaz spuštěn. Pokud například zvolíte za **posledních 5 minut** za **období** a **každou 1 minutu** pro **frekvenci**, výstraha vyhledá počet sad Runbook, které splnily kritéria za posledních 5 minut. Tento dotaz se spouští každou minutu a jakmile se kritéria výstrahy, která jste definovali, už v okně 5 minut nenaleznou, výstraha se vyřeší sám. Jakmile budete hotovi, klikněte na **Hotovo**.

   ![Vyberte prostředek pro výstrahu.](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-alert-details"></a>Definice podrobností o upozornění

1. V části **2. Zadejte podrobnosti výstrahy**, zadejte popisný název a popis výstrahy. Nastavte **závažnost** tak, aby odpovídala vaší podmínce upozornění. Existuje pět závažností od 0 do 5. Výstrahy se považují za nezávisle na závažnosti, ale můžete se shodovat se závažností, aby odpovídala vaší obchodní logice.

1. V dolní části oddílu je tlačítko, které umožňuje po dokončení Povolit pravidlo. Ve výchozím nastavení jsou pravidla povolena při vytváření. Pokud vyberete Ne, můžete vytvořit upozornění a vytvoří se v **zakázaném** stavu. Na stránce **pravidla** v Azure monitor můžete vybrat ho a kliknutím na **Povolit** povolit upozornění, až budete připraveni.

### <a name="define-the-action-to-take"></a>Definujte akci, která se má provést.

1. V části **3. Definujte skupinu akcí**, klikněte na **+ Nová skupina akcí**. Skupina akcí je skupina akcí, které lze použít v rámci více než jedné výstrahy. Ty můžou zahrnovat, ale nejsou omezené, e-mailová oznámení, Runbooky, Webhooky a spousta dalších. Další informace o skupinách akcí najdete v tématu [Vytváření a správa skupin akcí](../azure-monitor/platform/action-groups.md).

1. Do pole **Název skupiny akcí** zadejte popisný a krátký název. Krátký název se použije místo úplného názvu skupiny akcí při odesílání oznámení pomocí této skupiny.

1. V části **Akce** v části **typ akce**vyberte **e-mail/SMS/Push/Voice**.

1. Na stránce **E-mailové/SMS/nabízené/hlasové oznámení** zadejte název. Zaškrtněte políčko **E-mail** a zadejte platnou e-mailovou adresu, která se má použít.

   ![Konfigurace e-mailové skupiny akcí](./media/automation-alert-activity-log/add-action-group.png)

1. Kliknutím na **OK** zavřete stránku **E-mailové/SMS/nabízené/hlasové oznámení** a kliknutím na **OK** zavřete stránku **Přidat skupinu akcí**. Název zadaný na této stránce je uložen jako **název akce**.

1. Jakmile budete hotovi, klikněte na **Uložit**. Tato akce vytvoří pravidlo, které vás upozorní, když se sada Runbook dokončí s určitým stavem.

> [!NOTE]
> Když přidáváte e-mailovou adresu do skupiny akcí, pošle se e-mail s oznámením, že se adresa přidala do skupiny akcí.

## <a name="notification"></a>Oznámení

Když je splněno kritérium výstrahy, Skupina akcí spustí definovanou akci. V tomto článku je třeba poslat e-mail. Následující obrázek je příkladem e-mailu, který obdržíte po aktivaci výstrahy:

![E-mailové upozornění](./media/automation-alert-activity-log/alert-email.png)

Jakmile metrika přestane být mimo stanovenou prahové hodnoty, výstraha se deaktivuje a skupina akcí spustí definovanou akci. Pokud je vybraný typ akce e-mail, pošle se e-mail s řešením oznamující, že je vyřešený.

## <a name="next-steps"></a>Další kroky

V následujícím článku se dozvíte další informace o tom, jak můžete výstrahy integrovat do svého účtu Automation.

> [!div class="nextstepaction"]
> [Aktivace sady Runbook Azure Automation pomocí výstrahy](automation-create-alert-triggered-runbook.md)
