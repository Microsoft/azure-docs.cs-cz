---
title: 'Rychlý Start: monitorování webů pomocí Azure Monitor Application Insights'
description: V tomto rychlém startu se dozvíte, jak nastavit monitorování webu na straně klienta nebo prohlížeče pomocí Azure Monitor Application Insights.
ms.topic: quickstart
ms.date: 03/19/2021
ms.custom: mvc
ms.openlocfilehash: 0e10db39c8dbbf81087d696cfbb5b2ded1ae79ac
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104654891"
---
# <a name="quickstart-start-monitoring-your-website-with-azure-monitor-application-insights"></a>Rychlý Start: Začněte monitorovat web pomocí Azure Monitor Application Insights

V tomto rychlém startu se naučíte, jak na web přidat Open Source sadu Application Insights JavaScript SDK. Naučíte se také, jak lépe pochopit možnosti klienta nebo prohlížeče pro návštěvníky webu.

Azure Monitor Application Insights umožňuje snadné monitorování webu z hlediska dostupnosti, výkonu a využití. Můžete také rychle identifikovat a diagnostikovat chyby ve vaší aplikaci a nečekat na to, až je nahlásí uživatelé. Application Insights poskytuje možnosti monitorování na straně serveru i klienta nebo prohlížeče.

## <a name="prerequisites"></a>Předpoklady

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Web, na který můžete přidat Application Insights JavaScript SDK.

## <a name="enable-application-insights"></a>Povolení Application Insights

Application Insights můžou shromažďovat data telemetrie z jakékoli aplikace připojené k Internetu, která běží místně nebo v cloudu. Tato data zobrazíte pomocí následujících kroků:

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
1. Vyberte **vytvořit**  >  **Nástroje pro správu** prostředků  >  **Application Insights**.

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

1. Vyberte **Přehled** a zkopírujte **připojovací řetězec** aplikace. V tomto příkladu potřebujeme jenom část instrumentace připojovacího řetězce `InstrumentationKey=00000000-0000-0000-0000-000000000000;` .

    :::image type="content" source="media/website-monitoring/keys.png" alt-text="Snímek obrazovky se stránkou s přehledem a klíčem instrumentace a připojovacím řetězcem":::

