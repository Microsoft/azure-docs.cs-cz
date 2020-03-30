---
title: 'Úvodní příručka: Monitorování webů pomocí přehledů aplikací Azure Monitor'
description: Poskytuje funkce azure-to-sinbovatinastrus k dispozici pokyny pro nastavení klienta/prohlížeče pomocí přehledů aplikací Azure Monitor.
ms.topic: quickstart
ms.date: 07/15/2019
ms.custom: mvc
ms.openlocfilehash: 495c40ca8e383dd5a3cf3ba9e5bd42e2936ea015
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80132368"
---
# <a name="quickstart-start-monitoring-your-website-with-azure-monitor-application-insights"></a>Úvodní příručka: Začněte sledovat svůj web pomocí přehledů aplikací Azure Monitor

V tomto rychlém startu se naučíte přidat na svůj web open source application insights JavaScript SDK. Dozvíte se také, jak lépe porozumět prostředí na straně klienta / prohlížeče pro návštěvníky vašich webových stránek.

Azure Monitor Application Insights umožňuje snadné monitorování webu z hlediska dostupnosti, výkonu a využití. Můžete také rychle identifikovat a diagnostikovat chyby ve vaší aplikaci a nečekat na to, až je nahlásí uživatelé. Application Insights poskytuje monitorování na straně serveru i možnosti monitorování na straně klienta/prohlížeče.

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Web, na který můžete přidat javascriptovou sadu Application Insights.

## <a name="enable-application-insights"></a>Povolení Application Insights

Application Insights může shromažďovat telemetrická data ze všech aplikací připojených k internetu a spuštěných místně nebo v cloudu. Pokud chcete tato data začít zobrazovat, použijte následující kroky.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Vyberte **Možnost Vytvořit** > nástroje pro**správu** > **prostředků Application Insights**.

   > [!NOTE]
   >Pokud je to poprvé, co vytváříte prostředek Application Insights, můžete se dozvědět více v článku [Vytvořit prostředek Application Insights.](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)

   Zobrazí se konfigurační pole. K vyplnění vstupních polí použijte následující tabulku.

    | Nastavení        | Hodnota           | Popis  |
   | ------------- |:-------------|:-----|
   | **Název**      | Globálně jedinečná hodnota | Název, který identifikuje aplikaci, kterou sledujete |
   | **Skupina prostředků**     | myResourceGroup      | Název nové skupiny prostředků pro hostování dat Application Insights. Můžete vytvořit novou skupinu prostředků nebo použít existující. |
   | **Umístění** | USA – východ | Vyberte umístění ve vaší blízkosti nebo v blízkosti místa, kde se vaše aplikace hostuje. |

3. Klikněte na **Vytvořit**.

## <a name="create-an-html-file"></a>Vytvoření souboru HTML

1. Na místním počítači vytvořte soubor ``hello_world.html``. Pro účely tohoto příkladu se soubor umístí do kořenového adresáře jednotky C: ``C:\hello_world.html``.
2. Do souboru ``hello_world.html`` zkopírujte následující skript:

    ```html
    <!DOCTYPE html>
    <html>
    <head>
    <title>Azure Monitor Application Insights</title>
    </head>
    <body>
    <h1>Azure Monitor Application Insights Hello World!</h1>
    <p>You can use the Application Insights JavaScript SDK to perform client/browser-side monitoring of your website. To learn about more advanced JavaScript SDK configurations visit the <a href="https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md" title="API Reference">API reference</a>.</p>
    </body>
    </html>
    ```

## <a name="configure-application-insights-sdk"></a>Konfigurace sady SDK SDK aplikace

1. Vyberte **možnost Přehled** > **Essentials** > Zkopírujte klíč **instrumentace**aplikace .

   ![Formulář Nový prostředek Application Insights](media/website-monitoring/instrumentation-key-001.png)

2. Do souboru ``hello_world.html`` přidejte před ukončovací značku ``</head>`` následující skript:

   ```javascript
   <script type="text/javascript">
      var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
      {
         instrumentationKey:"INSTRUMENTATION_KEY"
      }
      );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
   </script>
   ```

3. Upravte soubor ``hello_world.html`` a přidejte svůj instrumentační klíč.

4. Otevřete soubor ``hello_world.html`` v místní relaci prohlížeče. Tato akce vytvoří jedno zobrazení stránky. Prohlížeč můžete aktualizovat a vygenerovat tak více testovacích zobrazení stránky.

