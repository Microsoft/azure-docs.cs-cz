---
title: Uchovávání a ukládání dat v Přehledech aplikací Azure | Dokumenty společnosti Microsoft
description: Prohlášení o zásadách uchovávání informací a ochrany osobních údajů
ms.topic: conceptual
ms.date: 09/29/2019
ms.openlocfilehash: 30878eecf795c85713b9f09b8325b326416022b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275994"
---
# <a name="data-collection-retention-and-storage-in-application-insights"></a>Shromažďování, uchovávání a ukládání dat v Application Insights

Když do aplikace [nainstalujete][start] stek Azure Application Insights SDK, odešle telemetrii o vaší aplikaci do cloudu. Samozřejmě, že odpovědní vývojáři chtějí přesně vědět, jaká data jsou odesílána, co se stane s daty a jak mohou udržet kontrolu nad tím. Zejména by mohly být odesílány citlivé údaje, kde jsou uloženy a jak bezpečné jsou? 

Za prvé, stručná odpověď:

* Standardní telemetrické moduly, které běží "po vybalení z krabice" je nepravděpodobné, že k odeslání citlivých dat do služby. Telemetrie se týká metrik zatížení, výkonu a využití, sestav výjimek a dalších diagnostických dat. Hlavní uživatelská data viditelná v diagnostických sestavách jsou adresy URL. ale vaše aplikace by v žádném případě neměla umístit citlivá data do prostého textu v adrese URL.
* Můžete napsat kód, který odešle další vlastní telemetrie, které vám pomohou s diagnostikou a monitorování mů e svyužití. (Tato rozšiřitelnost je skvělou funkcí Application Insights.) Bylo by možné omylem napsat tento kód tak, aby zahrnoval osobní a jiné citlivé údaje. Pokud vaše aplikace pracuje s těmito daty, měli byste použít důkladný proces kontroly na veškerý kód, který píšete.
* Při vývoji a testování aplikace je snadné zkontrolovat, co je odesíláno sadou SDK. Data se zobrazí ve výstupních oknech ladění ide a prohlížeče. 
* Data se uchovává na serverech [Microsoft Azure](https://azure.com) v USA nebo Evropě. (Ale vaše aplikace může běžet kdekoli.) Azure má [silné procesy zabezpečení a splňuje širokou škálu standardů dodržování předpisů](https://azure.microsoft.com/support/trust-center/). Pouze vy a váš určený tým máte přístup k vašim údajům. Pracovníci společnosti Microsoft mohou mít omezený přístup k němu pouze za určitých omezených okolností s vašimi znalostmi. Je to zašifrované při přepravě a v klidu.
*   Zkontrolujte shromážděná data, protože to může zahrnovat data, která jsou povolena za určitých okolností, ale ne jiné.  Dobrým příkladem je název zařízení. Název zařízení ze serveru nemá žádný dopad na ochranu osobních údajů a je užitečný, ale název zařízení z telefonu nebo přenosného počítače může mít dopad na ochranu osobních údajů a může být méně užitečný. Sada SDK vyvinutá především pro cílové servery by ve výchozím nastavení shromažďovala název zařízení a to může být nutné přepsat v běžných událostech i výjimkách.

Zbytek tohoto článku podrobněji rozvádí na tyto odpovědi. Je navržen tak, aby byl soběstačný, takže jej můžete ukázat kolegům, kteří nejsou součástí vašeho bezprostředního týmu.

## <a name="what-is-application-insights"></a>Co je Application Insights?
[Azure Application Insights][start] je služba poskytovaná společností Microsoft, která vám pomůže zlepšit výkon a použitelnost vaší živé aplikace. Monitoruje vaši aplikaci po celou dobu, po kterou je spuštěna, a to jak během testování, tak po publikování nebo nasazení. Application Insights vytváří grafy a tabulky, které vám například ukazují, jaké denní doby získáte většinu uživatelů, jak je aplikace responzivní a jak dobře ji obsluhují všechny externí služby, na kterých závisí. Pokud dojde k selhání, selhání nebo problémy s výkonem, můžete prohledávat telemetrická data podrobně diagnostikovat příčinu. A služba vám bude posílat e-maily, pokud dojde ke změnám v dostupnosti a výkonu vaší aplikace.

Chcete-li získat tuto funkci, nainstalujte sady Application Insights SDK ve vaší aplikaci, která se stane součástí jeho kódu. Když je vaše aplikace spuštěná, sada SDK monitoruje svou činnost a odesílá telemetrická data do služby Application Insights. Jedná se o cloudovou službu hodnosnou v [Microsoft Azure](https://azure.com). (Ale Application Insights funguje pro všechny aplikace, ne jen aplikace, které jsou hostované v Azure.)

Služba Application Insights ukládá a analyzuje telemetrická data. Chcete-li zobrazit analýzu nebo prohledat uloženou telemetrii, přihlaste se ke svému účtu Azure a otevřete prostředek Application Insights pro vaši aplikaci. Přístup k datům můžete také sdílet s ostatními členy vašeho týmu nebo se zadanými předplatiteli Azure.

Data můžete exportovat ze služby Application Insights, například do databáze nebo do externích nástrojů. Každému nástroji poskytnete speciální klíč, který získáte ze služby. Klíč lze v případě potřeby odvolat. 

Sady SDK Application Insights jsou k dispozici pro celou řadu typů aplikací: webové služby hostované ve vašem vlastním java ee nebo ASP.NET serverech nebo v Azure. webové klienty - to znamená kód běžící na webové stránce; desktopové aplikace a služby; aplikace zařízení, jako je Windows Phone, iOS a Android. Všichni odesílají telemetrii do stejné služby.

## <a name="what-data-does-it-collect"></a>Jaké údaje shromažďuje?
Existují tři zdroje dat:

* Sada SDK, kterou integrujete s aplikací ve [vývoji](../../azure-monitor/app/asp-net.md) nebo [za běhu](../../azure-monitor/app/monitor-performance-live-website-now.md). Existují různé sady SDK pro různé typy aplikací. K dispozici je také [Sada SDK pro webové stránky](../../azure-monitor/app/javascript.md), která se načte do prohlížeče koncového uživatele spolu se stránkou.
  
  * Každá sada SDK má řadu [modulů](../../azure-monitor/app/configuration-with-applicationinsights-config.md), které používají různé techniky ke shromažďování různých typů telemetrie.
  * Pokud nainstalujete sdk ve vývoji, můžete použít jeho rozhraní API k odeslání vlastní telemetrie, kromě standardních modulů. Tato vlastní telemetrie může obsahovat všechna data, která chcete odeslat.
* V některých webových serverech existují také agenti, kteří běží vedle aplikace a odesílají telemetrii o procesoru, paměti a obsazenosti sítě. Například virtuální počítače Azure, hostitelé Dockeru a [servery EE v Jazyce Java](../../azure-monitor/app/java-agent.md) mohou mít takové agenty.
* [Testy dostupnosti](../../azure-monitor/app/monitor-web-app-availability.md) jsou procesy provozované společností Microsoft, které v pravidelných intervalech odesílají požadavky do vaší webové aplikace. Výsledky jsou odeslány do služby Application Insights.

### <a name="what-kinds-of-data-are-collected"></a>Jaké druhy údajů jsou shromažďovány?
Hlavními kategoriemi jsou:

* [Telemetrie webového serveru](../../azure-monitor/app/asp-net.md) – požadavky HTTP.  Uri, doba, která byla časem zpracování požadavku, kódodpovědi, IP adresa klienta. `Session id`.
* [Webové stránky](../../azure-monitor/app/javascript.md) - Počet stránek, uživatelů a relací. Časy načítání stránky. Výjimky. Volá Ajax.
* Čítače výkonu - paměť, procesor, io, obsazenost sítě.
* Kontext klienta a serveru - Operační systém, národní prostředí, typ zařízení, prohlížeč, rozlišení obrazovky.
* [Výjimky](../../azure-monitor/app/asp-net-exceptions.md) a selhání - **výpisy zásobníku**, `build id`, typ procesoru. 
* [Závislosti -](../../azure-monitor/app/asp-net-dependencies.md) volání externích služeb, jako je REST, SQL, AJAX. Identifikátor URI nebo připojovací řetězec, doba trvání, úspěch, příkaz.
* [Testy dostupnosti](../../azure-monitor/app/monitor-web-app-availability.md) - doba trvání testu a kroků, odpovědi.
* [Trasování protokoly](../../azure-monitor/app/asp-net-trace-logs.md) a [vlastní telemetrie](../../azure-monitor/app/api-custom-events-metrics.md) - **cokoli, co kód do protokolů nebo telemetrie**.

[Více podrobností](#data-sent-by-application-insights).

## <a name="how-can-i-verify-whats-being-collected"></a>Jak mohu ověřit, co se shromažďuje?
Pokud vyvíjíte aplikaci pomocí Visual Studia, spusťte aplikaci v režimu ladění (F5). Telemetrie se zobrazí v okně Výstup. Odtud jej můžete zkopírovat a naformátovat jako JSON pro snadnou kontrolu. 

![](./media/data-retention-privacy/06-vs.png)

K dispozici je také čitelnější zobrazení v okně Diagnostika.

U webových stránek otevřete okno ladění prohlížeče.

![Stiskněte klávesu F12 a otevřete kartu Síť.](./media/data-retention-privacy/08-browser.png)

### <a name="can-i-write-code-to-filter-the-telemetry-before-it-is-sent"></a>Je možné napsat kód pro filtrování telemetrie před odesláním?
To by bylo možné napsáním [modulu plug-in telemetrického procesoru](../../azure-monitor/app/api-filtering-sampling.md).

## <a name="how-long-is-the-data-kept"></a>Jak dlouho jsou data uchovávána?
Nezpracovaná data body (tj. položky, na které se můžete dotazovat v Analytics a kontrolovat ve vyhledávání) jsou uchovávány po dobu až 730 dnů. Můžete [vybrat dobu uchovávání](https://docs.microsoft.com/azure/azure-monitor/app/pricing#change-the-data-retention-period) 30, 60, 90, 120, 180, 270, 365, 550 nebo 730 dní. Pokud potřebujete uchovávat data delší než 730 dní, můžete je během ingestování dat zkopírovat do účtu úložiště pomocí [průběžného exportu.](../../azure-monitor/app/export-telemetry.md) 

Za data uchovávaná déle než 90 dní se účtují poplatky. Další informace o cenách Application Insights najdete na [stránce s cenami azure monitoru](https://azure.microsoft.com/pricing/details/monitor/).

Agregovaná data (tj. počty, průměry a další statistická data zobrazená v Průzkumníku metrik) se uchovávají v zrnku 1 minuty po dobu 90 dnů.

Ladění snímky jsou [uloženy](../../azure-monitor/app/snapshot-debugger.md) po dobu 15 dnů. Tato zásada uchovávání informací je nastavena na základě pro aplikaci. Pokud potřebujete zvýšit tuto hodnotu, můžete požádat o zvýšení otevřením případu podpory na webu Azure Portal.

## <a name="who-can-access-the-data"></a>Kdo má přístup k datům?
Data jsou viditelná pro vás a pokud máte účet organizace, členové týmu. 

Můžete jej exportovat vy a vaši členové týmu a mohou být zkopírovány do jiných míst a předány jiným osobám.

#### <a name="what-does-microsoft-do-with-the-information-my-app-sends-to-application-insights"></a>Co microsoft dělá s informacemi, které moje aplikace odesílá do Application Insights?
Společnost Microsoft používá data pouze k tomu, aby vám službu poskytla.

## <a name="where-is-the-data-held"></a>Kde jsou údaje uchovávány?
* Umístění můžete vybrat při vytváření nového prostředku Application Insights. Další informace o dostupnosti přehledů aplikací v oblasti [najdete zde](https://azure.microsoft.com/global-infrastructure/services/?products=all).

#### <a name="does-that-mean-my-app-has-to-be-hosted-in-the-usa-europe-or-southeast-asia"></a>Znamená to, že moje aplikace musí být hostována v USA, Evropě nebo jihovýchodní Asii?
* Ne. Vaše aplikace může běžet kdekoli, buď ve vlastních místních hostitelích nebo v cloudu.

## <a name="how-secure-is-my-data"></a>Jak bezpečná jsou moje data?
Application Insights je služba Azure. Zásady zabezpečení jsou popsané v [dokumentu white paper azure security, privacy, and compliance](https://go.microsoft.com/fwlink/?linkid=392408).

Data se ukládají na serverech Microsoft Azure. U účtů na webu Azure Portal jsou omezení účtu popsána v [dokumentu Azure Security, Privacy a Compliance](https://go.microsoft.com/fwlink/?linkid=392408).

Přístup pracovníků společnosti Microsoft k vašim datům je omezen. K vašim údajům přistupujeme pouze s vaším svolením a pokud je to nutné pro podporu vašeho používání Application Insights. 

Souhrnná data ve všech aplikacích našich zákazníků (jako jsou rychlosti dat a průměrná velikost trasování) se používají ke zlepšení přehledů aplikací.

#### <a name="could-someone-elses-telemetry-interfere-with-my-application-insights-data"></a>Mohla by telemetrie někoho jiného zasahovat do mých dat Application Insights?
Mohou odeslat další telemetrii na váš účet pomocí instrumentačního klíče, který najdete v kódu vašich webových stránek. S dostatkem dalších dat by metriky nereprezentovaly výkon a využití vaší aplikace správně.

Pokud sdílíte kód s jinými projekty, nezapomeňte odebrat klíč instrumentace.

## <a name="is-the-data-encrypted"></a>Jsou data šifrovaná?
Všechna data jsou šifrována v klidovém stavu a při pohybu mezi datovými centry.

#### <a name="is-the-data-encrypted-in-transit-from-my-application-to-application-insights-servers"></a>Jsou data při přenosu z aplikace na servery Application Insights zašifrována?
Ano, používáme protokol https k odesílání dat na portál z téměř všech sad SDK, včetně webových serverů, zařízení a webových stránek HTTPS. 

## <a name="does-the-sdk-create-temporary-local-storage"></a>Vytvoří sada SDK dočasné místní úložiště?

Ano, některé kanály telemetrie budou uchovávat data místně, pokud nelze dosáhnout koncového bodu. Zkontrolujte níže, abyste zjistili, které architektury a telemetrické kanály jsou ovlivněny.

Telemetrické kanály, které využívají místní úložiště, vytvářejí dočasné soubory v adresářích TEMP nebo APPDATA, které jsou omezeny na konkrétní účet spuštěný v aplikaci. K tomu může dojít, když koncový bod byl dočasně nedostupný nebo pokud jste dosáhli omezení limitu. Po vyřešení tohoto problému bude kanál telemetrie pokračovat v odesílání všech nových a trvalých dat.

Tato trvalá data nejsou místně šifrována. Pokud se jedná o problém, zkontrolujte data a omezit shromažďování soukromých dat. (Další informace naleznete v [tématu Jak exportovat a odstranit soukromá data](https://docs.microsoft.com/azure/application-insights/app-insights-customer-data#how-to-export-and-delete-private-data).)

Pokud zákazník potřebuje nakonfigurovat tento adresář se specifickými požadavky na zabezpečení, lze jej nakonfigurovat podle architektury. Ujistěte se, že proces spuštěný aplikací má přístup pro zápis do tohoto adresáře, ale také ujistěte se, že tento adresář je chráněn, aby se zabránilo telemetrii čtení nezamýšlenými uživateli.

### <a name="java"></a>Java

`C:\Users\username\AppData\Local\Temp`se používá pro uchovávaná data. Toto umístění nelze konfigurovat z adresáře konfigurace a oprávnění pro přístup k této složce jsou omezena na konkrétního uživatele s požadovanými pověřeními. (Další informace naleznete v [tématu implementace](https://github.com/Microsoft/ApplicationInsights-Java/blob/40809cb6857231e572309a5901e1227305c27c1a/core/src/main/java/com/microsoft/applicationinsights/internal/util/LocalFileSystemUtils.java#L48-L72).)

###  <a name="net"></a>.Net

Ve `ServerTelemetryChannel` výchozím nastavení používá místní složku `%localAppData%\Microsoft\ApplicationInsights` dat aplikace `%TMP%`aktuálního uživatele nebo dočasnou složku . (Viz [implementace](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) zde.)


Prostřednictvím konfiguračního souboru:
```xml
<TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel,   Microsoft.AI.ServerTelemetryChannel">
    <StorageFolder>D:\NewTestFolder</StorageFolder>
</TelemetryChannel>
```

Pomocí kódu:

- Odebrat ServerTelemetryChannel z konfiguračního souboru
- Přidejte tento úryvek do konfigurace:
  ```csharp
  ServerTelemetryChannel channel = new ServerTelemetryChannel();
  channel.StorageFolder = @"D:\NewTestFolder";
  channel.Initialize(TelemetryConfiguration.Active);
  TelemetryConfiguration.Active.TelemetryChannel = channel;
  ```

### <a name="netcore"></a>NetCore

Ve `ServerTelemetryChannel` výchozím nastavení používá místní složku `%localAppData%\Microsoft\ApplicationInsights` dat aplikace `%TMP%`aktuálního uživatele nebo dočasnou složku . (Viz [implementace](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) zde.) V prostředí Linuxu bude místní úložiště zakázáno, pokud není zadána složka úložiště.

Následující fragment kódu ukazuje, jak `ServerTelemetryChannel.StorageFolder` nastavit `ConfigureServices()` metodu `Startup.cs` vaší třídy:

```csharp
services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
```

(Další informace naleznete [v tématu AspNetCore Custom Configuration](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration).)

### <a name="nodejs"></a>Node.js

Ve `%TEMP%/appInsights-node{INSTRUMENTATION KEY}` výchozím nastavení se používá pro uchovávání dat. Oprávnění k přístupu k této složce jsou omezena na aktuálního uživatele a správce. (Viz [implementace](https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/Library/Sender.ts) zde.)

Předponu `appInsights-node` složky lze přepsat změnou hodnoty za `Sender.TEMPDIR_PREFIX` běhu statické proměnné nalezené v [souboru Sender.ts](https://github.com/Microsoft/ApplicationInsights-node.js/blob/7a1ecb91da5ea0febf5ceab13d6a4bf01a63933d/Library/Sender.ts#L384).

### <a name="javascript-browser"></a>JavaScript (prohlížeč)

[Úložiště relací HTML5](https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage) se používá k uchování dat. Používají se dvě samostatné `AI_buffer` `AI_sent_buffer`nárazníky: a . Telemetrie, která je dávkována a `AI_buffer`čeká na odeslání, je uložena v aplikaci . Telemetrie, která byla `AI_sent_buffer` právě odeslána, je umístěna, dokud server ingestování neodpoví, že byl úspěšně přijat. Když je telemetrie úspěšně přijata, je odebrána ze všech vyrovnávacích pamětí. Při přechodných chybách (například uživatel ztratí připojení k síti) zůstane telemetrie v `AI_buffer` zařízení, dokud není úspěšně přijata nebo server ingestování neodpoví, že telemetrie je neplatná (například chybné schéma nebo příliš staré).

Telemetrické vyrovnávací paměti lze [`enableSessionStorageBuffer`](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/legacy/JavaScript/JavaScriptSDK.Interfaces/IConfig.ts#L31) zakázat nastavením na `false`. Když je úložiště relace vypnuto, místní pole se místo toho používá jako trvalé úložiště. Vzhledem k tomu, že sada JavaScript SDK běží na klientském zařízení, má uživatel přístup k tomuto umístění úložiště prostřednictvím vývojářských nástrojů prohlížeče.

### <a name="opencensus-python"></a>OpenCensus Python

Ve výchozím nastavení opencensus Python SDK používá aktuální uživatelskou složku `%username%/.opencensus/.azure/`. Oprávnění k přístupu k této složce jsou omezena na aktuálního uživatele a správce. (Viz [implementace](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/storage.py) zde.) Složka s trvalými daty bude pojmenována po souboru Pythonu, který generoval telemetrii.

Umístění souboru úložiště můžete změnit předáním `storage_path` parametru v konstruktoru exportéra, který používáte.

```python
AzureLogHandler(
  connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000',
  storage_path='<your-path-here>',
)
```

## <a name="how-do-i-send-data-to-application-insights-using-tls-12"></a>Jak odešlu data do Application Insights pomocí TLS 1.2?

Chcete-li zajistit zabezpečení dat při přenosu do koncových bodů Application Insights, důrazně doporučujeme zákazníkům, aby nakonfigurovali svou aplikaci tak, aby používala alespoň zabezpečení transportní vrstvy (TLS) 1.2. Starší verze vrstvy SSL (TLS/Secure Sockets Layer) byly shledány zranitelnými a zatímco v současné době stále pracují na povolení zpětné kompatibility, **nejsou doporučeny**a odvětví se rychle přesouvá k opuštění podpory pro tyto starší protokoly. 

[Rada pro bezpečnostní standardy PCI](https://www.pcisecuritystandards.org/) stanovila [lhůtu pro vypnutí starších](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) verzí Protokolu TLS/SSL a upgrade na bezpečnější protokoly. Jakmile Azure klesne starší podporu, pokud vaše aplikace/klienti nemohou komunikovat přes alespoň TLS 1.2, nebudete moct odesílat data do Application Insights. Přístup, který provedete k testování a ověření podpory TLS vaší aplikace, se bude lišit v závislosti na operačním systému nebo platformě a také na jazyku nebo rámci, který vaše aplikace používá.

Nedoporučujeme explicitně nastavit aplikaci tak, aby používala pouze protokol TLS 1.2, pokud to není nutné, protože to může přerušit funkce zabezpečení na úrovni platformy, které umožňují automaticky detekovat a využívat novější bezpečnější protokoly, jakmile budou k dispozici, například TLS 1,3. Doporučujeme provést důkladnou kontrolu kódu aplikace a zkontrolovat pevné kódování konkrétních verzí TLS/SSL.

### <a name="platformlanguage-specific-guidance"></a>Pokyny specifické pro platformu/jazyk

|Platforma/jazyk | Podpora | Další informace |
| --- | --- | --- |
| Azure App Services  | Podporováno, může být vyžadována konfigurace. | Podpora byla oznámena v dubnu 2018. Přečtěte si oznámení [o konfiguraci podrobnosti](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/).  |
| Aplikace Azure Functions | Podporováno, může být vyžadována konfigurace. | Podpora byla oznámena v dubnu 2018. Přečtěte si oznámení [o konfiguraci podrobnosti](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/). |
|.NET | Podporováno, konfigurace se liší podle verze. | Podrobné informace o konfiguraci pro rozhraní .NET 4.7 a starší verze naleznete v [těchto pokynech](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12).  |
|Monitorování stavu | Podporováno, je vyžadována konfigurace | Sledování stavu závisí na [konfiguraci operačního systému](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) + [.NET konfigurace](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12) pro podporu TLS 1.2.
|Node.js |  Podporováno, ve v10.5.0 může být vyžadována konfigurace. | Pro libovolnou konfiguraci specifickou pro aplikaci použijte [oficiální dokumentaci Node.js TLS/SSL.](https://nodejs.org/api/tls.html) |
|Java | Podporováno, podpora JDK pro TLS 1.2 byla přidána v [JDK 6 aktualizace 121](https://www.oracle.com/technetwork/java/javase/overview-156328.html#R160_121) a [JDK 7](https://www.oracle.com/technetwork/java/javase/7u131-relnotes-3338543.html). | JDK 8 používá [TLS 1.2 ve výchozím nastavení](https://blogs.oracle.com/java-platform-group/jdk-8-will-use-tls-12-as-default).  |
|Linux | Linuxové distribuce mají tendenci spoléhat se na [OpenSSL](https://www.openssl.org) pro podporu TLS 1.2.  | Zkontrolujte [OpenSSL Changelog](https://www.openssl.org/news/changelog.html) a potvrďte, že je vaše verze OpenSSL podporována.|
| Windows 8.0 - 10 | Podporováno a ve výchozím nastavení povoleno. | Chcete-li ověřit, zda stále používáte [výchozí nastavení](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings).  |
| Windows Server 2012 – 2016 | Podporováno a ve výchozím nastavení povoleno. | Ověření, zda stále používáte [výchozí nastavení](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) |
| Aktualizace Windows 7 SP1 a Windows Server 2008 R2 SP1 | Podporováno, ale ve výchozím nastavení není povoleno. | Podrobnosti o povolení najdete na stránce [nastavení registru TLS (Transport Layer Security) (TLS).](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings)  |
| Windows Server 2008 SP2 | Podpora tls 1.2 vyžaduje aktualizaci. | Viz [Aktualizace, která má přidat podporu pro tls 1.2](https://support.microsoft.com/help/4019276/update-to-add-support-for-tls-1-1-and-tls-1-2-in-windows-server-2008-s) v systému Windows Server 2008 SP2. |
|Windows Vista | Nepodporuje se. | Není dostupné.

### <a name="check-what-version-of-openssl-your-linux-distribution-is-running"></a>Zkontrolujte, jakou verzi OpenSSL vaše linuxová distribuce běží

Chcete-li zkontrolovat, jakou verzi openssl jste nainstalovali, otevřete terminál a spusťte:

```terminal
openssl version -a
```

### <a name="run-a-test-tls-12-transaction-on-linux"></a>Spuštění testovací transakce TLS 1.2 na Linuxu

Chcete-li spustit předběžný test, abyste zjistili, zda váš systém Linux může komunikovat přes TLS 1.2., otevřete terminál a spusťte:

```terminal
openssl s_client -connect bing.com:443 -tls1_2
```

## <a name="personal-data-stored-in-application-insights"></a>Osobní údaje uložené v Application Insights

Náš [článek o osobních údajích Application Insights](../../azure-monitor/platform/personal-data-mgmt.md) podrobně popisuje tento problém.

#### <a name="can-my-users-turn-off-application-insights"></a>Můžou moji uživatelé přehledy aplikací vypnout?
Ne přímo. Neposkytujeme přepínač, který by vaši uživatelé mohli ovládat, aby vypnuli Application Insights.

Tuto funkci však můžete implementovat ve vaší aplikaci. Všechny sady SDK obsahují nastavení rozhraní API, které vypne shromažďování telemetrií. 

## <a name="data-sent-by-application-insights"></a>Data odeslaná application insights
Sady SDK se liší mezi platformami a existuje několik součástí, které můžete nainstalovat. (Viz [Application Insights - přehled][start].) Každá komponenta odesílá jiná data.

#### <a name="classes-of-data-sent-in-different-scenarios"></a>Třídy dat odeslaných v různých scénářích

| Vaše akce | Shromažďované třídy dat (viz následující tabulka) |
| --- | --- |
| [Přidání sady Application Insights SDK do webového projektu .NET][greenbrown] |ServerKontext<br/>Odvodit<br/>Čítače výkonu<br/>Žádosti<br/>**Výjimky**<br/>Relace<br/>uživatelé |
| [Instalace sledování stavu ve službě IIS][redfield] |Závislosti<br/>ServerKontext<br/>Odvodit<br/>Čítače výkonu |
| [Přidání sady Application Insights SDK do webové aplikace Java][java] |ServerKontext<br/>Odvodit<br/>Žádost<br/>Relace<br/>uživatelé |
| [Přidání sady JavaScript SDK na webovou stránku][client] |Kontext klienta <br/>Odvodit<br/>stránka<br/>KlientPerf<br/>Ajax |
| [Definování výchozích vlastností][apiproperties] |**Vlastnosti** všech standardních a vlastních událostí |
| [Volání TrackMetric][api] |Číselné hodnoty<br/>**Vlastnosti** |
| [Stopa hovoru*][api] |Název události<br/>**Vlastnosti** |
| [Volání trackexception][api] |**Výjimky**<br/>Výpis zásobníku<br/>**Vlastnosti** |
| Sada SDK nemůže shromažďovat data. Například: <br/> - nemá přístup k perf čítačům<br/> - výjimka v telemetrickém inicializátoru |Diagnostika sady SDK |

Sady [SDK pro jiné platformy][platforms]naleznete v jejich dokumentech.

#### <a name="the-classes-of-collected-data"></a>Třídy shromážděných údajů

| Třída shromážděných dat | Zahrnuje (ne vyčerpávající seznam) |
| --- | --- |
| **Vlastnosti** |**Jakákoli data určená vaším kódem** |
| DeviceContext |`Id`, IP, Národní prostředí, Model zařízení, síť, typ sítě, název OEM, rozlišení obrazovky, instance role, název role, typ zařízení |
| Kontext klienta |Operační systém, národní prostředí, jazyk, síť, rozlišení oken |
| Relace |`session id` |
| ServerKontext |Název počítače, národní prostředí, operační systém, zařízení, uživatelská relace, uživatelský kontext, operace |
| Odvodit |geografická poloha z IP adresy, časového razítka, Operačního systému, prohlížeče |
| Metriky |Název a hodnota metriky |
| Události |Název a hodnota události |
| Zobrazení |Adresa URL a název stránky nebo název obrazovky |
| Perf klienta |Název adresy URL/stránky, doba načítání prohlížeče |
| Ajax |Volání HTTP z webové stránky na server |
| Žádosti |ADRESA URL, doba trvání, kód odpovědi |
| Závislosti |Type(SQL, HTTP, ...), připojovací řetězec nebo IDENTIFIKÁTOR URI, synchronizace/asynchronizace, doba trvání, úspěch, příkaz SQL (s monitorem stavu) |
| **Výjimky** |Typ, **zpráva**, zásobníky volání, zdrojový soubor, číslo řádku,`thread id` |
| Pády |`Process id`, `parent process id`, `crash thread id`; oprava aplikace, `id`, sestavení;  typ výjimky, adresa, důvod; obfuscated symboly a registry, binární počáteční a koncové adresy, binární název a cesta, typ cpu |
| Trasování |**Úroveň zprávy** a závažnosti |
| Čítače výkonu |Čas procesoru, dostupná paměť, rychlost požadavku, míra výjimek, míra soukromých bajtů procesu, rychlost vi, doba trvání požadavku, délka fronty požadavku |
| Dostupnost |Kód odpovědi webového testu, doba trvání každého kroku testu, název testu, časové razítko, úspěch, doba odezvy, umístění testu |
| Diagnostika sady SDK |Trasovací zpráva nebo výjimka |

Některá data můžete [vypnout úpravou souboru ApplicationInsights.config.][config]

> [!NOTE]
> Ip klienta se používá k odvodit geografické umístění, ale ve výchozím nastavení ip data již nejsou uložena a všechny nuly jsou zapsány do přidruženého pole. Chcete-li se lépe porozumět zpracování osobních údajů, doporučujeme tento [článek](../../azure-monitor/platform/personal-data-mgmt.md#application-data). Pokud potřebujete uložit údaje o IP adrese, náš [článek o shromažďování IP adres](https://docs.microsoft.com/azure/azure-monitor/app/ip-collection) vás provede vašimi možnostmi.

## <a name="credits"></a>Kredity
Tento produkt obsahuje data GeoLite2 vytvořená [https://www.maxmind.com](https://www.maxmind.com)společností MaxMind, dostupná od společnosti .



<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiproperties]: ../../azure-monitor/app/api-custom-events-metrics.md#properties
[client]: ../../azure-monitor/app/javascript.md
[config]: ../../azure-monitor/app/configuration-with-applicationinsights-config.md
[greenbrown]: ../../azure-monitor/app/asp-net.md
[java]: ../../azure-monitor/app/java-get-started.md
[platforms]: ../../azure-monitor/app/platforms.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md
