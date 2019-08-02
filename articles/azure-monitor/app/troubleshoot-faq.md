---
title: Nejčastější dotazy k Azure Application Insights | Microsoft Docs
description: Nejčastější dotazy týkající se Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0e3b103c-6e2a-4634-9e8c-8b85cf5e9c84
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: mbullwin
ms.openlocfilehash: 778a95db8ce462d06e2464db56b542f8113a4960
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875379"
---
# <a name="application-insights-frequently-asked-questions"></a>Application Insights: Nejčastější dotazy

## <a name="configuration-problems"></a>Problémy s konfigurací
*Mám potíže s nastavením mých:*

* [Aplikace .NET](asp-net-troubleshoot-no-data.md)
* [Monitorování již spuštěné aplikace](monitor-performance-live-website-now.md#troubleshoot)
* [Diagnostika Azure](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)
* [Webová aplikace Java](java-troubleshoot.md)

*Z mého serveru nezískám žádná data*

* [Nastavení výjimek brány firewall](ip-addresses.md)
* [Nastavení serveru ASP.NET](monitor-performance-live-website-now.md)
* [Nastavení serveru Java](java-agent.md)

## <a name="can-i-use-application-insights-with-"></a>Můžu použít Application Insights s...?

* [Webové aplikace na serveru služby IIS na virtuálním počítači Azure nebo v sadě Azure Virtual Machine scaleing](azure-vm-vmss-apps.md)
* [Webové aplikace na serveru služby IIS místně nebo na virtuálním počítači](asp-net.md)
* [Webové aplikace v jazyce Java](java-get-started.md)
* [Aplikace v Node.js](nodejs.md)
* [Webové aplikace v Azure](azure-web-apps.md)
* [Cloud Services v Azure](cloudservices.md)
* [Aplikační servery běžící v Docker](docker.md)
* [Jednostránkové webové aplikace](javascript.md)
* [SharePoint](sharepoint.md)
* [Desktopová aplikace pro Windows](windows-desktop.md)
* [Jiné platformy](platforms.md)

## <a name="is-it-free"></a>Je to zdarma?

Ano, pro experimentální použití. V cenovém plánu Basic může vaše aplikace odeslat určitý příspěvek na data každý měsíc zdarma. Bezplatná náhrada je dostatečně velká pro pokrytí vývoje a publikování aplikace pro malý počet uživatelů. Můžete nastavit limit, který zabrání zpracování více než zadaného množství dat.

Větší objemy telemetrie se účtují za GB. Nabízíme několik tipů, jak [omezit vaše poplatky](pricing.md).

Plán organizace se zaúčtuje za každý den, kdy každý uzel webového serveru odesílá telemetrii. Je vhodný v případě, že chcete průběžný export použít ve velkém měřítku.

[Přečtěte si Cenový tarif](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="how-much-is-it-costing"></a>Kolik stojí?

* Otevřete **stránku využití a odhadované náklady** v prostředku Application Insights. K dispozici je graf nedávného využití. Pokud chcete, můžete nastavit limit datového svazku.
* Otevřete okno [fakturace Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview) , ve kterém se zobrazí vaše účty napříč všemi prostředky.

## <a name="q14"></a>Co Application Insights v projektu upravovat?
Podrobnosti závisí na typu projektu. Pro webovou aplikaci:

* Přidá tyto soubory do projektu:

  * ApplicationInsights.config.
  * ai.js
* Nainstaluje tyto balíčky NuGet:

  * *Rozhraní API pro Application Insights* – základní rozhraní API
  * *Application Insights API pro webové aplikace* – slouží k posílání telemetrie ze serveru.
  * *Rozhraní API pro Application Insights pro aplikace JavaScriptu* – používá se k posílání telemetrie z klienta

    Balíčky zahrnují tato sestavení:
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* Vloží položky do:

  * Web.config
  * packages.config
* (Jenom nové projekty – Pokud [přidáte Application Insights do existujícího projektu][start], musíte to provést ručně.) Vloží fragmenty kódu do klienta a kód serveru pro inicializaci s ID prostředku Application Insights. Například v aplikaci MVC je kód vložen do zobrazení Master Page/Shared/_Layout. cshtml

## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Návody upgradovat ze starších verzí sady SDK?
Přečtěte si [poznámky k verzi](release-notes.md) pro sadu SDK odpovídající vašemu typu aplikace.

## <a name="update"></a>Jak můžu změnit, na který prostředek Azure můj projekt odesílá data?
V Průzkumník řešení klikněte pravým tlačítkem `ApplicationInsights.config` myši a vyberte možnost **aktualizovat Application Insights**. Data můžete odeslat do existujícího nebo nového prostředku v Azure. Průvodce aktualizací změní klíč instrumentace v souboru ApplicationInsights. config, který určuje, kde sada SDK serveru odesílá vaše data. Pokud nevyberete možnost Aktualizovat vše, změní se také klíč, ve kterém se zobrazí na webových stránkách.

## <a name="what-is-status-monitor"></a>Co je Monitorování stavu?

Desktopová aplikace, kterou můžete použít na webovém serveru služby IIS a které vám pomůžou nakonfigurovat Application Insights ve webových aplikacích. Neshromažďuje telemetrii: když aplikaci nekonfigurujete, můžete ji zastavit. 

[Další informace](monitor-performance-live-website-now.md#questions).

## <a name="what-telemetry-is-collected-by-application-insights"></a>Jaká telemetrie se shromažďuje pomocí Application Insights?

Z webových aplikací serveru:

* Požadavky HTTP
* [Závislosti](asp-net-dependencies.md). Volání na: Databáze SQL; Volání HTTP do externích služeb; Azure Cosmos DB, tabulka, úložiště objektů BLOB a fronta. 
* [Výjimky](asp-net-exceptions.md) a trasování zásobníku.
* [Čítače výkonu](performance-counters.md) – pokud používáte [monitorování stavu](monitor-performance-live-website-now.md), [monitorování Azure pro App Services](azure-web-apps.md), [monitorování Azure pro virtuální počítače nebo sadu škálování virtuálního počítače](azure-vm-vmss-apps.md)nebo [Application Insights shromážděného zapisovače](java-collectd.md).
* [Vlastní události a metriky](api-custom-events-metrics.md) , které kódujete.
* [Protokoly trasování](asp-net-trace-logs.md) , pokud konfigurujete příslušný kolektor.

Z [klientských webových stránek](javascript.md):

* [Počty zobrazení stránky](usage-overview.md)
* [Volání AJAX](asp-net-dependencies.md) Požadavky vytvořené ze spuštěného skriptu
* Data načtení zobrazení stránky
* Počty uživatelů a relací
* [ID ověřených uživatelů](api-custom-events-metrics.md#authenticated-users)

Z jiných zdrojů, pokud je nakonfigurujete:

* [Diagnostika Azure](../platform/diagnostics-extension-to-application-insights.md)
* [Import do analýz](../platform/data-collector-api.md)
* [Log Analytics](../platform/data-collector-api.md)
* [Logstash](../platform/data-collector-api.md)

## <a name="can-i-filter-out-or-modify-some-telemetry"></a>Můžu odfiltrovat nebo upravit určitou telemetrii?

Ano, na serveru, který můžete zapsat:

* Procesor telemetrie pro filtrování nebo přidání vlastností vybraných položek telemetrie před jejich odesláním z vaší aplikace
* Inicializátor telemetrie pro přidání vlastností do všech položek telemetrie

Další informace najdete v [ASP.NET](api-filtering-sampling.md) nebo [Java](java-filter-telemetry.md).

## <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>Jak se počítají města, země/oblast a další data geografického umístění?

IP adresu (IPv4 nebo IPv6) webového klienta vyhledáme pomocí [GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/).

* Telemetrie prohlížeče: Shromažďujeme IP adresu odesílatele.
* Telemetrie serveru: Modul Application Insights shromažďuje IP adresu klienta. Není shromažďována, pokud `X-Forwarded-For` je nastavena.

Můžete nakonfigurovat, `ClientIpHeaderTelemetryInitializer` aby se IP adresa převzala z jiného záhlaví. V některých systémech je například přesouvá server proxy, nástroj pro vyrovnávání zatížení nebo síť CDN `X-Originating-IP`. [Další informace](https://apmtips.com/blog/2016/07/05/client-ip-address/).

[Pomocí Power BI](export-power-bi.md ) můžete zobrazit telemetrii žádostí na mapě.


## <a name="data"></a>Jak dlouho se data na portálu uchovávají? Je zabezpečení?
Podívejte se na [uchovávání dat a ochranu osobních údajů][data].

## <a name="could-personal-data-be-sent-in-the-telemetry"></a>Můžou být do telemetrie odesílána osobní data?

To je možné, pokud váš kód odesílá taková data. K tomu může dojít také v případě, že proměnné v trasování zásobníku obsahují osobní údaje. Váš vývojový tým by měl provádět posouzení rizik, aby se zajistilo správné zpracování osobních údajů. [Přečtěte si další informace o uchovávání dat a ochraně osobních údajů](data-retention-privacy.md).

Po vyhledání atributů geografického umístění jsou **všechny** oktety klientské webové adresy vždycky nastavené na 0.

## <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>Můj klíč instrumentace je viditelný ve zdroji webové stránky. 

* To je běžný postup při monitorování řešení.
* Nedá se použít ke krádeži vašich dat.
* Dá se použít k zkosení dat nebo aktivaci výstrah.
* Neslyšeli jsme, že u každého zákazníka byly takové problémy.

Můžete:

* Pro data klienta a serveru použijte dva samostatné klíče instrumentace (samostatné Application Insights prostředky). Nebo
* Napíšete proxy server, který běží na serveru, a webový klient bude odesílat data prostřednictvím tohoto proxy serveru.

## <a name="post"></a>Návody najdete v tématu vystavování dat v diagnostickém vyhledávání?
Data neodesíláme automaticky, ale můžete použít volání TrackTrace: vložte data do parametru zprávy. To má delší omezení velikosti než limity vlastností řetězce, ale nemůžete je filtrovat.

## <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Mám použít jeden nebo více prostředků Application Insights?

Pro všechny komponenty nebo role v jednom podnikovém systému použijte jeden prostředek. Použijte samostatné prostředky pro vývoj, testování a vydání a pro nezávislé aplikace.

* [Podívejte se na diskuzi tady](separate-resources.md)
* [Příklad – cloudová služba s pracovními a webovými rolemi](cloudservices.md)

## <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Návody dynamicky měnit klíč instrumentace?

* [Diskuze sem](separate-resources.md)
* [Příklad – cloudová služba s pracovními a webovými rolemi](cloudservices.md)

## <a name="what-are-the-user-and-session-counts"></a>Jaké jsou počty uživatelů a relací?

* Sada JavaScript SDK nastavuje na webovém klientovi soubor cookie pro uživatele, identifikaci vracení uživatelů a soubor cookie relace pro seskupení aktivit.
* Pokud není k dispozici žádný skript na straně klienta, můžete [nastavit soubory cookie na serveru](https://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/).
* Pokud se vaše lokalita používá v různých prohlížečích nebo v rámci anonymním nebo v různých počítačích, bude se tato síť počítat více než jednou.
* Chcete-li identifikovat přihlášeného uživatele v počítačích a prohlížečích, přidejte volání [setAuthenticatedUserContext ()](api-custom-events-metrics.md#authenticated-users).

## <a name="q17"></a>Jsem povolil vše v Application Insights?
| Co byste měli vidět | Jak ho získat | Proč to chcete |
| --- | --- | --- |
| Grafy dostupnosti |[Webové testy](monitor-web-app-availability.md) |Informace o tom, že vaše webová aplikace je zapnutá |
| Výkon aplikace serveru: doby odezvy,... |[Přidejte do svého projektu Application Insights](asp-net.md) nebo [nainstalujte AI monitorování stavu na server](monitor-performance-live-website-now.md) (nebo zapište vlastní kód pro [sledování závislostí](api-custom-events-metrics.md#trackdependency)). |Zjistit problémy s výkonem |
| Telemetrie závislostí |[Nainstalovat AI Monitorování stavu na serveru](monitor-performance-live-website-now.md) |Diagnostika problémů s databázemi nebo jinými externími komponentami |
| Získat trasování zásobníku z výjimek |[Vložení volání TrackException do kódu](asp-net-exceptions.md) (ale některé jsou hlášeny automaticky) |Detekovat a diagnostikovat výjimky |
| Hledat trasování protokolu |[Přidat adaptér protokolování](asp-net-trace-logs.md) |Diagnostikujte výjimky, problémy s výkonem |
| Základy použití klientů: zobrazení stránky, relace,... |[Inicializátor JavaScriptu na webových stránkách](javascript.md) |Analýza využití |
| Vlastní metriky klienta |[Sledování hovorů na webových stránkách](api-custom-events-metrics.md) |Vylepšení uživatelského prostředí |
| Vlastní metriky serveru |[Sledování hovorů na serveru](api-custom-events-metrics.md) |Business intelligence |

## <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Proč se počty v grafech hledání a metrik nerovnají?

[Vzorkování](sampling.md) snižuje počet položek telemetrie (požadavky, vlastní události atd.), které jsou ve skutečnosti odesílány z vaší aplikace na portál. V části Hledat se zobrazí počet položek, které byly skutečně přijaty. V grafech metrik, které zobrazují počet událostí, se zobrazí počet původních událostí, ke kterým došlo. 

Každá odeslaná položka nese `itemCount` vlastnost, která ukazuje, kolik původních událostí položka představuje. Pokud chcete sledovat vzorkování v provozu, můžete spustit tento dotaz v Analytics:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


## <a name="automation"></a>Automation

### <a name="configuring-application-insights"></a>Konfigurace Application Insights

Pomocí Azure Sledování prostředků můžete [psát skripty PowerShellu](powershell.md) :

* Vytváření a aktualizace prostředků Application Insights.
* Nastavte cenový plán.
* Získejte klíč instrumentace.
* Přidejte upozornění metriky.
* Přidejte test dostupnosti.

Nemůžete nastavit sestavu Průzkumníka metrik ani nastavit průběžný export.

### <a name="querying-the-telemetry"></a>Dotazování telemetrie

Pro spouštění analytických dotazů [](analytics.md) použijte [REST API](https://dev.applicationinsights.io/) .

## <a name="how-can-i-set-an-alert-on-an-event"></a>Jak můžu nastavit upozornění na událost?

Výstrahy Azure jsou jenom v metrikách. Vytvořte vlastní metriku, která při každém výskytu události přeprotíná prahovou hodnotu hodnoty. Pak u metriky nastavte výstrahu. Všimněte si, že: obdržíte oznámení, kdykoli metrika v obou směrech vyřadí prahovou hodnotu. nebudete dostávat žádné oznámení do prvního křížení bez ohledu na to, jestli je počáteční hodnota vysoká nebo nízká. vždy se jedná o latenci během několika minut.

## <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Účtují se poplatky za přenos dat mezi webovou aplikací Azure a Application Insights?

* Pokud je webová aplikace Azure hostovaná v datovém centru, kde je koncový bod kolekce Application Insights, neúčtují se žádné poplatky. 
* Pokud se v datovém centru hostitele nenachází koncový bod kolekce, bude telemetrie vaší aplikace účtovat [odchozí poplatky za Azure](https://azure.microsoft.com/pricing/details/bandwidth/).

Nezáleží na tom, kde je váš prostředek Application Insights hostovaný. Záleží jenom na distribuci našich koncových bodů.

## <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Můžu na portálu Application Insights odeslat telemetrii?

Doporučujeme používat naše sady SDK a používat [rozhraní API SDK](api-custom-events-metrics.md). Existují varianty sady SDK pro různé [platformy](platforms.md). Tyto sady SDK zpracovávají ukládání do vyrovnávací paměti, kompresi, omezování, opakování a tak dále. [Schéma](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema) příjmu a [protokol koncového bodu](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) jsou ale veřejné.

## <a name="can-i-monitor-an-intranet-web-server"></a>Můžu monitorovat intranetový webový server?

Ano, ale budete muset pro naše služby povolený provoz buď pomocí výjimek brány firewall, nebo přesměrování proxy.
- QuickPulse`https://rt.services.visualstudio.com:443` 
- ApplicationIdProvider`https://dc.services.visualstudio.com:443` 
- TelemetryChannel`https://dc.services.visualstudio.com:443` 


[Tady si můžete](../../azure-monitor/app/ip-addresses.md)projít úplný seznam služeb a IP adres.

### <a name="firewall-exception"></a>Výjimka brány firewall

Umožněte webovému serveru odesílat telemetrii do našich koncových bodů. 

### <a name="gateway-redirect"></a>Přesměrování brány

Přesměrujte provoz z vašeho serveru do brány v intranetu přepsáním koncových bodů ve vaší konfiguraci.
Pokud tyto vlastnosti Endpoint nejsou v konfiguraci k dispozici, budou tyto třídy používat výchozí hodnoty uvedené níže v příkladu ApplicationInsights. config. 

Brána by měla směrovat provoz na základní adresu našeho koncového bodu. V konfiguraci nahraďte výchozí hodnoty `http://<your.gateway.address>/<relative path>`hodnotou.


#### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>Příklad ApplicationInsights. config s výchozími koncovými body:
```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://rt.services.visualstudio.com/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.services.visualstudio.com/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

_Poznámka ApplicationIdProvider je k dispozici od verze v 2.6.0_

### <a name="proxy-passthrough"></a>Průchozí proxy

Passthrough proxy můžete dosáhnout konfigurací úrovně počítače nebo proxy serveru na úrovni aplikace.
Další informace najdete v článku dotnet na [defaultProxy](https://docs.microsoft.com/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings).
 
 Příklad souboru Web. config:
 ```xml
<system.net>
    <defaultProxy>
      <proxy proxyaddress="http://xx.xx.xx.xx:yyyy" bypassonlocal="true"/>
    </defaultProxy>
</system.net>
```
 

## <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>Můžu webové testy dostupnosti spustit na intranetovém serveru?

Naše [webové testy](monitor-web-app-availability.md) jsou spouštěny v bodech přítomnosti, které jsou distribuovány po celém světě. Existují dvě řešení:

* Dvířka brány firewall – umožňují žádosti na server z dlouhého a nepřípustného [seznamu webových testovacích agentů](ip-addresses.md).
* Napište svůj vlastní kód pro posílání pravidelných požadavků na server v rámci intranetu. Pro tento účel můžete spustit webové testy sady Visual Studio. Tester by mohl odeslat výsledky do Application Insights pomocí rozhraní API TrackAvailability ().

## <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>Jak dlouho trvá shromažďování telemetrie?

Většina dat Application Insights má latenci kratší než 5 minut. Některá data mohou trvat déle; obvykle jsou větší soubory protokolu. Další informace najdete v tématu věnovaném [smlouvě SLA Application Insights](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/).

## <a name="more-answers"></a>Další odpovědi
* [Fórum Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

<!--Link references-->

[data]: data-retention-privacy.md
[platforms]: platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md
