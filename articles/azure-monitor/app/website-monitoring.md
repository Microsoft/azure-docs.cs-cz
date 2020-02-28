---
title: 'Rychlý Start: monitorování webů pomocí Azure Monitor Application Insights'
description: Poskytuje pokyny pro rychlý Start při nastavení monitorování klienta/webu na straně prohlížeče pomocí Azure Monitor Application Insights
ms.topic: quickstart
ms.date: 07/15/2019
ms.custom: mvc
ms.openlocfilehash: 1c581867f1e7ebc29f2aaff97124a08bec4e3b40
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670844"
---
# <a name="quickstart-start-monitoring-your-website-with-azure-monitor-application-insights"></a>Rychlý Start: Začněte monitorovat web pomocí Azure Monitor Application Insights

V tomto rychlém startu se naučíte, jak na web přidat Open Source sadu Application Insights JavaScript SDK. Naučíte se také, jak lépe pochopit možnosti klienta nebo prohlížeče pro návštěvníky webu.

Azure Monitor Application Insights umožňuje snadné monitorování webu z hlediska dostupnosti, výkonu a využití. Můžete také rychle identifikovat a diagnostikovat chyby ve vaší aplikaci a nečekat na to, až je nahlásí uživatelé. Application Insights poskytuje možnosti monitorování na straně serveru i klienta nebo prohlížeče.

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Web, na který můžete přidat Application Insights JavaScript SDK.

## <a name="enable-application-insights"></a>Povolení Application Insights

Application Insights může shromažďovat telemetrická data ze všech aplikací připojených k internetu a spuštěných místně nebo v cloudu. Pokud chcete tato data začít zobrazovat, použijte následující kroky.

1. Přihlaste se na web [Azure Portal ](https://portal.azure.com/).
2. Vyberte **Vytvořit prostředek** > **Nástroje pro správu** > **Application Insights**.

   > [!NOTE]
   >Pokud vytvoříte prostředek Application Insightse poprvé, můžete se dozvědět víc v článku [vytvoření prostředku Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) .

   Zobrazí se konfigurační pole. K vyplnění vstupních polí použijte následující tabulku.

    | Nastavení        | Hodnota           | Popis  |
   | ------------- |:-------------|:-----|
   | **Název**      | Globálně jedinečná hodnota | Název, který identifikuje aplikaci, kterou sledujete |
   | **Skupina prostředků**     | myResourceGroup      | Název nové skupiny prostředků pro hostování dat Application Insights. Můžete vytvořit novou skupinu prostředků nebo použít existující. |
   | **Umístění** | Východní USA | Vyberte umístění ve vaší blízkosti nebo v blízkosti místa, kde se vaše aplikace hostuje. |

3. Klikněte na možnost **Vytvořit**.

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

## <a name="configure-application-insights-sdk"></a>Konfigurace Application Insights SDK

1. Vyberte **Přehled** > **Základy** a zkopírujte **instrumentační klíč** vaší aplikace.

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

4. Otevřete soubor ``hello_world.html`` v místní relaci prohlížeče. Tato akce vytvoří jeden PageView. Prohlížeč můžete aktualizovat a vygenerovat tak více testovacích zobrazení stránky.

## <a name="start-monitoring-in-the-azure-portal"></a>Zahájení monitorování na webu Azure Portal

1. Nyní můžete znovu otevřít stránku **přehled** Application Insights v Azure Portal a zobrazit podrobnosti o aktuálně spuštěné aplikaci. Na stránce **Přehled** je místo, kde jste načetli klíč instrumentace. Čtyři výchozí grafy na stránce Přehled mají nastavený obor na data aplikace na straně serveru. Vzhledem k tomu, že instrumentuje interakce klienta nebo prohlížeče se sadou JavaScript SDK, toto konkrétní zobrazení se nepoužije, pokud je v něm také nainstalovaná sada SDK na straně serveru.

2. Klikněte na ![ikona mapa aplikace](media/website-monitoring/006.png) **Analytics**.  Tato akce otevře **analýzu**, která poskytuje bohatý dotazovací jazyk pro analýzu všech dat shromážděných v Application Insights. Pokud chcete zobrazit data související s požadavky prohlížeče na straně klienta, spusťte následující dotaz:

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

4. Pokud chcete začít zkoumat [nástroje pro analýzu chování uživatelů](../../azure-monitor/app/usage-overview.md), v hlavní nabídce Application Insights pod nadpisem [Využití**vyberte**](../../azure-monitor/app/usage-segmentation.md)Uživatelé. Vzhledem k tomu, že provádíme testování z jednoho počítače, uvidíme jenom data pro jednoho uživatele. V případě živého webu by distribuce uživatelů mohla vypadat nějak takto:

     ![Graf uživatelů](./media/website-monitoring/usage-users.png)

5. Dalším užitečným nástrojem pro instrumentaci složitějších webů s více stránkami je nástroj [**Toky uživatelů**](../../azure-monitor/app/usage-flows.md). Pomocí nástroje **Toky uživatelů** můžete sledovat cesty uživatelů různými částmi vašeho webu.

   ![Vizualizace nástroje Toky uživatelů](./media/website-monitoring/user-flows.png)

Pokud se chcete dozvědět o pokročilejších konfiguracích monitorování webů, přečtěte si [referenční informace k rozhraní API sady JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu pokračovat v práci s dalšími rychlými starty nebo kurzy, neprovádějte čištění prostředků vytvořených v rámci tohoto rychlého startu. Jinak pokud pokračovat nechcete, pomocí následujícího postupu odstraňte všechny prostředky vytvořené tímto rychlým startem v Azure Portal.

> [!NOTE]
> Pokud jste použili existující skupinu prostředků, následující pokyny nebudou fungovat a bude potřeba jenom odstranit jednotlivé prostředky Application Insights. Mějte na paměti, že kdykoli odstraníte skupinu prostředků, odstraní se všechny základního prostředky, které jsou členy této skupiny.

1. Na webu Azure Portal v nabídce vlevo klikněte na **Skupiny prostředků** a pak na **myResourceGroup**.
2. Na stránce skupiny prostředků klikněte na **Odstranit**, do textového pole zadejte **myResourceGroup** a pak klikněte na **Odstranit**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vyhledání a diagnostika potíží s výkonem](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)
