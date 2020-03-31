---
title: Monitorování virtuálních počítačů Azure pomocí Azure Monitoru
description: Popisuje, jak shromažďovat a analyzovat data monitorování z virtuálních počítačů v Azure pomocí Azure Monitoru.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: 2cb53d0c88d8c29da2bd8bf52d6536555d56c76e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283935"
---
# <a name="monitoring-azure-virtual-machines-with-azure-monitor"></a>Monitorování virtuálních počítačů Azure pomocí Azure Monitoru
Tento článek popisuje, jak pomocí Azure Monitoru shromažďovat a analyzovat data monitorování z virtuálních počítačů Azure k udržení jejich stavu. Virtuální počítače můžete sledovat dostupnost a výkon s Azure Monitor jako každý [jiný prostředek Azure](monitor-azure-resource.md), ale jsou jedinečné z jiných prostředků, protože je také potřeba sledovat hostované ho operačního systému a systému a úlohy, které v něm běží. 

> [!NOTE]
> Tento článek poskytuje úplný přehled konceptů a možností pro monitorování virtuálních počítačů ve službě Azure Monitor. Pokud chcete začít rychle monitorovat své virtuální počítače, aniž byste se zaměřili na základní koncepty, přečtěte si [úvodní příručku: Monitorování virtuálního počítače Azure pomocí Azure Monitoru](../learn/quick-monitor-azure-vm.md).


## <a name="differences-from-other-azure-resources"></a>Rozdíly oproti ostatním prostředkům Azure
[Monitorování prostředků Azure pomocí Azure Monitor](monitor-azure-resource.md) popisuje data monitorování generovaná prostředky Azure a jak můžete pomocí funkcí Azure Monitoru analyzovat a upozorňovat na tato data. Můžete shromažďovat a jednat na stejná data monitorování z virtuálních počítačů Azure s následujícími rozdíly:

- [Metriky platformy](../platform/data-platform-metrics.md) se shromažďují automaticky pro virtuální počítače, ale pouze pro [hostitele virtuálních počítačů](#monitoring-data). Ke shromažďování údajů o výkonu z hostovaného operačního systému potřebujete agenta. 
- Virtuální počítače negenerují [protokoly prostředků,](../platform/platform-logs-overview.md) které poskytují přehled o operacích, které byly provedeny v rámci prostředku Azure. Agent slouží ke shromažďování dat protokolu z hostovaného operačního systému.
- Můžete vytvořit [diagnostická nastavení](../platform/diagnostic-settings.md) pro virtuální počítač k odesílání metrik platformy do jiných cílů, jako jsou úložiště a centra událostí, ale tato diagnostická nastavení na webu Azure Portal nemůžete nakonfigurovat. 

## <a name="monitoring-data"></a>Data monitorování
Virtuální počítače v Azure v Azure [generují protokoly](../platform/data-platform-logs.md) a [metriky zobrazené](../platform/data-platform-metrics.md) v následujícím diagramu.

![Přehled](media/monitor-vm-azure/logs-metrics.png)


### <a name="virtual-machine-host"></a>Hostitel virtuálního počítače
Virtuální počítače v Azure generují následující data pro hostitele virtuálního počítače stejně jako ostatní prostředky Azure, jak je popsáno v [části Monitorování dat](monitor-azure-resource.md#monitoring-data).

- [Metriky platformy](../platform/data-platform-metrics.md) – číselné hodnoty, které jsou automaticky shromažďovány v pravidelných intervalech a popisují některé aspekty prostředku v určitém čase. Metriky platformy jsou shromažďovány pro hostitele virtuálního počítače, ale vyžadujete rozšíření diagnostiky ke shromažďování metrik pro hostovaný operační systém.
- [Protokol aktivit](../platform/platform-logs-overview.md) – poskytuje přehled o operacích na každém prostředku Azure v předplatném zvenčí (rovina správy). Pro virtuální počítač to zahrnuje takové informace, jako když byl spuštěn a všechny změny konfigurace.


### <a name="guest-operating-system"></a>Hostovaný operační systém
Chcete-li shromažďovat data z hostovaného operačního systému virtuálního počítače, budete potřebovat agenta, který běží místně na každém virtuálním počítači a odesílá data do Azure Monitoru. Pro Azure Monitor je k dispozici více agentů, přičemž každý z nich shromažďuje různá data a zapisuje data do různých umístění. Získejte podrobné porovnání různých agentů na [přehled agentů Azure Monitor](../platform/agents-overview.md). 

- [Agent log Analytics](../platform/agents-overview.md#log-analytics-agent) – k dispozici pro virtuální počítače v Azure, jiných cloudových prostředích a místně. Shromažďuje data do protokolů monitorování Azure. Podporuje Azure Monitor pro virtuální počítače a řešení monitorování. Jedná se o stejného agenta, který se používá pro nástroj System Center Operations Manager.
- [Agent závislostí](../platform/agents-overview.md#dependency-agent) – shromažďuje data o procesech spuštěných ve virtuálním počítači a jejich závislostech. Spoléhá na agenta Log Analytics pro přenos dat do Azure a podporuje Azure Monitor pro virtuální počítače, service map a wire data 2.0 řešení.
- [Rozšíření Diagnostika Azure](../platform/agents-overview.md#azure-diagnostics-extension) – k dispozici jenom pro virtuální počítače Azure Monitor. Můžete shromažďovat data do více míst, ale primárně slouží ke shromažďování dat o výkonu hosta do metrikazure monitoru metriky pro virtuální počítače s Windows.
- [Agent Telegraf](../platform/collect-custom-metrics-linux-telegraf.md) – shromažďujte data o výkonu z virtuálních účtů Linuxu do metrik Azure Monitor.


## <a name="configuration-requirements"></a>Požadavky na konfiguraci
Chcete-li povolit všechny funkce Azure Monitor pro monitorování virtuálního počítače, musíte shromažďovat data monitorování z hostitele virtuálního počítače a hostovaného operačního systému na [metriky Azure Monitor](../platform/data-platform-logs.md) a [protokoly Azure Monitor .](../platform/data-platform-logs.md) V následující tabulce je uvedena konfigurace, která musí být provedena, aby byla povolena tato kolekce. Můžete se rozhodnout neprovádět všechny tyto kroky v závislosti na vašich konkrétních požadavcích.

| Krok konfigurace | Dokončené akce | Povolené funkce |
|:---|:---|:---|
| Žádná konfigurace | - Metriky hostitelské platformy shromážděné v metrikách.<br>- Záznam o aktivitách shromážděn. | - Průzkumník metrik pro hostitele.<br>- Upozornění na metriky pro hostitele.<br>- Upozornění na záznamy o aktivitách. |
| [Povolení Azure Monitoru pro virtuální počítače](#enable-azure-monitor-for-vms) | - Byl nainstalován agent Log Analytics.<br>- Byl nainstalován agent závislostí.<br>- Údaje o výkonu hosta shromážděné do protokolů.<br>- Podrobnosti o procesu a závislosti shromážděné do protokolů. | - Výkonnostní grafy a sešity pro údaje o výkonu hosta.<br>- Protokolovat dotazy na údaje o výkonu hosta.<br>- Protokolovat upozornění pro údaje o výkonu hosta.<br>- Mapa závislostí. |
| [Instalace rozšíření diagnostiky a telegrafického agenta](#enable-diagnostics-extension-and-telegraf-agent) | - Údaje o výkonu hosta shromážděné v metrikách. | - Průzkumník metrik pro hosta.<br>- Upozornění na metriky pro hosta.  |
| [Konfigurace pracovního prostoru Analýzy protokolů](#configure-log-analytics-workspace) | - Akce shromážděné od hosta. | - Protokolovat dotazy na události hosta.<br>- Log upozornění pro hosta události. |
| [Vytvoření diagnostického nastavení pro virtuální počítač](#collect-platform-metrics-and-activity-log) | - Metriky platformy shromážděné v protokolech.<br>- Protokol aktivit shromážděný chvatů do protokolů. | - Loq se dotazuje na metriky hostitele.<br>- Log upozornění pro metriky hostitele.<br>- Protokolovat dotazy pro protokol aktivit.

Každý z těchto kroků konfigurace je popsán v následujících částech.

### <a name="enable-azure-monitor-for-vms"></a>Povolení Azure Monitoru pro virtuální počítače
[Azure Monitor pro virtuální počítače](vminsights-overview.md) je [přehled](insights-overview.md) v Azure Monitoru, který je primárním nástrojem pro monitorování virtuálních počítačů v Azure Monitoru. Poskytuje následující další hodnotu oproti standardním funkcím Azure Monitoru.

- Zjednodušené registrace agenta analýzy protokolů a agenta závislostí umožňující monitorování hostovaného operačního systému virtuálního počítače a úloh. 
- Předdefinované grafy výkonu trendů a sešity, které umožňují analyzovat základní metriky výkonu z hostovaného operačního systému virtuálního počítače.
- Mapa závislostí, která zobrazuje procesy spuštěné na každém virtuálním počítači a propojené součásti s jinými počítači a externími zdroji.

![Azure Monitor pro virtuální počítače](media/monitor-vm-azure/vminsights-01.png)

![Azure Monitor pro virtuální počítače](media/monitor-vm-azure/vminsights-02.png)


Povolte Azure Monitor pro virtuální počítače z **možnosti Přehledy** v nabídce virtuálního počítače na webu Azure Portal. Podrobnosti a další metody konfigurace najdete [v tématu Povolení azure monitoru pro virtuální počítače.](vminsights-enable-overview.md)

![Povolení Azure Monitoru pro virtuální počítače](media/monitor-vm-azure/enable-vminsights.png)

### <a name="configure-log-analytics-workspace"></a>Konfigurace pracovního prostoru Analýzy protokolů
Agent Analýzy protokolů používaný službou Azure Monitor pro virtuální počítače odesílá data do [pracovního prostoru Analýzy protokolů](../platform/data-platform-logs.md#how-is-data-in-azure-monitor-logs-structured). Shromažďování dalších dat o výkonu, událostí a dalších dat monitorování od agenta můžete povolit konfigurací pracovního prostoru Log Analytics. Je třeba ji nakonfigurovat pouze jednou, protože jakýkoli agent, který se připojuje k pracovnímu prostoru, automaticky stáhne konfiguraci a okamžitě začne shromažďovat definovaná data. 

Ke konfiguraci pracovního prostoru můžete přistupovat přímo z Azure Monitor u virtuálních počítačů výběrem **konfigurace pracovního prostoru** z **webu Začínáme**. Kliknutím na název pracovního prostoru otevřete jeho nabídku.

![Konfigurace pracovního prostoru](media/monitor-vm-azure/workspace-configuration.png)

V nabídce pracovního prostoru vyberte **Upřesnit nastavení** a potom **data** pro konfiguraci zdrojů dat. U agentů systému Windows vyberte **protokoly událostí systému Windows** a přidejte běžné protokoly událostí, například *Systém* a *aplikace*. Pro linuxové agenty vyberte **Syslog** a přidejte běžná zařízení, jako je *kern* a *daemon*. Seznam dostupných zdrojů dat a podrobnosti o jejich konfiguraci najdete v tématu [Zdroje dat agenta v Azure Monitoru.](../platform/agent-data-sources.md) 

![Konfigurace událostí](media/monitor-vm-azure/configure-events.png)


> [!NOTE]
> Můžete nakonfigurovat čítače výkonu, které se shromažďují z konfigurace pracovního prostoru, ale to může být redundantní s čítači výkonu shromážděných Azure Monitor pro virtuální počítače. Azure Monitor pro virtuální počítače shromažďuje nejběžnější sadu čítačů na frekvenci jednou za minutu. Nakonfigurujte pouze čítače výkonu, které mají být shromažďovány v pracovním prostoru, pokud chcete shromažďovat čítače, které ještě neshromáždil Azure Monitor pro virtuální počítače nebo pokud máte existující dotazy pomocí dat o výkonu.


### <a name="enable-diagnostics-extension-and-telegraf-agent"></a>Povolit rozšíření diagnostiky a agenttelegraf
Azure Monitor pro virtuální počítače je založený na agentovi Analýzy protokolů, který shromažďuje data do pracovního prostoru Analýzy protokolů. To podporuje [několik funkcí Azure Monitor,](../platform/data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) jako jsou dotazy protokolu , [výstrahy](../log-query/log-query-overview.md) [protokolu](../platform/alerts-log.md)a [sešity](../platform/workbooks-overview.md). [Rozšíření diagnostiky](../platform/diagnostics-extension-overview.md) shromažďuje údaje o výkonu z hostovaného operačního systému virtuálních počítačů Windows do Služby Azure Storage a volitelně odesílá data o výkonu do [metrik Azure Monitor .](../platform/data-platform-metrics.md) Pro virtuální počítače s Linuxem je [agent Telegraf](../platform/collect-custom-metrics-linux-telegraf.md) povinen odesílat data do Azure Metrics.  To umožňuje další funkce Azure Monitoru, jako je [průzkumník metrik](../platform/metrics-getting-started.md) a [výstrahy metrik](../platform/alerts-metric.md). Můžete také nakonfigurovat rozšíření diagnostiky pro odesílání událostí a dat o výkonu mimo Azure Monitor pomocí Azure Event Hubs.

Nainstalujte rozšíření diagnostiky pro jeden virtuální počítač Windows na webu Azure Portal z **možnosti nastavení Diagnostika** v nabídce Virtuální počítač. Vyberte možnost povolit **Azure Monitor** na kartě **Jímky.** Informace o povolení rozšíření ze šablony nebo příkazového řádku pro více virtuálních počítačů naleznete v [tématu Instalace a konfigurace](../platform/diagnostics-extension-overview.md#installation-and-configuration). Na rozdíl od agenta Log Analytics data ke shromažďování je definována v konfiguraci pro rozšíření na každém virtuálním počítači.

![Diagnostické nastavení](media/monitor-vm-azure/diagnostic-setting.png)

Podrobnosti o konfiguraci agentů Telegrafu na virtuálních počítačích s Linuxem najdete v tématu [Instalace a konfigurace telegrafu.](../platform/collect-custom-metrics-linux-telegraf.md#install-and-configure-telegraf) Možnost **nastavení diagnostiky** je dostupná pro Linux, ale umožní jenom odesílat data do úložiště Azure.

### <a name="collect-platform-metrics-and-activity-log"></a>Shromažďování metrik platformy a protokolu aktivit
Můžete zobrazit metriky platformy a protokol aktivit shromážděných pro každého hostitele virtuálního počítače na webu Azure Portal. Shromážděte tato data do stejného pracovního prostoru Log Analytics jako Azure Monitor pro virtuální počítače a analyzujte je s dalšími daty monitorování shromážděnými pro virtuální počítač. Tato kolekce je konfigurována s [diagnostickým nastavením](../platform/diagnostic-settings.md). Shromážděte protokol aktivit s [diagnostickým nastavením pro odběr](../platform/diagnostic-settings.md#create-diagnostic-settings-in-azure-portal).

Shromažďujte metriky platformy s diagnostickým nastavením pro virtuální počítač. Na rozdíl od jiných prostředků Azure nelze vytvořit diagnostické nastavení pro virtuální počítač na webu Azure Portal, ale musíte použít [jinou metodu](../platform/diagnostic-settings.md#create-diagnostic-settings-using-powershell). Následující příklady ukazují, jak shromažďovat metriky pro virtuální počítač pomocí PowerShellu i CLI.

```powershell
Set-AzDiagnosticSetting -Name vm-diagnostics -ResourceId "/subscriptions/monitor diagnostic-settings create \
xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" -Enabled $true -MetricCategory AllMetrics -workspaceId "/subscriptions/monitor diagnostic-settings create \
xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
```

```CLI
az monitor diagnostic-settings create \
--name VM-Diagnostics 
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace

```

## <a name="monitoring-in-the-azure-portal"></a>Monitorování na webu Azure Portal 
Jakmile nakonfigurujete shromažďování dat monitorování pro virtuální počítač, máte na webu Azure Portal několik možností přístupu k němu:

- Pomocí nabídky **Azure Monitor** můžete získat přístup k datům ze všech monitorovaných prostředků. 
- Azure Monitor pro virtuální počítače použijte pro monitorování sad virtuálních počítačů ve velkém měřítku.
- Analyzujte data pro jeden virtuální počítač z jeho nabídky na webu Azure Portal. V následující tabulce jsou uvedeny různé možnosti sledování nabídky virtuálních počítačů.

![Monitorování na webu Azure Portal](media/monitor-vm-azure/monitor-menu.png)

| Nabídka, volba | Popis |
|:---|:---|
| Přehled | Zobrazí [metriky platformy](../platform/data-platform-metrics.md) pro hostitele virtuálního počítače. Klikněte na graf pro práci s těmito daty v [průzkumníku metrik](../platform/metrics-getting-started.md). |
| Protokol aktivit | Položky [protokolu aktivit](../platform/activity-log-view.md) filtrované pro aktuální virtuální počítač. |
| Insights | Otevře [Azure Monitor pro virtuální počítače](../insights/vminsights-overview.md) s mapou pro aktuální virtuální počítač vybrané. |
| Výstrahy | Zobrazení [výstrah](../platform/alerts-overview.md) pro aktuální virtuální počítač.  |
| Metriky | Otevřete [průzkumník metrik](../platform/metrics-getting-started.md) s oborem nastaveným na aktuální virtuální počítač. |
| Nastavení diagnostiky | Povolte a nakonfigurujte [rozšíření diagnostiky](../platform/diagnostics-extension-overview.md) pro aktuální virtuální počítač. |
| Doporučení Advisoru | Doporučení pro aktuální virtuální počítač od [Azure Advisor](/azure/advisor/). |
| Protokoly | Otevřete [Analýzu protokolů](../log-query/log-query-overview.md#what-is-log-analytics) s [oborem](../log-query/scope.md) nastaveným na aktuální virtuální počítač. |
| Monitor připojení | Otevřete [sledování připojení sledování sítě](../../network-watcher/connection-monitor-preview.md) a sledujte připojení mezi aktuálním virtuálním počítačem a jinými virtuálními počítači. |


## <a name="analyzing-metric-data"></a>Analýza dat metriky
Metriky pro virtuální počítače pomocí průzkumníka metrik můžete analyzovat otevřením **metriky** z nabídky virtuálního počítače. Podrobnosti o používání tohoto nástroje najdete v tématu [Začínáme s Průzkumníkem metrik Azure.](../platform/metrics-getting-started.md) 

Virtuální počítače používají pro metriky dva obory názvů:

| Obor názvů | Popis |
|:---|:---|
| Hostitel virtuálního počítače | Metriky hostitele se automaticky shromažďují pro všechny virtuální počítače Azure. Podrobný seznam metrik na [Microsoft.Compute/virtualMachines](../platform/metrics-supported.md#microsoftcomputevirtualmachines). |
| Host virtuálního počítače | Metriky hostovaného operačního systému shromážděné z virtuálních počítačů s nainstalovaným a nakonfigurovaným rozšířením diagnostiky pro odesílání do jímky Azure Monitoru. |

![Metriky](media/monitor-vm-azure/metrics.png)

## <a name="analyzing-log-data"></a>Analýza dat protokolu
Virtuální počítače Azure budou shromažďovat následující data do protokolů monitorování Azure. 

Azure Monitor pro virtuální počítače umožňuje shromažďování předem určené sady čítačů výkonu, které jsou zapsány do tabulky *InsightsMetrics.* Jedná se o stejnou tabulku používanou [službou Azure Monitor for Containers](container-insights-overview.md). 

| Zdroj dat | Požadavky | Tabulky |
|:---|:---|:---|
| Azure Monitor pro virtuální počítače | Povolit na každém virtuálním počítači. | InsightsMetrics<br>Port VMBoundPort<br>Počítač VM<br>Připojení virtuálního připojení<br>Proces virtuálního měsíč<br>Podrobnosti najdete [v tématu Jak dotazovat protokoly z Azure Monitor u virtuálních počítačů](vminsights-log-search.md) podrobnosti. |
| Protokol aktivit | Diagnostické nastavení pro odběr. | AzureActivity |
| Metriky hostitele | Diagnostické nastavení pro virtuální počítač. | AzureMetrics |
| Zdroje dat z hostovaného operačního systému | Povolte agenta Log Analytics a nakonfigurujte zdroje dat. | Viz dokumentace pro každý zdroj dat. |


> [!NOTE]
> Údaje o výkonu shromážděné agentem Analýzy protokolů zapisují do tabulky *Perf,* zatímco Azure Monitor pro virtuální počítače je bude shromažďovat do tabulky *InsightsMetrics.* Jedná se o stejná data, ale tabulky mají jinou strukturu. Pokud máte existující dotazy založené na *Perf*, bude nutné přepsat používat *InsightsMetrics*.


## <a name="alerts"></a>Výstrahy
[Výstrahy](../platform/alerts-overview.md) v Azure Monitor proaktivně vás upozorní, když se nacházejí důležité podmínky v datech monitorování a potenciálně spustit akci, jako je spuštění aplikace logiky nebo volání webhooku. Pravidla výstrah definují logiku použitou k určení, kdy má být výstraha vytvořena. Azure Monitor shromažďuje data používaná pravidly výstrah, ale je potřeba vytvořit pravidla pro definování podmínek upozornění ve vašem předplatném Azure.

V následujících částech jsou popsány typy pravidel výstrah a doporučení, kdy byste měli používat. Toto doporučení je založeno na funkčnosti a nákladech typu pravidla výstrahy. Podrobnosti o cenách výstrah najdete v tématu [Azure Monitor cen](https://azure.microsoft.com/pricing/details/monitor/).


### <a name="activity-log-alert-rules"></a>Pravidla upozornění protokolu aktivit
[Pravidla výstrah protokolu aktivit se](../platform/alerts-activity-log.md) zapalují, když je v protokolu aktivit vytvořena položka odpovídající konkrétním kritériím. Nemají žádné náklady, takže by měly být vaší první volbou, pokud je logika, kterou požadujete, v protokolu aktivit. 

Cílovým prostředkem pro výstrahy protokolu aktivit může být konkrétní virtuální počítač, všechny virtuální počítače ve skupině prostředků nebo všechny virtuální počítače v předplatném.

Můžete například vytvořit výstrahu, pokud je kritický virtuální počítač zastaven výběrem *virtuálního počítače power off* pro název signálu.

![Výstraha protokolu aktivit](media/monitor-vm-azure/activity-log-alert.png)


### <a name="metric-alert-rules"></a>Pravidla upozornění metrik
[Pravidla upozornění metriky](../platform/alerts-metric.md) se zapalují, když hodnota metriky překročí prahovou hodnotu. Můžete definovat konkrétní prahovou hodnotu nebo povolit Azure Monitor dynamicky určit prahovou hodnotu na základě historických dat.  Upozornění na metriky používejte vždy, když je to možné, s metrickými daty, protože stojí méně a jsou citlivější než pravidla upozornění protokolu. Jsou také stavové což znamená, že se vyřeší sami, když metrika klesne pod prahovou hodnotu.

Cílovým prostředkem pro výstrahy protokolu aktivit může být konkrétní virtuální počítač nebo všechny virtuální počítače ve skupině prostředků.

Chcete-li například vytvořit výstrahu, když procesor virtuálního počítače překročí určitou hodnotu, vytvořte pravidlo upozornění metriky pomocí *procentuálního procesoru* jako typu signálu. Nastavte určitou prahovou hodnotu nebo povolte Azure Monitor nastavit dynamickou prahovou hodnotu. 

![Upozornění na metriku](media/monitor-vm-azure/metric-alert.png)

### <a name="log-alerts"></a>Výstrahy protokolu
[Pravidla výstrah protokolu](../platform/alerts-log.md) se zapalují, pokud výsledky naplánovaného dotazu protokolu odpovídají určitým kritériím. Výstrahy dotazů protokolu jsou nejdražší a nejméně reagující na pravidla výstrah, ale mají přístup k nejrůznějším datům a mohou provádět komplexní logiku, kterou nelze provést pomocí jiných pravidel výstrah. 

Cílovým prostředkem pro dotaz protokolu je pracovní prostor Analýzy protokolů. Filtrujte pro konkrétní počítače v dotazu.

Chcete-li například vytvořit výstrahu, která zkontroluje, zda jsou všechny virtuální počítače v určité skupině prostředků offline, použijte následující dotaz, který vrátí záznam pro každý počítač, který v posledních deseti minutách zmeškal prezenční signál. Použijte prahovou hodnotu 1, která se aktivuje, pokud alespoň jeden počítač má zmeškaný prezenční signál.

```kusto
Heartbeat
| where TimeGenerated < ago(10m)
| where ResourceGroup == "my-resource-group"
| summarize max(TimeGenerated) by Computer
```

![Výstraha protokolu](media/monitor-vm-azure/log-alert-01.png)

Chcete-li vytvořit výstrahu, pokud došlo k nadměrnému počtu neúspěšných přihlášení na všech virtuálních počítačích systému Windows v předplatném, použijte následující dotaz, který vrátí záznam pro každou neúspěšnou událost přihlášení za poslední hodinu. Použijte prahovou hodnotu nastavenou na počet neúspěšných přihlášení, která povolíte. 

```kusto
Event
| where TimeGenerated < ago(1hr)
| where EventID == 4625
```

![Výstraha protokolu](media/monitor-vm-azure/log-alert-02.png)


## <a name="system-center-operations-manager"></a>System Center Operations Manager
System Center Operations Manager (SCOM) poskytuje podrobné monitorování úloh na virtuálních počítačích. Porovnání monitorovacích platforem a různých strategií implementace najdete v [průvodci monitorováním cloudu.](https://docs.microsoft.com/azure/cloud-adoption-framework/manage/monitor/)

Pokud máte existující prostředí SCOM, které chcete stále používat, můžete jej integrovat s Azure Monitor poskytnout další funkce. Agent Log Analytics používaný službou Azure Monitor je stejný jako agent používaný pro SCOM, takže jste monitorovali virtuální počítače, které odesílají data do obou. Stále potřebujete přidat agenta do Azure Monitor pro virtuální počítače a nakonfigurovat pracovní prostor tak, aby shromažďoval další data, jak je uvedeno výše, ale virtuální počítače můžou dál spouštět své stávající sady Management Pack v prostředí SCOM bez úprav.

Mezi funkce Azure Monitoru, které rozšiřují stávající funkce SCOM, patří následující:

- Pomocí analýzy protokolů můžete interaktivně analyzovat data protokolu a výkonu.
- Pomocí výstrah protokolu definujte podmínky výstrah napříč více virtuálními počítači a pomocí dlouhodobých trendů, které nejsou možné pomocí výstrah v SCOM.   

Podrobnosti o připojení stávající skupiny pro správu SCOM k pracovnímu prostoru Log Analytics najdete v tématu [Connect Operations Manager s Azure Monitor.](../platform/om-agents.md)


## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak analyzovat data v protokolech Azure Monitor pomocí protokolových dotazů.](../log-query/get-started-queries.md)
* [Další informace o výstrahách pomocí metrik a protokolů v Azure Monitoru.](../platform/alerts-overview.md)

