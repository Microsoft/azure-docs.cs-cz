---
title: Řídicí panely a navigace ve službě Azure Application Insights | Microsoft Docs
description: Vytvořte zobrazení klíče APM grafy a dotazů.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 39b0701b-2fec-4683-842a-8a19424f67bd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: 72859f68fc1bb76a6c71bbd7b98cd713f1f0fe02
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296396"
---
# <a name="navigation-and-dashboards-in-the-application-insights-portal"></a>Navigační a řídicích panelů v portálu služby Application Insights
Až budete mít [nastavte Application Insights na projektu](app-insights-overview.md), telemetrická data o výkonu a využití vaší aplikace se zobrazí v projektu na prostředek Application Insights [portál Azure](https://portal.azure.com).

## <a name="find-your-telemetry"></a>Najít telemetrii
Přihlaste se k [portál Azure](https://portal.azure.com) a přejděte do prostředku Application Insights, kterou jste vytvořili pro vaši aplikaci.

![Klikněte na tlačítko Procházet, vyberte Application Insights a pak vaší aplikace.](./media/app-insights-dashboards/00-start.png)

V okně Přehled (stránky) pro vaši aplikaci se zobrazí souhrn klíčových diagnostiky metrik vaší aplikace a je brány do dalších funkcí portálu.

![Hlavní trasy k zobrazení telemetrie](./media/app-insights-dashboards/010-oview.png)

Můžete přizpůsobit kterékoli z grafy a mřížky a jejich Připnutí na řídicí panel. Tímto způsobem můžete zahrnout společně klíče telemetrii z různých aplikací Centrální řídicí panel.

## <a name="dashboards"></a>Řídicí panely
První věc, se zobrazí po přihlášení k [portálu Microsoft Azure](https://portal.azure.com) je řídicí panel. Zde můžete najednou přenést grafů, které jsou pro vás nejdůležitější mezi všechny vaše prostředky Azure, včetně telemetrie z [Azure Application Insights](app-insights-overview.md).

![Přizpůsobený řídicí panel.](./media/app-insights-dashboards/31.png)

1. **Přejděte ke konkrétním prostředkům** například vaše aplikace ve službě Application Insights: použijte na levém panelu.
2. **Vrátit na aktuální řídicí panel**, nebo přepnout do jiných poslední zobrazení: použijte rozevírací nabídce nahoře vlevo.
3. **Přepínač řídicí panely**: použijte rozevírací nabídky na název řídicího panelu
4. **Vytvářet, upravovat a sdílet řídicí panely** na řídicím panelu nástrojů.
5. **Upravit řídicí panel**: najeďte na dlaždici a potom pomocí jeho horním panelu přesunout, přizpůsobení nebo ji odeberte.

## <a name="add-to-a-dashboard"></a>Přidat na řídicí panel
Když se díváte na okno nebo sadu grafů, který je zajímavé, budete moct připnout kopie ho na řídicí panel. Se zobrazí při příštím vrátíte existuje.

![Připnete graf, pozastavte ukazatel myši nad ním a pak klikněte na tlačítko "..." v záhlaví.](./media/app-insights-dashboards/33.png)

1. PIN kód graf na řídicí panel. Kopie grafu se zobrazí na řídicím panelu.
2. Připnout celé okno na řídicí panel – zobrazí se na řídicím panelu jako dlaždici, prostřednictvím které po kliknutí.
3. Klikněte na tlačítko levého horního rohu se vraťte do aktuálního řídicího panelu. Pak můžete použít rozevírací nabídky se vrátit do aktuální zobrazení.

Všimněte si, že grafy jsou seskupené do dlaždice: dlaždici může obsahovat více než jeden graf. Připnete celou dlaždice na řídicím panelu.

Graf se automaticky aktualizují s frekvencí, která závisí na grafu časové rozmezí:

* Čas v rozsahu až 1 hodina: aktualizace každých 5 minut
* Čas rozsah 1 – 24 hodin: aktualizace každých 15 minut
* Čas rozsah vyšší než 24 hodin: (čas rozsah) / 60.

### <a name="pin-any-query-in-analytics"></a>Připnout jakýkoli dotaz ve Analytics
Můžete také [připnout Analytics](app-insights-analytics-using.md#pin-to-dashboard) grafy k [sdílené](#share-dashboards-with-your-team) řídicího panelu. To umožňuje přidat grafy jakýkoli libovolný dotaz spolu s standardní metriky. 

Výsledky jsou přepočítána automaticky každou hodinu. Klikněte na ikonu aktualizace na graf přepočítat okamžitě. (Obnovit v prohlížeči nezměněný.)

## <a name="adjust-a-tile-on-the-dashboard"></a>Upravit na dlaždici na řídicím panelu
Jakmile dlaždici na řídicím panelu, můžete ji upravit.

![Najeďte myší na graf ji Pokud chcete upravit.](./media/app-insights-dashboards/36.png)

1. Přidáte graf dlaždici.
2. Nastavte metriku, dimenze Seskupit podle a stylu grafu (tabulky, grafu).
3. Přetáhněte v diagramu na přiblížit; Klikněte na tlačítko Zpět a obnovit časový interval; nastavit vlastnosti filtru pro grafy na dlaždici.
4. Nastavte název dlaždice.

Dlaždice připnuté z okna Průzkumníka metriky mají další možnosti úprav než dlaždice připnuté z okno Přehled.

Původní dlaždici, která jste připnuli nemá vliv provedené úpravy.

## <a name="switch-between-dashboards"></a>Přepínání mezi řídicí panely
Můžete uložit více než jeden řídicí panel a mezi nimi přepínat. Pokud připnete graf nebo okna, přidají se do aktuálního řídicího panelu.

![Přepnout mezi řídicí panely, klikněte na řídicí panel a vyberte uložené řídicí panel. Vytvořte a uložte nový řídicí panel, klikněte na tlačítko Nový. Chcete-li změnit, klikněte na tlačítko Upravit.](./media/app-insights-dashboards/32.png)

Například můžete mít jeden řídicí panel pro zobrazení celé obrazovky v týmové místnosti a druhý pro obecné vývoj.

Na řídicím panelu, okno se zobrazí jako dlaždici: klikněte na něj přejdete do okna. Graf replikuje grafu v původním umístění.

![Klikněte na dlaždici otevřete okno, který představuje](./media/app-insights-dashboards/35.png)

## <a name="share-dashboards"></a>Sdílet řídicí panely
Pokud jste vytvořili řídicí panel, můžete ji sdílet s ostatními uživateli.

![V záhlaví řídicí panel klikněte na sdílenou složku](./media/app-insights-dashboards/41.png)

Další informace o [role a řízení přístupu](app-insights-resources-roles-access-control.md).

## <a name="create-dashboards-programmatically"></a>Vytvořit řídicí panely prostřednictvím kódu programu
Můžete automatizovat pomocí řídicího panelu vytvoření [Azure Resource Manager](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards-create-programmatically) a jednoduchý editor JSON.

## <a name="app-navigation"></a>Navigace aplikace
V okně Přehled je bránu a další informace o vaší aplikaci.

* **Všechny graf nebo dlaždice** – kliknutím na libovolnou dlaždici nebo grafu pro zobrazení dalších podrobností o co se zobrazí.

### <a name="overview-blade-buttons"></a>Přehled okno tlačítka
![Přehled okno horního navigačního panelu](./media/app-insights-dashboards/app-overview-top-nav.png)

* [**Průzkumníku metrik** ](app-insights-metrics-explorer.md) -vytvářet vlastní grafy výkonu a využití.
* [**Hledání** ](app-insights-diagnostic-search.md) – prozkoumat konkrétní instanci události, jako jsou žádosti o, výjimky, nebo protokolu trasování.
* [**Analýza** ](app-insights-analytics.md) -výkonných dotazů přes telemetrie.
* **Čas rozsah** -upravit rozsah zobrazuje všechny grafy v okně.
* **Odstranit** – prostředek Application Insights pro tuto aplikaci odstranit. Si také odebrat balíčky Application Insights z kódu aplikace, nebo upravit [klíč instrumentace](app-insights-create-new-resource.md#copy-the-instrumentation-key) ve vaší aplikaci směrovat telemetrii na jiný prostředek Application Insights.

### <a name="essentials-tab"></a>Karta Essentials
* [Klíč instrumentace](app-insights-create-new-resource.md#copy-the-instrumentation-key) -identifikuje tento prostředek aplikace.

### <a name="app-navigation-bar"></a>Navigační panel aplikace
![Levý navigační panel](./media/app-insights-dashboards/app-left-nav-bar.png)

* **Přehled** -vraťte do okna Přehled aplikace.
* **Protokol aktivit** -výstrahy a událostí Azure pro správu.
* [**Řízení přístupu** ](app-insights-resources-roles-access-control.md) -poskytnutí přístupu k členům týmu a další.
* [**Značky** ](../azure-resource-manager/resource-group-using-tags.md) -použití značek ke skupině vaší aplikace s ostatními.

PROZKOUMAT

* [**Mapa aplikace** ](app-insights-app-map.md) -Active mapě a ukáže jednotlivých součástí aplikace, odvozené z informací o závislostech.
* [**Inteligentní detekce** ](app-insights-proactive-diagnostics.md) – přečtěte si poslední výstrahy výkonu.
* [**Živý datový proud** ](app-insights-live-stream.md) – A fixed sadu téměř rychlých metriky, užitečné při nasazování nového sestavení nebo ladění.
* [**Dostupnost / webové testy** ](app-insights-monitor-web-app-availability.md) -odesílání pravidelných požadavků na vaši webovou aplikaci z kolem world.*
* [**Selhání, výkon** ](app-insights-web-monitor-performance.md) -výjimky, selhání sazby a dobu odezvy pro požadavky na aplikace a požadavky z vaší aplikace [závislosti](app-insights-asp-net-dependencies.md).
* [**Výkon** ](app-insights-web-monitor-performance.md) -doba odezvy, závislosti odezvy.
* [Servery](app-insights-web-monitor-performance.md) -čítače výkonu. K dispozici, pokud jste [nainstalujte monitorování stavu](app-insights-monitor-performance-live-website-now.md).
* **Prohlížeč** -stránka zobrazení a výkon AJAX. K dispozici, pokud jste [instrumentace webových stránek](app-insights-javascript.md).
* **Využití** -stránka zobrazení, uživatele a počty relací. K dispozici, pokud jste [instrumentace webových stránek](app-insights-javascript.md).

KONFIGUROVAT

* **Začínáme** -vložené kurzu.
* **Vlastnosti** -klíč instrumentace, předplatné a id prostředku.
* [Výstrahy](app-insights-alerts.md) -metriky konfigurace výstrahy.
* [Průběžné export](app-insights-export-telemetry.md) -konfigurace export telemetrie do úložiště Azure.
* [Testování výkonu](app-insights-monitor-web-app-availability.md#performance-tests) -nastavit syntetické zatížení na vašem webu.
* [Kvóty a ceny](app-insights-pricing.md) a [přijímání vzorkování](app-insights-sampling.md).
* **Přístup pomocí rozhraní API** -vytvořit [verze poznámek](app-insights-annotations.md) a pro rozhraní API služby Data Access.
* [**Pracovní položky** ](app-insights-diagnostic-search.md#create-work-item) – připojení k pracovním sledování systému, takže můžete vytvořit chyby při kontrole telemetrie.

NASTAVENÍ

* [**Zamkne** ](../azure-resource-manager/resource-group-lock-resources.md) -zamknutí prostředků Azure
* [**Skriptu pro automatizaci** ](app-insights-powershell.md) -export definice prostředku Azure tak, aby vám ho použít jako šablonu pro vytvoření nových prostředků.


## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Další postup

|  |  |
| --- | --- |
| [Průzkumníku metrik](app-insights-metrics-explorer.md)<br/>Filtr a segment metriky |![Příklad hledání](./media/app-insights-dashboards/64.png) |
| [Diagnostické vyhledávání](app-insights-diagnostic-search.md)<br/>Najít a kontrola události, související události a vytvořit chyby |![Příklad hledání](./media/app-insights-dashboards/61.png) |
| [Analýzy](app-insights-analytics.md)<br/>Účinný dotazovací jazyk |![Příklad hledání](./media/app-insights-dashboards/63.png) |
