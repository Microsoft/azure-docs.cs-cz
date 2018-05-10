---
title: Mapa aplikace ve službě Azure Application Insights | Microsoft Docs
description: Monitorování topologie komplexních aplikací s aplikací mapy
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: mbullwin; Soubhagya.Dash
ms.openlocfilehash: a02d345c0f624f2d1694b4b9622bf3c82c009429
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="application-map-triage-distributed-applications"></a>Mapa aplikace: Rychlou kontrolu distribuované aplikace
Mapy aplikací umožňuje přímé kritické body nebo selhání hotspotům pro všechny součásti distribuované aplikace. Každý uzel na mapě představuje komponentu aplikace nebo jeho závislé součásti; a má stav klíčového ukazatele výkonu a výstrahy stavu. Můžete kliknutím z libovolné součásti podrobnější diagnostiku, jako je například události Application Insights. Pokud vaše aplikace používá služby Azure, můžete také kliknutím na Azure diagnostics, jako je například doporučení Poradce pro databáze systému SQL.

## <a name="what-is-a-component"></a>Co je součástí?

Součásti jsou nezávisle nasadit částí aplikace distribuované nebo mikroslužeb. Operace a vývojáři týmy mají viditelnost na úrovni kódu nebo o přístup k telemetrii vygenerovanou tyto součásti aplikace. 

* Komponenty se liší od "zjištěnou" externí závislosti, jako je například SQL, EventHub atd, které váš tým nebo organizace nemusí mít přístup k (kód nebo telemetrie).
* Součásti spustit na libovolný počet instancí serveru, role nebo kontejnerů.
* Součástí může být samostatné klíčů instrumentace Application Insights (i v případě předplatných se liší) nebo různé role zprávy jeden klíč instrumentace Application Insights. Prostředí mapy preview zobrazuje komponenty bez ohledu na to, jak jsou nastavené.

## <a name="composite-application-map-preview"></a>Mapování složených aplikace (Preview)
*Toto je časná preview a přidáme další funkce pro tuto mapu. Rádi získat váš názor na nové prostředí. Můžete přepínat mezi preview a classic prostředí snadno.*

Povolit "Kompozitních aplikací Map" z [verze Preview seznamu](app-insights-previews.md), nebo klikněte na "Preview map" v přepínač v pravém horním rohu. Přejděte zpět na klasické prostředí můžete použít tento přepínač.
![Povolit preview mapy](media/app-insights-app-map/preview-from-classic.png)

>[!Note]
Tato verze preview nahrazuje předchozí verze preview "Mapování více rolí aplikací". V tomto okamžiku použijte k zobrazení celého topologie napříč více úrovní závislostí součásti aplikace. Sdělte nám svůj názor, přidáme podobná co classic mapy podporuje další funkce.

Topologie celou aplikaci můžete zobrazit napříč více úrovní součásti související aplikace. Součástí může být různé prostředky Application Insights, nebo různé role v jednom prostředku. Mapa aplikace vyhledá součásti podle následující HTTP závislostí volání mezi servery s Application Insights SDK nainstalována. 

Toto prostředí začíná progresivní zjišťování součástí. Při načítání nejprve ve verzi preview, vyvolají se ke zjištění součásti související se tato součást sadu dotazy. Počet součástí ve vaší aplikaci aktualizuje tlačítko v levém horním rohu, které jsou zjištěny. 
![Mapa Preview](media/app-insights-app-map/preview.png)

Po kliknutí na tlačítko "Aktualizace mapy komponenty", se aktualizují mapy se všemi součástmi odhalit dříve, než který bodu.
![Náhled načíst mapy](media/app-insights-app-map/components-loaded-hierarchical.png)

Pokud jsou všechny komponenty rolí v rámci jednoho prostředku Application Insights, není tento krok zjišťování povinný. Počáteční zatížení pro takové aplikace budou mít všechny jeho komponenty.

Jedním z cílů klíče s nové prostředí je možné vizualizovat komplexních topologiích se stovkami součásti. Nové prostředí podporuje přibližování a přidá podrobnosti, jako je přiblížení. Vám může oddálení zobrazíte další na první pohled a stále spot komponenty s vyšší selhání sazby. 

![Úrovně přiblížení](media/app-insights-app-map/zoom-levels.png)

Klikněte na všechny součásti, které chcete zobrazit přehledy související s a přejděte na výkon a selhání třídění prostředí pro danou součást.

![Plovoucím panelem](media/app-insights-app-map/preview-flyout.png)


## <a name="classic-application-map"></a>Mapa Classic aplikace

