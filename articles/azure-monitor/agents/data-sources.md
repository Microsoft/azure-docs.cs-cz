---
title: Zdroje dat v Azure Monitor | Microsoft Docs
description: Popisuje data dostupná ke sledování stavu a výkonu vašich prostředků Azure a aplikací, které jsou na nich spuštěné.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/06/2020
ms.openlocfilehash: 537163b4a78954443eeb7799dc624837ba03e2bd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102036618"
---
# <a name="sources-of-monitoring-data-for-azure-monitor"></a>Zdroje dat monitorování pro Azure Monitor
Azure Monitor je založený na [běžné platformě dat pro monitorování](../data-platform.md) , která zahrnuje [protokoly](../logs/data-platform-logs.md) a [metriky](../essentials/data-platform-metrics.md). Shromažďování dat na této platformě umožňuje analyzovat data z několika prostředků společně pomocí běžné sady nástrojů v Azure Monitor. Data monitorování se můžou posílat i na další místa pro podporu určitých scénářů a některé prostředky můžou zapisovat do jiných umístění, než je budete moct shromažďovat do protokolů nebo metrik.

Tento článek popisuje různé zdroje dat monitorování shromažďovaných nástrojem Azure Monitor kromě dat monitorování vytvořených prostředky Azure. Odkazy jsou k dispozici pro podrobné informace o konfiguraci potřebné ke shromažďování těchto dat do různých umístění.

## <a name="application-tiers"></a>Úrovně aplikace

Zdroje dat monitorování z aplikací Azure je možné uspořádat do vrstev, nejvyšší úrovně samotné aplikace a nižších vrstev, které jsou součástí platformy Azure. Metoda přístupu k datům z jednotlivých vrstev se liší. Aplikační vrstvy jsou shrnuté v následující tabulce a zdroje dat monitorování v jednotlivých vrstvách jsou uvedeny v následujících částech. V tématu [monitorování umístění dat v Azure](../monitor-reference.md) najdete popis každého umístění dat a způsob, jakým můžete získat přístup k datům.


![Úrovně monitorování](../media/overview/overview.png)


### <a name="azure"></a>Azure
Následující tabulka stručně popisuje aplikační vrstvy, které jsou specifické pro Azure. Následující odkaz vám podrobnější informace najdete v následujících částech.

