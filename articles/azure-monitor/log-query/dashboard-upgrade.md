---
title: Upgrade vizualizací řídicího panelu Log Analytics
description: Naučte se upgradovat vizualizace řídicího panelu Log Analytics pomocí dotazů, které vám poskytnou výkonné přehledy.
ms.subservice: logs
ms.topic: article
author: rboucher
ms.author: robb
ms.date: 07/01/2020
ms.openlocfilehash: a029dcbebf6dfe7a2b6cb517641c824a5937ca95
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90988244"
---
# <a name="upgrading-your-log-analytics-dashboard-visualizations"></a>Upgrade vizualizací řídicího panelu Log Analytics

V únoru 2020 jsme představili vylepšenou technologii vizualizace. Zlepšení a vylepšení vaší schopnosti vizualizovat výsledky dotazů a rychle dosáhnout výkonných přehledů. 

V této [aktualizaci Azure](https://azure.microsoft.com/updates/azure-monitor-log-analytics-upgraded-results-visualization/)si můžete přečíst další informace o tomto upgradu. 

Tato nová technologie vizualizace je přízpůsobí pro nové a vylepšené prostředí kolem sady výsledků dotazu. 

## <a name="dashboards-in-azure"></a>Řídicí panely v Azure

Řídicí panely Azure představují způsob, jak vizualizovat stav celé oblasti Azure Surface. Jsou navržené tak, aby poskytovaly jediné podokno se svým stavem v Azure a povolili celou řadu zástupců k běžným akcím. 

Další informace najdete v tématu [řídicí panely Azure](../../azure-portal/azure-portal-dashboards.md) .


## <a name="upgrading-log-analytics-dashboard-parts"></a>Upgrade Log Analytics částí řídicího panelu

Nová technologie vizualizace řeší některé běžné problémy se starou implementací a zavádí některé nové funkce Připnuté Log Analytics částí: 

- **Stejné dostupné typy** – všechny typy vizualizací, které jsou dostupné v Log Analytics, jsou k dispozici jako připnuté části na řídicích panelech.

- **Konzistentní vzhled** – zobrazení vizualizace a chování pro připnuté součásti je teď skoro stejné jako u Log Analytics. Rozdíly jsou způsobeny optimalizacemi, které vyžadují drobné rozdíly v obsahu dat vizuálu. Další informace o těchto rozdílech najdete v části aspekty tohoto dokumentu.

- **Popisy tlačítek a popisky** – nové připnuté Log Analytics vizualizace podporují popisy tlačítek. Výsečové a prstencové grafy nyní podporují popisky.

- **Interaktivní legendy** – kliknutím na legendu vizualizace můžete přidat nebo odebrat dimenze z připnutého vizuálu jako v Log Analytics.

## <a name="stage-1---opt-in-upgrade-message"></a>Fáze 1 – zpráva o upgradu souhlasu

Když je možné upgradovat část Log Analytics připnuté, zobrazí *se nové oznámení o přihlášení na* Log Analytics připnutých částech řídicích panelů, které umožní uživatelům upgradovat jejich vizualizaci. Pokud chcete vyzkoušet nové vizualizace a upgradovat vybrané vizualizace ve svém řídicím panelu.

 
![Stín](media/dashboard-upgrade/update-message-1.png)
 
![Stín](media/dashboard-upgrade/update-message-2.png)

> [!WARNING]
> Po publikování řídicího panelu je upgrade nevratný. Změny se ale zahodí, Pokud opustíte řídicí panel bez nutnosti opětovného publikování.  

Po kliknutí bude vizualizace aktualizována na novou technologii. Malé rozdíly ve vizualizaci mohou nastat při zarovnávání s jejich pohledem a chováním v Log Analytics.

Po upgradu vizualizací je potřeba znovu publikovat řídicí panel, aby se změna projevila.

![Stín](media/dashboard-upgrade/update-message-3.png)

## <a name="stage-2---migration-of-all-dashboards"></a>Fáze 2 – migrace všech řídicích panelů

Po uplynutí počátečního přihlašovacího období bude tým Log Analytics upgradovat všechny řídicí panely v systému. Zarovnávání všech řídicích panelů Azure umožňuje týmu zavést více vizualizací a vylepšení zkušeností napříč panelem.

## <a name="considerations"></a>Důležité informace

Log Analytics vizualizace připnuté na řídicí panel mají určité specifické chování, které je navržené pro optimální prostředí. Pokud připnete vizualizaci na řídicí panel, přečtěte si následující pokyny k návrhu.

### <a name="query-time-scope---30-day-limit"></a>Rozsah času dotazu – limit 30 dní

Vzhledem k tomu, že řídicí panely můžou obsahovat více vizualizací z několika dotazů, časový rozsah každého připnutého dotazu je omezený na 30 dnů. Jeden dotaz může běžet pouze v časovém rozsahu, který je menší nebo roven 30 dnů. Toto omezení znamená zajistit přiměřenou dobu načítání řídicího panelu.

### <a name="query-data-values---25-values-and-other-grouping"></a>Dotazování hodnot dat – 25 hodnot a dalších seskupení

Řídicí panely můžou být vizuálně husté a složité. Aby se snížilo zatížení při prohlížení řídicího panelu, optimalizujte vizualizace tím, že omezíte zobrazení na 25 různých datových typů. Pokud je jich více než 25, Log Analytics data optimalizuje. V takovém případě se zobrazí 25 typů s největším množstvím dat jako samostatné a potom se zbývající hodnoty seskupí do hodnoty "jiné". Následující graf ukazuje tento případ.  

![Stín](media/dashboard-upgrade/values-25-limit.png)

### <a name="dashboard-refresh-on-load"></a>Aktualizace řídicího panelu při zatížení

Řídicí panely se aktualizují při načtení. Všechny dotazy týkající se vizualizací Log Analytics připnuté na řídicím panelu se spustí a po načtení se řídicí panel aktualizuje. Pokud zůstane stránka řídicího panelu otevřená, data na řídicím panelu se aktualizují každých 60 minut.

## <a name="next-steps"></a>Další kroky

[Vytváření a sdílení řídicích panelů v Log Analytics](../learn/tutorial-logs-dashboards.md)
