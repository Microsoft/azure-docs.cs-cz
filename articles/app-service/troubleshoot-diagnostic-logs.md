---
title: Povolení diagnostického protokolování u aplikací – Azure App Service
description: Zjistěte, jak povolit diagnostické protokolování a přidat do svojí aplikace instrumentaci, jakož i jak získat přístup k informacím protokolovaným v Azure.
services: app-service
documentationcenter: .net
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: d5a94258e8c17d13e15f22f9fa96ef0647105abe
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2018
ms.locfileid: "53807869"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Povolit protokolování diagnostiky aplikací ve službě Azure App Service
## <a name="overview"></a>Přehled
Azure nabízí předdefinovanou diagnostiku, která vám pomůže s laděním [aplikace služby App Service](https://go.microsoft.com/fwlink/?LinkId=529714). V tomto článku se dozvíte, jak povolit diagnostické protokolování a přidat do svojí aplikace instrumentaci, jakož i jak získat přístup k informacím protokolovaným v Azure.

Tento článek používá [webu Azure portal](https://portal.azure.com) a Azure CLI pro práci s diagnostické protokoly. Informace o práci se diagnostické protokoly pomocí sady Visual Studio najdete v tématu [řešení potíží s Azure v sadě Visual Studio](troubleshoot-dotnet-visual-studio.md).

## <a name="whatisdiag"></a>Diagnostika webového serveru a application diagnostics
App Service poskytují diagnostické funkce pro protokolování informací z webového serveru a webové aplikace. Tyto jsou logicky rozdělena do **webového serveru diagnostiky** a **konzole application diagnostics**.

### <a name="web-server-diagnostics"></a>Diagnostika webového serveru
Můžete povolit nebo zakázat následující typy protokolů:

* **Podrobné protokolování chyb** – podrobné informace o chybě pro stavové kódy HTTP, které indikují chybu (stavový kód 400 nebo vyšší). Může obsahovat informace, které vám pomůže určit, proč server vrátil kód chyby.
* **Se nezdařilo, trasování požadavku** – podrobné informace o neúspěšných požadavcích, včetně trasování součásti služby IIS používá ke zpracování požadavku a doba trvání v jednotlivých komponentách. To je užitečné, pokud se pokoušíte zvýšit výkon webu a izolovat co způsobuje konkrétní chyba protokolu HTTP, který se má vrátit.
* **Web, protokolování na Server** – informace o transakce HTTP pomocí [rozšířený formát protokolu W3C souboru](https://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Je užitečné při určování celkové lokality metriky, jako je počet požadavků zpracovaných nebo kolik žádostí se z konkrétní IP adresu.

### <a name="application-diagnostics"></a>Diagnostika aplikace
Konzole Application diagnostics můžete zachytit informace vytvořené webové aplikace. Můžete použít aplikace ASP.NET [System.Diagnostics.Trace](https://msdn.microsoft.com/library/36hhw2t6.aspx) třídy k protokolování informací do protokolu diagnostiky aplikace. Příklad:

    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

Za běhu můžete načíst tyto protokoly, které pomůžou při řešení potíží. Další informace najdete v tématu [řešení potíží s Azure App Service v sadě Visual Studio](troubleshoot-dotnet-visual-studio.md).

App Service zaznamenávat informace o nasazení při publikování obsahu do aplikace. Probíhá automaticky a nejsou žádná nastavení konfigurace pro nasazení protokolování. Nasazení protokolování umožňuje určit, proč se nepovedlo nasazení. Například pokud používáte skript vlastního nasazení, můžete použít nasazení protokolování k určení, proč se skript selhává.

## <a name="enablediag"></a>Postup povolení diagnostiky
Povolení diagnostiky v [webu Azure portal](https://portal.azure.com), přejděte na stránku pro vaši aplikaci a klikněte na tlačítko **Nastavení > diagnostické protokoly**.

<!-- todo:cleanup dogfood addresses in screenshot -->
![Část protokoly](./media/web-sites-enable-diagnostic-log/logspart.png)

Když povolíte **konzole application diagnostics**, také zvolte **úroveň**. Toto nastavení vám umožní filtrovat informace zachycen **informační**, **upozornění**, nebo **chyba** informace. Nastavení na **podrobné** zaznamená všechny informace o vytvářené aplikace.

> [!NOTE]
> Na rozdíl od změny v souboru web.config, neprobíhá povolení rozhraní Application diagnostics nebo změna úrovně diagnostický protokol, na kterém aplikace běží v rámci domény aplikace.
>
>

Pro **protokolování aplikací**, můžete zapnout možnost souboru systému dočasně pro účely ladění. Tato volba vypne automaticky za 12 hodin. Můžete také zapnout možnost úložiště objektů blob kontejner objektů blob, do kterého se zapisují protokoly.

> [!NOTE]
> Momentálně se podporuje jenom protokoly aplikací .NET je možné zapisovat na úložiště objektů blob. Java, PHP, Node.js, Python, aplikační protokoly se dají ukládat jenom v systému souborů (bez úpravy kódu pro zápis protokolů na externí úložiště).
>
>

Pro **protokolování webového serveru**, můžete vybrat **úložiště** nebo **systém souborů**. Výběr **úložiště** vám umožní vybrat účet úložiště a kontejner objektů blob, který protokolů zapisován. 

Pokud uchováváte v systému souborů protokolů, soubory můžete získat přístup pomocí protokolu FTP nebo stáhnout jako archiv Zip s použitím rozhraní příkazového řádku Azure.

Ve výchozím nastavení, nejsou automaticky odstraněny protokoly (s výjimkou produktů **protokolování aplikace (systém souborů)**). Pokud chcete automaticky odstranit protokoly, nastavte **doba uchování (dny)** pole.

> [!NOTE]
> Pokud jste [znovu vygenerovat přístupové klíče účtu úložiště](../storage/common/storage-create-storage-account.md), musíte obnovit konfiguraci příslušných protokolování použití aktualizované klíčů. Použijte následující postup:
>
> 1. V **konfigurovat** kartu, nastavte funkci příslušných protokolování na **vypnout**. Uložte nastavení.
> 2. Povolte protokolování do účtu blob storage to znovu. Uložte nastavení.
>
>

Libovolnou kombinaci systému nebo objekt blob úložiště souborů je možné povolit ve stejnou dobu a mají vlastní protokol úrovni konfigurace. Můžete například chtít protokolovat chyby a upozornění do úložiště objektů blob jako dlouhodobé řešení pro protokolování při povolování protokolování systému souborů s úroveň verbose.

Zatímco i umístění úložiště poskytují stejné základní informace o protokolované události **úložiště objektů blob** protokoly Další informace, jako je instance ID, ID vlákna a časové razítko podrobnější (formát značek) než protokolování **systém souborů**.

> [!NOTE]
> Informace uložené v **úložiště objektů blob** lze přistupovat pouze pomocí klienta úložiště nebo aplikaci, která může pracovat přímo s těmito systémy úložišť. Například sadu Visual Studio 2013 obsahuje Průzkumníka služby Storage, který slouží k prozkoumání úložiště objektů blob a HDInsight můžete přístup k datům uloženým ve službě blob storage. Můžete také psát aplikace, která přistupuje k Azure Storage pomocí jednoho z [sady Azure SDK](https://azure.microsoft.com/downloads/).
>

## <a name="download"></a> Jak: Stáhnout protokoly
Diagnostické informace ukládají do systému souborů aplikace lze přistupovat přímo pomocí FTP. Taky ho můžete stáhnout jako archiv Zip pomocí Azure CLI.

Struktura adresářů, které protokoly se ukládají v vypadá takto:

* **Protokoly aplikací** -/LogFiles/aplikace /. Tato složka obsahuje jeden nebo více textové soubory obsahující informace o vytvářené protokolování aplikací.
* **Trasování požadavku se nezdařilo** – / LogFiles/W3SVC ### /. Tato složka obsahuje soubor XSL a jeden nebo více souborů XML. Ujistěte se stáhnout soubor XSL do stejného adresáře jako soubory XML, protože soubor XSL poskytuje funkce pro formátování a filtrování obsahu souborů XML v aplikaci Internet Explorer.
* **Podrobné protokoly chyb** -/LogFiles/DetailedErrors /. Tato složka obsahuje jeden nebo více souborů HTM, které poskytují podrobné informace, které se vyskytly chyby protokolu HTTP.
* **Web Server Logs** - /LogFiles/http/RawLogs. Tato složka obsahuje jeden nebo více textových souborů ve formátu pomocí [rozšířený formát protokolu W3C souboru](https://msdn.microsoft.com/library/windows/desktop/aa814385.aspx).
* **Protokoly nasazení** – / LogFiles/Git. Tato složka obsahuje protokoly generované interní nasazení procesu používaného ve službě Azure App Service, jakož i protokoly pro nasazení Git. Můžete také najít protokoly nasazení v rámci D:\home\site\deployments.

### <a name="ftp"></a>FTP

Chcete-li otevřít připojení FTP k serveru FTP aplikace, najdete v článku [nasazení aplikace do Azure App Service pomocí FTP/S](deploy-ftp.md).

Po připojení k serveru FTP/S pro aplikace, otevřete **LogFiles** složku, ve kterém jsou uložené soubory protokolů.

### <a name="download-with-azure-cli"></a>Stáhněte si pomocí Azure CLI
Chcete-li stáhnout soubory protokolů pomocí rozhraní příkazového řádku Azure, otevřete nový příkazový řádek, prostředí PowerShell, Bash nebo relaci Terminálové služby a zadejte následující příkaz:

    az webapp log download --resource-group resourcegroupname --name appname

Tento příkaz uloží protokoly pro aplikaci s názvem "appname' do souboru s názvem **diagnostics.zip** v aktuálním adresáři.

> [!NOTE]
> Pokud jste ještě nenainstalovali Azure CLI nebo nebyly nakonfigurované na používání vašeho předplatného Azure, přečtěte si téma [tom, jak pomocí Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).
>
>

## <a name="how-to-view-logs-in-application-insights"></a>Postup: Zobrazit protokoly ve službě Application Insights
Visual Studio Application Insights poskytuje nástroje pro filtrování a prohledávání protokolů a pro korelaci protokolů s žádostmi a jinými událostmi.

1. Přidejte Application Insights SDK do projektu v sadě Visual Studio.
   * V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt a vyberte Přidat Application Insights. Rozhraní vás provede kroky, které patří vytvoření prostředku Application Insights. [Další informace](../azure-monitor/app/asp-net.md)
2. Do projektu přidejte balíček naslouchací služby stopy.
   * Klikněte pravým tlačítkem na projekt a zvolte spravovat balíčky NuGet. Vyberte `Microsoft.ApplicationInsights.TraceListener` [Další informace](../azure-monitor/app/asp-net-trace-logs.md)
3. Nahrajte svůj projekt a spusťte jej. tím vygenerujete data protokolu.
4. V [webu Azure portal](https://portal.azure.com/), přejděte na nový prostředek Application Insights a otevřete **hledání**. Měli byste vidět data protokolu, společně se žádosti, využití a další telemetrie. Některé telemetrických dat může trvat několik minut, než dorazí: klikněte na tlačítko Aktualizovat. [Další informace](../azure-monitor/app/diagnostic-search.md)

[Další informace o výkonu sledování pomocí Application Insights](../azure-monitor/app/azure-web-apps.md)

## <a name="streamlogs"></a> Jak: Protokoly datových proudů
Při vývoji aplikace, je často užitečné prohlédnout si informace o protokolování v téměř reálném čase. Informace o protokolování můžete Streamovat do svého vývojového prostředí pomocí Azure CLI.

> [!NOTE]
> Některé typy vyrovnávací paměti pro protokolování zapisovat do souboru protokolu, což může vést k události mimo pořadí v datovém proudu. Například může se zobrazit položky protokolu aplikace, která nastane, pokud uživatel navštíví stránku ve službě stream před odpovídající položka protokolu HTTP pro požadavek na stránku.
>
> [!NOTE]
> Streamování protokolů také datové proudy informací, zapsán do jakékoli textového souboru, který je uložen v **D:\\domácí\\LogFiles\\**  složky.
>
>

### <a name="streaming-with-azure-cli"></a>Streamování pomocí rozhraní příkazového řádku Azure
Informace o protokolování datový proud stream, otevřete nový příkazový řádek, prostředí PowerShell, Bash nebo relaci Terminálové služby a zadejte následující příkaz:

    az webapp log tail --name appname --resource-group myResourceGroup

Tento příkaz se připojí k aplikaci s názvem "appname" a začít Streamovat informace do okna v aplikaci dojde k události protokolu. Veškeré informace, které zapisují do souborů HTM, txt nebo .log, které jsou uložené v adresáři /LogFiles (d:/home/logfiles) se streamují do místní konzoly.

Chcete-li filtrovat konkrétní události, jako jsou chyby, použijte **– filtr** parametru. Příklad:

    az webapp log tail --name appname --resource-group myResourceGroup --filter Error

Chcete-li filtrovat konkrétní typy, jako je například HTTP, použijte **– cesta** parametru. Příklad:

    az webapp log tail --name appname --resource-group myResourceGroup --path http

> [!NOTE]
> Pokud jste ještě nenainstalovali Azure CLI nebo nebyly nakonfigurované na používání vašeho předplatného Azure, přečtěte si téma [tom, jak pomocí Azure CLI](../cli-install-nodejs.md).
>
>

## <a name="understandlogs"></a> Jak: Vysvětlení diagnostické protokoly
### <a name="application-diagnostics-logs"></a>Protokoly diagnostiky aplikací
Konzole Application diagnostics ukládá informace v určitém formátu pro aplikace .NET, v závislosti na tom, jestli ukládání protokolů do služby file storage systému nebo objekt blob. 

Základní sadu – uložená data jsou stejné oba typy úložiště – datum a čas, ke které došlo k události, ID procesu, který vytvořil události, typu události (informace, upozornění a chyby) a zpráva o události. Pomocí systému souborů pro ukládání protokolů je užitečné, když potřebujete okamžitý přístup k řešení problémů, protože soubory protokolu jsou aktualizovány téměř okamžitě. Úložiště objektů blob je použít pro archivační účely, protože soubory ukládají do mezipaměti a pak se vyprázdní na kontejner úložiště podle plánu.

**Systém souborů**

Každý řádek přihlášení k systému souborů nebo přijaty pomocí vysílání datového proudu je v následujícím formátu:

    {Date}  PID[{process ID}] {event type/level} {message}

Například událost chyby vypadat podobně jako v následujícím příkladu:

    2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

Protokolování do systému souborů poskytuje základní informace o tři dostupné metody pouze čas, ID procesu, úroveň události a zprávy.

**Blob Storage**

Při přihlašování do úložiště objektů blob, data se ukládají ve formátu hodnot oddělených čárkami (CSV). Další pole jsou protokolovány k poskytnutí podrobnější informace o události. Pro každý řádek ve sdíleném svazku clusteru se používají následující vlastnosti:

| Název vlastnosti | / Ve formátu |
| --- | --- |
| Datum |Datum a čas, kdy došlo k události |
| Úroveň |Úroveň události (například Chyba, upozornění, informace) |
| ApplicationName |Název aplikace |
| ID instance |Instance aplikace, která na došlo k události |
| EventTickCount |Datum a čas, kdy došlo k události, formát značky (větší přesnost) |
| ID události |ID události této události<p><p>Výchozí hodnota je 0, pokud zadaný žádný |
| Identifikátor PID |ID procesu |
| TID. |ID vlákna vlákna, která vytváří události |
| Zpráva |Podrobná zpráva o události |

Data uložená v objektu blob, by vypadalo podobně jako v následujícím příkladu:

    date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
    2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [!NOTE]
> Pro ASP.NET Core, protokolování se provádí pomocí [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) poskytovatele tohoto zprostředkovatele protokolu další vkladů soubory do kontejneru objektů blob. Další informace najdete v tématu [ASP.NET Core protokolování v Azure](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#logging-in-azure).
>
>

### <a name="failed-request-traces"></a>Trasování požadavku se nezdařilo
Trasování chybných požadavků jsou uloženy v souborech XML s názvem **fr ### .xml**. Aby bylo snazší zobrazit zaznamenané informace, s názvem šablony stylů XSL **freb.xsl** je k dispozici ve stejném adresáři jako soubor XML. Pokud jeden ze souborů XML otevřít v aplikaci Internet Explorer, aplikace Internet Explorer šablonu stylů XSL používá k poskytování formátovaný zobrazení informací trasování, podobně jako v následujícím příkladu:

![Neúspěšná žádost zobrazit v prohlížeči](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

### <a name="detailed-error-logs"></a>Podrobnější protokoly chyb
Podrobnější protokoly chyb jsou dokumentů HTML, které poskytují podrobnější informace o chyby protokolu HTTP, ke kterým došlo. Protože jsou jednoduše dokumentů HTML, bylo možné zobrazit pomocí webového prohlížeče.

### <a name="web-server-logs"></a>Protokoly webového serveru
Protokoly webového serveru jsou formátovány pomocí [rozšířený formát protokolu W3C souboru](https://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Tyto informace lze číst pomocí textového editoru nebo analyzovat pomocí nástrojů, jako [analyzátoru protokolů](https://go.microsoft.com/fwlink/?LinkId=246619).

> [!NOTE]
> Protokoly vytvořené službou Azure App Service, nepodporují **s-computername**, **s-ip**, nebo **cs-version** pole.
>
>

## <a name="nextsteps"></a> Další kroky
* [Monitorování služby Azure App Service](web-sites-monitor.md)
* [Řešení potíží s Azure App Service v sadě Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [Analýza protokolů aplikace v HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
