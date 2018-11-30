---
title: Vytvoření vlastního řídicího panelu ve službě Azure Log Analytics | Dokumentace Microsoftu
description: Tento průvodce vám pomůže porozumět způsobu řídicí panely Log Analytics dokáží vizualizovat všechna vaše uložená prohledávání protokolů, získáte jednotný přehled vašeho prostředí.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: b692eb64f84e49a085e1d388b1e418a8d9c1a245
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52642631"
---
# <a name="create-a-custom-dashboard-for-use-in-log-analytics"></a>Vytvořit vlastní řídicí panel pro použití v Log Analytics

Tento průvodce vám pomůže porozumět způsobu řídicí panely Log Analytics dokáží vizualizovat všechna vaše uložená prohledávání protokolů, získáte jednotný přehled vašeho prostředí.

>[!NOTE]
> Nemůžete nadále upravovat stávající **Můj řídicí panel**. Tuto funkci se právě přestanou používat.

![Příklad řídicího panelu](./media/dashboards/oms-dashboards-example-dash.png)

Všechny vlastní řídicí panely, které vytvoříte na portálu OMS jsou dostupné v mobilní aplikaci OMS. Získáte na následujících stránkách pro další informace o aplikacích.

* [Mobilní aplikaci OMS z Microsoft Store](http://www.windowsphone.com/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865)
* [Mobilní aplikaci OMS z Apple iTunes](https://itunes.apple.com/app/microsoft-operations-management/id1042424859?mt=8)

![mobilní řídicího panelu](./media/dashboards/oms-search-mobile.png)

## <a name="how-do-i-create-my-dashboard"></a>Jak vytvořím Můj řídicí panel?
Pokud chcete začít, přejděte na stránku přehled OMS. Zobrazí se vám **Můj řídicí panel** dlaždice na levé straně. Klikněte na něj můžete přejít na řídicí panel.

![Přehled](./media/dashboards/oms-dashboards-overview.png)

## <a name="adding-a-tile"></a>Přidání dlaždice
V řídicích panelech dlaždicích využívají vaše uložená prohledávání protokolů. OMS se dodává s mnoha předem vytvořených uložená prohledávání protokolů, abyste mohli hned začít. Pomocí následujících kroků, které popisují, jak začít.

V zobrazení na vlastní řídicí panel, stačí kliknout **vlastní** vstupovat do režimu přizpůsobení.

![Obrazové](./media/dashboards/oms-dashboards-pictorial01.png)

 Na panelu, které se otevře na pravé straně stránky zobrazí všechny pracovního prostoru uložená prohledávání protokolů. Vizualizace prohledávání protokolu uložené jako dlaždice, najeďte myší uložené výsledky hledání a klikněte na tlačítko **plus** symbol.

![Přidat dlaždice 1](./media/dashboards/oms-dashboards-pictorial02.png)

Když kliknete **plus** symbol, v zobrazení na vlastní řídicí panel se zobrazí nová dlaždice.

![Přidat dlaždice 2](./media/dashboards/oms-dashboards-pictorial03.png)

## <a name="edit-a-tile"></a>Úprava dlaždice
V zobrazení na vlastní řídicí panel, stačí kliknout **vlastní** vstupovat do režimu přizpůsobení. Kliknutím na dlaždici, kterou chcete upravit. Pravý panel změny chcete upravit a výběr možností:

![Úprava dlaždice](./media/dashboards/oms-dashboards-pictorial04.png)

![Úprava dlaždice](./media/dashboards/oms-dashboards-pictorial05.png)

### <a name="tile-visualizations"></a>Vizualizace dlaždice
Existují tři typy dlaždic vizualizací můžete vybírat z:

| Typ grafu | Co to dělá |
| --- | --- |
| ![Pruhový graf](./media/dashboards/oms-dashboards-bar-chart.png) |Časová osa výsledků hledání uložený protokol zobrazí jako pruhový graf, nebo seznam výsledků podle pole v závislosti na, pokud vyhledávání protokolu agreguje výsledky podle pole, nebo ne. |
| ![metrika](./media/dashboards/oms-dashboards-metric.png) |Vaše celkové protokolu hledání výsledku přístupů zobrazí jako číslo v dlaždici. Metriky dlaždice umožňují nastavit prahovou hodnotu, která se na dlaždici zvýraznit, když je dosaženo prahové hodnoty. |
| ![Řádek](./media/dashboards/oms-dashboards-line.png) |Jako spojnicový graf zobrazuje časová osa vaše přístupů uložený protokol hledání výsledku s hodnotami. |

### <a name="threshold"></a>Prahová hodnota
Na dlaždici pomocí metrik vizualizace můžete vytvořit prahovou hodnotu. Vyberte na vytvoření prahovou hodnotu na dlaždici. Zvolte, zda chcete zvýraznit dlaždice, když hodnota je nad nebo pod zvolenou prahovou hodnotu, pak nastavte pod prahovou hodnotu.

## <a name="organizing-the-dashboard"></a>Uspořádání řídicího panelu
K uspořádání řídicí panel, přejděte do zobrazení Můj řídicí panel a klikněte na tlačítko **vlastní** zadat vlastní nastavení režimu. Klikněte a přetáhněte dlaždici, kterou chcete přesunout a přesuňte ho na místo, kam chcete dlaždice bude.

![Uspořádání řídicí panel](./media/dashboards/oms-dashboards-organize.png)

## <a name="remove-a-tile"></a>Odebrat dlaždici
Odebrat dlaždici, přejděte do zobrazení Můj řídicí panel a klikněte na **vlastní** zadat vlastní nastavení režimu. Vyberte dlaždici, kterou chcete odebrat a pak v pravém panelu vyberte **odebrat dlaždici**.

![Odebrat dlaždici](./media/dashboards/oms-dashboards-remove-tile.png)

## <a name="next-steps"></a>Další postup
* Vytvoření [výstrahy](../../monitoring-and-diagnostics/monitoring-overview-alerts.md) v Log Analytics ke generování oznámení a opravovat problémy.
