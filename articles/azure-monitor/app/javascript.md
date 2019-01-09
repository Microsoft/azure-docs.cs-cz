---
title: Azure Application Insights pro webové aplikace JavaScript | Dokumentace Microsoftu
description: Načtení zobrazení stránek a počty relací, data webového klienta a sledování vzorů využití. Zjištění výjimek a problémů s výkonem na webových stránkách v jazyce JavaScript.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b710d09-6ab4-4004-b26a-4fa840039500
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: 952dd97a06718d0c29f9c6f5abc79da592e6f3ae
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54117806"
---
# <a name="application-insights-for-web-pages"></a>Application Insights pro webové stránky
Zjistěte informace o výkonu a využití webové stránky nebo aplikace. Když přidáte [Application Insights](../../azure-monitor/app/app-insights-overview.md) do skriptu stránky, získáte časování načtení stránky a volání AJAX, počty a podrobnosti výjimek prohlížeče a selhání AJAX, a také počty uživatelů a relací. Všechny tyto hodnoty mohou být segmentovány podle stránky, klientského operačního systému a verze prohlížeče, zeměpisné polohy a ostatních dimenzí. Můžete nastavit výstrahy na počet selhání nebo pomalé načítání stránky. A vložíte-li do kódu JavaScript trasování volání, můžete sledovat využití různých funkcí aplikace webové stránky.

Application Insights můžete použít s jakýmikoli webovými stránkami – stačí přidat krátký kód jazyka JavaScript. Pokud používáte webovou službu [Java](java-get-started.md) nebo [ASP.NET](../../azure-monitor/app/asp-net.md), můžete integrovat telemetrii ze serveru a klientů.

![Na stránce portal.azure.com otevřete prostředek vaší aplikace a klikněte na Prohlížeč.](./media/javascript/03.png)