1. ``hello_world.html``Před uzavírací značku přidejte do souboru následující skript ``</head>`` :

    ```html
    <script type="text/javascript">
    !function(T,l,y){var S=T.location,k="script",D="instrumentationKey",C="ingestionendpoint",I="disableExceptionTracking",E="ai.device.",b="toLowerCase",w="crossOrigin",N="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"5",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[b](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,u,p,l;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][b]()]=i[1])}if(!e[C]){var r=e.endpointsuffix,o=r?e.location:null;e[C]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[D]||d[D]||"",u=s[C],p=u?u+"/v2/track":d.endpointUrl,(l=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=p,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),l.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,p)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:N,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(N,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(l,p))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(k);n.src=h;var e=y[w];return!e&&""!==e||"undefined"==n[w]||(n[w]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(k)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[I]&&!0!==s[I]){var c="onerror";t(["_"+c]);var u=T[c];T[c]=function(e,t,n,a,i){var r=u&&u(e,t,n,a,i);return!0!==r&&m["_"+c]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);function a(){y.onInit&&y.onInit(n)}(T[t]=n).queue&&0===n.queue.length?(n.queue.push(a),n.trackPageView({})):a()}(window,document,{
    src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js", // The SDK URL Source
    // name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
    // ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
    // useXhr: 1, // Use XHR instead of fetch to report failures (if available),
    crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag
    // onInit: null, // Once the application insights instance has loaded and initialized this callback function will be called with 1 argument -- the sdk instance (DO NOT ADD anything to the sdk.queue -- As they won't get called)
    cfg: { // Application Insights Configuration
        connectionString:"InstrumentationKey=YOUR_INSTRUMENTATION_KEY_GOES_HERE;" 
        /* ...Other Configuration Options... */
    }});
    </script>
    ```

    > [!NOTE]
    > Aktuální fragment (uvedený výše) je verze "5", verze je ve fragmentu zakódována jako sv: "#" a [aktuální verze a podrobnosti konfigurace jsou k dispozici na GitHubu](https://go.microsoft.com/fwlink/?linkid=2156318).

1. Upravte soubor ``hello_world.html`` a přidejte svůj instrumentační klíč.

1. Otevřete soubor ``hello_world.html`` v místní relaci prohlížeče. Tato akce vytvoří zobrazení na jednu stránku. Chcete-li generovat více zobrazení zkušební stránky, můžete aktualizovat prohlížeč.

## <a name="monitor-your-website-in-the-azure-portal"></a>Monitorování webu v Azure Portal

1. Znovu otevřete stránku **přehledu** Application Insights v Azure Portal a zobrazte podrobnosti o aktuálně spuštěné aplikaci. Na stránce **Přehled** je místo, kde jste načetli klíč instrumentace.

   Čtyři výchozí grafy na stránce Přehled mají nastavený obor na data aplikace na straně serveru. Vzhledem k tomu, že instrumentuje interakce klienta nebo prohlížeče se sadou JavaScript SDK, toto konkrétní zobrazení se nepoužije, pokud je v něm také nainstalovaná sada SDK na straně serveru.

1. Vyberte **Protokoly**.  Tato akce otevře **protokol**, který poskytuje bohatý dotazovací jazyk pro analýzu všech dat shromážděných v Application Insights. Chcete-li zobrazit data související s požadavky prohlížeče na straně klienta, spusťte následující dotaz:

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

   :::image type="content" source="media/website-monitoring/log-query.png" alt-text="Snímek grafu Log Analytics s požadavky uživatelů v časovém intervalu":::

1. Vraťte se na stránku **Přehled**. Pod hlavičkou **prozkoumat** vyberte **výkon** a pak vyberte kartu **prohlížeč** .  Zobrazí se metriky související s výkonem vašeho webu. Existuje odpovídající zobrazení pro analýzu selhání a výjimek na vašem webu. Můžete vybrat **ukázky** pro přístup k [podrobnostem o koncových transakcích](./transaction-diagnostics.md).

     :::image type="content" source="media/website-monitoring/performance.png" alt-text="Obrazovka karty výkon s grafem metrik prohlížeče":::

1. V hlavní nabídce Application Insights pod hlavičkou **použití** vyberte [**Uživatelé**](./usage-segmentation.md) a začněte prozkoumat [analytické nástroje chování uživatele](./usage-overview.md). Protože provádíme testování z jednoho počítače, zobrazí se pouze data pro jednoho uživatele.

1. Pro složitější web s více stránkami můžete použít nástroj [**toky uživatelů**](./usage-flows.md) ke sledování cest, které návštěvníci procházejí různými částmi vašeho webu.

Další informace o pokročilejších konfiguracích pro monitorování webů najdete v [referenčních informacích k rozhraní API sady JavaScript SDK](./javascript.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu pokračovat v práci s dalšími rychlými starty nebo kurzy, neprovádějte čištění prostředků vytvořených v rámci tohoto rychlého startu. V opačném případě pomocí následujících kroků odstraňte všechny prostředky vytvořené tímto rychlým startem v Azure Portal.

> [!NOTE]
> Pokud jste použili existující skupinu prostředků, následující pokyny nebudou fungovat. Místo toho můžete pouze odstranit jednotlivé prostředky Application Insights. Pamatujte, že když odstraníte skupinu prostředků, odstraní se i všechny základního prostředky, které jsou členy této skupiny.

1. V nabídce vlevo na Azure Portal vyberte **skupiny prostředků** a pak vyberte **myResourceGroup** nebo název dočasné skupiny prostředků.
1. Na stránce skupiny prostředků vyberte **Odstranit**, do textového pole zadejte **myResourceGroup** a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vyhledání a diagnostika potíží s výkonem](../logs/log-query-overview.md)