| Úroveň | Description | Metoda Collection |
|:---|:---|:---|
| [Tenant Azure](#azure-tenant) | Data týkající se provozu služeb Azure na úrovni tenanta, jako je například Azure Active Directory. | Zobrazte data AAD na portálu nebo nakonfigurujte kolekci tak, aby se Azure Monitor pomocí nastavení diagnostiky tenanta. |
| [Předplatné Azure](#azure-subscription) | Data související se stavem a správou služeb pro více prostředků ve vašem předplatném Azure, například Správce prostředků a Service Health. | Zobrazte na portálu nebo nakonfigurujte kolekci tak, aby se Azure Monitor pomocí profilu protokolu. |
| [Prostředky Azure](#azure-resources) |  Údaje o provozu a výkonu každého prostředku Azure. | Automaticky shromažďované metriky, zobrazit v Průzkumník metrik.<br>Nakonfigurujte nastavení diagnostiky pro shromažďování protokolů v Azure Monitor.<br>Řešení a přehledy, které jsou k dispozici pro podrobnější monitorování konkrétních typů prostředků. |

### <a name="azure-other-cloud-or-on-premises"></a>Azure, jiný Cloud nebo místní 
Následující tabulka stručně popisuje aplikační vrstvy, které se můžou nacházet v Azure, jiném cloudu nebo místním prostředí. Následující odkaz vám podrobnější informace najdete v následujících částech.

| Úroveň | Description | Metoda Collection |
|:---|:---|:---|
| [Operační systém (host)](#operating-system-guest) | Data o operačním systému výpočetních prostředků. | Nainstalujte agenta Log Analytics ke shromáždění zdrojů dat klientů do Azure Monitor a agenta závislostí ke shromáždění závislostí, které podporují přehledy virtuálních počítačů.<br>Pro virtuální počítače Azure nainstalujte diagnostické rozšíření Azure, které shromáždí protokoly a metriky do Azure Monitor. |
| [Kód aplikace](#application-code) | Údaje o výkonu a funkcích skutečné aplikace a kódu, včetně trasování výkonu, protokolů aplikací a telemetrie uživatelů. | Instrumentujte kód pro shromažďování dat do Application Insights. |
| [Vlastní zdroje](#custom-sources) | Data z externích služeb nebo jiných komponent nebo zařízení. | Shromažďovat data protokolu nebo metrik do Azure Monitor z libovolného klienta REST. |

## <a name="azure-tenant"></a>Tenant Azure
Telemetrie související s vaším klientem Azure se shromažďují ze služeb, jako je Azure Active Directory.

![Kolekce tenantů Azure](media/data-sources/tenant.png)

### <a name="azure-active-directory-audit-logs"></a>Protokoly auditu Azure Active Directory
[Vytváření sestav Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md) obsahuje historii přihlašovacích aktivit a revizní záznam změn provedených v rámci určitého tenanta. 

| Cíl | Description | Reference |
|:---|:---|:---|
| Protokoly služby Azure Monitor | Nakonfigurujte protokoly Azure AD, které se budou shromažďovat v Azure Monitor a analyzovat je s ostatními daty monitorování. | [Integrace protokolů služby Azure AD s protokoly Azure Monitor (Preview)](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure Storage | Exportujte protokoly služby Azure AD, které se Azure Storage k archivaci. | [Kurz: Archivace protokolů Azure AD do účtu úložiště Azure (Preview)](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Centrum událostí | Streamování protokolů Azure AD do dalších umístění pomocí Event Hubs. | [Kurz: streamování Azure Active Directory protokolů do centra událostí Azure (Preview)](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md). |



## <a name="azure-subscription"></a>Předplatné Azure
Telemetrie související se stavem a provozem vašeho předplatného Azure.

![Předplatné Azure](media/data-sources/azure-subscription.png)

### <a name="azure-activity-log"></a>Protokol aktivit Azure 
[Protokol aktivit Azure](../essentials/platform-logs-overview.md) zahrnuje záznamy o stavu služeb společně se záznamy o všech změnách konfigurace provedených v prostředcích v předplatném Azure. Protokol aktivit je dostupný pro všechny prostředky Azure a představuje jejich _externí_ zobrazení.

| Cíl | Description | Reference |
|:---|:---|
| Protokol aktivit | Protokol aktivit se shromáždí do vlastního úložiště dat, které můžete zobrazit v nabídce Azure Monitor, nebo můžete použít k vytvoření výstrah protokolu aktivit. | [Dotazování protokolu aktivit v Azure Portal](../essentials/activity-log.md#view-the-activity-log) |
| Protokoly služby Azure Monitor | Nakonfigurujte protokoly Azure Monitor pro shromáždění protokolu aktivit, abyste ho mohli analyzovat s ostatními daty monitorování. | [Shromažďování a analýza protokolů aktivit Azure v pracovním prostoru Log Analytics v Azure Monitor](../essentials/activity-log.md) |
| Azure Storage | Exportujte protokol aktivit do Azure Storage k archivaci. | [Protokol aktivit archivu](../essentials/resource-logs.md#send-to-azure-storage)  |
| Event Hubs | Streamování protokolu aktivit do jiných umístění pomocí Event Hubs | [Streamování protokolu aktivit do centra událostí](../essentials/resource-logs.md#send-to-azure-event-hubs). |

### <a name="azure-service-health"></a>Azure Service Health
[Azure Service Health](../../service-health/service-health-overview.md) poskytuje informace o stavu služeb Azure ve vašem předplatném, na kterém závisí vaše aplikace a prostředky.

| Cíl | Description | Reference |
|:---|:---|:---|
| Protokol aktivit<br>Protokoly služby Azure Monitor | Service Health záznamy se ukládají do protokolu aktivit Azure, takže je můžete zobrazit v Azure Portal nebo provádět jakékoli jiné aktivity, které můžete s protokolem aktivit provádět. | [Zobrazení oznámení o stavu služby s využitím webu Azure Portal](../../service-health/service-notifications.md) |


## <a name="azure-resources"></a>Prostředky Azure
Metriky a protokoly prostředků poskytují informace o _interní_ operaci prostředků Azure. Jsou k dispozici pro většinu služeb Azure a monitorovací řešení a přehledy shromažďují další data pro konkrétní služby.

![Kolekce prostředků Azure](media/data-sources/data-source-azure-resources.svg)


### <a name="platform-metrics"></a>Metriky platformy 
Většina služeb Azure bude odesílat [metriky platforem](../essentials/data-platform-metrics.md) , které odpovídají jejich výkonu a provozu přímo do databáze metrik. Konkrétní [metriky se budou lišit pro každý typ prostředku](../essentials/metrics-supported.md). 

| Cíl | Description | Reference |
|:---|:---|:---|
| Metriky Azure Monitoru | Metriky platformy budou zapisovat do databáze metrik Azure Monitor bez konfigurace. Přístup k metrikám platformy z Průzkumník metrik.  | [Začínáme s Průzkumníkem metrik Azure](../essentials/metrics-getting-started.md)<br>[Podporované metriky s Azure Monitor](../essentials/metrics-supported.md) |
| Protokoly služby Azure Monitor | Kopírování metrik platforem do protokolů pro účely trendů a dalších analýz pomocí Log Analytics. | [Diagnostika Azure přímo na Log Analytics](../essentials/resource-logs.md#send-to-log-analytics-workspace) |
| Event Hubs | Streamujte metriky do jiných umístění pomocí Event Hubs. |[Streamování dat monitorování Azure do centra událostí pro využití externím nástrojem](../essentials/stream-monitoring-data-event-hubs.md) |

### <a name="resource-logs"></a>Protokoly prostředků
[Protokoly prostředků](../essentials/platform-logs-overview.md) poskytují přehled o _interní_ operaci prostředku Azure.  Protokoly prostředků se vytvářejí automaticky, ale musíte vytvořit nastavení diagnostiky, abyste určili cíl pro shromažďování pro jednotlivé prostředky.

Požadavky na konfiguraci a obsah protokolů prostředků se liší podle typu prostředku a ne u všech služeb, které je ještě nevytvořily. Podrobnosti o každé službě a odkazy na podrobné postupy konfigurace najdete v tématu [podporované služby, schémata a kategorie pro protokoly prostředků Azure](../essentials/resource-logs-schema.md) . Pokud tato služba není uvedená v tomto článku, tato služba aktuálně nevytváří protokoly prostředků.

| Cíl | Description | Reference |
|:---|:---|:---|
| Protokoly služby Azure Monitor | Odešlete protokoly prostředků, aby se protokoly Azure Monitor pro analýzu s dalšími shromážděnými daty protokolů. | [Shromažďování protokolů prostředků Azure v pracovním prostoru Log Analytics v Azure Monitor](../essentials/resource-logs.md#send-to-azure-storage) |
| Storage | Odešlete protokoly prostředků, které se Azure Storage k archivaci. | [Archivace protokolů prostředků Azure](../essentials/resource-logs.md#send-to-log-analytics-workspace) |
| Event Hubs | Streamujte protokoly prostředků do jiných umístění pomocí Event Hubs. |[Streamování protokolů prostředků Azure do centra událostí](../essentials/resource-logs.md#send-to-azure-event-hubs) |

## <a name="operating-system-guest"></a>Operační systém (host)
Výpočetní prostředky v Azure, v jiných cloudech a místně mají hostovaný operační systém, který monitoruje. Při instalaci jednoho nebo více agentů můžete shromáždit telemetrii z hosta do Azure Monitor a analyzovat ji pomocí stejných nástrojů pro monitorování, jako jsou samotné služby Azure.

![Shromažďování prostředků Azure COMPUTE](media/data-sources/compute-resources.png)

### <a name="azure-diagnostic-extension"></a>Diagnostické rozšíření Azure
Povolením rozšíření Azure Diagnostics pro virtuální počítače Azure můžete shromažďovat protokoly a metriky z hostovaného operačního systému výpočetních prostředků Azure, včetně webových a pracovních rolí Azure Cloud Service (Classic), Virtual Machines, virtuálních počítačů Scale Sets a Service Fabric.

| Cíl | Description | Reference |
|:---|:---|:---|
| Storage | Rozšíření Azure Diagnostics se vždycky zapisuje do účtu Azure Storage. | [Instalace a konfigurace rozšíření Windows Azure Diagnostics (WAD)](./diagnostics-extension-windows-install.md)<br>[Použití diagnostického rozšíření Linuxu k monitorování metrik a protokolů](../../virtual-machines/extensions/diagnostics-linux.md) |
| Metriky Azure Monitoru | Když nakonfigurujete diagnostické rozšíření pro shromažďování čítačů výkonu, budou zapsány do databáze Azure Monitor metrik. | [Odeslání metriky hostovaného operačního systému do úložiště metriky Azure Monitor pomocí šablony Správce prostředků pro virtuální počítač s Windows](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md) |
| Event Hubs | Nakonfigurujte diagnostické rozšíření pro streamování dat do jiných umístění pomocí Event Hubs.  | [Streamování Azure Diagnostics dat pomocí Event Hubs](./diagnostics-extension-stream-event-hubs.md)<br>[Použití diagnostického rozšíření Linuxu k monitorování metrik a protokolů](../../virtual-machines/extensions/diagnostics-linux.md) |
| Protokoly Application Insights | Shromažďovat protokoly a čítače výkonu z výpočetního prostředku podporující vaši aplikaci, která se bude analyzovat pomocí dalších aplikačních dat | [Odeslat cloudovou službu, virtuální počítač nebo Service Fabric diagnostická data do Application Insights](./diagnostics-extension-to-application-insights.md) |


### <a name="log-analytics-agent"></a>Agent Log Analytics 
Nainstalujte agenta Log Analytics pro komplexní monitorování a správu virtuálních počítačů se systémem Windows nebo Linux. Virtuální počítač může běžet v Azure, jiném cloudu nebo v místním prostředí.

| Cíl | Description | Reference |
|:---|:---|:---|
| Protokoly služby Azure Monitor | Agent Log Analytics se připojuje k Azure Monitor buď přímo, nebo prostřednictvím System Center Operations Manager a umožňuje shromažďovat data ze zdrojů dat, které nakonfigurujete, nebo z monitorování řešení, která poskytují další přehledy o aplikacích, které na virtuálním počítači běží. | [Zdroje dat agenta v Azure Monitor](../agents/agent-data-sources.md)<br>[Připojit Operations Manager k Azure Monitor](./om-agents.md) |
| Úložiště virtuálního počítače | Cloud Insights používá agenta Log Analytics k ukládání informací o stavu stavu ve vlastním umístění. Více informací najdete v další části.  |


### <a name="vm-insights"></a>Přehledy virtuálních počítačů 
Virtual Machine [Insights](../vm/vminsights-overview.md) nabízí přizpůsobené možnosti monitorování pro virtuální počítače, které poskytují funkce nad rámec základních funkcí Azure monitor. Vyžaduje Dependency Agent na virtuálních počítačích s Windows a Linux, které se integrují s agentem Log Analytics ke shromažďování zjištěných dat o procesech spuštěných na virtuálním počítači a závislostech externích procesů.

| Cíl | Description | Reference |
|:---|:---|:---|
| Protokoly služby Azure Monitor | Ukládá data o procesech a závislostech na agentovi. | [Použití mapy virtuálních počítačů (Preview) k porozumění součástem aplikace](../vm/vminsights-maps.md) |



## <a name="application-code"></a>Kód aplikace
Podrobné monitorování aplikací v Azure Monitor se provádí s [Application Insights](/azure/application-insights/) , která shromažďuje data z aplikací běžících na různých platformách. Aplikace může běžet v Azure, jiném cloudu nebo v místním prostředí.

![Shromažďování dat aplikací](media/data-sources/applications.png)


### <a name="application-data"></a>Data aplikací
Pokud povolíte Application Insights pro aplikaci instalací balíčku instrumentace, budou shromažďovány metriky a protokoly týkající se výkonu a provozu aplikace. Application Insights ukládá data, která shromažďuje, do stejné Azure Monitor datové platformy, kterou používají jiné zdroje dat. Obsahuje obsáhlé nástroje pro analýzu těchto dat, ale můžete je také analyzovat pomocí dat z jiných zdrojů pomocí nástrojů, jako jsou Průzkumník metrik a Log Analytics.

| Cíl | Description | Reference |
|:---|:---|:---|
| Protokoly služby Azure Monitor | Provozní data o vaší aplikaci, včetně zobrazení stránky, žádostí o aplikace, výjimek a trasování. | [Analyzovat data protokolu v Azure Monitor](../logs/log-query-overview.md) |
|                    | Informace o závislostech mezi součástmi aplikace pro podporu mapy aplikace a korelace telemetrie. | [Korelace telemetrie v Application Insights](../app/correlation.md) <br> [Mapa aplikace](../app/app-map.md) |
|            | Výsledky testů dostupnosti, které testují dostupnost a rychlost odezvy vaší aplikace z různých míst na veřejném Internetu. | [Sledování dostupnosti a odezvy libovolných webů](../app/monitor-web-app-availability.md) |
| Metriky Azure Monitoru | Application Insights shromažďuje metriky, které popisují výkon a provoz aplikace, spolu s vlastními metrikami, které definujete v aplikaci, do databáze Azure Monitor metrik. | [Metriky založené na protokolech a předem agregované metriky ve službě Application Insights](../app/pre-aggregated-metrics-log-metrics.md)<br>[Rozhraní API služby Application Insights pro vlastní události a metriky](../app/api-custom-events-metrics.md) |
| Azure Storage | Odešle data aplikace do Azure Storage k archivaci. | [Export telemetrie z Application Insights](../app/export-telemetry.md) |
|            | Podrobnosti testů dostupnosti jsou uloženy v Azure Storage. Pomocí Application Insights v Azure Portal si můžete stáhnout pro místní analýzu. Výsledky testů dostupnosti jsou uloženy v protokolech Azure Monitor. | [Sledování dostupnosti a odezvy libovolných webů](../app/monitor-web-app-availability.md) |
|            | Data trasování profileru se ukládají v Azure Storage. Pomocí Application Insights v Azure Portal si můžete stáhnout pro místní analýzu.  | [Profilace produkčních aplikací v Azure pomocí Application Insights](../app/profiler-overview.md) 
|            | Data snímku ladění, která jsou zachycena pro podmnožinu výjimek, jsou uložena v Azure Storage. Pomocí Application Insights v Azure Portal si můžete stáhnout pro místní analýzu.  | [Jak fungují snímky](../app/snapshot-debugger.md#how-snapshots-work) |

## <a name="monitoring-solutions-and-insights"></a>Monitorování řešení a přehledů
[Monitorování řešení](../insights/solutions.md) a [přehledů](../monitor-reference.md) shromažďuje data, která poskytují další přehledy o provozu konkrétní služby nebo aplikace. Můžou řešit prostředky v různých aplikačních vrstvách a dokonce i na více úrovních.

### <a name="monitoring-solutions"></a>Řešení monitorování

| Cíl | Description | Reference
|:---|:---|:---|
| Protokoly služby Azure Monitor | Řešení monitorování shromažďují data do protokolů Azure Monitor, kde je lze analyzovat pomocí dotazovacího jazyka nebo [zobrazení](../visualize/view-designer.md) , která jsou obvykle obsažena v řešení. | [Podrobnosti shromažďování dat pro řešení monitorování v Azure](../monitor-reference.md) |


### <a name="container-insights"></a>Přehledy kontejnerů
Služba [Container Insights](../containers/container-insights-overview.md) nabízí přizpůsobené možnosti monitorování pro [Azure KUBERNETES Service (AKS)](../../aks/index.yml). Shromažďuje další údaje o těchto prostředcích popsaných v následující tabulce.

| Cíl | Description | Reference |
|:---|:---|:---|
| Protokoly služby Azure Monitor | Ukládá data monitorování pro AKS, včetně inventáře, protokolů a událostí. Data metriky se také ukládají v protokolech, aby bylo možné využít její funkci analýzy na portálu. | [Vysvětlení výkonu clusteru AKS s využitím služby Container Insights](../containers/container-insights-analyze.md) |
| Metriky Azure Monitoru | Data metriky se ukládají v databázi metrik pro řízení vizualizace a výstrah. | [Zobrazit metriky kontejneru v Průzkumníkovi metrik](../containers/container-insights-analyze.md#view-container-metrics-in-metrics-explorer) |
| Azure Kubernetes Service | Poskytuje přímý přístup k protokolům kontejnerů služby Azure Kubernetes (stdout/stderr), událostem a pod metrikám na portálu. | [Jak zobrazit protokoly Kubernetes, události a metriky pod v reálném čase ](../containers/container-insights-livedata-overview.md) |

### <a name="vm-insights"></a>Přehledy virtuálních počítačů
Služby [VM Insights](../vm/vminsights-overview.md) poskytují přizpůsobené prostředí pro monitorování virtuálních počítačů. Popis dat shromažďovaných službou VM Insights je obsažen v části [operační systém (host)](#operating-system-guest) výše.

## <a name="custom-sources"></a>Vlastní zdroje
Kromě standardních úrovní aplikace může být nutné monitorovat další prostředky, které mají telemetrii, které nelze shromáždit s ostatními zdroji dat. Pro tyto prostředky zapište tato data do metriky nebo protokolů pomocí rozhraní Azure Monitor API.

![Vlastní kolekce](media/data-sources/custom.png)

| Cíl | Metoda | Popis | Reference |
|:---|:---|:---|:---|
| Protokoly služby Azure Monitor | Rozhraní API kolekce dat | Shromážděte data protokolu z libovolného klienta REST a uložte je do pracovního prostoru Log Analytics. | [Odeslání dat protokolu do Azure Monitor pomocí rozhraní API kolekce dat HTTP (Public Preview)](../logs/data-collector-api.md) |
| Metriky Azure Monitoru | Rozhraní API pro vlastní metriky | Shromážděte data metrik z libovolného klienta REST a uložte je do databáze Azure Monitorch metrik. | [Odesílat vlastní metriky pro prostředek Azure do úložiště metrik Azure Monitor pomocí REST API](../essentials/metrics-store-custom-rest-api.md) |


## <a name="other-services"></a>Další služby
Další služby v Azure zapisují data na Azure Monitor datovou platformu. Díky tomu můžete analyzovat data shromážděná těmito službami s daty shromažďovanými nástrojem Azure Monitor a využívat stejné nástroje pro analýzu a vizualizaci.

| Služba | Cíl | Description | Reference |
|:---|:---|:---|:---|
| [Azure Security Center](../../security-center/index.yml) | Protokoly služby Azure Monitor | Azure Security Center ukládá data zabezpečení shromažďovaná v pracovním prostoru Log Analytics, který umožňuje jejich analýzu s dalšími daty protokolů shromážděnými Azure Monitor.  | [Shromažďování dat v Azure Security Center](../../security-center/security-center-enable-data-collection.md) |
| [Azure Sentinel](../../sentinel/index.yml) | Protokoly služby Azure Monitor | Služba Azure Sentinel ukládá data, která shromažďuje z různých zdrojů dat v pracovním prostoru Log Analytics, což umožňuje jejich analýzu s dalšími daty protokolů shromážděnými pomocí Azure Monitor.  | [Připojení zdrojů dat](../../sentinel/quickstart-onboard.md) |


## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [typech dat monitorování shromažďovaných nástrojem Azure monitor](../data-platform.md) a o tom, jak tato data zobrazit a analyzovat.
- Seznamte se s [různými umístěními, kde prostředky Azure ukládají data](../monitor-reference.md) a jak k nim máte přístup.