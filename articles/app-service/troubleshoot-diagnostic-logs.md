---
title: Povolit protokolování diagnostiky pro aplikace – Azure App Service
description: Naučte se, jak povolit diagnostické protokolování a přidat instrumentaci do vaší aplikace, a jak získat přístup k informacím protokolovaným v Azure.
services: app-service
documentationcenter: .net
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: af6d8b61c5d49ae219e90513abb93185f957222e
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074058"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Povolit protokolování diagnostiky pro aplikace v Azure App Service
## <a name="overview"></a>Přehled
Azure poskytuje integrovanou diagnostiku, která vám pomůže s laděním [aplikace App Service](https://go.microsoft.com/fwlink/?LinkId=529714). V tomto článku se dozvíte, jak povolit diagnostické protokolování a přidat instrumentaci do aplikace, a jak získat přístup k informacím protokolovaným v Azure.

Tento článek používá [Azure Portal](https://portal.azure.com) a Azure CLI pro práci s diagnostickými protokoly. Informace o práci s diagnostickými protokoly pomocí sady Visual Studio najdete v tématu [řešení potíží s Azure v aplikaci Visual Studio](troubleshoot-dotnet-visual-studio.md).

## <a name="whatisdiag"></a>Diagnostika webového serveru a Application Diagnostics
App Service poskytuje diagnostické funkce pro protokolování informací z webového serveru i z webové aplikace. Tyto jsou logicky rozděleny na **diagnostiku webových serverů** a **Application Diagnostics**.

### <a name="web-server-diagnostics"></a>Diagnostika webového serveru
Můžete povolit nebo zakázat následující typy protokolů:

* **Podrobné protokolování chyb** – podrobné informace o všech požadavcích, jejichž výsledkem je stavový kód HTTP 400 nebo vyšší. Může obsahovat informace, které vám pomohou určit, proč Server vrátil kód chyby. Jeden soubor HTML se vygeneruje pro každou chybu v systému souborů aplikace a zachová se až 50 chyb (souborů). Když počet souborů HTML překračuje 50, automaticky se odstraní nejstarší 26 souborů.
* **Trasování chybných požadavků** – podrobné informace o neúspěšných žádostech, včetně trasování komponent služby IIS použitých ke zpracování žádosti a doby trvání jednotlivých komponent. To je užitečné, pokud chcete zlepšit výkon webu nebo izolovat konkrétní chybu protokolu HTTP. Pro každou chybu v systému souborů aplikace je vygenerována jedna složka. Zásady uchovávání souborů jsou stejné jako podrobné protokolování chyb.
* **Protokolování webového serveru** – informace o transakcích HTTP pomocí [formátu souboru protokolu W3C Extended](/windows/desktop/Http/w3c-logging). Je užitečné při určování celkové metriky lokality, jako je počet zpracovaných požadavků nebo kolik požadavků z konkrétní IP adresy.

### <a name="application-diagnostics"></a>Diagnostika aplikace
Application Diagnostics umožňuje zachytit informace vytvářené webovou aplikací. ASP.NET aplikace mohou použít třídu [System. Diagnostics. Trace](/dotnet/api/system.diagnostics.trace) k protokolování informací do protokolu nástroje Application Diagnostics. Příklad:

    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

Za běhu můžete tyto protokoly načíst pro pomoc s řešením potíží. Další informace najdete v tématu [řešení potíží s Azure App Service v aplikaci Visual Studio](troubleshoot-dotnet-visual-studio.md).

App Service také zaznamená informace o nasazení při publikování obsahu do aplikace. K tomu dochází automaticky a není k dispozici žádné nastavení konfigurace pro protokolování nasazení. Protokolování nasazení umožňuje určit, proč nasazení selhalo. Pokud například použijete vlastní skript nasazení, můžete pomocí protokolování nasazení zjistit, proč se skript nedaří.

## <a name="enablediag"></a>Jak povolit diagnostiku
Pokud chcete povolit diagnostiku v [Azure Portal](https://portal.azure.com), přejděte na stránku vaší aplikace a klikněte na **Nastavení > diagnostické protokoly**.

<!-- todo:cleanup dogfood addresses in screenshot -->
![Součást protokolů](./media/web-sites-enable-diagnostic-log/logspart.png)

Pokud povolíte **nástroj Application Diagnostics**, zvolíte také **úroveň**. V následující tabulce jsou uvedeny kategorie protokolů, které jednotlivé úrovně zahrnují:

| Level| Zahrnuté kategorie protokolů |
|-|-|
|**Disabled** (Zakázáno) | Žádné |
|**Chyba** | Chyba, kritická |
|**Upozornění** | Upozornění, chyba, kritická|
|**Informace o** | Informace, varování, chyba, kritické|
|**Verbose** | Trasování, ladění, informace, varování, chyba, kritická (všechny kategorie) |
|-|-|

Pro **protokolování aplikace**můžete možnost systému souborů dočasně zapnout pro účely ladění. Tato možnost se automaticky vypne za 12 hodin. Můžete také zapnout možnost úložiště objektů BLOB a vybrat kontejner objektů blob, do kterého se mají zapisovat protokoly.

> [!NOTE]
> Do úložiště objektů BLOB se teď dají zapisovat jenom protokoly aplikací .NET. Protokoly aplikací v jazyce Java, PHP, Node. js a Python lze uložit pouze do systému souborů (bez úprav kódu pro zápis protokolů do externího úložiště).
>
>

Pro **protokolování webového serveru**můžete vybrat **úložiště** nebo **systém souborů**. Výběr **úložiště** vám umožní vybrat účet úložiště a pak kontejner objektů blob, do kterého se protokoly zapisují. 

Pokud ukládáte protokoly do systému souborů, můžete k souborům přistupovat pomocí FTP nebo stahovat jako archiv zip pomocí Azure CLI.

Ve výchozím nastavení se protokoly neodstraní automaticky (s výjimkou **protokolování aplikace (systém souborů)** ). Chcete-li automaticky odstranit protokoly, nastavte pole **Doba uchování (dny)** .

> [!NOTE]
> Pokud [znovu vygenerujete přístupové klíče účtu úložiště](../storage/common/storage-create-storage-account.md), musíte obnovit příslušnou konfiguraci protokolování tak, aby používala aktualizované klíče. Použijte následující postup:
>
> 1. Na kartě **Konfigurovat** nastavte příslušnou funkci protokolování na **vypnuto**. Uložte nastavení.
> 2. Znovu povolte protokolování do objektu BLOB účtu úložiště. Uložte nastavení.
>
>

Současně lze povolit jakoukoli kombinaci systému souborů nebo úložiště objektů BLOB a mít jednotlivé konfigurace na úrovni protokolu. Například můžete chtít protokolovat chyby a upozornění do úložiště objektů BLOB jako dlouhodobé řešení protokolování a zároveň povolit protokolování systému souborů s úrovní podrobností.

I když obě umístění úložiště poskytují stejné základní informace pro protokolované události, protokol **BLOB Storage** poskytuje další informace, jako je ID instance, ID vlákna a podrobnější časové razítko (formát Tick) než protokolování do **systému souborů**.

> [!NOTE]
> Informace uložené v **úložišti objektů BLOB** jsou k dispozici pouze pomocí klienta úložiště nebo aplikace, která může pracovat přímo s těmito systémy úložišť. Visual Studio 2013 například obsahuje Průzkumník služby Storage, která se dá použít k prozkoumávání úložiště objektů BLOB a HDInsight má přístup k datům uloženým v úložišti objektů BLOB. Můžete také napsat aplikaci, která přistupuje k Azure Storage pomocí jedné ze [sad Azure SDK](https://azure.microsoft.com/downloads/).
>

## <a name="download"></a>Jak: Stáhnout protokoly
Diagnostické informace uložené do systému souborů aplikace lze získat přímo pomocí serveru FTP. Můžete ji také stáhnout jako archiv zip pomocí Azure CLI.

Struktura adresáře, v níž jsou uloženy protokoly, je následující:

* **Protokoly aplikací** –/LogFiles/Application/. Tato složka obsahuje jeden nebo více textových souborů obsahujících informace vytvářené protokolováním aplikací.
* **Trasování chybných žádostí** -/LogFiles/W3SVC # # # # # # # # #/. Tato složka obsahuje soubor XSL a jeden nebo více souborů XML. Zajistěte, abyste stáhli soubor XSL do stejného adresáře jako soubor XML, protože soubor XSL poskytuje funkce pro formátování a filtrování obsahu souborů XML při prohlížení v aplikaci Internet Explorer.
* **Podrobné protokoly chyb** –/LogFiles/DetailedErrors/. Tato složka obsahuje jeden nebo více souborů. htm, které poskytují podrobné informace o všech chybách protokolu HTTP, ke kterým došlo.
* **Web Server Logs** - /LogFiles/http/RawLogs. Tato složka obsahuje jeden nebo více textových souborů formátovaných pomocí [rozšířeného formátu souboru protokolu W3C](/windows/desktop/Http/w3c-logging).
* **Protokoly nasazení** –/LogFiles/Git. Tato složka obsahuje protokoly generované interními procesy nasazení, které používá Azure App Service, a protokoly pro nasazení Git. Protokoly nasazení můžete najít také v části D:\home\site\deployments.

### <a name="ftp"></a>FTP

Pokud chcete otevřít připojení FTP k serveru FTP vaší aplikace, přečtěte si téma [nasazení aplikace pro Azure App Service pomocí FTP/s](deploy-ftp.md).

Po připojení k serveru FTP/S vaší aplikace otevřete složku soubory protokolů , kde jsou uložené soubory protokolu.

### <a name="download-with-azure-cli"></a>Stáhnout pomocí Azure CLI
Pokud chcete stáhnout soubory protokolu pomocí rozhraní příkazového řádku Azure, otevřete nový příkazový řádek, PowerShell, bash nebo relaci Terminálové služby a zadejte tento příkaz:

    az webapp log download --resource-group resourcegroupname --name appname

Tento příkaz uloží protokoly aplikace s názvem AppName do souboru s názvem **webapp_logs. zip** v aktuálním adresáři.

> [!NOTE]
> Pokud jste nenainstalovali rozhraní příkazového řádku Azure CLI nebo ho nenakonfigurovali, aby používal vaše předplatné Azure, přečtěte si téma [Jak používat Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).
>
>

## <a name="how-to-view-logs-in-application-insights"></a>Postup: Zobrazit protokoly v Application Insights
Visual Studio Application Insights poskytuje nástroje pro filtrování a hledání protokolů a pro korelaci protokolů s požadavky a dalšími událostmi.

1. Přidejte sadu Application Insights SDK do projektu v sadě Visual Studio.
   * V Průzkumník řešení klikněte pravým tlačítkem myši na projekt a vyberte Přidat Application Insights. Rozhraní vás provede kroky, které zahrnují vytvoření prostředku Application Insights. [Víc se uč](../azure-monitor/app/asp-net.md)
2. Přidejte do projektu balíček naslouchacího procesu trasování.
   * Klikněte pravým tlačítkem na projekt a vyberte Spravovat balíčky NuGet. Vybrat `Microsoft.ApplicationInsights.TraceListener` [Další informace](../azure-monitor/app/asp-net-trace-logs.md)
3. Nahrajte projekt a spusťte ho, aby se generovala data protokolu.
4. V [Azure Portal](https://portal.azure.com/)přejděte k novému prostředku Application Insights a otevřete **hledání**. Měla by se zobrazit vaše data protokolu spolu s požadavky, využitím a další telemetrie. Doručení některých telemetrie může trvat několik minut: klikněte na aktualizovat. [Víc se uč](../azure-monitor/app/diagnostic-search.md)

[Další informace o sledování výkonu pomocí Application Insights](../azure-monitor/app/azure-web-apps.md)

## <a name="streamlogs"></a>Jak: Protokoly datových proudů
Při vývoji aplikace je často užitečné zobrazit informace o protokolování téměř v reálném čase. Pomocí Azure CLI můžete streamovat informace o protokolování do vývojového prostředí.

> [!NOTE]
> Některé typy vyrovnávací paměti pro protokolování zapisují do souboru protokolu, což může vést k vyřazení událostí mimo pořadí v datovém proudu. Například položka aplikačního protokolu, která se zobrazí, když uživatel navštíví stránku, se může zobrazit v datovém proudu předtím, než odpovídající položka protokolu HTTP pro požadavek na stránku.
>
> [!NOTE]
> Streamování protokolů také streamuje informace zapsané do libovolného textového souboru, který je uložený ve složce **D:\\\\ Home\\LogFiles** .
>
>

### <a name="streaming-with-azure-cli"></a>Streamování pomocí Azure CLI
Pokud chcete streamovat informace o protokolování, otevřete nový příkazový řádek, PowerShell, bash nebo relaci terminálu a zadejte následující příkaz:

    az webapp log tail --name appname --resource-group myResourceGroup

Tento příkaz se připojí k aplikaci s názvem AppName a do okna zahájí streamované informace, jako by v aplikaci došlo k událostem protokolu. Všechny informace zapsané do souborů, které končí. txt,. log nebo. htm uložené v adresáři/LogFiles (d:/Home/LogFiles), jsou streamované do místní konzoly.

Chcete-li filtrovat konkrétní události, jako jsou například chyby, použijte parametr **--Filter** . Příklad:

    az webapp log tail --name appname --resource-group myResourceGroup --filter Error

Chcete-li filtrovat konkrétní typy protokolů, jako je například HTTP, použijte parametr **--path** . Příklad:

    az webapp log tail --name appname --resource-group myResourceGroup --path http

> [!NOTE]
> Pokud jste nenainstalovali rozhraní příkazového řádku Azure CLI nebo ho nenakonfigurovali, aby používal vaše předplatné Azure, přečtěte si téma [Jak používat Azure CLI](../cli-install-nodejs.md).
>
>

## <a name="understandlogs"></a>Jak: Principy diagnostických protokolů
### <a name="application-diagnostics-logs"></a>Protokoly diagnostiky aplikace
Application Diagnostics ukládá informace v konkrétním formátu pro aplikace .NET v závislosti na tom, zda ukládáte protokoly do systému souborů nebo úložiště objektů BLOB. 

Základní sada uložených dat je v obou typech úložiště stejná – datum a čas, kdy k události došlo, ID procesu, který vytvořil událost, typ události (informace, upozornění, chyba) a zpráva události. Použití systému souborů pro úložiště protokolů je užitečné v případě, že potřebujete okamžitý přístup k řešení problému, protože soubory protokolu se aktualizují blízko sebe. Úložiště objektů BLOB se používá pro účely archivace, protože soubory jsou uložené do mezipaměti a pak se vyprázdní do kontejneru úložiště podle plánu.

**Systém souborů**

Každý řádek protokolovaný do systému souborů nebo přijatý pomocí streamování je v následujícím formátu:

    {Date}  PID[{process ID}] {event type/level} {message}

Například chybová událost by mohla vypadat podobně jako v následující ukázce:

    2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

Protokolování do systému souborů poskytuje základní informace o třech dostupných metodách, které poskytují pouze čas, ID procesu, úroveň události a zprávu.

**Blob Storage**

Při protokolování do úložiště objektů BLOB se data ukládají ve formátu hodnot oddělených čárkami (CSV). Další pole jsou protokolována k poskytnutí podrobnějších informací o události. Pro každý řádek ve sdíleném svazku clusteru se používají tyto vlastnosti:

| Název vlastnosti | Hodnota/formát |
| --- | --- |
| Date |Datum a čas výskytu události |
| Level |Úroveň události (například chyba, upozornění, informace) |
| ApplicationName |Název aplikace |
| ID instance |Instance aplikace, na které došlo k události |
| EventTickCount |Datum a čas, kdy došlo k události, ve formátu osové (větší přesnost) |
| ID události |ID události této události<p><p>Pokud není zadaný, použije se výchozí hodnota 0. |
| PID |ID procesu |
| Identifikátory |ID vlákna, které vytvořilo událost |
| Message |Zpráva s podrobnostmi o události |

Data uložená v objektu BLOB by vypadala podobně jako v následujícím příkladu:

    date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
    2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [!NOTE]
> V případě ASP.NET Core se protokolování provádí pomocí poskytovatele [Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) , který tento zprostředkovatel uloží do kontejneru objektů BLOB další soubory protokolů. Další informace najdete v tématu [ASP.NET Core protokolování v Azure](/aspnet/core/fundamentals/logging).
>
>

### <a name="failed-request-traces"></a>Trasování chybných žádostí
Trasování chybných požadavků se ukládají do souborů XML s názvem **fr # # # # # #. XML**. Aby bylo možné zjednodušit zobrazování protokolovaných informací, šablona stylů XSL s názvem **FREB. xsl** je k dispozici ve stejném adresáři jako soubory XML. Pokud otevřete některý ze souborů XML v aplikaci Internet Explorer, aplikace Internet Explorer použije šablonu stylů XSL k poskytnutí formátovaného zobrazení informací o trasování, podobně jako v následujícím příkladu:

![neúspěšný požadavek zobrazený v prohlížeči](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

> [!NOTE]
> Snadný způsob, jak zobrazit naformátované trasování chybných požadavků, je přejít na stránku vaší aplikace na portálu. V nabídce vlevo vyberte diagnostikovat **a řešit problémy**, vyhledejte **protokoly pro trasování chybných požadavků**a potom klikněte na ikonu pro procházení a zobrazení požadovaného trasování.
>

### <a name="detailed-error-logs"></a>Podrobné protokoly chyb
Podrobné protokoly chyb jsou dokumenty HTML, které poskytují podrobnější informace o chybách HTTP, ke kterým došlo. Vzhledem k tomu, že jsou jednoduše dokumenty HTML, lze je zobrazit pomocí webového prohlížeče.

### <a name="web-server-logs"></a>Protokoly webového serveru
Protokoly webového serveru jsou formátovány pomocí [rozšířeného formátu souboru protokolu W3C](/windows/desktop/Http/w3c-logging). Tyto informace lze číst pomocí textového editoru nebo analyzovat pomocí nástrojů, jako je [analyzátor protokolů](https://go.microsoft.com/fwlink/?LinkId=246619).

> [!NOTE]
> Protokoly vytvořené pomocí Azure App Service nepodporují pole **s-ComputerName**, **s-IP**nebo **cs-Version** .
>
>

## <a name="nextsteps"></a> Další kroky
* [Jak monitorovat Azure App Service](web-sites-monitor.md)
* [Řešení potíží s Azure App Service v aplikaci Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [Analýza protokolů aplikací v HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
