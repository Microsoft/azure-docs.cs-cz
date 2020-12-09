---
title: Co monitoruje Azure Monitor
description: Odkaz na všechny služby a další prostředky, které monitoruje Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 08/15/2020
ms.openlocfilehash: 08d30fb72398c4b43422eb21f132d5fddd5502b7
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2020
ms.locfileid: "96853135"
---
# <a name="what-is-monitored-by-azure-monitor"></a>Co je monitorované pomocí Azure Monitor?
Tento článek popisuje různé aplikace a služby, které jsou monitorované pomocí Azure Monitor. 

## <a name="insights-and-core-solutions"></a>Přehledy a základní řešení
Core Insights a řešení se považují za součást Azure Monitor a dodržují smlouvy o úrovni služeb a podpory pro Azure. Podporují se ve všech oblastech Azure, kde je Azure Monitor k dispozici.

### <a name="insights"></a>Insights

Přehledy poskytují přizpůsobené možnosti monitorování pro konkrétní aplikace a služby. Shromažďují a analyzují protokoly a metriky.

| Přehled | Popis |
|:---|:---|
| [Application Insights](app/app-insights-overview.md) | Rozšiřitelná služba pro správu výkonu aplikací (APM) pro monitorování živé webové aplikace na libovolné platformě. |
| [Azure Monitor pro kontejnery](insights/container-insights-overview.md) | Monitoruje výkon úloh kontejneru nasazených pro Azure Container Instances nebo spravované clustery Kubernetes hostované ve službě Azure Kubernetes Service (AKS). |
| [Azure Monitor pro Cosmos DB](insights/cosmosdb-insights-overview.md) | Poskytuje přehled o celkovém výkonu, selháních, kapacitě a provozním stavu všech vašich Azure Cosmos DBch prostředků v jednotném interaktivním prostředí. |
| [Azure Monitor pro sítě (Preview)](insights/network-insights-overview.md) | Poskytuje komplexní přehled o stavu a metrikách pro všechny síťové prostředky. Rozšířené možnosti vyhledávání vám pomůžou identifikovat závislosti prostředků a povolit scénáře jako identifikaci prostředků, které hostují váš web, jednoduše hledáním názvu vašeho webu. |
[Azure Monitor pro skupiny prostředků (Preview)](insights/resource-group-insights.md) |  Roztřídit a diagnostikovat všechny problémy, které jednotlivé prostředky narazí, a současně nabídnout kontext jako stav a výkon skupiny prostředků jako celku. |
| [Azure Monitor pro úložiště](insights/storage-insights-overview.md) | Poskytuje ucelený přehled o vašich Azure Storagech účtech tím, že přináší jednotný přehled o výkonu, kapacitě a dostupnosti služby Azure Storage Services. |
| [Azure Monitor pro virtuální počítače](insights/vminsights-overview.md) | Monitoruje vaše virtuální počítače Azure a škálované sady virtuálních počítačů ve velkém měřítku. Analyzuje výkon a stav virtuálních počítačů s Windows a Linuxem a monitoruje jejich procesy a závislosti na jiných prostředcích a externích procesech. |
| [Azure Monitor pro Key Vault (Preview)](./insights/key-vault-insights-overview.md) | Poskytuje ucelený pohled na vaše trezory klíčů tím, že nabízí jednotný přehled o vašich Key Vaultch požadavcích, výkonu, selháních a latenci. |
| [Azure Monitor pro Azure cache pro Redis (Preview)](insights/redis-cache-insights-overview.md) |  Poskytuje jednotný a interaktivní pohled na celkový výkon, selhání, kapacitu a provozní stav. |


### <a name="core-solutions"></a>Základní řešení

Řešení jsou založená na dotazech a zobrazeních protokolu přizpůsobených pro určitou aplikaci nebo službu. Shromažďují a analyzují jenom protokoly a v průběhu času se neúčtují za využití přehledů.

| Řešení | Popis |
|:---|:---|
| [Stav agenta](insights/solution-agenthealth.md) | Analyzujte stav a konfiguraci Log Analyticsch agentů. |
| [Správa výstrah](platform/alert-management-solution.md) | Analyzujte výstrahy shromážděné z System Center Operations Manager, Nagios nebo Zabbix. |
| [Service Map](insights/service-map.md) | Automaticky zjišťuje součásti aplikace v systémech Windows a Linux a mapuje komunikaci mezi službami. |



