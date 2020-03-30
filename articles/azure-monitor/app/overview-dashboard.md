---
title: Řídicí panel Přehled přehledů aplikací Azure | Dokumenty společnosti Microsoft
description: Monitorujte aplikace pomocí azure application insights a funkce přehledového řídicího panelu.
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: e5188972d9058b85a9765c7d33f6209b37245d7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669892"
---
# <a name="application-insights-overview-dashboard"></a>Řídicí panel Přehled aplikací

Application Insights vždy poskytuje podokno souhrnného přehledu, které umožňuje rychlé a přehledné posouzení stavu a výkonu vaší aplikace. Nový přehledový řídicí panel poskytuje rychlejší flexibilitu.

## <a name="how-do-i-test-out-the-new-experience"></a>Jak mohu vyzkoušet nové prostředí?

Nový řídicí panel přehledu se nyní ve výchozím nastavení spouští:

![Podokno náhledu přehledu](./media/overview-dashboard/overview.png)

## <a name="better-performance"></a>Lepší výkon

Výběr časového rozsahu byl zjednodušen na jednoduché rozhraní jedním kliknutím.

![Časové rozmezí](./media/overview-dashboard/app-insights-overview-dashboard-03.png)

Celkový výkon byl výrazně zvýšen. Máte přístup jedním kliknutím k oblíbeným funkcím, jako **je Vyhledávání** a **Analýza**. Každá výchozí dlaždici dynamické aktualizace klíčového ukazatele výkonu poskytuje přehled o odpovídajících funkcích Application Insights. Chcete-li získat další informace o neúspěšných požadavcích, vyberte **chyby** v hlavičce **Investigate:**

![Selhání](./media/overview-dashboard/app-insights-overview-dashboard-04.png)

## <a name="application-dashboard"></a>Řídicí panel aplikací

Řídicí panel aplikace využívá stávající technologii řídicího panelu v rámci Azure k zajištění plně přizpůsobitelného zobrazení stavu a výkonu aplikace v jednom podokně.

Chcete-li získat přístup k výchozímu řídicímu panelu, vyberte _řídicí panel aplikace_ v levém horním rohu.

![Zobrazení řídicího panelu](./media/overview-dashboard/app-insights-overview-dashboard-05.png)

Pokud se k řídicímu panelu přistupuje poprvé, spustí se výchozí zobrazení:

![Zobrazení řídicího panelu](./media/overview-dashboard/0001-dashboard.png)

Pokud se vám líbí, můžete zachovat výchozí zobrazení. Nebo můžete také přidat a odstranit z řídicího panelu, aby co nejlépe vyhovovaly potřebám vašeho týmu.

> [!NOTE]
> Všichni uživatelé s přístupem k prostředku Application Insights sdílejí stejné prostředí řídicího panelu aplikace. Změny provedené jedním uživatelem změní zobrazení pro všechny uživatele.

Chcete-li přejít zpět do přehledu, stačí vybrat:

![Tlačítko Přehled](./media/overview-dashboard/app-insights-overview-dashboard-07.png)

## <a name="troubleshooting"></a>Řešení potíží

Pokud vyberete **Konfigurovat nastavení dlaždic** a nastavíte vlastní časový rozsah na více než 31 dní, řídicí panel se nezobrazí po 31 dnech dat, a to ani při výchozím uchovávání dat 90 dní. V současné době neexistuje žádné řešení pro toto chování.

## <a name="next-steps"></a>Další kroky

- [Trychtýře](../../azure-monitor/app/usage-funnels.md)
- [Uchovávání](../../azure-monitor/app/usage-retention.md)
- [Toky uživatele](../../azure-monitor/app/usage-flows.md)
