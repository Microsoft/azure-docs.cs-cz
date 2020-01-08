---
title: Monitorování webů pomocí Azure Monitor Application Insights
description: Pokyny pro rychlé nastavení monitorování webů na straně klienta nebo prohlížeče pomocí Azure Monitor Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: quickstart
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/15/2019
ms.custom: mvc
ms.openlocfilehash: 75c61452e73c50cbdaed03a27481d920e4c2ad86
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75406223"
---
# <a name="start-monitoring-your-website"></a>Zahájení monitorování webu

Azure Monitor Application Insights umožňuje snadné monitorování webu z hlediska dostupnosti, výkonu a využití. Můžete také rychle identifikovat a diagnostikovat chyby ve vaší aplikaci a nečekat na to, až je nahlásí uživatelé. Application Insights nabízí monitorování na straně serveru i možnosti monitorování na straně klienta nebo prohlížeče.

Tento rychlý Start vás provede přidáním [Open source Application Insights JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS) , který vám umožní pochopit prostředí klienta nebo prohlížeče pro návštěvníky webu.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu je potřeba:

- Mít předplatné Azure.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na web [Azure Portal](https://portal.azure.com/).

## <a name="enable-application-insights"></a>Povolení Application Insights

Application Insights může shromažďovat telemetrická data ze všech aplikací připojených k internetu a spuštěných místně nebo v cloudu. Pokud chcete tato data začít zobrazovat, použijte následující kroky.

1. Vyberte **Vytvořit prostředek** > **Nástroje pro správu** > **Application Insights**.

   > [!NOTE]
   >Pokud vytvoříte prostředek Application Insightse poprvé, můžete se dozvědět víc v článku [vytvoření prostředku Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) .

   Zobrazí se konfigurační pole. K vyplnění vstupních polí použijte následující tabulku.

    | Nastavení        | Hodnota           | Popis  |
   | ------------- |:-------------|:-----|
   | **Název**      | Globálně jedinečná hodnota | Název identifikující aplikaci, kterou monitorujete |
   | **Skupina prostředků**     | myResourceGroup      | Název nové skupiny prostředků pro hostování dat App Insights Můžete vytvořit novou skupinu prostředků nebo použít existující. |
   | **Umístění** | Východní USA | Vyberte umístění ve vaší blízkosti nebo v blízkosti místa, kde se vaše aplikace hostuje. |

2. Klikněte na **Vytvořit**.

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

## <a name="configure-app-insights-sdk"></a>Konfigurace sady App Insights SDK

1. Vyberte **Přehled** > **Základy** a zkopírujte **instrumentační klíč** vaší aplikace.

   ![Formulář Nový prostředek App Insights](media/website-monitoring/instrumentation-key-001.png)

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

4. Otevřete soubor ``hello_world.html`` v místní relaci prohlížeče. Tím se vytvoří jedno zobrazení stránky. Prohlížeč můžete aktualizovat a vygenerovat tak více testovacích zobrazení stránky.

## <a name="start-monitoring-in-the-azure-portal"></a>Zahájení monitorování na webu Azure Portal

1. Teď můžete znovu otevřít stránku **Přehled** služby Application Insights na webu Azure Portal, kde jste získali svůj instrumentační klíč, a zobrazit podrobné informace o aktuálně spuštěné aplikaci. Čtyři výchozí grafy na stránce Přehled mají nastavený obor na data aplikace na straně serveru. Vzhledem k tomu, že instrumentujeme interakce se sadou JavaScript SDK na straně klienta nebo prohlížeče, toto konkrétní zobrazení nás nezajímá, pokud nemáme nainstalovanou také sadu SDK na straně serveru.

2. Klikněte na ![ikona mapa aplikace](media/website-monitoring/006.png) **Analytics**.  Otevře se okno **Analýza** s bohatým dotazovacím jazykem pro analýzu všech dat shromážděných službou Application Insights. Pokud chcete zobrazit data související s požadavky prohlížeče na straně klienta, spusťte následující dotaz:

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

3. Vraťte se na stránku **Přehled**. Klikněte na **Prohlížeč** pod nadpisem **Prozkoumat** a pak vyberte **Výkon**. Tady najdete metriky související s výkonem vašeho webu. Najdete tu také odpovídající zobrazení pro analýzu selhání a výjimek na vašem webu. Kliknutím na **Ukázky** můžete přejít k podrobnostem o jednotlivých transakcích. Tady můžete získat přístup k prostředí s [podrobnostmi o celé transakci](../../azure-monitor/app/transaction-diagnostics.md).

   ![Graf metrik serveru](./media/website-monitoring/browser-performance.png)

4. Pokud chcete začít zkoumat [nástroje pro analýzu chování uživatelů](../../azure-monitor/app/usage-overview.md), v hlavní nabídce Application Insights pod nadpisem **Využití** vyberte [**Uživatelé**](../../azure-monitor/app/usage-segmentation.md). Protože testujeme z jediného počítače, zobrazí se nám data pouze pro jednoho uživatele. V případě živého webu by distribuce uživatelů mohla vypadat nějak takto:

     ![Graf uživatelů](./media/website-monitoring/usage-users.png)

5. Dalším užitečným nástrojem pro instrumentaci složitějších webů s více stránkami je nástroj [**Toky uživatelů**](../../azure-monitor/app/usage-flows.md). Pomocí nástroje **Toky uživatelů** můžete sledovat cesty uživatelů různými částmi vašeho webu.

   ![Vizualizace nástroje Toky uživatelů](./media/website-monitoring/user-flows.png)

Pokud se chcete dozvědět o pokročilejších konfiguracích monitorování webů, přečtěte si [referenční informace k rozhraní API sady JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete pokračovat v práci s dalšími rychlými starty nebo kurzy, nevyčišťujte prostředky vytvořené v rámci tohoto rychlého startu. Pokud však pokračovat nechcete, pomocí následujících kroků odstraňte všechny prostředky vytvořené tímto rychlým startem na webu Azure Portal.

> [!NOTE]
> Pokud jste použili existující skupinu prostředků, následující pokyny nebudou fungovat a bude potřeba jenom odstranit jednotlivé prostředky Application Insights. Mějte na paměti, že kdykoli odstraníte skupinu prostředků, odstraní se všechny základního prostředky, které jsou členy této skupiny.

1. Na webu Azure Portal v nabídce vlevo klikněte na **Skupiny prostředků** a pak na **myResourceGroup**.
2. Na stránce skupiny prostředků klikněte na **Odstranit**, do textového pole zadejte **myResourceGroup** a pak klikněte na **Odstranit**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vyhledání a diagnostika potíží s výkonem](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)
