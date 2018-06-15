---
title: Výkon aplikace nejčastější dotazy pro webové aplikace Azure | Microsoft Docs
description: Získejte odpovědi na nejčastější dotazy o dostupnosti, výkonu a k problémům s aplikací ve funkci Web Apps služby Azure App Service.
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
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 9c8eda1c4a65465a93b9c3f9caf23a6663073f26
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
ms.locfileid: "34159285"
---
# <a name="application-performance-faqs-for-web-apps-in-azure"></a>Výkon aplikace nejčastější dotazy pro webové aplikace v Azure

Tento článek obsahuje odpovědi na nejčastější dotazy (FAQ) o problémy s výkonem aplikace pro [funkce Web Apps služby Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-is-my-app-slow"></a>Proč je můj aplikace pomalé?

Několik faktorů může přispět k pomalý výkon aplikace. Podrobné kroky řešení potíží naleznete v tématu [Poradce při potížích pomalé webové aplikace výkonu](app-service-web-troubleshoot-performance-degradation.md).

## <a name="how-do-i-troubleshoot-a-high-cpu-consumption-scenario"></a>Jak odstranit scénáři vysoké spotřeby procesoru

V některých scénářích vysoké spotřeby procesoru skutečně vaše aplikace může vyžadovat další výpočetní prostředky. V takovém případě zvažte škálování pro vyšší úroveň služby, aplikace získá všechny prostředky, které potřebuje. Jinou dobu vysoké spotřeby procesoru může být způsobeno smyčku chybný nebo kódování postupem. Získávání aspekty co je aktivován vyšší spotřeby procesoru je proces dvě části. Nejprve vytvořit výpis procesu a poté analyzovat výpis procesu. Další informace najdete v tématu [zachycení a analyzovat soubor výpisu pro vysoké spotřeby procesoru pro webové aplikace](https://blogs.msdn.microsoft.com/asiatech/2016/01/20/how-to-capture-dump-when-intermittent-high-cpu-happens-on-azure-web-app/).

## <a name="how-do-i-troubleshoot-a-high-memory-consumption-scenario"></a>Jak odstranit scénář vysoké spotřeby paměti?

V některých scénářích vysoké spotřeby paměti skutečně vaše aplikace může vyžadovat další výpočetní prostředky. V takovém případě zvažte škálování pro vyšší úroveň služby, aplikace získá všechny prostředky, které potřebuje. Jinou dobu nevrácené paměti může způsobit chyby v kódu. Kódování postup také může zvýšit využití paměti. Získávání aspekty co je aktivován velkého množství paměti, že spotřeba je proces dvě části. Nejprve vytvořit výpis procesu a poté analyzovat výpis procesu. Služby Diagnostika havárií z Galerie rozšíření lokality Azure můžete efektivně provést oba tyto kroky. Další informace najdete v tématu [zachycení a analyzovat soubor výpisu pro přerušované vysokého využití paměti pro webové aplikace](https://blogs.msdn.microsoft.com/asiatech/2016/02/02/how-to-capture-and-analyze-dump-for-intermittent-high-memory-on-azure-web-app/).

## <a name="how-do-i-automate-app-service-web-apps-by-using-powershell"></a>Jak automatizovat webové aplikace služby App Service pomocí prostředí PowerShell?

Rutiny prostředí PowerShell můžete použít ke správě a údržbě webové aplikace aplikační služby. V našem blogu [automatizovat webové aplikace hostované v Azure App Service pomocí prostředí PowerShell](https://blogs.msdn.microsoft.com/puneetgupta/2016/03/21/automating-webapps-hosted-in-azure-app-service-through-powershell-arm-way/), jsme popisují, jak pomocí rutin prostředí PowerShell založené na správci prostředků Azure pro automatizaci běžných úkolů. V příspěvku blogu má také ukázkový kód pro různé úlohy správy webové aplikace. Popisy a syntaxe pro všechny rutiny webové aplikace služby App Service najdete v tématu [AzureRM.Websites](https://docs.microsoft.com/powershell/module/azurerm.websites/?view=azurermps-4.0.0).

## <a name="how-do-i-view-my-web-apps-event-logs"></a>Jak zobrazit protokoly událostí Moje webová aplikace?

Chcete-li zobrazit protokoly událostí vaší webové aplikace:

1. Přihlaste se k vaší [Kudu webu](https://*yourwebsitename*.scm.azurewebsites.net).
2. V nabídce vyberte **ladění konzoly** > **CMD**.
3. Vyberte **LogFiles** složky.
4. Chcete-li zobrazit protokoly událostí, vyberte ikonu tužky vedle **eventlog.xml**.
5. Chcete-li stahování protokolů, spusťte rutinu prostředí PowerShell `Save-AzureWebSiteLog -Name webappname`.

## <a name="how-do-i-capture-a-user-mode-memory-dump-of-my-web-app"></a>Jak zaznamenat výpisu paměti uživatelského režimu Moje webové aplikace?

Chcete-li zaznamenat výpisu paměti uživatelského režimu vaší webové aplikace:

1. Přihlaste se k vaší [Kudu webu](https://*yourwebsitename*.scm.azurewebsites.net).
2. Vyberte **Průzkumníka procesů** nabídky.
3. Klikněte pravým tlačítkem myši **w3wp.exe** procesu nebo procesu webové úlohy.
4. Vyberte **stáhnout výpis stavu paměti** > **úplný výpis**.

## <a name="how-do-i-view-process-level-info-for-my-web-app"></a>Jak zobrazit informace o úrovni procesu pro webová aplikace?

Máte dvě možnosti pro zobrazení informací na úrovni procesu pro webové aplikace:

*   Na webu Azure Portal:
    1. Otevřete **Průzkumníka procesů** pro webovou aplikaci.
    2. Chcete-li zobrazit podrobnosti, vyberte **w3wp.exe** procesu.
*   V konzole Kudu:
    1. Přihlaste se k vaší [Kudu webu](https://*yourwebsitename*.scm.azurewebsites.net).
    2. Vyberte **Průzkumníka procesů** nabídky.
    3. Pro **w3wp.exe** procesu, vyberte **vlastnosti**.

## <a name="when-i-browse-to-my-app-i-see-error-403---this-web-app-is-stopped-how-do-i-resolve-this"></a>Při procházení do mojí aplikace, I najdete v části "Chyba 403 - této webové aplikace je zastavena." Jak to lze vyřešit?

Tato chyba může způsobit tři podmínky:

* Webové aplikace byl dosažen limit fakturace a váš web byl zakázán.
* Webové aplikace se zastavilo na portálu.
* Webové aplikace byl dosažen limit kvóty prostředku, který může být použito volné nebo sdílený plán škálování služby.

Chcete zobrazit, co ho způsobuje chybu a vyřešte problém, postupujte podle kroků v [webové aplikace: "Chyba 403 – tato webová aplikace je zastavena"](https://blogs.msdn.microsoft.com/waws/2016/01/05/azure-web-apps-error-403-this-web-app-is-stopped/).

## <a name="where-can-i-learn-more-about-quotas-and-limits-for-various-app-service-plans"></a>Kde můžete další informace o kvóty a omezení pro různé plány služby App Service?

Informace o kvóty a omezení najdete v tématu [omezení služby App Service](../azure-subscription-service-limits.md#app-service-limits). 

## <a name="how-do-i-decrease-the-response-time-for-the-first-request-after-idle-time"></a>Jak zkrátit čas odpovědi na první požadavky po dobu nečinnosti?

Ve výchozím nastavení jsou uvolněna webové aplikace, pokud jsou na nastavenou dobu nečinnosti. Tímto způsobem systému můžete ušetřit prostředky. Nevýhodou je delší, aby webové aplikace načíst a spustit současné obsluhování odpovědí odpověď na první požadavek po webová aplikace je odpojen. V plánech úrovně Basic a Standard service, můžete zapnout **Always On** nastavení nechte aplikaci vždy načíst. Tím se eliminuje delší doby zatížení po nečinnosti aplikace. Chcete-li změnit **Always On** nastavení:

1. Na portálu Azure přejděte do vaší webové aplikace.
2. Vyberte **nastavení aplikace**.
3. Pro **Always On**, vyberte **na**.

## <a name="how-do-i-turn-on-failed-request-tracing"></a>Jak lze zapnout trasování chybných požadavků?

Chcete-li zapnout trasování chybných požadavků:

1. Na portálu Azure přejděte do vaší webové aplikace.
3. Vyberte **všechna nastavení** > **protokolů diagnostiky**.
4. Pro **trasování chybných požadavků**, vyberte **na**.
5. Vyberte **Uložit**.
6. V okně webové aplikace, vyberte **nástroje**.
7. Vyberte **sadou Visual Studio**.
8. Pokud toto nastavení není **na**, vyberte **na**.
9. Vyberte **přejděte**.
10. Vyberte **Web.config**.
11. Oddíl system.webServer přidejte tuto konfiguraci (pro zachycení konkrétní adresy URL):

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
12. K odstraňování problémů způsobit snížení výkonu, přidejte tuto konfiguraci (Pokud zaznamenávání požadavku trvá déle než 30 sekund):
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
13. Stažení trasování chybných požadavků [portál](https://portal.azure.com), přejděte na svůj web.
15. Vyberte **nástroje** > **Kudu** > **přejděte**.
18. V nabídce vyberte **ladění konzoly** > **CMD**.
19. Vyberte **LogFiles** složku a potom vyberte složku s názvem, který začíná **W3SVC**.
20. Pokud chcete zobrazit soubor XML, vyberte ikonu tužky.

## <a name="i-see-the-message-worker-process-requested-recycle-due-to-percent-memory-limit-how-do-i-address-this-issue"></a>Zobrazí zpráva "Pracovní proces si vyžádal recyklování z důvodu omezení, procenta paměti'." Jak tento problém vyřešit?

K dispozici maximální velikost paměti pro 32bitový proces (i na 64bitový operační systém) je 2 GB. Ve výchozím nastavení, pracovní proces je nastaven na 32-bit ve službě App Service (pro kompatibilitu s starší verze webové aplikace).

Zvažte možnost 64bitové procesy, můžete využít další paměť, která je k dispozici v své role pracovního procesu Web. Tím se spustí restartování webové aplikace, takže naplánovat odpovídajícím způsobem.

Všimněte si také, že prostředí 64-bit vyžaduje plán služeb Basic nebo Standard. Uvolněte a sdílené plány vždy spustit v prostředí 32-bit.

Další informace najdete v tématu [konfigurace webové aplikace v App Service](web-sites-configure.md).

## <a name="why-does-my-request-time-out-after-240-seconds"></a>Proč můj časový limit požadavku po 240 sekundách?

Azure nástroj pro vyrovnávání zatížení má výchozí nastavení časového limitu nečinnosti čtyři minut. Obvykle se jedná o přiměřené odpovědi časový limit pro webové žádosti. Pokud vaše webová aplikace vyžaduje zpracování na pozadí, doporučujeme používat Azure WebJobs. Webové aplikace Azure můžete volat webové úlohy a upozorněni, až po dokončení zpracování na pozadí. Můžete se z několika metod pro použití webové úlohy, včetně fronty a aktivační události.

Webové úlohy je navržen pro zpracování na pozadí. Můžete to udělat tolik zpracování na pozadí tak, jak chcete v webovou úlohu. Další informace o Webjobech najdete v tématu [spustit úlohy na pozadí s WebJobs](web-sites-create-web-jobs.md).

## <a name="aspnet-core-applications-that-are-hosted-in-app-service-sometimes-stop-responding-how-do-i-fix-this-issue"></a>Aplikace ASP.NET Core, které jsou hostované ve službě App Service někdy přestanou reagovat. Jak tento problém vyřešit?

O známý problém s předchozím kroku [Kestrel verze](https://github.com/aspnet/KestrelHttpServer/issues/1182) může způsobit 1.0 ASP.NET základní aplikaci, která je hostovaná ve službě App Service občas přestane reagovat. Také může zobrazit tato zpráva: "určená aplikace CGI zachytila chybu a server ukončil proces."

Tento problém vyřešen v Kestrel verzi 1.0.2. Tato verze je zahrnuté v aktualizaci ASP.NET Core 1.0.3. Chcete-li vyřešit tento problém, zkontrolujte, zda že aktualizace závislosti vaše aplikace používat Kestrel 1.0.2. Alternativně můžete použít jednu z dva alternativní postupy, které jsou popsané v příspěvku na blogu [pomalé výkonu ASP.NET Core 1.0 problémy ve službě App Service web apps](https://blogs.msdn.microsoft.com/waws/2016/12/11/asp-net-core-slow-perf-issues-on-azure-websites).


## <a name="i-cant-find-my-log-files-in-the-file-structure-of-my-web-app-how-can-i-find-them"></a>Nelze najít Moje soubory protokolu ve struktuře souborů webová aplikace. Jak je můžete je vyhledat?

Pokud používáte funkci místní mezipaměti služby App Service, se vztahuje struktura složek LogFiles a Data složek pro vaše instance služby App Service. Pokud se používá místní mezipaměti, jsou podsložky vytvořené v úložiště LogFiles a složek s daty. Podsložky použijte pojmenování vzor "Jedinečný identifikátor" + časové razítko. Každé podsložky odpovídá instance virtuálního počítače, ve kterém běží webové aplikace, nebo byla spuštěna.

Pokud chcete zjistit, zda používáte místní mezipaměti, zkontrolovat aplikační služby **nastavení aplikace** kartě. Pokud se používá místní mezipaměti, aplikace, nastavení `WEBSITE_LOCAL_CACHE_OPTION` je nastaven na `Always`.

Pokud nepoužíváte místní mezipaměti a se jedná o problém, odešlete žádost o podporu.

## <a name="i-see-the-message-an-attempt-was-made-to-access-a-socket-in-a-way-forbidden-by-its-access-permissions-how-do-i-resolve-this"></a>Zobrazí zpráva "byl proveden pokus o přístup k soketu způsobem zakázáno podle jeho oprávnění k přístupu." Jak to lze vyřešit?

Této chybě obvykle dochází, pokud jsou vyčerpány odchozí připojení TCP pro instanci virtuálního počítače. Ve službě App Service se vynucují omezení pro maximální počet odchozí připojení, které můžete provést pro každou instanci virtuálního počítače. Další informace najdete v tématu [Cross-VM numerické limity](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits).

Této chybě může také dojít, pokud se pokusíte přístup k místní adresu z vaší aplikace. Další informace najdete v tématu [místní adresu požadavky](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#local-address-requests).

Další informace o odchozí připojení ve vaší webové aplikaci, najdete v příspěvku blogu o [odchozí připojení k webům Azure](http://www.freekpaans.nl/2015/08/starving-outgoing-connections-on-windows-azure-web-sites/).

## <a name="how-do-i-use-visual-studio-to-remote-debug-my-app-service-web-app"></a>Použití sady Visual Studio pro vzdálené ladění webová aplikace služby App Service?

Podrobný postup, který ukazuje, jak ladit webové aplikace pomocí sady Visual Studio najdete v tématu [vzdáleného ladění webové aplikace služby App Service](https://blogs.msdn.microsoft.com/benjaminperkins/2016/09/22/remote-debug-your-azure-app-service-web-app/).
