---
title: 'Rychlý Start: monitorování webů pomocí Azure Monitor Application Insights'
description: V tomto rychlém startu se dozvíte, jak nastavit monitorování webu na straně klienta nebo prohlížeče pomocí Azure Monitor Application Insights.
ms.topic: quickstart
ms.date: 07/15/2019
ms.custom: mvc
ms.openlocfilehash: fa2ecd5d953ec0411a122dc7107ce23de1ae5bc6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87014044"
---
# <a name="quickstart-start-monitoring-your-website-with-azure-monitor-application-insights"></a>Rychlý Start: Začněte monitorovat web pomocí Azure Monitor Application Insights

V tomto rychlém startu se naučíte, jak na web přidat Open Source sadu Application Insights JavaScript SDK. Naučíte se také, jak lépe pochopit možnosti klienta nebo prohlížeče pro návštěvníky webu.

Azure Monitor Application Insights umožňuje snadné monitorování webu z hlediska dostupnosti, výkonu a využití. Můžete také rychle identifikovat a diagnostikovat chyby ve vaší aplikaci a nečekat na to, až je nahlásí uživatelé. Application Insights poskytuje možnosti monitorování na straně serveru i klienta nebo prohlížeče.

## <a name="prerequisites"></a>Předpoklady

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Web, na který můžete přidat Application Insights JavaScript SDK.

## <a name="enable-application-insights"></a>Povolení Application Insights

Application Insights můžou shromažďovat data telemetrie z jakékoli aplikace připojené k Internetu, která běží místně nebo v cloudu. Tato data zobrazíte pomocí následujících kroků:

