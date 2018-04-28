---
title: Řešení potíží s chybějícími daty v nástroji Application Insights pro .NET
description: Není zobrazuje data ve službě Azure Application Insights? Zkuste sem.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: e231569f-1b38-48f8-a744-6329f41d91d3
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: 951a3217d795df6360cd3cfa2d47db08c11f978e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="troubleshooting-no-data---application-insights-for-net"></a>Řešení potíží s chybějícími daty v nástroji Application Insights pro .NET
## <a name="some-of-my-telemetry-is-missing"></a>Chybí některé Moje telemetrie
*Ve službě Application Insights zobrazena pouze zlomek události, které generují Moje aplikace.*

* Jestli jste stejný zlomek konzistentně, pravděpodobně je z důvodu adaptivní [vzorkování](app-insights-sampling.md). Potvrďte to otevřete vyhledávání (v okně Přehled) a podívejte se na instanci žádost nebo jiná událost. V dolní části Vlastnosti klikněte na tlačítko "..." pro získání podrobností o úplné vlastnost. Pokud požadavku počet > 1, pak vzorkování je v provozu. 
* Jinak, je možné, že jste nedosáhli [limit rychlosti dat](app-insights-pricing.md#limits-summary) pro cenový plán. Tato omezení platí za minutu.

## <a name="no-data-from-my-server"></a>Žádná data ze svého serveru
*Po instalaci aplikace na webovém serveru, a teď se nezobrazí žádné telemetrie z něj. To šlo OK na můj vývojářském počítači.*

* Pravděpodobně problém brány firewall. [Nastavit výjimky brány firewall pro službu Application Insights k odesílání dat](app-insights-ip-addresses.md).
* Server služby IIS mohou chybět některé předpoklady: rozšiřitelnost rozhraní .NET 4.5 a technologii ASP.NET 4.5.

*I [nainstalován monitorování stavu](app-insights-monitor-performance-live-website-now.md) na webovém serveru ke sledování existující aplikace. Se nezobrazí žádné výsledky.*

* V tématu [řešení potíží s monitorování stavu](app-insights-monitor-performance-live-website-now.md#troubleshooting-runtime-configuration-of-application-insights). 

## <a name="q01"></a>Žádná možnost Přidat Application Insights v sadě Visual Studio
*Při kliknutí pravým tlačítkem myši existující projekt v Průzkumníku řešení, se nezobrazí žádné možnosti Application Insights.*

* Nástroje pro jsou podporovány všechny typy rozhraní .NET projektu. Projekty webové a WCF jsou podporovány. Pro ostatní typy projektu například stolní nebo služby aplikací, můžete stále [Application Insights SDK do projektu přidejte ručně](app-insights-windows-desktop.md).
* Zajistěte, aby byla [Visual Studio 2013 Update 3 nebo novější](http://go.microsoft.com/fwlink/?LinkId=397827). Pochází předinstalované Developer Analytics tools, které poskytují Application Insights SDK.
* Vyberte **nástroje**, **rozšíření a aktualizace** a zkontrolujte, zda **Developer Analytics Tools** je nainstalovaný a povolený. Pokud ano, klikněte na tlačítko **aktualizace** Pokud je k dispozici aktualizace.
* Otevřete dialogové okno Nový projekt a zvolte webové aplikace ASP.NET. Pokud se zobrazí možnost Application Insights existuje, jsou nainstalované nástroje. Pokud ne, zkuste odinstalace a opětovné instalaci nástroje Application Insights.

## <a name="q02"></a>Nezdařilo se přidání Application Insights
*Při pokusu přidat službu Application Insights do existujícího projektu se zobrazuje chybovou zprávu.*

Pravděpodobné příčiny:

* Komunikace s portálem Application Insights se nezdařila; nebo
* Je nějaký problém s vaším účtem Azure;
* Můžete mít pouze [přístup pro čtení pro předplatné nebo skupinu, kde chcete vytvořit nový prostředek](app-insights-resources-roles-access-control.md).

Oprava:

* Zkontrolujte, zda jste zadali přihlašovací údaje pro účet právo Azure. 
* V prohlížeči, zkontrolujte, zda máte přístup k [portál Azure](https://portal.azure.com). Otevřít nastavení a zobrazit, pokud neexistuje žádné omezení.
* [Přidejte Application Insights do existujícího projektu](app-insights-asp-net.md): V Průzkumníku řešení klikněte pravým tlačítkem na projekt a zvolte "Přidat Application Insights."
* Pokud stále nefunguje správně, postupujte podle kroků [Ruční postup](app-insights-windows-services.md) přidat prostředek na portálu a poté přidejte sadu SDK do projektu. 

## <a name="emptykey"></a>Dojde k chybě "klíč instrumentace nemůže být prázdný"
Vypadá to, došlo k chybě při byly instalaci Application Insights nebo může být adaptér protokolování.

V Průzkumníku řešení klikněte pravým tlačítkem na projekt a zvolte **Application Insights > Konfigurovat Application Insights**. Zobrazí dialogové okno se žádostí, můžete se přihlásit k Azure a buď vytvořte prostředek Application Insights, nebo znovu použijte stávající.

## <a name="NuGetBuild"></a> "Balíčky NuGet chybí" na serveru Moje sestavení
*Všechno, co sestavení OK při I mě ladění na můj vývojovém počítači, ale zobrazí se chyba NuGet na buildovacím serveru.*

Najdete v tématu [obnovení balíčků NuGet](http://docs.nuget.org/Consume/Package-Restore) a [automatické obnovení balíčků](http://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore).

## <a name="missing-menu-command-to-open-application-insights-from-visual-studio"></a>Chybějící příkazu v nabídce otevřete Application Insights v sadě Visual Studio
*Při kliknutí pravým tlačítkem myši projektu Průzkumníku řešení, se nezobrazí žádné příkazy, Application Insights, nebo příkaz otevřete Application Insights se nezobrazí.*

Pravděpodobné příčiny:

* Pokud jste vytvořili prostředku Application Insights ručně nebo pokud se jedná o typ, který nepodporuje nástroje Application Insights.
* V aplikaci Visual Studio jsou zakázány Developer Analytics tools. 
* Visual Studio je starší než 2013 Update 3.

Oprava:

* Zajistěte, aby byl vaší verzí sady Visual Studio 2013 update 3 nebo novější.
* Vyberte **nástroje**, **rozšíření a aktualizace** a zkontrolujte, zda **Developer Analytics tools** je nainstalovaný a povolený. Pokud ano, klikněte na tlačítko **aktualizace** Pokud je k dispozici aktualizace.
* Klikněte pravým tlačítkem na projekt v Průzkumníku řešení. Pokud se zobrazí příkaz **Application Insights > Konfigurovat Application Insights**, používat pro připojení k prostředku ve službě Application Insights projektu.

Jinak typ vašeho projektu nepodporuje přímo nástroje Application Insights. Pokud chcete zobrazit telemetrii, přihlaste se k [portál Azure](https://portal.azure.com), vyberte Application Insights na levém navigačním panelu a vyberte svou aplikaci.

## <a name="access-denied-on-opening-application-insights-from-visual-studio"></a>Odepření přístupu"' otevírání Application Insights v sadě Visual Studio
*Příkaz nabídky, otevřete Application Insights, trvá mi na portál Azure, ale zobrazí se chyba odepření přístupu.*

![](./media/app-insights-asp-net-troubleshoot-no-data/access-denied.png)

Microsoft přihlášení poslední používaného na výchozí prohlížeč nemá přístup k [prostředek, který byl vytvořen při Application Insights byla přidána do této aplikace](app-insights-asp-net.md). Existují dvě pravděpodobné příčiny: 

* Máte více než jeden účet Microsoft - možná pracovní a osobní účet Microsoft? Přihlášení, který jste naposledy použili na výchozí prohlížeč bylo pro jiný účet než ten, který má přístup k [do projektu přidejte Application Insights](app-insights-asp-net.md). 
  
  * Oprava: Klikněte na název v top napravo od okna prohlížeče a odhlášení. Pak se přihlaste pomocí účtu, který má přístup. Potom v levém navigačním panelu klikněte na tlačítko Application Insights a vyberte svou aplikaci.
* Někdo jiný přidat Application Insights do projektu a je zapomněli tak, abyste získali [přístup ke skupině prostředků](app-insights-resources-roles-access-control.md) ve které byly vytvořeny. 
  
  * Oprava: Pokud použili účet organizace, že vás může přidat týmu; nebo se můžete udělit jednotlivý přístup ke skupině prostředků.

## <a name="asset-not-found-on-opening-application-insights-from-visual-studio"></a>"Prostředek nebyl nalezen" otevírání Application Insights v sadě Visual Studio
*Příkaz nabídky, otevřete Application Insights, trvá mi na portál Azure, ale zobrazí chyba "prostředek nebyl nalezen".*

Pravděpodobné příčiny:

* Prostředek služby Application Insights pro vaši aplikaci byl odstraněn; nebo
* Klíč instrumentace nastavit nebo změnit v souboru ApplicationInsights.config tak, že upravíte přímo, bez aktualizace souboru projektu. 

Klíč instrumentace v ovládacích prvcích souboru ApplicationInsights.config, kam je odeslána telemetrii. Řádek v souboru projektu řídí, který prostředek se otevře při použití příkazu v sadě Visual Studio. 

Oprava:

* V Průzkumníku řešení klikněte pravým tlačítkem na projekt a vyberte Application Insights, konfigurovat Application Insights. V dialogovém okně buď můžete odesílat telemetrická data do existující prostředek, nebo vytvořte novou. nebo:
* Otevřete prostředek přímo. Přihlaste se k [portálu Azure](https://portal.azure.com), klikněte na tlačítko Application Insights na levém navigačním panelu a pak vyberte svou aplikaci.

## <a name="where-do-i-find-my-telemetry"></a>Kde Moje telemetrie
*Přihlášení k [portálu Microsoft Azure](https://portal.azure.com), a dívám se na domovský řídicí panel Azure. Kde lze najít data Application Insights?*

* V levém navigačním panelu klikněte na tlačítko Application Insights a pak název vaší aplikace. Pokud nemáte žádné projekty existuje, budete muset [přidat nebo nakonfigurovat Application Insights ve webovém projektu](app-insights-asp-net.md).
  
    Zde se zobrazí některé souhrnné grafy. Klikněte na tlačítko prostřednictvím je chcete zobrazit podrobněji.
* V sadě Visual Studio při ladění aplikace, klikněte na tlačítko Application Insights.

## <a name="q03"></a> Žádná data serveru (nebo žádná data na všechny)
*I spustili mé aplikace a pak otevřít službu Application Insights v Microsoft Azure, ale všechny grafy zobrazují, zjistěte, jak shromažďovat...' nebo "Není nakonfigurovaná."* Nebo, *jenom zobrazení stránky a uživatelská data, ale žádná data serveru.*

* Spusťte svoji aplikaci v režimu ladění v sadě Visual Studio (F5). Pomocí aplikace tak, aby se vygenerování nějaké telemetrie. Zkontrolujte, zda se zobrazí události zaznamenané v okně výstupu sady Visual Studio. 
  
    ![](./media/app-insights-asp-net-troubleshoot-no-data/output-window.png)
* V portálu služby Application Insights otevřete [diagnostické vyhledávání](app-insights-diagnostic-search.md). Data obvykle se zde zobrazí první.
* Klikněte na tlačítko Aktualizovat. V okně aktualizuje sám pravidelně, ale můžete také provést ručně. Interval aktualizace je delší, pro větší časových rozsahů.
* Zkontrolujte, zda že instrumentace klíče shodují. V hlavním okně pro vaši aplikaci na portálu služby Application Insights v **Essentials** rozevíracího seznamu, podívejte se na **klíč instrumentace**. Poté ve vašem projektu v sadě Visual Studio otevřete soubor ApplicationInsights.config a najít `<instrumentationkey>`. Zkontrolujte, zda jsou si rovny dva klíče. Pokud není:
  
  * Na portálu klikněte na tlačítko Application Insights a vyhledejte prostředek aplikace pravé klíčem; nebo
  * V Průzkumníku řešení Visual Studio klikněte pravým tlačítkem na projekt a vyberte Application Insights, konfigurovat. Resetujte aplikaci odesílat telemetrii na správné zdroje.
  * Pokud nemůžete najít odpovídající klíče, zkontrolujte, že používáte stejné přihlašovací údaje v sadě Visual Studio jako v na portál.
    
    ![](./media/app-insights-asp-net-troubleshoot-no-data/ikey-check.png)
* V [domovský řídicí panel Microsoft Azure](https://portal.azure.com), podívejte se na stav služby mapy. Pokud jsou některé výstrahy indikace, počkejte, dokud se změnil na OK a pak zavřete a znovu otevřete okně vaší aplikace Application Insights.
* Zkontrolujte také [náš blog stav](http://blogs.msdn.com/b/applicationinsights-status/).
* Nebyla můžete psaní jakéhokoli kódu pro [server na straně SDK](app-insights-api-custom-events-metrics.md) které mohou změnit klíč instrumentace do `TelemetryClient` instancí nebo v `TelemetryContext`? Nebo se zapsat [filtru nebo vzorkování konfigurací](app-insights-api-filtering-sampling.md) může být filtrování se příliš mnoho?
* Pokud jste upravili souboru ApplicationInsights.config, pečlivě zkontrolujte konfiguraci [TelemetryInitializers a TelemetryProcessors](app-insights-api-filtering-sampling.md). Parametr nebo nesprávně názvem typ může způsobit SDK k odesílání žádná data.

## <a name="q04"></a>Žádná data k zobrazení stránky, prohlížečů, použití
*Data v grafech doba odezvy serveru a požadavků na serveru, ale žádná data zobrazen v čas načtení stránky zobrazení nebo v oknech prohlížeče nebo využití.*

Data pocházejí z skriptů ve webových stránkách. 

* Pokud jste přidali Application Insights do stávajícího webového projektu, [je třeba ručně přidat skripty](app-insights-javascript.md).
* Ujistěte se, že Internet Explorer není v režimu kompatibility zobrazení vaší lokality.
* Pomocí funkce ladění prohlížeče (F12 na některé prohlížeče, pak zvolte síť) Chcete-li ověřit, že data je odesílána `dc.services.visualstudio.com`.

## <a name="no-dependency-or-exception-data"></a>Žádná data závislostí nebo výjimky
V tématu [telemetrických závislostí](app-insights-asp-net-dependencies.md) a [telemetrie výjimek](app-insights-asp-net-exceptions.md).

## <a name="no-performance-data"></a>Žádná data o výkonu
Údaje o výkonu (procesoru, vstupně-výstupní operace rychlost a tak dále) je k dispozici pro [Java webové služby](app-insights-java-collectd.md), [aplikacích klasické pracovní plochy Windows](app-insights-windows-desktop.md), [IIS webové aplikace a služby, pokud nainstalujete monitorování stavu](app-insights-monitor-performance-live-website-now.md), a [Cloudových služeb azure](app-insights-azure.md). najdete ho v části nastavení servery.

## <a name="no-server-data-since-i-published-the-app-to-my-server"></a>Žádná data (server), protože po publikování aplikace ke svému serveru
* Zkontrolujte, jestli jste ve skutečnosti zkopírovali všechny Microsoft. ApplicationInsights knihovny DLL na server, společně s Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll
* V bráně firewall, bude pravděpodobně nutné [otevřít některé porty TCP](app-insights-ip-addresses.md).
* Pokud budete muset použít proxy server k odeslání mimo vaší podnikové síti, nastavte [defaultProxy –](https://msdn.microsoft.com/library/aa903360.aspx) v souboru Web.config.
* Windows Server 2008: Ujistěte se, jsou nainstalovány následující aktualizace: [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://support.microsoft.com/kb/2600217).

## <a name="i-used-to-see-data-but-it-has-stopped"></a>Mohu použít chcete zobrazit data, ale byla zastavena
* Zkontrolujte [stav blog](http://blogs.msdn.com/b/applicationinsights-status/).
* Jste nedosáhli vaše měsíční kvóta datových bodů? Otevřete nastavení nebo kvóty a cena chcete zjistit. Pokud ano, můžete upgradovat plán nebo platit dodatečnou kapacitu. Najdete v článku [ceny schéma](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="i-dont-see-all-the-data-im-expecting"></a>Všechna data, která se byla očekávána se nezobrazí
Pokud vaše aplikace odešle velké množství dat a používáte Application Insights SDK pro verze technologie ASP.NET 2.0.0-beta3 nebo novější, [adaptivního vzorkování](app-insights-sampling.md) funkce může pracovat a odesílat pouze procento vaší telemetrie. 

Ji můžete vypnout, ale není to doporučeno. Vzorkování je navržené tak, aby související telemetrii správně přenosu, k diagnostickým účelům. 

## <a name="wrong-geographical-data-in-user-telemetry"></a>Chybná zeměpisné data v telemetrie uživatele
Město, oblast a země dimenze jsou odvozené z IP adres a není vždy přesné.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Výjimka „metoda nebyla nalezena“ při spuštění v Azure Cloud Services
Vytvořili jste sestavení pro .NET 4.6? Verze 4.6 není v rolích Azure Cloud Services podporována automaticky. Před spuštěním aplikace [nainstalujte pro každou roli verzi 4.6](../cloud-services/cloud-services-dotnet-install-dotnet.md).

## <a name="still-not-working"></a>Pořád nefunguje...
* [Fórum Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

