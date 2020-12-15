---
title: Řídicí panel přehled služby Azure Application Insights | Microsoft Docs
description: Sledujte aplikace s využitím Azure Application Insights a přehled funkcí řídicího panelu.
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 1b0708fa70d3a3ecb406f1d974bb1f2b47e55b40
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2020
ms.locfileid: "97504096"
---
# <a name="application-insights-overview-dashboard"></a>Řídicí panel přehledu Application Insights

Application Insights má vždy k dispozici souhrnný přehled, který umožňuje rychlé, okamžité posouzení stavu a výkonu vaší aplikace. Nový řídicí panel přehled nabízí rychlejší flexibilní prostředí.

## <a name="how-do-i-test-out-the-new-experience"></a>Návody vyzkoušet nové prostředí?

Nový řídicí panel přehled se teď ve výchozím nastavení spustí:

![Podokno náhledu – přehled](./media/overview-dashboard/overview.png)

## <a name="better-performance"></a>Lepší výkon

Výběr časového rozsahu byl zjednodušen pro jednoduché rozhraní jedním kliknutím.

![Časové rozmezí](./media/overview-dashboard/app-insights-overview-dashboard-03.png)

Celkový výkon se významně zvýšil. Máte přístup jedním kliknutím k oblíbeným funkcím, jako je **hledání** a **Analýza**. Každá výchozí dlaždice s dynamicky aktualizovanými KUV nabízí přehled o odpovídajících funkcích Application Insights. Pokud chcete získat další informace o neúspěšných požadavcích, vyberte v hlavičce **prozkoumat** **selhání** :

![Selhání](./media/overview-dashboard/app-insights-overview-dashboard-04.png)

## <a name="application-dashboard"></a>Řídicí panel aplikací

Řídicí panel aplikace využívá stávající technologii řídicího panelu v rámci Azure a poskytuje plně přizpůsobitelné zobrazení stavu a výkonu vaší aplikace v jednom podokně.

Chcete-li získat přístup k výchozímu řídicímu panelu, vyberte _řídicí panel aplikace_ v levém horním rohu.

![Snímek obrazovky znázorňující zvýrazněné tlačítko řídicího panelu aplikace](./media/overview-dashboard/app-insights-overview-dashboard-05.png)

Pokud k řídicímu panelu přistupujete poprvé, spustí se výchozí zobrazení:

![Zobrazení řídicího panelu](./media/overview-dashboard/0001-dashboard.png)

Můžete ponechat výchozí zobrazení, pokud to chcete. Nebo můžete také přidat a odstranit z řídicího panelu, aby nejlépe vyhovovaly potřebám vašeho týmu.

> [!NOTE]
> Všichni uživatelé s přístupem k prostředku Application Insights sdílejí stejné možnosti řídicího panelu aplikace. Změny provedené jedním uživatelem změní zobrazení pro všechny uživatele.

Chcete-li přejít zpět k prostředí přehledu, stačí vybrat:

![Tlačítko Přehled](./media/overview-dashboard/app-insights-overview-dashboard-07.png)

## <a name="troubleshooting"></a>Řešení potíží

Pro data zobrazená v řídicím panelu je aktuálně povolený limit 30 dní dat. Pokud vyberete filtr času delší než 30 dní, nebo pokud vyberete **Konfigurovat nastavení dlaždic** a nastavit vlastní časový rozsah přesahující 30 dní, váš řídicí panel se nezobrazí déle než 30 dní dat, a to ani s výchozím uchováním dat 90 dnů. Pro toto chování není aktuálně k dispozici žádné alternativní řešení.

## <a name="next-steps"></a>Další kroky

- [Trychtýře](./usage-funnels.md)
- [Uchovávání](./usage-retention.md)
- [Toky uživatelů](./usage-flows.md)

