---
title: Azure Application Insights – nejčastější dotazy | Dokumentace Microsoftu
description: Nejčastější dotazy ohledně služby Application Insights.
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
ms.openlocfilehash: 17774ea74c2462cb5b8a9dfe638b3dec02499d81
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55999160"
---
# <a name="application-insights-frequently-asked-questions"></a>Application Insights: Nejčastější dotazy

## <a name="configuration-problems"></a>Problémy s konfigurací
*Mám potíže při nastavení Moje:*

* [Aplikace .NET](asp-net-troubleshoot-no-data.md)
* [Monitorování již spuštěné aplikace](monitor-performance-live-website-now.md#troubleshoot)
* [Diagnostika Azure](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)
* [Webová aplikace Java](java-troubleshoot.md)

*Můžu získat žádná data ze svého serveru*

* [Nastavení výjimek brány firewall](ip-addresses.md)
* [Nastavení serveru technologie ASP.NET](monitor-performance-live-website-now.md)
* [Nastavení serveru Java](java-agent.md)

## <a name="can-i-use-application-insights-with-"></a>Je možné použít s Application Insights...?

* [Webové aplikace na serveru služby IIS – místní nebo ve virtuálním počítači](asp-net.md)
* [Webové aplikace v Javě](java-get-started.md)
* [Aplikace v Node.js](nodejs.md)
* [Web apps v Azure](azure-web-apps.md)
* [Cloudové služby v Azure](cloudservices.md)
* [Servery aplikace spuštěné v Dockeru](docker.md)
* [Webové jednostránkové aplikace](javascript.md)
* [SharePoint](sharepoint.md)
* [Aplikace pro klasickou plochu Windows](windows-desktop.md)
* [Jiné platformy](platforms.md)

## <a name="is-it-free"></a>Je bezplatný?

Ano, použít experimentální. V základní cenový plán vaše aplikace může odesílat na určité příděl dat měsíčně zdarma. Volný příděl je dostatečně velký pro vývoj titulní a publikování aplikace pro malý počet uživatelů. Můžete omezit více než zadaného množství dat zabránit právě zpracovává.

Větší objemy telemetrických dat se účtují za těchto Gb. Budeme poskytovat Rady o tom, jak [omezit poplatky](pricing.md).

Plán Enterprise účtují poplatky za každý den, každý uzel webového serveru odesílá telemetrická data. Je vhodné, pokud chcete použít průběžného exportu ve velkém měřítku.

[Čtení cenového plánu](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="how-much-is-it-costing"></a>Kolik to je cenu?

* Otevřít **využití a odhadované náklady na stránce** v prostředku Application Insights. Je graf z posledního použití. Limit objemu dat, můžete nastavit, pokud chcete.
* Otevřít [fakturace Azure okno](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview) zobrazíte vaší faktury přes všechny prostředky.

## <a name="q14"></a>Co Application Insights v projektu změnit?
Podrobnosti závisí na typu projektu. Pro webovou aplikaci:

* Tyto soubory se přidá do vašeho projektu:

  * ApplicationInsights.config.
  * ai.js
* Nainstaluje tyto balíčky NuGet:

  * *API pro Application Insights* – základního rozhraní API
  * *Rozhraní API pro Application Insights pro webové aplikace* – slouží k odesílání telemetrických dat ze serveru
  * *Rozhraní API pro Application Insights pro aplikace JavaScript* – slouží k odesílání telemetrie z klienta

    Balíčky patří tato sestavení:
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* Vložení položky do:

  * Soubor web.config
  * packages.config
* (Nové projekty pouze – pokud jste [přidat službu Application Insights do existujícího projektu][start], budete muset provést ručně.) Vloží fragmenty do kódu klienta a serveru inicializace s ID prostředku Application Insights. V aplikaci MVC, například kód je vložen do stránky předlohy Views/Shared/_Layout.cshtml

## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Jak upgradovat ze starší verze sady SDK?
Zobrazit [poznámky k verzi](release-notes.md) sady SDK, které jsou vhodné pro váš typ aplikace.

## <a name="update"></a>Změna které prostředků Azure Můj projekt odesílá data do
V Průzkumníku řešení klikněte pravým tlačítkem na `ApplicationInsights.config` a zvolte **aktualizace Application Insights**. Odeslat data do stávajícího nebo nového prostředku v Azure. Průvodce aktualizací změní Instrumentační klíč v souboru ApplicationInsights.config, která určuje, kde server SDK odešle data. Pokud zrušíte "Aktualizovat vše", se také změní klíče, kde se zobrazí na webových stránkách.

## <a name="what-is-status-monitor"></a>Co je Monitorování stavu?

Desktopové aplikace, které vám pomohou při konfiguraci Application Insights ve službě web apps můžete ve webovém serveru služby IIS. Sobě telemetrii neshromažďuje: můžete ho zastavit při nejsou konfiguraci aplikace. 

[Další informace](monitor-performance-live-website-now.md#questions).

## <a name="what-telemetry-is-collected-by-application-insights"></a>Jaké telemetrická data se shromažďují pomocí Application Insights?

Ze serveru webové aplikace:

* Požadavky HTTP
* [Závislosti](asp-net-dependencies.md). Volání: Databáze SQL. HTTP volání externích služeb; Azure Cosmos DB, table, úložiště objektů blob a fronty. 
* [Výjimky](asp-net-exceptions.md) a trasování zásobníku.
* [Čítače výkonu](performance-counters.md) – Pokud používáte [monitorování stavu](monitor-performance-live-website-now.md), [monitorování Azure](azure-web-apps.md), nebo [Application Insights shromážděná zapisovače](java-collectd.md).
* [Vlastní události a metriky](api-custom-events-metrics.md) , že kód.
* [Protokoly trasování](asp-net-trace-logs.md) při konfiguraci příslušnou kolekcí.

Z [klientské webové stránky](javascript.md):

* [Počty zobrazení stránek](usage-overview.md)
* [Volání AJAX](asp-net-dependencies.md) žádosti ze spuštěného skriptu.
* Data načtení zobrazení stránky
* Počty uživatelů a relací
* [ID ověřeného uživatele](api-custom-events-metrics.md#authenticated-users)

Z jiných zdrojů, pokud je konfigurace:

* [Diagnostika Azure](../platform/diagnostics-extension-to-application-insights.md)
* [Importovat do analýzy](../platform/data-collector-api.md)
* [Log Analytics](../platform/data-collector-api.md)
* [Logstash](../platform/data-collector-api.md)

## <a name="can-i-filter-out-or-modify-some-telemetry"></a>Můžete filtrovat nebo upravit nějaké telemetrie?

Ano, na serveru můžete psát:

* Procesor telemetrická data filtrovat nebo přidání vlastnosti do telemetrická data vybrané položky před jejich odesláním z vaší aplikace.
* Inicializátor telemetrie pro přidání vlastnosti pro všechny položky telemetrie.

Další informace pro [ASP.NET](api-filtering-sampling.md) nebo [Java](java-filter-telemetry.md).

## <a name="how-are-city-country-and-other-geo-location-data-calculated"></a>Jak se počítají města, země a další geografické umístění dat?

Podíváme se IP adresa (IPv4 nebo IPv6) webového klienta pomocí [GeoLite2](http://dev.maxmind.com/geoip/geoip2/geolite2/).

* Telemetrická data prohlížeče: Můžeme shromažďovat IP adresu odesílatele.
* Telemetrie serveru: Modul Application Insights shromažďuje IP adresu klienta. Nejsou shromažďovány Pokud `X-Forwarded-For` nastavena.

Můžete nakonfigurovat `ClientIpHeaderTelemetryInitializer` mají získat IP adresu z jiné záhlaví. U některých systémů, například se přesune na proxy serveru, načtěte nástroj pro vyrovnávání nebo sítěmi CDN, aby `X-Originating-IP`. [Další informace](https://apmtips.com/blog/2016/07/05/client-ip-address/).

Je možné [pomocí Power BI](export-power-bi.md ) zobrazíte telemetrických dat požadavek na mapě.


## <a name="data"></a>Jak dlouho se data uchovávají v portálu? Je bezpečný?
Podívejte se na [uchovávání dat a ochrany osobních údajů][data].

## <a name="could-personal-data-be-sent-in-the-telemetry"></a>Může v telemetrii posílat osobní údaje?

Je to možné, pokud váš kód odesílá tyto údaje. To může také dojít, pokud proměnné v trasování zásobníku obsahují osobní údaje. Váš vývojový tým by měl provést vyhodnocení rizik zajistit správné zpracování osobních údajů. [Další informace o uchovávání dat a ochrany osobních údajů](data-retention-privacy.md).

**Všechny** oktety webovou adresu klienta jsou vždy nastaveny na 0, poté, co jsou vyhledány atributů geografického umístění.

## <a name="my-ikey-is-visible-in-my-web-page-source"></a>Moje Instrumentační klíč je viditelná ve zdroji webové stránky. 

* To je běžný postup při sledování řešení.
* Nelze se používá ke krádeži vaše data.
* Se používal k zkosení upozornění dat nebo aktivační událost.
* Ještě jsme slyšeli, že každý zákazník se takovým problémům došlo.

Můžete:

* Dva samostatné Instrumentační klíče (oddělení prostředků Application Insights), použijte pro data s klientem a serverem. Nebo
* Zápis proxy server, na kterém běží na vašem serveru a mít webového klienta posílat data přes tento proxy server.

## <a name="post"></a>Jak můžu zobrazit následných dat v diagnostickém vyhledávání?
Automaticky jsme není protokolování dat o příspěvek, ale můžete použít volání TrackTrace: umístit data v parametru zprávy. Tato akce nemá omezení velikosti delší než omezení pro vlastnosti řetězce, i když ho nelze filtrovat.

## <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Použít jeden nebo více prostředků Application Insights?

Použijte jeden prostředek pro všechny součásti nebo role v jedné organizační systému. Použití samostatné prostředky pro vývoj, testování a verze a pro nezávislé aplikace.

* [Viz diskuze v tomto poli](separate-resources.md)
* [Příklad – cloudové služby pomocí rolí pracovního procesu a web](cloudservices.md)

## <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Jak můžu dynamicky měnit Instrumentační klíč?

* [Tato diskuse](separate-resources.md)
* [Příklad – cloudové služby pomocí rolí pracovního procesu a web](cloudservices.md)

## <a name="what-are-the-user-and-session-counts"></a>Co jsou uživatelů a relací se počítá?

* JavaScript SDK nastaví soubor cookie uživatele na webového klienta, k identifikaci vracející uživatelů a souboru cookie relace k seskupování aktivit.
* Pokud neexistuje žádný skript na straně klienta, můžete si [nastavení souborů cookie na serveru](https://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/).
* Pokud jeden uživatelů používá váš web v různých prohlížečích nebo pomocí procházení v private nebo incognito nebo různých počítačů a potom se bude počítat více než jednou.
* Chcete-li identifikuje přihlášeného uživatele na počítače a prohlížeče, přidejte volání do [setAuthenticatedUserContext()](api-custom-events-metrics.md#authenticated-users).

## <a name="q17"></a> Můžu povolili všechno, co ve službě Application Insights?
| Co byste měli vidět | Jak se dá stáhnout | Proč chcete |
| --- | --- | --- |
| Grafy dostupnosti |[Webové testy](monitor-web-app-availability.md) |Informace, že se že vaše webová aplikace je v provozu |
| Výkon serveru aplikace –: doby odezvy,... |[Přidejte Application Insights do projektu](asp-net.md) nebo [nainstalujte monitorování stavu AI na serveru](monitor-performance-live-website-now.md) (nebo napsat vlastní kód pro [sledovat závislosti](api-custom-events-metrics.md#trackdependency)) |Zjišťování problémů s výkonem |
| Telemetrie závislostí |[Nainstalujte na server monitorování stavu AI](monitor-performance-live-website-now.md) |Diagnostikovat problémy s databází nebo dalších externích součástí |
| Získat trasování zásobníku z výjimek |[Vložit volání TrackException ve vašem kódu](asp-net-exceptions.md) (ale některé automaticky označené) |Detekujte a Diagnostikujte výjimky |
| Hledání trasování protokolu |[Přidat adaptér protokolování](asp-net-trace-logs.md) |Diagnostikujte výjimky, problémů s výkonem |
| Základní informace o použití klienta: zobrazení stránky, relací... |[Inicializátor JavaScript na webových stránkách](javascript.md) |Analýza využití |
| Vlastní metriky klienta |[Sledování volání na webových stránkách](api-custom-events-metrics.md) |Vylepšit uživatelské prostředí |
| Vlastní metrik serveru |[Sledovací volání na serveru](api-custom-events-metrics.md) |Business intelligence |

## <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Počty v hledání metriky a grafy nerovnost

[Vzorkování](sampling.md) snižuje počet položek telemetrie (žádosti, vlastní události a tak dále), které jsou ve skutečnosti odesílány z vaší aplikace na portál. Ve službě Search uvidíte počet položek ve skutečnosti přijata. V grafy metrik, které zobrazují počet událostí uvidíte počet původní událostí, ke kterým došlo. 

Každou položku, která je přenášet mělo `itemCount` reprezentuje vlastnost, která ukazuje, kolik Původní události danou položku. Sledovat vzorkování v operaci, můžete spustit tento dotaz v Analytics:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


## <a name="automation"></a>Automation

### <a name="configuring-application-insights"></a>Konfigurace Application Insights

Je možné [psaní skriptů prostředí PowerShell](powershell.md) pomocí monitorování prostředků Azure:

* Vytvoření a aktualizaci prostředků Application Insights.
* Nastavte cenového plánu.
* Získejte klíč instrumentace.
* Přidáte upozornění metriky.
* Přidáte test dostupnosti.

Nelze nastavit Průzkumník metrik sestavy nebo nastavit průběžný export.

### <a name="querying-the-telemetry"></a>Dotazování na telemetrická data

Použití [rozhraní REST API](https://dev.applicationinsights.io/) spuštění [Analytics](analytics.md) dotazy.

## <a name="how-can-i-set-an-alert-on-an-event"></a>Jak můžu nastavit výstrahu pro událost?

Upozornění Azure jsou pouze na metriky. Vytvoření vlastní metriky, které překročí mezní hodnotu hodnotu při každém výskytu události. Potom nastavení upozornění na metriku. Všimněte si, že: vám pošle oznámení, kdykoliv metrika překročí prahovou hodnotu v obou směrech; nezískáte oznámení až do první překračování, bez ohledu na to, zda je počáteční hodnota vysoká nebo Nízká; je vždy latenci několik minut.

## <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Jsou nějaké poplatky za přenos dat mezi webovou aplikaci Azure a službou Application Insights?

* Pokud vaše webová aplikace Azure je hostována v datovém centru tam, kde je koncový bod kolekce Application Insights, se neúčtují žádné poplatky. 
* Pokud neexistuje žádný koncový bod kolekce ve vašem datovém centru hostitele a pak telemetrií vaší aplikace bude mít za následek [odchozí poplatky za Azure](https://azure.microsoft.com/pricing/details/bandwidth/).

To nezávisí na kde se hostuje váš prostředek Application Insights. Právě závisí na distribuci naše koncových bodů.

## <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Mohla odesílat telemetrii na portál Application Insights?

Doporučujeme použít naše sady SDK a použít [rozhraní API sady SDK](api-custom-events-metrics.md). Existují varianty sady SDK pro různé [platformy](platforms.md). Tyto sady SDK pro zpracování, ukládání do vyrovnávací paměti, komprese, omezení šířky pásma, opakované pokusy a tak dále. Ale [ingestování schématu](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema) a [protokol koncového bodu](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) byly veřejné.

## <a name="can-i-monitor-an-intranet-web-server"></a>Můžete monitorovat webový server intranetu?

Ano, ale budete muset povolit provoz do našich služeb výjimky brány firewall nebo proxy přesměrování.
- QuickPulse `https://rt.services.visualstudio.com:443` 
- ApplicationIdProvider `https://dc.services.visualstudio.com:443` 
- TelemetryChannel `https://dc.services.visualstudio.com:443` 


Projděte si náš úplný seznam služeb a IP adres [tady](../../azure-monitor/app/ip-addresses.md).

### <a name="firewall-exception"></a>Výjimka brány firewall

Povolit webový server k odesílání telemetrie na naše koncových bodů. 

### <a name="proxy-redirect"></a>Přesměrování proxy

Směrování provozu do brány ve vašem intranetu přepsáním koncových bodů v konfiguraci serveru.
Pokud tyto vlastnosti "Koncového bodu" se nenachází v konfiguraci, tyto třídy použije výchozí hodnoty uvedené níže v souboru ApplicationInsights.config v příkladu. 

Vaše brána by měl směrovat provoz na základní adresu naše koncového bodu. V konfiguraci, nahraďte výchozí hodnoty s `http://<your.gateway.address>/<relative path>`.


#### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>Příklad souboru ApplicationInsights.config s výchozí koncové body:
```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector"/>
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

_Poznámka: ApplicationIdProvider je k dispozici od v2.6.0_


 

## <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>Můžete spustit testy dostupnosti webu na intranetový server?

Naše [webové testy](monitor-web-app-availability.md) spouštět míst, které jsou distribuovány na celém světě. Existují dvě řešení:

* Dvířka knihovny s branou firewall – povolit požadavky na server z [dlouhé a změnit seznam webových testovacích agentů](ip-addresses.md).
* Psaní vlastního kódu k odesílání pravidelných požadavků na server z uvnitř intranetu. Můžete spustit webových testů Visual Studia pro tento účel. Tester výsledky odeslat do Application Insights pomocí rozhraní API TrackAvailability().

## <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>Jak dlouho trvá telemetrie, které se mají shromažďovat?

Většina dat Application Insights má latence méně než 5 minut. Některá data může trvat déle; obvykle větší soubory protokolu. Další informace najdete v tématu [Application Insights SLA](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/).

## <a name="more-answers"></a>Další odpovědi
* [Fórum pro Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

<!--Link references-->

[data]: data-retention-privacy.md
[platforms]: platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md
