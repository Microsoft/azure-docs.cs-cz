---
title: Výkon aplikace nejčastější dotazy pro Azure web apps | Dokumentace Microsoftu
description: Získejte odpovědi na nejčastější dotazy týkající se dostupnosti, výkonu a problémů v aplikacích ve funkci Web Apps služby Azure App Service.
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 00563b93a3601ba6299c479c49375fd252841f79
ms.sourcegitcommit: 6678e16c4b273acd3eaf45af310de77090137fa1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50748115"
---
# <a name="application-performance-faqs-for-web-apps-in-azure"></a>Nejčastějších dotazech týkajících se výkonu aplikace pro Web Apps v Azure

Tento článek obsahuje odpovědi na nejčastější dotazy (FAQ) o problémy s výkonem aplikací pro [funkce Web Apps služby Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-is-my-app-slow"></a>Proč se Moje aplikace je pomalá?

Více faktorech může přispět ke zpomalení výkonu aplikace. Podrobné kroky pro řešení potíží naleznete v tématu [výkonu Poradce při potížích pomalých webových aplikací](app-service-web-troubleshoot-performance-degradation.md).

## <a name="how-do-i-troubleshoot-a-high-cpu-consumption-scenario"></a>Jak řešit scénáři vysoké využití procesoru

V některých scénářích vysoké využití procesoru může vaše aplikace skutečně vyžadují víc výpočetních prostředků. V takovém případě zvažte možnost škálování na vyšší úroveň služby, aplikace získá všechny prostředky, které potřebuje. Jindy vysoké využití procesoru může být způsobena, chybný smyčky nebo způsobem kódování. Získání přehled o tom, co způsobuje zvýšení využití procesoru je dvě části procesu. Nejprve vytvořit výpis procesu a následně analyzujte výpis stavu procesu. Další informace najdete v tématu [zachycovat a analyzovat soubor s výpisem paměti za vysoké využití procesoru pro Web Apps](https://blogs.msdn.microsoft.com/asiatech/2016/01/20/how-to-capture-dump-when-intermittent-high-cpu-happens-on-azure-web-app/).

## <a name="how-do-i-troubleshoot-a-high-memory-consumption-scenario"></a>Jak můžu řešit scénáři vysoká spotřeba paměti?

V některých scénářích vysoké využití paměti vaší aplikace skutečně může vyžadovat další výpočetní prostředky. V takovém případě zvažte možnost škálování na vyšší úroveň služby, aplikace získá všechny prostředky, které potřebuje. Jindy nevracení paměti může způsobit chybu v kódu. Způsobem kódování také může zvýšit využití paměti. Získat přehled o tom, co způsobuje vysoké paměti, že využití je dvě části procesu. Nejprve vytvořit výpis procesu a následně analyzujte výpis stavu procesu. Obě tyto kroky můžete provést efektivně služby Diagnostika chyb z Galerie rozšíření webů Azure. Další informace najdete v tématu [zachycovat a analyzovat soubor s výpisem paměti pro občasné velkého množství paměti pro službu Web Apps](https://blogs.msdn.microsoft.com/asiatech/2016/02/02/how-to-capture-and-analyze-dump-for-intermittent-high-memory-on-azure-web-app/).

## <a name="how-do-i-automate-app-service-web-apps-by-using-powershell"></a>Jak automatizovat webových aplikací služby App Service web apps pomocí Powershellu?

Rutiny prostředí PowerShell můžete použít ke správě a údržbě aplikací služby App Service web apps. V našem blogovém příspěvku [automatizace webových aplikací hostovaných ve službě Azure App Service pomocí prostředí PowerShell](https://blogs.msdn.microsoft.com/puneetgupta/2016/03/21/automating-webapps-hosted-in-azure-app-service-through-powershell-arm-way/), zjistíte, jak automatizovat běžné úlohy pomocí rutin Powershellu založené na Azure Resource Manageru. Blogový příspěvek obsahuje také vzorový kód pro různé úlohy správy webové aplikace. Popisy a syntaxe pro všechny rutiny webových aplikací služby App Service najdete v tématu [AzureRM.Websites](https://docs.microsoft.com/powershell/module/azurerm.websites/?view=azurermps-4.0.0).

## <a name="how-do-i-view-my-web-apps-event-logs"></a>Jak můžu zobrazit protokoly událostí svou webovou aplikaci?

Chcete-li zobrazit protokoly událostí vaší webové aplikace:

1. Přihlaste se k vaší [webu Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
2. V nabídce vyberte **ladění konzoly** > **CMD**.
3. Vyberte **LogFiles** složky.
4. Chcete-li zobrazit protokoly událostí, vyberte ikonu tužky vedle **eventlog.xml**.
5. Pro stahování protokolů, spusťte rutinu Powershellu `Save-AzureWebSiteLog -Name webappname`.

## <a name="how-do-i-capture-a-user-mode-memory-dump-of-my-web-app"></a>Jak můžu zachycení výpisu paměti uživatelského režimu svou webovou aplikaci?

Zachycení výpisu paměti uživatelského režimu vaší webové aplikace:

1. Přihlaste se k vaší [webu Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
2. Vyberte **Process Explorer** nabídky.
3. Klikněte pravým tlačítkem myši **w3wp.exe** proces nebo proces vaše webová úloha.
4. Vyberte **stáhněte výpis paměti** > **úplný výpis**.

## <a name="how-do-i-view-process-level-info-for-my-web-app"></a>Jak zobrazit informace na úrovni procesu pro svou webovou aplikaci?

Máte dvě možnosti pro zobrazení informací na úrovni procesu pro vaši webovou aplikaci:

*   Na webu Azure Portal:
    1. Otevřít **Process Explorer** pro webovou aplikaci.
    2. Pokud chcete zobrazit podrobnosti, vyberte **w3wp.exe** procesu.
*   V konzole Kudu:
    1. Přihlaste se k vaší [webu Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
    2. Vyberte **Process Explorer** nabídky.
    3. Pro **w3wp.exe** proces, vyberte **vlastnosti**.

## <a name="when-i-browse-to-my-app-i-see-error-403---this-web-app-is-stopped-how-do-i-resolve-this"></a>Při procházení do mojí aplikace, se zobrazuje "Chyba 403 – tato webová aplikace zastavená." Jak to můžu vyřešit?

Tuto chybu může způsobovat tří podmínek:

* Webové aplikace dosáhla fakturačního limitu a webu je zakázané.
* Webové aplikace se zastavil na portálu.
* Webové aplikace dosáhl maximální kvóty prostředků, které mohou vztahovat na využít bezplatné nebo sdílený plán škálování služeb.

Pokud chcete zobrazit, co je příčinou chyby a řešení tohoto problému postupujte podle kroků v [webové aplikace: "Chyba 403 – tato webová aplikace je zastavena"](https://blogs.msdn.microsoft.com/waws/2016/01/05/azure-web-apps-error-403-this-web-app-is-stopped/).

## <a name="where-can-i-learn-more-about-quotas-and-limits-for-various-app-service-plans"></a>Kde můžou dozvědět více o kvóty a omezení pro různé plány služby App Service?

Informace o kvóty a omezení, najdete v části [limity služby App Service](../azure-subscription-service-limits.md#app-service-limits). 

## <a name="how-do-i-decrease-the-response-time-for-the-first-request-after-idle-time"></a>Jak můžu snížení doby odezvy na první požadavky po dobu nečinnosti?

Ve výchozím nastavení webové aplikace jsou uvolněna, pokud jsou nastavte dobu nečinnosti. Tímto způsobem může systém ušetřit prostředky. Nevýhodou je, že odpověď na první žádost po webové aplikace je uvolněna delší dobu, aby ve webové aplikaci načtěte a spusťte současné obsluhování odpovědí. V plánech Basic a Standard můžete zapnout **Always On** nastavení udržovat aplikace vždy načteno. Tím se eliminují delší dobu načítání po nečinnosti aplikaci. Chcete-li změnit **Always On** nastavení:

1. Na webu Azure Portal přejděte do své webové aplikace.
2. Vyberte **nastavení aplikace**.
3. Pro **Always On**vyberte **na**.

## <a name="how-do-i-turn-on-failed-request-tracing"></a>Jak můžu zapnout trasování chybných požadavků?

Zapnout trasování chybných požadavků:

1. Na webu Azure Portal přejděte do své webové aplikace.
3. Vyberte **všechna nastavení** > **diagnostické protokoly**.
4. Pro **trasování neúspěšných žádostí**vyberte **na**.
5. Vyberte **Uložit**.
6. V okně webové aplikace vyberte **nástroje**.
7. Vyberte **Visual Studio Online**.
8. Pokud toto nastavení není **na**vyberte **na**.
9. Vyberte **Přejít**.
10. Vyberte **Web.config**.
11. V system.webServer přidejte tuto konfiguraci (pro zachytávání na konkrétní adresu URL):

    ```
    <system.webServer>
    <tracing> <traceFailedRequests>
    <remove path="*api*" />
    <add path="*api*">
    <traceAreas>
    <add provider="ASP" verbosity="Verbose" />
    <add provider="ASPNET" areas="Infrastructure,Module,Page,AppServices" verbosity="Verbose" />
    <add provider="ISAPI Extension" verbosity="Verbose" />
    <add provider="WWW Server" areas="Authentication,Security,Filter,StaticFile,CGI,Compression, Cache,RequestNotifications,Module,FastCGI" verbosity="Verbose" />
    </traceAreas>
    <failureDefinitions statusCodes="200-999" />
    </add> </traceFailedRequests>
    </tracing>
    ```
12. Řešení potíží s pomalým výkonem, přidejte tuto konfiguraci (pokud zachycující požadavek trvá déle než 30 sekund):
    ```
    <system.webServer>
    <tracing> <traceFailedRequests>
    <remove path="*" />
    <add path="*">
    <traceAreas> <add provider="ASP" verbosity="Verbose" />
    <add provider="ASPNET" areas="Infrastructure,Module,Page,AppServices" verbosity="Verbose" />
    <add provider="ISAPI Extension" verbosity="Verbose" />
    <add provider="WWW Server" areas="Authentication,Security,Filter,StaticFile,CGI,Compression, Cache,RequestNotifications,Module,FastCGI" verbosity="Verbose" />
    </traceAreas>
    <failureDefinitions timeTaken="00:00:30" statusCodes="200-999" />
    </add> </traceFailedRequests>
    </tracing>
    ```
13. Chcete-li stáhnout trasování chybných požadavků v [portál](https://portal.azure.com), přejděte na svůj web.
15. Vyberte **nástroje** > **Kudu** > **Přejít**.
18. V nabídce vyberte **ladění konzoly** > **CMD**.
19. Vyberte **LogFiles** složku a potom vyberte složku s názvem, který začíná **W3SVC**.
20. Pokud chcete zobrazit soubor XML, vyberte ikonu tužky.

## <a name="i-see-the-message-worker-process-requested-recycle-due-to-percent-memory-limit-how-do-i-address-this-issue"></a>Zobrazí zpráva "Pracovní proces vyžádal recyklace z důvodu omezení"Procenta paměti"." Jak tento problém vyřešit?

K dispozici maximální velikost paměti pro 32bitový proces (i na 64bitový operační systém) je 2 GB. Ve výchozím nastavení, pracovní proces je nastavena na 32-bit ve službě App Service (z důvodu kompatibility se starším webových aplikací).

Zvažte možnost 64bitové procesy, můžete využít ve vaší roli webového pracovního procesu k dispozici další paměť. Tím se aktivuje restartování webové aplikace, proto naplánujte odpovídajícím způsobem.

Všimněte si také, že 64bitové prostředí vyžaduje plán služby Basic nebo Standard. Zdarma a sdílené plány vždy spustit v 32bitovém prostředí.

Další informace najdete v tématu [konfigurují webové aplikace ve službě App Service](web-sites-configure.md).

## <a name="why-does-my-request-time-out-after-230-seconds"></a>Proč moje žádosti o časový limit po sekundách 230?

Nástroj Azure Load Balancer má výchozí nastavení časového limitu nečinnosti čtyř minut. Obvykle se jedná časový limit odezvy pro webovou žádost. Pokud vaše webová aplikace vyžaduje zpracování na pozadí, doporučujeme použít Azure WebJobs. Webové aplikace Azure může volat WebJobs a upozorněni, až po dokončení zpracování na pozadí. Můžete vybrat z víc metody pro používání WebJobs, včetně front a aktivačních událostí.

WebJobs je určená pro zpracování na pozadí. Můžete to co nejvíce zpracování na pozadí do webové úlohy. Další informace o Webjobech najdete v tématu [spouštění úloh na pozadí pomocí WebJobs](web-sites-create-web-jobs.md).

## <a name="aspnet-core-applications-that-are-hosted-in-app-service-sometimes-stop-responding-how-do-i-fix-this-issue"></a>Aplikace ASP.NET Core, které jsou hostované ve službě App Service, někdy přestane reagovat. Jak opravit tento problém?

Známý problém s předchozí [Kestrel verze](https://github.com/aspnet/KestrelHttpServer/issues/1182) může způsobit, že aplikace ASP.NET Core 1.0, která je hostována ve službě App Service je občas přestane reagovat. Také může se zobrazit tato zpráva: "Zadaná aplikace CGI došlo k chybě a server byl ukončen proces."

Tento problém je vyřešen v Kestrel verzi 1.0.2. Tato verze je zahrnuté v ASP.NET Core 1.0.3 aktualizace. Chcete-li vyřešit tento problém, ujistěte se, že aktualizace závislosti vaše aplikace používat Kestrel 1.0.2. Alternativně můžete použít jednu ze dvou řešení, které jsou popsány v blogovém příspěvku [problémy s ASP.NET Core 1.0 pomalého výkonu ve službě App Service web apps](https://blogs.msdn.microsoft.com/waws/2016/12/11/asp-net-core-slow-perf-issues-on-azure-websites).


## <a name="i-cant-find-my-log-files-in-the-file-structure-of-my-web-app-how-can-i-find-them"></a>Nemůžu najít soubory protokolu ve struktuře soubor svou webovou aplikaci. Jak je lze najít?

Pokud používáte místní mezipaměť funkce služby App Service, ovlivníte strukturu složek pro složky LogFiles a Data pro vaši instanci služby App Service. Při použití místní mezipaměti se vytvoří podsložky v úložišti LogFiles a složek s daty. Podsložky pomocí názvů vzor "Jedinečný identifikátor" + časové razítko. Každá podsložka odpovídá instance virtuálního počítače, ve kterém běží webové aplikace, nebo byla spuštěna.

Pokud chcete zjistit, zda používáte místní mezipaměti, zkontrolujte službu App Service **nastavení aplikace** kartu. Pokud se používá místní mezipaměti, nastavení aplikace `WEBSITE_LOCAL_CACHE_OPTION` je nastavena na `Always`.

Pokud nepoužíváte místní mezipaměti a dochází k tomuto problému, odešlete žádost o podporu.

## <a name="i-see-the-message-an-attempt-was-made-to-access-a-socket-in-a-way-forbidden-by-its-access-permissions-how-do-i-resolve-this"></a>Zobrazí zpráva "byl proveden pokus o přístup k soketu tak automatické připojení zakázáno její přístupová oprávnění." Jak to můžu vyřešit?

K této chybě obvykle dochází, pokud vyčerpáte odchozí připojení TCP pro instanci virtuálního počítače. Ve službě App Service limity se vynucují s ohledem maximální počet odchozích připojení, které mohou být provedeny pro všechny instance virtuálních počítačů. Další informace najdete v tématu [číselné limity pro různé virtuální počítač](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits).

Této chybě může také dojít, pokud se pokusíte o přístup k místní adresu z vaší aplikace. Další informace najdete v tématu [požadavky na místní adresu](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#local-address-requests).

Další informace o odchozích připojeních ve webové aplikaci, najdete v blogovém příspěvku o [odchozí připojení k Azure websites na úrovni](http://www.freekpaans.nl/2015/08/starving-outgoing-connections-on-windows-azure-web-sites/).

## <a name="how-do-i-use-visual-studio-to-remote-debug-my-app-service-web-app"></a>Použití sady Visual Studio pro vzdálené ladění Moje webová aplikace App Service?

Podrobný postup, který ukazuje, jak ladit vaši webovou aplikaci pomocí sady Visual Studio, naleznete v tématu [vzdáleného ladění webové aplikace služby App Service](https://blogs.msdn.microsoft.com/benjaminperkins/2016/09/22/remote-debug-your-azure-app-service-web-app/).