## <a name="azure-services"></a>Služby Azure
Následující tabulka uvádí služby Azure a data, která shromažďuje do Azure Monitor. 

- Metriky – služba automaticky shromažďuje metriky do Azure Monitor metrik. 
- Protokoly – služba podporuje nastavení diagnostiky, která mohou shromažďovat protokoly platforem a metriky pro Azure Monitor protokolů.
- Přehled – pro službu je k dispozici přehled, který pro službu nabízí přizpůsobené prostředí pro monitorování.

| Služba | Metriky | Protokoly | Přehled | Poznámky |
|:---|:---|:---|:---|:---|
|Active Directory | Ne | Ano | [Ano](../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md) |  |
|Active Directory B2C | Ne | Ne | Ne |  |
|Active Directory Domain Services | Ne | Ano | Ne |  |
|Protokol aktivit | Ne | Ano | Ne | |
|Advanced Threat Protection | Ne | Ne | Ne |  |
|Advisor | Ne | Ne | Ne |  |
|AI Builder | Ne | Ne | Ne |  |
|Analysis Services | Ano | Ano | Ne |  |
|API pro FHIR | Ne | Ne | Ne |  |
|API Management | Ano | Ano | Ne |  |
|App Service | Ano | Ano | Ne |  |
|AppConfig | Ne | Ne | Ne |  |
|Application Gateway | Ano | Ano | Ne |  |
|Služba ověření identity | Ne | Ne | Ne |  |
|Automation | Ano | Ano | Ne |  |
|Azure Service Manager (RDFE) | Ne | Ne | Ne |  |
|Backup | Ne | Ano | Ne |  |
|Bastion | Ne | Ne | Ne |  |
|Batch | Ano | Ano | Ne |  |
|Batch AI | Ne | Ne | Ne |  |
|Služba Blockchain | Ne | Ano | Ne |  |
|Blueprint | Ne | Ne | Ne |  |
|Bot Service | Ne | Ne | Ne |  |
|Cloud Services | Ano | Ano | Ne | Agent nutný k monitorování hostovaného operačního systému a pracovních postupů.  |
|Cloud Shell | Ne | Ne | Ne |  |
|Cognitive Services | Ano | Ano | Ne |  |
|Container Instances | Ano | Ne | Ne |  |
|Container Registry | Ano | Ano | Ne |  |
|Content Delivery Network (CDN) | Ne | Ano | Ne |  |
|Databáze Cosmos | Ano | Ano | [Ano](insights/cosmosdb-insights-overview.md) |  |
|Cost Management | Ne | Ne | Ne |  |
|Data Box | Ne | Ne | Ne |  |
|Data Catalog Gen2 | Ne | Ne | Ne |  |
|Data Explorer | Ano | Ano | Ne |  |
|Data Factory | Ano | Ano | Ne |  |
|Data Factory v2 | Ne | Ano | Ne |  |
|Data Share | Ne | Ne | Ne |  |
|Database for MariaDB | Ano | Ano | Ne |  |
|Database for MySQL | Ano | Ano | Ne |  |
|Database for PostgreSQL | Ano | Ano | Ne |  |
|Database Migration Service | Ne | Ne | Ne |  |
|Databricks | Ne | Ano | Ne |  |
|DDoS Protection | Ano | Ano | Ne |  |
|DevOps | Ne | Ne | Ne |  |
|DNS | Ano | Ne | Ne |  |
|Názvy domén | Ne | Ne | Ne |  |
|DPS | Ne | Ne | Ne |  |
|Dynamics 365 Customer Engagement | Ne | Ne | Ne |  |
|Finance a operace Dynamics 365 | Ne | Ne | Ne |  |
|Event Grid | Ano | Ne | Ne |  |
|Event Hubs | Ano | Ano | Ne |  |
|ExpressRoute | Ano | Ano | Ne |  |
|Brána firewall | Ano | Ano | Ne |  |
|Front Door | Ano | Ano | Ne |  |
|Functions | Ano | Ano | Ne |  |
|HDInsight | Ne | Ano | Ne |  |
|HPC Cache | Ne | Ne | Ne |  |
|Information Protection | Ne | Ano | Ne |  |
|Intune | Ne | Ano | Ne |  |
|IoT Central | Ne | Ne | Ne |  |
|IoT Hub | Ano | Ano | Ne |  |
|Key Vault | Ano | Ano | [Ano](./insights/key-vault-insights-overview.md) |  |
|Kubernetes Service (AKS) | Ne | Ne | [Ano](insights/container-insights-overview.md)  |  |
|Load Balancer | Ano | Ne | Ne |  |
|Logic Apps | Ano | Ano | Ne |  |
|Machine Learning Service | Ne | Ne | Ne |  |
|Managed Applications  | Ne | Ne | Ne |  |
|Mapy  | Ne | Ne | Ne |  |
|Media Services | Ano | Ano | Ne |  |
|Microsoft Managed Desktop | Ne | Ne | Ne |  |
|Microsoft PowerApps | Ne | Ne | Ne |  |
|Microsoft Social Engagement | Ne | Ne | Ne |  |
|Microsoft Stream | Ano | Ano | Ne |  |
|Migrace | Ne | Ne | Ne |  |
|Multi-Factor Authentication | Ne | Ano | Ne |  |
|Network Watcher | Ano | Ano | Ne |  |
|Notification Hubs | Ano | Ne | Ne |  |
|Otevřené datové sady | Ne | Ne | Ne |  |
|Zásada | Ne | Ne | Ne |  |
|Power Automate | Ne | Ne | No |  |
|Power BI Embedded | Ano | Ano | Ne |  |
|Privátní propojení | Ne | Ne | Ne |  |
|Komunikační platforma pro zařazování projektu | Ne | Ne | Ne |  |
|Red Hat OpenShift | Ne | Ne | Ne |  |
|Redis Cache | Ano | Ano | [Ano](insights/redis-cache-insights-overview.md) | |
|Resource Graph | Ne | Ne | Ne |  |
|Resource Manager | Ne | Ne | Ne |  |
|Maloobchodní vyhledávání – podle Bingu | Ne | Ne | Ne |  |
|Search | Ano | Ano | Ne |  |
|Service Bus | Ano | Ano | Ne |  |
|Service Fabric | Ne | Ano | Ne | Agent nutný k monitorování hostovaného operačního systému a pracovních postupů.  |
|Portál pro registraci | Ne | Ne | Ne |  |
|Site Recovery | Ne | Ano | Ne |  |
|Jarní cloudová služba | Ne | Ne | Ne |  |
|Azure Synapse Analytics | Ano | Ano | Ne |  |
|Databáze SQL | Ano | Ano | Ne |  |
|SQL Server Stretch Database | Ano | Ano | Ne |  |
|Zásobník | Ne | Ne | Ne |  |
|Storage | Ano | Ne | [Ano](insights/storage-insights-overview.md) |  |
|Mezipaměť úložiště | Ne | Ne | Ne |  |
|Služby synchronizace úložiště | Ne | Ne | Ne |  |
|Stream Analytics | Ano | Ano | Ne |  |
|Time Series Insights | Ano | Ano | Ne |  |
|TINA | Ne | Ne | Ne |  |
|Traffic Manager | Ano | Ano | Ne |  |
|Univerzální tisk | Ne | Ne | Ne |  |
|Virtual Machine Scale Sets | Ne | Ano | [Ano](insights/vminsights-overview.md) | Agent nutný k monitorování hostovaného operačního systému a pracovních postupů. |
|Virtuální počítače | Ano | Ano | [Ano](insights/vminsights-overview.md) | Agent nutný k monitorování hostovaného operačního systému a pracovních postupů. |
|Virtual Network | Ano | Ano | [Ano](insights/network-insights-overview.md) |  |
|Protokoly toku Virtual Network – NSG | Ne | Ano | Ne |  |
|VPN Gateway | Ano | Ano | Ne |  |
|Windows Virtual Desktop | Ne | Ne | Ne |  |

