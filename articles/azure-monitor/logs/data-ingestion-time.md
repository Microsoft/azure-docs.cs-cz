---
title: Doba přijímání dat protokolu v Azure Monitor | Microsoft Docs
description: Vysvětluje různé faktory, které mají vliv na latenci při shromažďování dat protokolu v Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/18/2019
ms.openlocfilehash: 49122421f04ee6eef8828ca305cfb235aceee3fb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105035689"
---
# <a name="log-data-ingestion-time-in-azure-monitor"></a>Protokolování času pro příjem dat ve službě Azure Monitor
Azure Monitor je služba data ve velkém měřítku, která slouží tisícům zákazníků, kteří každý měsíc odesílají terabajty dat při rostoucím tempu. K dispozici jsou často dotazy týkající se času, po který se data protokolu budou k dispozici po shromáždění. Tento článek vysvětluje různé faktory, které mají vliv na tuto latenci.

## <a name="typical-latency"></a>Typická latence
Latence odkazuje na čas, kdy jsou data v monitorovaném systému vytvořena, a čas, který je k dispozici pro analýzu v Azure Monitor. Obvyklá latence k ingestování dat protokolu je mezi 20 sec a 3 minutami. Konkrétní latence pro konkrétní data se ale liší v závislosti na nejrůznějších faktorech, které jsou vysvětleny níže.


## <a name="factors-affecting-latency"></a>Faktory ovlivňující latenci
Celková doba příjmu konkrétní sady dat může být rozdělena do následujících oblastí vysoké úrovně. 

- Čas agenta – čas k odhalení události, její shromáždění a odeslání do Azure Monitor protokol ingestování protokolů jako záznam protokolu. Ve většině případů tento proces zpracovává agent. V síti může být zavedena další latence.
- Čas kanálu – doba, kterou kanálu příjmu dat trvá zpracovat záznam protokolu. To zahrnuje analýzu vlastností události a potenciálně Přidání počítaných informací.
- Čas indexování – čas strávený přijímáním záznamu protokolu do Azure Monitor velkých objemů dat.

Podrobnosti o různých latencích zavedených v tomto procesu jsou popsány níže.

### <a name="agent-collection-latency"></a>Latence shromažďování agenta
Agenti a řešení pro správu používají různé strategie ke shromažďování dat z virtuálního počítače. to může mít vliv na latenci. Mezi konkrétní příklady patří následující:

- Události Windows, události syslog a metriky výkonu se shromažďují hned. Čítače výkonu pro Linux se dotazují v intervalu 30 sekund.
- Protokoly služby IIS a vlastní protokoly jsou shromažďovány po změně jejich časového razítka. V případě protokolů služby IIS je to ovlivněno [plánem změny nastaveným ve službě IIS](../agents/data-sources-iis-logs.md). 
- Řešení replikace Active Directory provádí posouzení každých pět dní, zatímco Active Directory Assessment řešení provádí týdenní hodnocení infrastruktury služby Active Directory. Agent bude tyto protokoly shromažďovat až po dokončení posouzení.

### <a name="agent-upload-frequency"></a>Frekvence nahrávání agenta
Chcete-li zajistit, aby byl agent Log Analytics odlehčený, Agent ukládá protokoly do vyrovnávací paměti a pravidelně je odesílá do Azure Monitor. Frekvence nahrávání se v závislosti na typu dat liší v rozmezí 30 sekund a 2 minut. Většina dat se nahrává za 1 minutu. 

### <a name="network"></a>Síť
Síťové podmínky můžou negativně ovlivnit latenci těchto dat, aby se dosáhlo Azure Monitor protokolu pro přijímání zpráv.

### <a name="azure-activity-logs-resource-logs-and-metrics"></a>Protokoly aktivit Azure, protokoly prostředků a metriky
Data Azure přidávají další čas, který je k dispozici na Azure Monitor protokoluje bod pro přijímání zpráv pro zpracování:

- Protokoly prostředků obvykle přidávají 30-90 sekund v závislosti na službě Azure. Některé služby Azure (konkrétně Azure SQL Database a Azure Virtual Network) aktuálně hlásí své protokoly v intervalech 5 minut. Práce se ještě nezvyšuje. Podívejte se na [následující dotaz](#checking-ingestion-time) a prověřte tuto latenci ve vašem prostředí.
- Metriky platformy Azure ještě déle 3 minuty budou exportovány do Azure Monitor protokoluje bod pro přijímání zpráv.
- Pokud se používá starší verze integrace, data protokolu aktivit můžou trvat dalších 10-15 minut. Doporučujeme použít nastavení diagnostiky na úrovni předplatného k ingestování protokolů aktivit do protokolů Azure Monitor, což způsobí další latenci přibližně 30 sekund.

### <a name="management-solutions-collection"></a>Kolekce řešení pro správu
Některá řešení neshromažďují svá data z agenta a můžou používat metodu shromažďování, která zavádí další latenci. Některá řešení shromažďují data v pravidelných intervalech bez pokusu o shromažďování téměř v reálném čase. Konkrétní příklady zahrnují následující:

- Microsoft 365 řešení pro dotazování v protokolech aktivit pomocí rozhraní API pro správu aktivit, které v současné době neposkytuje záruky za téměř reálné časové prodlevy.
- Řešení Windows Analytics (například Update Compliance) se shromažďují podle každodenní frekvence.

Chcete-li zjistit četnost shromažďování dat, přečtěte si dokumentaci pro každé řešení.

### <a name="pipeline-process-time"></a>Kanál – čas procesu

Jakmile budou data v bodu příjmu k dispozici, budou k dispozici další 30-60 sekund pro dotazování.

Po ingestování záznamů protokolu do kanálu Azure Monitor (jak je identifikované vlastností [_TimeReceived](./log-standard-columns.md#_timereceived) ) se zapisují do dočasného úložiště, které zajistí izolaci tenanta a zajistěte, aby se data neztratila. Tento proces obvykle přidává 5-15 sekund. Některá řešení pro správu implementují těžší algoritmy pro agregaci dat a odvozují přehledy při streamování dat. Například monitorování výkonu sítě agreguje příchozí data v intervalech po dobu tří minut a efektivně přidá latenci na 3 minuty. Dalším procesem, který přidává latenci, je proces, který zpracovává vlastní protokoly. V některých případech může tento proces přidat několik minut latence do protokolů shromažďovaných ze souborů agentem.

### <a name="new-custom-data-types-provisioning"></a>Nové zřizování vlastních datových typů
Když se vytvoří nový typ vlastních dat z [vlastního protokolu](../agents/data-sources-custom-logs.md) nebo [rozhraní API kolekce dat](../logs/data-collector-api.md), systém vytvoří vyhrazený kontejner úložiště. Jedná se o jednorázovou režii, ke které dochází pouze při prvním výskytu daného datového typu.

### <a name="surge-protection"></a>Ochrana proti přepětí
Nejvyšší prioritou Azure Monitor je zajistit, že se neztratí žádná zákaznická data, takže systém má vestavěnou ochranu proti nárůstu dat. To zahrnuje vyrovnávací paměti, aby se zajistilo, že i v případě obrovské zátěže systém zůstane funkční. V rámci normálního zatížení tyto ovládací prvky přidávají méně než minutu, ale v extrémních podmínkách a selháních by mohly přidat značnou dobu, zatímco data jsou bezpečná.

### <a name="indexing-time"></a>Čas indexování
Pro každou platformu pro velké objemy dat je k dispozici předdefinovaná bilance, která poskytuje analytické a rozšířené možnosti vyhledávání na rozdíl od poskytování okamžitého přístupu k datům. Azure Monitor umožňuje spouštět výkonné dotazy na miliardy záznamů a získat výsledky během několika sekund. To je možné, protože infrastruktura transformuje data významně během příjmu a ukládá je v jedinečných kompaktních strukturách. Systém ukládá data do vyrovnávací paměti, dokud není k dispozici dostatek pro vytvoření těchto struktur. To je nutné provést před zobrazením záznamu protokolu ve výsledcích hledání.

Tento proces v současné době trvá přibližně 5 minut, když dojde k malým objemům dat, ale méně času při vyšších sazbách dat. To se jeví jako neintuitivní, ale tento proces umožňuje optimalizaci latence pro úlohy s vysokými objemy produkce.



## <a name="checking-ingestion-time"></a>Kontroluje se doba přijímání.
Doba příjmu se může u různých prostředků v různých případech lišit. Pomocí dotazů protokolu můžete identifikovat konkrétní chování vašeho prostředí. Následující tabulka určuje, jak můžete určit různé časy pro záznam při jeho vytvoření a odeslání do Azure Monitor.

| Krok | Vlastnost nebo funkce | Komentáře |
|:---|:---|:---|
| Záznam vytvořený ve zdroji dat | [TimeGenerated](./log-standard-columns.md#timegenerated-and-timestamp) <br>Pokud zdroj dat tuto hodnotu nenastaví, bude nastaven na stejný čas jako _TimeReceived. |
| Záznam přijatý Azure Monitor koncový bod pro ingestování | [_TimeReceived](./log-standard-columns.md#_timereceived) | Toto pole není optimalizované pro hromadné zpracování a nemělo by se používat k filtrování velkých datových sad. |
| Záznam uložený v pracovním prostoru a dostupný pro dotazy | [ingestion_time ()](/azure/kusto/query/ingestiontimefunction) | Pokud je potřeba filtrovat jenom záznamy, které se v určitém časovém intervalu ingestují, doporučuje se použít ingestion_time (). V takovém případě doporučujeme přidat také filtr TimeGenerated s větším rozsahem. |

### <a name="ingestion-latency-delays"></a>Zpoždění latence přijímání
Můžete změřit latenci konkrétního záznamu porovnáním výsledku funkce [ingestion_time ()](/azure/kusto/query/ingestiontimefunction) s vlastností _TimeGenerated_ . Tato data je možné použít s různými agregacemi k nalezení, jak se latence příjmu chová. Prověřte si určitý percentil doby příjmu, abyste získali přehled o velkém množství dat. 

Následující dotaz například zobrazí, které počítače měly nejvyšší čas příjmu za předchozích 8 hodin: 

``` Kusto
Heartbeat
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95), percentiles(AgentLatency,50,95) by Computer 
| top 20 by percentile_E2EIngestionLatency_95 desc
```

Předchozí kontroly percentilu jsou vhodné pro hledání obecných trendů v latenci. Pro identifikaci krátkodobého špičky v latenci může být použití maxima ( `max()` ) efektivnější.

Chcete-li přejít k podrobnostem o době příjmu konkrétního počítače v časovém intervalu, použijte následující dotaz, který také vizualizuje data z minulého dne v grafu: 


``` Kusto
Heartbeat 
| where TimeGenerated > ago(24h) //and Computer == "ContosoWeb2-Linux"  
| extend E2EIngestionLatencyMin = todouble(datetime_diff("Second",ingestion_time(),TimeGenerated))/60 
| extend AgentLatencyMin = todouble(datetime_diff("Second",_TimeReceived,TimeGenerated))/60 
| summarize percentiles(E2EIngestionLatencyMin,50,95), percentiles(AgentLatencyMin,50,95) by bin(TimeGenerated,30m) 
| render timechart
```
 
Pomocí následujícího dotazu Zobrazte čas ingestování počítačů podle země nebo oblasti, ve které se nacházejí, na základě jejich IP adresy: 

``` Kusto
Heartbeat 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95),percentiles(AgentLatency,50,95) by RemoteIPCountry 
```
 
Různým datovým typům, které pocházejí od agenta, může být čekací doba příjmu, takže předchozí dotazy lze použít s jinými typy. Pomocí následujícího dotazu prověřte dobu příjmu různých služeb Azure: 

``` Kusto
AzureDiagnostics 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95), percentiles(AgentLatency,50,95) by ResourceProvider
```

### <a name="resources-that-stop-responding"></a>Prostředky, které přestanou reagovat 
V některých případech může prostředek přestat odesílat data. Pokud chcete zjistit, jestli prostředek odesílá data, nebo ne, podívejte se na jeho poslední záznam, který se dá identifikovat standardním polem _TimeGenerated_ .  

Pomocí tabulky _prezenčního signálu_ Ověřte dostupnost virtuálního počítače, protože Agent odesílá prezenční signál jednou za minutu. Následující dotaz použijte k vypsání aktivních počítačů, které v poslední době neohlásily prezenční signál: 

``` Kusto
Heartbeat  
| where TimeGenerated > ago(1d) //show only VMs that were active in the last day 
| summarize NoHeartbeatPeriod = now() - max(TimeGenerated) by Computer  
| top 20 by NoHeartbeatPeriod desc 
```

## <a name="next-steps"></a>Další kroky
* Přečtěte si [smlouva SLA (SLA)](https://azure.microsoft.com/en-us/support/legal/sla/monitor/v1_3/) pro Azure monitor.