Potřebujete předplatné [Microsoft Azure](https://azure.com). Pokud má váš tým předplatné pro společnosti, požádejte vlastníka, aby do něj přidal váš účet Microsoft.

## <a name="set-up-application-insights-for-your-web-page"></a>Nastavte Application Insights pro svou webovou stránku
Na webové stránky přidejte následující fragment kódu zavaděče.

### <a name="open-or-create-application-insights-resource"></a>Otevření nebo vytvoření prostředku Application Insights
Prostředek služby Application Insights je místo, kde se zobrazují data o výkonu a využití vaší stránky. 

Přihlaste se na [portál Azure](https://portal.azure.com).

Pokud jste už nastavili monitorování pro aplikaci na straně serveru, už máte prostředek:

![Zvolte Procházet, služby pro vývojáře, Application Insights.](./media/javascript/01-find.png)

Pokud ji nemáte, vytvořte ji:

![Zvolte Nový, služby pro vývojáře, Application Insights.](./media/javascript/01-create.png)

*Již máte dotazy?* [Další informace o vytvoření prostředku](../../azure-monitor/app/create-new-resource.md ).

### <a name="add-the-sdk-script-to-your-app-or-web-pages"></a>Přidejte skript SDK do aplikace nebo webové stránky

```HTML
<!-- 
To collect user behavior analytics about your application, 
insert the following script into each page you want to track.
Place this code immediately before the closing </head> tag,
and before any other scripts. Your first data will appear 
automatically in just a few seconds.
-->
<script type="text/javascript">
var appInsights=window.appInsights||function(a){
  function b(a){c[a]=function(){var b=arguments;c.queue.push(function(){c[a].apply(c,b)})}}var c={config:a},d=document,e=window;setTimeout(function(){var b=d.createElement("script");b.src=a.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js",d.getElementsByTagName("script")[0].parentNode.appendChild(b)});try{c.cookie=d.cookie}catch(a){}c.queue=[];for(var f=["Event","Exception","Metric","PageView","Trace","Dependency"];f.length;)b("track"+f.pop());if(b("setAuthenticatedUserContext"),b("clearAuthenticatedUserContext"),b("startTrackEvent"),b("stopTrackEvent"),b("startTrackPage"),b("stopTrackPage"),b("flush"),!a.disableExceptionTracking){f="onerror",b("_"+f);var g=e[f];e[f]=function(a,b,d,e,h){var i=g&&g(a,b,d,e,h);return!0!==i&&c["_"+f](a,b,d,e,h),i}}return c
  }({
      instrumentationKey:"<your instrumentation key>"
  });
  
window.appInsights=appInsights,appInsights.queue&&0===appInsights.queue.length&&appInsights.trackPageView();
</script>
```

Vložte skript těsně před značku `</head>` každé stránky, kterou chcete sledovat. Pokud má daný web stránku předlohy, můžete se skript vložit. Příklad:

* Vložíte ho do projektu aplikace ASP.NET MVC do složky `View\Shared\_Layout.cshtml`.
* Na webu služby SharePoint, v Ovládacích panelech, otevřete [Nastavení webu / stránky předlohy](../../azure-monitor/app/sharepoint.md).

Skript obsahuje klíč instrumentace, který nasměruje data pro daný prostředek Application Insights. 

([Hlubší vysvětlení skriptu.](https://apmtips.com/blog/2015/03/18/javascript-snippet-explained/))

## <a name="detailed-configuration"></a>Podrobná konfigurace
Nastavit můžete několik [Parametrů](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config), i když ve většině případů to není třeba. Například můžete zakázat nebo omezit počet volání Ajax hlášených na zobrazení stránky (chcete-li snížit zatížení). Nebo můžete nastavit režim ladění tak, aby rychle přesouval telemetrická data prostřednictvím kanálu bez provedení dávkou.

Chcete-li tyto parametry nastavit, vyhledejte tento řádek ve fragmentu kódu a následně přidejte další položky oddělené čárkami:

    })({
      instrumentationKey: "..."
      // Insert here
    });

[Dostupné parametry](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config) zahrnují:

    // Send telemetry immediately without batching.
    // Remember to remove this when no longer required, as it
    // can affect browser performance.
    enableDebug: boolean,

    // Don't log browser exceptions.
    disableExceptionTracking: boolean,

    // Don't log ajax calls.
    disableAjaxTracking: boolean,

    // Limit number of Ajax calls logged, to reduce traffic.
    maxAjaxCallsPerView: 10, // default is 500

    // Time page load up to execution of first trackPageView().
    overridePageViewDuration: boolean,

    // Set dynamically for an authenticated user.
    accountId: string,

## <a name="run"></a>Spuštění aplikace
Spusťte webovou aplikaci, chvíli ji používejte ke generování telemetrie a počkejte několik sekund. Můžete ji buď spustit pomocí klávesy **F5** na vývojovém počítači, nebo ji publikovat a umožnit uživatelům, aby si ji vyzkoušeli.

Pokud chcete zkontrolovat telemetrii, kterou webová aplikace odesílá do služby Application Insights, použijte ladicí nástroje prohlížeče (**F12** u mnoha prohlížečů). Data budou odeslána na adresu dc.services.visualstudio.com.

## <a name="explore-your-browser-performance-data"></a>Prozkoumejte data výkonu prohlížeče
Otevřete okno prohlížeče zobrazující agregovaná data výkonu z prohlížečů uživatelů.

![Na stránce portal.azure.com otevřete prostředek vaší aplikace a klikněte na tlačítko Nastavení, Prohlížeč](./media/javascript/03.png)

Žádná data? V horní části stránky klikněte na **Obnovit**. Stále nic? Viz [Poradce při potížích](../../azure-monitor/app/troubleshoot-faq.md).

Okno prohlížeče je [okno Průzkumníka metrik](../../azure-monitor/app/metrics-explorer.md) s přednastavenými filtry a výběry grafu. Pokud chcete, můžete upravit časové rozmezí, filtry a konfiguraci grafu a uložit výsledek jako oblíbenou položku. Klikněte na tlačítko **Obnovit výchozí nastavení** a vraťte se zpět do původní konfigurace okna.

## <a name="page-load-performance"></a>Stav zatížení stránky
V horní části naleznete segmentovaný grafu časů načtení stránky. Celková výška grafu představuje průměrný čas načtení a zobrazuje stránky z vaší aplikace v prohlížečích vašich uživatelů. Čas se měří od chvíle, když prohlížeč odesílá počáteční požadavek HTTP, po zpracování veškerých synchronních zatížení, včetně rozložení a spouštění skriptů. Neobsahuje asynchronní úlohy, například načítání webových součástí z volání AJAX.

Tabulka segmentuje celkovou dobu načítání stránky do [standardních časování definovaných pomocí W3C](https://www.w3.org/TR/navigation-timing/#processing-model). 

![](./media/javascript/08-client-split.png)

Všimněte si, že čas *síťového připojení* je často nižší, než by se dalo očekávat, protože je průměrem přes všechny požadavky z prohlížeče na server. Mnoho jednotlivých požadavků obsahuje dobu připojení 0, protože již existuje aktivní připojení k serveru.

### <a name="slow-loading"></a>Pomalé načítání?
Pomalé načítání stránek představuje hlavní zdroj nespokojenosti uživatelů. Pokud tabulka naznačuje pomalé načítání stránky, je snadné provést trochu diagnostického výzkumu.

Graf zobrazuje průměr všech načtení stránky ve vaší aplikaci. Chcete-li zjistit, zda je tento problém omezen na konkrétní stránky, podívejte se níže na okno, kde jsou mřížky segmentované podle adresy URL stránky:

![](./media/javascript/09-page-perf.png)

Všimněte si počtu zobrazení stránky a směrodatné odchylky. Pokud je počet stránek velmi nízký, pak problém uživatele příliš neovlivňuje. Vysoká směrodatná odchylka (srovnatelná se samotným průměrem) označuje velké rozdíly mezi jednotlivými měřeními.

**Přibližte si jednu adresu URL a jednu stránku zobrazení.** Klikněte na libovolný název stránky a zobrazte okno grafů prohlížeče filtrované právě pro tuto adresu URL a pak na instanci zobrazení stránky.

![](./media/javascript/35.png)

Klikněte na tlačítko `...` pro úplný seznam vlastností pro danou událost nebo zkontrolujte volání Ajax a související události. Pomalá volání Ajax ovlivňují celkový čas načítání stránky, pokud jsou synchronní. Související události zahrnují požadavky serveru pro stejnou adresu URL (Pokud jste nastavili Application Insights na webovém serveru).

**Výkon stránky v čase.** Zpět v okně prohlížeče změňte mřížku zobrazení času načítání stránky na spojnicový graf a zjistěte, zda nedocházelo v určitou dobu ke špičkám:

![Klikněte na hlavičku mřížky a vyberte nový typ grafu](./media/javascript/10-page-perf-area.png)

**Rozdělení pomocí dalších dimenzí.** Vaše stránky se načítají pomaleji u určitého prohlížeče, klientského operačního systému nebo lokality uživatelů? Přidejte nový graf a experimentujte s dimenzí **Seskupit podle**.

![](./media/javascript/21.png)

## <a name="ajax-performance"></a>Výkon AJAX
Ujistěte se, že všechna volání AJAX na webových stránkách dobře fungují. Často se používají k synchronnímu vyplnění částí stránek. I když se celá stránka může načíst okamžitě, uživatelé mohou být frustrováni sledováním prázdných webových části při čekání na zobrazení dat.

Volání AJAX provedená z webové stránky se zobrazí v okně prohlížeče jako závislosti.

V horní části okna se nachází souhrnné grafy:

![](./media/javascript/31.png)

a níže pak podrobné mřížky:

![](./media/javascript/33.png)

Klikněte na libovolný řádek pro konkrétní podrobnosti.

> [!NOTE]
> Při odstranění filtru prohlížečů v okně se server a závislosti AJAX zahrnou do těchto grafů. Klikněte na tlačítko Obnovit výchozí nastavení a překonfigurujte filtr.
> 
> 

**Chcete-li zobrazit podrobnosti nezdařených volání Ajax** posuňte se dolů k mřížce selhání závislostí a klikněte na řádek pro zobrazení konkrétních instancí.

![](./media/javascript/37.png)


Klikněte na tlačítko `...` pro úplnou telemetrii volání Ajax.

### <a name="no-ajax-calls-reported"></a>Žádná nahlášená volání Ajax?
Volání AJAX zahrnují HTTP/HTTPS volání ze skriptu webové stránky. Pokud je nevidíte nahlášená, zkontrolujte, zda fragment kódu nenastavil [parametry](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config) `disableAjaxTracking` nebo `maxAjaxCallsPerView` .

## <a name="browser-exceptions"></a>Výjimky prohlížečů
V okně prohlížeče se nachází graf souhrnu výjimek a níže v okně mřížka typů výjimek.

![](./media/javascript/39.png)

Pokud nevidíte nahlášené výjimky prohlížeče, zkontrolujte, zda fragment kódu nenastavil [parametr](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config) `disableExceptionTracking`.

## <a name="inspect-individual-page-view-events"></a>Zkontrolujte jednotlivé stránky zobrazení událostí

Obvykle jsou telemetrická zobrazení stránky analyzována pomocí Application Insights a zobrazí se pouze kumulativní sestavy s průměrem za všechny uživatele. Ale pro účely ladění si můžete také prohlédnout jednotlivé stránky zobrazení událostí.

V okně diagnostické vyhledávání nastavte filtry pro zobrazení stránky.

![](./media/javascript/12-search-pages.png)

Vyberte další události pro zobrazení dalších podrobností. Na stránce podrobností klikněte na tlačítko „...“, chcete-li zobrazit více podrobností.

> [!NOTE]
> Pokud používáte [hledání](../../azure-monitor/app/diagnostic-search.md), Všimněte si, že musíte Hledat celá slova: "Abou" a "bout" se neshodují s "About".
> 
> 

Při hledání zobrazení stránek můžete také použít výkonný [dotazovací jazyk Log Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-tour#browser-timings-table).

### <a name="page-view-properties"></a>Zobrazení vlastností stránky
* **Doba trvání zobrazení stránky** 
  
  * Ve výchozím nastavení čas potřebný k načtení stránky, od žádosti klienta po úplné načtení (včetně pomocných souborů s výjimkou asynchronních úloh, například volání Ajax). 
  * Pokud nastavíte `overridePageViewDuration` v [konfiguraci stránky](#detailed-configuration), interval mezi požadavkem klienta po spuštění prvního `trackPageView`. Pokud jste přesunuli trackPageView z obvyklé pozice po inicializaci skriptu, bude odrážet odlišnou hodnotu.
  * Pokud je nastavený parametr `overridePageViewDuration` a u volání `trackPageView()` je zadán argument doby trvání, pak se místo toho použije hodnota argumentu. 

## <a name="custom-page-counts"></a>Počty vlastních stránek
Ve výchozím nastavení se počet stránek objeví vždy, když se do prohlížeče klienta načte nová stránka.  Ale můžete chtít spočítat další zobrazení stránky. Například stránka může zobrazit svůj obsah na kartách a vy chcete spočítat stránky, když uživatel přepíná karty. Nebo kód jazyka JavaScript na stránce může načíst nový obsah beze změny adresy URL v prohlížeči.

Vložte podobné volání jazyka JavaScript v odpovídajícím bodě v klientském kódu:

    appInsights.trackPageView(myPageName);

Název stránky může obsahovat stejné znaky jako adresa URL, ale cokoli za „#“ nebo „?“ se ignoruje.

## <a name="usage-tracking"></a>Sledování využití
Chcete zjistit, co uživatelé provádějí s vaší aplikací?

* [Další informace o nástrojích pro analýzu chování uživatele](../../azure-monitor/app/usage-overview.md)
* [Další informace o vlastních událostech a metrikách rozhraní API](../../azure-monitor/app/api-custom-events-metrics.md).

## <a name="video"></a> Video


> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]



## <a name="next"></a> Další kroky
* [Sledování využití](../../azure-monitor/app/usage-overview.md)
* [Vlastní události a metriky](../../azure-monitor/app/api-custom-events-metrics.md)
* [Sestavení vyhodnocení poučení](../../azure-monitor/app/usage-overview.md)

