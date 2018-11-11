---
title: Uchovávání dat a úložiště ve službě Azure Application Insights | Dokumentace Microsoftu
description: Prohlášení o zásadách uchovávání dat a ochrany osobních údajů
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
ms.date: 10/10/2018
ms.author: mbullwin
ms.openlocfilehash: 1994c714f691177b526b44e277fea705d18b4335
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51245694"
---
# <a name="data-collection-retention-and-storage-in-application-insights"></a>Shromažďování, uchování a ukládání dat v nástroji Application Insights

Při instalaci [Azure Application Insights] [ start] SDK v aplikaci, se odesílá telemetrii týkající se vaší aplikace do cloudu. Přirozeně odpovídá vývojáři chtějí vědět, jaká data se posílají přesně, co se stane, že k datům a jak můžete zachovat kontrolu nad jeho. Konkrétně by mohla pošlou citlivá data, kde je uložený a zabezpečené je? 

První, Stručná odpověď:

* Moduly standardní telemetrická data, na kterých běží "mimo pole" by problém nahlásili odesílat citlivá data do služby. Telemetrická data se týká zatížení, metriky výkonu a využití, sestavy výjimek a jiné diagnostické údaje. Adresy URL; jsou viditelné v diagnostických zprávách hlavní uživatelské údaje ale aplikace by neměl v každém případě citlivá data ve formátu prostého textu v adrese URL.
* Můžete napsat kód, který odešle další vlastní telemetrická data, vám pomůže při monitorování využití a Diagnostika. (Toto rozšíření je skvělou funkcí služby Application Insights.) Je to možné, omylem, psaní tohoto kódu tak, že obsahují osobní a další citlivá data. Pokud vaše aplikace funguje se takové údaje, byste měli použít důkladný přehled procesů pro veškerý kód, který píšete.
* Při vývoji a testování vaší aplikace, je snadné ke kontrole, co se právě odesílá prostřednictvím sady SDK. Data se zobrazí v ladění okna výstup integrované vývojové prostředí a prohlížeče. 
* Data se uchovávají v [Microsoft Azure](http://azure.com) serverů v USA nebo Evropa. (Ale vaše aplikace můžou běžet kdekoli.) Azure má [silné zabezpečení zpracuje a splňuje širokou škálu standardy pro dodržování předpisů](https://azure.microsoft.com/support/trust-center/). Pouze vám a vašemu týmu určené mít přístup k vašim datům. Zaměstnanci Microsoftu můžou mít omezený přístup k němu konkrétní ojedinělých případech s vaším vědomím. Zašifrovaná při přenosu, i když nejsou v serverech.

Zbývající část tohoto článku popisuje podrobněji na těchto otázek. Je navržena jako samostatná, tak, aby je bylo možné zobrazit kolegům, kteří nejsou součástí vašeho týmu.

## <a name="what-is-application-insights"></a>Co je Application Insights?
[Azure Application Insights] [ start] je služba od Microsoftu, která vám pomůže zvýšit výkon a možnosti využití vaší živé aplikace. Monitoruje vaše aplikace neustále, na kterých je spuštěný při testování a po publikování nebo ho nasadil. Application Insights vytvoří grafů a tabulek, které ukazují, například, kterou denní dobu můžete získat většina uživatelů, jak responzivní aplikace je a jak dobře je poskytovaný všech externích služeb, na kterých závisí. Pokud existují, chyb, chyby nebo problémy s výkonem, můžete prohledat data telemetrie podrobně určování příčin problémů. A služba vám pošle e-mailů, pokud existují změny v dostupnosti a výkonu vaší aplikace.

Pokud chcete získat tuto funkci, nainstalujte sadu SDK Application Insights ve vaší aplikaci, která se stane součástí jeho kód. Když vaše aplikace běží, sada SDK sleduje jeho operace a odesílá telemetrická data do služby Application Insights. Toto je Cloudová služba hostovaná společností [Microsoft Azure](http://azure.com). (Ale Application Insights se dá použít pro všechny aplikace, nejen ty, které jsou hostované v Azure.)

![Sada SDK ve vaší aplikaci odesílá telemetrii služby Application Insights.](./media/app-insights-data-retention-privacy/01-scheme.png)

Služba Application Insights ukládá a analyzuje telemetrii. Pokud chcete zobrazit analýzu nebo prohledejte uloženou telemetrii, přihlaste se ke svému účtu Azure a otevřete prostředek Application Insights pro vaši aplikaci. Přístup k datům můžete také sdílet s ostatními členy týmu, nebo s určeným odběratelům Azure.

Můžete mít dat exportovaných ze služby Application Insights, například do databáze nebo na externí nástroje. Každý nástroj poskytnete speciální klíč, který můžete získat ze služby. Klíč můžete v případě potřeby odvolat. 

Sady SDK služby Application Insights jsou k dispozici pro celou řadu typů aplikací: webové služby, které jsou hostované na vašich vlastních serverech J2EE a ASP.NET, nebo v Azure; webových klientů – to znamená, že kód spuštěný ve webové stránce. desktopové aplikace a služby; aplikace pro zařízení jako Windows Phone, iOS a Android. Všechny odesílají telemetrii ve stejné službě.

## <a name="what-data-does-it-collect"></a>Jaká data se shromažďuje nástroj?
### <a name="how-is-the-data-is-collected"></a>Jak se data se shromažďují?
Existují tři zdroje dat:

* Sady SDK, které můžete integrovat s vaší aplikací buď [ve vývoji](app-insights-asp-net.md) nebo [v době běhu](app-insights-monitor-performance-live-website-now.md). Existují různé sady SDK pro typy jiné aplikaci. K dispozici je také [sady SDK pro webové stránky](app-insights-javascript.md), který načte do prohlížeče uživatele end spolu s stránky.
  
  * Jednotlivých sadách SDK má několik [moduly](app-insights-configuration-with-applicationinsights-config.md), které používají různé technologie shromažďovat různé typy telemetrie.
  * Pokud instalace sady SDK do vývoje můžete použít své rozhraní API k odesílání vlastních telemetrických dat, kromě standardní moduly. Tato vlastní telemetrie může obsahovat všechna data, která chcete odeslat.
* V některých webových serverů existují také agenty, které běží souběžně s ní a odesílání telemetrických dat o využití procesoru, paměti a sítě obsazení. Například virtuální počítače Azure, hostitelů Docker, a [J2EE servery](app-insights-java-agent.md) může mít tyto agenty.
* [Testy dostupnosti](app-insights-monitor-web-app-availability.md) jsou procesy řízených microsoftem, který v pravidelných intervalech odesílat žádosti do své webové aplikace. Výsledky se odesílají do služby Application Insights.

### <a name="what-kinds-of-data-are-collected"></a>Jaké druhy dat se shromažďují?
Jsou hlavních kategorií:

* [Web telemetrická data server](app-insights-asp-net.md) – požadavky HTTP.  Identifikátor URI, doba zpracování požadavku, kód odpovědi, IP adresa klienta. Id relace.
* [Webové stránky](app-insights-javascript.md) -počtu stránek, uživatelů a relací. Časy načtení stránek. Výjimky. Volání AJAX.
* Výkon čítače – paměti, procesoru, vstupně-výstupních operací, obsazení sítě.
* Klient a server kontext - operačního systému, národní prostředí, typ zařízení, prohlížeč, rozlišení obrazovky.
* [Výjimky](app-insights-asp-net-exceptions.md) a selhání – **výpisy zásobníku**, id, typ procesoru sestavení. 
* [Závislosti](app-insights-asp-net-dependencies.md) – volání externích služeb, jako je REST, SQL a AJAX. Identifikátor URI nebo připojovací řetězec, doba trvání, úspěch, příkaz.
* [Testy dostupnosti](app-insights-monitor-web-app-availability.md) – doba trvání testu a kroky, odpovědi.
* [Protokoly trasování](app-insights-asp-net-trace-logs.md) a [vlastní telemetrii](app-insights-api-custom-events-metrics.md) - **nic kódu do protokoly nebo telemetrie**.

[Další podrobnosti](#data-sent-by-application-insights).

## <a name="how-can-i-verify-whats-being-collected"></a>Jak lze ověřit, co se shromažďují?
Pokud vyvíjíte aplikace pomocí sady Visual Studio, můžete tuto aplikaci spusťte v režimu ladění (F5). Telemetrie se zobrazí v okně výstup. Odtud můžete zkopírovat ji a naformátujeme ho jako JSON pro snadnou kontrolu. 

![](./media/app-insights-data-retention-privacy/06-vs.png)

Je zde i lépe čitelný zobrazení v okně diagnostické nástroje.

Pro webové stránky otevřete okno ladění vašeho prohlížeče.

![Stisknutím klávesy F12 a otevřete kartu síť.](./media/app-insights-data-retention-privacy/08-browser.png)

### <a name="can-i-write-code-to-filter-the-telemetry-before-it-is-sent"></a>Můžete napsat kód pro filtrování telemetrických dat před odesláním?
To může být zápisem je to možné [modulu plug-in procesoru telemetrie](app-insights-api-filtering-sampling.md).

## <a name="how-long-is-the-data-kept"></a>Jak dlouho se data ukládají?
Nezpracované datové body (to znamená, položky, které se můžete dotazovat v Analytics a kontrolovat v hledání) uchovávají po dobu 90 dnů. Pokud je potřeba uchovávat data déle než, můžete použít [průběžný export](app-insights-export-telemetry.md) a zkopírujte ho do účtu úložiště.

Agregovaná data (to znamená, počty, průměr a jiných statistická data, která se zobrazí v Průzkumníku metrik) jsou zachovány za interval 1 minuta po dobu 90 dnů.

[Ladění snímků](app-insights-snapshot-debugger.md) ukládají po dobu sedmi dní. Tyto zásady uchování je nastavena na základě jednotlivých aplikací. Pokud je potřeba tuto hodnotu zvýšit, můžete požádat o zvýšení tak, že otevřete případ podpory na webu Azure Portal.

## <a name="who-can-access-the-data"></a>Kdo má přístup k datům?
Data jsou viditelné pro vás a, pokud již máte účet organizace, členové týmu. 

Může byla exportovaná knihovnou vy a vaši členové týmu a může být zkopírován do jiných umístění a předávají do jiné osoby.

#### <a name="what-does-microsoft-do-with-the-information-my-app-sends-to-application-insights"></a>Co dělá Microsoftu s informacemi, které Moje aplikace odesílá do služby Application Insights?
Společnost Microsoft používá data jenom k poskytování služeb.

## <a name="where-is-the-data-held"></a>Kde se data uchovávají?
* V USA, Evropa nebo jihovýchodní Asie. Když vytvoříte nový prostředek Application Insights, můžete vybrat umístění. 


#### <a name="does-that-mean-my-app-has-to-be-hosted-in-the-usa-europe-or-southeast-asia"></a>To znamená, že aplikace má zajistit také jejich hostování v USA, Evropa nebo jihovýchodní Asie?
* Ne. Vaše aplikace mohla spustit kdekoli, v místních hostitelů nebo v cloudu.

## <a name="how-secure-is-my-data"></a>Zabezpečené jsou moje data?
Application Insights je služba Azure. Zásady zabezpečení jsou popsány v [dokument white paper zabezpečení Azure, ochrana osobních údajů a dodržování předpisů](https://go.microsoft.com/fwlink/?linkid=392408).

Jsou data uložená na serverech Microsoft Azure. Pro účty na webu Azure Portal, účet omezení jsou popsaná v [Azure zabezpečení, ochrany osobních údajů a dodržování předpisů dokumentu](https://go.microsoft.com/fwlink/?linkid=392408).

Přístup k vašim datům zaměstnanců společnosti Microsoft je omezen. Jsme přístup k vašim datům pouze s vaším souhlasem, a pokud je nezbytný pro podporu používání Application Insights. 

Data v agregaci přes všechny naše zákazníky aplikace (například datové sazby a průměrná velikost trasování) slouží ke zlepšení služby Application Insights.

#### <a name="could-someone-elses-telemetry-interfere-with-my-application-insights-data"></a>By mohla někoho jiného telemetrii ovlivňovat data Application Insights?
Další telemetrické údaje se může odesílat do vašeho účtu pomocí Instrumentační klíč, který se nachází v kódu webové stránky. S dostatek další data by metriky představují správně, výkonu a využití vaší aplikace.

Pokud sdílíte s jinými projekty kódu, nezapomeňte odebrat svůj Instrumentační klíč.

## <a name="is-the-data-encrypted"></a>Zašifrovaná data?
Ne uvnitř servery v současné době.

Všechna data se šifrují při jejich přesunu mezi datovými centry.

#### <a name="is-the-data-encrypted-in-transit-from-my-application-to-application-insights-servers"></a>Zašifrovaná data přenášená z mé aplikace na servery Application Insights?
Ano, můžeme použít https k odesílání dat do portálu v téměř všech sad SDK, včetně webových serverů, zařízení a protokol HTTPS webové stránky. Jedinou výjimkou je data odeslaná z jednoduché webové stránky HTTP.

## <a name="does-the-sdk-create-temporary-local-storage"></a>SDK vytvořit místní dočasné úložiště?

Ano, kanály některých telemetrických dat se zachová data místně, pokud není dostupný koncový bod. Přečtěte si prosím níže zobrazíte, které rozhraní a kanály telemetrie se vztahuje.


Telemetrie kanály, které využívají místní úložiště vytvořit dočasné soubory v adresáři TEMP nebo APPDATA, které jsou omezeny na konkrétní účet, který spouští vaše aplikace. K tomu může dojít, když se koncový bod není dočasně k dispozici nebo dosáhl omezení limitu. Po vyřešení tohoto problému obnoví kanál telemetrie odesílá všechny nové a trvalá data.


Tato data trvalých **nejsou šifrovaná** se důrazně doporučuje změny struktury vaší zásady shromažďování údajů zakázat shromažďování dat soukromých. (Viz [jak exportovat a odstranění dat soukromých](https://docs.microsoft.com/azure/application-insights/app-insights-customer-data#how-to-export-and-delete-private-data) Další informace.)


Pokud zákazník potřebuje k tomuto adresáři nakonfigurovat specifické požadavky na zabezpečení můžete nakonfigurovat na rozhraní framework. Ujistěte se prosím, že proces běží vaše aplikace má přístup k zápisu do tohoto adresáře, ale také se ujistěte, že tento adresář je chráněný, aby telemetrie čten stranou serveru neoprávněné uživatele.

### <a name="java"></a>Java

`C:\Users\username\AppData\Local\Temp` pro zachování dat se používá. Toto umístění není konfigurovatelné z adresáře config a oprávnění pro přístup k této složce jsou omezeny na konkrétní uživatele pomocí požadované přihlašovací údaje. (Viz [implementace](https://github.com/Microsoft/ApplicationInsights-Java/blob/40809cb6857231e572309a5901e1227305c27c1a/core/src/main/java/com/microsoft/applicationinsights/internal/util/LocalFileSystemUtils.java#L48-L72) zde.)

###  <a name="net"></a>.Net

Ve výchozím nastavení `ServerTelemetryChannel` používá složky dat lokální aplikace pro aktuálního uživatele `%localAppData%\Microsoft\ApplicationInsights` nebo dočasnou složku `%TMP%`. (Viz [implementace](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) zde.)


Prostřednictvím konfiguračního souboru:
```
<TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel,   Microsoft.AI.ServerTelemetryChannel">
    <StorageFolder>D:\NewTestFolder</StorageFolder>
</TelemetryChannel>
```

Prostřednictvím kódu:

- Odebrat ServerTelemetryChannel z konfiguračního souboru
- Přidejte tento fragment kódu do vaší konfigurace:
```
ServerTelemetryChannel channel = new ServerTelemetryChannel();
channel.StorageFolder = @"D:\NewTestFolder";
channel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = channel;
```

### <a name="netcore"></a>NetCore

Ve výchozím nastavení `ServerTelemetryChannel` používá složky dat lokální aplikace pro aktuálního uživatele `%localAppData%\Microsoft\ApplicationInsights` nebo dočasnou složku `%TMP%`. (Viz [implementace](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) zde.) V prostředí Linux místní úložiště se deaktivuje, pokud se zadá složka úložiště.

Následující fragment kódu ukazuje, jak nastavit `ServerTelemetryChannel.StorageFolder` v `ConfigureServices()`  metodu vaše `Startup.cs` třídy:

```
services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
```

(Viz [AspNetCore vlastní konfigurace](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) Další informace. )

### <a name="nodejs"></a>Node.js

Ve výchozím nastavení `%TEMP%/appInsights-node{INSTRUMENTATION KEY}` se používá pro zachovává data. Oprávnění pro přístup k této složce jsou omezeny pro aktuálního uživatele a správce. (Viz [implementace](https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/Library/Sender.ts) zde.)

Předpona složky `appInsights-node` lze přepsat tak, že změníte hodnotu runtime statická proměnná `Sender.TEMPDIR_PREFIX` součástí [Sender.ts](https://github.com/Microsoft/ApplicationInsights-node.js/blob/7a1ecb91da5ea0febf5ceab13d6a4bf01a63933d/Library/Sender.ts#L384).



## <a name="how-do-i-send-data-to-application-insights-using-tls-12"></a>Jak můžu poslat data do Application Insights pomocí protokolu TLS 1.2?

– Pomáhat zajistit zabezpečení dat při přenosu do koncových bodů služby Application Insights, důrazně doporučujeme zákazníci moci nakonfigurovat jejich aplikace použijte alespoň zabezpečení TLS (Transport Layer) 1.2. Starší verze z protokolu TLS/Secure Sockets Layer (SSL) bylo zjištěno ohrožen a stále aktuálně fungují povolit zpětnou kompatibilitu, ale jsou **ale nedoporučený krok**, a oboru je rychle se měnící spustit metodu Abandon podpory pro tyto starší protokoly. 

[PCI Security Standards Council](https://www.pcisecuritystandards.org/) nastavil [termínu 30. června 2018](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) zakázat starší verze protokolu TLS/SSL a upgrade bezpečnější protokoly. Jakmile Azure sníží stále podporuje starší verze, pokud vaše aplikace nebo klienti nemohou komunikovat přes alespoň TLS 1.2, nemohli odesílat data do Application Insights. Postup, jak otestovat a ověřit podporu protokolu TLS vaší aplikace se liší v závislosti na operačním systému a platformy, jakož i jazyk a rozhraní, které vaše aplikace používá.

Nedoporučujeme explicitním nastavením nezbytně nutné, protože toto může rozbít funkce zabezpečení na úrovni platformy, které umožňují automaticky rozpoznat a mohli využívat novější bezpečnější protokolů, jakmile budou používat jenom TLS 1.2, pokud vaše aplikace k dispozici, jako je například TLS 1.3. Doporučujeme provést důkladné auditu z kódu vaší aplikace ke kontrole hardcoding určité verze protokolu TLS/SSL.

### <a name="platformlanguage-specific-guidance"></a>Konkrétní pokyny k platformě a jazyce

|Platformu nebo jazyk | Podpora | Další informace |
| --- | --- | --- |
| Azure App Services  | Podporované, konfigurace mohou být vyžadovány. | V dubnu 2018 jsme představili podporu. Přečtěte si oznámení pro [podrobnosti o konfiguraci](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/).  |
| Aplikace Azure Function App | Podporované, konfigurace mohou být vyžadovány. | V dubnu 2018 jsme představili podporu. Přečtěte si oznámení pro [podrobnosti o konfiguraci](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/). |
|.NET | Podporované, konfigurace se liší podle verze. | Podrobnou konfiguraci informace o .NET 4.7 a předchozími verzemi najdete [tyto pokyny](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12).  |
|Monitorování stavu | Podporované, vyžaduje se konfigurace | Monitorování stavu spoléhá na [konfigurace operačního systému](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) + [konfigurace .NET](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12) pro podporu protokolu TLS 1.2.
|Node.js |  Podporované v v10.5.0, konfigurace mohou být vyžadovány. | Použití [oficiální dokumentaci k Node.js TLS/SSL](https://nodejs.org/api/tls.html) pro jakékoli konkrétní konfigurace aplikace. |
|Java | Podporované, JDK podpora protokolu TLS 1.2 bylo přidáno v [JDK 6 aktualizace 121](http://www.oracle.com/technetwork/java/javase/overview-156328.html#R160_121) a [JDK 7](http://www.oracle.com/technetwork/java/javase/7u131-relnotes-3338543.html). | Používá sadu JDK 8 [TLS 1.2 ve výchozím nastavení](https://blogs.oracle.com/java-platform-group/jdk-8-will-use-tls-12-as-default).  |
|Linux | Linuxové distribuce mají tendenci přináší setrvávání u [OpenSSL](https://www.openssl.org) pro podporu protokolu TLS 1.2.  | Zkontrolujte [protokolu změn OpenSSL](https://www.openssl.org/news/changelog.html) pro potvrzení, vaše verze OpenSSL není podporovaná.|
| Windows 8.0 10 | Podporované a ve výchozím nastavení povolená. | Potvrďte, že stále používáte [výchozí nastavení](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings).  |
| Windows Server 2012 – 2016 | Podporované a ve výchozím nastavení povolená. | Potvrďte, že stále používáte [výchozí nastavení](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) |
| Windows 7 SP1 a Windows Server 2008 R2 SP1 | Podporované, ale není ve výchozím nastavení povolená. | Najdete v článku [zabezpečení TLS (Transport Layer), nastavení registru](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) stránku Podrobnosti o tom, jak povolit.  |
| Windows Server 2008 SP2 | Podpora protokolu TLS 1.2 vyžaduje aktualizaci. | Zobrazit [aktualizace přidává funkce pro protokol TLS 1.2](https://support.microsoft.com/help/4019276/update-to-add-support-for-tls-1-1-and-tls-1-2-in-windows-server-2008-s) v systému Windows Server 2008 SP2. |
|Windows Vista | Nepodporuje se. | neuvedeno

### <a name="check-what-version-of-openssl-your-linux-distribution-is-running"></a>Kontrola, jaká verze OpenSSL vaší distribuci Linuxu je spuštěná.

Pokud chcete zkontrolovat, jaká verze OpenSSL nainstalujete, otevřete terminál a spusťte:

```terminal
openssl version -a
```

### <a name="run-a-test-tls-12-transaction-on-linux"></a>Spuštění testu TLS 1.2 transakce v Linuxu

Ke spuštění základních předběžného testu chcete zobrazit, pokud váš systém Linux komunikovat přes protokol TLS 1.2. Otevřete terminál a spusťte:

```terminal
openssl s_client -connect bing.com:443 -tls1_2
```

## <a name="personal-data-stored-in-application-insights"></a>Osobní údaje uložené ve službě Application Insights

Naše [článku osobních údajů služby Application Insights](../log-analytics/log-analytics-personal-data-mgmt.md) podrobné tento problém.

#### <a name="can-my-users-turn-off-application-insights"></a>Můžete svým uživatelům vypnout Application Insights?
Ne přímo. Neposkytujeme přepínač, který chcete-li vypnout Application Insights můžete provozovat vaši uživatelé.

Ale můžete implementovat této funkce ve vaší aplikaci. Všechny sady SDK zahrnují nastavení aplikace API, který vypne shromažďování telemetrie. 

## <a name="data-sent-by-application-insights"></a>Data odeslaná službou Application Insights
Sady SDK se liší mezi platformami a je několik komponent, které můžete nainstalovat. (Odkazovat [Application Insights – přehled][start].) Jednotlivé komponenty odešle jiná data.

#### <a name="classes-of-data-sent-in-different-scenarios"></a>Třídy data odeslaná v různých scénářích
| Vaše akce | Shromážděná data třídy (viz následující tabulka) |
| --- | --- |
| [Přidejte Application Insights SDK do webového projektu .NET][greenbrown] |ServerContext<br/>Odvodit<br/>Čítače výkonu<br/>Požadavky<br/>**Výjimky**<br/>Relace<br/>uživatelé |
| [Nainstalujte monitorování stavu ve službě IIS][redfield] |Závislosti<br/>ServerContext<br/>Odvodit<br/>Čítače výkonu |
| [Přidat sadu Application Insights SDK do webové aplikace v Javě][java] |ServerContext<br/>Odvodit<br/>Žádost<br/>Relace<br/>uživatelé |
| [Přidání sady SDK JavaScript do webové stránky][client] |Instance třídy ClientContext <br/>Odvodit<br/>Stránka<br/>ClientPerf<br/>AJAX |
| [Definovat výchozí vlastnosti][apiproperties] |**Vlastnosti** na všechny standardní a vlastní události |
| [Volání TrackMetric][api] |Číselné hodnoty<br/>**Vlastnosti** |
| [Sledování volání *][api] |Název události<br/>**Vlastnosti** |
| [Volání TrackException][api] |**Výjimky**<br/>Výpis zásobníku<br/>**Vlastnosti** |
| Sady SDK nelze shromáždit data. Příklad: <br/> – Nelze získat přístup k čítače výkonu<br/> -výjimka v inicializátoru telemetrie |Diagnostika sady SDK |

Pro [sady SDK pro jiné platformy][platforms], najdete v článku své dokumenty.

#### <a name="the-classes-of-collected-data"></a>Třídy shromážděných dat
| Třída shromážděných dat | Zahrnuje (ne o vyčerpávající seznam) |
| --- | --- |
| **Vlastnosti** |**Všechna data – určeno kódu** |
| DeviceContext |ID, IP, národní prostředí, model zařízení, sítě, typ sítě, název výrobce OEM, rozlišení obrazovky nebo instanci Role, název Role, typ zařízení |
| Instance třídy ClientContext |Operační systém, národní prostředí, jazyka, sítě, okno řešení |
| Relace |Id relace |
| ServerContext |Název počítače, národní prostředí operačního systému, zařízení, uživatelské relace, uživatelský kontext, operace |
| Odvodit |geografické umístění z IP adresy, časové razítko, operačního systému, prohlížeč |
| Metriky |Název metriky a hodnota |
| Události |Název události a hodnota |
| Zobrazení stránky |Adresa URL a stránky název nebo název obrazovky |
| Výkonu klienta |Název adresy URL/stránky, doba načítání prohlížečem |
| AJAX |HTTP volání z webové stránky serveru |
| Požadavky |Adresa URL, doba trvání, kód odpovědi |
| Závislosti |Typ (SQL, protokolu HTTP,...), připojovací řetězec nebo identifikátor URI, sync/async, doba trvání, úspěch, příkaz SQL (pomocí monitorování stavu) |
| **Výjimky** |Typ, **zpráva**, zásobníky volání, zdrojového souboru a řádku číslo id vlákna |
| Dojde k chybě |Id procesu, id nadřazeného procesu, id vlákna selhání; Oprava aplikace, id, sestavení;  Typ výjimky, adresa, z důvodu; obfuskovaný symboly a registry, binární počáteční a koncovou adresu, binární název a cesta, typ procesoru |
| Trasování |**Zpráva** a úroveň závažnosti |
| Čítače výkonu |Času procesoru, paměti, frekvence požadavků, frekvence výjimek, nesdílených bajtů procesu, frekvence v/v, doba trvání žádosti, délka fronty požadavků |
| Dostupnost |Odpovědi kódu webového testu, dobu trvání každý krok testu, název testu, časové razítko, úspěch, doby odezvy, umístění testu |
| Diagnostika sady SDK |Zprávy trasování a výjimky |

Můžete si [vypnout některá data pomocí úpravy souboru ApplicationInsights.config][config]

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

