---
title: Řešení potíží s agentem Log Analytics pro Windows
description: Popište příznaky, příčiny a řešení nejběžnějších problémů s agentem Log Analytics pro systém Windows v Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/21/2019
ms.openlocfilehash: 3d99293ea83c883f8d0870d78dfbec58f74c9bd1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87927313"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-windows"></a>Řešení potíží s agentem Log Analytics pro Windows 

Tento článek poskytuje nápovědu k řešení chyb, se kterými se můžete setkat s agentem Log Analytics pro Windows v Azure Monitor a navrhuje možná řešení k jejich řešení.

Pokud žádný z těchto kroků nefunguje za vás, jsou k dispozici i tyto kanály podpory:

* Zákazníci s výhodami Premier Support můžou otevřít žádost o podporu pomocí [Premier](https://premier.microsoft.com/).
* Zákazníci se smlouvou o podpoře Azure můžou na [Azure Portal](https://manage.windowsazure.com/?getsupport=true)otevřít žádost o podporu.
* Navštivte stránku Log Analytics zpětnou vazbu pro kontrolu odeslaných nápadů a chyb [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback) nebo pro nové soubory. 

## <a name="important-troubleshooting-sources"></a>Důležité zdroje pro odstraňování potíží

 Pro pomoc s řešením potíží souvisejících s Log Analytics agentem pro Windows Agent protokoluje události do protokolu událostí systému Windows, konkrétně v části *aplikace a správce Services\Operations*.  

## <a name="connectivity-issues"></a>Problémy s připojením

Pokud agent komunikuje prostřednictvím proxy server nebo brány firewall, mohou být zavedena omezení, která zabraňují komunikaci ze zdrojového počítače a služby Azure Monitor. V případě, že komunikace je blokována z důvodu chyby konfigurace, může registrace v pracovním prostoru selhat při pokusu o instalaci agenta nebo konfiguraci agenta po instalaci pro hlášení do dalšího pracovního prostoru. Komunikace agenta může po úspěšné registraci selhat. Tato část popisuje metody pro řešení tohoto typu problému s agentem Windows.

Ověřte, že je brána firewall nebo proxy server nakonfigurovaná tak, aby povolovala následující porty a adresy URL, které jsou popsané v následující tabulce. Ověřte také, že kontrola protokolu HTTP není povolena pro webový provoz, protože může bránit zabezpečenému kanálu TLS mezi agentem a Azure Monitor.  

|Prostředek agenta|Porty |Směr |Obejít kontrolu protokolu HTTPS|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Port 443 |Odchozí|Yes |  
|*.oms.opinsights.azure.com |Port 443 |Odchozí|Yes |  
|*.blob.core.windows.net |Port 443 |Odchozí|Yes |  
|*. agentsvc.azure-automation.net |Port 443 |Odchozí|Yes |  

Informace o bráně firewall požadované pro Azure Government najdete v tématu [správa Azure Government](../../azure-government/compare-azure-government-global-azure.md#azure-monitor). Pokud plánujete použít Azure Automation Hybrid Runbook Worker k připojení a registraci ve službě Automation pro použití sad Runbook nebo řešení správy ve vašem prostředí, musí mít přístup k číslu portu a adresám URL popsaným v tématu [Konfigurace sítě pro Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

Existuje několik způsobů, jak můžete ověřit, zda agent úspěšně komunikuje s Azure Monitor.

- Povolte v pracovním prostoru [hodnocení agent Health Azure Log Analytics](../insights/solution-agenthealth.md) . Z řídicího panelu Agent Health můžete zobrazit sloupec **počet nereagujících agentů** a rychle zjistit, jestli je agent uvedený.  

- Spuštěním následujícího dotazu potvrďte, že Agent odesílá prezenční signál do pracovního prostoru, do kterého je nakonfigurované hlášení. Nahraďte `<ComputerName>` skutečným názvem počítače.

    ```
    Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer 
    ```

    Pokud počítač úspěšně komunikuje se službou, dotaz by měl vrátit výsledek. Pokud dotaz nevrátil výsledek, nejdřív ověřte, že agent je nakonfigurovaný tak, aby nahlásil správnému pracovnímu prostoru. Pokud je správně nakonfigurována, přejděte ke kroku 3 a vyhledejte v protokolu událostí systému Windows, zda má agent protokolovat, jaký problém by mohl bránit v komunikaci s Azure Monitor.

- Další metodou identifikace problému s připojením je spuštění nástroje **TestCloudConnectivity** . Nástroj je ve výchozím nastavení nainstalován s agentem ve složce *%systemroot%\Program Files\Microsoft monitoring Agent\Agent*. Z příkazového řádku se zvýšenými oprávněními přejděte do složky a spusťte nástroj. Nástroj vrátí výsledky a zvýrazní, kde se test nezdařil (například pokud se jednalo o konkrétní port nebo adresu URL, která byla zablokována). 

    ![Výsledky spuštění nástroje TestCloudConnection](./media/agent-windows-troubleshoot/output-testcloudconnection-tool-01.png)

- Filtrujte protokol událostí *Operations Manager* podle **zdrojů událostí**  -  *Health Service moduly*, *HealthService*a *konektor služby* a filtrujte podle *Upozornění* na **úrovni události** a *chybu* , abyste se ujistili, zda mají v následující tabulce zapsané události. Pokud jsou, Projděte si postup řešení, který je součástí jednotlivých možných událostí.

    |ID události |Zdroj |Popis |Řešení |
    |---------|-------|------------|-----------|
    |2133 & 2129 |Služba Health Service |Nepovedlo se připojit ke službě z agenta. |K této chybě může dojít, pokud Agent nemůže komunikovat přímo nebo přes bránu firewall/proxy server ke službě Azure Monitor. Ověřte nastavení proxy serveru agenta nebo síťová brána firewall/proxy umožňuje provoz TCP z počítače do služby.|
    |2138 |Moduly Health Service |Proxy server vyžaduje ověření |Nakonfigurujte nastavení proxy serveru agenta a zadejte uživatelské jméno/heslo potřebné k ověření pomocí proxy server. |
    |2129 |Moduly Health Service |Neúspěšné připojení/neúspěšné vyjednávání TLS |Ověřte nastavení protokolu TCP/IP síťového adaptéru a nastavení proxy agenta.|
    |2 127 |Moduly Health Service |Chyba při odesílání dat přijatý kód chyby |Pokud k tomu dochází pouze pravidelně během dne, může to být pouze náhodná anomálie, kterou lze ignorovat. Sledujte, jak často dochází k tomu. Pokud k tomu dojde často v průběhu dne, nejprve ověřte konfiguraci sítě a nastavení proxy serveru. Pokud popis obsahuje kód chyby HTTP 404 a je první, když se agent pokusí odeslat data službě, bude obsahovat chybu 500 s vnitřním kódem chyby 404. 404 se nenašly, což znamená, že se stále zřizuje oblast úložiště pro nový pracovní prostor. Při příštím opakování budou data v pracovním prostoru úspěšně zapsána podle očekávání. Chyba protokolu HTTP 403 může indikovat problém s oprávněním nebo přihlašovacími údaji. V případě chyby 403 jsou k dispozici další informace, které vám pomůžou problém vyřešit.|
    |4000 |Konektor služby |Překlad názvu DNS se nezdařil. |Počítač nemohl přeložit internetovou adresu, která se používá při odesílání dat službě. To může být nastavení překladače DNS na vašem počítači, nesprávné nastavení proxy serveru nebo možná dočasný problém DNS s vaším poskytovatelem. Pokud k tomu dojde pravidelně, může to být způsobeno přechodným problémem souvisejícím se sítí.|
    |4001 |Konektor služby |Připojení ke službě se nezdařilo. |K této chybě může dojít, pokud Agent nemůže komunikovat přímo nebo přes bránu firewall/proxy server ke službě Azure Monitor. Ověřte nastavení proxy serveru agenta nebo síťová brána firewall/proxy umožňuje provoz TCP z počítače do služby.|
    |4002 |Konektor služby |Služba vrátila stavový kód HTTP 403 v reakci na dotaz. Obraťte se na správce služby se stavem služby. Dotaz se bude opakovat později. |Tato chyba se zapisuje během fáze prvotní registrace agenta a zobrazí se adresa URL podobná následující: *https:// \<workspaceID> . OMS.opinsights.Azure.com/AgentService.svc/AgentTopologyRequest*. Kód chyby 403 znamená zakázaný a může být způsoben nesprávně zadaným ID pracovního prostoru nebo klíčem nebo jsou data a čas v počítači nesprávné. Pokud je čas +/-15 minut od aktuálního času, registrace se nezdařila. Tuto chybu opravíte tak, že aktualizujete datum nebo časové pásmo počítače s Windows.|

## <a name="data-collection-issues"></a>Problémy se shromažďováním dat

Po instalaci agenta a hlášení do nakonfigurovaného pracovního prostoru nebo pracovních prostorů může přestat přijímat konfiguraci, shromažďovat nebo předávat výkon, protokoly nebo jiná data službě v závislosti na tom, co je povolené a cílí na počítač. Je nutné určit, zda:

- Je to konkrétní datový typ nebo všechna data, která nejsou v pracovním prostoru k dispozici?
- Je datový typ zadaný řešením nebo zadaný jako součást konfigurace shromažďování dat v pracovním prostoru?
- Kolik počítačů je ovlivněno? Jedná se o jeden nebo víc počítačů, které se hlásí do pracovního prostoru?
- Pracovalo a zastavilo se v konkrétní denní době nebo nebylo dosud shromážděno? 
- Používá se dotaz na prohledávání protokolu syntakticky správný? 
- Přijal Agent od Azure Monitor svou konfiguraci?

Prvním krokem při řešení potíží je určit, jestli počítač posílá událost prezenčního signálu.

```
Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer
```

Pokud dotaz vrátí výsledky, je nutné určit, zda určitý datový typ není shromážděn a předán do služby. To může být způsobeno tím, že agent neobdrží aktualizovanou konfiguraci ze služby nebo nějaký jiný příznak, který brání v běžném provozu agenta. K dalšímu řešení potíží proveďte následující kroky.

1. V počítači otevřete příkazový řádek se zvýšenými oprávněními a restartujte službu agenta zadáním příkazu `net stop healthservice && net start healthservice` .
2. Otevřete protokol událostí *Operations Manager* a vyhledejte **ID událostí** *7023, 7024, 7025, 7028* a *1210* ze **zdroje událostí** *HealthService*.  Tyto události označují, že agent úspěšně přijímá konfiguraci od Azure Monitor a aktivně monitoruje počítač. Popis události pro ID události 1210 se také určí na posledním řádku všechna řešení a přehledy, které jsou součástí rozsahu monitorování v agentovi.  

    ![Popis události s ID 1210](./media/agent-windows-troubleshoot/event-id-1210-healthservice-01.png)

3. Pokud po několika minutách nevidíte očekávaná data ve výsledcích nebo vizualizaci dotazu, v závislosti na tom, jestli prohlížíte data z řešení nebo vhledu, z protokolu událostí *Operations Manager* vyhledejte **zdroje událostí** *HealthService* a *Health Service* a vyfiltrujte *Upozornění* podle **úrovně události** a *chybu* , abyste se ujistili, jestli mají v následující tabulce napsané události.

    |ID události |Zdroj |Popis |Řešení |
    |---------|-------|------------|
    |8000 |HealthService |Tato událost určí, jestli pracovní postup související s výkonem, událostí nebo jiným shromážděným datovým typem nemůže přesílat službě k ingestování do pracovního prostoru. | Událost s ID 2136 ze zdrojového HealthService je zapsána spolu s touto událostí a může značit, že Agent nemůže komunikovat se službou, pravděpodobně kvůli nepatřičné konfiguraci proxy serveru a nastavení ověřování, výpadku sítě nebo síťové brány firewall/proxy serveru nepovoluje provoz TCP z počítače do služby.| 
    |10102 a 10103 |Moduly Health Service |Pracovní postup nemohl vyřešit zdroj dat. |Tato situace může nastat, pokud zadaný čítač výkonu nebo instance v počítači neexistují nebo jsou nesprávně definovány v nastavení dat pracovního prostoru. Pokud se jedná o [čítač výkonu](data-sources-performance-counters.md#configuring-performance-counters)zadaný uživatelem, ověřte, že zadané informace jsou následující ve správném formátu a existují v cílových počítačích. |
    |26002 |Moduly Health Service |Pracovní postup nemohl vyřešit zdroj dat. |Tato situace může nastat, pokud zadaný protokol událostí systému Windows v počítači neexistuje. Tuto chybu můžete bezpečně ignorovat, pokud se neočekává, že bude tento protokol událostí zaregistrován, jinak pokud se jedná o [protokol událostí](data-sources-windows-events.md#configuring-windows-event-logs)zadaný uživatelem, ověřte, jestli jsou zadané informace správné. |
