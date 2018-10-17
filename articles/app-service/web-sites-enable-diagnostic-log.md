---
title: Povolení protokolování diagnostiky pro webové aplikace ve službě Azure App Service
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
ms.openlocfilehash: 5cd56abd02c55dbf72c92ed070f9988fae2b6762
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49365250"
---
# <a name="enable-diagnostics-logging-for-web-apps-in-azure-app-service"></a>Povolení protokolování diagnostiky pro webové aplikace ve službě Azure App Service
## <a name="overview"></a>Přehled
Azure nabízí předdefinovanou diagnostiku, která vám pomůže s laděním [webová aplikace App Service](http://go.microsoft.com/fwlink/?LinkId=529714). V tomto článku se dozvíte, jak povolit diagnostické protokolování a přidat do svojí aplikace instrumentaci, jakož i jak získat přístup k informacím protokolovaným v Azure.

Tento článek používá [webu Azure portal](https://portal.azure.com), prostředí Azure PowerShell a rozhraní příkazového řádku Azure (Azure CLI) pro práci s diagnostické protokoly. Informace o práci se diagnostické protokoly pomocí sady Visual Studio najdete v tématu [řešení potíží s Azure v sadě Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="whatisdiag"></a>Diagnostika webového serveru a application diagnostics
Aplikace služby App Service web apps poskytují diagnostické funkce pro protokolování informací z webového serveru a webové aplikace. Tyto jsou logicky rozdělena do **webového serveru diagnostiky** a **konzole application diagnostics**.

### <a name="web-server-diagnostics"></a>Diagnostika webového serveru
Můžete povolit nebo zakázat následující typy protokolů:

* **Podrobné protokolování chyb** – podrobné informace o chybě pro stavové kódy HTTP, které indikují chybu (stavový kód 400 nebo vyšší). Může obsahovat informace, které vám pomůže určit, proč server vrátil kód chyby.
* **Se nezdařilo, trasování požadavku** – podrobné informace o neúspěšných požadavcích, včetně trasování součásti služby IIS používá ke zpracování požadavku a doba trvání v jednotlivých komponentách. To je užitečné, pokud se pokoušíte zvýšit výkon webu a izolovat co způsobuje konkrétní chyba protokolu HTTP, který se má vrátit.
* **Web, protokolování na Server** – informace o transakce HTTP pomocí [rozšířený formát protokolu W3C souboru](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Je užitečné při určování celkové lokality metriky, jako je počet požadavků zpracovaných nebo kolik žádostí se z konkrétní IP adresu.

### <a name="application-diagnostics"></a>Diagnostika aplikace
Konzole Application diagnostics můžete zachytit informace vytvořené webové aplikace. Můžete použít aplikace ASP.NET [System.Diagnostics.Trace](http://msdn.microsoft.com/library/36hhw2t6.aspx) třídy k protokolování informací do protokolu diagnostiky aplikace. Příklad:

    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

Za běhu můžete načíst tyto protokoly, které pomůžou při řešení potíží. Další informace najdete v tématu [řešení potíží s Azure web apps v sadě Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

Aplikace služby App Service web apps zaznamenávat informace o nasazení při publikování obsahu do webové aplikace. Probíhá automaticky a nejsou žádná nastavení konfigurace pro nasazení protokolování. Nasazení protokolování umožňuje určit, proč se nepovedlo nasazení. Například pokud používáte skript vlastního nasazení, můžete použít nasazení protokolování k určení, proč se skript selhává.

## <a name="enablediag"></a>Postup povolení diagnostiky
Povolení diagnostiky v [webu Azure portal](https://portal.azure.com), přejděte na stránku pro vaši webovou aplikaci a klikněte na tlačítko **Nastavení > diagnostické protokoly**.

<!-- todo:cleanup dogfood addresses in screenshot -->
![Část protokoly](./media/web-sites-enable-diagnostic-log/logspart.png)

Když povolíte **konzole application diagnostics**, také zvolte **úroveň**. Toto nastavení vám umožní filtrovat informace zachycen **informační**, **upozornění**, nebo **chyba** informace. Nastavení na **podrobné** zaznamená všechny informace o vytvářené aplikace.

> [!NOTE]
> Na rozdíl od změny v souboru web.config, neprobíhá povolení rozhraní Application diagnostics nebo změna úrovně diagnostický protokol, na kterém aplikace běží v rámci domény aplikace.
>
>

Pro **protokolování aplikací**, můžete zapnout možnost souboru systému dočasně pro účely ladění. Tato volba vypne automaticky za 12 hodin. Můžete také zapnout možnost úložiště objektů blob kontejner objektů blob, do kterého se zapisují protokoly.

Pro **protokolování webového serveru**, můžete vybrat **úložiště** nebo **systém souborů**. Výběr **úložiště** vám umožní vybrat účet úložiště a kontejner objektů blob, který protokolů zapisován. 

Pokud uchováváte v systému souborů protokolů, soubory můžete získat přístup pomocí protokolu FTP nebo pomocí Azure Powershellu nebo rozhraní příkazového řádku Azure (Azure CLI) stáhnout jako archiv Zip.

Ve výchozím nastavení, nejsou automaticky odstraněny protokoly (s výjimkou produktů **protokolování aplikace (systém souborů)**). Pokud chcete automaticky odstranit protokoly, nastavte **doba uchování (dny)** pole.

> [!NOTE]
> Pokud jste [znovu vygenerovat přístupové klíče účtu úložiště](../storage/common/storage-create-storage-account.md), musíte obnovit konfiguraci příslušných protokolování použití aktualizované klíčů. Použijte následující postup:
>
> 1. V **konfigurovat** kartu, nastavte funkci příslušných protokolování na **vypnout**. Uložte nastavení.
> 2. Povolení protokolování pro tento objekt blob účtu úložiště nebo tabulku znovu. Uložte nastavení.
>
>

Libovolnou kombinaci systému souborů, table storage a blob storage je možné povolit ve stejnou dobu a mají vlastní protokol úrovni konfigurace. Můžete například chtít protokolovat chyby a upozornění do úložiště objektů blob jako dlouhodobé řešení pro protokolování při povolování protokolování systému souborů s úroveň verbose.

Zatímco všech tří umístění úložiště poskytují stejné základní informace o protokolované události **tabulky úložiště** a **úložiště objektů blob** Protokolovat další informace, jako je například instance ID, ID vlákna a více Detailní časové razítko (formát značek) než protokolování **systém souborů**.

> [!NOTE]
> Informace uložené v **tabulky úložiště** nebo **úložiště objektů blob** lze přistupovat pouze pomocí klienta úložiště nebo aplikaci, která může pracovat přímo s těmito systémy úložišť. Například sadu Visual Studio 2013 obsahuje Průzkumníka služby Storage, který slouží k prozkoumání tabulkou nebo objektem blob storage a HDInsight můžete přístup k datům uloženým ve službě blob storage. Můžete také psát aplikace, která přistupuje k Azure Storage pomocí jednoho z [sady Azure SDK](https://azure.microsoft.com/downloads/).
>
> [!NOTE]
> Diagnostika se dá povolit i z Azure Powershellu pomocí **Set-AzureWebsite** rutiny. Pokud jste ještě nenainstalovali Azure Powershellu nebo nebyly nakonfigurované na používání vašeho předplatného Azure, přečtěte si téma [nainstalovat a nakonfigurovat Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.6.0).
>
>

## <a name="download"></a> Postupy: stažení protokolů
Diagnostické informace ukládají do systému souborů webové aplikace lze přistupovat přímo pomocí FTP. Taky ho můžete stáhnout jako archiv Zip pomocí Azure Powershellu nebo rozhraní příkazového řádku Azure.

Struktura adresářů, které protokoly se ukládají v vypadá takto:

* **Protokoly aplikací** -/LogFiles/aplikace /. Tato složka obsahuje jeden nebo více textové soubory obsahující informace o vytvářené protokolování aplikací.
* **Trasování požadavku se nezdařilo** – / LogFiles/W3SVC ### /. Tato složka obsahuje soubor XSL a jeden nebo více souborů XML. Ujistěte se stáhnout soubor XSL do stejného adresáře jako soubory XML, protože soubor XSL poskytuje funkce pro formátování a filtrování obsahu souborů XML v aplikaci Internet Explorer.
* **Podrobné protokoly chyb** -/LogFiles/DetailedErrors /. Tato složka obsahuje jeden nebo více souborů HTM, které poskytují podrobné informace, které se vyskytly chyby protokolu HTTP.
* **Web Server Logs** - /LogFiles/http/RawLogs. Tato složka obsahuje jeden nebo více textových souborů ve formátu pomocí [rozšířený formát protokolu W3C souboru](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx).
* **Protokoly nasazení** – / LogFiles/Git. Tato složka obsahuje protokoly generované interní nasazení procesy používané modulem Azure web apps, jakož i protokoly pro nasazení Git. Můžete také najít protokoly nasazení v rámci D:\home\site\deployments.

### <a name="ftp"></a>FTP

Chcete-li otevřít připojení FTP k serveru FTP aplikace, najdete v článku [nasazení aplikace do Azure App Service pomocí FTP/S](app-service-deploy-ftp.md).

Po připojení k serveru FTP/S vaší webové aplikace, otevřete **LogFiles** složku, ve kterém jsou uložené soubory protokolů.

### <a name="download-with-azure-powershell"></a>Stáhněte si pomocí Azure Powershellu
Stáhněte si soubory protokolů, spustit novou instanci prostředí Azure PowerShell a pomocí následujícího příkazu:

    Save-AzureWebSiteLog -Name webappname

Tento příkaz uloží protokoly pro webovou aplikaci určené **– název** parametr do souboru s názvem **logs.zip** v aktuálním adresáři.

> [!NOTE]
> Pokud jste ještě nenainstalovali Azure Powershellu nebo nebyly nakonfigurované na používání vašeho předplatného Azure, přečtěte si téma [nainstalovat a nakonfigurovat Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.6.0).
>
>

### <a name="download-with-azure-command-line-interface"></a>Stáhněte si pomocí rozhraní příkazového řádku Azure
Chcete-li stáhnout soubory protokolů pomocí rozhraní příkazového řádku Azure, otevřete nový příkazový řádek, prostředí PowerShell, Bash nebo relaci Terminálové služby a zadejte následující příkaz:

    az webapp log download --resource-group resourcegroupname --name webappname

Tento příkaz uloží protokoly pro webovou aplikaci s názvem "webappname' do souboru s názvem **diagnostics.zip** v aktuálním adresáři.

> [!NOTE]
> Pokud jste ještě nenainstalovali rozhraní příkazového řádku Azure (Azure CLI) nebo nebyly nakonfigurované na používání vašeho předplatného Azure, přečtěte si téma [tom, jak pomocí Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).
>
>

## <a name="how-to-view-logs-in-application-insights"></a>Postupy: zobrazení protokolů ve službě Application Insights
Visual Studio Application Insights poskytuje nástroje pro filtrování a prohledávání protokolů a pro korelaci protokolů s žádostmi a jinými událostmi.

1. Přidejte Application Insights SDK do projektu v sadě Visual Studio.
   * V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt a vyberte Přidat Application Insights. Rozhraní vás provede kroky, které patří vytvoření prostředku Application Insights. [Další informace](../application-insights/app-insights-asp-net.md)
2. Do projektu přidejte balíček naslouchací služby stopy.
   * Klikněte pravým tlačítkem na projekt a zvolte spravovat balíčky NuGet. Vyberte `Microsoft.ApplicationInsights.TraceListener` [Další informace](../application-insights/app-insights-asp-net-trace-logs.md)
3. Nahrajte svůj projekt a spusťte jej. tím vygenerujete data protokolu.
4. V [webu Azure portal](https://portal.azure.com/), přejděte na nový prostředek Application Insights a otevřete **hledání**. Měli byste vidět data protokolu, společně se žádosti, využití a další telemetrie. Některé telemetrických dat může trvat několik minut, než dorazí: klikněte na tlačítko Aktualizovat. [Další informace](../application-insights/app-insights-diagnostic-search.md)

[Další informace o výkonu sledování pomocí Application Insights](../application-insights/app-insights-azure-web-apps.md)

## <a name="streamlogs"></a> Postupy: Stream protokolů
Při vývoji aplikace, je často užitečné prohlédnout si informace o protokolování v téměř reálném čase. Informace o protokolování můžete Streamovat do svého vývojového prostředí pomocí Azure Powershellu nebo rozhraní příkazového řádku Azure.

> [!NOTE]
> Některé typy vyrovnávací paměti pro protokolování zapisovat do souboru protokolu, což může vést k události mimo pořadí v datovém proudu. Například může se zobrazit položky protokolu aplikace, která nastane, pokud uživatel navštíví stránku ve službě stream před odpovídající položka protokolu HTTP pro požadavek na stránku.
>
> [!NOTE]
> Streamování protokolů také datové proudy informací, zapsán do jakékoli textového souboru, který je uložen v **D:\\domácí\\LogFiles\\**  složky.
>
>

### <a name="streaming-with-azure-powershell"></a>Streamování s využitím Azure Powershellu
Streamování informace o protokolování, spusťte novou instanci prostředí Azure PowerShell a pomocí následujícího příkazu:

    Get-AzureWebSiteLog -Name webappname -Tail

Tento příkaz se připojí k webové aplikaci určené **– název** parametr a začít Streamovat informace do okna prostředí PowerShell, jak ve webové aplikaci dojde k událostem protokolu. Veškeré informace, které zapisují do souborů HTM, txt nebo .log, které jsou uložené v adresáři /LogFiles (d:/home/logfiles) se streamují do místní konzoly.

Chcete-li filtrovat konkrétní události, jako jsou chyby, použijte **– zpráva** parametru. Příklad:

    Get-AzureWebSiteLog -Name webappname -Tail -Message Error

Chcete-li filtrovat konkrétní typy, jako je například HTTP, použijte **-cesta** parametru. Příklad:

    Get-AzureWebSiteLog -Name webappname -Tail -Path http

Pokud chcete zobrazit seznam dostupných cest, použijte parametr - ListPath.

> [!NOTE]
> Pokud jste ještě nenainstalovali Azure Powershellu nebo nebyly nakonfigurované na používání vašeho předplatného Azure, přečtěte si téma [tom, jak pomocí Azure Powershellu](http://azure.microsoft.com/develop/nodejs/how-to-guides/powershell-cmdlets/).
>
>

### <a name="streaming-with-azure-command-line-interface"></a>Streamování pomocí rozhraní příkazového řádku Azure
Informace o protokolování datový proud stream, otevřete nový příkazový řádek, prostředí PowerShell, Bash nebo relaci Terminálové služby a zadejte následující příkaz:

    az webapp log tail --name webappname --resource-group myResourceGroup

Tento příkaz se připojí k webové aplikaci s názvem "webappname" a začít Streamovat informace do okna ve webové aplikaci dojde k události protokolu. Veškeré informace, které zapisují do souborů HTM, txt nebo .log, které jsou uložené v adresáři /LogFiles (d:/home/logfiles) se streamují do místní konzoly.

Chcete-li filtrovat konkrétní události, jako jsou chyby, použijte **– filtr** parametru. Příklad:

    az webapp log tail --name webappname --resource-group myResourceGroup --filter Error

Chcete-li filtrovat konkrétní typy, jako je například HTTP, použijte **– cesta** parametru. Příklad:

    az webapp log tail --name webappname --resource-group myResourceGroup --path http

> [!NOTE]
> Pokud jste ještě nenainstalovali rozhraní příkazového řádku Azure, nebo nebyly nakonfigurované na používání vašeho předplatného Azure, přečtěte si téma [jak chcete používat rozhraní příkazového řádku Azure](../cli-install-nodejs.md).
>
>

## <a name="understandlogs"></a> Postupy: pochopení diagnostické protokoly
### <a name="application-diagnostics-logs"></a>Protokoly diagnostiky aplikací
Konzole Application diagnostics ukládá informace v určitém formátu pro aplikace .NET, v závislosti na tom, jestli ukládání protokolů do systému souborů, table storage a blob storage. Základní sadu uložených dat je stejná napříč všemi třemi typy úložiště – datum a čas, ke které došlo k události, ID procesu, který vytvořil události, typu události (informace, upozornění a chyby) a zpráva o události.

**Systém souborů**

Každý řádek přihlášení k systému souborů nebo přijaty pomocí vysílání datového proudu je v následujícím formátu:

    {Date}  PID[{process ID}] {event type/level} {message}

Například událost chyby vypadat podobně jako v následujícím příkladu:

    2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

Protokolování do systému souborů poskytuje základní informace o tři dostupné metody pouze čas, ID procesu, úroveň události a zprávy.

**Table Storage**

Při přihlašování do table storage, se používají další vlastnosti pro usnadnění vyhledávání data uložená v tabulce, stejně jako podrobnější informace o události. Pro každou entitu (řádků), uložené v tabulce se používají následující vlastnosti (sloupce).

| Název vlastnosti | / Ve formátu |
| --- | --- |
| PartitionKey |Datum a čas ve formátu yyyyMMddHH události |
| RowKey |Hodnota identifikátoru GUID, který jedinečně identifikuje tuto entitu |
| Časové razítko |Datum a čas, kdy došlo k události |
| EventTickCount |Datum a čas, kdy došlo k události, formát značky (větší přesnost) |
| ApplicationName |Název webové aplikace |
| Úroveň |Úroveň události (například Chyba, upozornění, informace) |
| ID události |ID události této události<p><p>Výchozí hodnota je 0, pokud zadaný žádný |
| ID instance |Instance webové aplikace, které i připadá na |
| Identifikátor PID |ID procesu |
| TID. |ID vlákna vlákna, která vytváří události |
| Zpráva |Podrobná zpráva o události |

**Blob Storage**

Při přihlašování do úložiště objektů blob, data se ukládají ve formátu hodnot oddělených čárkami (CSV). Table Storage, podobně jako další pole jsou protokolovány k poskytnutí podrobnější informace o události. Pro každý řádek ve sdíleném svazku clusteru se používají následující vlastnosti:

| Název vlastnosti | / Ve formátu |
| --- | --- |
| Datum |Datum a čas, kdy došlo k události |
| Úroveň |Úroveň události (například Chyba, upozornění, informace) |
| ApplicationName |Název webové aplikace |
| ID instance |Instance webové aplikace, na došlo k události |
| EventTickCount |Datum a čas, kdy došlo k události, formát značky (větší přesnost) |
| ID události |ID události této události<p><p>Výchozí hodnota je 0, pokud zadaný žádný |
| Identifikátor PID |ID procesu |
| TID. |ID vlákna vlákna, která vytváří události |
| Zpráva |Podrobná zpráva o události |

Data uložená v objektu blob, by vypadalo podobně jako v následujícím příkladu:

    date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
    2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [!NOTE]
> První řádek v protokolu obsahuje záhlaví sloupců, jak je v tomto příkladu.
>
>

### <a name="failed-request-traces"></a>Trasování požadavku se nezdařilo
Trasování chybných požadavků jsou uloženy v souborech XML s názvem **fr ### .xml**. Aby bylo snazší zobrazit zaznamenané informace, s názvem šablony stylů XSL **freb.xsl** je k dispozici ve stejném adresáři jako soubor XML. Pokud jeden ze souborů XML otevřít v aplikaci Internet Explorer, aplikace Internet Explorer šablonu stylů XSL používá k poskytování formátovaný zobrazení informací trasování, podobně jako v následujícím příkladu:

![Neúspěšná žádost zobrazit v prohlížeči](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

### <a name="detailed-error-logs"></a>Podrobnější protokoly chyb
Podrobnější protokoly chyb jsou dokumentů HTML, které poskytují podrobnější informace o chyby protokolu HTTP, ke kterým došlo. Protože jsou jednoduše dokumentů HTML, bylo možné zobrazit pomocí webového prohlížeče.

### <a name="web-server-logs"></a>Protokoly webového serveru
Protokoly webového serveru jsou formátovány pomocí [rozšířený formát protokolu W3C souboru](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Tyto informace lze číst pomocí textového editoru nebo analyzovat pomocí nástrojů, jako [analyzátoru protokolů](http://go.microsoft.com/fwlink/?LinkId=246619).

> [!NOTE]
> Nepodporuje protokoly vytvořené metodou webové aplikace Azure **s-computername**, **s-ip**, nebo **cs-version** pole.
>
>

## <a name="nextsteps"></a> Další kroky
* [Monitorování webových aplikací](web-sites-monitor.md)
* [Řešení potíží s aplikací Azure web apps v sadě Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md)
* [Analýza protokolů webové aplikace v HDInsight](http://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)

> [!NOTE]
> Pokud chcete začít používat službu Azure App Service před registrací k účtu Azure, přejděte k možnosti [Vyzkoušet službu App Service](https://azure.microsoft.com/try/app-service/), kde můžete okamžitě vytvořit krátkodobou úvodní webovou aplikaci. Není vyžadována platební karta a nevzniká žádný závazek.
>
>
