---
title: Řídicí panely a navigace ve službě Azure Application Insights | Dokumentace Microsoftu
description: Vytvořte zobrazení klíčové grafy APM a dotazů.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 39b0701b-2fec-4683-842a-8a19424f67bd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: 1b0c991c09d1235215dc9b930c529a219d492a8c
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54117073"
---
# <a name="navigation-and-dashboards-in-the-application-insights-portal"></a>Navigace a řídicí panely na portálu Application Insights
Až budete mít [nastavení Application Insights v projektu](../../azure-monitor/app/app-insights-overview.md), telemetrická data o výkonu a využití vaší aplikace se zobrazí v projektu prostředek Application Insights [webu Azure portal](https://portal.azure.com).

## <a name="find-your-telemetry"></a>Najít telemetrii
Přihlaste se k [webu Azure portal](https://portal.azure.com) a přejděte do prostředku Application Insights, který jste vytvořili pro vaši aplikaci.

![Klikněte na tlačítko Procházet, vyberte Application Insights, vaše aplikace.](./media/app-insights-dashboards/00-start.png)

Okno přehledu (stránky) pro vaše aplikace zobrazuje souhrn klíčových diagnostických metrik vaší aplikace a představuje bránu k funkcím portálu.

![Hlavní trasy a zobrazení telemetrických dat](./media/app-insights-dashboards/010-oview.png)

Můžete přizpůsobit kteroukoliv z grafy a mřížky a připnout na řídicí panel. Tímto způsobem můžete mít pohromadě klíčové telemetrická data z různých aplikací na centrálním řídicím panelu.

## <a name="dashboards"></a>Řídicí panely
První věc, kterou se po přihlášení k [portálu Microsoft Azure](https://portal.azure.com) je řídicí panel. Tady můžete kombinovat grafy, které jsou pro vás nejdůležitější ve všech vašich prostředků Azure, včetně telemetrie z [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md).

![Přizpůsobeného řídicího panelu.](./media/app-insights-dashboards/31.png)

1. **Přejděte ke konkrétním prostředkům** jako je například vaše aplikace ve službě Application Insights: Použijte na levém panelu.
2. **Vraťte se do aktuálního řídicího panelu**, nebo přepněte na jiné poslední zobrazení: Pomocí rozevírací nabídky vlevo nahoře.
3. **Přepnutí řídicím panelům**: Pomocí rozevírací nabídky na název řídicího panelu
4. **Vytvářet, upravovat a sdílet řídicí panely** na řídicí panel nástrojů.
5. **Upravte řídicí panel**: Najeďte myší na dlaždici a potom pomocí jeho horní panel přesunout, přizpůsobit nebo ho neodeberete.

## <a name="add-to-a-dashboard"></a>Přidat na řídicí panel
Když se podíváte na okno nebo sadu grafy, který je zvlášť zajímavý, můžete jeho kopii na řídicí panel připnout. Zobrazí se vám to při příštím vrácení existuje.

![Pro Připnutí grafu, najeďte myší nad ním a potom klikněte na tlačítko "..." v záhlaví.](./media/app-insights-dashboards/33.png)

1. Připnout graf na řídicí panel. Kopie grafu se zobrazí na řídicím panelu.
2. Připnutí celé okno na řídicí panel – zobrazí se na řídicí panel jako dlaždici, která se můžete proklikat.
3. Klikněte na tlačítko levého horního rohu se vrátíte k aktuálnímu řídicímu panelu. Pak můžete použít rozevírací nabídky se vrátíte do aktuální zobrazení.

Všimněte si, že grafy jsou seskupené do dlaždice: dlaždice může obsahovat více než jeden graf. Můžete připnout celou dlaždici na řídicí panel.

Grafu se automaticky aktualizují s frekvencí, která závisí na grafu časový rozsah:

* Časový rozsah nahoru na 1 hodinu: Aktualizovat každých 5 minut
* Časový rozsah 1 – 24 hodin: Aktualizovat každých 15 minut
* Časový rozsah vyšší než 24 hodin: (Časový rozsah) / 60.

### <a name="pin-any-query-in-analytics"></a>Připnout libovolný dotaz v Analytics
Můžete také [připnout Analytics](../../azure-monitor/log-query/get-started-portal.md) grafů k [sdílené](#share-dashboards-with-your-team) řídicího panelu. To umožňuje přidávat diagramy z jakéhokoli libovolného dotazu vedle standardních metrik. 

Výsledky se automaticky přepočítají, každou hodinu. Klikněte na ikonu aktualizace v grafu tak, aby přepočítat okamžitě. (Obnovit v prohlížeči nezměněný.)

## <a name="adjust-a-tile-on-the-dashboard"></a>Úprava dlaždice na řídicím panelu
Jakmile se dlaždice na řídicím panelu, můžete jej přizpůsobit.

![Najeďte myší na graf, abyste ji mohli editovat.](./media/app-insights-dashboards/36.png)

1. Přidáte graf na dlaždici.
2. Nastavte metriku, Seskupit podle dimenzí a stylu grafu (tabulka, graf).
3. Proveďte operaci přetažení přes diagram přiblížit; Klikněte na tlačítko Zpět a obnovit časový interval; nastavit vlastnosti filtru pro grafy na dlaždici.
4. Nastavení názvu dlaždice.

Dlaždice připnuté z okna Průzkumník metrik mít víc úpravy možností než dlaždice připnuté z s přehledem.

Úpravy nemá vliv na původní dlaždici, která jste připnuli.

## <a name="switch-between-dashboards"></a>Přepnout mezi řídicími panely
Můžete uložit více než jeden řídicí panel a mezi nimi přepínat. Když připnete graf nebo okno, se přidají do aktuálního řídicího panelu.

![Můžete přepínat mezi řídicími panely, klikněte na řídicí panel a uložené řídicí panel. Pokud chcete vytvořit a uložit nový řídicí panel, klikněte na nový. Chcete-li uspořádat, klikněte na Upravit.](./media/app-insights-dashboards/32.png)

Například může mít jeden řídicí panel pro zobrazení zobrazení na celé obrazovce v týmové místnosti a druhý pro obecný vývoj.

Na řídicím panelu, okno se zobrazí jako dlaždice: klikněte na tlačítko, přejdete do okna. Graf replikuje grafu v původním umístění.

![Kliknutím na dlaždici, otevře se okno, které představuje](./media/app-insights-dashboards/35.png)

## <a name="share-dashboards"></a>Sdílení řídicích panelů
Když jsme vytvořili řídicí panel, můžete ho sdílet s ostatními uživateli.

![V záhlaví řídicího panelu klikněte na sdílenou složku](./media/app-insights-dashboards/41.png)

Další informace o [role a řízení přístupu](../../azure-monitor/app/resources-roles-access-control.md).

## <a name="create-dashboards-programmatically"></a>Vytváření řídicích panelů prostřednictvím kódu programu
Můžete automatizovat pomocí vytvoření řídicího panelu [Azure Resource Manageru](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards-create-programmatically) a jednoduchý editor JSON.

## <a name="app-navigation"></a>Navigační aplikace
Okno přehledu je brána na další informace o vaší aplikaci.

* **Všechny dlaždice nebo grafu** – kliknutím na kteroukoli dlaždici nebo graf zobrazíte další podrobnosti o co se zobrazí.

### <a name="overview-blade-buttons"></a>Tlačítka okna přehledu
![Přehled okna horním navigačním panelu](./media/app-insights-dashboards/app-overview-top-nav.png)

* [**Průzkumník metrik** ](../../azure-monitor/app/metrics-explorer.md) -vytvářet vlastní grafy výkonu a využití.
* [**Hledání** ](../../azure-monitor/app/diagnostic-search.md) – prozkoumat konkrétní instance události, například požadavky, výjimky, nebo trasování protokolů.
* [**Analytics** ](../../azure-monitor/app/analytics.md) -zadávat efektivní dotazy na telemetrie.
* **Časový rozsah** – upravit rozsah zobrazí všechny grafy v okně.
* **Odstranit** – odstranit prostředek Application Insights pro tuto aplikaci. Také buď odstranit balíčky Application Insights z kódu vaší aplikace, nebo upravit [Instrumentační klíč](../../azure-monitor/app/create-new-resource.md #copy-the-instrumentation-key) ve vaší aplikaci na přímé telemetrických dat do jiného prostředku Application Insights.

### <a name="essentials-tab"></a>Na kartě
* [Instrumentační klíč](../../azure-monitor/app/create-new-resource.md #copy-the-instrumentation-key) -identifikuje tento prostředek aplikace.

### <a name="app-navigation-bar"></a>Navigační panel aplikace
![Levý navigační panel](./media/app-insights-dashboards/app-left-nav-bar.png)

* **Přehled** -vrátit na kartě s přehledem aplikace.
* **Protokol aktivit** – výstrahy a události pro správu Azure.
* [**Řízení přístupu** ](../../azure-monitor/app/resources-roles-access-control.md) – poskytnutí přístupu pro členy týmu a ostatní uživatele.
* [**Značky** ](../../azure-resource-manager/resource-group-using-tags.md) – používání značek k skupiny vaší aplikace s jinými uživateli.

PROZKOUMAT

* [**Mapa aplikace** ](app-map.md) – aktivní mapa zobrazující komponent vaší aplikace, odvozený z informací o závislostech.
* [**Inteligentní zjišťování** ](../../azure-monitor/app/proactive-diagnostics.md) – zkontrolujte nedávné výstrahy výkonu.
* [**Live Stream** ](../../azure-monitor/app/live-stream.md) – A nelze upravovat nastavení téměř okamžité metriky, užitečné při nasazování nového sestavení nebo ladění.
* [**Dostupnost / webové testy** ](../../azure-monitor/app/monitor-web-app-availability.md) -odesílání pravidelných požadavků pro vaše webové aplikace po celém world.*
* [**Výkon, selhání** ](../../azure-monitor/app/web-monitor-performance.md) – výjimky, chybovost a doby odezvy pro požadavky vaší aplikace a požadavky z vaší aplikace do [závislosti](../../azure-monitor/app/asp-net-dependencies.md).
* [**Výkon** ](../../azure-monitor/app/web-monitor-performance.md) – doba odezvy, závislosti, doby odezvy.
* [Servery](../../azure-monitor/app/web-monitor-performance.md) – čítače výkonu. K dispozici po vás [nainstalujte monitorování stavu](../../azure-monitor/app/monitor-performance-live-website-now.md).
* **Prohlížeč** – stránka zobrazení a výkonu aplikace AJAX. K dispozici po vás [instrumentace webových stránek](../../azure-monitor/app/javascript.md).
* **Využití** -stránce počty zobrazení, uživatelů a relací. K dispozici po vás [instrumentace webových stránek](../../azure-monitor/app/javascript.md).

KONFIGUROVAT

* **Začínáme se službou** – kurz vložené.
* **Vlastnosti** -Instrumentační klíč, předplatné a id prostředku.
* [Výstrahy](../../azure-monitor/app/alerts.md) – konfigurace metriky upozornění.
* [Průběžný export](../../azure-monitor/app/export-telemetry.md) -nakonfigurovat export telemetrických dat do služby Azure storage.
* [Testování výkonu](../../azure-monitor/app/monitor-web-app-availability.md#performance-tests) -nastavit syntetické zatížení na vašem webu.
* [Kvóta a ceny](../../azure-monitor/app/pricing.md) a [vzorkování příjmu](../../azure-monitor/app/sampling.md).
* **Přístup přes rozhraní API** – vytvořit [poznámky k verzi](annotations.md) a pro rozhraní API služby Data Access.
* [**Pracovní položky** ](../../azure-monitor/app/diagnostic-search.md#create-work-item) – připojit se k práci, systém sledování, takže můžete vytvořit chyby při kontrole telemetrická data.

NASTAVENÍ

* [**Zamkne** ](../../azure-resource-manager/resource-group-lock-resources.md) -uzamčení prostředků Azure
* [**Automatizační skript** ](../../azure-monitor/app/powershell.md) -export definice prostředků Azure, tak, aby vám ho použít jako šablonu k vytvoření nových prostředků.


## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Další postup

|  |  |
| --- | --- |
| [Průzkumník metrik](../../azure-monitor/app/metrics-explorer.md)<br/>Filtrujte a segmentujte metriky |![Příklad prohledávání](./media/app-insights-dashboards/64.png) |
| [Diagnostické vyhledávání](../../azure-monitor/app/diagnostic-search.md)<br/>Najít a zkoumat události, související události a vytvářet chyby |![Příklad prohledávání](./media/app-insights-dashboards/61.png) |
| [Analýzy](../../azure-monitor/app/analytics.md)<br/>Výkonný dotazovací jazyk |![Příklad prohledávání](./media/app-insights-dashboards/63.png) |
