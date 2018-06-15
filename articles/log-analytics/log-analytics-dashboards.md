---
title: Vytvořit vlastní řídicí panel v Azure Log Analytics | Microsoft Docs
description: Tento průvodce vám pomůže pochopit jak řídicí panely analýzy protokolů můžete vizualizovat všechny uložený protokol hledání, budete jeden přehledu zobrazení prostředí.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2017
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7eaa74655b009e3154e2cc4f09d5599040ec4589
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
ms.locfileid: "34010767"
---
# <a name="create-a-custom-dashboard-for-use-in-log-analytics"></a>Vytvořit vlastní řídicí panel pro použití v analýzy protokolů

Tento průvodce vám pomůže pochopit jak řídicí panely analýzy protokolů můžete vizualizovat všechny uložený protokol hledání, budete jeden přehledu zobrazení prostředí.

>[!NOTE]
> Už můžete upravit stávající **vlastní řídicí panel**. Tato funkce se právě zastaralá.

![Příklad řídicího panelu](./media/log-analytics-dashboards/oms-dashboards-example-dash.png)

Všechny vlastní řídicí panely, které vytvoříte na portálu OMS jsou také k dispozici v OMS mobilní aplikace. Najdete na následujících stránkách Další informace o aplikacích.

* [OMS mobilní aplikace z Windows Store Microsoft](http://www.windowsphone.com/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865)
* [OMS mobilních aplikací z Apple iTunes](https://itunes.apple.com/app/microsoft-operations-management/id1042424859?mt=8)

![mobilní řídicí panel](./media/log-analytics-dashboards/oms-search-mobile.png)

## <a name="how-do-i-create-my-dashboard"></a>Vytvoření Můj řídicí panel
Pokud chcete začít, přejděte na stránku přehled OMS. Zobrazí se **vlastní řídicí panel** dlaždici na levé straně. Klikněte na něj můžete rozbalit řídicího panelu.

![Přehled](./media/log-analytics-dashboards/oms-dashboards-overview.png)

## <a name="adding-a-tile"></a>Přidat dlaždice
V řídicí panely dlaždice se používá technologii uložený protokol hledání. OMS se dodává s mnoha předem provedené uložený protokol hledání, abyste mohli začít. Použijte následující kroky, které popisují, jak začít.

V zobrazení vlastní řídicí panel, klikněte jednoduše **přizpůsobit** zadat vlastní nastavení režimu.

![Obrazové](./media/log-analytics-dashboards/oms-dashboards-pictorial01.png)

 Na panelu, které se otevře na pravé straně stránky zobrazí všechny pracovního prostoru uložený protokol hledání. K vizualizaci uložený protokol hledání jako dlaždici, najeďte myší na uloženého hledání a klikněte **plus** symbol.

![Přidat dlaždice 1](./media/log-analytics-dashboards/oms-dashboards-pictorial02.png)

Když kliknete **plus** symbolů, nová dlaždice se zobrazí v zobrazení Moje řídicího panelu.

![Přidat dlaždice 2](./media/log-analytics-dashboards/oms-dashboards-pictorial03.png)

## <a name="edit-a-tile"></a>Upravit dlaždici
V zobrazení vlastní řídicí panel, klikněte jednoduše **přizpůsobit** zadat vlastní nastavení režimu. Klikněte na dlaždici, kterou chcete upravit. Změny pravém panelu, který chcete upravit a umožňuje výběr možností:

![Upravit vedle sebe](./media/log-analytics-dashboards/oms-dashboards-pictorial04.png)

![Upravit vedle sebe](./media/log-analytics-dashboards/oms-dashboards-pictorial05.png)

### <a name="tile-visualizations"></a>Vizualizace dlaždice
Existují tři druhy vizualizace dlaždice můžete vybrat ze:

| Typ grafu | Jak funguje |
| --- | --- |
| ![Pruhový graf](./media/log-analytics-dashboards/oms-dashboards-bar-chart.png) |Časová osa výsledků hledání uložený protokol zobrazí jako pruhový graf nebo seznam výsledků podle pole v závislosti na tom, pokud vyhledávání protokolu agreguje výsledky podle pole, nebo ne. |
| ![metrika](./media/log-analytics-dashboards/oms-dashboards-metric.png) |Zobrazí vaše přístupů výsledek hledání celkový protokolu jako číslo v dlaždici. Metriky dlaždice umožňují nastavit prahovou hodnotu, která se zaměřuje na dlaždici na dosažení prahové hodnoty. |
| ![řádek](./media/log-analytics-dashboards/oms-dashboards-line.png) |Jako spojnicový graf zobrazuje časová osa přístupů vaší uložený protokol hledání výsledek s hodnotami. |

### <a name="threshold"></a>Sloučit barvy
Na dlaždici pomocí metriky vizualizace můžete vytvořit prahovou hodnotu. Vyberte na vytvoření prahovou hodnotu na dlaždici. Vyberte, zda chcete zvýraznit dlaždici, když hodnota je nad nebo pod zvolenou prahovou hodnotu, a poté nastavit mezní hodnotu níže.

## <a name="organizing-the-dashboard"></a>Uspořádání řídicí panel
Chcete-li uspořádat řídicího panelu, přejděte do zobrazení Moje řídicího panelu a klikněte na **přizpůsobit** k zadání vlastní nastavení režimu. Klikněte na tlačítko a přetáhněte dlaždice, které chcete přesunout a přesunout na místo, kde dlaždice. Chcete-li být.

![Uspořádání řídicího panelu](./media/log-analytics-dashboards/oms-dashboards-organize.png)

## <a name="remove-a-tile"></a>Odebrat dlaždici
Odebrat dlaždici, přejděte do zobrazení Moje řídicího panelu a klikněte na tlačítko **přizpůsobit** zadat vlastní nastavení režimu. Vyberte dlaždici, kterou chcete odebrat a pak na pravém panelu vyberte **odebrat dlaždici**.

![Odebrat dlaždici](./media/log-analytics-dashboards/oms-dashboards-remove-tile.png)

## <a name="next-steps"></a>Další postup
* Vytvoření [výstrahy](log-analytics-alerts.md) v analýzy protokolů generování oznámení a opravovat problémy.
