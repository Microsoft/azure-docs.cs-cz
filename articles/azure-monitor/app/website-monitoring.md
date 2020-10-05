---
title: 'Rychlý Start: monitorování webů pomocí Azure Monitor Application Insights'
description: V tomto rychlém startu se dozvíte, jak nastavit monitorování webu na straně klienta nebo prohlížeče pomocí Azure Monitor Application Insights.
ms.topic: quickstart
ms.date: 08/19/2020
ms.custom: mvc
ms.openlocfilehash: 959beeac004c71fb4593740d3c1685771638e40c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "88611326"
---
# <a name="quickstart-start-monitoring-your-website-with-azure-monitor-application-insights"></a>Rychlý Start: Začněte monitorovat web pomocí Azure Monitor Application Insights

V tomto rychlém startu se naučíte, jak na web přidat Open Source sadu Application Insights JavaScript SDK. Naučíte se také, jak lépe pochopit možnosti klienta nebo prohlížeče pro návštěvníky webu.

Azure Monitor Application Insights umožňuje snadné monitorování webu z hlediska dostupnosti, výkonu a využití. Můžete také rychle identifikovat a diagnostikovat chyby ve vaší aplikaci a nečekat na to, až je nahlásí uživatelé. Application Insights poskytuje možnosti monitorování na straně serveru i klienta nebo prohlížeče.

## <a name="prerequisites"></a>Předpoklady

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Web, na který můžete přidat Application Insights JavaScript SDK.

## <a name="enable-application-insights"></a>Povolení Application Insights

Application Insights můžou shromažďovat data telemetrie z jakékoli aplikace připojené k Internetu, která běží místně nebo v cloudu. Tato data zobrazíte pomocí následujících kroků:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/).
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
    !function(T,l,y){var S=T.location,u="script",k="instrumentationKey",D="ingestionendpoint",C="disableExceptionTracking",E="ai.device.",I="toLowerCase",b="crossOrigin",w="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"4",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[I](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,p,l,u;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][I]()]=i[1])}if(!e[D]){var r=e.endpointsuffix,o=r?e.location:null;e[D]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[k]||d[k]||"",p=s[D],l=p?p+"/v2/track":config.endpointUrl,(u=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=l,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),u.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,l)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:w,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(w,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(u,l))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(u);n.src=h;var e=y[b];return!e&&""!==e||"undefined"==n[b]||(n[b]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(u)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[C]&&!0!==s[C]){method="onerror",t(["_"+method]);var c=T[method];T[method]=function(e,t,n,a,i){var r=c&&c(e,t,n,a,i);return!0!==r&&m["_"+method]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);(T[t]=n).queue&&0===n.queue.length&&n.trackPageView({})}(window,document,{
    src: "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js", // The SDK URL Source
    //name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
    //ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
    //useXhr: 1, // Use XHR instead of fetch to report failures (if available),
    //crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag 
    cfg: { // Application Insights Configuration
        instrumentationKey: "YOUR_INSTRUMENTATION_KEY_GOES_HERE"
        /* ...Other Configuration Options... */
    }});
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

1. Vraťte se na stránku **Přehled**. Pod hlavičkou **prozkoumat** vyberte možnost **prohlížeč**a pak vyberte možnost **výkon**.  Zobrazí se metriky související s výkonem vašeho webu. Existuje odpovídající zobrazení pro analýzu selhání a výjimek na vašem webu. Můžete vybrat **ukázky** pro přístup k [podrobnostem o koncových transakcích](./transaction-diagnostics.md).

   ![Graf metrik serveru](./media/website-monitoring/browser-performance.png)

1. V hlavní nabídce Application Insights pod hlavičkou **použití** vyberte [**Uživatelé**](./usage-segmentation.md) a začněte prozkoumat [analytické nástroje chování uživatele](./usage-overview.md). Protože provádíme testování z jednoho počítače, zobrazí se pouze data pro jednoho uživatele. Pro živý web by distribuce uživatelů mohla vypadat takto:

     ![Graf uživatelů](./media/website-monitoring/usage-users.png)

1. Pro složitější web s více stránkami můžete použít nástroj [**toky uživatelů**](./usage-flows.md) ke sledování cest, které návštěvníci procházejí různými částmi vašeho webu.

   ![Vizualizace nástroje Toky uživatelů](./media/website-monitoring/user-flows.png)

Další informace o pokročilejších konfiguracích pro monitorování webů najdete v [referenčních informacích k rozhraní API sady JavaScript SDK](./javascript.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu pokračovat v práci s dalšími rychlými starty nebo kurzy, neprovádějte čištění prostředků vytvořených v rámci tohoto rychlého startu. V opačném případě pomocí následujících kroků odstraňte všechny prostředky vytvořené tímto rychlým startem v Azure Portal.

> [!NOTE]
> Pokud jste použili existující skupinu prostředků, následující pokyny nebudou fungovat. Místo toho můžete pouze odstranit jednotlivé prostředky Application Insights. Pamatujte, že když odstraníte skupinu prostředků, odstraní se i všechny základního prostředky, které jsou členy této skupiny.

1. V nabídce vlevo na Azure Portal vyberte **skupiny prostředků**a pak vyberte **myResourceGroup** nebo název dočasné skupiny prostředků.
1. Na stránce skupiny prostředků vyberte **Odstranit**, do textového pole zadejte **myResourceGroup** a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vyhledání a diagnostika potíží s výkonem](../log-query/log-query-overview.md)

