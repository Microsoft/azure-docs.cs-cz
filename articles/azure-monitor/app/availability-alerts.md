---
title: Nastavení upozornění na dostupnost pomocí Přehledů aplikací Azure | Dokumenty společnosti Microsoft
description: Nastavení testů webu ve službě Application Insights. Zasílání upozornění, pokud web přestane být k dispozici nebo reaguje pomalu.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: e0c1a93ef663762bec199abc5aa7eabbc821168d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654949"
---
# <a name="availability-alerts"></a>Upozornění na dostupnost

[Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) odesílá do vaší aplikace webové požadavky v pravidelných intervalech z bodů po celém světě. Může vás upozornit, pokud vaše aplikace neodpovídá nebo pokud reaguje příliš pomalu.

## <a name="enable-alerts"></a>Povolení upozornění

Výstrahy jsou nyní automaticky povoleny ve výchozím nastavení, ale aby bylo možné plně nakonfigurovat výstrahu, musíte nejprve vytvořit test dostupnosti.

![Vytváření prostředí](./media/availability-alerts/create-test.png)

> [!NOTE]
>  U [nových jednotných výstrah](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts) **musí být** v prostředí výstrah nakonfigurována závažnost pravidla výstrahy a předvolby oznámení se [skupinami akcí.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) Bez následujících kroků budete dostávat pouze oznámení na portálu.

1. Po uložení testu dostupnosti klikněte na kartě podrobnosti na tři tečky testem, který jste právě provedli. Klikněte na "upravit upozornění".

   ![Upravit po uložení](./media/availability-alerts/edit-alert.png)

2. Nastavte požadovanou úroveň závažnosti, popis pravidla a co je nejdůležitější – skupinu akcí, která má předvolby oznámení, které chcete použít pro toto pravidlo výstrahy.

   ![Upravit po uložení](./media/availability-alerts/set-action-group.png)

### <a name="alert-on-x-out-of-y-locations-reporting-failures"></a>Výstraha na x mimo umístění Y hlásí cípy

Pravidlo výstrahy Umístění X mimo Y je ve výchozím nastavení povoleno v [novém prostředí sjednocených výstrah](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)při vytváření nového testu dostupnosti. Můžete se odhlásit výběrem možnosti "klasické" nebo se rozhodli zakázat pravidlo výstrahy.

> [!NOTE]
> Nakonfigurujte skupiny akcí tak, aby přijímaných oznámení při aktivaci výstrahy podle výše uvedených kroků. Bez tohoto kroku budete dostávat pouze oznámení na portálu, když se pravidlo aktivuje.
>

### <a name="alert-on-availability-metrics"></a>Upozornění na metriky dostupnosti

Pomocí [nových jednotných výstrah](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)můžete také upozornit na segmentobilní agregátní metriky dostupnosti a doby trvání testu:

1. Vyberte prostředek Application Insights v prostředí metriky a vyberte metriku dostupnost:

    ![Výběr metrik dostupnosti](./media/availability-alerts/select-metric.png)

2. Možnost Konfigurovat výstrahy z nabídky vás přenese do nového prostředí, kde můžete vybrat konkrétní testy nebo umístění, na které chcete nastavit pravidlo výstrah. Skupiny akcí pro toto pravidlo výstrahy můžete také nakonfigurovat zde.

### <a name="alert-on-custom-analytics-queries"></a>Upozornění na vlastní analytické dotazy

Pomocí [nových jednotných výstrah](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)můžete upozornit na [vlastní dotazy protokolu](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log). Pomocí vlastních dotazů můžete upozornit na libovolné podmínky, které vám pomohou získat nejspolehlivější signál o dostupnosti. To platí také, pokud odesíláte vlastní výsledky dostupnosti pomocí sady TrackAvailability SDK.

> [!Tip]
> Metriky na datech dostupnosti zahrnují všechny vlastní výsledky dostupnosti, které můžete odesílat, telefonicky na naší sdk dostupnosti stop. Pomocí podpory upozornění na metriky můžete upozornit na vlastní výsledky dostupnosti.
>

## <a name="automate-alerts"></a>Automatizace výstrah

Chcete-li tento proces automatizovat pomocí šablon Azure Resource Manager, podívejte se na [oznámení vytvořit metriku s](../../azure-monitor/platform/alerts-metric-create-templates.md#template-for-an-availability-test-along-with-a-metric-alert) dokumentací šablony Správce prostředků.

## <a name="troubleshooting"></a>Řešení potíží

Vyhrazený [článek o řešení potíží](troubleshoot-availability.md).

## <a name="next-steps"></a>Další kroky

* [Vícekrokové webové testy](availability-multistep.md)
* [Webové testy příkazu URL](monitor-web-app-availability.md)
