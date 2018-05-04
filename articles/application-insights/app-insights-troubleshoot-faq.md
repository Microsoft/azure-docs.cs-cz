---
title: Nejčastější dotazy k Azure Application Insights | Microsoft Docs
description: Nejčastější dotazy o Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0e3b103c-6e2a-4634-9e8c-8b85cf5e9c84
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: mbullwin
ms.openlocfilehash: d7abfd1ac6f914c75297ff49462590e5b6169dbd
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2018
---
# <a name="application-insights-frequently-asked-questions"></a>Application Insights: Nejčastější dotazy

## <a name="configuration-problems"></a>Problémy s konfigurací
*Mám potíže při nastavení Moje:*

* [Aplikace .NET](app-insights-asp-net-troubleshoot-no-data.md)
* [Monitorování aplikace již spuštěná](app-insights-monitor-performance-live-website-now.md#troubleshooting-runtime-configuration-of-application-insights)
* [Azure diagnostics](app-insights-azure-diagnostics.md)
* [Webová aplikace Java](app-insights-java-troubleshoot.md)

*Zobrazí žádná data ze svého serveru*

* [Sada výjimky brány firewall](app-insights-ip-addresses.md)
* [Nastavení serveru technologie ASP.NET](app-insights-monitor-performance-live-website-now.md)
* [Nastavení serveru Java](app-insights-java-agent.md)

## <a name="can-i-use-application-insights-with-"></a>Můžete použít Application Insights s...?

* [Webové aplikace na serveru služby IIS – místně nebo ve virtuálním počítači](app-insights-asp-net.md)
* [Webové aplikace v jazyce Java](app-insights-java-get-started.md)
* [Aplikace v Node.js](app-insights-nodejs.md)
* [Webové aplikace v Azure](app-insights-azure-web-apps.md)
* [Cloudové služby v Azure](app-insights-cloudservices.md)
* [Servery aplikace spuštěné v Docker](app-insights-docker.md)
* [Jednostránkové webové aplikace](app-insights-javascript.md)
* [Sharepoint](app-insights-sharepoint.md)
* [Aplikace na ploše systému Windows](app-insights-windows-desktop.md)
* [Jiné platformy](app-insights-platforms.md)

## <a name="is-it-free"></a>Je bezplatný?

Ano, pro experimentální použití. V základní cenový plán vaše aplikace může odesílat určité příspěvek na dat každý měsíc zdarma. Volné povoleného užívání je dostatečně velký pro vývoj titulní a publikování aplikace pro malý počet uživatelů. Můžete nastavit cap s více než po zadanou dobu dat zabránit zpracovává.

Větší objemy telemetrie budou účtovat podle Gb. Poskytujeme některé tipy pro [omezit vaše poplatky](app-insights-pricing.md).

Plánu podnikového způsobuje poplatků za každý den, které každý uzel webového serveru odesílá telemetrická data. Je vhodný, pokud chcete použít průběžné exportovat ve velkém rozsahu.

[Přečtěte si cenový plán](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="how-much-is-it-costing"></a>Kolik je je výpočet nákladů?

* Otevřete **využití a odhadované náklady na stránce** stránky v prostředek Application Insights. Je graf posledního použití. Svazek cap data, můžete nastavit, pokud chcete.
* Otevřete [Azure fakturace okno](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview) zobrazíte vaší faktur mezi všechny prostředky.

## <a name="q14"></a>Co Application Insights v projektu změnit?
Podrobnosti závisí na typu projektu. Pro webovou aplikaci:

* Přidá tyto soubory do projektu:

  * ApplicationInsights.config.
  * ai.js
* Nainstaluje tyto balíčky NuGet:

  * *Application Insights API* – základní rozhraní API
  * *Application Insights API pro webové aplikace* – použité k odesílání telemetrie ze serveru
  * *Application Insights API pro aplikace JavaScript* – použité k odesílání telemetrie z klienta

    Balíčky zahrnout tyto sestavení:
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* Vloží položky do:

  * Soubor web.config
  * packages.config
* (Nové projekty pouze – pokud jste [přidat službu Application Insights do existujícího projektu][start], budete muset udělat to ručně.) Fragmenty kódu vloží do kódu klienta a serveru k chybě při inicializaci je s ID prostředku Application Insights. V aplikaci MVC, například kód je vložen do stránky předlohy Views/Shared/_Layout.cshtml

## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Jak upgradovat ze starší verze sady SDK?
Najdete v článku [poznámky k verzi](app-insights-release-notes.md) pro sadu SDK, které jsou vhodné pro váš typ aplikace.

## <a name="update"></a>Jak můžete změnit který Azure prostředek projektu odešle data?
V Průzkumníku řešení klikněte pravým tlačítkem na `ApplicationInsights.config` a zvolte **aktualizace Application Insights**. Může posílat data do existující nebo nové prostředek v Azure. V Průvodci aktualizací změní klíč instrumentace do souboru ApplicationInsights.config, která určuje, kde server SDK odešle svá data. Pokud zrušíte výběr "Aktualizovat vše", změní se také klíč, pokud se objeví v webových stránek.

## <a name="what-is-status-monitor"></a>Co je Monitorování stavu?

Aplikace na ploše, můžete použít v webový server IIS můžete nakonfigurovat Application Insights ve webové aplikace. Ho nebude shromažďovat telemetrická data: můžete zastavit, pokud nejsou konfigurace aplikace. 

[Další informace](app-insights-monitor-performance-live-website-now.md#questions).

## <a name="what-telemetry-is-collected-by-application-insights"></a>Jaké telemetrických dat shromažďovaných pomocí Application Insights?

Ze serveru webové aplikace:

* Požadavky HTTP
* [Závislosti](app-insights-asp-net-dependencies.md). Volání: databáze SQL; Volání HTTP externích služeb; Azure Cosmos DB, table, úložiště objektů blob a fronty. 
* [Výjimky](app-insights-asp-net-exceptions.md) a trasování zásobníku.
* [Čítače výkonu](app-insights-performance-counters.md) – Pokud používáte [monitorování stavu](app-insights-monitor-performance-live-website-now.md), [Azure monitorování](app-insights-azure-web-apps.md) nebo [Application Insights collectd zapisovače](app-insights-java-collectd.md).
* [Vlastní události a metriky](app-insights-api-custom-events-metrics.md) vám kód.
* [Protokoly trasování](app-insights-asp-net-trace-logs.md) Pokud nakonfigurujete příslušné kolekce.

Z [klienta webové stránky](app-insights-javascript.md):

* [Spočítá počet zobrazení stránky](app-insights-web-track-usage.md)
* [Volání AJAX](app-insights-asp-net-dependencies.md) požadavky vytvořené z spouštění skriptu.
* Stránka zobrazení načtení dat
* Počet uživatelů a relace
* [Ověřený uživatel ID](app-insights-api-custom-events-metrics.md#authenticated-users)

Z jiných zdrojů, pokud je nakonfigurovat:

* [Azure diagnostics](app-insights-azure-diagnostics.md)
* [Kontejnery docker](app-insights-docker.md)
* [Import tabulky k analýze](app-insights-analytics-import.md)
* [Log Analytics](https://azure.microsoft.com/blog/omssolutionforappinsightspublicpreview/)
* [Logstash](app-insights-analytics-import.md)

## <a name="can-i-filter-out-or-modify-some-telemetry"></a>Můžete filtrovat nebo upravovat nějaké telemetrie?

Ano, v serveru, který může zapisovat:

* Procesor telemetrie filtrovat nebo přidání vlastnosti do položky vybrané telemetrie před jejich odesláním z vaší aplikace.
* Telemetrie inicializátoru k přidání vlastností pro všechny položky telemetrie.

Další informace pro [ASP.NET](app-insights-api-filtering-sampling.md) nebo [Java](app-insights-java-filter-telemetry.md).

## <a name="how-are-city-country-and-other-geo-location-data-calculated"></a>Jak jsou vypočítávány města, země a další data geografické umístění?

Podíváme adresa IP (IPv4 nebo IPv6) webového klienta pomocí [GeoLite2](http://dev.maxmind.com/geoip/geoip2/geolite2/).

* Prohlížeč telemetrie: shromažďujeme IP adresa odesílatele.
* Server telemetrie: modul Application Insights shromažďuje IP adresu klienta. Nejsou shromažďovány, pokud `X-Forwarded-For` nastavena.

Můžete nakonfigurovat `ClientIpHeaderTelemetryInitializer` provést IP adresu z různých hlavičky. V některých systémů, například se přesune pomocí serveru proxy a načtěte vyrovnávání nebo CDN `X-Originating-IP`. [Další informace](http://apmtips.com/blog/2016/07/05/client-ip-address/).

Můžete [používat Power BI](app-insights-export-power-bi.md) zobrazit telemetrii požadavek na mapě.


## <a name="data"></a>Jak dlouho se data uchovávají v portálu? Je bezpečné?
Podívejte se na [uchovávání dat a ochrana osobních údajů][data].

## <a name="might-personally-identifiable-information-pii-be-sent-in-the-telemetry"></a>Mohou identifikovatelné osobní údaje (PII) být odeslány telemetrie?

To je možné, pokud kód odešle taková data. Může také dojít, pokud obsahovat PII variables v trasování zásobníku. Váš vývojový tým musí provést hodnocení rizik může zajistit správné zpracování PII. [Další informace o uchovávání dat a ochrany osobních údajů](app-insights-data-retention-privacy.md).

**Všechny** oktety webovou adresu klienta jsou vždy nastavená na 0, po geografické umístění atributy jsou prohledávat.

## <a name="my-ikey-is-visible-in-my-web-page-source"></a>Moje iKey je viditelná ve zdroji webové stránky. 

* Toto je běžnou praxí v sledování řešení.
* Nelze se používá ke krádeži vaše data.
* Může k zkreslit data nebo aktivační událost upozornění.
* Nebyly Slyšeli jsme, že všechny odběratel měl takovým problémům.

Vám může:

* Použití dvou samostatných iKeys (jednotlivé prostředky Application Insights) pro klienta a serveru data. Nebo
* Zápis proxy server, který běží na vašem serveru a mít webového klienta odesílat data prostřednictvím tohoto proxy serveru.

## <a name="post"></a>Jak zobrazit data POST ve vyhledávání diagnostiky?
Jsme nemáte protokolovat POST data automaticky, ale můžete použít volání TrackTrace: umístit data v parametru zprávy. Tato akce nemá omezení velikosti delší než limity pro vlastnosti string, i když ho nelze filtrovat.

## <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Použít jeden nebo více prostředků Application Insights?

Použijte jediný zdroj pro všechny součásti nebo role v rámci jedné obchodní systému. Použijte samostatný prostředky pro vývoj, testování a verze a nezávislé aplikace.

* [Přečtěte si zde diskuzi](app-insights-separate-resources.md)
* [Příklad: Cloudová služba se worker a webové role](app-insights-cloudservices.md)

## <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Dynamicky změna klíč instrumentace?

* [Tato diskuse](app-insights-separate-resources.md)
* [Příklad: Cloudová služba se worker a webové role](app-insights-cloudservices.md)

## <a name="what-are-the-user-and-session-counts"></a>Jaké jsou uživatele a relace počty?

* JavaScript SDK nastaví soubor cookie uživatele na webovému klientovi, k identifikaci stávajícím uživatelům a soubor cookie relace k seskupování aktivit.
* Pokud není žádný skript na straně klienta, můžete [nastavit soubory cookie na serveru](http://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/).
* Pokud jeden reálný uživatel používá váš web v různých prohlížečích nebo pomocí procházení v privátní nebo incognito nebo různých počítačů a potom se budou počítat více než jednou.
* Chcete-li identifikovat přihlášeného uživatele na počítače a prohlížeče, přidejte volání [setAuthenticatedUserContext()](app-insights-api-custom-events-metrics.md#authenticated-users).

## <a name="q17"></a> I povolili všechno ve službě Application Insights?
| Co byste měli vidět | Jak ji připravit | Proč byste je |
| --- | --- | --- |
| Grafy dostupnosti |[Testy webu](app-insights-monitor-web-app-availability.md) |Vědět, že vaše webová aplikace je nahoru |
| Server aplikace výkonu: odezvy,... |[Do projektu přidejte Application Insights](app-insights-asp-net.md) nebo [nainstalujte monitorování stavu AI na serveru](app-insights-monitor-performance-live-website-now.md) (nebo napsat vlastní kód pro [sledování závislostí](app-insights-api-custom-events-metrics.md#trackdependency)) |Rozpoznat problémy s výkonu |
| Telemetrických závislostí |[Nainstalujte monitorování stavu AI na serveru](app-insights-monitor-performance-live-website-now.md) |Diagnostikovat problémy s databází nebo jiné externí součásti |
| Sám trasování zásobníku výjimky |[Vložit volání TrackException ve vašem kódu](app-insights-asp-net-exceptions.md) (ale některé jsou hlášeny automaticky) |Najít a diagnostikovat výjimky |
| Vyhledávání protokolu trasování |[Přidat adaptér protokolování](app-insights-asp-net-trace-logs.md) |Diagnostikovat výjimky výkonu problémy |
| Základy použití klienta: zobrazení stránky, relace,... |[Inicializátor JavaScript ve webových stránkách](app-insights-javascript.md) |Analýza využití |
| Vlastní metriky klienta |[Sledování volání do webové stránky](app-insights-api-custom-events-metrics.md) |Vylepšení činnost koncového uživatele |
| Vlastní metriky serveru |[Sledování volání na serveru](app-insights-api-custom-events-metrics.md) |Business intelligence |

## <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Proč jsou počty v grafech vyhledávání a metriky nerovné?

[Vzorkování](app-insights-sampling.md) snižuje počet položek telemetrie (požadavků, vlastní události a tak dále), ve skutečnosti odesílaných z vaší aplikace na portál. Hledání se zobrazuje počet položek ve skutečnosti přijata. Metriky grafy, které zobrazí počet událostí se zobrazuje počet původní událostí, které došlo k chybě. 

Každá položka, která se přenášejí má u sebe `itemCount` představuje vlastnost, která ukazuje, kolik Původní události této položky. Abyste mohli pozorovat vzorkování v operaci, můžete spustit tento dotaz v Analytics:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


## <a name="automation"></a>Automation

### <a name="configuring-application-insights"></a>Konfigurace služby Application Insights

Můžete [psaní skriptů prostředí PowerShell](app-insights-powershell.md) pomocí nástroje Sledování prostředků Azure pro:

* Vytvořte a aktualizují prostředky s Application Insights.
* Nastavte cenový plán.
* Získejte klíč instrumentace.
* Přidání metriky oznámení.
* Přidáte test dostupnosti.

Nelze nastavit metrika Explorer sestavy nebo nastavit průběžné export.

### <a name="querying-the-telemetry"></a>Dotazování telemetrii

Použití [REST API](https://dev.applicationinsights.io/) ke spuštění [Analytics](app-insights-analytics.md) dotazy.

## <a name="how-can-i-set-an-alert-on-an-event"></a>Jak můžete nastavit upozornění na událost?

Azure výstrahy jsou pouze o metrikách. Vytvoření vlastní metriky protne hodnota prahové hodnoty při každém výskytu události. Nastavte na metriku výstrahu. Všimněte si, že: se vám zobrazí oznámení pokaždé, když metrika protne prahovou hodnotu v obou směrech; nezobrazí se oznámení do první překročení, bez ohledu na to, jestli je počáteční hodnota vysoká nebo Nízká; je vždy latence několik minut.

## <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Existují poplatky za přenos dat mezi webové aplikace Azure a Application Insights?

* Pokud vaše webové aplikace Azure je hostována v datovém centru je koncový bod shromažďování Application Insights, není nijak zpoplatněn. 
* Pokud ve vašem datovém centru hostitele není žádný koncový bod kolekce, pak je zpoplatněná telemetrii aplikace [Azure odchozí poplatky](https://azure.microsoft.com/pricing/details/bandwidth/).

To nezávisí na je hostitelem prostředku Application Insights. Právě závisí na distribuci naše koncové body.

## <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Mohla odesílat telemetrii do portálu služby Application Insights?

Doporučujeme používat naše sady SDK a používat [rozhraní API sady SDK](app-insights-api-custom-events-metrics.md). Variant sady SDK pro různé [platformy](app-insights-platforms.md). Tyto sady SDK zpracovávat ukládání do vyrovnávací paměti, komprese, omezení šířky pásma, opakování a tak dále. Ale [přijímání schématu](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema) a [koncový bod protokolu](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) jsou veřejné.

## <a name="can-i-monitor-an-intranet-web-server"></a>Můžete sledovat webový server intranetu?

Zde jsou dvě metody:

### <a name="firewall-door"></a>Dvířka brány firewall

Povolit váš webový server k odeslání telemetrie do našich koncových bodů https://dc.services.visualstudio.com:443 a https://rt.services.visualstudio.com:443. 

### <a name="proxy"></a>Proxy server

Směrujte provoz ze serveru do brány v intranetu, podle overwritting tato nastavení v příkladu souboru ApplicationInsights.config. Pokud tyto vlastnosti "Koncový bod" nejsou v souboru config, tyto třídy, budou používat výchozí hodnoty zobrazené v následujícím příkladu.

#### <a name="example-applicationinsightsconfig"></a>Příklad souboru ApplicationInsights.config:
```xml
<ApplicationInsights>
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

_Poznámka: ApplicationIdProvider je k dispozici od v2.6.0_

Vaše brána by měl směrovat přenosy do https://dc.services.visualstudio.com:443

Nahraďte hodnoty výš se: `http://<your.gateway.address>/<relative path>`
 
Příklad: 
```
http://<your.gateway.endpoint>/v2/track 
http://<your.gateway.endpoint>/api/profiles/{0}/apiId
```




## <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>Můžete spouštět testy dostupnosti webu na intranetový server?

Naše [webové testy](app-insights-monitor-web-app-availability.md) spustit na bodů přítomnosti, které jsou distribuovány po celém světě. Existují dvě řešení:

* Brány firewall dveře – povolit požadavky na váš server z [dlouhé a změnit seznam webové testovacích agentů](app-insights-ip-addresses.md).
* Napište vlastní kód pro odesílání pravidelných požadavků na server z uvnitř intranetu. Můžete spustit webových testů Visual Studia pro tento účel. Zkušební zařízení může odeslat výsledky do služby Application Insights pomocí rozhraní API TrackAvailability().

## <a name="more-answers"></a>Další odpovědi
* [Fórum Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

<!--Link references-->

[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md