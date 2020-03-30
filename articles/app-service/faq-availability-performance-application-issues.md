---
title: Časté otázky týkající se výkonu aplikací
description: Získejte odpovědi na nejčastější dotazy týkající se dostupnosti, výkonu a problémů s aplikacemi ve službě Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 433f5885c7f057226e78c4ae57e03d7619004d21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259861"
---
# <a name="application-performance-faqs-for-web-apps-in-azure"></a>Časté otázky k výkonu aplikací pro webové aplikace v Azure

> [!NOTE]
> Některé z následujících pokynů mohou fungovat pouze ve Windows nebo Linux App Services. Například Linux App Services běží ve výchozím nastavení v 64bitovém režimu.
>

Tento článek obsahuje odpovědi na nejčastější dotazy týkající se problémů s výkonem aplikací pro [funkci Webové aplikace služby Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-is-my-app-slow"></a>Proč je moje aplikace pomalá?

Ke snížení výkonu aplikací může přispět několik faktorů. Podrobné kroky řešení potíží najdete [v tématu Poradce při potížích s pomalým výkonem webových aplikací](troubleshoot-performance-degradation.md).

## <a name="how-do-i-troubleshoot-a-high-cpu-consumption-scenario"></a>Jak lze vyřešit problém se scénářem vysoké spotřeby procesoru?

V některých scénářích s vysokou spotřebou procesoru může vaše aplikace skutečně vyžadovat více výpočetních prostředků.V takovém případě zvažte škálování na vyšší úroveň služby, aby aplikace získá všechny prostředky, které potřebuje. Jindy může být vysoká spotřeba procesoru způsobena chybnou smyčkou nebo praxí kódování. Získání přehledu o tom, co spouští zvýšenou spotřebu procesoru, je dvoudílný proces. Nejprve vytvořte výpis procesu a potom analyzujte výpis procesu. Další informace naleznete v [tématu Zachycení a analýza souboru s výpisem stavu paměti pro vysokou spotřebu procesoru pro webové aplikace](https://blogs.msdn.microsoft.com/asiatech/2016/01/20/how-to-capture-dump-when-intermittent-high-cpu-happens-on-azure-web-app/).

## <a name="how-do-i-troubleshoot-a-high-memory-consumption-scenario"></a>Jak lze vyřešit problém s vysokou spotřebou paměti?

V některých scénářích s vysokou spotřebou paměti může vaše aplikace skutečně vyžadovat více výpočetních prostředků.V takovém případě zvažte škálování na vyšší úroveň služby, aby aplikace získá všechny prostředky, které potřebuje. Jindy může chyba v kódu způsobit nevracení paměti. Kódování praxe také může zvýšit spotřebu paměti.Získání přehledu o tom, co spouští vysokou spotřebu paměti, je dvoudílný proces. Nejprve vytvořte výpis procesu a potom analyzujte výpis procesu. Diagnostikátor selhání z Galerie rozšíření webu Azure můžete efektivně provádět oba tyto kroky. Další informace naleznete v [tématu Zachycení a analýza souboru s výpisem stavu paměti pro přerušované vysoké paměti pro webové aplikace](https://blogs.msdn.microsoft.com/asiatech/2016/02/02/how-to-capture-and-analyze-dump-for-intermittent-high-memory-on-azure-web-app/).

## <a name="how-do-i-automate-app-service-web-apps-by-using-powershell"></a>Jak můžu automatizovat webové aplikace služby App Service pomocí PowerShellu?

Rutiny prostředí PowerShell můžete použít ke správě a údržbě webových aplikací služby App Service. V našem příspěvku blogu [Automatizujte webové aplikace hostované ve službě Azure App Service pomocí PowerShellu](https://blogs.msdn.microsoft.com/puneetgupta/2016/03/21/automating-webapps-hosted-in-azure-app-service-through-powershell-arm-way/)popisujeme, jak pomocí rutin Prostředí PowerShell založených na Azure Resource Manageru automatizovat běžné úkoly. Příspěvek na blogu má také ukázkový kód pro různé úlohy správy webových aplikací. Popisy a syntaxe pro všechny rutiny webových aplikací služby App Service najdete v [tématu Az.Websites](/powershell/module/az.websites).

## <a name="how-do-i-view-my-web-apps-event-logs"></a>Jak zobrazím protokoly událostí webové aplikace?

Zobrazení protokolů událostí webové aplikace:

1. Přihlaste se na [své webové stránky Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
2. V nabídce vyberte **možnost Ladění konzoly** > **CMD**.
3. Vyberte složku **LogFiles.**
4. Chcete-li zobrazit protokoly událostí, vyberte ikonu tužky vedle **souboru eventlog.xml**.
5. Chcete-li stáhnout protokoly, spusťte `Save-AzureWebSiteLog -Name webappname`rutinu prostředí PowerShell .

## <a name="how-do-i-capture-a-user-mode-memory-dump-of-my-web-app"></a>Jak zachytím výpis paměti v uživatelském režimu webové aplikace?

Zachycení výpisu paměti webové aplikace v uživatelském režimu:

1. Přihlaste se na [své webové stránky Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
2. Vyberte nabídku **Průzkumník procesů.**
3. Klepněte pravým tlačítkem myši na proces **w3wp.exe** nebo proces webjob.
4. Vyberte **možnost Stáhnout úplný výpis stavu** > **paměti**.

## <a name="how-do-i-view-process-level-info-for-my-web-app"></a>Jak zobrazím informace na úrovni procesu pro webovou aplikaci?

Pro webovou aplikaci máte dvě možnosti zobrazení informací na úrovni procesu:

*   Na webu Azure Portal:
    1. Otevřete **Průzkumníkprocesů** pro webovou aplikaci.
    2. Chcete-li zobrazit podrobnosti, vyberte proces **w3wp.exe.**
*   V konzoli Kudu:
    1. Přihlaste se na [své webové stránky Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
    2. Vyberte nabídku **Průzkumník procesů.**
    3. Pro proces **w3wp.exe** vyberte **vlastnosti**.

## <a name="when-i-browse-to-my-app-i-see-error-403---this-web-app-is-stopped-how-do-i-resolve-this"></a>Když přejdu do aplikace, zobrazí se mi "Chyba 403 – tato webová aplikace je zastavena." Jak to můžu vyřešit?

Tato chyba může způsobit tři podmínky:

* Webová aplikace dosáhla fakturačního limitu a váš web byl zakázán.
* Webová aplikace byla na portálu zastavena.
* Webová aplikace dosáhla limitu kvóty prostředků, který se může vztahovat na plán služby Bezplatné nebo sdílené škálování.

Chcete-li zjistit, co je příčinou chyby a vyřešit problém, postupujte podle pokynů v [webových aplikacích: "Chyba 403 – Tato webová aplikace je zastavena"](https://blogs.msdn.microsoft.com/waws/2016/01/05/azure-web-apps-error-403-this-web-app-is-stopped/).

## <a name="where-can-i-learn-more-about-quotas-and-limits-for-various-app-service-plans"></a>Kde najdu další informace o kvótách a limitech pro různé plány služby App Service?

Informace o kvótách a limitech najdete v [tématu Limity služby App Service](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits). 

## <a name="how-do-i-decrease-the-response-time-for-the-first-request-after-idle-time"></a>Jak lze zkrátit dobu odezvy pro první požadavek po nečinnosti?

Ve výchozím nastavení jsou webové aplikace uvolněny, pokud jsou nečinné po nastavenou dobu. Tímto způsobem může systém šetřit prostředky. Nevýhodou je, že odpověď na první požadavek po uvolnění webové aplikace je delší, aby webová aplikace mohla načíst a začít zobrazovat odpovědi. V základních a standardních plánech služeb můžete zapnout nastavení **Vždy zapnuto,** aby byla aplikace vždy načtená. Tím se eliminuje delší doby načítání po nečinnosti aplikace. Změna nastavení **Vždy zapnuto:**

1. Na webu Azure Portal přejděte do webové aplikace.
2. Vybrat **konfiguraci**
3. Vyberte **Obecné nastavení**.
4. V **popřípadě Vždy zapnuto**vyberte **Možnost Zapnuto**.

## <a name="how-do-i-turn-on-failed-request-tracing"></a>Jak lze zapnout trasování neúspěšných požadavků?

Zapnutí trasování neúspěšných požadavků:

1. Na webu Azure Portal přejděte do webové aplikace.
3. Vyberte**všechny protokoly diagnostiky** **nastavení** > .
4. V **části Trasování neúspěšných požadavků**vyberte možnost **Zapnuto**.
5. Vyberte **Uložit**.
6. V okně webové aplikace vyberte **Nástroje**.
7. Vyberte **Visual Studio Online**.
8. Pokud nastavení není **zapnuto**, vyberte **Možnost Zapnuto**.
9. Vyberte **Přejít**.
10. Vyberte **web.config**.
11. V system.webServer, přidejte tuto konfiguraci (zachytit konkrétní URL):

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
12. Chcete-li vyřešit problémy s pomalým výkonem, přidejte tuto konfiguraci (pokud požadavek na zachycení trvá déle než 30 sekund):
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
13. Chcete-li stáhnout trasování neúspěšných požadavků, přejděte na [portál](https://portal.azure.com)na svůj web.
15. Vyberte **Nástroje** > **Kudu** > **Go**.
18. V nabídce vyberte **možnost Ladění konzoly** > **CMD**.
19. Vyberte složku **LogFiles** a pak vyberte složku s názvem začínajícím **w3SVC**.
20. Chcete-li zobrazit soubor XML, vyberte ikonu tužky.

## <a name="i-see-the-message-worker-process-requested-recycle-due-to-percent-memory-limit-how-do-i-address-this-issue"></a>Zobrazuje se zpráva "Pracovní proces požadoval recyklaci z důvodu limitu procenta paměti". Jak tento problém řeším?

Maximální dostupné množství paměti pro 32bitový proces (i v 64bitovém operačním systému) je 2 GB. Ve výchozím nastavení je pracovní proces ve službě App Service nastaven na 32bitový (z důvodu kompatibility se staršími webovými aplikacemi).

Zvažte přechod na 64bitové procesy, abyste mohli využít další paměť, která je k dispozici v roli webového pracovního procesu. Tím se spustí restartování webové aplikace, takže plán odpovídajícím způsobem.

Všimněte si také, že 64bitové prostředí vyžaduje základní nebo standardní plán služeb. Bezplatné a sdílené plány vždy běží v 32bitovém prostředí.

Další informace najdete [v tématu Konfigurace webových aplikací ve službě App Service](configure-common.md).

## <a name="why-does-my-request-time-out-after-230-seconds"></a>Proč můj požadavek časový masív po 230 sekundách?

Azure Load Balancer má výchozí nastavení časového limitu nečinnosti čtyři minuty. Toto je obecně přiměřená doba odezvy pro webový požadavek. Pokud vaše webová aplikace vyžaduje zpracování na pozadí, doporučujeme používat Azure WebJobs. Webová aplikace Azure může volat WebJobs a být upozorněni po dokončení zpracování na pozadí. Můžete si vybrat z několika metod pro používání webových úloh, včetně front a aktivačních událostí.

WebJobs je určen pro zpracování na pozadí. Ve službě WebJob můžete provést zpracování na pozadí, kolik chcete. Další informace o službě WebJobs naleznete v [tématu Spouštění úloh na pozadí pomocí služby WebJobs](webjobs-create.md).

## <a name="aspnet-core-applications-that-are-hosted-in-app-service-sometimes-stop-responding-how-do-i-fix-this-issue"></a>ASP.NET základní aplikace, které jsou hostované ve službě App Service někdy přestat reagovat. Jak tento problém vyřeším?

Známý problém s dřívější [verzí Kestrel](https://github.com/aspnet/KestrelHttpServer/issues/1182) může způsobit, že aplikace ASP.NET Core 1.0, která je hostovaná ve službě App Service, občas přestane reagovat. Může se také zobrazit tato zpráva: "Zadaná aplikace CGI zjistila chybu a server proces ukončil."

Tento problém je vyřešen v Kestrel verze 1.0.2. Tato verze je součástí aktualizace ASP.NET Core 1.0.3. Chcete-li tento problém vyřešit, ujistěte se, že aktualizujete závislosti aplikací tak, aby používaly Kestrel 1.0.2. Případně můžete použít jedno ze dvou řešení, která jsou popsána v příspěvku blogu [ASP.NET problémy s pomalým perf jádrem 1.0 ve webových aplikacích Služby App Service](https://blogs.msdn.microsoft.com/waws/2016/12/11/asp-net-core-slow-perf-issues-on-azure-websites).


## <a name="i-cant-find-my-log-files-in-the-file-structure-of-my-web-app-how-can-i-find-them"></a>Soubory protokolu nelze najít ve struktuře souborů webové aplikace Jak je mohu najít?

Pokud používáte funkci Místní mezipaměť služby App Service, bude ovlivněna struktura složek LogFiles a Data pro instanci služby App Service. Při použití místní mezipaměti jsou v souborech LogFiles úložiště a datových složkách vytvořeny podsložky. Podsložky používají vzor pojmenování "jedinečný identifikátor" + časové razítko. Každá podsložka odpovídá instanci virtuálního aplikace, ve které je webová aplikace spuštěná nebo spuštěná.

Chcete-li zjistit, zda používáte místní mezipaměť, zkontrolujte kartu **Nastavení aplikace** služby App Service. Pokud se používá místní mezipaměť, `WEBSITE_LOCAL_CACHE_OPTION` nastavení `Always`aplikace je nastaveno na .

Pokud nepoužíváte místní mezipaměti a dochází k tomuto problému, odešlete žádost o podporu.

## <a name="i-see-the-message-an-attempt-was-made-to-access-a-socket-in-a-way-forbidden-by-its-access-permissions-how-do-i-resolve-this"></a>Zobrazuje se zpráva "Došlo k pokusu o přístup k soketu způsobem zakázaným jeho přístupovými oprávněními." Jak to můžu vyřešit?

K této chybě obvykle dochází, pokud jsou vyčerpána odchozí připojení TCP v instanci virtuálního počítači. Ve službě App Service se vynucují limity pro maximální počet odchozích připojení, která lze provést pro každou instanci virtuálního aplikace. Další informace naleznete v [tématu Číselné limity mezi virtuálními virtuálními virtuálními min](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits).

K této chybě může dojít také při pokusu o přístup k místní adrese z aplikace. Další informace naleznete v [tématu Místní požadavky na adresy](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#local-address-requests).

Další informace o odchozích připojeních ve webové aplikaci najdete v příspěvku blogu o [odchozích připojeních k webům Azure](https://www.freekpaans.nl/2015/08/starving-outgoing-connections-on-windows-azure-web-sites/).

## <a name="how-do-i-use-visual-studio-to-remote-debug-my-app-service-web-app"></a>Jak se pomocí Visual Studia dostanu ke vzdálenému ladění webové aplikace App Service?

Podrobný návod, který ukazuje, jak ladit webovou aplikaci pomocí Visual Studia, najdete v článku [Vzdálené ladění webové aplikace App Service](https://blogs.msdn.microsoft.com/benjaminperkins/2016/09/22/remote-debug-your-azure-app-service-web-app/).