## <a name="start-monitoring-in-the-azure-portal"></a>Zahájení monitorování na webu Azure Portal

1. Teď můžete znovu otevřít stránku **Přehled** aplikací na webu Azure Portal a zobrazit podrobnosti o aktuálně spuštěné aplikaci. Stránka **Přehled** je místo, kde jste načetli klíč instrumentace. Čtyři výchozí grafy na stránce Přehled mají nastavený obor na data aplikace na straně serveru. Vzhledem k tomu, že jsme instrumentování klienta / prohlížeče-side interakce s JavaScript SDK, toto konkrétní zobrazení neplatí, pokud máme také nainstalovaný sdk na straně serveru.

2. Klikněte na ![ikonu Mapa aplikace](media/website-monitoring/006.png) **Analýza**.  Tato akce otevře **službu Analytics**, která poskytuje rozšířený dotazovací jazyk pro analýzu všech dat shromážděných pomocí přehledů aplikací. Pokud chcete zobrazit data související s požadavky prohlížeče na straně klienta, spusťte následující dotaz:

    ```kusto
    // average pageView duration by name
    let timeGrain=1s;
    let dataset=pageViews
    // additional filters can be applied here
    | where timestamp > ago(15m)
    | where client_Type == "Browser" ;
    // calculate average pageView duration for all pageViews
    dataset
    | summarize avg(duration) by bin(timestamp, timeGrain)
    | extend pageView='Overall'
    // render result in a chart
    | render timechart
    ```

   ![Graf analýzy uživatelských požadavků za časové období](./media/website-monitoring/analytics-query.png)

3. Vraťte se na stránku **Přehled**. Klikněte na **Prohlížeč** pod nadpisem **Prozkoumat** a pak vyberte **Výkon**. Tady najdete metriky související s výkonem vašeho webu. K dispozici je také odpovídající zobrazení pro analýzu selhání a výjimek na vašem webu. Kliknutím na **Ukázky** můžete přejít k podrobnostem o jednotlivých transakcích. Tady můžete získat přístup k prostředí s [podrobnostmi o celé transakci](../../azure-monitor/app/transaction-diagnostics.md).

   ![Graf metrik serveru](./media/website-monitoring/browser-performance.png)

4. Pokud chcete začít zkoumat [nástroje pro analýzu chování uživatelů](../../azure-monitor/app/usage-overview.md), v hlavní nabídce Application Insights pod nadpisem **Využití** vyberte [**Uživatelé**](../../azure-monitor/app/usage-segmentation.md). Vzhledem k tomu, že testujeme z jednoho počítače, zobrazí se data pouze pro jednoho uživatele. V případě živého webu by distribuce uživatelů mohla vypadat nějak takto:

     ![Graf uživatelů](./media/website-monitoring/usage-users.png)

5. Dalším užitečným nástrojem pro instrumentaci složitějších webů s více stránkami je nástroj [**Toky uživatelů**](../../azure-monitor/app/usage-flows.md). Pomocí nástroje **Toky uživatelů** můžete sledovat cesty uživatelů různými částmi vašeho webu.

   ![Vizualizace nástroje Toky uživatelů](./media/website-monitoring/user-flows.png)

Pokud se chcete dozvědět o pokročilejších konfiguracích monitorování webů, přečtěte si [referenční informace k rozhraní API sady JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat v práci s dalšími rychlými starty nebo s kurzy, nečistěte prostředky vytvořené v tomto rychlém startu. V opačném případě pokud nemáte v plánu pokračovat, použijte následující kroky k odstranění všech prostředků vytvořených tímto rychlým startem na webu Azure Portal.

> [!NOTE]
> Pokud jste použili existující skupinu prostředků, níže uvedené pokyny nebudou fungovat a budete muset odstranit jednotlivé prostředky Application Insights. Mějte na paměti, že kdykoli odstraníte skupinu prostředků, všechny podzásobené prostředky, které jsou členy této skupiny, budou odstraněny.

1. V levé nabídce na webu Azure portal klikněte na **Skupiny prostředků**a potom klikněte na **myResourceGroup** nebo název vaší dočasné skupiny prostředků.
2. Na stránce skupiny prostředků klikněte na **Odstranit**, do textového pole zadejte **myResourceGroup** a potom klikněte na **Odstranit**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vyhledání a diagnostika potíží s výkonem](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)
