---
title: Poradce při potížích s agentem Log Analytics pro Windows
description: Popište příznaky, příčiny a řešení nejčastějších problémů s agentem Log Analytics pro Windows v Azure Monitoru.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/21/2019
ms.openlocfilehash: 4112555347ce1d718375fbab3f166c6f2f5deeaa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80333506"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-windows"></a>Řešení potíží s agentem Log Analytics pro Windows 

Tento článek poskytuje pomoc při řešení chyb, ke kterým může dojít s agentem Analýzy protokolů pro Windows ve službě Azure Monitor, a navrhuje možná řešení, která je vyřeší.

Pokud žádný z těchto kroků nefunguje, jsou k dispozici také následující kanály podpory:

* Zákazníci s výhodami podpory Premier mohou otevřít žádost o podporu pomocí [služby Premier](https://premier.microsoft.com/).
* Zákazníci se smlouvami o podpoře Azure můžou otevřít žádost o podporu [na webu Azure Portal](https://manage.windowsazure.com/?getsupport=true).
* Navštivte stránku Senáčse Log Analytics a prohlédněte si odeslané nápady a chyby [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback) nebo ponořte nové. 

## <a name="important-troubleshooting-sources"></a>Důležité zdroje řešení potíží

 Chcete-li pomoci při řešení potíží souvisejících s agentem Log Analytics pro systém Windows, agent protokoluje události do protokolu událostí systému Windows, konkrétně v části *Aplikace a služby\Operations Manager*.  

## <a name="connectivity-issues"></a>Problémy s připojením

Pokud agent komunikuje prostřednictvím proxy serveru nebo brány firewall, může existovat omezení na místě brání komunikaci ze zdrojového počítače a služby Azure Monitor. V případě, že je komunikace blokována z důvodu chybné konfigurace, může dojít k selhání registrace s pracovním prostorem při pokusu o instalaci agenta nebo konfiguraci agenta po instalaci tak, aby se hlásil do dalšího pracovního prostoru. Komunikace agenta může selhat po úspěšné registraci. Tato část popisuje metody řešení tohoto typu problému s agentem systému Windows.

Zkontrolujte, zda je brána firewall nebo proxy server nakonfigurován tak, aby umožňoval následující porty a adresy URL popsané v následující tabulce. Také zkontrolujte http není povolena pro webový provoz, protože může zabránit zabezpečenému kanálu TLS mezi agentem a Azure Monitor.  

|Prostředek agenta|Porty |Směr |Obejít kontrolu protokolu HTTPS|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Přístav 443 |Odchozí|Ano |  
|*.oms.opinsights.azure.com |Přístav 443 |Odchozí|Ano |  
|*.blob.core.windows.net |Přístav 443 |Odchozí|Ano |  

Informace o bráně firewall požadované pro Azure Government najdete v [tématu Správa Azure Government](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). Pokud plánujete použít pracovník hybridní sady Runbook Azure Automation pro připojení a registraci ke službě Automation a k použití runbooků nebo řešení pro správu ve vašem prostředí, musí mít přístup k číslu portu a adresám URL popsaným v [části Konfigurace sítě pro pracovníka hybridní sady Runbook](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

Existuje několik způsobů, jak můžete ověřit, pokud agent úspěšně komunikuje s Azure Monitor.

- Povolte [posouzení stavu agenta Azure Log Analytics](../insights/solution-agenthealth.md) v pracovním prostoru. Na řídicím panelu Stav agenta zobrazte sloupec **Počet nereagujících agentů** a rychle zjistěte, jestli je agent v seznamu.  

- Spusťte následující dotaz a potvrďte, že agent odesílá prezenční signál do pracovního prostoru, do který je nakonfigurován. Nahraďte `<ComputerName>` skutečným názvem zařízení.

    ```
    Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer 
    ```

    Pokud počítač úspěšně komunikuje se službou, dotaz by měl vrátit výsledek. Pokud dotaz nevrátil výsledek, nejprve ověřte, zda je agent nakonfigurován tak, aby byl podaný do správného pracovního prostoru. Pokud je správně nakonfigurován, přejděte ke kroku 3 a vyhledejte protokol událostí systému Windows a zjistěte, zda agent zaznamenává, jaký problém mu může bránit v komunikaci s Azure Monitorem.

- Další metodou k identifikaci problému s připojením je spuštění nástroje **TestCloudConnectivity.** Nástroj je ve výchozím nastavení nainstalován s agentem ve složce *%SystemRoot%\Program Files\Microsoft Monitoring Agent\Agent*. Z příkazového řádku se zvýšenými oprávněními přejděte do složky a spusťte nástroj. Nástroj vrátí výsledky a zvýrazní, kde se test nezdařil (například pokud souvisí s určitým portem/adresou URL, která byla zablokována). 

    ![Výsledky spuštění nástroje TestCloudConnection](./media/agent-windows-troubleshoot/output-testcloudconnection-tool-01.png)

- Filtrujte protokol událostí *nástroje Operations Manager* podle **modulů** - *služby Health Service*zdrojů událostí , *healthservice*a *servisního konektoru* a filtrujte podle *upozornění* a *chyby* **na úrovni události,** abyste potvrdili, zda byly události zapsány z následující tabulky. Pokud ano, zkontrolujte kroky řešení zahrnuté pro každou možnou událost.

    |ID události |Zdroj |Popis |Řešení |
    |---------|-------|------------|-----------|
    |2133 & 2129 |Služba Health Service |Připojení ke službě od agenta se nezdařilo. |K této chybě může dojít, když agent nemůže komunikovat přímo nebo prostřednictvím serveru firewall/proxy se službou Azure Monitor. Ověřte nastavení serveru proxy agenta nebo ověřte, zda síťová brána firewall/proxy umožňuje přenos protokolu TCP z počítače do služby.|
    |2138 |Moduly zdravotnických služeb |Proxy server vyžaduje ověření. |Nakonfigurujte nastavení proxy agenta a zadejte uživatelské jméno nebo heslo potřebné k ověření u proxy serveru. |
    |2129 |Moduly zdravotnických služeb |Neúspěšné vyjednávání připojení/neúspěšných tls |Zkontrolujte nastavení protokolu TCP/IP síťového adaptéru a nastavení proxy serveru agenta.|
    |2 127 |Moduly zdravotnických služeb |Chyba odesílání dat přijatých kód chyby |Pokud se to děje pouze pravidelně během dne, může to být jen náhodná anomálie, která může být ignorována. Sledujte, abyste pochopili, jak často se to stane. Pokud se to stane často po celý den, nejprve zkontrolujte konfiguraci sítě a nastavení proxy serveru. Pokud popis obsahuje kód chyby HTTP 404 a je to poprvé, kdy se agent pokusí odeslat data do služby, bude obsahovat chybu 500 s vnitřním kódem chyby 404. 404 znamená, že nebyl nalezen, což znamená, že prostor úložiště pro nový pracovní prostor je stále zřizován. Při dalším opakování budou data úspěšně zapisovat do pracovního prostoru podle očekávání. Chyba protokolu HTTP 403 může znamenat problém s oprávněním nebo pověřením. K dispozici jsou další informace obsažené v chybě 403, které vám pomohou problém vyřešit.|
    |4000 |Servisní konektor |Překlad názvů DNS se nezdařil. |Počítač nemohl vyřešit internetovou adresu použitou při odesílání dat do služby. Může se jedná o nastavení překladače DNS v počítači, nesprávné nastavení proxy serveru nebo možná dočasný problém dns s poskytovatelem. Pokud k tomu dochází pravidelně, může to být způsobeno přechodným problémem souvisejícím se sítí.|
    |4001 |Servisní konektor |Připojení ke službě se nezdařilo. |K této chybě může dojít, když agent nemůže komunikovat přímo nebo prostřednictvím serveru firewall/proxy se službou Azure Monitor. Ověřte nastavení serveru proxy agenta nebo ověřte, zda síťová brána firewall/proxy umožňuje přenos protokolu TCP z počítače do služby.|
    |4002 |Servisní konektor |Služba vrátila stavový kód HTTP 403 jako odpověď na dotaz. Stav služby se obraťte na správce služby. Dotaz bude opakován později. |Tato chyba je zapsána během počáteční fáze registrace agenta a zobrazí se adresa URL podobná následující: *https://\<workspaceID>.oms.opinsights.azure.com/AgentService.svc/AgentTopologyRequest*. Kód chyby 403 znamená zakázáno a může být způsobeno chybně zadané ID pracovního prostoru nebo klíč, nebo data a čas je nesprávná v počítači. Pokud je čas +/- 15 minut od aktuálního času, pak onboarding selže. Chcete-li tento problém opravit, aktualizujte datum nebo časové pásmo počítače se systémem Windows.|

## <a name="data-collection-issues"></a>Problémy se shromažďováním dat

Po instalaci agenta a podají se do nakonfigurovaného pracovního prostoru nebo pracovních prostorů, může zastavit příjem konfigurace, shromažďování nebo předávání výkonu, protokolů nebo jiných dat do služby v závislosti na tom, co je povoleno a cílení na počítač. Je nutné určit, zda:

- Jedná se o určitý datový typ nebo všechna data, která nejsou v pracovním prostoru k dispozici?
- Je datový typ určen řešením nebo zadán jako součást konfigurace shromažďování dat pracovního prostoru?
- Kolik počítačů je ovlivněno? Je to jeden nebo více počítačů, které se hlásí do pracovního prostoru?
- Fungovalo to a zastavilo se to v určitou denní dobu, nebo to nikdy nebylo shromážděno? 
- Je vyhledávací dotaz protokolu, který používáte syntakticky správné? 
- Obdržel agent někdy svou konfiguraci z Azure Monitoru?

Prvním krokem při řešení potíží je zjistit, zda počítač odesílá událost prezenčního signálu.

```
Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer
```

Pokud dotaz vrátí výsledky, je třeba určit, pokud konkrétní datový typ není shromažďována a předána službě. To může být způsobeno agentnem, který nepřijímá aktualizovanou konfiguraci ze služby, nebo jiným příznakem, který agentovi brání v normálním provozu. K dalšímu řešení potíží proveďte následující kroky.

1. Otevřete v počítači příkazový řádek se zvýšenými `net stop healthservice && net start healthservice`oprávněními a restartujte službu agenta zadáním příkazu .
2. Otevřete protokol událostí *nástroje Operations Manager* a vyhledejte **ID událostí** *7023, 7024, 7025, 7028* a *1210* ze **zdroje událostí** *HealthService*.  Tyto události označují, že agent úspěšně přijímá konfiguraci z Azure Monitoru a aktivně monitoruje počítač. Popis události pro ID události 1210 také určí na posledním řádku všechna řešení a přehledy, které jsou zahrnuty v rozsahu monitorování na agenta.  

    ![Popis ID události 1210](./media/agent-windows-troubleshoot/event-id-1210-healthservice-01.png)

3. Pokud po několika minutách nevidíte očekávaná data ve výsledcích dotazu nebo vizualizaci, v závislosti na tom, zda prohlížíte data z řešení nebo insight, z protokolu událostí *Operations Manager,* vyhledejte **zdroje událostí** *HealthService* a *moduly služby Health Service* a filtrujte podle *upozornění* na úrovni **události** a *chyby,* abyste potvrdili, zda byly zapsány události z následující tabulky.

    |ID události |Zdroj |Popis |Řešení |
    |---------|-------|------------|
    |8000 |Služba HealthService |Tato událost určí, zda pracovní postup související s výkonem, událostí nebo jiným shromážděným typem dat nemůže předat službu pro ingestování do pracovního prostoru. | ID události 2136 ze zdroje HealthService je napsánspolečně s touto událostí a může znamenat, že agent není schopen komunikovat se službou, pravděpodobně z důvodu chybné konfigurace proxy a nastavení ověřování, výpadku sítě nebo síťové brány firewall/proxy neumožňuje přenos protokolu TCP z počítače do služby.| 
    |10102 a 10103 |Moduly zdravotnických služeb |Pracovní postup nemohl přeložit zdroj dat. |Tato situace může nastat, pokud zadaný čítač výkonu nebo instance v počítači neexistuje nebo je nesprávně definována v nastavení dat pracovního prostoru. Pokud se jedná o [čítač výkonu](data-sources-performance-counters.md#configuring-performance-counters)zadaný uživatelem , ověřte, zda zadané informace sledují správný formát a existují v cílových počítačích. |
    |26002 |Moduly zdravotnických služeb |Pracovní postup nemohl přeložit zdroj dat. |Tato situace může nastat, pokud zadaný protokol událostí systému Windows v počítači neexistuje. Tato chyba může být bezpečně ignorována, pokud se neočekává, že počítač bude mít tento protokol událostí registrován, jinak pokud se jedná o [protokol událostí](data-sources-windows-events.md#configuring-windows-event-logs)zadaný uživatelem , ověřte, zda jsou zadané informace správné. |

