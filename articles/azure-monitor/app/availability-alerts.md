---
title: Nastavení upozornění na dostupnost pomocí Azure Application Insights | Microsoft Docs
description: Naučte se, jak nastavit webové testy v Application Insights. Zasílání upozornění, pokud web přestane být k dispozici nebo reaguje pomalu.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: 300c4bcdb948665b2cdf4ce0eb429499208660b1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90970754"
---
# <a name="availability-alerts"></a>Upozornění na dostupnost

[Azure Application Insights](./app-insights-overview.md) odesílá do vaší aplikace webové požadavky v pravidelných intervalech z bodů po celém světě. Může vás upozornit, pokud vaše aplikace nereaguje nebo pokud reaguje příliš pomalu.

## <a name="enable-alerts"></a>Povolení upozornění

Výstrahy jsou teď ve výchozím nastavení automaticky povolené, ale aby bylo možné úplně nakonfigurovat výstrahu, musíte nejdřív vytvořit test dostupnosti.

![Vytvořit prostředí](./media/availability-alerts/create-test.png)

> [!NOTE]
>  S [novými sjednocenými výstrahami](../platform/alerts-overview.md)se **musí** nakonfigurovat závažnost pravidla výstrahy a předvolby oznámení se [skupinami akcí](../platform/action-groups.md) v prostředí výstrahy. Bez následujících kroků obdržíte pouze oznámení v portálu.

1. Po uložení testu dostupnosti klikněte na kartě Podrobnosti na tři tečky, které jste právě provedli. Klikněte na upravit upozornění.

   ![Upravit po uložení](./media/availability-alerts/edit-alert.png)

2. Nastavte požadovanou úroveň závažnosti, popis pravidla a nejdůležitější informace – skupina akcí, která má předvolby oznámení, kterou chcete použít pro toto pravidlo upozornění.

   ![Upravit po uložení](./media/availability-alerts/set-action-group.png)

> [!NOTE]
> Výstrahy dostupnosti vytvořené prostřednictvím tohoto prostředí jsou založené na stavu. To znamená, že když je kritérium výstrahy splněno, vygeneruje se jedna výstraha, když je lokalita zjištěná jako nedostupná. Pokud je lokalita stále v provozu při příštím vyhodnocení kritérií výstrahy, nebude vygenerována nová výstraha. Takže pokud vaše lokalita byla kratší hodinu a nastavili jste e-mailové upozornění, dostanete e-mail jenom v případě, že se web vypnul, a následný e-mail, když se web zálohoval. Nebudete dostávat nepřetržité výstrahy s upozorněním, že je lokalita stále nedostupná.

### <a name="alert-on-x-out-of-y-locations-reporting-failures"></a>Výstraha na ose X z umístění Y hlášení selhání

Pravidlo upozornění na umístění X z Y je ve výchozím nastavení povolené v [novém jednotném prostředí výstrah](../platform/alerts-overview.md)při vytváření nového testu dostupnosti. Můžete si vybrat možnost Classic nebo zakázat pravidlo výstrahy.

> [!NOTE]
> Nakonfigurujte skupiny akcí tak, aby přijímaly oznámení v případě, že se výstraha aktivuje podle výše uvedeného postupu. Bez tohoto kroku dostanete oznámení v portálu jenom v případě, že se pravidlo aktivuje.
>

### <a name="alert-on-availability-metrics"></a>Upozornění na metriky dostupnosti

Pomocí [nových sjednocených výstrah](../platform/alerts-overview.md)můžete upozornit na segmenticky agregovanou dostupnost a metriky doby trvání testu:

1. Vyberte prostředek Application Insights v prostředí metrik a vyberte metriku dostupnosti:

    ![Výběr metrik dostupnosti](./media/availability-alerts/select-metric.png)

2. Možnost konfigurovat výstrahy z nabídky vás převezme do nového prostředí, kde můžete vybrat konkrétní testy nebo umístění pro nastavení pravidla výstrahy. Tady můžete nakonfigurovat také skupiny akcí pro toto pravidlo upozornění.

### <a name="alert-on-custom-analytics-queries"></a>Upozornění na vlastní analytické dotazy

Pomocí [nových sjednocených výstrah](../platform/alerts-overview.md)můžete upozornit na [vlastní dotazy protokolu](../platform/alerts-unified-log.md). S vlastními dotazy můžete upozorňovat na jakékoli podmínky, které vám pomůžou dosáhnout nejspolehlivějšího signálu problémů s dostupností. To platí i v případě, že odesíláte vlastní výsledky dostupnosti pomocí sady TrackAvailability SDK.

> [!Tip]
> Metriky dostupnosti dat zahrnují všechny vlastní výsledky dostupnosti, které můžete odeslat voláním naší sady TrackAvailability SDK. Můžete použít upozorňování na podporu metrik pro upozornění na výsledky vlastní dostupnosti.
>

## <a name="automate-alerts"></a>Automatizace výstrah

Pokud chcete tento proces automatizovat pomocí šablon Azure Resource Manager, přečtěte si dokumentaci k tématu [Vytvoření upozornění na metriku pomocí šablony Správce prostředků](../platform/alerts-metric-create-templates.md#template-for-an-availability-test-along-with-a-metric-alert) .

## <a name="troubleshooting"></a>Řešení potíží

Vyhrazený [článek týkající se řešení potíží](troubleshoot-availability.md).

## <a name="next-steps"></a>Další kroky

* [Vícekrokové webové testy](availability-multistep.md)
* [Testování webových testů adresy URL](monitor-web-app-availability.md)

