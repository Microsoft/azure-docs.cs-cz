---
title: Doba přihlašování dat v Azure Monitoru | Dokumenty společnosti Microsoft
description: Vysvětluje různé faktory, které ovlivňují latenci při shromažďování dat protokolu v Azure Monitoru.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/18/2019
ms.openlocfilehash: 99d5594dd3ebe3750cb0a09ea803065e2aeb5ba2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666633"
---
# <a name="log-data-ingestion-time-in-azure-monitor"></a>Protokolování času pro příjem dat ve službě Azure Monitor
Azure Monitor je datová služba ve velkém měřítku, která poskytuje tisíce zákazníků odesílajících každý měsíc terabajty dat rostoucím tempem. Často existují otázky o době, která trvá pro data protokolu k dispozici po jejich shromáždění. Tento článek vysvětluje různé faktory, které ovlivňují tuto latenci.

## <a name="typical-latency"></a>Typická latence
Latence označuje čas, který se vytvoří data ve sledovaném systému a čas, který je k dispozici pro analýzu v Azure Monitoru. Typická latence pro ingestování dat protokolu je mezi 2 a 5 minut. Konkrétní latence pro konkrétní data se bude lišit v závislosti na různých faktorech vysvětlených níže.


## <a name="factors-affecting-latency"></a>Faktory ovlivňující latenci
Celkovou dobu požití pro určitou sadu dat lze rozdělit do následujících oblastí vysoké úrovně. 

- Agent čas – čas zjistit událost, shromažďovat a pak ji odeslat do bodu ingestování Azure Monitor jako záznam protokolu. Ve většině případů je tento proces zpracován agentem.
- Čas kanálu – doba, kterou kanálu příjmu dat trvá zpracovat záznam protokolu. To zahrnuje analýzu vlastností události a potenciálně přidání vypočítaných informací.
- Doba indexování – čas strávený k ingestování záznamu protokolu do úložiště velkých objemů dat Azure Monitor.

Podrobnosti o různé latence zavedené v tomto procesu jsou popsány níže.

### <a name="agent-collection-latency"></a>Latence shromažďování agenta
Agenti a řešení pro správu používají různé strategie ke shromažďování dat z virtuálního počítače, což může ovlivnit latenci. Některé konkrétní příklady zahrnují následující:

- Události systému Windows, události syslogu a metriky výkonu jsou shromažďovány okamžitě. Čítače výkonu Linuxu jsou dotazovány v intervalech 30 sekund.
- Protokoly iis a vlastní protokoly jsou shromažďovány po změně časového razítka. U protokolů iis je to ovlivněno [plánem přechodu nakonfigurovaným ve službě IIS](data-sources-iis-logs.md). 
- Řešení služby AD Replikace provádí své hodnocení každých pět dní, zatímco řešení vyhodnocení služby Active Directory provádí týdenní hodnocení infrastruktury služby Active Directory. Agent bude shromažďovat tyto protokoly pouze po dokončení posouzení.

### <a name="agent-upload-frequency"></a>Frekvence nahrávání agenta
Chcete-li zajistit, že agent Log Analytics je lehký, agent vyrovnávací paměti protokoly a pravidelně je odesílá do Azure Monitor. Frekvence nahrávání se pohybuje mezi 30 sekundami a 2 minutami v závislosti na typu dat. Většina dat je nahrána za méně než 1 minutu. Podmínky v síti může negativně ovlivnit latenci těchto dat k dosažení bodu přijetí Azure Monitor.

### <a name="azure-activity-logs-resource-logs-and-metrics"></a>Protokoly aktivit Azure, protokoly prostředků a metriky
Data Azure přidává další čas, aby byly k dispozici na přihlašovací bod Log Analytics pro zpracování:

