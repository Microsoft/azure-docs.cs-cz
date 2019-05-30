---
title: Zdroje dat ve službě Azure Monitor | Dokumentace Microsoftu
description: Popisuje data, která je k dispozici pro sledování stavu a výkonu vašich prostředků Azure a aplikace běžící na ně.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/23/2019
ms.author: bwren
ms.openlocfilehash: b77fb3ab5651147c59b9f0afd22a2d6d0159c90e
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357492"
---
# <a name="sources-of-monitoring-data-for-azure-monitor"></a>Zdroje dat monitorování pro monitorování Azure
Azure Monitor je založen na [společnou datovou platformu monitorování](data-platform.md) , který obsahuje [protokoly](data-platform-logs.md) a [metriky](data-platform-metrics.md). Shromažďování dat na této platformě umožňuje dat z různých zdrojů, které má být analyzován pomocí společnou sadu nástrojů ve službě Azure Monitor. Data monitorování mohou také být odeslány do jiných umístění pro zajištění podpory určitých scénářů a některé prostředky můžou zapisovat do jiných umístění, než mohou být shromážděny do protokolů nebo metrik.

Tento článek popisuje různé zdroje monitorování data shromážděná službou Azure Monitor, kromě dat monitorování, které jsou vytvořené prostředky Azure. Jsou uvedeny odkazy na podrobné informace o konfiguraci vyžadovanou pro shromažďování těchto dat do různých umístění.

## <a name="application-tiers"></a>Úrovně aplikace

Zdroje dat z aplikací Azure monitorování mohou být uspořádány do vrstev, nejvyšší úrovně se vaše vlastní aplikace a nižší úrovně, které jsou součástí platformy Azure. Metoda přístup k datům v jednotlivých úrovních se liší. V následující tabulce jsou shrnuté aplikačních vrstev, a zdroje dat v jednotlivých vrstvách monitorování jsou uvedeny v následující části. Zobrazit [monitorování umístění dat v Azure](data-locations.md) popis každého umístění dat a jak přistupovat k jeho datům.


![Sledování úrovně](../media/overview/overview.png)


### <a name="azure"></a>Azure
Následující tabulka stručně popisuje aplikačních vrstev, které jsou specifické pro Azure. Následující odkaz pro další podrobnosti o každé v následujících částech.