Mapa zobrazuje:

* Testy dostupnosti
* Součásti klientské strany (monitorovat pomocí JavaScript SDK)
* Klientská součást produktu
* Závislosti součásti klienta a serveru

![Mapa aplikace](./media/app-insights-app-map/02.png)

Můžete rozbalit nebo sbalit skupiny odkaz závislost:

![Sbalit](./media/app-insights-app-map/03.png)

Pokud máte velký počet závislostí jednoho typu (SQL, HTTP atd.), se mohou objevit seskupené. 

![seskupené závislosti](./media/app-insights-app-map/03-2.png)

## <a name="spot-problems"></a>Identifikaci problémů
Každý uzel má ukazatele relevantní výkonu, jako je například zatížení, výkonu a selhání sazby za tuto součást. 

Ikony upozornění upozorňují na možné problémy. Oranžové upozornění znamená, že tam jsou chyby v žádostech, zobrazení stránek nebo volání závislostí. Red se rozumí míra selhání výše 5 %. Pokud chcete upravit tyto prahové hodnoty, otevřete panel Možnosti.

![selhání ikony](./media/app-insights-app-map/04.png)

Aktivní výstrahy také zobrazit nahoru: 

![aktivní výstrahy](./media/app-insights-app-map/05.png)

Pokud používáte SQL Azure, je ikonu, která ukazuje, kdy jsou doporučení na tom, jak může zlepšit výkon. 

![Azure doporučení](./media/app-insights-app-map/06.png)

Kliknutím na libovolnou ikonu zobrazíte další podrobnosti:

![Azure doporučení](./media/app-insights-app-map/07.png)

## <a name="diagnostic-click-through"></a>Diagnostické klikněte na tlačítko prostřednictvím
Každý z uzlů na mapě nabízí cílové kliknutím prostřednictvím pro diagnostiku. Možnosti se liší v závislosti na typu uzlu.

![Možnosti serveru](./media/app-insights-app-map/09.png)

Mezi možnosti pro součásti, které jsou hostované v Azure, patří přímé odkazy na ně.

## <a name="filters-and-time-range"></a>Filtry a časový rozsah
Ve výchozím nastavení mapy souhrn všech dat k dispozici pro vybrané časové rozmezí. Ale můžete filtrovat tak, aby obsahovala pouze konkrétní operaci názvy nebo závislosti.

* Název operace: Jedná se o zobrazení stránky a typy požadavků na straně serveru. Pomocí této možnosti mapa znázorňuje klíčového ukazatele výkonu na uzlu serveru nebo klientské pro pouze vybrané operace. Zobrazuje závislosti volat v rámci těchto konkrétních operací.
* Název základní závislosti: Jedná se o AJAX prohlížeče závislosti a závislosti na straně serveru. Pokud sestavu vlastní závislosti telemetrie s rozhraním API TrackDependency zároveň jsou zde. Můžete vybrat závislosti zobrazit na mapě. Tento výběr aktuálně nefiltruje žádosti na straně serveru nebo zobrazení stránky na straně klienta.

![Nastavení filtrů](./media/app-insights-app-map/11.png)

## <a name="save-filters"></a>Uložit filtry
Pokud chcete uložit filtry, které jste použili, filtrované zobrazení na připnout [řídicí panel](app-insights-dashboards.md).

![Připnutí na řídicí panel](./media/app-insights-app-map/12.png)

## <a name="error-pane"></a>Podokno chyby
Při kliknutí na uzel v mapě, zobrazí se podokno k chybě na pravé straně shrnutí selhání pro tento uzel. Chyby jsou nejprve seskupené podle ID operace a potom seskupené podle ID problému.

![Podokno chyby](./media/app-insights-app-map/error-pane.png)

Kliknutím na selhání přejdete k nejnovější instanci tohoto selhání.

## <a name="resource-health"></a>Stav prostředků
Pro některé typy prostředků v horní části podokna chyba se zobrazí stav prostředku. Například kliknutím na uzel SQL se zobrazí stav databáze a všechny výstrahy, které mají aktivováno.

![Stav prostředků](./media/app-insights-app-map/resource-health.png)

Můžete kliknout na název prostředku zobrazíte standardní přehled metriky pro tento prostředek.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player] 

## <a name="feedback"></a>Váš názor
Zadejte prosím zpětnou vazbu prostřednictvím možnosti portálu zpětné vazby.

![Obrázek MapLink-1](./media/app-insights-app-map/13.png)


## <a name="next-steps"></a>Další postup

* [Azure Portal](https://portal.azure.com)
