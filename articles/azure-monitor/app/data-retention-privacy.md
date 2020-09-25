---
title: Uchovávání dat a ukládání v Azure Application Insights | Microsoft Docs
description: Prohlášení o zásadách uchovávání a ochrany osobních údajů
ms.topic: conceptual
ms.date: 06/30/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 2205ab1115a66092ae6dd6d75ee7004ab281eec7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91263908"
---
# <a name="data-collection-retention-and-storage-in-application-insights"></a>Shromažďování, uchování a ukládání dat v nástroji Application Insights

Když do své aplikace nainstalujete sadu [Azure Application Insights][start] SDK, pošle se telemetrie o vaší aplikaci do cloudu. Přirozeně informující vývojáři chtějí přesně informovat o tom, jaká data se odesílají, co se stane s daty a jak je můžou mít pod kontrolou. Konkrétně by mohla být posílána citlivá data, kde jsou uložená a jak je zabezpečená? 

Za prvé, krátká odpověď:

* Standardní moduly telemetrie, které spouští "vycházející z boxu", nepravděpodobně posílají citlivá data službě. Telemetrie se týká metriky zatížení, výkonu a využití, sestav výjimek a dalších diagnostických dat. Hlavní data uživatele zobrazená v diagnostických sestavách jsou adresy URL; ale vaše aplikace by neměla v žádném případě vkládat citlivá data do prostého textu v adrese URL.
* Můžete napsat kód, který odesílá další vlastní telemetrii, které vám pomůžou s diagnostikou a monitorováním využití. (Toto rozšíření je skvělou funkcí Application Insights.) Může to být omylem, aby bylo možné napsat tento kód tak, aby obsahoval osobní a další citlivá data. Pokud vaše aplikace pracuje s takovými daty, měli byste použít důkladný proces kontroly na všechen kód, který píšete.
* Při vývoji a testování vaší aplikace je snadno možné zkontrolovat, co posílá sada SDK. Data se zobrazí v oknech výstup ladění rozhraní IDE a prohlížeče.
* Umístění můžete vybrat při vytváření nového prostředku Application Insights. [Tady](https://azure.microsoft.com/global-infrastructure/services/?products=all)se dozvíte víc o dostupnosti Application Insights v jednotlivých oblastech.
*   Zkontrolujte shromážděná data, protože to může zahrnovat data, která jsou v některých případech povolena, ale ne jiné.  Dobrým příkladem je název zařízení. Název zařízení ze serveru nemá žádný vliv na ochranu osobních údajů a je užitečný, ale název zařízení z telefonu nebo přenosného počítače může mít dopad na ochranu osobních údajů a je méně užitečný. Sada SDK vyvinutá primárně pro cílové servery by ve výchozím nastavení shromáždila název zařízení a může být nutné ji přepsat v normálních událostech a výjimkách.

Zbývající část tohoto článku podrobněji vychází z těchto odpovědí. Je navržena tak, aby byla samostatná, takže ji můžete zobrazit kolegům, kteří nejsou součástí svého bezprostředního týmu.

## <a name="what-is-application-insights"></a>Co je Application Insights?
[Azure Application Insights][start] je služba poskytovaná Microsoftem, která pomáhá zlepšit výkon a použitelnost vaší živé aplikace. Monitoruje vaši aplikaci pokaždé, když je spuštěná, jak během testování, tak i po jeho publikování nebo nasazení. Application Insights vytvoří grafy a tabulky, které vám ukáže například dobu, po kterou se dostanete nejvíc uživatelů, jak reagovat na aplikaci a jak dobře je obsluhovaná pomocí všech externích služeb, na kterých závisí. Pokud dojde k chybám, selháním nebo problémům s výkonem, můžete prohledat data telemetrie podrobněji a diagnostikovat příčinu. A služba vám pošle e-maily, pokud dojde ke změnám v dostupnosti a výkonu vaší aplikace.

Chcete-li získat tuto funkci, nainstalujte do aplikace sadu Application Insights SDK, která se stává součástí jejího kódu. Když je vaše aplikace spuštěná, SDK monitoruje svou činnost a odesílá telemetrii do služby Application Insights. Toto je cloudová služba, jejímž hostitelem je [Microsoft Azure](https://azure.com). (Ale Application Insights funguje pro všechny aplikace, ne jenom aplikace, které jsou hostované v Azure.)

Služba Application Insights ukládá a analyzuje telemetrii. Pokud chcete zobrazit analýzu nebo prohledat uloženou telemetrii, přihlaste se ke svému účtu Azure a otevřete Application Insights prostředek pro vaši aplikaci. Můžete také sdílet přístup k datům s ostatními členy týmu nebo se zadanými předplatiteli Azure.

Můžete mít data exportovaná z Application Insights služby, například do databáze nebo externích nástrojů. Každý nástroj poskytnete speciální klíč, který získáte ze služby. V případě potřeby lze klíč odvolat. 

Application Insights sady SDK jsou dostupné pro řadu typů aplikací: webové služby hostované ve vašich vlastních serverech Java EE nebo ASP.NET nebo v Azure. webové klienty – to znamená, že kód spuštěný na webové stránce; desktopové aplikace a služby; aplikace pro zařízení, jako jsou Windows Phone, iOS a Android. Všechny odesílají telemetrii do stejné služby.

## <a name="what-data-does-it-collect"></a>Jaká data shromažďuje?
Existují tři zdroje dat:

* Sada SDK, kterou můžete integrovat s aplikací buď [při vývoji](./asp-net.md) , nebo [v době běhu](./monitor-performance-live-website-now.md). Existují různé sady SDK pro různé typy aplikací. K dispozici je také [sada SDK pro webové stránky](./javascript.md), která se načte do prohlížeče koncového uživatele spolu se stránkou.
  
  * Každá sada SDK má několik [modulů](./configuration-with-applicationinsights-config.md), které používají různé techniky ke shromažďování různých typů telemetrie.
  * Pokud nainstalujete sadu SDK ve vývoji, můžete použít její rozhraní API k posílání vlastní telemetrie, a to i ke standardním modulům. Tato vlastní telemetrie může zahrnovat všechna data, která chcete odeslat.
* Na některých webových serverech existují také agenti, kteří se spouštějí společně s aplikací, a zasílat telemetrii o procesoru, paměti a obsazení sítě. Například virtuální počítače Azure, hostitelé Docker a [servery Java EE](./java-agent.md) můžou mít takové agenty.
* [Testy dostupnosti](./monitor-web-app-availability.md) jsou procesy spouštěné Microsoftem, které odesílají požadavky do vaší webové aplikace v pravidelných intervalech. Výsledky se odesílají do služby Application Insights.

### <a name="what-kinds-of-data-are-collected"></a>Jaké druhy dat se shromažďují?
Hlavní kategorie jsou:

* [Telemetrie webového serveru](./asp-net.md) – požadavky HTTP.  Identifikátor URI, čas potřebný ke zpracování žádosti, kód odpovědi, IP adresa klienta. `Session id`.
* [Webové stránky](./javascript.md) – počty stránek, uživatelů a relací. Doba načítání stránky Výjimek. Volání AJAX.
* Čítače výkonu – paměť, procesor, vstup/výstup, obsazenost sítě.
* Kontext klienta a serveru – operační systém, národní prostředí, typ zařízení, prohlížeč a rozlišení obrazovky.
* [Výjimky](./asp-net-exceptions.md) a zhroucení – **výpisy zásobníku**, `build id` typ procesoru. 
* [Závislosti](./asp-net-dependencies.md) – volání externích služeb, jako jsou REST, SQL a AJAX. Identifikátor URI nebo připojovací řetězec, doba trvání, úspěch, příkaz
* [Testy dostupnosti](./monitor-web-app-availability.md) – doba trvání testu a kroky, odpovědi.
* [Protokoly trasování](./asp-net-trace-logs.md) a [vlastní telemetrie](./api-custom-events-metrics.md)  -  **cokoli, co kódujete do svých protokolů nebo telemetrie**.

[Další podrobnosti](#data-sent-by-application-insights).

## <a name="how-can-i-verify-whats-being-collected"></a>Jak můžu ověřit, co se shromažďuje?
Pokud vyvíjíte aplikaci pomocí sady Visual Studio, spusťte aplikaci v režimu ladění (F5). Telemetrie se zobrazí v okně výstup. Odtud ji můžete zkopírovat a naformátovat jako JSON pro snadnější kontrolu. 

![Snímek obrazovky, který ukazuje spuštění aplikace v režimu ladění v aplikaci Visual Studio.](./media/data-retention-privacy/06-vs.png)

V okně diagnostiky je také přehlednější zobrazení.

V případě webových stránek otevřete okno ladění v prohlížeči.

![Stiskněte klávesu F12 a otevřete kartu síť.](./media/data-retention-privacy/08-browser.png)

### <a name="can-i-write-code-to-filter-the-telemetry-before-it-is-sent"></a>Můžu napsat kód pro filtrování telemetrie před odesláním?
To je možné tím, že napíšete [modul plug-in procesoru telemetrie](./api-filtering-sampling.md).

## <a name="how-long-is-the-data-kept"></a>Jak dlouho se data uchovávají?
Nezpracované datové body (tj. položky, které se dají dotazovat v analýze a prohledávat ve vyhledávání) jsou zachované až 730 dnů. Můžete [vybrat dobu uchovávání dat](./pricing.md#change-the-data-retention-period) 30, 60, 90, 120, 180, 270, 365, 550 nebo 730 dnů. Pokud potřebujete zachovat data déle než 730 dní, můžete pomocí [průběžného exportu](./export-telemetry.md) ji během příjmu dat zkopírovat do účtu úložiště. 

Data uchovávaná déle než 90 dnů se účtují za přidání poplatků. Přečtěte si další informace o Application Insights cenách na [stránce s cenami Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).

Agregovaná data (tj. počty, průměry a další statistická data, která vidíte v Průzkumníkovi metrik) se uchovávají po dobu 1 minuty po 90 dnech.

[Snímky ladění](./snapshot-debugger.md) se ukládají po dobu 15 dnů. Tyto zásady uchovávání informací se nastavují na základě jednotlivých aplikací. Pokud potřebujete tuto hodnotu zvýšit, můžete požádat o zvýšení otevřením případu podpory v Azure Portal.

## <a name="who-can-access-the-data"></a>Kdo má přístup k datům?
Data jsou viditelná pro vás a v případě, že máte účet organizace, členové týmu. 

Může být exportována vámi a členy týmu a může být zkopírována do jiných umístění a předána jiným lidem.

#### <a name="what-does-microsoft-do-with-the-information-my-app-sends-to-application-insights"></a>Co Microsoft dělá s informacemi, které moje aplikace pošle Application Insights?
Microsoft Data používá jenom k tomu, aby službu poskytovala.

## <a name="where-is-the-data-held"></a>Kde jsou uložená data?
* Umístění můžete vybrat při vytváření nového prostředku Application Insights. [Tady](https://azure.microsoft.com/global-infrastructure/services/?products=all)se dozvíte víc o dostupnosti Application Insights v jednotlivých oblastech.

## <a name="how-secure-is-my-data"></a>Jak zabezpečená má moje data?
Application Insights je služba Azure. Zásady zabezpečení jsou popsané v [dokumentu White Paper zabezpečení, ochrana osobních údajů a dodržování předpisů v Azure](https://go.microsoft.com/fwlink/?linkid=392408).

Data jsou uložená na Microsoft Azure serverech. V případě účtů v Azure Portal jsou omezení účtů popsána v [dokumentu zabezpečení, ochrana osobních údajů a dodržování předpisů v Azure](https://go.microsoft.com/fwlink/?linkid=392408).

Přístup k datům od zaměstnanců Microsoftu je omezený. K vašim datům přistupuje pouze s vaším svolením a pokud je to nutné pro podporu vašeho používání Application Insights. 

Pro zlepšení Application Insights se používají data agregovaná napříč všemi aplikacemi pro zákazníky (například sazby dat a Průměrná velikost trasování).

#### <a name="could-someone-elses-telemetry-interfere-with-my-application-insights-data"></a>Je možné, že telemetrie někoho jiného narušuje data Application Insights?
Pomocí klíče instrumentace, který je možné najít v kódu vašich webových stránek, by mohlo odeslat další telemetrii k vašemu účtu. S dostatečnými dodatečnými daty vaše metrika správně nepředstavovala výkon a využití vaší aplikace.

Pokud sdílíte kód s jinými projekty, nezapomeňte odebrat klíč instrumentace.

## <a name="is-the-data-encrypted"></a>Jsou data zašifrovaná?
Všechna data jsou šifrovaná v klidovém stavu a pohyb mezi datovými centry.

#### <a name="is-the-data-encrypted-in-transit-from-my-application-to-application-insights-servers"></a>Jsou data zašifrovaná při přenosu z mé aplikace do Application Insights servery?
Ano, používáme protokol HTTPS k posílání dat na portál prakticky ze všech sad SDK, včetně webových serverů, zařízení a webových stránek HTTPS. 

## <a name="does-the-sdk-create-temporary-local-storage"></a>Vytváří SDK dočasné místní úložiště?

Ano, některé kanály telemetrie budou uchovávat data místně, pokud koncový bod není dostupný. Níže si přečtěte informace o tom, které architektury a kanály telemetrie jsou ovlivněné.

Kanály telemetrie, které využívají místní úložiště, vytvářejí dočasné soubory v adresářích TEMP nebo v adresářích, které jsou omezené na konkrétní účet, na kterém je spuštěná vaše aplikace. K tomu může dojít v případě, že koncový bod nebyl dočasně dostupný nebo pokud jste dosáhli limitu omezení. Až se tento problém vyřeší, kanál telemetrie bude pokračovat v odesílání všech nových a trvalých dat.

Tato trvalá data nejsou šifrována místně. Pokud se to týká, zkontrolujte data a omezte shromažďování soukromých dat. (Další informace najdete v tématu [Jak exportovat a odstranit soukromá data](../platform/personal-data-mgmt.md#how-to-export-and-delete-private-data).)

Pokud zákazník potřebuje nakonfigurovat tento adresář s konkrétními požadavky na zabezpečení, je možné ho nakonfigurovat na rozhraní. Ujistěte se prosím, že proces, ve kterém je aplikace spuštěná, má přístup pro zápis do tohoto adresáře, ale také se ujistěte, že je tento adresář chráněný, aby nedocházelo ke čtení telemetrie nezamýšlenými uživateli.

### <a name="java"></a>Java

`C:\Users\username\AppData\Local\Temp` slouží k uchování dat. Toto umístění není možné konfigurovat z konfiguračního adresáře a oprávnění pro přístup k této složce jsou omezená na konkrétního uživatele s požadovanými přihlašovacími údaji. (Další informace najdete v tématu [implementace](https://github.com/Microsoft/ApplicationInsights-Java/blob/40809cb6857231e572309a5901e1227305c27c1a/core/src/main/java/com/microsoft/applicationinsights/internal/util/LocalFileSystemUtils.java#L48-L72).)

###  <a name="net"></a>.NET

Ve výchozím nastavení `ServerTelemetryChannel` používá místní složku dat aplikace `%localAppData%\Microsoft\ApplicationInsights` nebo složku TEMP aktuálního uživatele `%TMP%` . (Viz [implementace](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) tady.)


Prostřednictvím konfiguračního souboru:
```xml
<TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel,   Microsoft.AI.ServerTelemetryChannel">
    <StorageFolder>D:\NewTestFolder</StorageFolder>
</TelemetryChannel>
```

Prostřednictvím kódu:

- Odebrat ServerTelemetryChannel z konfiguračního souboru
- Přidejte tento fragment kódu do konfigurace:
  ```csharp
  ServerTelemetryChannel channel = new ServerTelemetryChannel();
  channel.StorageFolder = @"D:\NewTestFolder";
  channel.Initialize(TelemetryConfiguration.Active);
  TelemetryConfiguration.Active.TelemetryChannel = channel;
  ```

### <a name="netcore"></a>NetCore

Ve výchozím nastavení `ServerTelemetryChannel` používá místní složku dat aplikace `%localAppData%\Microsoft\ApplicationInsights` nebo složku TEMP aktuálního uživatele `%TMP%` . (Viz [implementace](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) tady.) 

V prostředí Linux bude místní úložiště zakázané, pokud není Zadaná složka úložiště.

> [!NOTE]
> S verzí Release 2.15.0-beta3 a vyšším místním úložištěm se teď automaticky vytvoří pro Linux, Mac a Windows. U systémů, které nejsou systémy Windows, sada SDK automaticky vytvoří místní složku úložiště na základě následující logiky:
> - `${TMPDIR}` – Pokud `${TMPDIR}` je nastavená proměnná prostředí, použije se toto umístění.
> - `/var/tmp` – Pokud předchozí umístění neexistuje, zkusíme to `/var/tmp` .
> - `/tmp` – Pokud žádná předchozí umístění neexistují, zkusíme to `tmp` . 
> - Pokud žádné z těchto umístění neexistují, místní úložiště se nevytvoří a stále se vyžaduje ruční konfigurace. [Pro úplné podrobnosti o implementaci](https://github.com/microsoft/ApplicationInsights-dotnet/pull/1860).

Následující fragment kódu ukazuje, jak nastavit `ServerTelemetryChannel.StorageFolder` v `ConfigureServices()` metodě vaší `Startup.cs` třídy:

```csharp
services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
```

(Další informace najdete v tématu [vlastní konfigurace AspNetCore](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration).)

### <a name="nodejs"></a>Node.js

Ve výchozím nastavení `%TEMP%/appInsights-node{INSTRUMENTATION KEY}` se používá pro trvalá data. Oprávnění pro přístup k této složce jsou omezená na aktuálního uživatele a správce. (Viz [implementace](https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/Library/Sender.ts) tady.)

Předponu složky `appInsights-node` lze přepsat změnou hodnoty za běhu statické proměnné `Sender.TEMPDIR_PREFIX` nalezené v [sender. TS](https://github.com/Microsoft/ApplicationInsights-node.js/blob/7a1ecb91da5ea0febf5ceab13d6a4bf01a63933d/Library/Sender.ts#L384).

### <a name="javascript-browser"></a>JavaScript (prohlížeč)

[Úložiště relací HTML5](https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage) slouží k uchovávání dat. Používají se dvě samostatné vyrovnávací paměti: `AI_buffer` a `AI_sent_buffer` . Telemetrie, která je v dávce a čeká na odeslání, je uložená v `AI_buffer` . Telemetrii, která byla právě odeslána, je umístěna do, `AI_sent_buffer` dokud server pro příjem dat neodpoví, že byl úspěšně přijat. Po úspěšném přijetí telemetrie se odebere ze všech vyrovnávacích pamětí. Při přechodných chybách (například uživatel ztratí připojení k síti), telemetrie zůstane v `AI_buffer` neúspěšném přijetí nebo server pro ingestování odpoví na to, že telemetrie je neplatné (například chybné schéma nebo příliš staré).

Vyrovnávací paměti telemetrie lze zakázat nastavením [`enableSessionStorageBuffer`](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/legacy/JavaScript/JavaScriptSDK.Interfaces/IConfig.ts#L31) na `false` . Když je úložiště relace vypnuté, místo toho se použije místní pole jako trvalé úložiště. Vzhledem k tomu, že sada JavaScript SDK běží na klientském zařízení, má uživatel k tomuto umístění úložiště přístup prostřednictvím vývojářských nástrojů prohlížeče.

### <a name="opencensus-python"></a>OpenCensus Python

Ve výchozím nastavení OpenCensus Python SDK používá aktuální složku uživatele `%username%/.opencensus/.azure/` . Oprávnění pro přístup k této složce jsou omezená na aktuálního uživatele a správce. (Viz [implementace](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/storage.py) tady.) Složka s trvalými daty bude pojmenována po souboru Python, který vygeneroval telemetrii.

Umístění souboru úložiště můžete změnit předáním `storage_path` parametru v konstruktoru používaného vývozce.

```python
AzureLogHandler(
  connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000',
  storage_path='<your-path-here>',
)
```

## <a name="how-do-i-send-data-to-application-insights-using-tls-12"></a>Návody posílat data Application Insights pomocí TLS 1,2?

Aby se zajistilo zabezpečení dat při přenosu do koncových bodů Application Insights, důrazně doporučujeme zákazníkům nakonfigurovat, aby používali aspoň protokol TLS (Transport Layer Security) 1,2. Zjistili jsme, že starší verze TLS/SSL (Secure Sockets Layer) (SSL) jsou zranitelné a i když stále fungují k tomu, aby se zajistila zpětná kompatibilita, **nedoporučuje**se a odvětví se rychle přesouvá na zrušení podpory těchto starších protokolů. 

[Rada standardů zabezpečení PCI](https://www.pcisecuritystandards.org/) nastavila [konečný termín 30. června 2018](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) pro zakázání starších verzí TLS/SSL a upgradování na bezpečnější protokoly. Až Azure sníží podporu starší verze, pokud vaše aplikace nebo klienti nemůžou komunikovat přes aspoň protokol TLS 1,2, nebudete moct odesílat data Application Insights. Přístup, který jste probrali k otestování a ověření podpory protokolu TLS vaší aplikace, se liší v závislosti na operačním systému nebo platformě a na jazyku nebo architektuře, které vaše aplikace používá.

Nedoporučujeme explicitně nastavit aplikaci tak, aby používala protokol TLS 1,2, pokud to není nutné, protože může přerušit funkce zabezpečení na úrovni platformy, které vám umožní automaticky zjišťovat a využívat nové bezpečnější protokoly, když budou k dispozici, jako je například TLS 1,3. Doporučujeme, abyste provedli důkladné auditování kódu vaší aplikace a zkontrolovali zakódujeme konkrétní verze TLS/SSL.

### <a name="platformlanguage-specific-guidance"></a>Doprovodné materiály pro konkrétní platformu nebo jazyk

|Platforma/jazyk | Podpora | Další informace |
| --- | --- | --- |
| Azure App Services  | Podporuje se může vyžadovat konfigurace. | Podpora byla oznámena v dubnu 2018. [Podrobnosti o konfiguraci](https://azure.github.io/AppService/2018/04/17/App-Service-and-Functions-hosted-apps-can-now-update-TLS-versions!)najdete v oznámení.  |
| Aplikace Azure Functions | Podporuje se může vyžadovat konfigurace. | Podpora byla oznámena v dubnu 2018. [Podrobnosti o konfiguraci](https://azure.github.io/AppService/2018/04/17/App-Service-and-Functions-hosted-apps-can-now-update-TLS-versions!)najdete v oznámení. |
|.NET | Podporováno, konfigurace se liší podle verze. | Podrobné informace o konfiguraci pro .NET 4,7 a starší verze najdete v [těchto pokynech](/dotnet/framework/network-programming/tls#support-for-tls-12).  |
|Monitorování stavu | Podporováno, vyžaduje se konfigurace | Monitorování stavu spoléhá na [OS Configuration](/windows-server/security/tls/tls-registry-settings)  +  [konfiguraci rozhraní .NET](/dotnet/framework/network-programming/tls#support-for-tls-12) konfigurace operačního systému pro podporu TLS 1,2.
|Node.js |  V 10.5.0 může být vyžadována konfigurace, která je podporována. | Pro jakoukoliv konfiguraci specifickou pro aplikaci použijte [oficiální Node.js dokumentaci TLS/SSL](https://nodejs.org/api/tls.html) . |
|Java | Podpora JDK pro TLS 1,2 byla přidána do [JDK 6 aktualizace 121](https://www.oracle.com/technetwork/java/javase/overview-156328.html#R160_121) a [JDK 7](https://www.oracle.com/technetwork/java/javase/7u131-relnotes-3338543.html). | JDK 8 používá standardně [TLS 1,2](https://blogs.oracle.com/java-platform-group/jdk-8-will-use-tls-12-as-default).  |
|Linux | Distribuce systému Linux se obvykle spoléhají na [OpenSSL](https://www.openssl.org) pro podporu TLS 1,2.  | Zkontrolujte [OpenSSL protokolu změn](https://www.openssl.org/news/changelog.html) a potvrďte, že je podporovaná vaše verze OpenSSL.|
| Windows 8,0 – 10 | Podporované a povolené ve výchozím nastavení. | Potvrďte, že stále používáte [výchozí nastavení](/windows-server/security/tls/tls-registry-settings).  |
| Windows Server 2012 – 2016 | Podporované a povolené ve výchozím nastavení. | Potvrzení, že stále používáte [výchozí nastavení](/windows-server/security/tls/tls-registry-settings) |
| Windows 7 SP1 a Windows Server 2008 R2 SP1 | Podporované, ale nejsou ve výchozím nastavení povolené. | Podrobnosti o tom, jak povolit, najdete na stránce [nastavení registru TLS (Transport Layer Security)](/windows-server/security/tls/tls-registry-settings) .  |
| Windows Server 2008 SP2 | Podpora TLS 1,2 vyžaduje aktualizaci. | Pokud [chcete přidat podporu pro TLS 1,2](https://support.microsoft.com/help/4019276/update-to-add-support-for-tls-1-1-and-tls-1-2-in-windows-server-2008-s) ve Windows serveru 2008 SP2, viz aktualizace. |
|Windows Vista | Nepodporuje se. | Není k dispozici

### <a name="check-what-version-of-openssl-your-linux-distribution-is-running"></a>Ověřte, jakou verzi OpenSSL je vaše distribuce systému Linux spuštěná.

Pokud chcete zjistit, jakou verzi OpenSSL máte nainstalovanou, otevřete terminál a spusťte příkaz:

```terminal
openssl version -a
```

### <a name="run-a-test-tls-12-transaction-on-linux"></a>Spuštění testovací transakce TLS 1,2 na Linux

Chcete-li spustit předběžný test, aby bylo možné zjistit, zda systém Linux může komunikovat přes TLS 1,2, otevřete terminál a spusťte příkaz:

```terminal
openssl s_client -connect bing.com:443 -tls1_2
```

## <a name="personal-data-stored-in-application-insights"></a>Osobní údaje uložené v Application Insights

Náš [článek Application Insights osobních údajů](../platform/personal-data-mgmt.md) popisuje tento problém podrobně.

#### <a name="can-my-users-turn-off-application-insights"></a>Je možné, že se moji uživatelé vypnou Application Insights?
Ne přímo. Neposkytujeme přepínač, který můžou uživatelé používat k vypnutí Application Insights.

Tuto funkci však můžete v aplikaci implementovat. Všechny sady SDK obsahují nastavení rozhraní API, které vypne shromažďování telemetrie. 

## <a name="data-sent-by-application-insights"></a>Data odesílaná Application Insights
Sady SDK se mezi platformami liší a je možné nainstalovat několik součástí. (Podívejte se na [Application Insights – přehled][start].) Každá součást odesílá jiná data.

#### <a name="classes-of-data-sent-in-different-scenarios"></a>Třídy dat odesílaných v různých scénářích

| Vaše akce | Shromážděné datové třídy (viz další tabulka) |
| --- | --- |
| [Přidání sady SDK Application Insights do webového projektu .NET][greenbrown] |ServerContext<br/>Odvodit<br/>Čítače výkonu<br/>Žádosti<br/>**Výjimky**<br/>Relace<br/>uživatelé |
| [Instalace Monitorování stavu ve službě IIS][redfield] |Závislosti<br/>ServerContext<br/>Odvodit<br/>Čítače výkonu |
| [Přidání sady SDK Application Insights do webové aplikace v jazyce Java][java] |ServerContext<br/>Odvodit<br/>Požadavek<br/>Relace<br/>uživatelé |
| [Přidat sadu JavaScript SDK na webovou stránku][client] |Instance třídy ClientContext <br/>Odvodit<br/>Stránka<br/>ClientPerf<br/>Ajax |
| [Definovat výchozí vlastnosti][apiproperties] |**Vlastnosti** všech standardních a vlastních událostí |
| [TrackMetric volání][api] |Číselné hodnoty<br/>**Vlastnosti** |
| [Hovorová stopa *][api] |Název události<br/>**Vlastnosti** |
| [TrackException volání][api] |**Výjimky**<br/>Výpis zásobníku<br/>**Vlastnosti** |
| Sada SDK nemůže shromažďovat data. Příklad: <br/> – nejde získat přístup k čítačům výkonu.<br/> – výjimka v inicializátoru telemetrie |Diagnostika sady SDK |

Pro [sady SDK pro jiné platformy][platforms]se podívejte na jejich dokumenty.

#### <a name="the-classes-of-collected-data"></a>Třídy shromážděných dat

| Shromážděná data – třída | Zahrnuje (není vyčerpávající seznam). |
| --- | --- |
| **Vlastnosti** |**Všechna data – určená vaším kódem** |
| DeviceContext |`Id`, IP, locale, model zařízení, síť, typ sítě, název výrobce OEM, rozlišení obrazovky, instance role, název role, typ zařízení |
| Instance třídy ClientContext |Rozlišení operačního systému, národního prostředí, jazyka, sítě a oken |
| Relace |`session id` |
| ServerContext |Název počítače, národní prostředí, operační systém, zařízení, uživatelská relace, kontext uživatele, operace |
| Odvodit |geografické umístění z IP adresy, časového razítka, operačního systému, prohlížeče |
| Metriky |Název a hodnota metriky |
| Události |Název a hodnota události |
| PageViews |Adresa URL a název stránky nebo název obrazovky |
| Výkon klienta |Adresa URL/název stránky, čas načtení prohlížeče |
| Ajax |Volání HTTP z webové stránky na server |
| Žádosti |Adresa URL, doba trvání, kód odpovědi |
| Závislosti |Typ (SQL, HTTP,...), připojovací řetězec nebo identifikátor URI, Sync/Async, Duration, úspěch, příkaz SQL (s Monitorování stavu) |
| **Výjimky** |Typ, **zpráva**, zásobníky volání, zdrojový soubor, číslo řádku, `thread id` |
| Chybě |`Process id`, `parent process id` , `crash thread id` ; Oprava aplikace, `id` , Build;  Typ výjimky, adresa, důvod; zakódováné symboly a registry, binární počáteční a koncové adresy, binární název a cesta, typ procesoru |
| Trasování |Úroveň **zprávy** a závažnosti |
| Čítače výkonu |Čas procesoru, dostupná paměť, frekvence požadavků, četnost výjimek, zpracování soukromých bajtů, frekvence v/v, doba trvání žádosti, délka fronty požadavků |
| Dostupnost |Kód odpovědi webového testu, doba trvání každého testovacího kroku, název testu, časové razítko, úspěch, doba odezvy, umístění testu |
| Diagnostika sady SDK |Trasovat zprávu nebo výjimku |

[Některá data můžete odpínat úpravou ApplicationInsights.config][config]

> [!NOTE]
> IP adresa klienta se používá k odvodit zeměpisnou polohu, ale ve výchozím nastavení se data IP už neukládají a do přidruženého pole se zapisují všechny nuly. Chcete-li získat další informace o zpracování osobních údajů, doporučujeme tento [článek](../platform/personal-data-mgmt.md#application-data). Pokud potřebujete ukládat údaje o IP adrese, Projděte si tyto možnosti podle [článku](./ip-collection.md) .

## <a name="credits"></a>Kredity
Tento produkt zahrnuje data GeoLite2 vytvořená v MaxMind, která jsou dostupná z [https://www.maxmind.com](https://www.maxmind.com) .



<!--Link references-->

[api]: ./api-custom-events-metrics.md
[apiproperties]: ./api-custom-events-metrics.md#properties
[client]: ./javascript.md
[config]: ./configuration-with-applicationinsights-config.md
[greenbrown]: ./asp-net.md
[java]: ./java-get-started.md
[platforms]: ./platforms.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[redfield]: ./monitor-performance-live-website-now.md
[start]: ./app-insights-overview.md

