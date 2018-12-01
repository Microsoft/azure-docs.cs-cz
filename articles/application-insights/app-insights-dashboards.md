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
ms.openlocfilehash: 904727c09fec1cd9cf1093f2681ba170c84f4fab
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52723104"
---
# <a name="navigation-and-dashboards-in-the-application-insights-portal"></a>Navigace a řídicí panely na portálu Application Insights
Až budete mít [nastavení Application Insights v projektu](app-insights-overview.md), telemetrická data o výkonu a využití vaší aplikace se zobrazí v projektu prostředek Application Insights [webu Azure portal](https://portal.azure.com).

## <a name="find-your-telemetry"></a>Najít telemetrii
Přihlaste se k [webu Azure portal](https://portal.azure.com) a přejděte do prostředku Application Insights, který jste vytvořili pro vaši aplikaci.

![Klikněte na tlačítko Procházet, vyberte Application Insights, vaše aplikace.](./media/app-insights-dashboards/00-start.png)

Okno přehledu (stránky) pro vaše aplikace zobrazuje souhrn klíčových diagnostických metrik vaší aplikace a představuje bránu k funkcím portálu.

![Hlavní trasy a zobrazení telemetrických dat](./media/app-insights-dashboards/010-oview.png)

Můžete přizpůsobit kteroukoliv z grafy a mřížky a připnout na řídicí panel. Tímto způsobem můžete mít pohromadě klíčové telemetrická data z různých aplikací na centrálním řídicím panelu.

## <a name="dashboards"></a>Řídicí panely
První věc, kterou se po přihlášení k [portálu Microsoft Azure](https://portal.azure.com) je řídicí panel. Tady můžete kombinovat grafy, které jsou pro vás nejdůležitější ve všech vašich prostředků Azure, včetně telemetrie z [Azure Application Insights](app-insights-overview.md).

![Přizpůsobeného řídicího panelu.](./media/app-insights-dashboards/31.png)

1. **Přejděte ke konkrétním prostředkům** jako je například vaše aplikace ve službě Application Insights: použít na levém panelu.
2. **Vraťte se do aktuálního řídicího panelu**, nebo přepněte na jiné poslední zobrazení: pomocí rozevírací nabídky vlevo nahoře.
3. **Přepnutí řídicím panelům**: použijte rozevírací nabídku na název řídicího panelu
4. **Vytvářet, upravovat a sdílet řídicí panely** na řídicí panel nástrojů.
5. **Upravte řídicí panel**: najeďte myší na dlaždici a potom pomocí jeho horní panel přesunout, přizpůsobit nebo ho neodeberete.

## <a name="add-to-a-dashboard"></a>Přidat na řídicí panel
Když se podíváte na okno nebo sadu grafy, který je zvlášť zajímavý, můžete jeho kopii na řídicí panel připnout. Zobrazí se vám to při příštím vrácení existuje.

![Pro Připnutí grafu, najeďte myší nad ním a potom klikněte na tlačítko "..." v záhlaví.](./media/app-insights-dashboards/33.png)

1. Připnout graf na řídicí panel. Kopie grafu se zobrazí na řídicím panelu.
2. Připnutí celé okno na řídicí panel – zobrazí se na řídicí panel jako dlaždici, která se můžete proklikat.
3. Klikněte na tlačítko levého horního rohu se vrátíte k aktuálnímu řídicímu panelu. Pak můžete použít rozevírací nabídky se vrátíte do aktuální zobrazení.

Všimněte si, že grafy jsou seskupené do dlaždice: dlaždice může obsahovat více než jeden graf. Můžete připnout celou dlaždici na řídicí panel.

Grafu se automaticky aktualizují s frekvencí, která závisí na grafu časový rozsah:

* Časový rozsah až 1 hodinu: aktualizovat každých 5 minut
* Časový rozsah 1 – 24 hodin: aktualizovat každých 15 minut
* Časový rozsah vyšší než 24 hodin: (časový rozsah) / 60.

### <a name="pin-any-query-in-analytics"></a>Připnout libovolný dotaz v Analytics
Můžete také [připnout Analytics](../log-analytics/query-language/get-started-analytics-portal.md) grafů k [sdílené](#share-dashboards-with-your-team) řídicího panelu. To umožňuje přidávat diagramy z jakéhokoli libovolného dotazu vedle standardních metrik. 

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

Další informace o [role a řízení přístupu](app-insights-resources-roles-access-control.md).

## <a name="create-dashboards-programmatically"></a>Vytváření řídicích panelů prostřednictvím kódu programu
Můžete automatizovat pomocí vytvoření řídicího panelu [Azure Resource Manageru](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards-create-programmatically) a jednoduchý editor JSON.

## <a name="app-navigation"></a>Navigační aplikace
Okno přehledu je brána na další informace o vaší aplikaci.

* **Všechny dlaždice nebo grafu** – kliknutím na kteroukoli dlaždici nebo graf zobrazíte další podrobnosti o co se zobrazí.

### <a name="overview-blade-buttons"></a>Tlačítka okna přehledu
![Přehled okna horním navigačním panelu](./media/app-insights-dashboards/app-overview-top-nav.png)

* [**Průzkumník metrik** ](app-insights-metrics-explorer.md) -vytvářet vlastní grafy výkonu a využití.
* [**Hledání** ](app-insights-diagnostic-search.md) – prozkoumat konkrétní instance události, například požadavky, výjimky, nebo trasování protokolů.
* [**Analytics** ](app-insights-analytics.md) -zadávat efektivní dotazy na telemetrie.
* **Časový rozsah** – upravit rozsah zobrazí všechny grafy v okně.
* **Odstranit** – odstranit prostředek Application Insights pro tuto aplikaci. Také buď odstranit balíčky Application Insights z kódu vaší aplikace, nebo upravit [Instrumentační klíč](app-insights-create-new-resource.md#copy-the-instrumentation-key) ve vaší aplikaci na přímé telemetrických dat do jiného prostředku Application Insights.

### <a name="essentials-tab"></a>Na kartě
* [Instrumentační klíč](app-insights-create-new-resource.md#copy-the-instrumentation-key) -identifikuje tento prostředek aplikace.

### <a name="app-navigation-bar"></a>Navigační panel aplikace
![Levý navigační panel](./media/app-insights-dashboards/app-left-nav-bar.png)

* **Přehled** -vrátit na kartě s přehledem aplikace.
* **Protokol aktivit** – výstrahy a události pro správu Azure.
* [**Řízení přístupu** ](app-insights-resources-roles-access-control.md) – poskytnutí přístupu pro členy týmu a ostatní uživatele.
* [**Značky** ](../azure-resource-manager/resource-group-using-tags.md) – používání značek k skupiny vaší aplikace s jinými uživateli.

PROZKOUMAT

* [**Mapa aplikace** ](app-insights-app-map.md) – aktivní mapa zobrazující komponent vaší aplikace, odvozený z informací o závislostech.
* [**Inteligentní zjišťování** ](app-insights-proactive-diagnostics.md) – zkontrolujte nedávné výstrahy výkonu.
* [**Live Stream** ](app-insights-live-stream.md) – A nelze upravovat nastavení téměř okamžité metriky, užitečné při nasazování nového sestavení nebo ladění.
* [**Dostupnost / webové testy** ](app-insights-monitor-web-app-availability.md) -odesílání pravidelných požadavků pro vaše webové aplikace po celém world.*
* [**Výkon, selhání** ](app-insights-web-monitor-performance.md) – výjimky, chybovost a doby odezvy pro požadavky vaší aplikace a požadavky z vaší aplikace do [závislosti](app-insights-asp-net-dependencies.md).
* [**Výkon** ](app-insights-web-monitor-performance.md) – doba odezvy, závislosti, doby odezvy.
* [Servery](app-insights-web-monitor-performance.md) – čítače výkonu. K dispozici po vás [nainstalujte monitorování stavu](app-insights-monitor-performance-live-website-now.md).
* **Prohlížeč** – stránka zobrazení a výkonu aplikace AJAX. K dispozici po vás [instrumentace webových stránek](app-insights-javascript.md).
* **Využití** -stránce počty zobrazení, uživatelů a relací. K dispozici po vás [instrumentace webových stránek](app-insights-javascript.md).

KONFIGUROVAT

* **Začínáme se službou** – kurz vložené.
* **Vlastnosti** -Instrumentační klíč, předplatné a id prostředku.
* [Výstrahy](app-insights-alerts.md) – konfigurace metriky upozornění.
* [Průběžný export](app-insights-export-telemetry.md) -nakonfigurovat export telemetrických dat do služby Azure storage.
* [Testování výkonu](app-insights-monitor-web-app-availability.md#performance-tests) -nastavit syntetické zatížení na vašem webu.
* [Kvóta a ceny](app-insights-pricing.md) a [vzorkování příjmu](app-insights-sampling.md).
* **Přístup přes rozhraní API** – vytvořit [poznámky k verzi](app-insights-annotations.md) a pro rozhraní API služby Data Access.
* [**Pracovní položky** ](app-insights-diagnostic-search.md#create-work-item) – připojit se k práci, systém sledování, takže můžete vytvořit chyby při kontrole telemetrická data.

NASTAVENÍ

* [**Zamkne** ](../azure-resource-manager/resource-group-lock-resources.md) -uzamčení prostředků Azure
* [**Automatizační skript** ](app-insights-powershell.md) -export definice prostředků Azure, tak, aby vám ho použít jako šablonu k vytvoření nových prostředků.


## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Další postup

|  |  |
| --- | --- |
| [Průzkumník metrik](app-insights-metrics-explorer.md)<br/>Filtrujte a segmentujte metriky |![Příklad prohledávání](./media/app-insights-dashboards/64.png) |
| [Diagnostické vyhledávání](app-insights-diagnostic-search.md)<br/>Najít a zkoumat události, související události a vytvářet chyby |![Příklad prohledávání](./media/app-insights-dashboards/61.png) |
| [Analýzy](app-insights-analytics.md)<br/>Výkonný dotazovací jazyk |![Příklad prohledávání](./media/app-insights-dashboards/63.png) |
