---
title: Monitorování virtuálních počítačů Azure pomocí Azure Monitor
description: Popisuje, jak shromažďovat a analyzovat data monitorování z virtuálních počítačů v Azure pomocí Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/05/2020
ms.openlocfilehash: fc89790c7d268bcfa0c08bd26249bc91979d7fca
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186894"
---
# <a name="monitoring-azure-virtual-machines-with-azure-monitor"></a>Monitorování virtuálních počítačů Azure pomocí Azure Monitor
Tento článek popisuje, jak pomocí Azure Monitor shromažďovat a analyzovat data monitorování z virtuálních počítačů Azure a udržovat jejich stav. Virtuální počítače je možné monitorovat z hlediska dostupnosti a výkonu pomocí Azure Monitor jako u jakéhokoli [jiného prostředku Azure](monitor-azure-resource.md), ale jsou jedinečné od jiných prostředků, protože potřebujete také monitorovat hostovaný operační systém a systémy a úlohy, které jsou v něm spuštěné. 

> [!NOTE]
> Tento článek poskytuje úplný přehled konceptů a možností monitorování virtuálních počítačů v Azure Monitor. Pokud chcete rychle začít monitorovat virtuální počítače, aniž byste se museli zaměřit na základní koncepty, přečtěte si téma [rychlý Start: monitorování virtuálního počítače Azure pomocí Azure monitor](../learn/quick-monitor-azure-vm.md).


## <a name="differences-from-other-azure-resources"></a>Rozdíly oproti ostatním prostředkům Azure
[Monitorování prostředků Azure pomocí Azure monitor](monitor-azure-resource.md) popisuje data monitorování vygenerovaná prostředky Azure a způsob, jak můžete pomocí funkcí Azure monitor analyzovat a upozorňovat na tato data. Můžete shromažďovat a pracovat se stejnými daty monitorování z virtuálních počítačů Azure s těmito rozdíly:

-  [Metriky platformy](../platform/data-platform-metrics.md) se shromažďují automaticky pro virtuální počítače, ale jenom pro [hostitele virtuálního počítače](#monitoring-data). K shromažďování údajů o výkonu z hostovaného operačního systému potřebujete agenta. 
- Virtuální počítače negenerují [protokoly prostředků](../platform/platform-logs-overview.md) , které poskytují přehled o operacích provedených v rámci prostředku Azure. Pomocí agenta můžete shromažďovat data protokolu z hostovaného operačního systému.
- Můžete vytvořit [nastavení diagnostiky](../platform/diagnostic-settings.md) pro virtuální počítač, abyste odesílali metriky platforem do jiných cílů, jako jsou úložiště a centra událostí, ale tato nastavení diagnostiky nemůžete nakonfigurovat v Azure Portal. 

## <a name="monitoring-data"></a>Data monitorování
Virtuální počítače v Azure generují [protokoly](../platform/data-platform-logs.md) a [metriky](../platform/data-platform-metrics.md) , jak je znázorněno v následujícím diagramu.

![Přehled](media/monitor-vm-azure/logs-metrics.png)


### <a name="virtual-machine-host"></a>Hostitel virtuálního počítače
Virtuální počítače v Azure generují pro hostitele virtuálního počítače následující data, která jsou stejná jako u jiných prostředků Azure, jak je popsáno v tématu [monitorování dat](monitor-azure-resource.md#monitoring-data).

- [Metriky platforem](../platform/data-platform-metrics.md) – číselné hodnoty, které se automaticky shromažďují v pravidelných intervalech a popisují určitý aspekt prostředku v určitou dobu. Metriky platforem jsou shromažďovány pro hostitele virtuálního počítače, ale budete potřebovat diagnostické rozšíření pro shromažďování metrik pro hostovaný operační systém.
- [Protokol aktivit](../platform/platform-logs-overview.md) – poskytuje přehled o operacích u jednotlivých prostředků Azure v rámci předplatného zvenčí (rovina správy). Pro virtuální počítač zahrnuje tyto informace, jako když byla spuštěna, a všechny změny konfigurace.


### <a name="guest-operating-system"></a>Hostovaný operační systém
Pokud chcete shromažďovat data z hostovaného operačního systému virtuálního počítače, potřebujete agenta, který běží místně na každém virtuálním počítači a odesílá data do Azure Monitor. K dispozici je více agentů pro Azure Monitor s každým shromažďováním různých dat a zápisem dat do různých umístění. Podrobné porovnání různých agentů najdete v [přehledu Azure Monitorch agentů](../platform/agents-overview.md). 

- [Agent Log Analytics](../platform/agents-overview.md#log-analytics-agent) – k dispozici pro virtuální počítače v Azure, v jiných cloudových prostředích a místně. Shromažďuje data do protokolů Azure Monitor. Podporuje řešení Azure Monitor pro virtuální počítače a monitorování. To je stejný agent, který se používá pro System Center Operations Manager.
- [Agent závislostí](../platform/agents-overview.md#dependency-agent) – shromažďuje data o procesech spuštěných na virtuálním počítači a jejich závislostech. Spoléhá na Log Analytics agenta k přenosu dat do Azure a podporuje řešení Azure Monitor pro virtuální počítače, Service Map a Wire Data 2.0.
- [Diagnostické rozšíření Azure](../platform/agents-overview.md#azure-diagnostics-extension) – dostupné jenom pro Azure monitor virtuální počítače. Může shromažďovat data do více umístění, ale primárně se používá ke shromažďování dat o výkonu hostů do Azure Monitor metriky pro virtuální počítače s Windows.
- [Telegraf agent](../platform/collect-custom-metrics-linux-telegraf.md) – shromažďování údajů o výkonu z virtuálních počítačů se systémem Linux do metrik Azure monitor.


## <a name="configuration-requirements"></a>Požadavky na konfiguraci
Chcete-li povolit všechny funkce Azure Monitor pro monitorování virtuálního počítače, je třeba shromažďovat data monitorování z hostitele virtuálního počítače a hostovaného operačního systému do obou [Azure monitor metrik](../platform/data-platform-logs.md) a [protokolů Azure monitor](../platform/data-platform-logs.md). Následující tabulka uvádí konfiguraci, kterou je nutné provést, aby bylo možné tuto kolekci povolit. V závislosti na vašich konkrétních požadavcích se můžete rozhodnout neprovádět všechny tyto kroky.

| Krok konfigurace | Dokončené akce | Funkce povoleny |
|:---|:---|:---|
| Žádná konfigurace | – Metriky hostitelských platforem shromážděné do metrik.<br>– Byl shromážděn protokol aktivit. | – Průzkumník metrik pro hostitele.<br>– Výstrahy metrik pro hostitele.<br>– Výstrahy protokolu aktivit. |
| [Povolit Azure Monitor pro virtuální počítače](#enable-azure-monitor-for-vms) | -Log Analytics agenta nainstalováno.<br>– Byl nainstalován agent závislosti.<br>-Data o výkonu hosta shromážděna do protokolů.<br>-Podrobnosti o procesech a závislostech shromážděné do protokolů. | – Grafy výkonu a sešity pro data o výkonu hosta.<br>-V protokolu se dotazuje na data výkonu hostů.<br>– Výstrahy protokolu pro data výkonu hosta.<br>– Mapa závislostí. |
| [Instalace diagnostického rozšíření a agenta telegraf](#enable-diagnostics-extension-and-telegraf-agent) | – Údaje o výkonu hosta shromážděné do metrik. | – Průzkumník metrik pro hosta.<br>– Výstrahy metrik pro hosta.  |
| [Nakonfigurujete pracovní prostor služby Log Analytics](#configure-log-analytics-workspace) | -Události shromážděné z hosta. | -Zaznamenává dotaz na události typu Host.<br>– Výstrahy protokolu pro události typu Host. |
| [Vytvořit nastavení diagnostiky pro virtuální počítač](#collect-platform-metrics-and-activity-log) | – Metriky platformy shromážděné do protokolů.<br>-Protokol aktivit byl shromážděn do protokolů. | – Dotazy protokolu pro metriky hostitele.<br>– Výstrahy protokolu pro metriky hostitele.<br>– Protokoluje dotazy pro protokol aktivit.

Každý z těchto kroků konfigurace je popsaný v následujících částech.

### <a name="enable-azure-monitor-for-vms"></a>Povolit Azure Monitor pro virtuální počítače
[Azure monitor pro virtuální počítače](vminsights-overview.md) je [Přehled](../monitor-reference.md) v Azure monitor, který je primárním nástrojem pro monitorování virtuálních počítačů v Azure monitor. Poskytuje následující další hodnotu oproti standardním Azure Monitor funkcím.

- Zjednodušená registrace agenta Log Analytics a agenta závislostí, který umožňuje monitorování hostovaného operačního systému a zatížení virtuálního počítače. 
- Předem definované vývojové grafy a sešity výkonu, které umožňují analyzovat základní metriky výkonu z hostovaného operačního systému virtuálního počítače.
- Mapa závislostí, která zobrazuje procesy běžící na každém virtuálním počítači a propojené součásti s ostatními počítači a externími zdroji.

![Zobrazení výkonu Azure Monitor pro virtuální počítače](media/monitor-vm-azure/vminsights-01.png)

![Zobrazení Azure Monitor pro virtuální počítačech map](media/monitor-vm-azure/vminsights-02.png)


Povolte Azure Monitor pro virtuální počítače z možnosti **Insights** v nabídce virtuálního počítače Azure Portal. Podrobnosti a další metody konfigurace najdete v tématu [povolení Azure monitor pro virtuální počítače přehled](vminsights-enable-overview.md) .

![Povolit Azure Monitor pro virtuální počítače](media/monitor-vm-azure/enable-vminsights.png)

### <a name="configure-log-analytics-workspace"></a>Nakonfigurujete pracovní prostor služby Log Analytics
Agent Log Analytics, který Azure Monitor pro virtuální počítače používá, odesílá data do [pracovního prostoru Log Analytics](../platform/data-platform-logs.md). Konfigurací pracovního prostoru Log Analytics můžete povolit shromažďování dalších údajů o výkonu, událostí a dalších dat monitorování od agenta. Musí se nakonfigurovat jenom jednou, protože každý agent, který se připojuje k pracovnímu prostoru, automaticky stáhne konfiguraci a hned začne shromažďovat definovaná data. 

Ke konfiguraci pracovního prostoru můžete přistupovat přímo z Azure Monitor pro virtuální počítače tím **, že v části Začínáme** vyberete **Konfigurace pracovního prostoru** . Kliknutím na název pracovního prostoru otevřete jeho nabídku.

![Konfigurace pracovního prostoru](media/monitor-vm-azure/workspace-configuration.png)

V nabídce pracovní prostor vyberte **Upřesnit nastavení** a potom **data** pro konfiguraci zdrojů dat. U agentů Windows vyberte **protokoly událostí Windows** a přidejte běžné protokoly událostí, jako je třeba *systém* a *aplikace*. U agentů pro Linux vyberte **SYSLOG** a přidejte společná zařízení, jako je například *párování* a *démon*. Seznam zdrojů dat [agenta v Azure monitor najdete v tématu zdroje dat agentů](../platform/agent-data-sources.md) , které jsou k dispozici, a podrobné informace o jejich konfiguraci. 

![Konfigurace událostí](media/monitor-vm-azure/configure-events.png)


> [!NOTE]
> Můžete nakonfigurovat čítače výkonu, které mají být shromažďovány z konfigurace pracovního prostoru, ale mohou být redundantní s čítači výkonu shromažďovanými nástrojem Azure Monitor pro virtuální počítače. Azure Monitor pro virtuální počítače shromažďuje nejběžnější sadu čítačů v četnosti jednou za minutu. V případě, že chcete shromáždit čítače, které ještě nejsou shromážděny pomocí Azure Monitor pro virtuální počítače nebo pokud máte dotazy pomocí údajů o výkonu, nakonfigurujte pouze čítače výkonu, které mají být v pracovním prostoru shromažďovány.


### <a name="enable-diagnostics-extension-and-telegraf-agent"></a>Povolit diagnostické rozšíření a agenta telegraf
Azure Monitor pro virtuální počítače je založen na agentu Log Analytics, který odesílá data do Log Analyticsho pracovního prostoru. To podporuje více funkcí Azure Monitor, jako jsou například [dotazy protokolu](../log-query/log-query-overview.md), [výstrahy protokolu](../platform/alerts-log.md)a [sešity](../platform/workbooks-overview.md). [Diagnostické rozšíření](../platform/diagnostics-extension-overview.md) shromažďuje údaje o výkonu z hostovaného operačního systému virtuálních počítačů s Windows a Azure Storage a volitelně odesílá údaje o výkonu do [Azure monitor metrik](../platform/data-platform-metrics.md). Pro virtuální počítače se systémem Linux je nutné, aby byl [Agent telegraf](../platform/collect-custom-metrics-linux-telegraf.md) k odesílání dat do metrik Azure.  To umožňuje další funkce Azure Monitor, jako jsou například výstrahy [Průzkumníka metrik](../platform/metrics-getting-started.md) a [metriky](../platform/alerts-metric.md). Můžete také nakonfigurovat diagnostické rozšíření pro posílání událostí a dat o výkonu mimo Azure Monitor pomocí Azure Event Hubs.

Nainstalujte do Azure Portal rozšíření diagnostiky pro jeden virtuální počítač s Windows a z možnosti **nastavení diagnostiky** v nabídce virtuální počítač. Vyberte možnost povolení **Azure monitor** na kartě **jímky** . Pokud chcete povolit rozšíření ze šablony nebo příkazového řádku pro více virtuálních počítačů, přečtěte si téma [instalace a konfigurace](../platform/diagnostics-extension-overview.md#installation-and-configuration). Na rozdíl od agenta Log Analytics se data, která se mají shromáždit, definují v konfiguraci pro rozšíření na každém virtuálním počítači.

![Nastavení diagnostiky](media/monitor-vm-azure/diagnostic-setting.png)

Podrobnosti o konfiguraci agentů telegraf na virtuálních počítačích se systémem Linux najdete v tématu [install and Configure telegraf](../platform/collect-custom-metrics-linux-telegraf.md#install-and-configure-telegraf) . Možnost nabídky **nastavení diagnostiky** je dostupná pro Linux, ale umožní vám jenom posílat data do služby Azure Storage.

### <a name="collect-platform-metrics-and-activity-log"></a>Shromažďování metrik platforem a protokolu aktivit
Metriky platformy a protokol aktivit shromážděné pro každého hostitele virtuálního počítače můžete zobrazit v Azure Portal. Tato data můžete shromažďovat do stejného Log Analytics pracovního prostoru jako Azure Monitor pro virtuální počítače k jejich analýze s ostatními shromažďovanými daty monitorování pro virtuální počítač. Tato kolekce je nakonfigurována s [nastavením diagnostiky](../platform/diagnostic-settings.md). Shromážděte protokol aktivit s [nastavením diagnostiky pro předplatné](../platform/diagnostic-settings.md#create-in-azure-portal).

Shromážděte metriky platforem s nastavením diagnostiky pro virtuální počítač. Na rozdíl od jiných prostředků Azure nelze vytvořit nastavení diagnostiky pro virtuální počítač v Azure Portal, ale je nutné použít [jinou metodu](../platform/diagnostic-settings.md#create-using-powershell). Následující příklady znázorňují, jak shromažďovat metriky pro virtuální počítač pomocí PowerShellu i rozhraní příkazového řádku.

```powershell
Set-AzDiagnosticSetting -Name vm-diagnostics -ResourceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" -Enabled $true -MetricCategory AllMetrics -workspaceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
```

```CLI
az monitor diagnostic-settings create \
--name VM-Diagnostics 
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace
```

## <a name="monitoring-in-the-azure-portal"></a>Monitorování v Azure Portal 
Jakmile nakonfigurujete shromažďování dat monitorování pro virtuální počítač, máte k dispozici několik možností pro přístup k němu v Azure Portal:

- Pro přístup k datům ze všech monitorovaných prostředků použijte nabídku **Azure monitor** . 
- Pomocí Azure Monitor pro virtuální počítače můžete monitorovat sady virtuálních počítačů ve velkém měřítku.
- Analyzujte data pro jeden virtuální počítač z jeho nabídky v Azure Portal. V následující tabulce jsou uvedeny různé možnosti pro monitorování nabídky virtuálních počítačů.

![Monitorování v Azure Portal](media/monitor-vm-azure/monitor-menu.png)

| Možnost nabídky | Popis |
|:---|:---|
| Přehled | Zobrazí [metriky platforem](../platform/data-platform-metrics.md) pro hostitele virtuálního počítače. Pokud chcete s těmito daty pracovat v [Průzkumníkovi metrik](../platform/metrics-getting-started.md), klikněte na graf. |
| Protokol aktivit | Položky [protokolu aktivit](../platform/activity-log.md#view-the-activity-log) filtrované pro aktuální virtuální počítač. |
| Insights | Otevře [Azure monitor pro virtuální počítače](./vminsights-overview.md) s mapou pro vybraný aktuální virtuální počítač. |
| Výstrahy | Zobrazí [výstrahy](../platform/alerts-overview.md) pro aktuální virtuální počítač.  |
| Metriky | Otevřete [Průzkumníka metrik](../platform/metrics-getting-started.md) s oborem nastaveným na aktuální virtuální počítač. |
| Nastavení diagnostiky | Povolí a nakonfiguruje [diagnostické rozšíření](../platform/diagnostics-extension-overview.md) pro aktuální virtuální počítač. |
| Doporučení Advisoru | Doporučení pro aktuální virtuální počítač z [Azure Advisor](../../advisor/index.yml). |
| Protokoly | Otevřete [Log Analytics](../log-query/log-analytics-overview.md) s [oborem](../log-query/scope.md) nastaveným na aktuální virtuální počítač. |
| Monitorování připojení | Otevřete [monitorování připojení Network Watcher](../../network-watcher/connection-monitor-overview.md) pro monitorování připojení mezi aktuálním virtuálním počítačem a dalšími virtuálními počítači. |


## <a name="analyzing-metric-data"></a>Analýza dat metriky
Metriky pro virtuální počítače můžete analyzovat otevřením **metrik** z nabídky virtuálního počítače. Podrobnosti o použití tohoto nástroje najdete v tématu [Začínáme s Azure Průzkumník metrik](../platform/metrics-getting-started.md) . 

Virtuální počítače používají tři obory názvů pro metriky:

| Obor názvů | Popis | Požadavek |
|:---|:---|:---|
| Hostitel virtuálního počítače | Metriky hostitele se automaticky shromáždily pro všechny virtuální počítače Azure. Podrobný seznam metrik v [Microsoft. COMPUTE/virtualMachines](../platform/metrics-supported.md#microsoftcomputevirtualmachines). | Shromažďováno automaticky bez nutnosti konfigurace. |
| Host (klasický) | Omezená sada údajů o výkonu hostovaného operačního systému a aplikace. K dispozici v Průzkumníkovi metrik, ale ne jiné funkce Azure Monitor, jako jsou například výstrahy metriky.  | Bylo nainstalováno [diagnostické rozšíření](../platform/diagnostics-extension-overview.md) . Data se čtou z Azure Storage.  |
| Host virtuálního počítače | Data o výkonu hostovaného operačního systému a aplikací jsou dostupná všem funkcím Azure Monitor s využitím metrik. | Pro Windows je [nainstalovaná diagnostická rozšíření](../platform/diagnostics-extension-overview.md) nainstalovaná s povolenou Azure monitor jímka. Pro Linux se [nainstaluje agent telegraf](../platform/collect-custom-metrics-linux-telegraf.md). |

![Průzkumník metrik v Azure Portal](media/monitor-vm-azure/metrics.png)

## <a name="analyzing-log-data"></a>Analýza dat protokolu
Virtuální počítače Azure budou shromažďovat následující data pro Azure Monitor protokolů. 

Azure Monitor pro virtuální počítače umožňuje kolekci předem určené sady čítačů výkonu, které jsou zapsány do tabulky *InsightsMetrics* . Toto je stejná tabulka, kterou používá [Azure monitor for Containers](container-insights-overview.md). 

| Zdroj dat | Požadavky | Tabulky |
|:---|:---|:---|
| Azure Monitor pro virtuální počítače | Povolit na každém virtuálním počítači. | InsightsMetrics<br>VMBoundPort<br>VMComputer<br>VMConnection<br>VMProcess<br>Podrobnosti najdete v tématu [Postup dotazování protokolů z Azure monitor pro virtuální počítače](vminsights-log-search.md) . |
| Protokol aktivit | Nastavení diagnostiky pro odběr. | AzureActivity |
| Metriky hostitele | Nastavení diagnostiky pro virtuální počítač. | AzureMetrics |
| Zdroje dat z hostovaného operačního systému | Povolte agenta Log Analytics a nakonfigurujte zdroje dat. | Jednotlivé zdroje dat najdete v dokumentaci. |


> [!NOTE]
> Údaje o výkonu shromážděné agentem Log Analytics zapisuje do tabulky *perf* , zatímco Azure monitor pro virtuální počítače se shromáždí do tabulky *InsightsMetrics* . Jedná se o stejná data, ale tabulky mají jinou strukturu. Pokud máte dotazy na základě *výkonu*, bude nutné je přepsat, aby používaly *InsightsMetrics*.


## <a name="alerts"></a>Výstrahy
[Výstrahy](../platform/alerts-overview.md) v Azure monitor proaktivně upozorňují na to, že jsou ve vašich datech monitorování zjištěny důležité podmínky, a potenciálně spustí akci, jako je například spuštění aplikace logiky nebo volání Webhooku. Pravidla výstrah definují logiku použitou k určení, kdy se má vytvořit výstraha. Azure Monitor shromažďuje data používaná pravidly výstrah, ale je potřeba vytvořit pravidla pro definování podmínek upozorňování ve vašem předplatném Azure.

V následujících částech jsou popsány typy pravidel a doporučení pro výstrahy, kdy byste je měli použít. Toto doporučení je založené na funkcích a nákladech typu pravidla výstrahy. Podrobnosti o cenách výstrah najdete v tématu [Azure monitor ceny](https://azure.microsoft.com/pricing/details/monitor/).


### <a name="activity-log-alert-rules"></a>Pravidla upozornění protokolu aktivit
[Pravidla upozornění protokolu aktivit](../platform/alerts-activity-log.md) se aktivují, když se v protokolu aktivit vytvoří určitá kritéria. Nemají žádné náklady, takže by měli být první volbou, pokud je logika, kterou požadujete, v protokolu aktivit. 

Cílový prostředek pro výstrahy protokolu aktivit může být konkrétní virtuální počítač, všechny virtuální počítače ve skupině prostředků nebo všechny virtuální počítače v rámci předplatného.

Můžete například vytvořit výstrahu v případě, že je virtuální počítač zastavený, a to výběrem možnosti *vypnout virtuální počítač* pro název signálu.

![Upozornění protokolu aktivit](media/monitor-vm-azure/activity-log-alert.png)


### <a name="metric-alert-rules"></a>Pravidla upozornění metrik
[Pravidla upozornění metriky](../platform/alerts-metric.md) se aktivují, když hodnota metriky překročí prahovou hodnotu. Můžete definovat konkrétní prahovou hodnotu nebo Azure Monitor umožní dynamicky určit prahovou hodnotu na základě historických dat.  Používejte výstrahy metrik, kdykoli je to možné, a data metriky, protože jsou levnější a jsou větší než pravidla upozornění protokolů. Jsou to také stavový význam, který se vyřeší, když metrika klesne pod prahovou hodnotu.

Cílový prostředek pro výstrahy protokolu aktivit může být konkrétní virtuální počítač nebo všechny virtuální počítače ve skupině prostředků.

Pokud například chcete vytvořit výstrahu v případě, že procesor virtuálního počítače překročí určitou hodnotu, vytvořte pravidlo upozornění metriky s využitím *PROCENTA procesoru* jako typu signálu. Nastavte buď určitou prahovou hodnotu, nebo povolte Azure Monitor nastavit dynamickou prahovou hodnotu. 

![Výstraha metriky](media/monitor-vm-azure/metric-alert.png)

### <a name="log-alerts"></a>Výstrahy protokolu
[Pravidla upozornění protokolů](../platform/alerts-log.md) se aktivují, když výsledky plánovaného dotazu protokolu odpovídají určitým kritériím. Výstrahy dotazování protokolu jsou nejlevnější a musí odpovídat pravidlům výstrah, ale mají přístup k nejvíce různorodým datům a mohou provádět komplexní logiku, kterou nelze provést pomocí jiných pravidel upozornění. 

Cílovým prostředkem pro dotaz na protokol je Log Analytics pracovní prostor. Filtrovat konkrétní počítače v dotazu.

Chcete-li například vytvořit výstrahu, která kontroluje, zda jsou některé virtuální počítače v určité skupině prostředků v režimu offline, použijte následující dotaz, který vrátí záznam pro každý počítač, který nenalezl prezenční signál za posledních deset minut. Použijte prahovou hodnotu 1, která se aktivuje, pokud alespoň v jednom počítači chybí prezenční signál.

```kusto
Heartbeat
| where TimeGenerated > ago(10m)
| where ResourceGroup == "my-resource-group"
| summarize max(TimeGenerated) by Computer
```

![Výstraha protokolu pro zmeškaný prezenční signál](media/monitor-vm-azure/log-alert-01.png)

Chcete-li vytvořit výstrahu v případě, že došlo k nadměrnému počtu neúspěšných přihlášení na libovolných virtuálních počítačích s Windows v rámci předplatného, použijte následující dotaz, který vrátí záznam pro každou událost neúspěšného přihlášení během poslední hodiny. Použijte prahovou hodnotu nastavenou na počet neúspěšných přihlášení, která povolíte. 

```kusto
Event
| where TimeGenerated > ago(1hr)
| where EventID == 4625
```

![Výstraha protokolu pro neúspěšná přihlášení](media/monitor-vm-azure/log-alert-02.png)


## <a name="system-center-operations-manager"></a>System Center Operations Manager
System Center Operations Manager poskytuje podrobné monitorování úloh na virtuálních počítačích. Porovnání monitorovacích platforem a různých strategií pro implementaci najdete v [Průvodci monitorováním cloudu](/azure/cloud-adoption-framework/manage/monitor/) .

Pokud máte existující prostředí Operations Manager, které chcete dál používat, můžete ho integrovat s Azure Monitor a poskytnout další funkce. Agent Log Analytics používaný v Azure Monitor je stejný jako používaný pro Operations Manager, aby bylo možné monitorovat virtuální počítače, odesílat data do obou. Stále musíte přidat agenta Azure Monitor pro virtuální počítače a nakonfigurovat pracovní prostor pro shromažďování dalších dat, jak je uvedeno výše, ale virtuální počítače mohou nadále spouštět stávající sady Management Pack v prostředí Operations Manager bez úprav.

Mezi funkce Azure Monitor, které rozšiřují existující Operations Manager funkce, patří následující:

- Pomocí Log Analytics můžete interaktivně analyzovat data protokolů a výkonu.
- Používejte výstrahy protokolu k definování podmínek upozorňování napříč několika virtuálními počítači a používání dlouhodobých trendů, které neumožňují použití výstrah v Operations Manager.   

Podrobnosti o připojení existující skupiny pro správu Operations Manager k pracovnímu prostoru Log Analytics najdete v tématu [připojení Operations Manager k Azure monitor](../platform/om-agents.md) .


## <a name="next-steps"></a>Další kroky

* [Naučte se analyzovat data v protokolech Azure Monitor pomocí dotazů protokolu.](../log-query/get-started-queries.md)
* [Seznamte se s výstrahami pomocí metrik a protokolů v Azure Monitor.](../platform/alerts-overview.md)