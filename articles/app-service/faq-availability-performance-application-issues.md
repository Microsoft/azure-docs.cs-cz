---
title: Nejčastější dotazy týkající se výkonu aplikace
description: Získejte odpovědi na nejčastější dotazy týkající se problémů s dostupností, výkonem a aplikací v Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: dfaeee6a6e2a9728d7e63fb5681c487fbbd6139e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88958993"
---
# <a name="application-performance-faqs-for-web-apps-in-azure"></a>Nejčastější dotazy týkající se výkonu aplikací pro Web Apps v Azure

> [!NOTE]
> Některé z těchto pokynů můžou fungovat jenom na Windows nebo Linux App Services. Například Linux App Services ve výchozím nastavení spouštěn ve 64 režimu.
>

Tento článek obsahuje odpovědi na nejčastější dotazy týkající se problémů s výkonem aplikace [Web Apps funkce Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-is-my-app-slow"></a>Proč je moje aplikace pomalá?

K zpomalení výkonu aplikace může přispět více faktorů. Podrobné kroky pro řešení potíží najdete v tématu [řešení potíží s výkonem pomalých webových aplikací](troubleshoot-performance-degradation.md).

## <a name="how-do-i-troubleshoot-a-high-cpu-consumption-scenario"></a>Návody řešit potíže s vysokým scénářem využití procesoru?

V některých velmi vysokých scénářích spotřeby procesoru může vaše aplikace skutečně vyžadovat více výpočetních prostředků.V takovém případě zvažte škálování na vyšší úroveň služby, takže aplikace získá všechny prostředky, které potřebuje. V ostatních případech může být vysoká spotřeba procesoru způsobena chybnou smyčkou nebo postupem kódování. Seznámení s tím, co se aktivuje zvýšené spotřeby procesoru, je proces dvou částí. Nejprve vytvořte výpis procesu a poté Analyzujte výpis procesu. Další informace najdete v tématu [zachycení a analýza souboru s výpisem paměti pro vysokou spotřebu procesoru pro Web Apps](/archive/blogs/asiatech/how-to-capture-dump-when-intermittent-high-cpu-happens-on-azure-web-app).

## <a name="how-do-i-troubleshoot-a-high-memory-consumption-scenario"></a>Návody řešení potíží s vysokým využitím paměti?

V některých scénářích s vysokými nároky na paměť může vaše aplikace skutečně vyžadovat více výpočetních prostředků.V takovém případě zvažte škálování na vyšší úroveň služby, takže aplikace získá všechny prostředky, které potřebuje. Jindy může chyba v kódu způsobit nevracení paměti. Postup kódování může také zvýšit spotřebu paměti.Získání přehledu o tom, co spouští vysokou spotřebu paměti, je proces dvou částí. Nejprve vytvořte výpis procesu a poté Analyzujte výpis procesu. Diagnostiku havárií z galerie rozšíření webu Azure může efektivně provádět oba tyto kroky. Další informace najdete v tématu [zachycení a analýza souboru s výpisem paměti pro přerušovanou vysokou paměť pro Web Apps](/archive/blogs/asiatech/how-to-capture-and-analyze-dump-for-intermittent-high-memory-on-azure-web-app).

## <a name="how-do-i-automate-app-service-web-apps-by-using-powershell"></a>Návody Automatizujte App Service webové aplikace pomocí PowerShellu?

K správě a údržbě App Service webových aplikací můžete použít rutiny prostředí PowerShell. V našem blogovém příspěvku můžete [automatizovat webové aplikace hostované v Azure App Service pomocí prostředí PowerShell](/archive/blogs/puneetgupta/automating-webapps-hosted-in-azure-app-service-through-powershell-arm-way). popisujeme, jak používat rutiny prostředí PowerShell založené na Azure Resource Manager k automatizaci běžných úloh. Blogový příspěvek má také vzorový kód pro různé úlohy správy webových aplikací. Popisy a syntaxi pro všechny rutiny App Service Web Apps najdete v tématu [AZ. websites](/powershell/module/az.websites).

## <a name="how-do-i-view-my-web-apps-event-logs"></a>Návody zobrazit protokoly událostí webové aplikace?

Chcete-li zobrazit protokoly událostí vaší webové aplikace:

1. Přihlaste se k **webu Kudu** ( `https://*yourwebsitename*.scm.azurewebsites.net` ).
2. V nabídce vyberte **ladit konzolu**  >  **cmd**.
3. Vyberte složku **soubory protokolů** .
4. Chcete-li zobrazit protokoly událostí, vyberte ikonu tužky vedle **eventlog.xml**.
5. Pokud chcete stáhnout protokoly, spusťte rutinu PowerShellu `Save-AzureWebSiteLog -Name webappname` .

## <a name="how-do-i-capture-a-user-mode-memory-dump-of-my-web-app"></a>Návody zachytit výpis paměti moje webové aplikace v uživatelském režimu?

Postup při zaznamenání výpisu paměti ve vaší webové aplikaci v uživatelském režimu:

1. Přihlaste se k **webu Kudu** ( `https://*yourwebsitename*.scm.azurewebsites.net` ).
2. Vyberte nabídku **Průzkumník procesů** .
3. Klikněte pravým tlačítkem na proces **w3wp.exe** nebo na proces webové úlohy.
4. Vyberte možnost **Stáhnout výpis stavu paměti**–  >  **úplný výpis**.

## <a name="how-do-i-view-process-level-info-for-my-web-app"></a>Návody zobrazit informace na úrovni procesu pro moji webovou aplikaci?

Pro zobrazení informací na úrovni procesu pro vaši webovou aplikaci máte dvě možnosti:

*   Na webu Azure Portal:
    1. Otevřete **Průzkumníka procesů** pro webovou aplikaci.
    2. Chcete-li zobrazit podrobnosti, vyberte proces **w3wp.exe** .
*   V konzole Kudu:
    1. Přihlaste se k **webu Kudu** ( `https://*yourwebsitename*.scm.azurewebsites.net` ).
    2. Vyberte nabídku **Průzkumník procesů** .
    3. V případě procesu **w3wp.exe** vyberte možnost **vlastnosti**.

## <a name="when-i-browse-to-my-app-i-see-error-403---this-web-app-is-stopped-how-do-i-resolve-this"></a>Při procházení k aplikaci se zobrazí zpráva "Chyba 403-Tato webová aplikace je zastavená". Jak to můžu vyřešit?

Tato chyba může způsobit tři podmínky:

* U webové aplikace se dosáhlo limitu fakturace a vaše lokalita je zakázaná.
* Webová aplikace se zastavila na portálu.
* U webové aplikace se dosáhlo limitu kvóty prostředků, který by se mohl vztahovat na plán služby škály free nebo Shared.

Chcete-li zjistit, co způsobuje chybu a vyřešit problém, postupujte podle kroků v [Web Apps: "error 403 – Tato webová aplikace je zastavena"](/archive/blogs/waws/azure-web-apps-error-403-this-web-app-is-stopped).

## <a name="where-can-i-learn-more-about-quotas-and-limits-for-various-app-service-plans"></a>Kde se mohu dozvědět více o kvótách a omezeních pro různé plány App Service?

Informace o kvótách a omezeních najdete v tématu [omezení App Service](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits). 

## <a name="how-do-i-decrease-the-response-time-for-the-first-request-after-idle-time"></a>Návody zkrátit dobu odezvy pro první požadavek po nečinnosti?

Ve výchozím nastavení jsou webové aplikace uvolněny, pokud nejsou po stanovenou dobu nečinné. Systém tak může šetřit prostředky. Nevýhodou je to, že odpověď na první požadavek po uvolnění webové aplikace je delší, aby mohla webová aplikace načíst a začít obsluhovat odpovědi. V plánech Basic a Standard můžete zapnout nastavení **Always On** , aby se aplikace pořád načetla. Tím se eliminuje delší doba načítání po nečinnosti aplikace. Chcete-li změnit nastavení **vždy zapnuto** :

1. V Azure Portal přejdete do své webové aplikace.
2. Vybrat **konfiguraci**
3. Vyberte **Obecné nastavení**.
4. Pro možnost **vždy zapnuto**vyberte **zapnuto**.

## <a name="how-do-i-turn-on-failed-request-tracing"></a>Návody zapnout trasování chybných požadavků?

Zapnutí trasování chybných požadavků:

1. V Azure Portal přejdete do své webové aplikace.
3. Vyberte **všechna nastavení**  >  **diagnostické protokoly**.
4. Pro **trasování chybných požadavků**vyberte **zapnuto**.
5. Vyberte **Uložit**.
6. V okně webová aplikace vyberte **nástroje**.
7. Vyberte **Visual Studio Online**.
8. Pokud nastavení není **zapnuto**, vyberte **zapnuto**.
9. Vyberte **Přejít**.
10. Vyberte **Web.config**.
11. V System. webServer přidejte tuto konfiguraci (pro zachycení konkrétní adresy URL):

    ```xml
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
12. Chcete-li vyřešit problémy s pomalým výkonem, přidejte tuto konfiguraci (Pokud požadavek na zachycení trvá déle než 30 sekund):
    ```xml
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
13. Pokud si chcete stáhnout trasování chybných požadavků, na [portálu](https://portal.azure.com)přejdete na svůj web.
15. Vyberte **nástroje**  >  **Kudu**  >  **Přejít**.
18. V nabídce vyberte **ladit konzolu**  >  **cmd**.
19. Vyberte složku **soubory protokolu** a potom vyberte složku s názvem, který začíná na **W3SVC**.
20. Chcete-li zobrazit soubor XML, vyberte ikonu tužky.

## <a name="i-see-the-message-worker-process-requested-recycle-due-to-percent-memory-limit-how-do-i-address-this-issue"></a>Zobrazuje se zpráva "pracovní proces požádal o recyklaci z důvodu limitu procent paměti". Návody vyřešit tento problém?

Maximální dostupné množství paměti pro 32 proces (i na 64 operačním systému) je 2 GB. Ve výchozím nastavení je pracovní proces nastaven na 32-bit v App Service (pro kompatibilitu se staršími webovými aplikacemi).

Zvažte přechod na 64 procesů, abyste mohli využít další paměť dostupnou v roli webového pracovního procesu. Tím se aktivuje restart webové aplikace, takže se odpovídajícím způsobem naplánujte.

Všimněte si také, že 64 prostředí vyžaduje plán služby Basic nebo Standard. Bezplatné a sdílené plány se vždycky spouštějí v 32ovém prostředí.

Další informace najdete v tématu [Konfigurace webových aplikací v App Service](configure-common.md).

## <a name="why-does-my-request-time-out-after-230-seconds"></a>Proč vypršel časový limit mého požadavku po 230 sekundách?

Azure Load Balancer má výchozí nastavení časového limitu nečinnosti čtyři minuty. Toto je obecně přiměřený časový limit odezvy pro webový požadavek. Pokud vaše webová aplikace vyžaduje zpracování na pozadí, doporučujeme použít Azure WebJobs. Webová aplikace Azure může zavolat WebJobs a upozornit na dokončení zpracování na pozadí. Můžete si vybrat z několika metod používání služby WebJobs, včetně front a triggerů.

Webové úlohy jsou navržené pro zpracování na pozadí. V rámci webové úlohy můžete provádět tolik zpracování na pozadí. Další informace o webových úlohách najdete v tématu [spouštění úloh na pozadí pomocí WebJobs](webjobs-create.md).

## <a name="aspnet-core-applications-that-are-hosted-in-app-service-sometimes-stop-responding-how-do-i-fix-this-issue"></a>ASP.NET Core aplikace, které jsou hostované v App Service někdy přestanou reagovat. Návody tento problém vyřešit?

Známý problém s dřívější [verzí Kestrel](https://github.com/aspnet/KestrelHttpServer/issues/1182) může způsobit, že aplikace ASP.NET Core 1,0, která je hostovaná v App Service, bude schopná přestat reagovat. Může se také zobrazit tato zpráva: v zadané aplikaci CGI došlo k chybě a server ukončil proces.

Tento problém je opravený v Kestrel verze 1.0.2. Tato verze je součástí aktualizace ASP.NET Core 1.0.3. Pokud chcete tento problém vyřešit, nezapomeňte aktualizovat závislosti aplikací tak, aby používaly Kestrel 1.0.2. Alternativně můžete použít jedno ze dvou alternativních řešení, která jsou popsána v příspěvku blogu [ASP.NET Core 1,0 pomalých potížích s výkonem v App Service Web Apps](/archive/blogs/waws/asp-net-core-slow-perf-issues-on-azure-websites).


## <a name="i-cant-find-my-log-files-in-the-file-structure-of-my-web-app-how-can-i-find-them"></a>Nemohu najít soubory protokolu ve struktuře souborů moje webové aplikace. Jak je můžu najít?

Pokud používáte funkci místní mezipaměti App Service, jsou ovlivněny struktury složek protokolů a složek dat pro vaši instanci App Service. Při použití místní mezipaměti se v protokolech úložiště a složkách dat vytvoří podsložky. Podsložky používají vzor pojmenování "jedinečný identifikátor" + časové razítko. Každá podsložka odpovídá instanci virtuálního počítače, ve které je webová aplikace spuštěná nebo spuštěná.

Chcete-li zjistit, zda používáte místní mezipaměť, zkontrolujte kartu **nastavení aplikace** App Service. Pokud se používá místní mezipaměť, nastavení aplikace `WEBSITE_LOCAL_CACHE_OPTION` je nastaveno na `Always` .

Pokud nepoužíváte místní mezipaměť a dochází k tomuto problému, odešlete žádost o podporu.

## <a name="i-see-the-message-an-attempt-was-made-to-access-a-socket-in-a-way-forbidden-by-its-access-permissions-how-do-i-resolve-this"></a>Zobrazí se zpráva oznamující, že došlo k pokusu o přístup k soketu způsobem, který zakázal jeho přístupová oprávnění. Jak to můžu vyřešit?

K této chybě obvykle dochází v případě, že jsou vyčerpána odchozí připojení TCP na instanci virtuálního počítače. V App Service se limity vynutily pro maximální počet odchozích připojení, která se dají vytvořit pro každou instanci virtuálního počítače. Další informace najdete v tématu [Číselná omezení pro více virtuálních počítačů](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits).

K této chybě může dojít také v případě, že se pokusíte o přístup k místní adrese z vaší aplikace. Další informace najdete v tématu [požadavky na místní adresy](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#local-address-requests).

Další informace o odchozích připojeních ve vaší webové aplikaci najdete v blogovém příspěvku o [odchozích připojeních k Azure websites](https://www.freekpaans.nl/2015/08/starving-outgoing-connections-on-windows-azure-web-sites/).

## <a name="how-do-i-use-visual-studio-to-remote-debug-my-app-service-web-app"></a>Návody použít Visual Studio ke vzdálenému ladění moje App Service webové aplikace?

Podrobný návod, který ukazuje, jak webovou aplikaci ladit pomocí sady Visual Studio, najdete v tématu [vzdálené ladění App Service webové aplikace](/archive/blogs/benjaminperkins/remote-debug-your-azure-app-service-web-app).