## <a name="virtual-machine-agents"></a>Agenti virtuálních počítačů
Následující tabulka uvádí agenty, které mohou shromažďovat data z hostovaného operačního systému virtuálních počítačů a odesílat data do monitorování. Každý agent může shromažďovat různá data a odesílat je do těchto metrik nebo protokolů v Azure Monitor. 

Podrobnosti o datech, která může každý Agent shromažďovat, najdete v tématu [přehled Azure Monitorch agentů](platform/agents-overview.md) .

| Agent |  Metriky | Protokoly |
|:---|:---|:---|:---|
| [Agent Azure Monitor (Preview)](platform/azure-monitor-agent-overview.md) | Ano | Ano |
| [Agent Log Analytics](platform/log-analytics-agent.md) | Ne | Ano|
| [Rozšíření diagnostiky](platform/diagnostics-extension-overview.md) | Ano | Ne |
| [Agent telegraf](platform/collect-custom-metrics-linux-telegraf.md) | Ano | Ne |
| [Agent závislostí](insights/vminsights-enable-overview.md) | Ne | Ano |


## <a name="product-integrations"></a>Integrace produktů
Služby a řešení v následující tabulce ukládají svá data do Log Analyticsho pracovního prostoru, aby je bylo možné analyzovat pomocí dalších dat protokolů shromážděných Azure Monitor.