1. Přihlaste se na portál [Azure Portal](https://portal.azure.com/).
1. Vyberte **vytvořit**  >  **Nástroje pro správu**prostředků  >  **Application Insights**.

   > [!NOTE]
   >Pokud Application Insights prostředek vytvoříte poprvé, přečtěte si téma [vytvoření prostředku Application Insights](./create-new-resource.md).
1. Jakmile se zobrazí pole konfigurace, dokončete pomocí následující tabulky vstupní pole:

    | Nastavení        | Hodnota           | Popis  |
   | ------------- |:-------------|:-----|
   | **Název**      | Globálně jedinečná hodnota | Název, který identifikuje aplikaci, kterou sledujete. |
   | **Skupina prostředků**     | myResourceGroup      | Název nové skupiny prostředků pro hostování dat Application Insights. Můžete vytvořit novou skupinu prostředků nebo použít existující. |
   | **Umístění** | East US | Vyberte umístění poblíž vaší aplikace nebo poblíž místa, kde je vaše aplikace hostovaná. |
1. Vyberte **Vytvořit**.

## <a name="create-an-html-file"></a>Vytvoření souboru HTML

1. Na místním počítači vytvořte soubor ``hello_world.html``. V tomto příkladu vytvořte soubor na kořeni jednotky C, aby vypadal jako ``C:\hello_world.html`` .
1. Zkopírujte následující skript a vložte ho do ``hello_world.html`` :

    ```html
    <!DOCTYPE html>
    <html>
    <head>
    <title>Azure Monitor Application Insights</title>
    </head>
    <body>
    <h1>Azure Monitor Application Insights Hello World!</h1>
    <p>You can use the Application Insights JavaScript SDK to perform client/browser-side monitoring of your website. To learn about more advanced JavaScript SDK configurations, visit the <a href="https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md" title="API Reference">API reference</a>.</p>
    </body>
    </html>
    ```

## <a name="configure-application-insights-sdk"></a>Konfigurace Application Insights SDK

1. Vyberte **Přehled**  >  **základy**a pak zkopírujte **klíč instrumentace**vaší aplikace.

   ![Formulář Nový prostředek Application Insights](media/website-monitoring/instrumentation-key-001.png)

1. ``hello_world.html``Před uzavírací značku přidejte do souboru následující skript ``</head>`` :

   ```javascript
   <script type="text/javascript">
      var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
      {
         instrumentationKey:"INSTRUMENTATION_KEY"
      }
      );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
   </script>
   ```

1. Upravte soubor ``hello_world.html`` a přidejte svůj instrumentační klíč.

1. Otevřete soubor ``hello_world.html`` v místní relaci prohlížeče. Tato akce vytvoří zobrazení na jednu stránku. Chcete-li generovat více zobrazení zkušební stránky, můžete aktualizovat prohlížeč.

## <a name="monitor-your-website-in-the-azure-portal"></a>Monitorování webu v Azure Portal

1. Znovu otevřete stránku **přehledu** Application Insights v Azure Portal a zobrazte podrobnosti o aktuálně spuštěné aplikaci. Na stránce **Přehled** je místo, kde jste načetli klíč instrumentace.

   Čtyři výchozí grafy na stránce Přehled mají nastavený obor na data aplikace na straně serveru. Vzhledem k tomu, že instrumentuje interakce klienta nebo prohlížeče se sadou JavaScript SDK, toto konkrétní zobrazení se nepoužije, pokud je v něm také nainstalovaná sada SDK na straně serveru.

1. Vyberte **Analytics** ![ ikonu mapa aplikace Analytics ](media/website-monitoring/006.png) .  Tato akce otevře **analýzu**, která poskytuje bohatý dotazovací jazyk pro analýzu všech dat shromážděných v Application Insights. Chcete-li zobrazit data související s požadavky prohlížeče na straně klienta, spusťte následující dotaz:

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

1. Vraťte se na stránku **Přehled**. Pod hlavičkou **prozkoumat** vyberte možnost **prohlížeč**a pak vyberte možnost **výkon**.  Zobrazí se metriky související s výkonem vašeho webu. Existuje odpovídající zobrazení pro analýzu selhání a výjimek na vašem webu. Můžete vybrat **ukázky** pro přístup k [podrobnostem o koncových transakcích](../../azure-monitor/app/transaction-diagnostics.md).

   ![Graf metrik serveru](./media/website-monitoring/browser-performance.png)

1. V hlavní nabídce Application Insights pod hlavičkou **použití** vyberte [**Uživatelé**](../../azure-monitor/app/usage-segmentation.md) a začněte prozkoumat [analytické nástroje chování uživatele](../../azure-monitor/app/usage-overview.md). Protože provádíme testování z jednoho počítače, zobrazí se pouze data pro jednoho uživatele. Pro živý web by distribuce uživatelů mohla vypadat takto:

     ![Graf uživatelů](./media/website-monitoring/usage-users.png)

1. Pro složitější web s více stránkami můžete použít nástroj [**toky uživatelů**](../../azure-monitor/app/usage-flows.md) ke sledování cest, které návštěvníci procházejí různými částmi vašeho webu.

   ![Vizualizace nástroje Toky uživatelů](./media/website-monitoring/user-flows.png)

Další informace o pokročilejších konfiguracích pro monitorování webů najdete v [referenčních informacích k rozhraní API sady JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu pokračovat v práci s dalšími rychlými starty nebo kurzy, neprovádějte čištění prostředků vytvořených v rámci tohoto rychlého startu. V opačném případě pomocí následujících kroků odstraňte všechny prostředky vytvořené tímto rychlým startem v Azure Portal.

> [!NOTE]
> Pokud jste použili existující skupinu prostředků, následující pokyny nebudou fungovat. Místo toho můžete pouze odstranit jednotlivé prostředky Application Insights. Pamatujte, že když odstraníte skupinu prostředků, odstraní se i všechny základního prostředky, které jsou členy této skupiny.

1. V nabídce vlevo na Azure Portal vyberte **skupiny prostředků**a pak vyberte **myResourceGroup** nebo název dočasné skupiny prostředků.
1. Na stránce skupiny prostředků vyberte **Odstranit**, do textového pole zadejte **myResourceGroup** a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vyhledání a diagnostika potíží s výkonem](../log-query/log-query-overview.md)