| Úroveň | Popis | Kolekce – metoda |
|:---|:---|:---|
| [Azure Tenant](#azure-tenant) | Data o provozu služeb Azure úrovni klienta, jako je Azure Active Directory. | Zobrazení dat AAD na portálu nebo konfigurace shromažďování dat do Azure monitoru pomocí nastavení diagnostiky tenanta. |
| [Předplatné Azure](#azure-subscription) | Data týkající se stavu a správu služeb napříč prostředky ve vašem předplatném Azure, jako je například Resource Manageru a stavy služeb. | Zobrazit na portálu nebo konfigurace shromažďování dat do Azure monitoru pomocí profilu protokolu. |
| [Prostředky Azure](#azure-resources) |  Data o provozu a výkonu každého prostředku Azure. | Shromažďovat metriky automaticky, zobrazit v Průzkumníku metrik.<br>Konfigurace nastavení diagnostiky pro shromažďování protokolů ve službě Azure Monitor.<br>Monitorování řešení a přehledy, které jsou k dispozici pro podrobné monitorování pro konkrétní typy prostředků. |

### <a name="azure-other-cloud-or-on-premises"></a>Azure, dalších cloudových nebo místních 
Následující tabulka stručně popisuje aplikačních vrstev, které mohou být v Azure, jiného cloudu nebo místně. Následující odkaz pro další podrobnosti o každé v následujících částech.

| Úroveň | Popis | Kolekce – metoda |
|:---|:---|:---|
| [Operační systém (guest)](#operating-system-guest) | Data o operační systém na výpočetní prostředky. | Nainstalujte agenta Log Analytics ke shromažďování klientské zdroje dat do Azure monitoru a závislostí agenta ke shromažďování závislosti, podpoře pro virtuální počítače Azure Monitor.<br>Pro virtuální počítače Azure nainstalujte rozšíření diagnostiky Azure shromažďovat protokoly a metriky do služby Azure Monitor. |
| [Kód aplikace](#application-code) | Data o výkonu a funkce aplikace skutečný a kódu, včetně trasování výkonu a protokoly aplikací a telemetrii uživatelů. | Instrumentace kódu ke shromažďování dat do Application Insights. |
| [Vlastní zdroje](#custom-sources) | Data z externích služeb nebo jiné součásti nebo zařízení. | Shromažďování protokolů nebo metrik dat do služby Azure Monitor z jakéhokoli klienta REST. |

## <a name="azure-tenant"></a>Tenant Azure
Telemetrické údaje vztahující se k tenantovi Azure se shromažďují z celého tenanta služeb, jako je Azure Active Directory.

![Kolekce Azure klienta](media/data-sources/tenant.png)

### <a name="azure-active-directory-audit-logs"></a>Protokoly auditování Azure Active Directory
[Generování sestav v Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md) obsahuje historii přihlašovací aktivitu a revizní záznam změn provedených v rámci konkrétního tenanta. 

| Cíl | Popis | Referenční informace |
|:---|:---|:---|
| Azure Monitor Logs | Konfigurace protokolů Azure AD, které se mají shromažďovat ve službě Azure Monitor k analýze s dalšími daty monitorování. | [Integrace protokolů Azure AD s protokoly Azure monitoru (preview)](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure Storage | Exportujte protokoly služby Azure AD do služby Azure Storage pro archivaci. | [Kurz: Archivovat protokoly služby Azure AD do účtu služby Azure storage (preview)](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Centrum událostí | Stream protokolů služby Azure AD do jiných umístění s využitím služby Event Hubs. | [Kurz: Stream protokolů služby Azure Active Directory do centra událostí Azure (preview)](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md). |



## <a name="azure-subscription"></a>Předplatné Azure
Telemetrické údaje vztahující se na stav a provoz vašeho předplatného Azure.

![Předplatné Azure](media/data-sources/azure-subscription.png)

### <a name="azure-activity-log"></a>Protokol aktivit v Azure 
[Protokol aktivit Azure](activity-logs-overview.md) obsahuje záznamy stavu služby spolu s záznamů na změny konfigurace provedené v prostředcích v předplatném Azure. Protokol aktivit je k dispozici pro všechny prostředky Azure a představuje jejich _externí_ zobrazení.

| Cíl | Popis | Referenční informace |
|:---|:---|
| Protokol aktivit | Protokol aktivit se shromažďují do své vlastní úložiště dat, které můžete zobrazit v nabídce Azure Monitor nebo můžete použít k vytvoření upozornění protokolu aktivit. | [Dotaz protokolu aktivit na webu Azure Portal](activity-log-view.md#azure-portal) |
| Azure Monitor Logs | Nakonfigurujte protokoly Azure monitoru ke shromažďování protokolu aktivit pro analýzu s dalšími daty monitorování. | [Shromažďování a analýza protokolů aktivit Azure v pracovním prostoru Log Analytics ve službě Azure Monitor](activity-log-collect.md) |
| Azure Storage | Exportujte protokolu aktivit do služby Azure Storage pro archivaci. | [Archivace protokolu aktivit](activity-log-export.md#archive-activity-log)  |
| Event Hubs | Stream protokolů aktivit do jiných umístění s využitím služby Event Hubs | [Stream protokolů aktivit do centra událostí](activity-log-export.md#stream-activity-log-to-event-hub). |

### <a name="azure-service-health"></a>Azure Service Health
[Azure Service Health](../../service-health/service-health-overview.md) poskytuje informace o stavu služby Azure ve vašem předplatném, které využívají vaše aplikace a prostředky.

| Cíl | Popis | Referenční informace |
|:---|:---|:---|
| Protokol aktivit<br>Azure Monitor Logs | Service Health záznamy ukládají v protokolu aktivit Azure, můžete je zobrazit na webu Azure Portal nebo provádět jiné činnosti, které můžete provádět pomocí protokolu aktivit. | [Zobrazení oznámení o stavu služby s využitím webu Azure Portal](service-notifications.md) |


## <a name="azure-resources"></a>Prostředky Azure
Metriky a prostředků úrovně diagnostické protokoly poskytují informace o _interní_ operace prostředků Azure. Tyto jsou dostupné pro většina služeb Azure a monitorování řešení a insights shromažďovat další data pro konkrétní služby.

![Kolekce prostředků Azure](media/data-sources/azure-resources.png)


### <a name="platform-metrics"></a>Platforma metriky 
Většina služeb Azure bude odesílat [platformy metriky](data-platform-metrics.md) , které reflektují jejich výkon a operace přímo do databáze metriky. Konkrétní [metriky se liší pro jednotlivé typy prostředků](metrics-supported.md). 

| Cíl | Popis | Referenční informace |
|:---|:---|:---|
| Metriky Azure Monitor | Metriky platformy bude zapisovat do databáze metrik Azure monitoru bez konfigurace. Přístup k platformě metriky z Průzkumníka metrik.  | [Začínáme s Průzkumníkem metrik Azure](metrics-getting-started.md)<br>[Podporované metriky ve službě Azure Monitor](metrics-supported.md) |
| Azure Monitor Logs | Zkopírujte platformy metriky do protokolů pro analýzu trendů a dalších pomocí Log Analytics. | [Diagnostika Azure směrovat do služby Log Analytics](collect-azure-metrics-logs.md#azure-diagnostics-direct-to-log-analytics) |
| Event Hubs | Stream metrik do jiných umístění s využitím služby Event Hubs. |[Pomocí externího nástroje pro monitorování data do centra událostí pro používání Azure Stream](stream-monitoring-data-event-hubs.md) |

### <a name="diagnostic-logs"></a>Diagnostické protokoly
[Diagnostické protokoly](diagnostic-logs-overview.md) poskytují přehled o _interní_ operace prostředku Azure.  Ve výchozím nastavení nejsou povolené diagnostické protokoly. Musíte je povolit a zadejte cílovou složku pro každý zdroj. 

Požadavky na konfiguraci a obsah diagnostické protokoly se liší podle typu prostředku, a ne všechny služby ještě vytvořte diagnostické protokoly. Zobrazit [podporované služby, schémat a kategorie pro diagnostické protokoly Azure](diagnostic-logs-schema.md) podrobné informace o jednotlivých služeb a odkazy na postupy podrobnou konfiguraci. Pokud služba není uvedená v tomto článku, pak tuto službu není aktuálně zapisovat do diagnostické protokoly.

| Cíl | Popis | Referenční informace |
|:---|:---|:---|
| Azure Monitor Logs | Odesílání diagnostických protokolů pro analýzu s ostatní shromážděná data protokolu protokoly Azure monitoru. Některé prostředky můžete psát přímo do Azure monitoru, zatímco ostatní zápis do účtu úložiště před importováním do pracovního prostoru Log Analytics. | [Diagnostické protokoly Azure Stream do pracovního prostoru Log Analytics ve službě Azure Monitor](diagnostic-logs-stream-log-store.md)<br>[Shromažďování protokolů ze služby Azure Storage pomocí webu Azure portal](azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage)  |
| Úložiště | Odesílání diagnostických protokolů do služby Azure Storage pro archivaci. | [Archivovat diagnostické protokoly Azure](archive-diagnostic-logs.md) |
| Event Hubs | Stream diagnostické protokoly do jiných umístění s využitím služby Event Hubs. |[Diagnostické protokoly Azure Stream do centra událostí](diagnostic-logs-stream-event-hubs.md) |

## <a name="operating-system-guest"></a>Operační systém (guest)
Výpočetní prostředky v Azure, v ostatních cloudech a v místním mít hostovaný operační systém pro monitorování. Při instalaci jedné nebo více agentů může shromažďovat telemetrická data z hosta do služby Azure Monitor k analýze pomocí stejné nástroje pro sledování jako samotné služby Azure.

![Kolekce prostředků Azure compute](media/data-sources/compute-resources.png)

### <a name="azure-diagnostic-extension"></a>Diagnostické rozšíření Azure
Povolení rozšíření Azure Diagnostics pro Azure Virtual machines vám umožní shromažďovat protokoly a metriky z hostovaného operačního systému Azure výpočetní prostředky, včetně cloudových služeb Azure (klasické) webové a pracovní role, virtuální počítače, virtuální počítač škálovací sady a Service Fabric.

| Cíl | Popis | Referenční informace |
|:---|:---|:---|
| Úložiště | Když povolíte rozšíření diagnostiky, bude zapisovat do účtu úložiště ve výchozím nastavení. | [Ukládání a zobrazení diagnostických dat v Azure Storage](diagnostics-extension-to-storage.md) |
| Metriky Azure Monitor | Při konfiguraci rozšíření diagnostiky ke shromažďování čítačů výkonu jsou zapsány do databáze metrik Azure monitoru. | [Odeslání metrik hostovaného operačního systému do Azure monitoru metriky ukládat pomocí šablony Resource Manageru pro virtuální počítače s Windows](collect-custom-metrics-guestos-resource-manager-vm.md) |
| Application Insights Logs | Shromážděte protokoly a čítače výkonu z výpočetních prostředků, které podporuje vaše aplikace bude analyzovaný se ostatní data aplikací. | [Posílat diagnostická data cloudové služby, virtuální počítač nebo Service Fabric do Application Insights](diagnostics-extension-to-application-insights.md) |
| Event Hubs | Konfigurace diagnostického rozšíření Streamovat data do jiných umístění s využitím služby Event Hubs.  | [Streamování dat diagnostiky Azure do horké cesty s využitím služby Event Hubs](diagnostics-extension-stream-event-hubs.md) |

### <a name="log-analytics-agent"></a>Agenta log Analytics 
Instalace agenta Log Analytics pro komplexní monitorování a správa virtuálních počítačů Windows nebo Linuxem. Virtuální počítač může běžet v Azure, jiného cloudu nebo místně.

| Cíl | Popis | Referenční informace |
|:---|:---|:---|
| Azure Monitor Logs | Agenta Log Analytics se připojí k Azure monitorovat přímo nebo pomocí System Center Operations Manager a umožňuje shromažďovat data ze zdrojů dat, které nakonfigurujete nebo z monitorování řešení, která poskytování dalších přehledů o aplikace spuštěné na virtuálním počítači. | [Agent zdroje dat ve službě Azure Monitor](agent-data-sources.md)<br>[Připojení nástroje Operations Manager k monitorování Azure](om-agents.md) |


### <a name="azure-monitor-for-vms"></a>Azure Monitor pro virtuální počítače 
[Azure Monitor pro virtuální počítače](../insights/vminsights-overview.md) poskytuje přizpůsobené monitorování prostředí pro virtuální počítače a poskytují funkce nad rámec základní funkce monitorování Azure, včetně stavu služby a stav virtuálního počítače. Vyžaduje agenta závislost na virtuální počítače s Windows a Linux, která se integruje s agenta Log Analytics ke shromažďování zjištěná data o procesy spuštěné na virtuálním počítači a závislosti externího procesu.

| Cíl | Popis | Referenční informace |
|:---|:---|:---|
| Azure Monitor Logs | Ukládá data o procesech a závislosti v agentovi. | [Pro virtuální počítače (preview) mapy pochopit součásti aplikace pomocí Azure monitoru](../insights/vminsights-maps.md) |
| Úložiště virtuálního počítače | Azure Monitor pro virtuální počítače ukládá informace o stavu stavu ve vlastním umístění. To je k dispozici pouze pro monitorování Azure pro virtuální počítače na webu Azure Portal kromě [rozhraní REST API Azure Resource health](/rest/api/resourcehealth/). | [Pochopení stavu virtuálních počítačů Azure](../insights/vminsights-health.md)<br>[Azure Resource health rozhraní REST API](https://docs.microsoft.com/rest/api/resourcehealth/) |



## <a name="application-code"></a>Kód aplikace
Podrobné application monitoring pro aplikace ve službě Azure Monitor provádí [Application Insights](https://docs.microsoft.com/azure/application-insights/) který shromažďuje data z aplikací běžících na různých platformách. Aplikace může běžet v Azure, jiného cloudu nebo místně.

![Shromažďování dat aplikací](media/data-sources/applications.png)


### <a name="application-data"></a>Data aplikací
Když povolíte Application Insights pro aplikace nainstalováním Instrumentační balíček, shromažďuje metriky a protokoly týkajících se výkonu a operací aplikace. Application Insights ukládá data, která shromažďuje používají jiné zdroje dat stejné datová Platforma Azure Monitor. Zahrnuje rozsáhlé nástroje pro analýzu těchto dat, ale můžete také analyzovat, jak s daty z jiných zdrojů pomocí nástrojů, jako je Průzkumník metrik a Log Analytics.

| Cíl | Popis | Referenční informace |
|:---|:---|:---|
| Azure Monitor Logs | Provozní data o vaší aplikaci, včetně zobrazení stránek, žádosti o aplikace, výjimkami a trasami. | [Analyzuje data protokolů ve službě Azure Monitor](../log-query/log-query-overview.md) |
|                    | Informace o závislostech mezi součástmi aplikace pro podporu Mapa aplikace a korelace telemetrie. | [Korelace telemetrie v Application Insights](../app/correlation.md) <br> [Mapa aplikace](../app/app-map.md) |
|            | Výsledky testů dostupnosti, které test dostupnosti a odezvy aplikace z různých míst na veřejném Internetu. | [Sledování dostupnosti a odezvy libovolných webů](../app/monitor-web-app-availability.md) |
| Metriky Azure Monitor | Application Insights shromažďuje metriky výkonu a operací aplikace kromě vlastní metriky, které definujete ve vaší aplikaci do databáze metrik Azure monitoru. | [Na základě protokolů a předem agregovaných metrik ve službě Application Insights](../app/pre-aggregated-metrics-log-metrics.md)<br>[Rozhraní API pro Application Insights pro vlastní události a metriky](../app/api-custom-events-metrics.md) |
| Azure Storage | Odeslat data aplikací do služby Azure Storage pro archivaci. | [Export telemetrie z Application Insights](../app/export-telemetry.md) |
|            | Podrobnosti o testy dostupnosti jsou uložené ve službě Azure Storage. Pomocí služby Application Insights na webu Azure Portal ke stažení pro místní analýzu. Výsledky testů dostupnosti jsou uloženy v monitorování protokolů Azure. | [Sledování dostupnosti a odezvy libovolných webů](../app/monitor-web-app-availability.md) |
|            | Profiler trasování data se ukládají ve službě Azure Storage. Pomocí služby Application Insights na webu Azure Portal ke stažení pro místní analýzu.  | [Profil produkční aplikace v Azure pomocí Application Insights](../app/profiler-overview.md) 
|            | Ladit snímek, který data, která je zachycena pro podmnožinu výjimky se ukládají ve službě Azure Storage. Pomocí služby Application Insights na webu Azure Portal ke stažení pro místní analýzu.  | [Jak fungují snímky](../app/snapshot-debugger.md#how-snapshots-work) |

## <a name="monitoring-solutions-and-insights"></a>Řešení monitorování a přehledy
[Řešení monitorování](../insights/solutions.md) a [Insights](../insights/insights-overview.md) shromažďování dat pro poskytování dalších přehledů o provozu nějaká konkrétní služba nebo aplikace. Může řeší prostředků v různých aplikačních vrstev a dokonce i více vrstvami.

### <a name="monitoring-solutions"></a>Řešení monitorování

| Cíl | Popis | Referenční informace
|:---|:---|:---|
| Azure Monitor Logs | Řešení monitorování shromažďování dat do Azure monitoru protokolů kde mohou být analyzovány pomocí dotazovacího jazyka nebo [zobrazení](view-designer.md) , které jsou typicky zahrnuty v řešení. | [Podrobnosti shromažďování dat pro monitorování řešení v Azure](../insights/solutions-inventory.md) |


### <a name="azure-monitor-for-containers"></a>Azure Monitor for Containers
[Azure Monitor for Containers](../insights/container-insights-overview.md) poskytuje přizpůsobené monitorování prostředí pro [Azure Kubernetes Service (AKS)](/azure/aks/). Shromažďuje údaje o tyto prostředky jsou popsané v následující tabulce.

| Cíl | Popis | Referenční informace |
|:---|:---|:---|
| Azure Monitor Logs | Úložiště dat monitorování pro AKS včetně inventáře, protokoly a události. Data metriky se také uložené v protokolech, aby bylo možné využívat jeho funkce analýzy na portálu. | [Porozumění výkonu clusteru AKS pomocí služby Azure Monitor pro kontejnery](../insights/container-insights-analyze.md) |
| Metriky Azure Monitor | Metriky data uložená v databázi metriky, jednotky vizualizace a výstrahy. | [Kontejner zobrazení metrik v Průzkumníku metrik](../insights/container-insights-analyze.md#view-container-metrics-in-metrics-explorer) |
| Azure Kubernetes Service | Azure Monitor pro kontejnery v pořadí téměř reálném čase prostředí, zobrazí data přímo ze služby Azure Kubernetes na webu Azure Portal. | [Postup zobrazení protokolů kontejneru v reálném čase pomocí služby Azure Monitor pro kontejnery (verze Preview)](../insights/container-insights-live-logs.md) |

### <a name="azure-monitor-for-vms"></a>Azure Monitor pro virtuální počítače
[Azure Monitor pro virtuální počítače](../insights/vminsights-overview.md) poskytuje přizpůsobené prostředí pro monitorování virtuálních počítačů. Je součástí popis data shromážděná službou Azure Monitor pro virtuální počítače [operační systém (guest)](#operating-system-guest) výše uvedené části.

## <a name="custom-sources"></a>Vlastní zdroje
Kromě standardní úrovní aplikace budete muset další prostředky, které mají telemetrie, která není možné shromáždit s jinými zdroji dat monitorování. Pro tyto prostředky zápisu těchto dat metriky nebo protokoly pomocí rozhraní API Azure Monitor.

![Vlastní shromažďování](media/data-sources/custom.png)

| Cíl | Metoda | Popis | Referenční informace |
|:---|:---|:---|:---|
| Azure Monitor Logs | Rozhraní API kolekce dat | Shromažďovat data protokolu z jakéhokoli klienta REST a uložit do pracovního prostoru Log Analytics. | [Odeslat data protokolu pro monitorování Azure pomocí rozhraní API kolekce dat HTTP (public preview)](data-collector-api.md) |
| Metriky Azure Monitor | Vlastní metriky rozhraní API | Shromažďovat data metriky z jakéhokoli klienta REST a úložiště v databázi metrik Azure monitoru. | [Odeslat vlastní metriky pro prostředek Azure k úložišti Azure Monitor metriky pomocí rozhraní REST API](metrics-store-custom-rest-api.md) |


## <a name="other-services"></a>Ostatní služby
Ostatní služby v Azure zapisovat data do datové platformy Azure Monitor. To umožňuje analyzovat data shromážděná z těchto služeb se data shromážděná službou Azure Monitor a využijte stejný analýzy a vizualizačních nástrojů.

| Služba | Cíl | Popis | Referenční informace |
|:---|:---|:---|:---|
| [Azure Security Center](/azure/security-center/) | Azure Monitor Logs | Azure Security Center ukládají data zabezpečení, které se shromáždí do pracovního prostoru Log Analytics, což umožňuje analyzovat pomocí jiných protokolů data shromážděná službou Azure Monitor.  | [Shromažďování dat v Azure Security Center](../../security-center/security-center-enable-data-collection.md) |
| [Azure Sentinel](/azure/sentinel/) | Azure Monitor Logs | Azure Sentinel ukládá data, která shromažďuje z různých zdrojů dat v pracovním prostoru Log Analytics to má být analyzován pomocí jiných protokolů data shromážděná službou Azure Monitor umožňuje.  | [Připojení zdroje dat](/azure/sentinel/quickstart-onboard) |


## <a name="next-steps"></a>Další postup

- Další informace o [typy monitorování dat shromážděných službou Azure Monitor](data-platform.md) a jak zobrazit a analyzovat tato data.
- Seznam [různých umístěních, kam ukládat data prostředků Azure](data-locations.md) a jak k němu máte přístup. 