| Produkt/služba | Popis |
|:---|:---|
| [Azure Automation](../automation/index.yml) | Spravujte aktualizace operačního systému a sledujte změny v počítačích s Windows a Linux. Viz [Change Tracking](../automation/change-tracking/overview.md) a [Update Management](../automation/update-management/overview.md). |
| [Azure Information Protection ](/azure/information-protection/) | Klasifikace a volitelně ochrana dokumentů a e-mailů. Přečtěte si téma [centrální vytváření sestav pro Azure Information Protection](/azure/information-protection/reports-aip#configure-a-log-analytics-workspace-for-the-reports). |
| [Azure Security Center](../security-center/index.yml) | Shromažďování a analýza událostí zabezpečení a provádění analýz hrozeb. Viz [shromažďování dat v Azure Security Center](../security-center/security-center-enable-data-collection.md) |
| [Azure Sentinel](../sentinel/index.yml) | Připojí se k různým zdrojům, včetně Office 365 a Amazon Web Servicesho cloudového záznamu. Viz [propojení zdrojů dat](../sentinel/connect-data-sources.md). |
| [Microsoft Intune](/intune/) | Vytvořte nastavení diagnostiky pro odesílání protokolů do Azure Monitor. Viz [odeslání dat protokolu do úložiště, centra událostí nebo Log Analytics v Intune (Preview)](/intune/fundamentals/review-logs-using-azure-monitor).  |
| Síť  | [Network Performance Monitor](insights/network-performance-monitor.md) – monitorovat síťové připojení a výkon do koncových bodů služby a aplikace.<br>[Azure Application Gateway](insights/azure-networking-analytics.md#azure-application-gateway-analytics) – Analyzujte protokoly a metriky z Azure Application Gateway.<br>[Analýza provozu](../network-watcher/traffic-analytics.md) – analyzuje protokoly toků Network Watcher skupiny zabezpečení sítě (NSG), které poskytují přehled o toku přenosů ve vašem cloudu Azure. |
| [Office 365](insights/solution-office-365.md) | Monitorujte své prostředí Office 365. Aktualizovaná verze s vylepšenou dostupností prostřednictvím služby Azure Sentinel. |
| [Analýza SQL](insights/azure-sql.md) | Sledujte výkon databází SQL Azure a spravovaných instancí SQL ve velkém měřítku a napříč několika předplatnými. |
| [Surface Hub](insights/surface-hubs.md) | Sledujte stav a využití Surface Hubch zařízení. |
| [System Center Operations Manager](/system-center/scom) | Shromažďování dat z agentů Operations Manager připojením skupiny pro správu k Azure Monitor. Viz [připojení Operations Manager k Azure monitor](platform/om-agents.md)<br> Posuzuje rizika a stav vaší skupiny pro správu System Center Operations Manager pomocí řešení pro [posouzení Operations Manager](insights/scom-assessment.md) . |
| [Místnosti Microsoft Teams](/microsoftteams/room-systems/azure-monitor-deploy) | Integrovaná a kompletní správa zařízení Microsoft Teams Room. |
| [Visual Studio App Center](/appcenter/) | Sestavujte, testujte a distribuujte aplikace a sledujte jejich stav a využití. Podívejte [se na téma Začínáme s analýzou mobilní aplikace pomocí App Center a Application Insights](learn/mobile-center-quickstart.md). |
| Windows | [Web Windows Update dodržování předpisů](/windows/deployment/update/update-compliance-get-started) – vyhodnocuje upgrady pro stolní počítače s Windows.<br>[Plocha Analytics](/configmgr/desktop-analytics/overview) – integruje se s Configuration Manager, aby poskytovala přehledy a informace, které vám pomohou s rozhodováním o připravenosti na aktualizace vašich klientů Windows. |



## <a name="other-solutions"></a>Další řešení
Další řešení jsou k dispozici pro monitorování různých aplikací a služeb, ale aktivní vývoj se zastavil a nemusí být k dispozici ve všech oblastech. Jsou pokryté smlouvou o úrovni služeb Azure Log Analytics ingestování dat.

| Řešení | Popis |
|:---|:---|
| [Kontroly stavu služby Active Directory](insights/ad-assessment.md) | Posuzuje rizika a stav prostředí Active Directory. |
| [Stav replikace služby Active Directory](insights/ad-replication-status.md) | Pravidelně monitoruje prostředí Active Directory pro případ selhání replikace. |
| [Analýza protokolů aktivit](platform/activity-log.md#activity-log-analytics-monitoring-solution) | Zobrazení položek protokolu aktivit. |
| [DNS Analytics (Preview)](insights/dns-analytics.md) | Shromažďuje, analyzuje a koreluje protokoly Windows DNS pro analýzu a audit a další související data ze serverů DNS. |
| [Cloud Foundry](../cloudfoundry/cloudfoundry-oms-nozzle.md) | Umožňuje shromažďovat, zobrazovat a analyzovat vaše Cloud Foundry a metriky výkonu v různých nasazeních. |
| [Containers](insights/containers.md) | Umožňuje zobrazit a spravovat hostitele kontejnerů Docker a Windows. |
| [Posouzení na vyžádání](/services-hub/health/getting_started_with_on_demand_assessments) | Vyhodnoťte a optimalizujte dostupnost, zabezpečení a výkon vašich místních, hybridních i cloudových prostředí Microsoftu. |
| [Kontroly stavu SQL](insights/sql-assessment.md) | Posuzuje rizika a stav vašich SQL Serverch prostředí.  |
| [Linková data](insights/wire-data.md) | Konsolidovaná data o síti a výkonu shromážděná z počítačů připojených k systému Windows a se systémem Linux s agentem Log Analytics. |

## <a name="third-party-integration"></a>Integrace třetích stran

| Řešení | Popis |
|:---|:---|
| [ITSM](platform/itsmc-overview.md) | ITSM konektor (ITSMC) umožňuje propojit Azure s podporovaným produktem nebo službou správy IT služeb (ITSM).  |


## <a name="resources-outside-of-azure"></a>Prostředky mimo Azure
Azure Monitor mohou shromažďovat data z prostředků mimo Azure pomocí metod uvedených v následující tabulce.

| Prostředek | Metoda |
|:---|:---|
| Aplikace | Monitorujte webové aplikace mimo Azure pomocí Application Insights. Podívejte [se, co je Application Insights?](./app/app-insights-overview.md). |
| Virtuální počítače | Pomocí agentů můžete shromažďovat data z hostovaného operačního systému virtuálních počítačů v jiných cloudových prostředích nebo místních. Přečtěte si téma [přehled Azure Monitorch agentů](platform/agents-overview.md). |
| Klient REST API | K dispozici jsou samostatná rozhraní API pro zápis dat, která Azure Monitor protokoly a metriky z libovolného klienta REST API. V tématu [odeslání dat protokolu pro Azure monitor s rozhraním API kolekce dat http](platform/data-collector-api.md) pro protokoly a [odeslání vlastních metrik pro prostředek Azure do úložiště metrik Azure monitor pomocí REST API](platform/metrics-store-custom-rest-api.md) pro metriky. |



## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [Azure monitor datovou platformu, která ukládá protokoly a metriky shromažďované poznatky a řešeními](platform/data-platform.md).
- Dokončete [kurz monitorování prostředku Azure](learn/tutorial-resource-logs.md).
- Dokončete [kurz pro zápis dotazu protokolu, který analyzuje data v protokolech Azure monitor](learn/tutorial-resource-logs.md).
- Dokončete [kurz vytváření grafu metrik pro analýzu dat v Azure monitor metriky](learn/tutorial-metrics-explorer.md).

