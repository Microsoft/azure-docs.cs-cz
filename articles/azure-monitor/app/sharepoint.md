---
title: Monitorování webu SharePointu pomocí Application Insights
description: Zahájení monitorování nové aplikace s novým klíčem instrumentace
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: f07c317a61c91daf7a469c1f9eaf1fb80d4ecc49
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91825293"
---
# <a name="monitor-a-sharepoint-site-with-application-insights"></a>Monitorování webu SharePointu pomocí Application Insights

Služba Azure Application Insights monitoruje dostupnost, výkon a využití vaší aplikace. Zde se dozvíte, jak připravit prostředí pro web SharePointu.

> [!NOTE]
> Kvůli problémům se zabezpečením nemůžete přímo přidat skript, který je popsaný v tomto článku, na vaše webové stránky v moderním uživatelském prostředí SharePointu. Alternativně můžete použít [SharePoint Framework (SPFx)](/sharepoint/dev/spfx/extensions/overview-extensions) k sestavení vlastního rozšíření, které můžete použít k instalaci Application Insights na webech služby SharePoint.

## <a name="create-an-application-insights-resource"></a>Vytvořte prostředek Application Insights
Na webu [Azure Portal](https://portal.azure.com) vytvořte nový prostředek Application Insights. Vyberte jako typ aplikace ASP.NET.

![Klikněte na tlačítko Vlastnosti, vyberte klíč a stiskněte klávesy ctrl + C](./media/sharepoint/001.png)

V okně, které se otevře, je místo, kde se zobrazí data o využití a výkonu vaší aplikace. Pokud se k němu chcete vrátit při příštím přihlášení k Azure, měli byste najít příslušnou dlaždici na úvodní obrazovce. Případně ho můžete najít kliknutím na Procházet.

## <a name="add-the-script-to-your-web-pages"></a>Přidání skriptu na webové stránky

```HTML
<!-- 
To collect user behavior analytics tools about your application, 
insert the following script into each page you want to track.
Place this code immediately before the closing </head> tag,
and before any other scripts. Your first data will appear 
automatically in just a few seconds.
-->
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(n){var o={config:n,initialize:!0},t=document,e=window,i="script";setTimeout(function(){var e=t.createElement(i);e.src=n.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",t.getElementsByTagName(i)[0].parentNode.appendChild(e)});try{o.cookie=t.cookie}catch(e){}function a(n){o[n]=function(){var e=arguments;o.queue.push(function(){o[n].apply(o,e)})}}o.queue=[],o.version=2;for(var s=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];s.length;)a("track"+s.pop());var r="Track",c=r+"Page";a("start"+c),a("stop"+c);var u=r+"Event";if(a("start"+u),a("stop"+u),a("addTelemetryInitializer"),a("setAuthenticatedUserContext"),a("clearAuthenticatedUserContext"),a("flush"),o.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===n.disableExceptionTracking||n.extensionConfig&&n.extensionConfig.ApplicationInsightsAnalytics&&!0===n.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){a("_"+(s="onerror"));var p=e[s];e[s]=function(e,n,t,i,a){var r=p&&p(e,n,t,i,a);return!0!==r&&o["_"+s]({message:e,url:n,lineNumber:t,columnNumber:i,error:a}),r},n.autoExceptionInstrumented=!0}return o}(
{
  instrumentationKey:"INSTRUMENTATION_KEY"
}
);(window[aiName]=aisdk).queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

Vložte skript těsně před &lt; &gt; značku/Head každé stránky, kterou chcete sledovat. Pokud má váš web stránku předlohy, můžete skript umístit sem. Například v projektu ASP.NET MVC ho vložíte do souboru View\Shared\_Layout.cshtml.

Skript obsahuje klíč instrumentace, který nasměruje telemetrii pro daný prostředek Application Insights.

### <a name="add-the-code-to-your-site-pages"></a>Přidání kódu na stránky webu
#### <a name="on-the-master-page"></a>Na stránku šablony
Pokud můžete upravit stránku šablony daného webu, přidáte monitorování na každou stránku webu.

Rezervujte si stránku šablony a upravte ji pomocí nástroje SharePoint Designer nebo jiného editoru.

![Snímek obrazovky, který ukazuje, jak upravit hlavní stránku pomocí návrháře správce nebo jiného editoru.](./media/sharepoint/03-master.png)

Přidejte kód těsně před </head> Inteligentní. 

![Snímek obrazovky, který ukazuje, kde přidat kód na stránku webu.](./media/sharepoint/04-code.png)

#### <a name="or-on-individual-pages"></a>Nebo na jednotlivé stránky
Chcete-li monitorovat omezenou sadu stránek, přidejte skript jednotlivě na každou stránku. 

Vložte webovou část a vložte do ní fragment kódu.

![Snímek obrazovky, který ukazuje přidání skriptu pro monitorování omezené sady stránek.](./media/sharepoint/05-page.png)

## <a name="view-data-about-your-app"></a>Zobrazení dat o aplikaci
Znovu nasaďte aplikaci.

Vraťte se do okna vaší aplikace na webu [Azure Portal](https://portal.azure.com).

První události se zobrazí v hledání. 

![Snímek obrazovky zobrazující nová data, která lze zobrazit v aplikaci.](./media/sharepoint/09-search.png)

Pokud očekáváte více dat, klikněte za několik sekund na Aktualizovat.

## <a name="capturing-user-id"></a>Zaznamenání ID uživatele
Standardní fragment kódu webové stránky nezachycuje ze SharePointu ID uživatele, ale můžete toho dosáhnout pomocí malé změny.

1. Zkopírujte klíč instrumentace vaší aplikace z rozevíracího seznamu Základy ve službě Application Insights. 

    ![Snímek obrazovky, který ukazuje kopírování instrumentace aplikace z rozevíracího seznamu Essentials v Application Insights.](./media/sharepoint/02-props.png)

1. Nahraďte klíč instrumentace za text „XXXX“ v následujícím fragmentu kódu. 
2. Vložte skript do aplikaci SharePointu místo fragmentu kódu, který jste získali z portálu.

```


<SharePoint:ScriptLink ID="ScriptLink1" name="SP.js" runat="server" localizable="false" loadafterui="true" /> 
<SharePoint:ScriptLink ID="ScriptLink2" name="SP.UserProfiles.js" runat="server" localizable="false" loadafterui="true" /> 

<script type="text/javascript"> 
var personProperties; 

// Ensure that the SP.UserProfiles.js file is loaded before the custom code runs. 
SP.SOD.executeOrDelayUntilScriptLoaded(getUserProperties, 'SP.UserProfiles.js'); 

function getUserProperties() { 
    // Get the current client context and PeopleManager instance. 
    var clientContext = new SP.ClientContext.get_current(); 
    var peopleManager = new SP.UserProfiles.PeopleManager(clientContext); 

    // Get user properties for the target user. 
    // To get the PersonProperties object for the current user, use the 
    // getMyProperties method. 

    personProperties = peopleManager.getMyProperties(); 

    // Load the PersonProperties object and send the request. 
    clientContext.load(personProperties); 
    clientContext.executeQueryAsync(onRequestSuccess, onRequestFail); 
} 

// This function runs if the executeQueryAsync call succeeds. 
function onRequestSuccess() { 
var appInsights=window.appInsights||function(config){
function s(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},r=document,f=window,e="script",o=r.createElement(e),i,u;for(o.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js",r.getElementsByTagName(e)[0].parentNode.appendChild(o),t.cookie=r.cookie,t.queue=[],i=["Event","Exception","Metric","PageView","Trace"];i.length;)s("track"+i.pop());return config.disableExceptionTracking||(i="onerror",s("_"+i),u=f[i],f[i]=function(config,r,f,e,o){var s=u&&u(config,r,f,e,o);return s!==!0&&t["_"+i](config,r,f,e,o),s}),t
    }({
        instrumentationKey:"XXXX"
    });
    window.appInsights=appInsights;
    appInsights.trackPageView(document.title,window.location.href, {User: personProperties.get_displayName()});
} 

// This function runs if the executeQueryAsync call fails. 
function onRequestFail(sender, args) { 
} 
</script> 


```



## <a name="next-steps"></a>Další kroky
* [Webové testy](./monitor-web-app-availability.md) k monitorování dostupnosti vašeho webu
* [Application Insights](./app-insights-overview.md) pro jiné typy aplikace.

<!--Link references-->
