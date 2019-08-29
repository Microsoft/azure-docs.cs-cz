---
title: Nastavení upozornění na dostupnost pomocí Azure Application Insights | Microsoft Docs
description: Nastavení testů webu ve službě Application Insights. Zasílání upozornění, pokud web přestane být k dispozici nebo reaguje pomalu.
services: application-insights
documentationcenter: ''
author: lgayhardt
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: lagayhar
ms.openlocfilehash: 1d7527d6f52235c6b95ad2e336ea9f9ba85d6344
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114398"
---
# <a name="availability-alerts"></a>Upozornění na dostupnost

[Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) odesílá do vaší aplikace webové požadavky v pravidelných intervalech z bodů po celém světě. Může vás upozornit, pokud vaše aplikace nereaguje nebo pokud reaguje příliš pomalu.

## <a name="enable-alerts"></a>Povolit výstrahy

Výstrahy jsou teď ve výchozím nastavení automaticky povolené, ale aby bylo možné úplně nakonfigurovat výstrahu, musíte nejdřív vytvořit test dostupnosti.

![Vytvořit prostředí](./media/availability-alerts/create-test.png)

> [!NOTE]
>  S [novými sjednocenými výstrahami](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)se **musí** nakonfigurovat závažnost pravidla výstrahy a předvolby oznámení se [skupinami akcí](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) v prostředí výstrahy. Bez následujících kroků obdržíte pouze oznámení v portálu.

1. Po uložení testu dostupnosti klikněte na kartě Podrobnosti na tři tečky, které jste právě provedli. Klikněte na upravit upozornění.

   ![Upravit po uložení](./media/availability-alerts/edit-alert.png)

2. Nastavte požadovanou úroveň závažnosti, popis pravidla a nejdůležitější informace – skupina akcí, která má předvolby oznámení, kterou chcete použít pro toto pravidlo upozornění.

   ![Upravit po uložení](./media/availability-alerts/set-action-group.png)

### <a name="alert-on-x-out-of-y-locations-reporting-failures"></a>Výstraha na ose X z umístění Y hlášení selhání

Pravidlo upozornění na umístění X z Y je ve výchozím nastavení povolené v [novém jednotném prostředí výstrah](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)při vytváření nového testu dostupnosti. Můžete si vybrat možnost Classic nebo zakázat pravidlo výstrahy.

> [!NOTE]
> Nakonfigurujte skupiny akcí tak, aby přijímaly oznámení v případě, že se výstraha aktivuje podle výše uvedeného postupu. Bez tohoto kroku dostanete oznámení v portálu jenom v případě, že se pravidlo aktivuje.
>

### <a name="alert-on-availability-metrics"></a>Upozornění na metriky dostupnosti

Pomocí [nových sjednocených výstrah](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)můžete upozornit na segmenticky agregovanou dostupnost a metriky doby trvání testu:

1. Vyberte prostředek Application Insights v prostředí metrik a vyberte metriku dostupnosti:

    ![Výběr metrik dostupnosti](./media/availability-alerts/select-metric.png)

2. Možnost konfigurovat výstrahy z nabídky vás převezme do nového prostředí, kde můžete vybrat konkrétní testy nebo umístění pro nastavení pravidla výstrahy. Tady můžete nakonfigurovat také skupiny akcí pro toto pravidlo upozornění.

### <a name="alert-on-custom-analytics-queries"></a>Upozornění na vlastní analytické dotazy

Pomocí [nových sjednocených výstrah](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)můžete upozornit na [vlastní dotazy protokolu](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log). S vlastními dotazy můžete upozorňovat na jakékoli podmínky, které vám pomůžou dosáhnout nejspolehlivějšího signálu problémů s dostupností. To platí i v případě, že odesíláte vlastní výsledky dostupnosti pomocí sady TrackAvailability SDK.

> [!Tip]
> Metriky dostupnosti dat zahrnují všechny vlastní výsledky dostupnosti, které můžete odeslat voláním naší sady TrackAvailability SDK. Můžete použít upozorňování na podporu metrik pro upozornění na výsledky vlastní dostupnosti.
>

## <a name="automate-alerts"></a>Automatizace výstrah

Pokud chcete tento proces automatizovat pomocí šablon Azure Resource Manager, přečtěte si dokumentaci k tématu [Vytvoření upozornění na metriku pomocí šablony Správce prostředků](../../azure-monitor/platform/alerts-metric-create-templates.md#template-for-a-availability-test-along-with-availability-test-alert) .

## <a name="troubleshooting"></a>Řešení potíží

Vyhrazený [článek týkající se řešení potíží](troubleshoot-availability.md).

## <a name="next-steps"></a>Další kroky

* [Webové testy s více kroky](availability-multistep.md)
* [Testování webových testů adresy URL](monitor-web-app-availability.md)