- Data z protokolů prostředků trvat 2 až 15 minut, v závislosti na službě Azure. Podívejte se na [níže uvedený dotaz](#checking-ingestion-time) a prozkoumejte tuto latenci ve vašem prostředí
- Metriky platformy Azure trvat 3 minuty, které se odešlou do bodu příjmu Log Analytics.
- Data protokolu aktivit bude trvat asi 10-15 minut, které mají být odeslány do bodu příjmu Log Analytics.

Jakmile jsou k dispozici v bodě požití, data trvá další 2-5 minut k dispozici pro dotazování.

### <a name="management-solutions-collection"></a>Kolekce řešení pro správu
Některá řešení neshromažďují svá data od agenta a mohou používat metodu kolekce, která zavádí další latenci. Některá řešení shromažďují data v pravidelných intervalech bez pokusu o shromažďování v téměř reálném čase. Mezi konkrétní příklady patří následující:

- Řešení Office 365 dotazování protokoly aktivit pomocí rozhraní API pro aktivity správy Office 365, které aktuálně neposkytuje žádné záruky latence téměř v reálném čase.
- Data řešení Windows Analytics (například dodržování předpisů aktualizací) jsou shromažďována řešením na denní frekvenci.

Informace o četnosti sběru naleznete v dokumentaci ke každému řešení.

### <a name="pipeline-process-time"></a>Čas procesu potrubí
Jakmile se záznamy protokolu po požití do kanálu Azure Monitor (jak je uvedeno ve [vlastnosti _TimeReceived),](log-standard-properties.md#_timereceived) jsou zapsány do dočasného úložiště, aby byla zajištěna izolace tenanta a ujistěte se, že data nejsou ztracena. Tento proces obvykle přidá 5-15 sekund. Některá řešení pro správu implementují těžší algoritmy pro agregaci dat a odvození přehledů při streamování dat. Například monitorování výkonu sítě agreguje příchozí data v intervalech 3 minut, což efektivně přidává latenci 3 minuty. Dalším procesem, který přidává latenci je proces, který zpracovává vlastní protokoly. V některých případech tento proces může přidat několik minut latence protokoly, které jsou shromažďovány ze souborů agentem.

### <a name="new-custom-data-types-provisioning"></a>Nové vlastní datové typy zřizování
Při vytvoření nového typu vlastních dat z [vlastního protokolu](data-sources-custom-logs.md) nebo rozhraní API pro [sběr dat](data-collector-api.md)vytvoří systém vyhrazený kontejner úložiště. Toto je jednorázová režie, ke které dochází pouze při prvním výskytu tohoto datového typu.

### <a name="surge-protection"></a>Přepěťová ochrana
Nejvyšší prioritou Azure Monitoru je zajistit, aby nedošlo ke ztrátě žádných zákaznických dat, takže systém má integrovanou ochranu pro přepětí dat. To zahrnuje vyrovnávací paměti, které zajistí, že i při obrovském zatížení bude systém fungovat. Při normálním zatížení tyto ovládací prvky přidat méně než minutu, ale v extrémních podmínkách a selhání, které by mohly přidat významný čas při zajištění dat je bezpečné.

### <a name="indexing-time"></a>Čas indexování
Pro každou platformu pro velké objemy dat existuje integrovaná rovnováha mezi poskytováním analýz a pokročilými vyhledávacími funkcemi, na rozdíl od poskytování okamžitého přístupu k datům. Azure Monitor umožňuje spouštět výkonné dotazy na miliardy záznamů a získat výsledky během několika sekund. To je možné, protože infrastruktura transformuje data dramaticky během jeho požití a ukládá je v jedinečných kompaktních struktur. Systém uvězní data, dokud nebude k dispozici dostatek dat k vytvoření těchto struktur. To to musí být dokončeno před zobrazením záznamu protokolu ve výsledcích hledání.

Tento proces v současné době trvá asi 5 minut, pokud je nízký objem dat, ale méně času při vyšších přenosových rychlostech. To se zdá být neintuitivní, ale tento proces umožňuje optimalizaci latence pro velkoobjemové produkční úlohy.



## <a name="checking-ingestion-time"></a>Kontrola doby požití
Doba požití se může lišit pro různé zdroje za různých okolností. Dotazy protokolu můžete použít k identifikaci konkrétní chování vašeho prostředí. Následující tabulka určuje, jak můžete určit různé časy pro záznam, jak je vytvořen a odeslán do Azure Monitoru.

| Krok | Vlastnost nebo funkce | Komentáře |
|:---|:---|:---|
| Záznam vytvořený ve zdroji dat | [TimeGenerated](log-standard-properties.md#timegenerated-and-timestamp) <br>Pokud zdroj dat tuto hodnotu nenastaví, bude nastaven na stejný čas jako _TimeReceived. |
| Záznam přijatý koncovým bodem ingestování azure monitoru | [_TimeReceived](log-standard-properties.md#_timereceived) | |
| Záznam uložený v pracovním prostoru a dostupný pro dotazy | [ingestion_time()](/azure/kusto/query/ingestiontimefunction) | |

### <a name="ingestion-latency-delays"></a>Zpoždění latence požití
Latenci konkrétního záznamu můžete změřit porovnáním výsledku funkce [ingestion_time()](/azure/kusto/query/ingestiontimefunction) s vlastností _TimeGenerated._ Tato data lze použít s různými agregacemi k zjištění, jak se chová latence ingestování. Prozkoumejte některé percentily doby požití získat přehledy pro velké množství dat. 

Následující dotaz vám například ukáže, které počítače měly nejvyšší dobu požití za předchozích 8 hodin: 

``` Kusto
Heartbeat
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95), percentiles(AgentLatency,50,95) by Computer 
| top 20 by percentile_E2EIngestionLatency_95 desc
```

Předchozí kontroly percentilu jsou vhodné pro hledání obecných trendů v latenci. Chcete-li identifikovat krátkodobý nárůst latence, použití`max()`maximální ( ) může být efektivnější.

Pokud chcete přejít k podrobnostem o době přijetí pro konkrétní počítač po určitou dobu, použijte následující dotaz, který také vizualizuje data z minulého dne v grafu: 


``` Kusto
Heartbeat 
| where TimeGenerated > ago(24h) //and Computer == "ContosoWeb2-Linux"  
| extend E2EIngestionLatencyMin = todouble(datetime_diff("Second",ingestion_time(),TimeGenerated))/60 
| extend AgentLatencyMin = todouble(datetime_diff("Second",_TimeReceived,TimeGenerated))/60 
| summarize percentiles(E2EIngestionLatencyMin,50,95), percentiles(AgentLatencyMin,50,95) by bin(TimeGenerated,30m) 
| render timechart
```
 
Následující dotaz slouží k zobrazení času při holání počítače podle země nebo oblasti, ve které se nacházejí a ve které je založena jejich IP adresa: 

``` Kusto
Heartbeat 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95),percentiles(AgentLatency,50,95) by RemoteIPCountry 
```
 
Různé datové typy pocházející z agenta může mít různé latence ingestování čas, takže předchozí dotazy lze použít s jinými typy. Pomocí následujícího dotazu zkontrolujte čas přijetí různých služeb Azure: 

``` Kusto
AzureDiagnostics 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95), percentiles(AgentLatency,50,95) by ResourceProvider
```

### <a name="resources-that-stop-responding"></a>Zdroje, které přestanou reagovat 
V některých případech může prostředek zastavit odesílání dat. Chcete-li pochopit, zda zdroj odesílá data nebo ne, podívejte se na jeho nejnovější záznam, který lze identifikovat podle standardního pole _TimeGenerated._  

Pomocí tabulky _Prezenční signál_ zkontrolujte dostupnost virtuálního signálu, protože prezenční signál je odeslán jednou za minutu agentem. Pomocí následujícího dotazu můžete uvést aktivní počítače, které v poslední době nehlásily prezenční signál: 

``` Kusto
Heartbeat  
| where TimeGenerated > ago(1d) //show only VMs that were active in the last day 
| summarize NoHeartbeatPeriod = now() - max(TimeGenerated) by Computer  
| top 20 by NoHeartbeatPeriod desc 
```

## <a name="next-steps"></a>Další kroky
* Přečtěte si [smlouvu o úrovni služeb (SLA)](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_1/) pro Azure Monitor.

