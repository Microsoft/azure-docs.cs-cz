---
title: Zdroje dat ve službě Azure Monitor | Dokumenty společnosti Microsoft
description: Popisuje data, která jsou k dispozici pro sledování stavu a výkonu prostředků Azure a aplikací, které na nich běží.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/19/2019
ms.openlocfilehash: d1c0652844556b545cf0617032d21b80dd67d198
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479837"
---
# <a name="sources-of-monitoring-data-for-azure-monitor"></a>Zdroje dat monitorování pro Azure Monitor
Azure Monitor je založený na [běžné datové platformě monitorování,](data-platform.md) která zahrnuje [protokoly](data-platform-logs.md) a [metriky](data-platform-metrics.md). Shromažďování dat do této platformy umožňuje data z více prostředků, které mají být analyzovány společně pomocí společné sady nástrojů v Azure Monitoru. Data monitorování mohou být také odesílána do jiných umístění pro podporu určitých scénářů a některé prostředky mohou zapisovat do jiných umístění, než je lze shromáždit do protokolů nebo metrik.

Tento článek popisuje různé zdroje dat monitorování shromážděných Azure Monitor kromě dat monitorování vytvořených prostředky Azure. Odkazy jsou poskytovány na podrobné informace o konfiguraci potřebné ke shromažďování těchto údajů do různých umístění.

## <a name="application-tiers"></a>Úrovně aplikace

Zdroje dat monitorování z aplikací Azure lze uspořádat do úrovní, přičemž nejvyšší úrovně jsou samotná vaše aplikace a nižší vrstvy jsou součásti platformy Azure. Způsob přístupu k datům z každé vrstvy se liší. Úrovně aplikace jsou shrnuty v následující tabulce a zdroje dat monitorování v každé vrstvě jsou uvedeny v následujících částech. V části [Monitorování datových umístění v Azure](data-locations.md) najdete popis jednotlivých umístění dat a způsob, jakým máte přístup k jeho datům.


![Úrovně monitorování](../media/overview/overview.png)


### <a name="azure"></a>Azure
Následující tabulka stručně popisuje aplikační vrstvy, které jsou specifické pro Azure. Následující odkaz pro další podrobnosti o každém v níže uvedených částech.

