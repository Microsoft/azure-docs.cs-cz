---
title: Řídicí panel přehled služby Azure Application Insights | Microsoft Docs
description: Sledujte aplikace s využitím Azure Application Insights a přehled funkcí řídicího panelu.
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 714488d88b269f252dfd6677f847db315969a270
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90970620"
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

Pokud vyberete **Konfigurovat nastavení dlaždic** a nastavíte vlastní časový rozsah přesahující 31 dní, řídicí panel se nezobrazí déle než 31 dní dat, a to ani s výchozím uchováváním dat 90 dnů. Pro toto chování není aktuálně k dispozici žádné alternativní řešení.

## <a name="next-steps"></a>Další kroky

- [Trychtýře](./usage-funnels.md)
- [Uchovávání](./usage-retention.md)
- [Toky uživatele](./usage-flows.md)

