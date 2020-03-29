---
title: Monitorování runbooků Azure Automation pomocí upozornění na metriky
description: Tento článek vás provede monitorováním runbooků Azure Automation založených na metrikách
services: automation
ms.date: 11/01/2018
ms.topic: article
ms.openlocfilehash: 9bd028157b33817898ef69f9e47cb8b5d9b8f381
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75367089"
---
# <a name="monitoring-runbooks-with-metric-alerts"></a>Monitorování sad runbooků pomocí upozornění na metriky

V tomto článku se dozvíte, jak vytvořit výstrahy na základě stavu dokončení runbooků.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k Azure na adrese https://portal.azure.com.

## <a name="create-alert"></a>Vytvoření upozornění

Výstrahy umožňují definovat podmínku, kterou chcete sledovat, a akci, která má být splněna při splnění této podmínky.

Na webu Azure Portal přejděte na svůj účet Automation. V části **Sledování**vyberte **Výstrahy** a klepněte na tlačítko **+ Nové pravidlo výstrah**. Obor pro cíl je již definován pro váš účet automatizace.

### <a name="configure-alert-criteria"></a>Konfigurace kritérií výstrah

1. Klepněte na tlačítko **+ Přidat kritéria**. Vyberte **metriky** pro **typ signálu**a z tabulky zvolte **Celkové počet úloh.**

2. Konfigurovat **logiku signálu** stránka je místo, kde definujete logiku, která aktivuje výstrahu. Pod historickým grafem jsou prezentovány dvě dimenze, Název a **Stav** **runbooku** . Dimenze jsou různé vlastnosti pro metriku, kterou lze použít k filtrování výsledků. V **části Název sady Runbook**vyberte runbook, na který chcete upozornit, nebo ponechte prázdné upozornění na všech runbookech. V **části Stav**vyberte stav v rozevíracím souboru, který chcete sledovat. Hodnoty názvu a stavu runbooku, které se zobrazují v rozevíracím seznamu, jsou pouze pro úlohy, které byly spuštěny v minulém týdnu.

   Pokud chcete upozornit na stav nebo runbook, který se nezobrazuje **\+** v rozevíracím seznamu, klikněte na další dimenzi. Tato akce otevře dialogové okno, které umožňuje zadat vlastní hodnotu, která v poslední době pro tuto dimenzi nevyzařovala. Pokud zadáte hodnotu, která pro vlastnost neexistuje, vaše upozornění se neaktivuje.

   > [!NOTE]
   > Pokud nepoužijete název dimenze **RunbookName,** pokud existují nějaké sady Runbook, které splňují kritéria stavu, která zahrnují skryté systémové sady Runbook, obdržíte výstrahu.

3. V části **Logika výstrahy**definujte podmínku a prahovou hodnotu výstrahy. Pod ním je zobrazen náhled definovaného stavu.

4. V části **Vyhodnocení na základě**vyberte časový klíč pro dotaz a jak často chcete, aby byl dotaz spuštěn. Pokud například zvolíte **Za posledních 5 minut** pro **období** a **Každou 1 minutu** pro **frekvenci**, výstraha vyhledá počet runbooků, které splnily vaše kritéria za posledních 5 minut. Tento dotaz se spouštějí každou minutu a jakmile se definovaná kritéria výstrahy již nenacházejí v 5minutovém okně, výstraha se vyřeší sama. Jakmile budete hotovi, klikněte na **Hotovo**.

   ![Výběr zdroje pro výstrahu](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-alert-details"></a>Definice podrobností o upozornění

1. Do **2. Definujte podrobnosti výstrahy**, přiřazujte výstrahě popisný název a popis. Nastavte **závažnost tak,** aby odpovídala stavu výstrahy. Existuje pět závažnosti v rozmezí od 0 do 5. S výstrahami se zachází stejně nezávisle na závažnosti, můžete porovnat závažnost tak, aby odpovídala vaší obchodní logice.

1. V dolní části oddílu je tlačítko, které umožňuje povolit pravidlo po dokončení. Ve výchozím nastavení jsou pravidla při vytváření povolena. Pokud vyberete ne, můžete vytvořit výstrahu a vytvoří se ve stavu **Zakázáno.** Na stránce **Pravidla** ve službě Azure Monitor ji můžete vybrat a kliknutím na **Povolit,** abyste výstrahu povolili, až budete připraveni.

### <a name="define-the-action-to-take"></a>Definování akce, která má být

1. Do **3. Definujte skupinu akcí**, klepněte na tlačítko **+ Nová skupina akcí**. Skupina akcí je skupina akcí, které můžete použít ve více výstrahách. Mohou mezi ně patřit e-mailová oznámení, runbooky, webhooky a mnoho dalších. Další informace o skupinách akcí najdete v tématu [Vytváření a správa skupin akcí](../azure-monitor/platform/action-groups.md).

1. Do pole **Název skupiny akcí** zadejte popisný a krátký název. Krátký název se použije místo úplného názvu skupiny akcí při odesílání oznámení pomocí této skupiny.

1. V části **Akce** v části **ACTION TYPE**vyberte **možnost E-mail/SMS/Push/Voice**.

1. Na stránce **E-mailové/SMS/nabízené/hlasové oznámení** zadejte název. Zaškrtněte políčko **E-mail** a zadejte platnou e-mailovou adresu, která se má použít.

   ![Konfigurace e-mailové skupiny akcí](./media/automation-alert-activity-log/add-action-group.png)

1. Kliknutím na **OK** zavřete stránku **E-mailové/SMS/nabízené/hlasové oznámení** a kliknutím na **OK** zavřete stránku **Přidat skupinu akcí**. Název zadaný na této stránce je uložen jako **NÁZEV AKCE**.

1. Jakmile budete hotovi, klikněte na **Uložit**. Tato akce vytvoří pravidlo, které vás upozorní, když je runbook dokončen s určitým stavem.

> [!NOTE]
> Při přidávání e-mailové adresy do skupiny akcí se odešle e-mail s oznámením, že adresa byla přidána do skupiny akcí.

## <a name="notification"></a>Oznámení

Když jsou splněna kritéria výstrahy, skupina akcí spustí definovanou akci. V tomto článku je odeslán e-mail. Následující obrázek je příkladem e-mailu, který obdržíte po aktivaci výstrahy:

![Upozornění na e-mail](./media/automation-alert-activity-log/alert-email.png)

Jakmile metrika již není mimo definovanou prahovou hodnotu, výstraha se deaktivuje a skupina akcí spustí definovanou akci. Pokud je vybrán typ akce e-mailu, odešle se e-mail s rozlišením, který uvádí, že byl vyřešen.

## <a name="next-steps"></a>Další kroky

Pokračujte v následujícím článku a dozvíte se o dalších způsobech, jak můžete integrovat výstrahy do účtu automatizace.

> [!div class="nextstepaction"]
> [Spuštění runbooku Azure Automation pomocí výstrahy](automation-create-alert-triggered-runbook.md)
