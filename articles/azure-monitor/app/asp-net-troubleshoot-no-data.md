---
title: Řešení potíží s chybějícími daty v nástroji Application Insights pro .NET
description: Nezobrazila se data ve službě Azure Application Insights? Zkuste to tady.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: e231569f-1b38-48f8-a744-6329f41d91d3
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/23/2018
ms.author: mbullwin
ms.openlocfilehash: 2387af0ddd9f6f3ecbe91afaaca66d89f070c036
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54076036"
---
# <a name="troubleshooting-no-data---application-insights-for-net"></a>Řešení potíží s chybějícími daty v nástroji Application Insights pro .NET
## <a name="some-of-my-telemetry-is-missing"></a>Chybí některé telemetrie
*Ve službě Application Insights zobrazuje jenom zlomek události, které se generují Moje aplikace.*

* Pokud se vám zobrazují konzistentně stejný zlomek, je pravděpodobně z důvodu adaptivní [vzorkování](../../azure-monitor/app/sampling.md). Pokud to pokud chcete potvrdit, otevřete vyhledávání (v okně Přehled) a podívejte se na instanci žádost nebo jiná událost. V dolní části Vlastnosti klikněte na tlačítko "..." pro získání podrobností o celou vlastnost. Pokud žádost o Count > 1 a pak vzorkování je v provozu. 
* V opačném případě je možné, že narazíte [limitu přenosové rychlosti dat](../../azure-monitor/app/pricing.md#limits-summary) pro cenový tarif. Tato omezení se vztahují na minutu.

## <a name="no-data-from-my-server"></a>Žádná data ze svého serveru
*Mám nainstalovanou aplikaci na webovém serveru a nyní nevidím žádnou telemetrii z něj. OK pracoval na mém počítači vývoje.*

* Pravděpodobně potíže s bránou firewall. [Nastavit výjimky brány firewall pro službu Application Insights k odesílání dat](../../azure-monitor/app/ip-addresses.md).
* Server služby IIS může chybět některé požadované součásti: rozšiřitelnost rozhraní .NET 4.5 a ASP.NET 4.5.

*Můžu [nainstalované monitorování stavu](../../azure-monitor/app/monitor-performance-live-website-now.md) na webovém serveru k monitorování stávajících aplikací. Se mi nezobrazují žádné výsledky.*

* Zobrazit [řešení potíží s monitorování stavu](../../azure-monitor/app/monitor-performance-live-website-now.md#troubleshooting-runtime-configuration-of-application-insights). 

## <a name="q01"></a>Žádná možnost Přidat Application Insights v sadě Visual Studio
*Když pravým tlačítkem myši existující projekt v Průzkumníku řešení, se mi nezobrazují žádné možnosti Application Insights.*

* Nástroje podporují všechny typy projektu .NET. Web a WCF projekty jsou podporovány. Pro ostatní typy projektů jako jsou třeba aplikace klasické pracovní plochy nebo služby, můžete ji [ručně přidejte sadu Application Insights SDK do projektu](../../azure-monitor/app/windows-desktop.md).
* Ujistěte se, že máte [Visual Studio 2013 Update 3 nebo novější](https://docs.microsoft.com/visualstudio/releasenotes/vs2013-update3-rtm-vs). Obsahuje předem nainstalovaných pomocí nástroje Developer Analytics, které poskytují Application Insights SDK.
* Vyberte **nástroje**, **rozšíření a aktualizace** a zkontrolujte, že **Developer Analytics Tools** je nainstalován a povolen. Pokud ano, klikněte na tlačítko **aktualizace** zobrazíte, pokud je k dispozici aktualizace.
* Otevřete dialogové okno Nový projekt a zvolte webovou aplikaci ASP.NET. Pokud se zobrazí možnost Application Insights, pak tyto nástroje jsou nainstalovány. Pokud tomu tak není, zkuste odinstalovat a opětovné instalaci nástroje Application Insights.

## <a name="q02"></a>Nepovedlo se přidat Application Insights
*Při přidání Application Insights do existujícího projektu zobrazí chybová zpráva.*

Pravděpodobné příčiny:

* Komunikace s portálem Application Insights se nezdařil. nebo
* Je něco v nepořádku s vaším účtem Azure;
* Stačí [přístup pro čtení pro předplatné nebo skupinu, ve kterém snažil vytvořit nový prostředek](../../azure-monitor/app/resources-roles-access-control.md).

Oprava:

* Zkontrolujte, že jste zadali přihlašovací údaje pro účet přímo Azure. 
* V prohlížeči, zkontrolujte, že máte přístup k [webu Azure portal](https://portal.azure.com). Otevřete nastavení a zjistit, jestli žádná omezení.
* [Přidejte Application Insights do existujícího projektu](../../azure-monitor/app/asp-net.md): V Průzkumníku řešení klikněte pravým tlačítkem na projekt a zvolte "Přidat Application Insights."
* Pokud to pořád nefunguje, postupujte [Ruční postup](../../azure-monitor/app/windows-services.md) přidat prostředek na portálu a potom přidejte sadu SDK do projektu. 

## <a name="emptykey"></a>Dojde k chybě "Instrumentační klíč nemůže být prázdný"
Vypadá to, došlo k chybě při jste instalovali Application Insights nebo možná adaptér protokolování.

V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt a zvolte **Application Insights > Konfigurovat Application Insights**. Zobrazí se dialogové okno, které vás zve k přihlášení k Azure a vytvořte prostředek Application Insights, nebo znovu použijte existující.

## <a name="NuGetBuild"></a> "Balíčky NuGet, které nebyly nalezeny" na serveru sestavení
*Všechno sestavuje OK, je-li můžu mě na mém počítači vývoje ladění, ale získat chybu NuGet na server sestavení.*

Podrobnosti najdete na [obnovení balíčků NuGet](https://docs.nuget.org/Consume/Package-Restore) a [automatické obnovení balíčku](https://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore).

## <a name="missing-menu-command-to-open-application-insights-from-visual-studio"></a>Chybí příkaz nabídky a otevře Application Insights v sadě Visual Studio
*Když mám klikněte pravým tlačítkem na projekt Průzkumníka řešení, se mi nezobrazují všechny příkazy Application Insights nebo nevidím v příkazu Otevřít Application Insights.*

Pravděpodobné příčiny:

* Pokud jste ručně vytvořili prostředek Application Insights, nebo pokud se jedná o typ, který nepodporuje nástroje Application Insights.
* Developer Analytics tools jsou zakázány ve Visual Studiu. 
* Visual Studio je starší než 2013 Update 3.

Oprava:

* Ujistěte se, že je vaše verze sady Visual Studio 2013 update 3 nebo novější.
* Vyberte **nástroje**, **rozšíření a aktualizace** a zkontrolujte, že **nástroje Developer Analytics tools** je nainstalován a povolen. Pokud ano, klikněte na tlačítko **aktualizace** zobrazíte, pokud je k dispozici aktualizace.
* Klikněte pravým tlačítkem na projekt v Průzkumníku řešení. Pokud se zobrazí příkaz **Application Insights > Konfigurovat Application Insights**, používat ho pro připojování vašeho projektu na prostředek ve službě Application Insights.

V opačném případě typ projektu nepodporuje přímo nástrojů služby Application Insights. Pokud chcete zobrazit telemetrii, přihlaste se k [webu Azure portal](https://portal.azure.com), zvolte Application Insights na levém navigačním panelu a vyberte svou aplikaci.

## <a name="access-denied-on-opening-application-insights-from-visual-studio"></a>"Přístup zakázán" při otevření služby Application Insights v sadě Visual Studio
*Příkaz nabídky "Otevřít Application Insights" budu přesměrován na webu Azure portal, ale se zobrazí chyba "přístup byl odepřen".*

Microsoft přihlášení, která jste naposledy použili na váš výchozí prohlížeč nemá přístup k [prostředek, který byl vytvořen při přidání Application Insights k této aplikaci](../../azure-monitor/app/asp-net.md). Existují dvě pravděpodobné příčiny: 

* Můžete mít více než jeden účet Microsoft – možná pracovní a osobní účet Microsoft? Přihlášení, které jste naposledy použili ve webovém prohlížeči výchozí bylo pro jiný účet než ten, který má přístup k [přidat službu Application Insights do projektu](../../azure-monitor/app/asp-net.md). 
  
  * Oprava: Klikněte na název v horní pravé části okna prohlížeče a odhlásit se. Pak se přihlaste pomocí účtu, který má přístup. Potom v levém navigačním panelu klikněte na tlačítko Application Insights a vyberte svou aplikaci.
* Někdo jiný přidat Application Insights do projektu a jejich zapomněli získáte [přístup ke skupině prostředků](../../azure-monitor/app/resources-roles-access-control.md) ve které byla vytvořena. 
  
  * Oprava: Pokud používají účet organizace, že je přidejte do týmu; nebo se vám může udělit individuálního přístupu ke skupině prostředků.

## <a name="asset-not-found-on-opening-application-insights-from-visual-studio"></a>"Prostředek nebyl nalezen" při otevření služby Application Insights v sadě Visual Studio
*Příkaz nabídky "Otevřít Application Insights" budu přesměrován na webu Azure portal, ale se zobrazí chyba "prostředek nebyl nalezen".*

Pravděpodobné příčiny:

* Prostředek Application Insights pro vaši aplikaci odstraněn. nebo
* Klíč instrumentace se nastavil nebo změnil v souboru ApplicationInsights.config tak, že upravíte přímo, bez aktualizace souboru projektu. 

Instrumentační klíč v souboru ApplicationInsights.config ovládacích prvcích odešle telemetrická data. Řádek v souboru projektu určuje, který prostředek se otevře, když použijete příkaz v sadě Visual Studio. 

Oprava:

* V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt a vyberte Application Insights, konfigurovat Application Insights. V dialogovém okně buď můžete odesílat telemetrická data pro existující prostředek, nebo vytvořte novou. nebo:
* Otevřete prostředek přímo. Přihlaste se k [na webu Azure portal](https://portal.azure.com), v levém navigačním panelu klikněte na tlačítko Application Insights a pak vyberte svou aplikaci.

## <a name="where-do-i-find-my-telemetry"></a>Kde najdu telemetrie?
*Jsem se přihlásil [portálu Microsoft Azure](https://portal.azure.com), a dívám se na řídicím panelu Azure home. Takže kde můžu najít data Application Insights?*

* V levém navigačním panelu klikněte na tlačítko Application Insights a pak název vaší aplikace. Pokud nemáte žádné projekty existuje, budete muset [přidat nebo konfigurovat Application Insights v projektu webové](../../azure-monitor/app/asp-net.md).
  
    Tam uvidíte některé souhrnné grafy. Kliknutím zjistíte další podrobnosti.
* V sadě Visual Studio během ladění aplikace, klikněte na tlačítko Application Insights.

## <a name="q03"></a> Žádná data serveru (nebo vůbec žádná data)
*Můžu spustili svoji aplikaci a pak otevřít službu Application Insights v Microsoft Azure, ale všechny grafy zobrazují "Přečtěte si, jak shromažďovat..." nebo "Není nakonfigurováno."* Nebo, *pouze zobrazení stránky a uživatelská data, ale žádná data serveru.*

* Spusťte aplikaci v režimu ladění v sadě Visual Studio (F5). Pomocí aplikace tak, aby vygenerování nějaké telemetrie. Zkontrolujte, zda se zobrazí události zaznamenané v okně výstupu sady Visual Studio. 
  
    ![](./media/asp-net-troubleshoot-no-data/output-window.png)
* Na portálu Application Insights, otevřete [diagnostické vyhledávání](../../azure-monitor/app/diagnostic-search.md). Obvykle se zobrazí data zde poprvé.
* Klikněte na tlačítko Aktualizovat. V okně se aktualizuje sám pravidelně, ale je možné také provést ručně. Interval aktualizace je delší dobu větší časových rozsahů.
* Zkontrolujte, že Instrumentační klíče shodují. V hlavním okně pro vaši aplikaci v portálu služby Application Insights v **Essentials** rozevíracího seznamu, podívejte se na **Instrumentační klíč**. Pak otevřete soubor ApplicationInsights.config v projektu v sadě Visual Studio a vyhledejte `<instrumentationkey>`. Zkontrolujte, že dva klíče jsou stejné. Pokud není:
  
  * Na portálu klikněte na tlačítko Application Insights a vyhledejte prostředek aplikace s klávesa Šipka vpravo; nebo
  * V Průzkumníku řešení Visual Studio klikněte pravým tlačítkem na projekt a vyberte Application Insights, konfigurovat. Resetujte aplikaci k odesílání telemetrie na správný prostředek.
  * Pokud nemůžete najít odpovídající klíče, zkontrolujte, že používáte stejné přihlašovací údaje v sadě Visual Studio jako na portál.
    
* V [domácí řídicím panelu Microsoft Azure](https://portal.azure.com), podívejte se na stav služby mapy. Pokud jsou některé upozornění údaje, počkejte, dokud se změnil na OK a pak zavřete a znovu otevřete okno aplikace Application Insights.
* Zkontrolujte také [náš blog o stavu](https://blogs.msdn.microsoft.com/servicemap-status/).
* Napsali jste kód [SDK na straně serveru](../../azure-monitor/app/api-custom-events-metrics.md) , který může změnit instrumentačního klíče v `TelemetryClient` instancí nebo v `TelemetryContext`? Nebo napsali jste [konfigurace filtru nebo vzorkování](../../azure-monitor/app/api-filtering-sampling.md) , který může být filtrování si příliš mnoho?
* Pokud jste upravili soubor ApplicationInsights.config, pečlivě zkontrolujte konfiguraci [TelemetryInitializers a TelemetryProcessors](../../azure-monitor/app/api-filtering-sampling.md). Nesprávně pojmenované typ nebo parametr může způsobit sadu SDK k odesílání žádná data.

## <a name="q04"></a>Žádná data o využití zobrazení stránky, prohlížeče,
*V okamžiku načtení zobrazení stránky, nebo v oknech prohlížeč nebo využití zobrazuje data v grafech doba odezvy serveru a požadavky na Server, ale žádná data.*

Data pocházejí z skripty na webových stránkách. 

* Pokud jste přidali Application Insights do existujícího webového projektu, [budete muset ručně přidejte skripty](../../azure-monitor/app/javascript.md).
* Ujistěte se, že aplikace Internet Explorer není v režimu kompatibility zobrazení vašeho webu.
* Pomocí funkce ladění prohlížeči (F12 na některé prohlížeče zvolte sítě) můžete ověřit, že dat je odesíláno do `dc.services.visualstudio.com`.

## <a name="no-dependency-or-exception-data"></a>Žádná data závislost nebo výjimku
Zobrazit [telemetrických závislostí](../../azure-monitor/app/asp-net-dependencies.md) a [telemetrie výjimek](asp-net-exceptions.md).

## <a name="no-performance-data"></a>Žádná data o výkonu
Údaje o výkonu (procesor, frekvence v/v a tak dále) je k dispozici pro [webových služeb v Javě](../../azure-monitor/app/java-collectd.md), [aplikací klasické pracovní plochy Windows](../../azure-monitor/app/windows-desktop.md), [IIS webové aplikace a služby, pokud budete instalovat monitorování stavu](../../azure-monitor/app/monitor-performance-live-website-now.md), a [Azure Cloud Services](../../application-insights/app-insights-overview.md). najdete ji v části nastavení serverů.

## <a name="no-server-data-since-i-published-the-app-to-my-server"></a>Žádná data (server), protože jsem publikoval aplikaci ke svému serveru
* Ve skutečnosti zkopírování všech Microsoft zkontrolujte. ApplicationInsights knihovny DLL na serveru, spolu s Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll
* V bráně firewall, bude pravděpodobně nutné [otevřít některé porty TCP](../../azure-monitor/app/ip-addresses.md).
* Pokud budete muset používat proxy server k odeslání mimo vaší podnikové síti, nastavte [defaultProxy](https://msdn.microsoft.com/library/aa903360.aspx) v souboru Web.config
* Windows Server 2008: Ujistěte se, že máte nainstalované následující aktualizace: [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://support.microsoft.com/kb/2600217).

## <a name="i-used-to-see-data-but-it-has-stopped"></a>Můžu použít k zobrazení dat, ale byla zastavena
* Zkontrolujte, [stav blogu](https://blogs.msdn.com/b/applicationinsights-status/).
* Jste nedosáhli kvóta měsíčního počtu datových bodů? Otevřete nastavení/kvóta a ceny a zjistěte. Pokud ano, můžete upgradovat svůj plán nebo platit za dodatečnou kapacitu. Zobrazit [cenové schéma](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="i-dont-see-all-the-data-im-expecting"></a>Nevidím všechna data, který bych čekal
Pokud vaše aplikace odešle velké množství dat a používáte Application Insights SDK pro verze technologie ASP.NET 2.0.0-beta3 nebo novější, [adaptivního vzorkování](../../azure-monitor/app/sampling.md) funkce může pracovat a odesílat pouze procento vaší telemetrie. 

Lze je vypnout, ale to se nedoporučuje. Vzorkování je navržený tak, aby souvisejícími telemetrickými daty správně přenosu, k diagnostickým účelům. 

## <a name="client-ip-address-is-0000"></a>IP adresa klienta je 0.0.0.0

5. února 2018 jsme oznámili, že jsme odebrali protokolování IP adresu klienta. Geografické umístění to neovlivní.

> [!NOTE]
> Pokud je nutné nejprve 3 oktety IP adresu, můžete použít [inicializátor telemetrie](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#add-properties-itelemetryinitializer) přidat vlastní atribut.
> Toto nemá vliv na data shromážděná před 5. únorem 2018.


## <a name="wrong-geographical-data-in-user-telemetry"></a>Chybná zeměpisná data v telemetrii uživatele
Město, oblast a země dimenze jsou odvozeny z IP adresy a nejsou vždy přesné. Tyto IP adresy nejdřív zpracování pro umístění a potom změnit na hodnotu 0.0.0.0 mají být uloženy.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Výjimka „metoda nebyla nalezena“ při spuštění v Azure Cloud Services
Vytvořili jste sestavení pro .NET 4.6? Verze 4.6 není v rolích Azure Cloud Services podporována automaticky. Před spuštěním aplikace [nainstalujte pro každou roli verzi 4.6](../../cloud-services/cloud-services-dotnet-install-dotnet.md).

## <a name="still-not-working"></a>Pořád nefunguje...
* [Fórum pro Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

