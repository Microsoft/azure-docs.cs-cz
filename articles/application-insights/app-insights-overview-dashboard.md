---
title: Řídicí panel Přehled Statistika aplikací | Microsoft Docs
description: Monitorování aplikací s funkcemi Azure Application Insights a řídicí panel Přehled.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: mbullwin
ms.openlocfilehash: bccb56ad45d9054a437bf2d85e74a8d81fbc3db1
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2018
---
# <a name="overview-dashboard-preview"></a>Přehled řídicí panel (preview)

Application Insights vždy poskytl přehled souhrnu podokně umožňuje rychlý a na přehled vyhodnocení stavu a výkonu vaší aplikace. Spouštění na 15. května 2018 nové prostředí pro rychlejší flexibilnější, budou vydané jako náhled. Na 29. květen 2018 vyřadí classic Přehled prostředí.

## <a name="how-do-i-test-out-the-new-experience"></a>Jak otestovat na nové prostředí?

Na 15 může na nové prostředí zahájíte objeví ve službě Application Insights v části: _prošetření_ > _přehled (preview)_.

![Přehled Preview](.\media\app-insights-overview-dashboard\01.png)

Tím se spustí novou výchozí přehled řídicí panel:

![Přehled podokna náhledu](.\media\app-insights-overview-dashboard\02.png)

## <a name="better-performance"></a>Lepší výkon

Výběr časového rozsahu zjednodušenou jednoduché rozhraní jedním kliknutím.

![Časové rozmezí](.\media\app-insights-overview-dashboard\04.png)

Celkový výkon značně zvýšilo. Každý výchozí dynamicky aktualizuje klíčového ukazatele výkonu dlaždice se propojí odpovídající funkci Application Insights. Například výběr neúspěšných požadavků se spustí _selhání_ podokně:

![Počet selhání](.\media\app-insights-overview-dashboard\03.png)

## <a name="application-dashboard"></a>Řídicí panel aplikací

Řídicí panel aplikací využívá technologie existující řídicí panel v rámci Azure zajistit plně přizpůsobitelná jednoho podokna zobrazení stavu aplikací a výkonu.

Pro přístup k výchozí řídicí panel select _řídicí panel aplikací_ v levém horním rohu.

![Zobrazení řídicího panelu](.\media\app-insights-overview-dashboard\0009.png)

Toto je poprvé přístup k řídicím panelu se spustí výchozí zobrazení:

![Zobrazení řídicího panelu](.\media\app-insights-overview-dashboard\06.png)

Zatímco můžete ponechat výchozí zobrazení, pokud ho chcete, můžete také přidat a odstranit z řídicího panelu na nejvhodnější podle potřeb vašeho týmu.

Přejděte zpět na právě vyberte Přehled prostředí:

![Tlačítko – Přehled](.\media\app-insights-overview-dashboard\07.png)

Je také nové tlačítko názvem _částí kódu Pin_.

![Tlačítko – Přehled](.\media\app-insights-overview-dashboard\008.png)

To replikuje trochu známé funkce z klasického přehled, který vám umožní všechny dlaždice ze staré Přehled prostředí _(výstrahy, dostupnosti, Live metriky, využití, proaktivní detekce a mapy aplikací)_ a přidáte do vlastní řídicí panely. 

V případě výchozí _řídicí panel aplikací_ jsme již přidali tyto dlaždice. Ale pokud vytvoříte další vlastní řídicí panely, nebo pokud někdo ve vašem týmu odstraní classic dlaždice a chcete ho přidat zpět, _částí kódu Pin_ poskytuje tuto funkci v žádném snadno najít místní.
