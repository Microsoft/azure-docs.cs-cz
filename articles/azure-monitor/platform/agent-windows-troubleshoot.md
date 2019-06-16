---
title: Řešení potíží pomocí agenta Log Analytics pro Windows | Dokumentace Microsoftu
description: Popište příznaky, příčiny a řešení běžných problémů pomocí agenta Log Analytics pro Windows ve službě Azure Monitor.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/12/2019
ms.author: magoedte
ms.openlocfilehash: afa4483677336e9a887908a8cccf9590eed27af3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67120107"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-windows"></a>Řešení potíží pomocí agenta Log Analytics pro Windows 

Tento článek poskytuje nápovědu řešení potíží s chybami setkat s agenta Log Analytics pro Windows ve službě Azure Monitor a navrhne řešení k jejich řešení.

Pokud žádný z těchto kroků pro vás nejvhodnější, jsou také k dispozici následující kanály podpory:

* Výhody v oblasti podpory pro zákazníky v rámci podpory Premier můžou otevřít žádost o podporu s [Premier](https://premier.microsoft.com/).
* Zákazníci s smlouvy o podpoře Azure můžou otevřít žádost o podporu [na webu Azure Portal](https://manage.windowsazure.com/?getsupport=true).
* Na stránce Log Analytics zpětnou vazbu ke kontrole odeslané nápady a chyby [ https://aka.ms/opinsightsfeedback ](https://aka.ms/opinsightsfeedback) nebo soubor nový. 

## <a name="important-troubleshooting-sources"></a>Důležité zdroje pro řešení potíží

 Jako pomoc při řešení potíží vztahujících se k agentovi Log Analytics pro Windows, agent protokoluje události do protokolu událostí Windows konkrétně v části *aplikací a správce Services\Operations*.  

## <a name="connectivity-issues"></a>Problémy s připojením

Pokud je agent komunikaci prostřednictvím serveru proxy nebo brány firewall, může být omezení na místě, které znemožňuje komunikaci ze zdrojového počítače a služby Azure Monitor. Pokud je komunikace blokovaná, chybné konfiguraci, registrace s pracovním prostorem může selhat při pokusu o instalaci agenta, konfigurace po instalaci agenta pro hlášení další pracovní prostor, nebo po úspěšném dokončení registrace se nezdaří komunikace agenta. Tato část popisuje metody k řešení potíží s tímto typem problémů s agentem Windows. 

Překontrolujte, že brána firewall nebo proxy server nakonfigurována, aby umožňovala následující porty a adresy URL popisované v následující tabulce. Také potvrďte, že kontrola HTTP není povolena pro webový provoz, jak může zabránit zabezpečený kanál TLS mezi agentem a Azure Monitor.  

|Prostředek agenta|Porty |Směr |Obejít kontrolu protokolu HTTPS|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Port 443 |Odchozí|Ano |  
|*.oms.opinsights.azure.com |Port 443 |Odchozí|Ano |  
|*.blob.core.windows.net |Port 443 |Odchozí|Ano |  
|*.azure-automation.net |Port 443 |Odchozí|Ano |  

Informace o bráně firewall vyžadované pro Azure Government, najdete v části [správy Azure Government](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). 

Existuje několik způsobů, jak můžete ověřit, pokud je agent úspěšně komunikuje s Azure Monitor.

- Povolit [hodnocení Azure Log Analytics agenta stavu](../insights/solution-agenthealth.md) v pracovním prostoru. Zobrazit na řídicím panelu Stav agenta **počet nereagujících agentů** sloupec, který se krátce zobrazit, pokud je agent uveden.  

- Spusťte následující dotaz, abychom potvrdili, že agenta k pracovnímu prostoru, který je nakonfigurovaný k ukládání dat do odesílá prezenční signál. Nahraďte <ComputerName> skutečným názvem počítače.

    ```
    Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer 
    ```

    Pokud počítač úspěšně komunikuje se službou, by dotaz vrátit výsledek. Pokud dotaz nevrátí výsledek, nejdřív ověřte, zda že je agent nakonfigurovaný k ukládání dat do správného pracovního prostoru. Pokud je nakonfigurovaný správně, přejděte ke kroku 3 a hledání v protokolu událostí Windows a zjistěte, jestli je agent protokolování, s jakým problémem může být brání jeho komunikaci se službou Azure Monitor.

- Jinou metodou identifikovat problém s připojením je spuštěním **TestCloudConnectivity** nástroj. Nástroj je nainstalovaný ve výchozím nastavení s agentem ve složce *%SystemRoot%\Program Files\Microsoft Monitoring Agent\Agent*. Z příkazového řádku se zvýšenými oprávněními přejděte do složky a spusťte nástroj. Nástroj vrátí výsledky a vybraná vystoupení, kde se test nezdařil (například pokud byl souvisejících s konkrétní port/adresy URL, který se zablokoval). 

    ![Výsledky spuštění nástroje TestCloudConnection](./media/agent-windows-troubleshoot/output-testcloudconnection-tool-01.png)

- Filtr *nástroje Operations Manager* protokolu událostí podle **zdroje událostí** - *modulů služby stavu*, *HealthService*, a *Konektoru služby* a filtrovat podle **úroveň události** *upozornění* a *chyba* pro potvrzení, pokud má zapisovat události z Následující tabulka. Pokud jsou, přečtěte si postup řešení, které jsou zahrnuté pro všechny možné události.

    |ID události |source |Popis |Řešení |
    |---------|-------|------------|-----------|
    |2133 & 2129 |Služba Health Service |Připojení ke službě z agenta se nezdařila |Této chybě může dojít, pokud agent nemůže komunikovat přímo nebo prostřednictvím brány firewall a proxy serveru do služby Azure Monitor. Ověřte nastavení proxy serveru agenta nebo, že brány firewall nebo proxy server sítě povoluje provoz TCP z počítače do služby.|
    |2138 |Modulů služby stavu. |Proxy server vyžaduje ověření |Konfigurace nastavení proxy serveru agenta a zadejte uživatelské jméno/heslo potřebné k ověření proxy serveru. |
    |2129 |Modulů služby stavu. |Nezdařilo se připojení/nevyhověl vyjednávání protokolu SSL |Zkontrolujte nastavení protokolu TCP/IP síťového adaptéru a nastavení serveru proxy agenta.|
    |2127 |Modulů služby stavu. |Chyba odesílání dat přijatých kód chyby: |Pokud se stane pouze pravidelně během dne, je možné pouze náhodné anomálií, které můžete ignorovat. Monitorování a pochopení, jak často se stane. Pokud se to stává často v průběhu dne, nejprve zkontrolujte konfiguraci sítě a nastavení proxy serveru. Pokud je poprvé, agent se pokusí o odeslání dat službě popis obsahuje kód chyby protokolu HTTP 404, bude zahrnovat 500 chybě s kódem vnitřní chybu 404. 404 znamená, že nebyl nalezen, což znamená, že se oblast úložiště pro nový pracovní prostor ještě zřizuje. Na další opakování se úspěšně zápisu dat do pracovního prostoru podle očekávání. Oprávnění nebo problém přihlašovacích údajů může znamenat chybu HTTP 403. Není k dispozici další informace ceně Chyba 403, které vám pomohou vyřešit problém.|
    |4000 |Konektoru Service Connector |Překlad názvů DNS se nezdařilo |Počítač nebylo možné přeložit adresu Internetu používá při odesílání dat do služby. To může být nastavení překladu DNS na váš počítač, nastavení proxy serveru nesprávné nebo možná o dočasný problém DNS u svého poskytovatele. Pokud dochází pravidelně, může být způsobena přechodný problém související se sítí.|
    |4001 |Konektoru Service Connector |Připojení ke službě se nezdařilo. |Této chybě může dojít, pokud agent nemůže komunikovat přímo nebo prostřednictvím brány firewall a proxy serveru do služby Azure Monitor. Ověřte nastavení proxy serveru agenta nebo, že brány firewall nebo proxy server sítě povoluje provoz TCP z počítače do služby.|
    |4002 |Konektoru Service Connector |Služba vrátila v reakci na dotaz stavový kód HTTP 403. Zeptejte se správcem služby stav služby. Dotaz se zopakuje později. |Tato chyba je zapsán v průběhu fáze počáteční registraci agenta a zobrazí se vám podobná následující adresy URL: *https://<workspaceID>.oms.opinsights.azure.com/AgentService.svc/AgentTopologyRequest*. Chybu kód 403 znamená, že je zakázáno a může být způsobena chybným zadáním ID pracovního prostoru nebo klíč, nebo data a času je nesprávné v počítači. Pokud je doba +/-15 minut od aktuálního času, registrace se nezdaří. Když to pokud chcete opravit, aktualizujte datum a časové pásmo počítače Windows.|

## <a name="data-collection-issues"></a>Problémy s kolekce dat

Po instalaci agenta a sestavy do jeho nakonfigurovaný pracovní prostor nebo pracovní prostory, může systém přestat přijetí konfigurace, shromažďování nebo předávání výkonu, protokolů nebo jiná data do služby v závislosti na tom, co je povolená a cílí na počítač. Je potřeba určit, jestli:

- Je konkrétní datový typ nebo všechna data, která není k dispozici v pracovním prostoru?
- Datový typ určený řešení nebo se musí zadat jako součást konfigurace shromažďování dat pracovního prostoru?
- Kolik počítačů se to týká? Je jeden nebo více počítačích odesílajících sestavy do pracovního prostoru?
- Byla práce a zastavit v určitou denní dobu nebo ho nebylo nikdy shromážděných? 
- Je syntakticky správný z dotazu prohledávání protokolu, které používáte? 
- Agent někdy přijal konfiguraci ze služby Azure Monitor?

Prvním krokem při řešení potíží je určit, pokud počítač odesílá události prezenčního signálu.

```
Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer
```

Pokud dotaz vrací výsledky, budete muset zjistit, zda konkrétní datový typ není shromažďovat a předávat do služby. Důvodem může být agent nepřijímá aktualizovanou konfiguraci ze služby nebo některé jiné příznaky, které brání normálně agenta. Proveďte následující kroky, chcete-li dále řešit.

1. Otevřete příkazový řádek se zvýšenými oprávněními v počítači a restartujte službu agenta zadáním `net stop healthservice && net start healthservice`.
2. Otevřít *nástroje Operations Manager* protokolu událostí a vyhledejte **ID událostí** *7023, 7024 7025, 7028* a *1210* z **událostí Zdroj** *HealthService*.  Tyto události značí agenta úspěšně přijímá konfiguraci ze služby Azure Monitor a jsou počítač aktivně monitorovat. Popis události pro událost, kterou zadejte ID 1210 také na poslední řádek všech řešení a přehledy, které jsou zahrnuty v oboru monitorování v agentovi.  

    ![Popis události. ID 1210](./media/agent-windows-troubleshoot/event-id-1210-healthservice-01.png)

3. Pokud po několika minutách se nezobrazí ve výsledcích dotazu nebo vizualizaci očekávaná data, podle toho, pokud zobrazujete data z řešení nebo získávání informací z *nástroje Operations Manager* protokolu událostí, vyhledejte **událostí zdroje** *HealthService* a *modulů služby stavu* a filtrovat podle **úroveň události** *upozornění* a *Chyba* pro potvrzení, pokud zapsala události z následující tabulky.

    |ID události |source |Popis |Řešení |
    |---------|-------|------------|
    |8000 |HealthService |Tato událost bude zadat, pokud pracovní postup souvisejících s výkonem, události nebo jiný typ dat shromážděných není schopen dál služba pro ingestování do pracovního prostoru. | ID události 2136 ze zdroje služby Health Service se zapíše společně s touto událostí a může znamenat, že agent není schopen komunikovat se službou, pravděpodobně z důvodu chybné konfigurace nastavení proxy serveru a ověřování, výpadek sítě nebo síťová brána firewall / proxy server nepovoluje provoz TCP z počítače do služby.| 
    |10102 a 10103 |Modulů služby stavu. |Zdroj dat se nepovedlo vyhodnotit pracovního postupu. |Tato situace může nastat, pokud zadaný čítač výkonu nebo instance v počítači neexistuje nebo je nesprávně definovaný v nastavení dat pracovního prostoru. Pokud je zadán uživatel [čítač výkonu](data-sources-performance-counters.md#configuring-performance-counters)ověřte informace zadané sleduje ve správném formátu a existuje v cílových počítačích. |
    |26002 |Modulů služby stavu. |Zdroj dat se nepovedlo vyhodnotit pracovního postupu. |Tato situace může nastat, pokud zadaný protokol událostí Windows v počítači neexistuje. Tuto chybu můžete bezpečně ignorovat, pokud počítač není má mít tento protokol událostí zaregistrované, v opačném případě pokud je zadán uživatel [protokolu událostí](data-sources-windows-events.md#configuring-windows-event-logs), zkontrolujte zadané informace jsou správné. |

