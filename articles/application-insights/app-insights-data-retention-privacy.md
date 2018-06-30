---
title: Uchovávání dat a úložiště v Azure Application Insights | Microsoft Docs
description: Prohlášení o zásadách uchovávání dat a ochrana osobních údajů
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: a6268811-c8df-42b5-8b1b-1d5a7e94cbca
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/29/2018
ms.author: mbullwin
ms.openlocfilehash: 897671ef592ac691402a4e452f7a0baa04aa228a
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37129053"
---
# <a name="data-collection-retention-and-storage-in-application-insights"></a>Shromažďování, uchování a ukládání dat v nástroji Application Insights


Při instalaci [Azure Application Insights] [ start] SDK v aplikaci se odesílá telemetrii týkající se vaší aplikace do cloudu. Samozřejmě zodpovědná vývojáři chcete vědět, jaká data se odesílají přesně, co se stane, že k datům a jak můžete zachovat kontrolu nad jeho. Konkrétně může být odeslána citlivá data, kde je uložený a jak zabezpečení je? 

První, krátké odpovědí:

* Standardní telemetrie modulů, které se spouštějí "předinstalované" je nepravděpodobné, že službě odesílat citlivá data. Telemetrie se týká zatížení, metriky výkonu a využití, sestavy výjimek a další diagnostická data. Hlavní uživatel data zobrazená v diagnostické zprávy jsou adresy URL; ale aplikace nesmí v žádném případě put citlivá data v prostém textu v adrese URL.
* Můžete napsat kód, který odesílá další vlastní telemetrii vám pomohou při sledování využití a Diagnostika. (Toto rozšíření je skvělé funkce Application Insights.) Je možné, omylem, zápis tento kód tak, že obsahují osobní a další citlivá data. Pokud vaše aplikace funguje s taková data, byste měli použít pro všechny kód, který napsat důkladné zkontrolujte procesy.
* Při vývoji a testování vaší aplikace, je snadné zkontrolovat, co Probíhá odesílání sadou SDK. Data se zobrazí v oknech výstupu ladění IDE a prohlížeče. 
* Data se nachází v [Microsoft Azure](http://azure.com) servery v USA nebo v Evropě. (Ale aplikace můžou běžet kdekoli.) Azure má [silné zabezpečení zpracovává a splňuje širokou škálu standardů dodržování předpisů](https://azure.microsoft.com/support/trust-center/). Pouze vám a vašemu týmu určené mít přístup k datům. Zaměstnanci Microsoftu může mít omezený přístup k němu pouze za určité omezené okolností s vašeho vědomí. Je šifrovaný během přenosu, i když není v serveru.

Zbývající část tohoto článku popisuje podrobněji na tyto odpovědi. Je určený jako samostatné, tak, aby je bylo možné zobrazit s kolegy, kteří nejsou součástí vašeho týmu.

## <a name="what-is-application-insights"></a>Co je Application Insights?
[Azure Application Insights] [ start] je služba poskytovaná společnosti Microsoft, který vám pomůže zvýšit výkon a použitelnost vaše živé aplikace. Sleduje aplikace vždy, které běží, během testování a po publikována nebo nasazená. Application Insights vytvoří grafů a tabulek, které vás, například zobrazit, jaké denní dobu získat většina uživatelů, jak reaguje aplikace je a jak dobře, jsou-li obsloužených žádné externí služby, které závisí na. Pokud dojde k chybě, chyby nebo problémy s výkonem, můžete hledat data telemetrie podrobně při určování příčin. A služba je odešle e-mailů, pokud jsou všechny změny v dostupnosti a výkonu vaší aplikace.

Chcete-li získat tuto funkci, nainstalujte Application Insights SDK v aplikaci, která se stane součástí jeho kód. Pokud vaše aplikace běží, sadu SDK monitoruje své činnosti a odesílá telemetrická data do služby Application Insights. Toto je Cloudová služba hostovaná společností [Microsoft Azure](http://azure.com). (Ale Application Insights funguje u všech aplikací, nikoli pouze ty, které jsou hostované v Azure.)

![Sada SDK ve vaší aplikaci odesílá telemetrická data do služby Application Insights.](./media/app-insights-data-retention-privacy/01-scheme.png)

Službu Application Insights ukládá a analyzuje telemetrii. Pokud chcete zobrazit analýzu nebo vyhledávání prostřednictvím uložené telemetrie, přihlaste se k účtu Azure a otevřete prostředek Application Insights pro vaši aplikaci. Přístup k datům můžou taky sdílet s ostatními členy týmu nebo s Azure stanoveným příjemcům.

Můžete mít data exportovaná z službu Application Insights, například do databáze nebo externí nástroje. Každý nástroj poskytnete speciální klíč, který získá od služby. V případě potřeby se dají odvolávat klíč. 

Sadách Application Insights SDK jsou k dispozici pro celou řadu typů aplikací: webové služby hostované v vaše servery J2EE nebo v technologii ASP.NET, nebo v Azure; webové klienti – to znamená, kód spuštěný na webové stránce; desktopové aplikace a služby; aplikace zařízení, například Windows Phone, iOS a Android. Všechny odesílání telemetrie na stejnou službu.

## <a name="what-data-does-it-collect"></a>Jaká data ho shromáždit?
### <a name="how-is-the-data-is-collected"></a>Jak jsou data se shromažďují?
Existují tři zdroje dat:

* Sadu SDK, které můžete integrovat s vaší aplikací buď [vývojem](app-insights-asp-net.md) nebo [v době běhu](app-insights-monitor-performance-live-website-now.md). Existují různé sady SDK pro typy jinou aplikaci. K dispozici je také [SDK pro webové stránky](app-insights-javascript.md), což způsobí načtení do prohlížeče uživatele end společně s stránky.
  
  * Každý sada SDK obsahuje řadu [moduly](app-insights-configuration-with-applicationinsights-config.md), které používají různé technologie ke shromažďování různé typy telemetrie.
  * Pokud instalujete sadu SDK v vývoj, můžete jeho rozhraní API k odeslání vlastní telemetrie, kromě standardní moduly. Tato vlastní telemetrii může obsahovat data, která chcete odeslat.
* V některých webových serverů existují také agentů, které běží souběžně s aplikací a odesílat telemetrická data o využití procesoru, paměti a sítě obsazení. Například virtuální počítače Azure, hostitelů Docker a [servery J2EE](app-insights-java-agent.md) může mít tyto agenty.
* [Testy dostupnosti](app-insights-monitor-web-app-availability.md) společnosti Microsoft, který odesílat žádosti do vaší webové aplikace v pravidelných intervalech spouštějí procesy. Výsledky se odesílají do služby Application Insights.

### <a name="what-kinds-of-data-are-collected"></a>Jaké druhy dat se shromažďují?
Jsou hlavních kategorií:

* [Webový server telemetrie](app-insights-asp-net.md) -požadavky HTTP.  Identifikátor URI, doba trvání zpracování požadavku, kód odpovědi, IP adresa klienta. Id relace.
* [Webové stránky](app-insights-javascript.md) -stránky, uživatele a počty relací. Časů načtení stránky. Výjimky. Volání AJAX.
* Výkon čítače - paměti, procesoru, vstupně-výstupní operace, obsazení sítě.
* Klient a server kontext - OS, národní prostředí, typ zařízení, prohlížeč, rozlišení obrazovky.
* [Výjimky](app-insights-asp-net-exceptions.md) a havárií - **výpisy zásobníku**, sestavení id, typ procesoru. 
* [Závislosti](app-insights-asp-net-dependencies.md) -volání externích služeb, jako je například REST, SQL, AJAX. Identifikátor URI nebo připojovací řetězec, doba trvání, úspěch, příkaz.
* [Testy dostupnosti](app-insights-monitor-web-app-availability.md) – doba trvání testu a kroky, odpovědi.
* [Protokoly trasování](app-insights-asp-net-trace-logs.md) a [vlastní telemetrii](app-insights-api-custom-events-metrics.md) - **nic kódu do protokolů nebo telemetrie**.

[Další podrobnosti](#data-sent-by-application-insights).

## <a name="how-can-i-verify-whats-being-collected"></a>Jak lze ověřit, co jsou shromažďována?
Pokud vyvíjíte aplikace pomocí sady Visual Studio, spusťte aplikaci v režimu ladění (F5). Telemetrie se zobrazí v okně výstupu. Odtud můžete ho zkopírovat a naformátujte ho jako JSON pro snadné kontroly. 

![](./media/app-insights-data-retention-privacy/06-vs.png)

V okně diagnostické existuje také srozumitelnější zobrazení.

Pro webové stránky otevřete okno prohlížeče na ladění.

![Stisknutím klávesy F12 a otevřete kartu sítě.](./media/app-insights-data-retention-privacy/08-browser.png)

### <a name="can-i-write-code-to-filter-the-telemetry-before-it-is-sent"></a>Můžete napsat kód pro filtrování telemetrii před odesláním?
By to bylo možné zápisem [modulu plug-in procesoru telemetrie](app-insights-api-filtering-sampling.md).

## <a name="how-long-is-the-data-kept"></a>Jak dlouho se data uchovávají?
Nezpracovaná data body (to znamená, položky, které se můžete dotazovat v analýzy a zkontrolovat ve vyhledávání) jsou zachovány po dobu 90 dnů. Pokud potřebujete ponechat data delší, než je, můžete použít [průběžné export](app-insights-export-telemetry.md) a zkopírujte ho do účtu úložiště.

Agregovaná data (to znamená, počty, průměry a další statistické data, která se zobrazí v Průzkumníku metrika) jsou uchovány v interval 1 minutu po dobu 90 dnů.

## <a name="who-can-access-the-data"></a>Kdo má přístup k datům?
Data jsou viditelné pro vás, a pokud máte účet organizace, členové týmu. 

Může exportovat vámi a členové týmu a může být zkopírován do jiných umístění a k předání jiní lidé.

#### <a name="what-does-microsoft-do-with-the-information-my-app-sends-to-application-insights"></a>K čemu Microsoft slouží s informacemi, které aplikace odesílá do služby Application Insights?
Společnost Microsoft používá data pouze, aby bylo možné poskytnout službu pro vás.

## <a name="where-is-the-data-held"></a>Kde se nachází data?
* V USA, Evropa nebo jihovýchodní Asie. Můžete vybrat umístění, když vytvoříte nový prostředek Application Insights. 


#### <a name="does-that-mean-my-app-has-to-be-hosted-in-the-usa-europe-or-southeast-asia"></a>Znamená to, že Moje aplikace musí být hostované v USA, Evropa nebo jihovýchodní Asie?
* Ne. Aplikace, můžete spustit v hostitelů místně nebo v cloudu.

## <a name="how-secure-is-my-data"></a>Do jaké míry je svá data?
Application Insights je služby Azure. Zásady zabezpečení jsou popsány v tématu [Azure zabezpečení, ochrany osobních údajů a dodržování předpisů dokumentu white paper](http://go.microsoft.com/fwlink/?linkid=392408).

Jsou data uložena v serverů Microsoft Azure. Pro účty na portálu Azure, účet omezení jsou popsána v [Azure zabezpečení, ochrany osobních údajů a dodržování předpisů dokumentu](http://go.microsoft.com/fwlink/?linkid=392408).

Přístup k datům zaměstnanců společnosti Microsoft je omezen. Jsme přístup k datům jenom s vaším svolením a pokud je potřeba podporu služby Application Insights. 

Data v agregace napříč aplikacemi všech našich zákazníků (například datové sazby a průměrná velikost trasování) se používají ke zlepšení Application Insights.

#### <a name="could-someone-elses-telemetry-interfere-with-my-application-insights-data"></a>By mohla někoho jiného telemetrie narušovat svá data Application Insights?
Další telemetrické se může odeslat ke svému účtu pomocí klíč instrumentace, který naleznete v kódu webové stránky. S dostatek další data nebude vaše metriky představují správně, výkonu a využití vaší aplikace.

Pokud sdílíte kód s jinými projekty, nezapomeňte odebrat klíč instrumentace.

## <a name="is-the-data-encrypted"></a>Je zašifrovaná data?
Není v serverech v současné době.

Všechna data se šifrují, když se přesunuje mezi datovými centry.

#### <a name="is-the-data-encrypted-in-transit-from-my-application-to-application-insights-servers"></a>Má tato data šifrovat během přenosu z mé aplikace na servery Application Insights?
Ano, používáme protokol https k odesílání dat do portálu od skoro všech sad SDK, včetně webových serverů, zařízení a HTTPS webové stránky. Jedinou výjimkou je dat odesílaných ze prostý webové stránky HTTP.

## <a name="how-do-i-send-data-to-application-insights-using-tls-12"></a>Jak se odesílají data do služby Application Insights pomocí protokolu TLS 1.2?

Zajistit zabezpečení dat při přenosu do koncových bodů služby Application Insights, doporučujeme důrazně zákazníkům, nakonfigurovat jejich aplikace použijte alespoň zabezpečení TLS (Transport Layer) 1.2. Chyba zabezpečení byly zjištěny starší verze protokolu TLS/Secure Sockets Layer (SSL) a když pracují stále aktuálně povolit zpětné kompatibility, jsou **nedoporučuje**, a v odvětví je rychle se měnící ukončil podpory pro tyto starší protokoly. 

[Council standardy zabezpečení PCI](https://www.pcisecuritystandards.org/) nastavil [termínu 30. června 2018](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) zakázat starší verze na více bezpečné protokoly TLS/SSL a upgradu. Jakmile Azure zahodí podporuje starší verze, pokud vaše aplikace nebo klienti nemůžou komunikovat přes alespoň TLS 1.2 již nebude možné odesílat data do služby Application Insights. Přístupů, které můžete použít k testování a validaci podporu protokolu TLS vaší aplikace se bude lišit v závislosti na operačním systému nebo platformy, jakož i jazyk nebo rozhraní, které vaše aplikace používá.

Nedoporučujeme explicitně nastavení aplikace používat jenom TLS 1.2, pokud není nezbytně nutné, protože to může dojít k narušení zabezpečení na úrovni funkcí platformy, které vám umožní automaticky zjistit a využít výhod novější bezpečnější protokoly, které jsou zrovna k dispozici jako je například TLS 1.3. Doporučujeme provádět důkladné auditu kódu vaší aplikace zkontrolujte hardcoding konkrétních verzí protokolu TLS/SSL.

### <a name="platformlanguage-specific-guidance"></a>Konkrétní pokyny platformy nebo jazyk

|Platforma/jazyk | Podpora | Další informace |
| --- | --- | --- |
| Azure App Services  | Podporované, konfigurace mohou být vyžadovány. | Podpora byl oznámen v dubnu 2018. Přečtěte si sdělení pro [podrobnosti konfigurace](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/).  |
| Aplikace Azure – funkce | Podporované, konfigurace mohou být vyžadovány. | Podpora byl oznámen v dubnu 2018. Přečtěte si sdělení pro [podrobnosti konfigurace](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/). |
|.NET | Podporované, konfigurace se liší podle verze. | Informace o podrobnou konfiguraci pro rozhraní .NET 4.7 a starší verze najdete v části [tyto pokyny](https://docs.microsoft.com/en-us/dotnet/framework/network-programming/tls#support-for-tls-12).  |
|Monitorování stavu | Požadována podporované konfigurace. | Monitorování stavu spoléhá na [konfigurace operačního systému](https://docs.microsoft.com/en-us/windows-server/security/tls/tls-registry-settings) + [konfigurace .NET](https://docs.microsoft.com/en-us/dotnet/framework/network-programming/tls#support-for-tls-12) podpory protokolu TLS 1.2.
|Node.js |  Podporované v v10.5.0, konfigurace mohou být vyžadovány. | Použití [oficiální dokumentaci Node.js TLS/SSL](https://nodejs.org/api/tls.html) pro všechny konfigurace pro konkrétní aplikace. |
|Java | Podporované, v byla přidána podpora JDK pro TLS 1.2 [aktualizace JDK 6 121](http://www.oracle.com/technetwork/java/javase/overview-156328.html#R160_121) a [JDK 7](http://www.oracle.com/technetwork/java/javase/7u131-relnotes-3338543.html). | Používá JDK 8 [TLS 1.2 ve výchozím nastavení](https://blogs.oracle.com/java-platform-group/jdk-8-will-use-tls-12-as-default).  |
|Linux | Zpravidla spoléhají na Linuxových distribucích [OpenSSL](https://www.openssl.org) podpora protokolu TLS 1.2.  | Zkontrolujte [protokol změn OpenSSL](https://www.openssl.org/news/changelog.html) potvrďte vaše verze OpenSSL není podporovaná.|
| Windows 8.0 10 | Podporované a ve výchozím nastavení povolené. | Potvrďte, že stále používáte [výchozí nastavení](https://docs.microsoft.com/en-us/windows-server/security/tls/tls-registry-settings).  |
| Windows Server 2012 2016 | Podporované a ve výchozím nastavení povolené. | Potvrďte, že stále používáte [výchozí nastavení](https://docs.microsoft.com/en-us/windows-server/security/tls/tls-registry-settings) |
| Windows 7 SP1 a Windows Server 2008 R2 SP1 | Podporované, ale není povoleno ve výchozím nastavení. | Najdete v článku [zabezpečení TLS (Transport Layer), nastavení registru](https://docs.microsoft.com/en-us/windows-server/security/tls/tls-registry-settings) stránku Podrobnosti o tom, jak povolit.  |
| Windows Server 2008 SP2 | Podpora pro TLS 1.2 vyžaduje aktualizaci. | V tématu [aktualizace přidání podpory pro TLS 1.2](https://support.microsoft.com/help/4019276/update-to-add-support-for-tls-1-1-and-tls-1-2-in-windows-server-2008-s) v systému Windows Server 2008 SP2. |
|Windows Vista | Nepodporuje se. | neuvedeno

### <a name="check-what-version-of-openssl-your-linux-distribution-is-running"></a>Zkontrolujte, jaké verze OpenSSL distribuční Linux běží.

Pokud chcete zkontrolovat, jaká verze OpenSSL jste nainstalovali, otevřete terminál a spusťte:

```terminal
openssl version -a
```

### <a name="run-a-test-tls-12-transaction-on-linux"></a>Spuštění testu TLS 1.2 transakce v systému Linux

Ke spuštění základní předběžný test ke zjištění, pokud váš systém Linux komunikovat přes TLS 1.2. Otevřete terminál a spusťte:

```terminal
openssl s_client -connect bing.com:443 -tls1_2
```

## <a name="personal-data-stored-in-application-insights"></a>Osobní data uložená ve službě Application Insights

Naše [Application Insights osobní data článku](app-insights-customer-data.md) tento problém podrobné.

#### <a name="can-my-users-turn-off-application-insights"></a>Moji uživatelé vypnout Application Insights?
Ne přímo. Poskytujeme nemáte přepínač, který vaši uživatelé mohou pracovat vypnout Application Insights.

Však můžete implementovat této funkce ve vaší aplikaci. Sady SDK zahrnout nastavení rozhraní API, který vypne telemetrii kolekce. 

## <a name="data-sent-by-application-insights"></a>Data odeslaná Application Insights
Sady SDK se liší mezi platformami a několik součástí, které můžete nainstalovat. (Odkazovat na [Application Insights - přehled][start].) Jednotlivé komponenty odešle různých data.

#### <a name="classes-of-data-sent-in-different-scenarios"></a>Třídy dat odesílaných v různých scénářích
| Vaše akce | Datové třídy, které jsou shromážděny (viz další tabulce) |
| --- | --- |
| [Přidání Application Insights SDK do projektu webové rozhraní .NET][greenbrown] |ServerContext<br/>Odvodit<br/>Čítače výkonu<br/>Požadavky<br/>**Výjimky**<br/>Relace<br/>uživatelé |
| [Nainstalujte monitorování stavu ve službě IIS][redfield] |Závislosti<br/>ServerContext<br/>Odvodit<br/>Čítače výkonu |
| [Přidejte Application Insights SDK do webové aplikace Java][java] |ServerContext<br/>Odvodit<br/>Žádost<br/>Relace<br/>uživatelé |
| [Přidá do webové stránky JavaScript SDK][client] |ClientContext <br/>Odvodit<br/>Stránka<br/>ClientPerf<br/>AJAX |
| [Definovat výchozí vlastnosti][apiproperties] |**Vlastnosti** na všechny standardní a vlastní události |
| [Volání TrackMetric][api] |Číselné hodnoty.<br/>**Vlastnosti** |
| [Volání sledovat *][api] |Název události<br/>**Vlastnosti** |
| [Volání TrackException][api] |**Výjimky**<br/>Výpis zásobníku<br/>**Vlastnosti** |
| Sady SDK nelze shromažďovat data. Příklad: <br/> -nemůže získat přístup k čítače výkonu<br/> -výjimky v inicializátoru telemetrie |Diagnostika SDK |

Pro [sady SDK pro jiné platformy][platforms], najdete v části své dokumenty.

#### <a name="the-classes-of-collected-data"></a>Třídy shromážděná data
| Shromážděná data – třída | Zahrnuje (není vyčerpávající seznam) |
| --- | --- |
| **Vlastnosti** |**Žádná data - určenému kódu** |
| DeviceContext |ID, IP, národní prostředí, model zařízení, sítě, typ sítě, název výrobce OEM, rozlišení obrazovky, instanci Role, název Role, typ zařízení |
| ClientContext |Operační systém, národní prostředí, jazyka, sítě, okno řešení |
| Relace |id relace |
| ServerContext |Název počítače, národní prostředí, operačního systému, zařízení, uživatelské relace, kontextu uživatele, operace |
| Odvodit |geografické umístění z IP adresy, časové razítko, operačního systému, prohlížeč |
| Metriky |Název metriky a hodnotu |
| Události |Název události a hodnotu |
| PageViews |Název adresy URL a stránky nebo obrazovky |
| Výkonu klienta |Název adresy URL/stránky, čas načítání prohlížeče |
| AJAX |Volání protokolu HTTP z webové stránky k serveru |
| Požadavky |Adresa URL, doba trvání, kód odpovědi |
| Závislosti |Typ (SQL, protokolu HTTP,...), připojovací řetězec nebo identifikátor URI, synchronizace nebo asynchronní, doba trvání, úspěch, příkaz jazyka SQL (s monitorování stavu) |
| **Výjimky** |Typ, **zpráva**, zpětná volání, zdrojového souboru a řádku číslo id vlákna |
| Chyby |Id procesu, id nadřazeného procesu havárií id vlákna; Oprava aplikace, id, sestavení;  Typ výjimky, adresu, důvod; zakódovaná symboly a registrů, binární počáteční a koncové adresy, binární název a cesta, typ procesoru |
| Trasování |**Zpráva** a úroveň závažnosti |
| Čítače výkonu |Čas procesoru, dostupná paměť, rychlost požadavků, výjimka sazba, proces nesdílených bajtů, vstupně-výstupní operace sazba, doba trvání požadavku, délka fronty požadavků |
| Dostupnost |Kód odpovědi webového testu, doba trvání jednotlivých test krok, název testu, časové razítko, úspěch, doby odezvy, umístění testu |
| Diagnostika SDK |Trasovací zpráva nebo výjimky |

Můžete [vypnout některá data pomocí úpravy souboru ApplicationInsights.config][config]

## <a name="credits"></a>Závěrečné titulky
Tento produkt obsahuje GeoLite2 data vytvořená systémem MaxMind, k dispozici z [ http://www.maxmind.com ](http://www.maxmind.com).



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[platforms]: app-insights-platforms.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md