| Úroveň | Popis | Metoda sběru |
|:---|:---|:---|
| [Azure Tenant](#azure-tenant) | Data týkající se provozu služeb Azure na úrovni tenanta, jako je například Azure Active Directory. | Zobrazte data AAD na portálu nebo nakonfigurujte kolekci na Azure Monitor pomocí nastavení diagnostiky klienta. |
| [Předplatné Azure](#azure-subscription) | Data související se stavem a správou služeb napříč prostředky ve vašem předplatném Azure, jako je Správce prostředků a Stav služeb. | Zobrazení na portálu nebo konfigurace kolekce na Azure Monitor pomocí profilu protokolu. |
| [Prostředky Azure](#azure-resources) |  Data o operaci a výkonu každého prostředku Azure. | Metriky shromážděné automaticky, zobrazení v Průzkumníku metrik.<br>Nakonfigurujte nastavení diagnostiky pro shromažďování protokolů v Azure Monitoru.<br>Monitorování řešení a přehledy k dispozici pro podrobnější monitorování pro konkrétní typy prostředků. |

### <a name="azure-other-cloud-or-on-premises"></a>Azure, jiný cloud nebo místní 
Následující tabulka stručně popisuje aplikační vrstvy, které mohou být v Azure, jiném cloudu nebo v místním prostředí. Následující odkaz pro další podrobnosti o každém v níže uvedených částech.

| Úroveň | Popis | Metoda sběru |
|:---|:---|:---|
| [Operační systém (host)](#operating-system-guest) | Data o operačním systému na výpočetních prostředcích. | Nainstalujte agenta Log Analytics ke shromažďování zdrojů dat klientů do Azure Monitor a závislost agenta shromažďovat závislosti podporující Azure Monitor pro virtuální počítače.<br>Pro virtuální počítače Azure nainstalujte Azure Diagnostic Extension pro shromažďování protokolů a metrik do Azure Monitoru. |
| [Kód aplikace](#application-code) | Data o výkonu a funkčnosti skutečné aplikace a kódu, včetně sledování výkonu, protokolů aplikací a telemetrie uživatele. | Instrumentujte svůj kód ke shromažďování dat do Application Insights. |
| [Vlastní zdroje](#custom-sources) | Data z externích služeb nebo jiných součástí nebo zařízení. | Shromažďujte data protokolu nebo metrik do Azure Monitoru z libovolného klienta REST. |

## <a name="azure-tenant"></a>Tenant Azure
Telemetrie související s vaším tenantem Azure se shromažďuje ze služeb pro celý tenant, jako je Azure Active Directory.

![Kolekce tenantů Azure](media/data-sources/tenant.png)

### <a name="azure-active-directory-audit-logs"></a>Protokoly auditu služby Azure Active Directory
[Vytváření sestav služby Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md) obsahuje historii aktivitpřihlášení a auditování změn provedených v rámci konkrétního klienta. 

| Cíl | Popis | Odkaz |
|:---|:---|:---|
| Protokoly služby Azure Monitor | Nakonfigurujte protokoly Azure AD, které se mají shromažďovat v Azure Monitoru a analyzovat je s dalšími daty monitorování. | [Integrace protokolů Azure AD s protokoly Azure Monitor (preview)](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure Storage | Exportujte protokoly Azure AD do Úložiště Azure pro archivaci. | [Kurz: Archivace protokolů Azure AD do účtu úložiště Azure (Preview)](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Centrum událostí | Streamujte protokoly Azure AD do jiných umístění pomocí centra událostí. | [Kurz: Streamujte protokoly Služby Azure AD do centra událostí Azure (preview).](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |



## <a name="azure-subscription"></a>Předplatné Azure
Telemetrie související se stavem a provozem vašeho předplatného Azure.

![Předplatné Azure](media/data-sources/azure-subscription.png)

### <a name="azure-activity-log"></a>Protokol aktivit Azure 
[Protokol aktivit Azure](platform-logs-overview.md) zahrnuje záznamy o stavu služby spolu se záznamy o všech změnách konfigurace provedených v prostředcích ve vašem předplatném Azure. Protokol aktivit je k dispozici pro všechny prostředky Azure a představuje jejich _externí_ zobrazení.

| Cíl | Popis | Odkaz |
|:---|:---|
| Protokol aktivit | Protokol aktivit se shromažďuje do vlastního úložiště dat, které můžete zobrazit z nabídky Azure Monitor nebo použít k vytvoření výstrahprotokolu aktivit. | [Dotaz na protokol aktivit na webu Azure Portal](activity-log-view.md#azure-portal) |
| Protokoly služby Azure Monitor | Nakonfigurujte protokoly monitorování Azure tak, aby shromažďovaly protokol aktivit a analyzovaly jej s dalšími daty monitorování. | [Shromažďování a analýza protokolů aktivit Azure v pracovním prostoru Log Analytics v Azure Monitoru](activity-log-collect.md) |
| Azure Storage | Exportujte protokol aktivit do Úložiště Azure pro archivaci. | [Protokol aktivit archivace](resource-logs-collect-storage.md)  |
| Event Hubs | Streamování protokolu aktivit do jiných umístění pomocí centra událostí | [Streamovat protokol aktivit do centra událostí](resource-logs-stream-event-hubs.md). |

### <a name="azure-service-health"></a>Azure Service Health
[Azure Service Health](../../service-health/service-health-overview.md) poskytuje informace o stavu služeb Azure ve vašem předplatném, na které vaše aplikace a prostředky spoléhají.

| Cíl | Popis | Odkaz |
|:---|:---|:---|
| Protokol aktivit<br>Protokoly služby Azure Monitor | Záznamy stavu služby jsou uloženy v protokolu aktivit Azure, takže je můžete zobrazit na webu Azure Portal nebo provádět jakékoli jiné aktivity, které můžete provádět s protokolem aktivit. | [Zobrazení oznámení o stavu služby s využitím webu Azure Portal](service-notifications.md) |


## <a name="azure-resources"></a>Prostředky Azure
Metriky a protokoly prostředků poskytují informace o _interní_ masce prostředků Azure. Ty jsou k dispozici pro většinu služeb Azure a řešení monitorování a přehledy shromažďují další data pro konkrétní služby.

![Kolekce prostředků Azure](media/data-sources/azure-resources.png)


### <a name="platform-metrics"></a>Metriky platformy 
Většina služeb Azure [odešle metriky platformy,](data-platform-metrics.md) které odrážejí jejich výkon a provoz přímo do databáze metrik. Konkrétní [metriky se budou lišit pro každý typ zdroje](metrics-supported.md). 

| Cíl | Popis | Odkaz |
|:---|:---|:---|
| Metriky azure monitoru | Metriky platformy se zapisují do databáze metrik Azure Monitoru bez konfigurace. Přístup k metrikám platformy z Průzkumníka metrik.  | [Začínáme s Průzkumníkem metrik Azure](metrics-getting-started.md)<br>[Podporované metriky pomocí Azure Monitoru](metrics-supported.md) |
| Protokoly služby Azure Monitor | Pomocí analýzy protokolů zkopírujte metriky platformy do protokolů pro účely trendů a další analýzy. | [Diagnostika Azure přímo do analýzy protokolů](resource-logs-collect-workspace.md) |
| Event Hubs | Streamování metrik do jiných umístění pomocí centra událostí. |[Streamování dat monitorování Azure do centra událostí pro spotřebu pomocí externího nástroje](stream-monitoring-data-event-hubs.md) |

### <a name="resource-logs"></a>Protokoly prostředků
[Protokoly prostředků](platform-logs-overview.md) poskytují přehled o _interní_ mandatálce prostředku Azure.  Protokoly prostředků jsou vytvářeny automaticky, ale je nutné vytvořit diagnostické nastavení, které určí cíl, který mají být shromážděny pro každý prostředek.

Požadavky na konfiguraci a obsah protokolů prostředků se liší podle typu prostředku a ne všechny služby je ještě nevytvářejí. Podrobnosti o jednotlivých službách a odkazy na podrobné konfigurační postupy najdete v tématu [Podporované služby, schémata a kategorie pro protokoly prostředků Azure.](diagnostic-logs-schema.md) Pokud služba není uvedena v tomto článku, pak tato služba aktuálně nevytváří protokoly prostředků.

| Cíl | Popis | Odkaz |
|:---|:---|:---|
| Protokoly služby Azure Monitor | Odesílat protokoly prostředků do protokolů monitorování Azure pro analýzu s jinými shromážděnými daty protokolu. | [Shromažďování protokolů prostředků Azure v pracovním prostoru Log Analytics v Azure Monitoru](resource-logs-collect-storage.md) |
| Úložiště | Odešlete protokoly prostředků do Úložiště Azure pro archivaci. | [Archivace protokolů prostředků Azure](resource-logs-collect-workspace.md) |
| Event Hubs | Streamujte protokoly prostředků do jiných umístění pomocí centra událostí. |[Streamování protokolů prostředků Azure do centra událostí](resource-logs-stream-event-hubs.md) |

## <a name="operating-system-guest"></a>Operační systém (host)
Výpočetní prostředky v Azure, v jiných cloudech a v místním prostředí mají hostovaný operační systém, který můžete monitorovat. S instalací jednoho nebo více agentů můžete shromažďovat telemetrická data od hosta do Azure Monitoru a analyzovat je pomocí stejných nástrojů pro monitorování jako samotné služby Azure.

![Kolekce výpočetních prostředků Azure](media/data-sources/compute-resources.png)

### <a name="azure-diagnostic-extension"></a>Rozšíření diagnostiky Azure
Povolení rozšíření Diagnostika Azure pro virtuální počítače Azure umožňuje shromažďovat protokoly a metriky z hostovaného operačního systému výpočetních prostředků Azure, včetně webových a pracovních rolí Azure Cloud Service (klasické) webové a pracovní role, virtuální počítače, virtuální počítače škálovací sady a service fabric.

| Cíl | Popis | Odkaz |
|:---|:---|:---|
| Úložiště | Rozšíření diagnostiky Azure vždy zapíše do účtu Azure Storage. | [Instalace a konfigurace rozšíření diagnostiky Windows Azure (WAD)](diagnostics-extension-windows-install.md)<br>[Použití diagnostického rozšíření Linuxu k monitorování metrik a protokolů](../../virtual-machines/extensions/diagnostics-linux.md) |
| Metriky azure monitoru | Když nakonfigurujete rozšíření diagnostiky pro shromažďování čítačů výkonu, zapisují se do databáze metrik Azure Monitor. | [Odeslání metrik hostovaného operačního systému do úložiště metrik Azure Monitor pomocí šablony Správce prostředků pro virtuální počítač s Windows](collect-custom-metrics-guestos-resource-manager-vm.md) |
| Event Hubs | Nakonfigurujte rozšíření diagnostiky pro streamování dat do jiných umístění pomocí centra událostí.  | [Streamování dat Azure Diagnostics pomocí centra událostí](diagnostics-extension-stream-event-hubs.md)<br>[Použití diagnostického rozšíření Linuxu k monitorování metrik a protokolů](../../virtual-machines/extensions/diagnostics-linux.md) |
| Protokoly přehledů aplikací | Shromažďovat protokoly a čítače výkonu z výpočetního prostředku podporujícího vaši aplikaci, která má být analyzována s jinými daty aplikace. | [Odeslání diagnostických dat cloudové služby, virtuálního počítače nebo infrastruktury služeb do přehledů aplikací](diagnostics-extension-to-application-insights.md) |


### <a name="log-analytics-agent"></a>Agent Log Analytics 
Nainstalujte agenta Log Analytics pro komplexní monitorování a správu vašich virtuálních počítačů se systémem Windows nebo Linux. Virtuální počítač může běžet v Azure, jiném cloudu nebo místně.

| Cíl | Popis | Odkaz |
|:---|:---|:---|
| Protokoly služby Azure Monitor | Agent Log Analytics se připojuje k Azure Monitoru přímo nebo prostřednictvím nástroje System Center Operations Manager a umožňuje shromažďovat data ze zdrojů dat, které nakonfigurujete, nebo z řešení monitorování, která poskytují další přehled o aplikacích. běží na virtuálním počítači. | [Zdroje dat agenta ve službě Azure Monitor](agent-data-sources.md)<br>[Připojení nástroje Operations Manager ke službě Azure Monitor](om-agents.md) |
| Úložiště virtuálních mís | Azure Monitor pro virtuální počítače používá agenta Analýzy protokolů k ukládání informací o stavu vaze ve vlastním umístění. Více informací najdete v další části.  |


### <a name="azure-monitor-for-vms"></a>Azure Monitor pro virtuální počítače 
[Azure Monitor pro virtuální počítače](../insights/vminsights-overview.md) poskytuje přizpůsobené monitorování pro virtuální počítače poskytující funkce nad rámec základních funkcí Azure Monitoru. Vyžaduje agentzávislostí na virtuálních počítačích se systémem Windows a Linux, který se integruje s agentem Log Analytics ke shromažďování zjištěných dat o procesech spuštěných na virtuálním počítači a závislostech externích procesů.

| Cíl | Popis | Odkaz |
|:---|:---|:---|
| Protokoly služby Azure Monitor | Ukládá data o procesech a závislostech na agentovi. | [Použití mapy Azure Monitor pro virtuální počítače (preview) k pochopení součástí aplikací](../insights/vminsights-maps.md) |



## <a name="application-code"></a>Kód aplikace
Podrobné monitorování aplikací ve službě Azure Monitor se provádí pomocí [application insights,](https://docs.microsoft.com/azure/application-insights/) která shromažďuje data z aplikací spuštěných na různých platformách. Aplikace může být spuštěna v Azure, jiném cloudu nebo místně.

![Shromažďování dat aplikací](media/data-sources/applications.png)


### <a name="application-data"></a>Data aplikací
Když povolíte Application Insights pro aplikaci instalací balíčku instrumentace, shromažďuje metriky a protokoly související s výkonem a provozem aplikace. Application Insights ukládá data, která shromažďuje ve stejné datové platformě Azure Monitor u jiných zdrojů dat. Obsahuje rozsáhlé nástroje pro analýzu těchto dat, ale můžete je také analyzovat pomocí dat z jiných zdrojů pomocí nástrojů, jako je Průzkumník metrik a Analýza protokolů.

| Cíl | Popis | Odkaz |
|:---|:---|:---|
| Protokoly služby Azure Monitor | Provozní data o vaší aplikaci včetně zobrazení stránky, požadavků aplikací, výjimek a trasování. | [Analýza dat protokolu v Azure Monitoru](../log-query/log-query-overview.md) |
|                    | Informace o závislostech mezi součástmi aplikace pro podporu mapy aplikací a korelace telemetrie. | [Korelace telemetrie v application insights](../app/correlation.md) <br> [Mapa aplikace](../app/app-map.md) |
|            | Výsledky testů dostupnosti, které testují dostupnost a odezvu aplikace z různých umístění na veřejném Internetu. | [Sledování dostupnosti a odezvy libovolných webů](../app/monitor-web-app-availability.md) |
| Metriky azure monitoru | Application Insights shromažďuje metriky popisující výkon a provoz aplikace kromě vlastnímetriky, které definujete ve vaší aplikaci do databáze metrik Azure Monitor. | [Metriky založené na protokolech a předem agregované metriky ve službě Application Insights](../app/pre-aggregated-metrics-log-metrics.md)<br>[Rozhraní API služby Application Insights pro vlastní události a metriky](../app/api-custom-events-metrics.md) |
| Azure Storage | Odešlete data aplikací do Azure Storage pro archivaci. | [Export telemetrie z Application Insights](../app/export-telemetry.md) |
|            | Podrobnosti o testech dostupnosti se ukládají ve službě Azure Storage. Ke stažení pro místní analýzu použijte Přehledy aplikací na webu Azure Portal. Výsledky testů dostupnosti jsou uloženy v protokolech monitorování Azure. | [Sledování dostupnosti a odezvy libovolných webů](../app/monitor-web-app-availability.md) |
|            | Data trasování profileru se ukládají ve službě Azure Storage. Ke stažení pro místní analýzu použijte Přehledy aplikací na webu Azure Portal.  | [Profilprodukční aplikace v Azure s Application Insights](../app/profiler-overview.md) 
|            | Ladění snímek data, která se zachycena pro podmnožinu výjimek se ukládá ve službě Azure Storage. Ke stažení pro místní analýzu použijte Přehledy aplikací na webu Azure Portal.  | [Jak snímky fungují](../app/snapshot-debugger.md#how-snapshots-work) |

## <a name="monitoring-solutions-and-insights"></a>Monitorovací řešení a přehledy
[Řešení monitorování](../insights/solutions.md) a [přehledy](../insights/insights-overview.md) shromažďují data, aby poskytla další přehled o provozu určité služby nebo aplikace. Mohou adresovat prostředky v různých aplikačních vrstvách a dokonce i více úrovních.

### <a name="monitoring-solutions"></a>Řešení monitorování

| Cíl | Popis | Odkaz
|:---|:---|:---|
| Protokoly služby Azure Monitor | Řešení monitorování shromažďovat data do protokolů Azure Monitor, kde může být analyzovány pomocí dotazovacího jazyka nebo [zobrazení,](view-designer.md) které jsou obvykle zahrnuty v řešení. | [Podrobnosti o shromažďování dat pro řešení monitorování v Azure](../insights/solutions-inventory.md) |


### <a name="azure-monitor-for-containers"></a>Azure Monitor pro kontejnery
[Azure Monitor pro kontejnery](../insights/container-insights-overview.md) poskytuje přizpůsobené prostředí monitorování pro [službu Azure Kubernetes Service (AKS).](/azure/aks/) Shromažďuje další údaje o těchto prostředcích popsaných v následující tabulce.

| Cíl | Popis | Odkaz |
|:---|:---|:---|
| Protokoly služby Azure Monitor | Ukládá data monitorování pro AKS včetně zásob, protokolů a událostí. Metrická data jsou také uložena v protokolech, aby bylo možné využít jeho funkce analýzy na portálu. | [Porozumění výkonu clusteru AKS pomocí služby Azure Monitor pro kontejnery](../insights/container-insights-analyze.md) |
| Metriky azure monitoru | Metrická data jsou uložena v databázi metrik pro zvýšení vizualizace a výstrah. | [Zobrazení metrik kontejneru v průzkumníku metrik](../insights/container-insights-analyze.md#view-container-metrics-in-metrics-explorer) |
| Azure Kubernetes Service | Poskytuje přímý přístup k protokolům kontejnerů služby Azure Kubernetes (AKS) (stdout/stderror), událostem a metrikám pod na portálu. | [Jak zobrazit kubernetes protokoly, události a pod metriky v reálném čase](../insights/container-insights-livedata-overview.md) |

### <a name="azure-monitor-for-vms"></a>Azure Monitor pro virtuální počítače
[Azure Monitor pro virtuální počítače](../insights/vminsights-overview.md) poskytuje přizpůsobené prostředí pro monitorování virtuálních počítačů. Popis dat shromážděných službou Azure Monitor pro virtuální počítače je součástí výše uvedené části [Operační systém (host).](#operating-system-guest)

## <a name="custom-sources"></a>Vlastní zdroje
Kromě standardních úrovní aplikace může být nutné sledovat další prostředky, které mají telemetrii, které nelze shromažďovat s jinými zdroji dat. Pro tyto prostředky zapište tato data do metriky nebo protokoly pomocí rozhraní API Azure Monitor.

![Vlastní kolekce](media/data-sources/custom.png)

| Cíl | Metoda | Popis | Odkaz |
|:---|:---|:---|:---|
| Protokoly služby Azure Monitor | Rozhraní API kolekce dat | Shromažďujte data protokolu z libovolného klienta REST a uložte je v pracovním prostoru Log Analytics. | [Odeslání dat protokolu do azure monitoru pomocí rozhraní API pro shromažďování dat HTTP (public preview)](data-collector-api.md) |
| Metriky azure monitoru | Rozhraní API vlastních metrik | Shromažďujte metrická data z libovolného klienta REST a uložte je v databázi metrik Azure Monitoru. | [Odeslání vlastních metrik pro prostředek Azure do úložiště metrik Azure Monitor pomocí rozhraní REST API](metrics-store-custom-rest-api.md) |


## <a name="other-services"></a>Další služby
Další služby v Azure zapisují data na datovou platformu Azure Monitoru. To vám umožní analyzovat data shromážděná těmito službami pomocí dat shromážděných službou Azure Monitor a využít stejné nástroje pro analýzu a vizualizaci.

| Služba | Cíl | Popis | Odkaz |
|:---|:---|:---|:---|
| [Azure Security Center](/azure/security-center/) | Protokoly služby Azure Monitor | Azure Security Center ukládá data zabezpečení, která shromažďuje v pracovním prostoru Log Analytics, který umožňuje analyzovat pomocí jiných dat protokolu shromážděných službou Azure Monitor.  | [Shromažďování dat v Azure Security Center](../../security-center/security-center-enable-data-collection.md) |
| [Azure Sentinel](/azure/sentinel/) | Protokoly služby Azure Monitor | Azure Sentinel ukládá data, která shromažďuje z různých zdrojů dat v pracovním prostoru Log Analytics, který umožňuje analyzovat pomocí jiných dat protokolu shromážděných službou Azure Monitor.  | [Připojení zdrojů dat](/azure/sentinel/quickstart-onboard) |


## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [typech dat monitorování shromážděných službou Azure Monitor](data-platform.md) a o tom, jak tato data zobrazit a analyzovat.
- Uveďte [různá umístění, kde prostředky Azure ukládají data](data-locations.md) a jak k nim máte přístup. 
